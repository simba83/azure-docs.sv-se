---
title: Distribuera IPv6-program med dubbla stackar – grundläggande Load Balancer-CLI
titlesuffix: Azure Virtual Network
description: Lär dig hur du distribuerar ett program med dubbla stackar (IPv4 + IPv6) med grundläggande Load Balancer med hjälp av Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 2746397e2aa97f3c8c3b98d1c7be65b083dcc425
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983828"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>Distribuera ett IPv6-program med dubbla stackar med Basic Load Balancer-CLI

Den här artikeln visar hur du distribuerar ett program med dubbla stackar (IPv4 + IPv6) med grundläggande Load Balancer med hjälp av Azure CLI som innehåller ett virtuellt nätverk med dubbla stackar med ett dubbelt stack-undernät, en grundläggande Load Balancer med dubbla (IPv4 + IPv6) frontend-konfigurationer, virtuella datorer med nätverkskort som har en dubbel IP-konfiguration, regler för dubbla nätverks säkerhets grupper och dubbla offentliga IP

Om du vill distribuera ett program med dubbla stackar (IPV4 + IPv6) med hjälp av Standard Load Balancer, se [distribuera ett IPv6-program med dubbla stackar med standard Load Balancer med Azure CLI](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).


Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda Azure CLI lokalt i stället måste du använda Azure CLI version 2.0.49 eller senare för den här snabb starten. Kör `az --version` för att hitta den installerade versionen. Se [Installera Azure CLI](/cli/azure/install-azure-cli) för installations- eller uppgraderingsinformation.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa ett virtuellt nätverk med dubbla staplar måste du skapa en resurs grupp med [AZ Group Create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *DsResourceGroup01* på platsen för *öster* :

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Skapa offentliga IP-adresser för IPv4 och IPv6 för belastningsutjämnare
För att få åtkomst till dina IPv4-och IPv6-slutpunkter på Internet behöver du IPv4-och IPv6-offentliga IP-adresser för belastningsutjämnaren. Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip). I följande exempel skapas IPv4-och IPv6 offentlig IP-adress med namnet *dsPublicIP_v4* och *dsPublicIP_v6* i resurs gruppen *DsResourceGroup01* :

```azurecli-interactive
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Skapa offentliga IP-adresser för virtuella datorer

Om du vill fjärrans luta till dina virtuella datorer på Internet behöver du offentliga IPv4-IP-adresser för de virtuella datorerna. Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Skapa en lastbalanserare

I det här avsnittet konfigurerar du IP-adresser för dubbel klient del (IPv4 och IPv6) och backend-adresspoolen för belastningsutjämnaren och sedan skapar du en grundläggande Load Balancer.

### <a name="create-load-balancer"></a>Skapa en lastbalanserare

Skapa den grundläggande Load Balancer med [AZ Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) med namnet **dsLB** som innehåller en frontend-pool med namnet **dsLbFrontEnd_v4**, en backend-pool med namnet **DsLbBackEndPool_v4** som är associerad med den offentliga IPv4-IP-adressen **dsPublicIP_v4** som du skapade i föregående steg. 

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Skapa IPv6-frontend

Skapa en IP-adress för IPV6-klient med [AZ Network lb frontend-IP Create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). I följande exempel skapas en IP-konfiguration för klient delen med namnet *dsLbFrontEnd_v6* och den *dsPublicIP_v6* adressen bifogas:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Konfigurera IPv6-backend-adresspool

Skapa en IPv6-backend-adresspool med [AZ Network lb Address-pool Create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). I följande exempel skapas backend-adresspoolen med namnet *dsLbBackEndPool_v6*  att inkludera virtuella datorer med IPv6 NIC-konfigurationer:

```azurecli-interactive
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning
Skapa en hälsoavsökning med [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) så att du kan övervaka de virtuella datorernas hälsotillstånd. 

```azurecli-interactive
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. 

Använd [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) för att skapa en regel för lastbalanseraren. I följande exempel skapas belastnings Utjämnings regler som heter *dsLBrule_v4* och *dsLBrule_v6* och balanserar trafik på *TCP* -port *80* till IP-konfigurationer för IPv4 och IPv6-klient:

```azurecli-interactive
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Skapa nätverksresurser
Innan du distribuerar vissa virtuella datorer måste du skapa stöd för nätverks resurser – tillgänglighets uppsättning, nätverks säkerhets grupp, virtuellt nätverk och virtuella nätverkskort. 
### <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning
Du kan förbättra tillgängligheten för din app genom att placera dina virtuella datorer i en tillgänglighets uppsättning.

Skapa en tillgänglighets uppsättning med [AZ VM Availability-set Create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). I följande exempel skapas en tillgänglighets uppsättning med namnet *dsAVset*:

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Skapa nätverkssäkerhetsgrupp

Skapa en nätverks säkerhets grupp för de regler som ska styra inkommande och utgående kommunikation i ditt VNET.

#### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Skapa en regel för nätverks säkerhets grupp för inkommande och utgående anslutningar

Skapa en regel för nätverks säkerhets grupp för att tillåta RDP-anslutningar via port 3389, Internet anslutning via port 80 och för utgående anslutningar med [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

```azurecli-interactive
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med kommandot [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). I följande exempel skapas ett virtuellt nätverk med namnet *dsVNET* med undernät *dsSubNET_v4* och *dsSubNET_v6*:

```azurecli-interactive
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Skapa nätverkskort

Skapa virtuella nätverkskort för varje virtuell dator med [AZ Network NIC Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). I följande exempel skapas ett virtuellt nätverkskort för varje virtuell dator. Varje nätverkskort har två IP-konfigurationer (1 IPv4-konfiguration, 1 IPv6-konfiguration). Du skapar IPV6-konfigurationen med [AZ Network NIC IP-config Create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).

```azurecli-interactive
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa de virtuella [datorerna med AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). I följande exempel skapas två virtuella datorer och de virtuella nätverkskomponenter som krävs, om de inte redan finns. 

Skapa *dsVM0* för virtuell dator på följande sätt:

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Skapa *dsVM1* för virtuell dator på följande sätt:

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visa ett virtuellt IPv6-nätverk med dubbla stackar i Azure Portal
Du kan visa det virtuella IPv6-nätverket med dubbla stackar i Azure Portal på följande sätt:
1. Skriv *dsVnet*i portalens Sök fält.
2. När **myVirtualNetwork** visas i sökresultatet väljer du det. Då startas **översikts** sidan för det virtuella nätverket med dubbla stackar med namnet *dsVnet*. Det virtuella nätverket med dubbla stackar visar de två nätverkskorten med både IPv4-och IPv6-konfigurationer som finns i det dubbla stack-undernätet med namnet *dsSubnet*.

  ![IPv6-virtuellt nätverk med dubbla stackar i Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en grundläggande Load Balancer med en IP-konfiguration med dubbel klient del (IPv4 och IPv6). Du skapade också två virtuella datorer som omfattade nätverkskort med dubbla IP-konfigurationer (IPV4 + IPv6) som har lagts till i belastningsutjämnaren för belastningsutjämnaren. Mer information om IPv6-stöd i Azure Virtual Networks finns i [Vad är IPv6 för Azure Virtual Network?](ipv6-overview.md)
