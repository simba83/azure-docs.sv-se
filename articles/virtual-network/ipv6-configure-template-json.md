---
title: Distribuera ett IPv6-program med dubbla stackar med grundläggande Load Balancer i Azure Virtual Network – Resource Manager-mall
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur du distribuerar ett IPv6-program med dubbla stackar i Azure Virtual Network med hjälp av Azure Resource Manager VM-mallar.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 51d43a1cdb24455c12717f4db50955fc7bc738f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84707454"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template"></a>Distribuera ett IPv6-program med dubbla stackar med grundläggande Load Balancer i Azure-Template

Den här artikeln innehåller en lista över konfigurations uppgifter för IPv6 med den del av Azure Resource Manager mall för virtuella datorer som gäller för. Använd mallen som beskrivs i den här artikeln för att distribuera ett program med dubbla stackar (IPv4 + IPv6) med grundläggande Load Balancer som innehåller ett virtuellt nätverk med IPv4 och IPv6-undernät, en grundläggande Load Balancer med dubbla (IPv4 + IPv6) frontend-konfigurationer, virtuella datorer med nätverkskort som har en dubbel IP-konfiguration, nätverks säkerhets grupp och offentliga IP-adresser.

Om du vill distribuera ett program med dubbla stackar (IPV4 + IPv6) med hjälp av Standard Load Balancer, se [distribuera ett IPv6-program med dubbla stackar med standard Load Balancer-mall](ipv6-configure-standard-load-balancer-template-json.md).

## <a name="required-configurations"></a>Obligatoriska konfigurationer

Sök efter mall-avsnitten i mallen för att se var de ska inträffa.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>IPv6-addressSpace för det virtuella nätverket

Mall-avsnittet som ska läggas till:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6-undernät i det virtuella IPv6-nätverket addressSpace

Mall-avsnittet som ska läggas till:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>IPv6-konfiguration för NÄTVERKSKORTet

Mall-avsnittet som ska läggas till:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>Regler för IPv6-nätverks säkerhets grupp (NSG)

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Villkorlig konfiguration

Om du använder en virtuell nätverks installation lägger du till IPv6-vägar i routningstabellen. Annars är den här konfigurationen valfri.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Valfri konfiguration

### <a name="ipv6-internet-access-for-the-virtual-network"></a>IPv6-Internet åtkomst för det virtuella nätverket

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Offentliga IPv6-IP-adresser

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6-klient del för Load Balancer

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>IPv6 backend-adresspool för Load Balancer

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6-belastnings Utjämnings regler för att koppla inkommande och utgående portar

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Exempel-JSON för mall för virtuell dator
Om du vill distribuera ett IPv6-program med dubbla stackar med grundläggande Load Balancer i Azure Virtual Network med Azure Resource Manager mall, se exempel mall [här](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/).

## <a name="next-steps"></a>Nästa steg

Du kan hitta information om priser för [offentliga IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/), [nätverks bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/)eller [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
