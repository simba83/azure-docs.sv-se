---
title: Snabb start – skicka ett SMS-meddelande
titleSuffix: An Azure Communication Services quickstart
description: Lär dig hur du skickar ett SMS-meddelande med Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 52cec861ba81a804081c4b879ab73333a3281e61
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360300"
---
# <a name="quickstart-send-an-sms-message"></a>Snabb start: Skicka ett SMS-meddelande

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET client library](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript client library](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python client library](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java client library](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>Felsökning

Om du vill felsöka problem som rör SMS-leverans kan du [Aktivera leverans rapportering med event Grid](./handle-sms-events.md) för att hämta leverans information.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skickar SMS-meddelanden med Azure Communication Services.

> [!div class="nextstepaction"]
> [Prenumerera på SMS-händelser](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Planera din PSTN-lösning](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [Läs mer om SMS](../../concepts/telephony-sms/concepts.md)