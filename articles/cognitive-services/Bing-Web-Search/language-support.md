---
title: Språk stöd – API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: En lista över naturliga språk, länder och regioner som stöds av API för webbsökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: b3ce1fdc0b52db2706f81950fff735cf7f95e52b
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379688"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Språk-och region stöd för API för webbsökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

API för webbsökning i Bing har stöd för över tre dussin länder eller regioner, många med fler än ett språk. Genom att ange ett land eller en region med en fråga kan du förfina Sök resultat baserat på dessa länder eller regioner. Resultaten kan innehålla länkar till Bing, och dessa länkar kan lokalisera Bing-användargränssnittet enligt angivet land/region eller språk.

Du kan ange ett land eller en region med hjälp av `cc` Frågeparametern. När ett land eller en region har angetts måste du ange en eller flera språk koder med [ `Accept-Language` rubriken](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). Använd [tabellen marknader](#markets) för att visa en lista över språk som stöds på varje marknad.

Alternativt kan du ange marknaden med `mkt` Frågeparametern och en kod från tabellen **marknader** . Att ange en marknad samtidigt anger ett land eller en region och ett önskat språk. Du kan uttryckligen ange språket med `setLang` Frågeparametern.

## <a name="countriesregions"></a>Länder/regioner

|Land/region|Kod|
|-------|----|
|Argentina|AR|
|Australien|AU|
|Österrike|AT|
|Belgien|BE|
|Brasilien|BR|
|Kanada|CA|
|Chile|CL|
|Danmark|DK|
|Finland|FI|
|Frankrike|FR|
|Tyskland|DE|
|Folkrepubliken Kinas särskilda administrativa region Hongkong|HK|
|Indien|IN|
|Indonesien|ID|
|Italien|IT|
|Japan|JP|
|Korea|KR|
|Malaysia|MY|
|Mexico|MX|
|Nederländerna|NL|
|Nya Zeeland|NZ|
|Norge|NO|
|Kina|CN|
|Polen|PL|
|Portugal|PT|
|Filippinerna|PH|
|Ryssland|RU|
|Saudiarabien|SA|
|Sydafrika|ZA|
|Spanien|ES|
|Sverige|SE|
|Schweiz|CH|
|Taiwan|TW|
|Turkiet|TR|
|Storbritannien|GB|
|USA|USA|

## <a name="markets"></a>Marknaden

|Land/region|Språk|Marknads kod|
|-------|--------|-----------|
|Argentina|Spanska|ES-AR|
|Australien|Engelska|en – AU|
|Österrike|Tyska|avinstallation|
|Belgien|Nederländska|NL-vara|
|Belgien|Franska|fr-vara|
|Brasilien|Portugisiska|pt-BR|
|Kanada|Engelska|en-CA|
|Kanada|Franska|fr-CA|
|Chile|Spanska|ES-CL|
|Danmark|Danska|da-DK|
|Finland|Finska|fi-FI|
|Frankrike|Franska|fr-FR|
|Tyskland|Tyska|de-DE|
|Folkrepubliken Kinas särskilda administrativa region Hongkong|Traditionell kinesiska|zh-HK|
|Indien|Engelska|en-IN|
|Indonesien|Engelska|en-ID|
|Italien|Italienska|it-IT|
|Japan|Japanska|ja-JP|
|Korea|Koreanska|ko-KR|
|Malaysia|Engelska|en-MY|
|Mexico|Spanska|ES – MX|
|Nederländerna|Nederländska|nl-NL|
|Nya Zeeland|Engelska|en-NZ|
|Norge|Norska|ingen – nej|
|Kina|Kinesiska|zh-CN|
|Polen|Polska|pl-PL|
|Portugal|Portugisiska|pt-PT|
|Filippinerna|Engelska|en-PH|
|Ryssland|Ryska|ru-RU|
|Saudiarabien|Arabiska|ar-SA|
|Sydafrika|Engelska|en-ZA|
|Spanien|Spanska|es-ES|
|Sverige|Svenska|sv-SE|
|Schweiz|Franska|Frankrike-CH|
|Schweiz|Tyska|de-CH|
|Taiwan|Traditionell kinesiska|zh-TW|
|Turkiet|Turkiska|tr-TR|
|Storbritannien|Engelska|en-GB|
|USA|Engelska|sv-SE|
|USA|Spanska|ES – USA|

## <a name="next-steps"></a>Nästa steg

* [API-referens för bildsökning i Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)