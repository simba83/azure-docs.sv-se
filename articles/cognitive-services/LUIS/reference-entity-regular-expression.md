---
title: Enhets typ för reguljärt uttryck – LUIS
description: Ett reguljärt uttryck passar bäst för rå uttryck text. Det ignorerar Skift läge och ignorerar kulturell variant.  Matchning av reguljära uttryck används efter stavnings kontroll av ändringar på tecknen, inte på token-nivå.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 0c8d4a5013f23a805c22dc5c44444de6b10ab369
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316247"
---
# <a name="regular-expression-entity"></a>Entitet för reguljära uttryck

En entitet för reguljära uttryck extraherar en entitet baserat på ett mönster för reguljära uttryck som du anger.

Ett reguljärt uttryck passar bäst för rå uttryck text. Det ignorerar Skift läge och ignorerar kulturell variant.  Matchning av reguljära uttryck används efter stavnings kontroll av ändringar på tecknen, inte på token-nivå. Om det reguljära uttrycket är för komplex, t. ex. genom att använda många hakparenteser, kan du inte lägga till uttrycket i modellen. Använder en del men inte alla [.net regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) -bibliotek.

**Entiteten passar bra när:**

* Data formateras konsekvent med alla variationer som också är konsekventa.
* Det reguljära uttrycket behöver inte fler än 2 kapslings nivåer.

![Entitet för reguljära uttryck](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Användnings överväganden

Reguljära uttryck kan matcha mer än vad du förväntar dig att matcha. Ett exempel på detta är numerisk ord matchning som `one` och `two` . Ett exempel är följande regex, som matchar antalet `one` tillsammans med andra tal:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Det här regex-uttrycket matchar även ord som slutar med talen, till exempel `phone` . För att åtgärda problem som detta måste du kontrol lera att regex-matchningarna tar hänsyn till ord gränser. Regex för att använda ord gränser för det här exemplet används i följande regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Exempel-JSON

När `kb[0-9]{6}` du använder, som enhets definition för reguljärt uttryck, är följande JSON-svar ett exempel på uttryck med returnerade reguljära uttrycks enheter för frågan:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)


Detta är JSON om anges `verbose=false` i frågesträngen:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Detta är JSON om anges `verbose=true` i frågesträngen:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Nästa steg

Lär dig mer om entiteter:

* [Begrepp](luis-concept-entity-types.md)
* [Så här skapar du](luis-how-to-add-entities.md)