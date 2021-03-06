---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 1a9f468b8e2f9fff20b9b26b8890d485e426b691
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523990"
---
Genom att använda regional VNet-integrering kan din app komma åt:

* Resurser i ett VNet i samma region som din app.
* Resurser i virtuella nätverk som peer-kopplas till det virtuella nätverk som din app är integrerat med.
* Säkra tjänster för tjänst slut punkt.
* Resurser över Azure ExpressRoute-anslutningar.
* Resurser i det virtuella nätverk som du är integrerad med.
* Resurser mellan peer-anslutningar, inklusive Azure ExpressRoute-anslutningar.
* Privata slut punkter 

När du använder VNet-integrering med virtuella nätverk i samma region kan du använda följande funktioner i Azure-nätverk:

* **Nätverks säkerhets grupper (NSG: er)** : du kan blockera utgående trafik med en NSG som placeras i ditt integrations undernät. Reglerna för inkommande trafik gäller inte eftersom du inte kan använda VNet-integrering för att ge inkommande åtkomst till din app.
* **Routningstabeller (UDR)** : du kan placera en routningstabell i integrations under nätet för att skicka utgående trafik där du vill.

Som standard dirigerar din app endast RFC1918 trafik till ditt VNet. Om du vill dirigera all utgående trafik till ditt VNet använder du appens inställning WEBSITE_VNET_ROUTE_ALL till din app. Konfigurera appens inställning:

1. Gå till **konfigurations** gränssnittet på din app-Portal. Välj **Ny programinställning**.
1. Ange **WEBSITE_VNET_ROUTE_ALL** i rutan **namn** och skriv **1** i rutan **värde** .

   ![Ange program inställning][4]

1. Välj **OK**.
1. Välj **Spara**.

> [!NOTE]
> Om du dirigerar all utgående trafik till ditt VNet, omfattas den av NSG: er och UDR som tillämpas på ditt integrations undernät. När du dirigerar all utgående trafik till ditt VNet, är dina utgående adresser fortfarande de utgående adresser som visas i dina app-egenskaper, om du inte tillhandahåller vägar för att skicka trafiken någon annan stans.

Det finns vissa begränsningar med att använda VNet-integrering med virtuella nätverk i samma region:

* Du kan inte komma åt resurser över global peering anslutningar.
* Funktionen är tillgänglig från alla App Service skalnings enheter i Premium v2 och Premium v3. Den är också tillgänglig i standard men endast från nyare App Service skalnings enheter. Om du använder en äldre skalnings enhet kan du bara använda funktionen från en Premium v2-App Service plan. Om du vill vara säker på att kunna använda funktionen i en standard App Service plan skapar du din app i en Premium v3-App Service plan. Dessa planer stöds endast på våra nyaste skalnings enheter. Du kan skala ned om du vill.  
* Integrations under nätet kan bara användas av en App Service plan.
* Funktionen kan inte användas av isolerade plan-appar som finns i en App Service-miljön.
* Funktionen kräver ett oanvänt undernät som är a/28 eller större i ett Azure Resource Manager VNet.
* Appen och VNet måste finnas i samma region.
* Du kan inte ta bort ett VNet med en integrerad app. Ta bort integrationen innan du tar bort det virtuella nätverket.
* Du kan bara integrera med virtuella nätverk i samma prenumeration som appen.
* Du kan bara ha en regional VNet-integration per App Service plan. Flera appar i samma App Service plan kan använda samma VNet.
* Du kan inte ändra prenumerationen på en app eller en plan när det finns en app som använder regional VNet-integrering.
* Din app kan inte matcha adresser i Azure DNS Private Zones utan konfigurations ändringar

VNet-integreringen är beroende av att ett dedikerat undernät används.  När du etablerar ett undernät förlorar Azure-undernätet 5 IP-adresser för från början. En adress används från integrations under nätet för varje plan instans. Om du skalar din app till fyra instanser används fyra adresser. Debet med 5 adresser från under näts storleken innebär att maximalt antal tillgängliga adresser per CIDR-block är:

- /28 har 11 adresser
- /27 har 27 adress
- /26 har 59 adresser

Om du skalar upp eller ned i storlek måste du dubblera dina adress behov under en kort tids period. Gränserna i storlek innebär att de tillgängliga instanser som stöds per under näts storlek är tillgängliga, om ditt undernät är ett:

- /28, din maximala vågräta skala är 5 instanser
- /27 är din maximala vågräta skala 13 instanser
- /26 är din maximala horisontella skalning 29 instanser

De gränser som anges vid maximal horisontell skala förutsätter att du behöver skala upp eller ned i antingen storlek eller SKU. 

Eftersom under näts storleken inte kan ändras efter tilldelningen använder du ett undernät som är tillräckligt stort för att anpassa vilken skala appen kan komma åt. För att undvika problem med under näts kapaciteten är en/26 med 64 adresser den rekommenderade storleken.  

Om du vill att dina appar i en annan plan ska kunna komma åt ett VNet som redan är anslutet till av appar i ett annat abonnemang väljer du ett annat undernät än det som används av den befintliga VNet-integreringen.

Funktionen stöds fullt ut för både Windows-och Linux-appar, inklusive [anpassade behållare](../articles/app-service/quickstart-custom-container.md). Alla beteenden fungerar på samma sätt mellan Windows-appar och Linux-appar.

### <a name="service-endpoints"></a>Tjänstslutpunkter

Regional VNet-integrering gör att du kan använda tjänst slut punkter. Om du vill använda tjänstens slut punkter med din app använder du regional VNet-integrering för att ansluta till ett valt VNet och konfigurerar sedan tjänstens slut punkter med mål tjänsten på under nätet som du använde för integreringen. Om du sedan ville få åtkomst till en tjänst via tjänst slut punkter:

1. Konfigurera regional VNet-integrering med din webbapp
1. Gå till mål tjänsten och konfigurera tjänst slut punkter mot det undernät som används för integrering

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Du kan använda nätverks säkerhets grupper för att blockera inkommande och utgående trafik till resurser i ett VNet. En app som använder regional VNet-integrering kan använda en [nätverks säkerhets grupp][VNETnsg] för att blockera utgående trafik till resurser i ditt VNet eller Internet. Om du vill blockera trafik till offentliga adresser måste du ha program inställningen WEBSITE_VNET_ROUTE_ALL inställd på 1. Reglerna för inkommande trafik i en NSG gäller inte för din app eftersom VNet-integrering endast påverkar utgående trafik från din app.

Om du vill kontrol lera inkommande trafik till din app använder du funktionen begränsningar för åtkomst. En NSG som tillämpas på ditt integrations under nät gäller oavsett vilka vägar som tillämpas på ditt integrations under nät. Om WEBSITE_VNET_ROUTE_ALL har angetts till 1 och du inte har några vägar som påverkar offentlig adress trafik i ditt integrations undernät, omfattas all utgående trafik fortfarande till NSG: er som har tilldelats ditt integrations under nät. Om WEBSITE_VNET_ROUTE_ALL inte har angetts tillämpas NSG: er endast på RFC1918-trafik.

### <a name="routes"></a>Vägar

Du kan använda routningstabeller för att dirigera utgående trafik från din app till var du vill. Som standard påverkar routningstabeller bara din RFC1918-destinations trafik. Om du anger WEBSITE_VNET_ROUTE_ALL till 1 påverkas alla utgående samtal. Vägar som anges i ditt integrations undernät påverkar inte svar på inkommande app-begäranden. Vanliga mål kan omfatta brand Väggs enheter eller gatewayer.

Om du vill dirigera all utgående trafik lokalt kan du använda en routningstabell för att skicka all utgående trafik till din ExpressRoute-Gateway. Om du dirigerar trafik till en gateway måste du ställa in vägar i det externa nätverket för att skicka svar tillbaka.

Border Gateway Protocol (BGP) vägar påverkar också din app-trafik. Om du har BGP-vägar från något som liknar en ExpressRoute-gateway kommer din app utgående trafik att påverkas. BGP-vägar påverkar som standard bara din RFC1918-destinations trafik. Om WEBSITE_VNET_ROUTE_ALL är inställt på 1 kan all utgående trafik påverkas av BGP-vägar.

### <a name="azure-dns-private-zones"></a>Azure DNS Private Zones 

När din app har integrerats med ditt VNet, använder den samma DNS-server som ditt VNet har kon figurer ATS med. Du kan åsidosätta det här beteendet i appen genom att konfigurera appens inställning WEBSITE_DNS_SERVER med adressen till önskad DNS-server. Om du har konfigurerat en anpassad DNS-server som har kon figurer ATS med ditt VNet men vill att appen ska använda Azure DNS privata zoner, bör du ange WEBSITE_DNS_SERVER med värdet 168.63.129.16. 

### <a name="private-endpoints"></a>Privata slut punkter

Om du vill göra anrop till [privata slut punkter][privateendpoints]måste du kontrol lera att dina DNS-sökningar kommer att matcha den privata slut punkten. För att säkerställa att DNS-sökningarna från din app pekar på dina privata slut punkter kan du:

* integrera med Azure DNS Private Zones. Om ditt VNet inte har en anpassad DNS-server är detta automatiskt
* hantera den privata slut punkten på den DNS-server som används av din app. Om du vill göra det måste du känna till adressen till den privata slut punkten och sedan peka den slut punkt som du försöker ansluta till adressen med en A-post.
* Konfigurera din egen DNS-server att vidarebefordra till Azure DNS privata zoner

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
