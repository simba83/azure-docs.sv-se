---
title: Frågespråk
titleSuffix: Azure Digital Twins
description: Lär dig grunderna för det digitala Azure-språket för frågor.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d656f19f6f4030025ff1393c3e5017466b3333fd
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044402"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Om frågespråket för Azure Digitals dubbla

Kom ihåg att mitten av Azure Digitals flätas är den [**dubbla grafen**](concepts-twins-graph.md), som är konstruerad från **digitala dubbla** och **relationer**. Det här diagrammet kan tillfrågas om du vill ha information om de digitala dubbla och relationer som den innehåller. Dessa frågor skrivs i ett anpassat SQL-liknande frågespråk, som kallas **Azure Digitals**-frågespråket.

Om du vill skicka en fråga till tjänsten från en klient app använder du Azure Digital- [**fråge-API: et**](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview). Detta gör det möjligt för utvecklare att skriva frågor och applicera filter för att hitta uppsättningar digitala dubbla i det dubbla diagrammet och annan information om det digitala Azure-scenariot.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig att skriva frågor och se exempel på klient kod i [*anvisningar: fråga det dubbla diagrammet*](how-to-query-graph.md).