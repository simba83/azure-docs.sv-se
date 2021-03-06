---
title: Uppgradera och skala en Azure API Management-instans | Microsoft Docs
description: I det här avsnittet beskrivs hur du uppgraderar och skalar en Azure API Management-instans.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 626f5b67905e5dd89cf8f12460bc2378451614de
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078314"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Uppgradera och skala en Azure API Management-instans  

Kunder kan skala en Azure API Management-instans genom att lägga till och ta bort enheter. En **enhet** består av dedikerade Azure-resurser och har en viss belastnings bär ande kapacitet uttryckt som ett antal API-anrop per månad. Det här talet representerar inte någon anrops gräns, utan i stället ett maximalt data flödes värde för att tillåta grov kapacitets planering. Det faktiska data flödet och svars tiden varierar i stort sett beroende på faktorer som antalet samtidiga anslutningar, typ och antal konfigurerade principer, begär Anden och svars tider samt Server dels svars tid.

Kapaciteten och priset för varje enhet beror på på vilken **nivå** enheten finns. Du kan välja mellan fyra nivåer: **Developer**, **Basic**, **standard**och **Premium**. Om du behöver öka kapaciteten för en tjänst inom en nivå bör du lägga till en enhet. Om den nivå som för närvarande är markerad i API Management-instansen inte tillåter att fler enheter läggs till, måste du uppgradera till en nivå på högre nivå.

Priset för varje enhet och de tillgängliga funktionerna (till exempel distribution i flera regioner) beror på vilken nivå du väljer för din API Management instans. I artikeln [pris information](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) beskrivs priset per enhet och de funktioner som du får på varje nivå. 

>[!NOTE]
>I artikeln [pris information](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) visas ungefärligt antal enhets kapaciteter på varje nivå. För att få mer exakta siffror måste du titta på ett realistiskt scenario för dina API: er. Se [kapaciteten för en artikel i Azure API Management-instansen](api-management-capacity.md) .

## <a name="prerequisites"></a>Förutsättningar

För att följa stegen i den här artikeln måste du:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Ha en API Management-instans. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).

+ Förstå konceptet med [kapacitet för en Azure API Management-instans](api-management-capacity.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Uppgradera och skala  

Du kan välja mellan fyra nivåer: **Developer**, **Basic**,  **standard**och **Premium**. **Developer** -nivån ska användas för att utvärdera tjänsten. den bör inte användas för produktion. **Developer** -nivån har inget SLA och du kan inte skala den här nivån (Lägg till/ta bort enheter). 

**Basic**, **standard**och **Premium** är produktions nivåer som har service avtal och kan skalas. **Basic** -nivån är billigaste-nivån med ett service avtal och kan skalas upp till två enheter. **standard** nivån kan skalas upp till fyra enheter. Du kan lägga till valfritt antal enheter på **Premium** -nivån.

På **Premium** -nivån kan du distribuera en enda Azure API Management-instans över valfritt antal Azure-regioner. När du skapar en Azure API Management-tjänst första gången innehåller instansen bara en enhet och finns i en enda Azure-region. Den första regionen anges som den **primära** regionen. Du kan enkelt lägga till ytterligare regioner. När du lägger till en region anger du antalet enheter som du vill tilldela. Du kan till exempel ha en enhet i den **primära** regionen och fem enheter i en annan region. Du kan skräddarsy antalet enheter till den trafik du har i varje region. Mer information finns i [distribuera en Azure API Management-tjänstinstans till flera Azure-regioner](api-management-howto-deploy-multi-region.md).

Du kan uppgradera och nedgradera till och från valfri nivå. Uppgradering eller nedgradering kan ta bort vissa funktioner, till exempel virtuella nätverk eller distribution i flera regioner, vid nedgradering till standard eller Basic från Premium-nivån.

> [!NOTE]
> Uppgraderings-eller skalnings processen kan ta mellan 15 och 45 minuter att tillämpa. Du får ett meddelande när det är färdigt.

> [!NOTE]
> API Management tjänst i **förbruknings** nivån skalas automatiskt baserat på trafiken.

## <a name="scale-your-api-management-service"></a>Skala din API Management-tjänst

![Skala API Management tjänst i Azure Portal](./media/upgrade-and-scale/portal-scale.png)

1. Navigera till din API Management-tjänst i [Azure Portal](https://portal.azure.com/).
2. Välj **platser** på menyn.
3. Klicka på raden med den plats som du vill skala.
4. Ange det nya antalet **enheter** – Använd antingen skjutreglaget eller Skriv in numret.
5. Klicka på **Använd**.

## <a name="change-your-api-management-service-tier"></a>Ändra API Management tjänst nivå

1. Navigera till din API Management-tjänst i [Azure Portal](https://portal.azure.com/).
2. Klicka på **pris nivån** i menyn.
3. Välj önskad tjänst nivå i list rutan. Använd skjutreglaget för att ange skalan för din API Management-tjänst efter ändringen.
4. Klicka på **Spara**.

## <a name="downtime-during-scaling-up-and-down"></a>Drift stopp vid skalning upp och ned
Om du skalar från eller till Developer-nivån kommer det att vara stillestånds tid. Annars finns det ingen stillestånds tid. 


## <a name="next-steps"></a>Nästa steg

- [Så distribuerar du en Azure API Management-tjänstinstans till flera Azure-regioner](api-management-howto-deploy-multi-region.md)
- [Skala en Azure API Management-tjänstinstans automatiskt](api-management-howto-autoscale.md)
- [Optimera och Spara på dina moln utgifter](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)