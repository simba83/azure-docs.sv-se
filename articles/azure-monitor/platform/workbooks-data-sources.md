---
title: Azure Monitor arbets böcker data källor | Microsoft-dokument
description: Förenkla komplex rapportering med färdiga och anpassade parameterstyrda Azure Monitor arbets böcker som skapats från flera data källor
services: azure-monitor
documentationcenter: ''
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: d41629dd9a56272af89a06cb55e9bd88b604baee
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927914"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Monitor arbets böcker data källor

Arbets böcker är kompatibla med ett stort antal data källor. Den här artikeln vägleder dig genom data källor som för närvarande är tillgängliga för Azure Monitor arbets böcker.

## <a name="logs"></a>Loggar

Med arbets böcker kan du skicka frågor till loggar från följande källor:

* Azure Monitor loggar (Application Insights resurser och Log Analytics arbets ytor.)
* Resursbaserade data (aktivitets loggar)

Arbets boks författare kan använda KQL frågor som transformerar underliggande resurs data för att välja en resultat uppsättning som kan visualiseras som text, diagram eller rutnät.

![Skärm bild av rapport gränssnitt för arbets boks loggar](./media/workbooks-overview/logs.png)

Arbets boks författare kan enkelt fråga över flera resurser som skapar en verkligt enhetlig rapport upplevelse.

## <a name="metrics"></a>Mått

Azure-resurser genererar [mått](data-platform-metrics.md) som kan nås via arbets böcker. Mått kan nås i arbets böcker via en specialiserad kontroll som gör att du kan ange mål resurserna, önskade mått och deras agg regering. Dessa data kan sedan ritas i diagram eller rutnät.

![Skärm bild av mått diagram för arbets böcker med processor användning](./media/workbooks-overview/metrics-graph.png)

![Skärm bild av gränssnittet för arbets boks mått](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

Arbets böcker stöder frågor om resurser och deras metadata med hjälp av Azure Resource Graph (ARG). Den här funktionen används främst för att bygga anpassade fråge omfattningar för-rapporter. Resurs omfånget uttrycks via en KQL-delmängd som ARG stöder – vilket ofta är tillräckligt för vanliga användnings fall.

Om du vill att en fråga ska använda den här data källan använder du List rutan typ av fråga för att välja Azure Resource Graph och väljer de prenumerationer som du vill använda. Använd kontrollen fråga för att lägga till den ARG KQL-delmängd som väljer en intressant resurs del uppsättning.

![Skärm bild av Azure Resource Graph KQL-fråga](./media/workbooks-overview/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

Arbets boken stöder Azure Resource Manager REST-åtgärder. Detta gör det möjligt att fråga management.azure.com-slutpunkten utan att du behöver ange din egen token för Authorization heading.

Om du vill göra en fråg-kontroll använder du den här data källan, använder list rutan data källa för att välja Azure Resource Manager. Ange lämpliga parametrar, till exempel http-metod, URL-sökväg, sidhuvud, URL-parametrar och/eller brödtext.

> [!NOTE]
> Endast `GET` -, `POST` -och- `HEAD` åtgärder stöds för närvarande.

## <a name="azure-data-explorer"></a>Azure-datautforskaren

Arbets böcker har nu stöd för frågor från [Azure datautforskaren](/azure/data-explorer/) -kluster med det kraftfulla [Kusto](/azure/kusto/query/index) -frågespråket.   

![Skärm bild av Kusto Query-fönster](./media/workbooks-overview/data-explorer.png)

## <a name="workload-health"></a>Arbets belastnings hälsa

Azure Monitor har funktioner som proaktivt övervakar tillgänglighet och prestanda för Windows-eller Linux-gäst operativ system. Azure Monitor modeller nyckel komponenter och deras relationer, kriterier för hur man mäter hälsan för dessa komponenter och vilka komponenter som varnar dig när ett dåligt hälso tillstånd upptäcks. Med arbets böcker kan användarna använda den här informationen för att skapa interaktiva interaktiva rapporter.

Om du vill att en fråga ska använda den här data källan använder du List rutan **typ av fråga** för att välja arbets belastnings hälsa och väljer prenumeration, resurs grupp eller VM-resurser som mål. Använd List rutorna för hälso filter för att välja en intressant del av hälso incidenter för dina analys behov.

![Skärm bild av fråga om aviseringar](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure Resource Health

Arbets böcker har stöd för att få Azure Resource Health och kombinera dem med andra data källor för att skapa avancerade, interaktiva hälso rapporter

Om du vill att en fråga ska använda den här data källan använder du List rutan **typ av fråga** för att välja Azure Health och väljer de resurser som ska användas som mål. Använd List rutorna för hälso filter för att välja en intressant del av resurs problem för dina analys behov.

![Skärm bild av aviserings frågan som visar hälso filter listor.](./media/workbooks-overview/resource-health.png)

## <a name="json"></a>JSON

Med JSON-providern kan du skapa ett frågeresultat från statiskt JSON-innehåll. Den används oftast i parametrar för att skapa List Rute parametrar med statiska värden. Enkla JSON-matriser eller objekt kommer automatiskt att konverteras till rutnäts rader och kolumner.  Om du vill ha mer information kan du använda fliken resultat och JSONPath inställningar för att konfigurera kolumner.

## <a name="alerts-preview"></a>Aviseringar (förhandsversion)

> [!NOTE]
> Det föreslagna sättet att fråga efter Azures aviserings information är att använda [Azures resurs diagram](#azure-resource-graph) data källa genom att fråga `AlertsManagementResources` tabellen.
>
> Se [tabell referensen för Azure Resource Graph](../../governance/resource-graph/reference/supported-tables-resources.md)eller [mallen aviseringar](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook) för exempel.
>
> Aviserings data källan kommer att vara tillgänglig under en viss tids period medan författare övergår till att använda ARG. Det rekommenderas inte att använda den här data källan i mallar. 

Med arbets böcker kan användare visualisera aktiva aviseringar relaterade till deras resurser. Begränsningar: aviserings data källan kräver Läs åtkomst till prenumerationen för att kunna söka efter resurser och kan inte Visa nya typer av aviseringar. 

Om du vill göra en fråg-kontroll använder du den här data källan, använder list rutan _data källa_ för att välja _aviseringar (för hands version)_ och väljer de prenumerationer, resurs grupper eller resurser som ska användas som mål. Använd List rutorna för varnings filter för att välja en intressant del av aviseringarna för dina analys behov.

## <a name="custom-endpoint"></a>Anpassad slut punkt

Arbets böcker har stöd för att hämta data från alla externa källor. Om dina data finns utanför Azure kan du använda dem i arbets böcker med den här typen av data källa.

Om du vill göra en fråg-kontroll använder du den här data källan använder du List rutan _data källa_ för att välja _anpassad slut punkt_ . Ange lämpliga parametrar som,, `Http method` `url` `headers` `url parameters` och/eller `body` . Kontrol lera att data källan har stöd för [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) , annars Miss Miss förfrågan.

För att undvika att anrop till obetrodda värdar när du använder mallar, måste användaren Markera de använda värdarna som betrodda. Du kan göra detta genom att antingen klicka på knappen _Lägg till som betrodd_ eller genom att lägga till den som en betrodd värd i arbets boks inställningarna. De här inställningarna kommer att sparas i webbläsare som stöder IndexDb med webb arbetare, mer information [här](https://caniuse.com/#feat=indexeddb).

> [!NOTE]
> Skriv inte några hemligheter i något av fälten (,, `headers` `parameters` `body` , `url` ) eftersom de visas för alla användare i arbets boken.

## <a name="next-steps"></a>Nästa steg

* [Kom igång](./workbooks-overview.md#visualizations) lär dig mer om arbets böcker många avancerade visualiserings alternativ.
* [Kontrol lera](workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.
* [Tips för Log Analytics optimering av frågor](../log-query/query-optimization.md)