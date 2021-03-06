---
title: Transformera data i molnet med hjälp av PowerShell
description: Det här PowerShell-skriptet transformerar data i molnet genom att köra Spark-program på ett Azure HDInsight Spark kluster.
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: 3af38f4cae53807c7268f00017f02f1325116fb6
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637062"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShell-skript – transformera data i molnet med hjälp av Azure Data Factory

Det här exemplet på PowerShell-skriptet skapar en pipeline som transformerar data i molnet genom att köra Spark-program på ett Azure HDInsight Spark kluster. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Azure Storage konto** . Skapa ett Python-skript och en indatafil och överför dem till Azure Storage. Spark-programmets utdata lagras på det här lagringskontot. Spark-klustret på begäran använder samma lagringskonto som den primära lagringen.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Överföra Python-skriptet till ditt Blob Storage-konto
1. Skapa en python-fil med namnet **WordCount_Spark. py** med följande innehåll: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Ersätt **&lt; storageAccountName &gt;** med namnet på ditt Azure Storage-konto. Spara sedan filen. 
3. Skapa en container med namnet **adftutorial** i Azure Blob Storage om den inte finns. 
4. Skapa en mapp med namnet **spark** .
5. Skapa en undermapp med namnet **script** under mappen **spark** . 
6. Överför filen **WordCount_Spark.py** till undermappen **script** . 


### <a name="upload-the-input-file"></a>Överföra indatafilen
1. Skapa en fil med namnet **minecraftstory.txt** med lite text. Spark-programmet räknar antalet ord i texten. 
2. Skapa en undermapp med namnet `inputfiles` i `spark` mappen i BLOB-behållaren. 
3. Överför `minecraftstory.txt` till mappen `inputfiles`. 

## <a name="sample-script"></a>Exempelskript
> [!IMPORTANT]
> Det här skriptet skapar JSON-filer som definierar Data Factory entiteter (länkad tjänst, data uppsättning och pipeline) på hård disken i mappen c:\ projektbevakningsmappen.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Rensa distribution

När du har kört exempel skriptet kan du använda följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Om du vill ta bort data fabriken från resurs gruppen kör du följande kommando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon:

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Skapa en datafabrik. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Skapar en länkad tjänst i data fabriken. En länkad tjänst länkar ett data lager eller en beräkning till en data fabrik. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Skapar en pipeline i data fabriken. En pipeline innehåller en eller flera aktiviteter som utför en viss åtgärd. I den här pipelinen transformerar en spark-aktivitet data genom att köra ett program på ett Azure HDInsight Spark-kluster. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | Skapar en körning för pipelinen. Med andra ord körs pipelinen. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Hämtar information om körningen av aktiviteten (aktivitets körning) i pipelinen. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).

Ytterligare Azure Data Factory PowerShell-skript exempel finns i [Azure Data Factory PowerShell-exempel](../samples-powershell.md).