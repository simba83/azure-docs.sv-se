---
title: SCIM-synkronisering med Azure Active Directory
description: Arkitektur vägledning för att uppnå detta mönster för synkronisering
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1dda465dd675e0f5f519f86289df2621be0b9bb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367864"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>SCIM-synkronisering med Azure Active Directory

System för SCIM (Cross-Domain Identity Management) är ett öppet standard protokoll för automatisering av utbyte av användar identitets information mellan identitets domäner och IT-system. SCIM garanterar att anställda som har lagts till i HCM-systemet (Human kapitalet Management) automatiskt har konton skapade i Azure Active Directory (Azure AD) eller Windows Server Active Directory. Användarattribut och profiler synkroniseras mellan de två systemen och uppdaterar borttagning av användare baserat på användarens status eller roll ändringar.

SCIM är en standardiserad definition av två slut punkter: en/users-slutpunkt och en/Groups-slutpunkt. Den använder vanliga REST-verb för att skapa, uppdatera och ta bort objekt. Det använder också ett fördefinierat schema för vanliga attribut, t. ex. grupp namn, användar namn, förnamn, efter namn och e-post. Program som erbjuder en SCIM 2,0-REST API kan minska eller eliminera den smärta som fungerar med egna API: er eller produkter för användar hantering. Till exempel kan alla SCIM-kompatibla klienter göra en HTTP POST av ett JSON-objekt till/Users-slutpunkten för att skapa en ny användar post. I stället för att behöva ett något annorlunda API för samma grundläggande åtgärder kan appar som uppfyller SCIM-standarden omedelbart dra nytta av befintliga klienter, verktyg och kod. 

## <a name="use-when"></a>Använd när: 

Du vill automatiskt etablera användar information från ett HCM-system till Azure AD och Windows Server Active Directory och sedan till mål systemen vid behov. 

![arkitektur diagram](./media/authentication-patterns/scim-auth.png)


## <a name="components-of-system"></a>System komponenter 

* **HCM system**: program och tekniker som möjliggör mänsklig kapital hanterings process och praxis som stöder och AUTOMATISERAr HR-processer under hela livs cykeln för anställda. 

* **Azure AD Provisioning-tjänsten**: använder scim 2,0-protokollet för automatisk etablering. Tjänsten ansluter till SCIM-slutpunkten för programmet och använder SCIM för användar objekt och REST-API: er för att automatisera etablering och avetablering av användare och grupper.  

* **Azure AD**: användar lagring som används för att hantera livs cykeln för identiteter och deras rättigheter. 

* **Mål system**: program eller system som har scim-slutpunkt och fungerar med Azure AD-etableringen för att aktivera automatisk etablering av användare och grupper.  

## <a name="implement-scim-with-azure-ad"></a>Implementera SCIM med Azure AD 

* [Hur etablering fungerar i Azure AD ](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works)

* [Hantera användar konto etablering för företags program i Azure Portal ](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

* [Bygg en SCIM-slutpunkt och konfigurera användar etablering med Azure AD  ](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)

* [SCIM 2,0 protokoll kompatibilitet för Azure AD Provisioning-tjänsten](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-scim-compatibility)

