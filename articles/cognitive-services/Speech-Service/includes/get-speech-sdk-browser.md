---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: f8c7b9ee90e947534d6f938b1eb22f58b57270e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91376985"
---
:::row:::
    :::column span="3":::
        Tal-SDK för Java Script är tillgängligt som ett NPM-paket, se <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-Speech <span class="docon docon-navigate-external x-hidden-focus"></span> -SDK</a> och dess Companion GitHub-lagringsplats <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">kognitiv-Services-Speech- <span class="docon docon-navigate-external x-hidden-focus"></span> SDK-JS </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Även om tal-SDK för Java Script är tillgängligt som ett NPM-paket, så kan både klient webbläsare och Node.js använda IT-överväga de olika arkitektoniska konsekvenserna av varje miljö. <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Dokument objekts modellen (dom <span class="docon docon-navigate-external x-hidden-focus"></span> )</a> är till exempel inte tillgänglig för program på Server sidan, precis som <a href="https://nodejs.org/api/fs.html" target="_blank">fil systemet <span class="docon docon-navigate-external x-hidden-focus"></span> </a> inte är tillgängligt för program på klient sidan.

### <a name="nodejs-package-manager-npm"></a>Node.js Package Manager (NPM)

Om du vill installera tal-SDK för Java Script kör du följande `npm install` kommando nedan.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML-skriptfil

Alternativt kan du direkt ta med en `<script>` tagg i HTML- `<head>` elementet, som förlitar sig på <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM- <span class="docon docon-navigate-external x-hidden-focus"></span> syndikering </a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

Mer information finns i <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">snabb <span class="docon docon-navigate-external x-hidden-focus"></span> starten för webbläsarens tal-SDK </a>.
