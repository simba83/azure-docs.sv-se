---
title: Common Data Model-formatet
description: Transformera data med hjälp av common data Model metadata system
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: daperlov
ms.openlocfilehash: 452aa3406ac09dd8342d8ade0b56b126067b7582
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636416"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Gemensamt data modell format i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

COMMON data service-systemet (common data Model) gör det möjligt för data och dess innebörd att enkelt delas mellan program och affärs processer. Mer information finns i Översikt över [common data Model](/common-data-model/) .

I Azure Data Factory kan användare omvandla data från common data service entiteter i både model.jsi och manifest form som lagras i [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) med hjälp av mappnings data flöden. Du kan också sinka data i common data service-format med common data service-entitetsreferenser som kommer att använda dina data i CSV-eller Parquet-format i partitionerade mappar. 

> [!NOTE]
> Common data Model (common data service) format Connector för ADF-dataflöden är för närvarande tillgängligt som en offentlig för hands version.

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

Den gemensamma data modellen är tillgänglig som en [infogad data uppsättning](data-flow-source.md#inline-datasets) i mappa data flöden som både en källa och en mottagare.

> [!NOTE]
> När du skriver common data service-entiteter måste du ha en befintlig common data service Entity-definition (metadata schema) som redan har definierats för användning som referens. Data flödes mottagaren för ADF läser den common data service och importerar schemat till din mottagare för fält mappning.

### <a name="source-properties"></a>Käll egenskaper

I tabellen nedan visas de egenskaper som stöds av en common data service-källa. Du kan redigera dessa egenskaper på fliken **käll alternativ** .

| Namn | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Formatet måste vara `cdm` | yes | `cdm` | format |
| Format för metadata | Där enhets referensen till data finns. Om du använder common data service version 1,0 väljer du manifest. Om du använder en common data service-version före 1,0 väljer du model.jspå. | Ja | `'manifest'` eller `'model'` | manifestType |
| Rot plats: behållare | Behållarens namn på mappen common data service | yes | Sträng | Fil Systems |
| Rot plats: mappsökväg | Rotmappens plats för mappen common data service | yes | Sträng | folderPath |
| Manifest fil: enhets Sök väg | Mappsökväg för entiteten i rotmappen | nej | Sträng | entityPath |
| Manifest fil: manifest namn | Manifest filens namn. Standardvärdet är ' default '  | Nej | Sträng | manifestName |
| Filtrera efter senast ändrad | Välj att filtrera filer baserat på när de senast ändrades | nej | Timestamp | modifiedAfter <br> modifiedBefore | 
| Länkad schema tjänst | Den länkade tjänsten där sökkorpus finns | Ja, om du använder manifest | `'adlsgen2'` eller `'github'` | corpusStore | 
| Enhets referens behållare | Containerns sökkorpus finns i | Ja, om du använder manifest och sökkorpus i ADLS Gen2 | Sträng | adlsgen2_fileSystem |
| Enhets referens databas | GitHub-lagringsplatsnamn | Ja, om du använder manifest och sökkorpus i GitHub | Sträng | github_repository |
| Enhets referens gren | Gren för GitHub-lagringsplats | Ja, om du använder manifest och sökkorpus i GitHub | Sträng |  github_branch |
| Sökkorpus-mapp | sökkorpus rot plats | Ja, om du använder manifest | Sträng | corpusPath |
| Sökkorpus-entitet | Sökväg till entitetsreferens | yes | Sträng | entitetsrelation |
| Det gick inte att hitta några filer | Om värdet är true uppstår ett fel inte om inga filer hittas | nej | `true` eller `false` | ignoreNoFilesFound |

Om den enhets definition som du vill använda i din käll omvandling finns i samma katalog som din datamapp, kan du avmarkera kryss rutan Använd entitet från sökkorpus och bara skriva in entiteten för entiteten som du vill använda som enhets referens.

### <a name="sink-settings"></a>Mottagar inställningar

* Peka på den common data service som innehåller definitionen för den entitet som du vill skriva.

![enhets inställningar](media/data-flow/common-data-model-111.png "Entitetsreferens")

* Definiera partitionens sökväg och format för de utdatafiler som du vill att ADF ska använda för att skriva dina entiteter.

![format för entitet](media/data-flow/common-data-model-222.png "Format för entitet")

* Ange platsen för utdatafilen och plats och namn för manifest filen.

![common data service-plats](media/data-flow/common-data-model-333.png "COMMON data service-plats")


#### <a name="import-schema"></a>Importera schema

COMMON data service är endast tillgänglig som en infogad data uppsättning och har som standard inte ett associerat schema. Hämta kolumnens metadata genom att klicka på knappen **Importera schema** på fliken **projektion** . Detta gör att du kan referera till kolumn namn och data typer som anges av sökkorpus. Om du vill importera schemat måste en [fel söknings session för data flöde](concepts-data-flow-debug-mode.md) vara aktiv och du måste ha en befintlig definitions fil för common data service för att peka på.

När du mappar data flödes kolumner till enhets egenskaper i Sink-omvandlingen, klickar du på fliken mappning och väljer Importera schema. ADF läser enhets referensen som du pekade på i dina Sink-alternativ, så att du kan mappa till mål common data service-schemat.

![Inställningar för common data service-mottagare](media/data-flow/common-data-model-444.png "COMMON data service-mappning")

> [!NOTE]
>  När du använder model.jsav ursprungs typ som härstammar från Power BI eller Power Platform data flöden kan du stöta på "sökkorpus-sökvägen är null eller tom" fel från käll omvandlingen. Detta beror troligen på formateringsfel i sökvägen till partitionens plats i model.jsfilen. Följ dessa steg för att åtgärda detta: 

1. Öppna model.jspå filen i en text redigerare
2. Hitta partitionerna. Plats egenskap 
3. Ändra "blob.core.windows.net" till "dfs.core.windows.net"
4. Korrigera all "% 2F"-kodning i URL: en till "/"
 

### <a name="cdm-source-data-flow-script-example"></a>Skript exempel för common data service-käll data flöde

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>Egenskaper för mottagare

I tabellen nedan visas de egenskaper som stöds av en common data service-mottagare. Du kan redigera dessa egenskaper på fliken **Inställningar** .

| Namn | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Formatet måste vara `cdm` | yes | `cdm` | format |
| Rot plats: behållare | Behållarens namn på mappen common data service | yes | Sträng | Fil Systems |
| Rot plats: mappsökväg | Rotmappens plats för mappen common data service | yes | Sträng | folderPath |
| Manifest fil: enhets Sök väg | Mappsökväg för entiteten i rotmappen | nej | Sträng | entityPath |
| Manifest fil: manifest namn | Manifest filens namn. Standardvärdet är ' default ' | Nej | Sträng | manifestName |
| Länkad schema tjänst | Den länkade tjänsten där sökkorpus finns | yes | `'adlsgen2'` eller `'github'` | corpusStore | 
| Enhets referens behållare | Containerns sökkorpus finns i | Ja, om sökkorpus i ADLS Gen2 | Sträng | adlsgen2_fileSystem |
| Enhets referens databas | GitHub-lagringsplatsnamn | Ja, om sökkorpus i GitHub | Sträng | github_repository |
| Enhets referens gren | Gren för GitHub-lagringsplats | Ja, om sökkorpus i GitHub | Sträng |  github_branch |
| Sökkorpus-mapp | sökkorpus rot plats | yes | Sträng | corpusPath |
| Sökkorpus-entitet | Sökväg till entitetsreferens | yes | Sträng | entitetsrelation |
| Sökväg till partition | Plats där partitionen skrivs | nej | Sträng | partitionPath |
| Rensa mappen | Om målmappen rensas innan den skrivs | nej | `true` eller `false` | truncate |
| Format typ | Välj att ange Parquet-format | nej | `parquet` om det anges | under format |
| Kolumn avgränsare | Om du skriver till DelimitedText, så här begränsar du kolumner | Ja, om du skriver till DelimitedText | Sträng | columnDelimiter |
| Första raden som rubrik | Om du använder DelimitedText, om kolumn namnen läggs till som en rubrik | nej | `true` eller `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>Exempel på data flödes skript för common data service-mottagare

Det associerade data flödes skriptet är:

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>Nästa steg

Skapa en [käll omvandling](data-flow-source.md) i mappnings data flödet.