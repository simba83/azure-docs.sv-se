---
title: ta med fil
description: ta med fil
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 79a8bc73f416c8d10d83e7ad94a727094f072b00
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331715"
---
| Domännamn                  | Utgående portar | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Krävs av integration runtime med egen värd för interaktiv redigering. |
| `{datafactory}.{region}.datafactory.azure.net`<br> eller `*.frontend.clouddatahub.net` | 443            | Krävs av integration runtime med egen värd för att ansluta till tjänsten Data Factory. <br>För nya Data Factory som skapats ska du hitta det fullständiga domän namnet från din egen värd Integration Runtime nyckel som är i formatet {DataFactory}. {region}. DataFactory. Azure. net. Om du inte ser det fullständiga domän namnet i den egna värdbaserade integrerings nyckeln använder du *. frontend.clouddatahub.net i stället för gammal data fabrik. |
| `download.microsoft.com`    | 443            | Krävs av den egna värdbaserade integrerings körningen för att ladda ned uppdateringarna. Om du har inaktiverat automatisk uppdatering kan du hoppa över konfigurationen av den här domänen. |
| `*.core.windows.net`          | 443            | Används av integration runtime med egen värd för att ansluta till Azure Storage-kontot när du använder funktionen för [mellanlagrad kopiering](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) . |
| `*.database.windows.net`      | 1433           | Krävs endast när du kopierar från eller till Azure SQL Database eller Azure Synapse Analytics och valfritt annat. Använd funktionen för mellanlagrad kopiering för att kopiera data till SQL Database-eller Synapse-analys utan att öppna port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Krävs endast när du kopierar från eller till Azure Data Lake Store och valfritt annat. |
