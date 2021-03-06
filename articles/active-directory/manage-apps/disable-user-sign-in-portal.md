---
title: Inaktivera användar inloggningar för en Enterprise-App i Azure AD
description: Så här inaktiverar du ett företags program så att inga användare kan logga in på det i Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/12/2019
ms.author: kenwith
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c6fc9a4716f1427257b3bbf18e5fa653567e141
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763388"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Inaktivera användar inloggningar för en företags app i Azure Active Directory

Det är enkelt att inaktivera ett företags program så att inga användare kan logga in på det i Azure Active Directory (Azure AD). Du behöver rätt behörighet för att hantera företags appen. Och du måste vara global administratör för katalogen.

## <a name="how-do-i-disable-user-sign-ins"></a>Hur gör jag för att inaktivera användar inloggningar?

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
1. Välj **alla tjänster**, ange **Azure Active Directory** i text rutan och välj sedan **RETUR**.
1. I fönstret **Azure Active Directory**  -   ***DirectoryName*** (det vill säga Azure AD-fönstret för den katalog som du hanterar) väljer du **företags program**.
1. I fönstret **företags program – alla program** visas en lista över de appar som du kan hantera. Välj en app.
1. I fönstret ***APPNAME*** (det vill säga fönstret med namnet på den valda appen i rubriken) väljer du **Egenskaper**.
1. I fönstret ***appname***  -  **Egenskaper** för APPNAME väljer du **Nej** för **aktiverade användare för inloggning?**.
1. Välj kommandot **Spara** .

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Använd Azure AD PowerShell för att inaktivera en app som inte är listad

Om du känner till AppId för en app som inte visas i listan över företags appar (till exempel eftersom du har tagit bort appen eller om tjänstens huvud namn inte har skapats på grund av att appen är förauktoriserad av Microsoft), kan du skapa tjänstens huvud namn manuellt för appen och sedan inaktivera den med hjälp av [AzureAD PowerShell-cmdleten](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Nästa steg

* [Visa alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företags app](assign-user-or-group-access-portal.md)
* [Ta bort en användare eller grupp tilldelning från en företags app](remove-user-or-group-access-portal.md)
* [Ändra namn eller logo typ för en företags app](change-name-or-logo-portal.md)
