---
title: Ansluta Forcepoint-DLP till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Forcepoint-DLP till Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588254"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Anslut din Forcepoint-DLP till Azure Sentinel

> [!IMPORTANT]
> Data anslutningen Forcepoint data förlust skydd (DLP) i Azure Sentinel är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Med Forcepoint DLP-anslutningsprogrammet kan du automatiskt exportera DLP-incidenter – data till Azure Sentinel. Du kan använda den för att få insyn i användar aktiviteter och incidenter för data förlust. Det möjliggör också korrelationer med data från Azure-arbetsbelastningar och andra flöden och förbättrar övervaknings kapaciteten med arbets böcker i Azure Sentinel.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-forcepoint-dlp"></a>Konfigurera och ansluta Forcepoint-DLP

Konfigurera Forcepoint DLP för att vidarebefordra incident data i JSON-format till Azure-arbetsytan via REST API enligt beskrivningen i [FORCEPOINT DLP integration guide](https://frcpnt.com/dlp-sentinel).


## <a name="find-your-data"></a>Hitta dina data

När Forcepoint DLP-anslutningen har kon figurer ATS visas data i Log Analytics under CustomLogs **ForcepointDLPEvents_CL**.


Om du vill använda det relevanta schemat i Log Analytics för Forcepoint DLP söker du efter **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Forcepoint-DLP till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.