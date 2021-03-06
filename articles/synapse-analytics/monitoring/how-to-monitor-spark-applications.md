---
title: Övervaka Apache Spark-program i Synapse Studio
description: Lär dig hur du övervakar Apache Spark program med hjälp av Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 10/16/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 72fe7965f3e2b7ce0cdd53e4ec6e122e5bcbc6ee
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746294"
---
# <a name="use-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Använd Synapse Studio (för hands version) för att övervaka dina Apache Spark-program

Med Azure Synapse Analytics kan du använda Spark för att köra antecknings böcker, jobb och andra typer av program på dina Spark-pooler på din arbets yta.

I den här artikeln beskrivs hur du övervakar Apache Spark program, så att du kan hålla koll på den senaste statusen, problemen och förloppet.

## <a name="access-apache-spark-applications-list"></a>Åtkomst till Apache Spark program lista

Om du vill se en lista över Apache Spark program i din arbets yta [öppnar du först Synapse Studio](https://web.azuresynapse.net/) och väljer din arbets yta.

![Logga in på arbets ytan](./media/common/login-workspace.png)

När du har öppnat din arbets yta väljer du avsnittet **övervaka** till vänster.

![Välj övervaka hubb](./media/common/left-nav.png)

Välj **Apache Spark program** om du vill visa en lista över Apache Spark program.

 ![Välj Spark-program](./media/how-to-monitor-spark-applications/monitor-hub-nav-spark-applications.png)

## <a name="filter-your-apache-spark-applications"></a>Filtrera dina Apache Spark-program

Du kan filtrera listan över Apache Spark program till dem som du är intresse rad av. Med filtren längst upp på skärmen kan du ange ett fält som du vill filtrera.

Du kan till exempel filtrera vyn om du bara vill se de Apache Spark program som innehåller namnet "Sales":

![Knappen Filtrera](./media/common/filter-button.png)

![Exempel filter](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>Visa information om ett specifik Apache Spark-program

Om du vill visa information om ett Apache Spark program väljer du Apache Spark programmet och visar informationen. Om Apache Spark programmet fortfarande körs kan du övervaka förloppet. [Läs mer](apache-spark-applications.md).

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning av pipeline-körningar finns i artikeln [övervaka pipelinen kör Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Mer information om fel sökning av Apache Spark program finns i artikeln [övervaka Apache Spark program på Synapse Studio](apache-spark-applications.md) .