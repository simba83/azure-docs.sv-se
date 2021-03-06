---
title: Vad är media Graph-tillägget – Azure
description: Med live video analys på IoT Edge kan du utöka medie diagrammets bearbetnings funktioner via en nod i diagram tillägget.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 74929cc51a868d20952f1e25432f5343e4821d08
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "89569345"
---
# <a name="media-graph-extension"></a>Mediegraftillägg

Med live video analys på IoT Edge kan du utöka medie diagrammets bearbetnings funktioner via en nod i diagram tillägget. Ditt analys tilläggs-plugin-program kan använda traditionella bild bearbetnings metoder eller AI-modeller för visuellt innehåll. Graph-tillägg är aktiverade genom att inkludera en nod för förlängning i ett medie diagram. Noden för förlängnings processor reläerar video bild rutor till den konfigurerade slut punkten och fungerar som gränssnitt för ditt tillägg. Anslutningen kan göras till en lokal eller fjärrslutpunkt och den kan skyddas av autentisering och TLS-kryptering, om det behövs. Dessutom tillåter noden för Graph-tillägget processor för valfri skalning och kodning av video bild rutorna innan de skickas till ditt anpassade tillägg.

Live video analys har stöd för två typer av medie diagrams tilläggs processorer:

* [Processor för HTTP-tillägg](media-graph-concept.md#http-extension-processor)
* [gRPC förlängnings processor](media-graph-concept.md#grpc-extension-processor)

## <a name="http-extension-processor"></a>Processor för HTTP-tillägg

HTTP-tilläggsbegäranden möjliggör utöknings bara scenarier med HTTP-protokollet, där prestanda och/eller optimal resursutnyttjande inte är det viktigaste. Du kan exponera din egen AI till ett medie diagram via en HTTP-REST-slutpunkt. 

Använd noden processor för HTTP-tillägg när:

* Du vill ha bättre interoperabilitet med befintliga HTTP Inferencing-system.
* Data överföring med låg prestanda är acceptabelt.
* Du vill använda ett enkelt Request-Response-gränssnitt för live video analys.

## <a name="grpc-extension-processor"></a>gRPC förlängnings processor

gRPC Extensions-processorn möjliggör utöknings bara scenarier med gRPC-baserat, högpresterande strukturerat protokoll. Det är idealiskt för scenarier där prestanda och/eller optimal resursutnyttjande är en prioritet. Med gRPC Extension processor kan du få fullständig nytta av de strukturerade data definitionerna. gRPC erbjuder hög innehålls överförings prestanda med:

* [i rutan delat minne](https://en.wikipedia.org/wiki/Shared_memory) eller 
* bädda in innehållet direkt i bröd texten i gRPC-meddelanden. 

GRPC Extension-processorn kan användas för att skicka medie egenskaper tillsammans med utbyte av meddelanden om utöknings störningar.
Använd därför en gRPC för utöknings processor när du:

* Vill använda ett strukturerat kontrakt (till exempel strukturerade meddelanden för förfrågningar och svar)
* Vill du använda[protobuf](https://developers.google.com/protocol-buffers)(Protocol Buffers) som sitt underliggande meddelande utbytes format för kommunikation.
* Vill kommunicera med en gRPC-server i en enda Stream-session i stället för den traditionella Request-Response-modellen som behöver en anpassad begär ande hanterare för att parsa inkommande begär Anden och anropa rätt implementerings funktioner. 
* Vill ha låg latens och hög genom strömnings kommunikation mellan live video analys och din modul.

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>Använd din inferencing-modell med live video analys

Med Media Graph-tillägg kan du köra eventuella utöknings modeller som du väljer på alla tillgängliga distributions körningar, till exempel ONNX, TensorFlow, PyTorch eller andra i din egen Docker-behållare. Det anpassade tillägget för inferencing bör distribueras tillsammans med Live Video Analytics Edge-modulen för bästa prestanda och kommer sedan att anropas via HTTP-tilläggs processorn eller gRPC-tilläggs processorn som ingår i din graf-topologi. Dessutom kan anrops frekvensen i det anpassade tillägget begränsas genom att du kan lägga till en [rörelse detektor processor](media-graph-concept.md#motion-detection-processor) och ett [ram frekvens filter processor](media-graph-concept.md#frame-rate-filter-processor) uppströms till medie tilläggs processorn.

Diagrammet nedan visar data flödet på hög nivå:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph-extension/analyze-live-video-with-AI-inference-service.svg" alt-text="AI-tjänst för AI":::

## <a name="samples"></a>Exempel

Kolla in några av våra [Jupyter Notebook](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/readme.md) -exempel för live video analys. De här antecknings böckerna innehåller stegvisa instruktioner för **Media Graph-tilläggen** på:

* Så här skapar du en Docker-behållar avbildning av en tilläggs tjänst
* Distribuera tilläggs tjänsten som en behållare tillsammans med Live Video Analytics-behållaren
* Så här använder du ett video diagram med Real video analys med en tilläggs klient och pekar på tilläggets slut punkt (HTTP/gRPC)