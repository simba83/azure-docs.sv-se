---
title: Data konvertering – LUIS
titleSuffix: Azure Cognitive Services
description: Lär dig hur yttranden kan ändras innan förutsägelser i Language Understanding (LUIS)
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: b305be693f59b65a62570f656a0132f4f03cf099
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541806"
---
# <a name="convert-data-format-of-utterances"></a>Konvertera data formatet yttranden
LUIS tillhandahåller följande konverteringar av en användar-uttryck innan du förutsäger "

* Tal till text med hjälp av [Cognitive Services Speech](../Speech-Service/overview.md) service.

## <a name="speech-to-text"></a>Tal till text

Tal till text tillhandahålls som en integrering med LUIS.

### <a name="intent-conversion-concepts"></a>Konverterings begrepp för avsikt
Genom att konvertera tal till text i LUIS kan du skicka talade yttranden till en slut punkt och få ett LUIS förutsägelse svar. Processen är en integrering av [tal](https://docs.microsoft.com/azure/cognitive-services/Speech) tjänsten med Luis. Lär dig mer om tal till avsikt med en [själv studie kurs](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Viktiga krav
Du behöver inte skapa en **API för Bing-taligenkänning** nyckel för den här integrationen. En **language Understanding** nyckel som skapats i Azure Portal fungerar för den här integrationen. Använd inte start nyckeln LUIS.

### <a name="pricing-tier"></a>Prisnivå
Den här integrationen använder en annan [pris](luis-limits.md#key-limits) modell än vanliga language Understanding pris nivåer.

### <a name="quota-usage"></a>Kvot användning
Se [viktiga begränsningar](luis-limits.md#key-limits) för information.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera data](luis-concept-data-extraction.md)

