---
title: Felsöka fel i resurs skapande i Azure HDInsight
description: Vanliga fel och lösningar för problem med kapacitets problem finns i den här artikeln.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 2e9f6898dba559f557f49cc0ee9ff39644da243f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287345"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Felsöka fel i resurs skapande i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Fel: distributionen skulle överskrida kvoten på 800

Azure har en kvot på 800 distributioner per resursgrupp. Olika kvoter tillämpas per resurs grupp, prenumeration, konto eller andra omfång. Din prenumeration kan till exempel konfigureras så att antalet kärnor för en region begränsas. Försök att distribuera en virtuell dator med fler kärnor än tillåtna resultat i fel meddelande som anger att kvoten överskreds.

Lös problemet genom att ta bort de distributioner som inte längre behövs med hjälp av Azure Portal, CLI eller PowerShell.

Mer information finns i [Åtgärda fel med resurskvoter](../azure-resource-manager/templates/error-resource-quota.md).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Fel: den maximala noden överskred de tillgängliga kärnorna i den här regionen

Din prenumeration kanske har konfigurerats så att antalet kärnor för en region begränsas. Försök att distribuera en resurs med fler kärnor än tillåtet resultat i fel meddelande som anger att kvoten överskreds.

Följ dessa steg om du vill begära en kvotökning:

1. Gå till [Azure Portal](https://portal.azure.com)och välj **Hjälp + Support**.

1. Välj **Ny supportbegäran**.

1. På fliken **grundläggande** på sidan **ny supportbegäran** anger du följande information:

   * **Typ av problem:** Välj **tjänst-och prenumerations gränser (kvoter)**.
   * **Prenumeration:** Välj den prenumeration som du vill ändra.
   * **Kvot typ:** Välj **HDInsight**.

Mer information finns i [Skapa ett supportärende för att öka antalet kärnor](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]