---
title: IP-adresser i Azure Integration Runtime
description: Lär dig vilka IP-adresser du måste tillåta inkommande trafik från, för att konfigurera brand väggar korrekt för att skydda nätverks åtkomsten till data lager.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: fb83be59a3ccb11ea8bbd88307596937caa2b354
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638303"
---
# <a name="azure-integration-runtime-ip-addresses"></a>IP-adresser i Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Vilka IP-adresser som Azure Integration Runtime använder beror på den region där din Azure integration runtime finns. *Alla* Azures integrerings körningar som finns i samma region använder samma IP-adressintervall.

> [!IMPORTANT]  
> Data flöden och Azure Integration Runtime som aktiverar hanterade Virtual Network stöder inte användning av fasta IP-intervall.
>
> Du kan använda dessa IP-intervall för data förflyttning, pipeline och externa aktiviteter. Dessa IP-intervall kan användas för filtrering i data lager/nätverks säkerhets grupper (NSG)/brand väggar för inkommande åtkomst från Azure integration Runtime. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime IP-adresser: vissa regioner

Tillåt trafik från IP-adresserna som anges för Azure integration runtime i den aktuella Azure-region där dina resurser finns. Du kan hämta en lista över tjänst etiketter från service märken från [service Tags-länken för hämtning av IP-intervall](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). Om Azure-regionen till exempel är **AustraliaEast** kan du hämta en lista över IP-intervall från **DataFactory. AustraliaEast** .


## <a name="known-issue-with-azure-storage"></a>Känt problem med Azure Storage

* När du ansluter till Azure Storage-kontot har IP-regler ingen påverkan på begär Anden som kommer från Azure integration runtime i samma region som lagrings kontot. Mer information [finns i den här artikeln](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range). 

  I stället rekommenderar vi [att du använder betrodda tjänster när du ansluter till Azure Storage](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Nästa steg

* [Säkerhets överväganden för data förflyttning i Azure Data Factory](data-movement-security-considerations.md)