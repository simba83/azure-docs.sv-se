---
title: 'Snabb start: skapa en cacheminne för företags nivå'
description: I den här snabb starten lär du dig hur du skapar en instans av Azure cache för Redis Enterprise-nivå
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: bd5e05f38d34199d9012c52ca3fdad33af231aad
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127993"
---
# <a name="quickstart-create-an-enterprise-tier-cache-preview"></a>Snabb start: skapa en cacheminne för företags nivå (för hands version)

Azure cache för Redis "företags nivåer ger fullständigt integrerat och hanterade [Redis Enterprise](https://redislabs.com/redis-enterprise/) på Azure. De är för närvarande tillgängliga som för hands version. Det finns två nya nivåer i den här förhands granskningen:
* Enterprise, som använder flyktigt minne (DRAM) på en virtuell dator för att lagra data
* Enterprise Flash, som använder både flyktigt och icke-flyktigt minne (NVMe eller SSD) för att lagra data.

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration innan du börjar. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) först.

## <a name="create-a-cache"></a>Skapa en cache
1. Om du vill skapa en cache loggar du in på Azure Portal med hjälp av länken i din förhands gransknings inbjudan och väljer **skapa en resurs** .

1. Välj **databaser** på sidan **nytt** och välj sedan **Azure cache för Redis** .
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Välj Azure-cache för Redis":::
   
1. På sidan **ny Redis cache** konfigurerar du inställningarna för din nya cache.
   
   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. | 
   | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Din cacheposts *värdnamn* är *\<DNS name> . <Azure region> .. redisenterprise.cache.azure.net* . | 
   | **Plats** | List rutan och välj en plats. | Företags nivåer är tillgängliga i begränsade Azure-regioner under för hands versionen. |
   | **Cachestorlek** | List rutan och välj en *Enterprise* -eller *Enterprise Flash* -nivå och en storlek. |  Nivån avgör storlek, prestanda och funktioner som är tillgängliga för cachen. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Välj Azure-cache för Redis" innan du fortsätter.
   >

1. Välj **Nästa: nätverk** och hoppa över.

   > [!NOTE] 
   > Alternativet för privat länk hämtas och kanske inte är tillgängligt omedelbart i din region.
   >

1. Välj **Nästa: Avancerat** och ange **kluster princip** till **företag** .
   
   Du kan behålla standardinställningarna eller ändra dem efter behov. När du aktiverar **Tillåt endast åtkomst via TLS** måste du använda TLS för att komma åt den nya cachen från ditt program.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Välj Azure-cache för Redis":::

   > [!NOTE] 
   > Redis-moduler stöds inte i Enterprise Flash-nivån ännu. Om du planerar att använda en Redis-modul ser du till att välja en cacheminne för företags nivå.
   >
   
1. Välj **Nästa: Taggar** och hoppa över.

1. Välj **Nästa: Granska + skapa** .

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Välj Azure-cache för Redis":::

1. Granska inställningarna och klicka på **skapa** .
   
   Det tar lite tid för cacheminnet att skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång** är cacheminnet redo att användas.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en Azure-instans av Azure cache för Redis.

> [!div class="nextstepaction"]
> [Skapa en ASP.NET-webbapp som använder en Azure Cache for Redis.](./cache-web-app-howto.md)

