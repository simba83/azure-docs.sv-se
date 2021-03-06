---
title: inkludera fil
description: inkludera fil
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 06/03/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 2a35180e4c5ca26d53be135718e345f9657af6a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88655307"
---
| **Resurs** | **Standardgräns** | **Maximal gräns** |
| --- | --- | --- |
| Azure Batch konton per region per prenumeration | 1-3 |50 |
| Dedikerade kärnor per batch-konto | 90-900 | Kontakta supporten |
| Låg prioritets kärnor per batch-konto | 10-100 | Kontakta supporten |
| **[Aktiva](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** jobb och jobb scheman per batch-konto (**slutförda** jobb har ingen gräns) | 100-300 | 1 000<sup>1</sup> |
| Pooler per Batch-konto | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Kontakta Azure-supporten om du vill begära en ökning utöver den här gränsen.

> [!IMPORTANT]
> Vi ändrar hur du begär och hanterar dedikerad kvot.  Det totala dedikerade virtuella processorer är det påtvingade värdet, men snart kommer vi att genomdriva dedikerad kvot per VM-serie. Kvoten med låg prioritet fortsätter att gälla baserat på den totala gränsen. den kommer inte att tillämpas av VM-serien.

> [!NOTE]
> Standard gränserna varierar beroende på vilken typ av prenumeration du använder för att skapa ett batch-konto. Kärnor som visas är för batch-konton i batch-tjänstens läge. [Visa kvoterna i batch-kontot](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> För att hjälpa oss att hantera kapaciteten under den globala hälso Pandemic är standard kvoterna för nya batch-konton i vissa regioner och för vissa typer av prenumeration sänkta från ovanstående värde intervall, i vissa fall till noll kärnor. När du skapar ett nytt batch-konto [kontrollerar du kärn kvoten](../articles/batch/batch-quota-limit.md#view-batch-quotas) och [begär en ökad kärn kvot](../articles/batch/batch-quota-limit.md#increase-a-quota), om det behövs. Du kan också överväga att återanvända batch-konton som redan har tillräcklig kvot. 
