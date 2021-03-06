---
title: FHIR funktioner som stöds i Azure – Azure API för FHIR
description: I den här artikeln beskrivs vilka funktioner i FHIR-specifikationen som implementeras i Azure API för FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: 609bd01e8dcb0e9202d1d9dbe1d1fc1a01cac550
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368289"
---
# <a name="features"></a>Funktioner

Azure API för FHIR tillhandahåller en fullständigt hanterad distribution av Microsoft FHIR-servern för Azure. Servern är en implementering av [FHIR](https://hl7.org/fhir) -standarden. Det här dokumentet listar huvud funktionerna i FHIR-servern.

## <a name="fhir-version"></a>FHIR-version

Senaste version som stöds: `4.0.1`

Tidigare versioner som också stöds är: `3.0.2`

## <a name="rest-api"></a>REST-API

| API                            | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| läsa                           | Ja       | Ja       | Ja       |                                                     |
| vread                          | Ja       | Ja       | Ja       |                                                     |
| update                         | Ja       | Ja       | Ja       |                                                     |
| uppdatera med optimistisk låsning | Ja       | Ja       | Ja       |                                                     |
| uppdatering (villkorligt)           | Ja       | Ja       | Ja       |                                                     |
| 9.0a                          | Nej        | Nej        | Nej        |                                                     |
| delete                         | Ja       | Ja       | Ja       |                                                     |
| ta bort (villkorligt)           | Nej        | Nej        | Nej        |                                                     |
| historik                        | Ja       | Ja       | Ja       |                                                     |
| skapa                         | Ja       | Ja       | Ja       | Stöd för både efter-och-placering                               |
| Skapa (villkorlig)           | Ja       | Ja       | Ja       | Problem [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| sök                         | Delvis   | Delvis   | Delvis   | Se nedan                                           |
| kedjad sökning                 | Nej        | Ja       | Nej        |                                           |
| omvänd länkad sökning         | Nej        | Nej        | Nej        |                                            |
| funktioner                   | Ja       | Ja       | Ja       |                                                     |
| batch                          | Ja       | Ja       | Ja       |                                                     |
| transaktionen                    | Nej        | Ja       | Nej        |                                                     |
| växlings                         | Delvis   | Delvis   | Delvis   | `self` och `next` stöds                     |
| mellanhand                 | Nej        | Nej        | Nej        |                                                     |

## <a name="search"></a>Search

Alla typer av Sök parametrar stöds. 

| Typ av Sök parameter | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|-----------------------|-----------|-----------|-----------|---------|
| Antal                | Ja       | Ja       | Ja       |         |
| Datum/DateTime         | Ja       | Ja       | Ja       |         |
| Sträng                | Ja       | Ja       | Ja       |         |
| Token                 | Ja       | Ja       | Ja       |         |
| Referens             | Ja       | Ja       | Ja       |         |
| Sammansatt             | Ja       | Ja       | Ja       |         |
| Kvantitet              | Ja       | Ja       | Ja       |         |
| URI                   | Ja       | Ja       | Ja       |         |
| Speciella               | Nej        | Nej        | Nej        |         |


| Modifierare             | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Ja       | Ja       | Ja       |         |
|`:exact`               | Ja       | Ja       | Ja       |         |
|`:contains`            | Ja       | Ja       | Ja       |         |
|`:text`                | Ja       | Ja       | Ja       |         |
|`:in` åtkomsttokenbegäran          | Nej        | Nej        | Nej        |         |
|`:below` åtkomsttokenbegäran       | Nej        | Nej        | Nej        |         |
|`:above` åtkomsttokenbegäran       | Nej        | Nej        | Nej        |         |
|`:not-in` åtkomsttokenbegäran      | Nej        | Nej        | Nej        |         |
|`:[type]` förhållande  | Nej        | Nej        | Nej        |         |
|`:below` URI         | Ja       | Ja       | Ja       |         |
|`:not`                 | Nej        | Nej        | Nej        |         |
|`:above` URI         | Nej        | Nej        | Nej        | Problem [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Vanlig Sök parameter | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Ja       | Ja       | Ja       |         |
| `_lastUpdated`          | Ja       | Ja       | Ja       |         |
| `_tag`                  | Ja       | Ja       | Ja       |         |
| `_profile`              | Ja       | Ja       | Ja       |         |
| `_security`             | Ja       | Ja       | Ja       |         |
| `_text`                 | Nej        | Nej        | Nej        |         |
| `_content`              | Nej        | Nej        | Nej        |         |
| `_list`                 | Ja       | Ja       | Ja       |         |
| `_has`                  | Nej        | Nej        | Nej        |         |
| `_type`                 | Ja       | Ja       | Ja       |         |
| `_query`                | Nej        | Nej        | Nej        |         |
| `_filter`               | Nej        | Nej        | Nej        |         |

| Sök Resultat parametrar | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|-------------------------|-----------|-----------|-----------|---------|
| `_sort`                 | Delvis        | Delvis   | Delvis        |   `_sort=_lastUpdated` stöds       |
| `_count`                | Ja       | Ja       | Ja       | `_count` är begränsat till 100 tecken. Om värdet är högre än 100 kommer endast 100 att returneras och en varning returneras i paketet. |
| `_include`              | Nej        | Ja       | Nej        |         |
| `_revinclude`           | Nej        | Ja       | Nej        | Inkluderade objekt är begränsade till 100. |
| `_summary`              | Delvis   | Delvis   | Delvis   | `_summary=count` stöds |
| `_total`                | Delvis   | Delvis   | Delvis   | _total = icke och _total = korrekt      |
| `_elements`             | Ja       | Ja       | Ja       |         |
| `_contained`            | Nej        | Nej        | Nej        |         |
| `containedType`         | Nej        | Nej        | Nej        |         |
| `_score`                | Nej        | Nej        | Nej        |         |

## <a name="extended-operations"></a>Utökade åtgärder

Alla åtgärder som stöds och som utökar RESTful-API: et.

| Typ av Sök parameter | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|------------------------|-----------|-----------|-----------|---------|
| $export (hela systemet) | Ja       | Ja       | Ja       |         |
| Patient/$export        | Ja       | Ja       | Ja       |         |
| Grupp/$export          | Ja       | Ja       | Ja       |         |

## <a name="persistence"></a>Bevarande

Microsoft FHIR-servern har en pluggad beständig modul (se [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

För närvarande inkluderar FHIR-serverns kod med öppen källkod en implementering för [Azure Cosmos DB](../cosmos-db/index-overview.md) och [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB är en globalt distribuerad multi-Model-databas (SQL API, MongoDB API osv.). Den har stöd för olika [konsekvens nivåer](../cosmos-db/consistency-levels.md). Standardmallen för distribution konfigurerar FHIR-servern med `Strong` konsekvens, men konsekvens policyn kan ändras (i allmänhet avslappnadt) på en begäran med hjälp av begär `x-ms-consistency-level` ande huvudet.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

FHIR-servern använder [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) för åtkomst kontroll. Mer specifikt är Role-Based Access Control (RBAC) framtvingas, om `FhirServer:Security:Enabled` konfigurations parametern har angetts till `true` och alla begär Anden (förutom `/metadata` ) till FHIR-servern måste ha `Authorization` begär ande huvud inställt på `Bearer <TOKEN>` . Token måste innehålla en eller flera roller som definieras i `roles` anspråket. En begäran kommer att tillåtas om token innehåller en roll som tillåter den angivna åtgärden på den angivna resursen.

För närvarande tillämpas de tillåtna åtgärderna för en specifik roll *globalt* på API: et.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du läst om de FHIR-funktioner som stöds i Azure API för FHIR. Distribuera sedan Azure-API: et för FHIR.
 
>[!div class="nextstepaction"]
>[Distribuera Azure API för FHIR](fhir-paas-portal-quickstart.md)
