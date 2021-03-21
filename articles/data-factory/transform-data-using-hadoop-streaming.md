---
title: Adatátalakítás az Hadoop streaming-tevékenységgel
description: A cikk azt ismerteti, hogyan lehet az Hadoop adatfolyam-továbbítási tevékenységet használni a Azure Data Factoryban az adatátalakításhoz a Hadoop-fürtön futó Hadoop streaming programok futtatásával
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: e2a9bc9d664ba15da3cdefa5cf28519ab703d6ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361434"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Adatátalakítás Hadoop-adatfolyam-továbbítási tevékenység használatával Azure Data Factory
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-hadoop-streaming-activity.md)
> * [Aktuális verzió](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Data Factory [folyamat](concepts-pipelines-activities.md) HDInsight adatfolyam-továbbítási tevékenysége a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürtön hajtja végre a Hadoop streaming-programokat. Ez a cikk az Adatátalakítási [tevékenységekről](transform-data.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről.

Ha még nem ismeri a Azure Data Factoryt, olvassa el a [Azure Data Factory bevezetését](introduction.md) , és végezze el az [oktatóanyagot: az adatátalakítást](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

## <a name="json-sample"></a>JSON-minta
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Szintaxis részletei

| Tulajdonság          | Leírás                              | Kötelező |
| ----------------- | ---------------------------------------- | -------- |
| name              | A tevékenység neve                     | Yes      |
| leírás       | A tevékenység által használt szöveg leírása | No       |
| típus              | Hadoop adatfolyam-továbbítási tevékenység esetén a tevékenység típusa HDInsightStreaming. | Yes      |
| linkedServiceName | Hivatkozás a Data Factory társított szolgáltatásként regisztrált HDInsight-fürtre. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk. | Yes      |
| leképező            | A Mapper végrehajtható fájljának nevét adja meg | Yes      |
| szűkítő           | A csökkentő végrehajtható fájl nevét adja meg | Yes      |
| egyesítő          | A Combiner végrehajtható fájljának nevét adja meg | No       |
| fileLinkedService | Hivatkozás egy Azure Storage társított szolgáltatásra, amely a leképezett Mapper, Combiner és redukáló programok tárolására szolgál. Itt csak az **[Azure Blob Storage](./connector-azure-blob-storage.md)** és **[ADLS Gen2](./connector-azure-data-lake-storage.md)** társított szolgáltatások támogatottak. Ha nem megadja ezt a társított szolgáltatást, a rendszer a HDInsight társított szolgáltatásban definiált Azure Storage társított szolgáltatást használja. | No       |
| filePath          | Adja meg a fileLinkedService által hivatkozott Azure Storage-ban tárolt Mapper, Combiner és redukáló programok elérési útjának tömbjét. Az elérési út megkülönbözteti a kis- és nagybetűket. | Yes      |
| bemenet             | Meghatározza a Mapper bemeneti fájljának WASB elérési útját. | Yes      |
| output            | Megadja a szűkítő kimeneti fájljának WASB elérési útját. | Yes      |
| getDebugInfo      | Megadja, hogy a rendszer mikor másolja a naplófájlokat a Scriptlinkedservice szolgáltatás által meghatározott HDInsight-fürt (vagy) által használt Azure-tárolóba. Megengedett értékek: nincs, mindig vagy sikertelen. Alapértelmezett érték: nincs. | No       |
| argumentumok         | Argumentumok tömbjét adja meg egy Hadoop feladatokhoz. Az argumentumok parancssori argumentumként lesznek átadva az egyes feladatokhoz. | No       |
| meghatározza           | Adja meg a paramétereket kulcs/érték párokként a kaptár-parancsfájlon belüli hivatkozáshoz. | Nem       | 

## <a name="next-steps"></a>Következő lépések
A következő cikkekből megtudhatja, hogyan alakíthat át más módon az adatátalakítást: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Struktúra tevékenysége](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)