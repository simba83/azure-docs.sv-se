---
title: Snabb start – skapa och hantera åtkomsttoken
titleSuffix: An Azure Communication Services quickstart
description: Lär dig hur du hanterar identiteter och åtkomsttoken med hjälp av Azure Communication Services administrations klient bibliotek.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: b67d0808643797d88628b626403c1b9d97cf1cad
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506244"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Snabb start: skapa och hantera åtkomsttoken

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Kom igång med Azure Communication Services med hjälp av klient biblioteket för administration av kommunikations tjänster. Det gör att du kan skapa identiteter och hantera dina åtkomst-token. Identiteten representerar entiteten för ditt program i Azure Communication Service (till exempel användare eller enhet). Med åtkomst-token kan chatten och anropa klient biblioteken autentisera direkt mot Azure Communication Services. Vi rekommenderar att du genererar åtkomsttoken på en server-side-tjänst. Åtkomsttoken används sedan för att initiera klient biblioteken för kommunikations tjänster på klient enheter.

Alla priser som visas i bilder i den här självstudien är endast i demonstrations syfte.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

Utdata från appen beskriver varje åtgärd som slutförs:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Created an identity: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued a access token with 'voip' scope for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502:
<token signature here>

Successfully deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

> [!div class="checklist"]
> * Hantera identiteter
> * Utfärda åtkomsttoken
> * Använda klient biblioteket för administration av kommunikations tjänster


> [!div class="nextstepaction"]
> [Lägga till röstsamtal i din app](./voice-video-calling/getting-started-with-calling.md)

Du kanske också vill:

 - [Läs mer om autentisering](../concepts/authentication.md)
 - [Lägga till chatt i din app](./chat/get-started.md)
 - [Lär dig mer om klient-och server arkitektur](../concepts/client-and-server-architecture.md)
 