---
author: msmbaldwin
ms.service: key-vault
ms.subservice: B2C
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 826d5c2183ecc70908192695927890dcf06137b1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "87013147"
---
Skapa en åtkomst princip för nyckel valvet som beviljar behörighet till tjänstens huvud namn genom `clientId` att skicka till AZ-kommando för nyckel [valv set-princip](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Ge tjänstens huvud namn get-, list-och set-behörigheter för både nycklar och hemligheter.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```
