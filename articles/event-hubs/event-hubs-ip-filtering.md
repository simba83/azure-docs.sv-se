---
title: Regler för Azure Event Hubs-brandvägg | Microsoft Docs
description: Använd brand Väggs regler för att tillåta anslutningar från vissa IP-adresser till Azure Event Hubs.
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: e07f863bf8b7d5f64ec0ba04bf16fba12f4a785d
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427453"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>Tillåt åtkomst till Azure Event Hubs-namnrymder från vissa IP-adresser eller intervall
Som standard är Event Hubs-namnrymder tillgängliga från Internet så länge förfrågan levereras med giltig autentisering och auktorisering. Med IP-brandvägg kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adress intervall i [CIDR-notation (classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Den här funktionen är användbar i scenarier där Azure Event Hubs bör endast vara tillgängligt från vissa välkända webbplatser. Med brand Väggs regler kan du konfigurera regler för att acceptera trafik som kommer från vissa IPv4-adresser. Om du till exempel använder Event Hubs med [Azure Express Route][express-route], kan du skapa en **brand Väggs regel** som tillåter trafik från enbart lokala infrastruktur-IP-adresser. 

>[!WARNING]
> Genom att aktivera brand Väggs regler för Event Hubs namn området blockeras inkommande begär Anden som standard, om inte begär Anden härstammar från en tjänst som körs från tillåtna offentliga IP-adresser. Begär Anden som blockeras inkluderar de från andra Azure-tjänster, från Azure Portal, från loggnings-och mått tjänster och så vidare. Som ett undantag kan du tillåta åtkomst till Event Hubs resurser från vissa betrodda tjänster även när IP-filtrering är aktive rad. En lista över betrodda tjänster finns i [betrodda Microsoft-tjänster](#trusted-microsoft-services).

## <a name="ip-firewall-rules"></a>Regler för IP-brandvägg
IP-brandväggens regler tillämpas på Event Hubs namn områdes nivå. Reglerna gäller därför för alla anslutningar från klienter som använder ett protokoll som stöds. Alla anslutnings försök från en IP-adress som inte matchar en tillåten IP-regel på Event Hubs namn området nekas som obehörig. Svaret nämns inte IP-regeln. IP filter regler tillämpas i ordning och den första regeln som matchar IP-adressen avgör vilken åtgärd som godkänns eller nekas.

## <a name="use-azure-portal"></a>Använda Azure-portalen
Det här avsnittet visar hur du använder Azure Portal för att skapa IP-brandvägg för ett Event Hubs namn område. 

1. Navigera till **Event Hubs namn området** i [Azure Portal](https://portal.azure.com).
4. Välj **nätverk** under **Inställningar** på den vänstra menyn. Fliken **nätverk** visas endast för **standard** -eller **dedikerade** namn områden. 
    > [!NOTE]
    > Som standard är alternativet **valda nätverk** markerat som visas i följande bild. Om du inte anger en IP-brandväggsregel eller lägger till ett virtuellt nätverk på den här sidan, kan namn området nås via **offentliga Internet** (med hjälp av åtkomst nyckeln).  

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Fliken nätverk – alternativet valda nätverk" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Om du väljer alternativet **alla nätverk** , godkänner händelsehubben anslutningar från alla IP-adresser (med hjälp av åtkomst nyckeln). Den här inställningen motsvarar en regel som accepterar IP-adressintervallet 0.0.0.0/0. 

    ![Skärm bild som visar sidan "brand väggar och virtuella nätverk" med alternativet "alla nätverk" markerat.](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Om du vill begränsa åtkomsten till vissa IP-adresser kontrollerar du att alternativet **valda nätverk** är markerat. I avsnittet **brand vägg** följer du dessa steg:
    1. Välj alternativet **Lägg till klientens IP-adress** för att ge din aktuella klient-IP åtkomst till namn området. 
    2. För **adress intervall** anger du en angiven IPv4-adress eller ett intervall med IPv4-adresser i CIDR-notering. 
3. Ange om du vill **tillåta att betrodda Microsoft-tjänster kringgår den här brand väggen**. Se [betrodda Microsoft-tjänster](#trusted-microsoft-services) för mer information. 

      ![Brand vägg – alternativet alla nätverk är valt](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Spara inställningarna genom att välja **Spara** i verktygsfältet. Vänta några minuter tills bekräftelsen visas på Portal meddelandena.

    > [!NOTE]
    > Information om hur du begränsar åtkomsten till vissa virtuella nätverk finns i [Tillåt åtkomst från vissa nätverk](event-hubs-service-endpoints.md).

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]


## <a name="use-resource-manager-template"></a>Använda Resource Manager-mallar

> [!IMPORTANT]
> Brand Väggs regler stöds i **standard** -och **dedikerade** nivåer av Event Hubs. De stöds inte på grundläggande nivå.

Följande Resource Manager-mall gör det möjligt att lägga till en IP-filterlista till ett befintligt Event Hubs-namnområde.

Mallparametrar:

- **ipMask** är en enskild IPv4-adress eller ett block med IP-adresser i CIDR-notation. I CIDR-notation 70.37.104.0/24 representerar till exempel 256 IPv4-adresser från 70.37.104.0 till 70.37.104.255, med 24 som anger antalet signifikanta prefix för intervallet.

> [!NOTE]
> Även om det inte finns några tillåtna nekade regler, har Azure Resource Manager mal len standard åtgärden inställd på **Tillåt** , vilket inte begränsar anslutningar.
> När du skapar Virtual Network-eller brand Väggs regler måste vi ändra **_"defaultAction"_**
> 
> Från
> ```json
> "defaultAction": "Allow"
> ```
> på
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Följ anvisningarna för [Azure Resource Manager][lnk-deploy]om du vill distribuera mallen.

## <a name="next-steps"></a>Nästa steg

Information om hur du begränsar åtkomsten till Event Hubs till virtuella Azure-nätverk finns i följande länk:

- [Virtual Network tjänst slut punkter för Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
