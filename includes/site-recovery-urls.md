---
title: inkludera fil
description: inkludera fil
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67187868"
---
Name | Kommersiell URL | Myndighets-URL | Beskrivning
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Används för åtkomst kontroll och identitets hantering med hjälp av Azure Active Directory. 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Används för överföring av replikeringsdata och koordination.
Replikering | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Används för åtgärder för replikeringshantering och koordination.
Storage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Används för åtkomst till lagringskontot som lagrar replikerade data.
Telemetri (valfritt) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Används för telemetri.
Tidssynkronisering | ``time.windows.com`` | ``time.nist.gov`` | Används för att kontrol lera tidssynkronisering mellan system och global tid i alla distributioner.


