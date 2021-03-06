---
title: Begränsa data exfiltrering till Azure Storage – Azure CLI
description: I den här artikeln får du lära dig hur du begränsar och begränsar virtuella nätverks data exfiltrering till Azure Storage resurser med tjänst slut punkts principer för virtuella nätverk med hjälp av Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 53f10996a7f15e32261f151600163f41df4e58ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666797"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Hantera data exfiltrering för att Azure Storage konton med tjänst slut punkts principer för virtuella nätverk med hjälp av Azure CLI

Med tjänst slut punkts principer för virtuella nätverk kan du tillämpa åtkomst kontroll på Azure Storage-konton inifrån ett virtuellt nätverk över tjänst slut punkter. Det här är en nyckel för att skydda dina arbets belastningar, hantera vilka lagrings konton som tillåts och var data exfiltrering tillåts.
I den här artikeln kan du se hur du:

* Skapa ett virtuellt nätverk och Lägg till ett undernät.
* Aktivera tjänst slut punkt för Azure Storage.
* Skapa två Azure Storage konton och Tillåt nätverks åtkomst till det från det undernät som skapades ovan.
* Skapa en tjänst slut punkts princip för att endast tillåta åtkomst till ett av lagrings kontona.
* Distribuera en virtuell dator (VM) till under nätet.
* Bekräfta åtkomst till det tillåtna lagrings kontot från under nätet.
* Bekräfta åtkomst nekas till det icke-tillåtna lagrings kontot från under nätet.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.28 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Innan du skapar ett virtuellt nätverk måste du skapa en resurs grupp för det virtuella nätverket och alla andra resurser som skapas i den här artikeln. Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Skapa ett virtuellt nätverk med ett undernät med [AZ Network VNet Create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Aktivera en tjänstslutpunkt 

I det här exemplet skapas en tjänst slut punkt för *Microsoft. Storage* för under nätets *privata*: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Begränsa nätverksåtkomst för ett undernät

Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](/cli/azure/network/nsg). I följande exempel skapas en nätverks säkerhets grupp med namnet *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Koppla nätverks säkerhets gruppen till det *privata* under nätet med [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet). I följande exempel associeras nätverks säkerhets gruppen *myNsgPrivate* till det *privata* under nätet:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Skapa säkerhets regler med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). Regeln nedan tillåter utgående åtkomst till de offentliga IP-adresser som tilldelats den Azure Storage tjänsten: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Varje nätverks säkerhets grupp innehåller flera [Standard säkerhets regler](security-overview.md#default-security-rules). Regeln nedan åsidosätter en standard säkerhets regel som tillåter utgående åtkomst till alla offentliga IP-adresser. `destination-address-prefix "Internet"`Alternativet nekar utgående åtkomst till alla offentliga IP-adresser. Den föregående regeln åsidosätter den här regeln, på grund av dess högre prioritet, vilket ger åtkomst till de offentliga IP-adresserna för Azure Storage.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

Följande regel tillåter att SSH-trafik inkommande till under nätet var som helst. Regeln åsidosätter en standardsäkerhetsregel som nekar all inkommande trafik från internet. SSH tillåts till under nätet så att anslutningen kan testas i ett senare steg.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Begränsa nätverks åtkomsten till Azure Storage konton

Det här avsnittet innehåller steg för att begränsa nätverks åtkomsten för ett Azure Storage konto från det tilldelade under nätet i ett virtuellt nätverk via tjänst slut punkt.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa två Azure Storage-konton med [AZ lagrings konto Create](/cli/azure/storage/account).

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

När lagrings kontona har skapats hämtar du anslutnings strängen för lagrings kontona till en variabel med [AZ lagrings konto show-Connection-String](/cli/azure/storage/account). Anslutnings strängen används för att skapa en fil resurs i ett senare steg.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Visa innehållet i variabeln och anteckna värdet för **AccountKey** som returneras i utdata, eftersom det används i ett senare steg.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Skapa en filresurs i lagringskontot

Skapa en fil resurs i lagrings kontot med [AZ Storage Share Create](/cli/azure/storage/share). I ett senare steg monteras den här fil resursen för att bekräfta nätverks åtkomsten till den.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Neka all nätverks åtkomst till lagrings kontot

Som standard godkänner lagringskonton nätverksanslutningar från klienter i alla nätverk. Om du vill begränsa åtkomsten till valda nätverk ändrar du standard åtgärden till *neka* med [AZ Storage Account Update](/cli/azure/storage/account). När nätverksåtkomsten nekas är lagringskontot inte tillgängligt från något nätverk.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Aktivera nätverks åtkomst från undernät för virtuellt nätverk

Tillåt nätverks åtkomst till lagrings kontot från det *privata* under nätet med [AZ lagrings konto nätverks regel Lägg till](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Använd princip för att tillåta åtkomst till ett giltigt lagrings konto

Azure Service Endpoint-principer är bara tillgängliga för Azure Storage. Därför kommer vi att aktivera tjänstens slut punkt för *Microsoft. Storage* i det här under nätet för den här exempel installationen.

Tjänst slut punkts principer tillämpas via tjänst slut punkter. Vi börjar med att skapa en tjänst slut punkts princip. Vi skapar sedan princip definitionerna under den här principen för Azure Storage konton som ska godkännas för det här under nätet

Skapa en princip för tjänst slut punkt

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Spara resurs-URI för det tillåtna lagrings kontot i en variabel. Innan du kör kommandot nedan ersätter du *\<your-subscription-id>* med det faktiska värdet för ditt prenumerations-ID.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Skapa & Lägg till en princip definition för att tillåta ovanstående Azure Storage konto till tjänstens slut punkts princip

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

Och uppdatera det virtuella nätverkets undernät så att det kopplas till den tjänst slut punkts princip som skapades i föregående steg

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Verifiera åtkomst begränsning till Azure Storage konton

### <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

Om du vill testa nätverks åtkomst till ett lagrings konto distribuerar du en virtuell dator till under nätet.

Skapa en virtuell dator i det *privata* under nätet med [AZ VM Create](/cli/azure/vm). Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När du har skapat den noterar du **publicIpAddress** i resultatet som returneras. Den här adressen används för att komma åt den virtuella datorn från Internet i ett senare steg.

### <a name="confirm-access-to-storage-account"></a>Bekräfta åtkomst till lagringskontot

SSH till den virtuella *myVmPrivate* -datorn. Ersätt *\<publicIpAddress>* med den offentliga IP-adressen för din virtuella *myVmPrivate* -dator.

```bash 
ssh <publicIpAddress>
```

Skapa en mapp för en monterings punkt:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Montera Azure-filresursen i den katalog som du skapade. Innan du kör kommandot nedan ersätter du *\<storage-account-key>* med värdet *AccountKey* från **$saConnectionString 1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Du får meddelandet `user@myVmPrivate:~$` . Azure-filresursen har monterats på */mnt/MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Bekräfta att åtkomst till lagringskontot nekas

Skapa en katalog för en monterings punkt från samma VM- *myVmPrivate*:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Försök att montera Azure-filresursen från lagrings kontots *notallowedstorageacc* till den katalog som du skapade. Den här artikeln förutsätter att du har distribuerat den senaste versionen av Ubuntu. Om du använder tidigare versioner av Ubuntu kan du läsa mer instruktioner om hur du monterar fil resurser i avsnittet [montera på Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . 

Innan du kör kommandot nedan ersätter du *\<storage-account-key>* med värdet *AccountKey* från **$saConnectionString 2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Åtkomst nekas och du får ett `mount error(13): Permission denied` fel meddelande eftersom det här lagrings kontot inte finns i listan över tillåtna tjänster för den tjänst slut punkts princip som vi tillämpade på under nätet. 

Avsluta SSH-sessionen till den virtuella *myVmPublic* -datorn.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du [AZ Group Delete](/cli/azure) för att ta bort resurs gruppen och alla resurser den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tillämpat en tjänst slut punkt princip över en tjänst slut punkt för Azure Virtual Network för att Azure Storage. Du har skapat Azure Storage konton och begränsad nätverks åtkomst till endast vissa lagrings konton (och därmed nekade andra) från ett virtuellt nätverks under nät. Läs mer om principer för tjänst slut punkter i [Översikt över tjänst slut punkter](virtual-network-service-endpoint-policies-overview.md).
