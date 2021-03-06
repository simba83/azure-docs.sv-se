---
title: Översikt över avsikts igenkänning – tal tjänst
titleSuffix: Azure Cognitive Services
description: Med igenkänning av avsikt kan du identifiera användar mål som du har fördefinierat. Den här artikeln är en översikt över fördelarna och funktionerna i tjänsten för avsikts igenkänning.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: avsikts igenkänning
ms.openlocfilehash: 0d718459e0fd0ea410232d3a165b560aa8c59cd1
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92174645"
---
# <a name="what-is-intent-recognition"></a>Vad är avsikts igenkänning?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

I den här översikten får du lära dig om fördelarna och funktionerna i avsikts igenkänning. Cognitive Services Speech SDK kan integreras med Language Understanding-tjänsten (LUIS) för att tillhandahålla avsiktsigenkänning. En avsikt är något som användaren vill göra: boka en flygning, titta på vädret eller ringa ett samtal.
Genom att använda avsikts igenkänning kan dina program, verktyg och enheter avgöra vad användaren vill initiera eller göra baserat på alternativ som du definierar i LUIS.

## <a name="luis-key-required"></a>LUIS-nyckel krävs

* LUIS integreras med Speech-tjänsten för att identifiera avsikter från tal. Du behöver inte en Speech-tjänstprenumeration, bara LUIS.
* Igenkänning av tal avsikt är integrerat med SDK. Du kan använda en LUIS-nyckel med tal tjänsten.
* Avsikts igenkänning via talet SDK [erbjuds i en delmängd av de regioner som stöds av Luis](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#intent-recognition).

## <a name="get-started"></a>Kom igång

Se [snabb start](quickstarts/intent-recognition.md) för att komma igång med avsikts igenkänning.

## <a name="sample-code"></a>Exempelkod

Exempel kod för avsikts igenkänning:

* [Snabbstart: Använda en färdig hemautomatiseringsapp](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app)
* [Identifiera avsikter från tal med hjälp av Speech SDK för C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)
* [Avsikts igenkänning och andra tal tjänster som använder Unity i C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Identifiera avsikter med tal-SDK för python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Igenkänning av avsikter och andra tal tjänster med hjälp av tal-SDK för C++ i Windows](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Igenkänning av avsikter och andra tal tjänster med hjälp av tal-SDK för java i Windows eller Linux](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Igenkänning av avsikter och andra tal tjänster med hjälp av tal-SDK för Java Script i en webbläsare](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>Referens dokument

* [Speech SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>Nästa steg

* Slutför [snabb](quickstarts/intent-recognition.md) starten för avsikts igenkänning
* [Hämta en prenumerations nyckel för Speech service kostnads fritt](overview.md#try-the-speech-service-for-free)
* [Hämta tal-SDK](speech-sdk.md)
