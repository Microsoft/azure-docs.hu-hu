---
title: Adatátalakítás az Hadoop Pig-tevékenységgel
description: Megtudhatja, hogyan használhatja a Pig-tevékenységet egy Azure-beli adatgyárban a Pig-parancsfájlok igény szerinti vagy saját HDInsight-fürtön való futtatásához.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: 1d0f5ce9dfbf09023b57accb761b5dd85b0f3599
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375714"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Az adatátalakítás Hadoop Pig-tevékenységgel Azure Data Factory

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-pig-activity.md)
> * [Aktuális verzió](transform-data-using-hadoop-pig.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A HDInsight Pig tevékenység egy Data Factory- [folyamatban](concepts-pipelines-activities.md) a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürtön végzett Pig-lekérdezéseket hajt végre. Ez a cikk az Adatátalakítási [tevékenységekről](transform-data.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről.

Ha még nem ismeri a Azure Data Factoryt, olvassa el a [Azure Data Factory bevezetését](introduction.md) , és végezze el az [oktatóanyagot: az adatátalakítást](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

## <a name="syntax"></a>Syntax

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
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

| Tulajdonság            | Leírás                              | Kötelező |
| ------------------- | ---------------------------------------- | -------- |
| name                | A tevékenység neve                     | Yes      |
| leírás         | A tevékenység által használt szöveg leírása | No       |
| típus                | A kaptár tevékenység esetén a tevékenység típusa HDinsightPig. | Yes      |
| linkedServiceName   | Hivatkozás a Data Factory társított szolgáltatásként regisztrált HDInsight-fürtre. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk. | Yes      |
| Scriptlinkedservice szolgáltatás | Hivatkozás egy olyan Azure Storage társított szolgáltatásra, amely a végrehajtandó Pig-szkript tárolására szolgál. Itt csak az **[Azure Blob Storage](./connector-azure-blob-storage.md)** és **[ADLS Gen2](./connector-azure-data-lake-storage.md)** társított szolgáltatások támogatottak. Ha nem megadja ezt a társított szolgáltatást, a rendszer a HDInsight társított szolgáltatásban definiált Azure Storage társított szolgáltatást használja. | No       |
| scriptPath          | Adja meg a Scriptlinkedservice szolgáltatás által hivatkozott Azure Storage-ban tárolt parancsfájl elérési útját. A fájl neve megkülönbözteti a kis-és nagybetűket. | No       |
| getDebugInfo        | Megadja, hogy a rendszer mikor másolja a naplófájlokat a Scriptlinkedservice szolgáltatás által meghatározott HDInsight-fürt (vagy) által használt Azure-tárolóba. Megengedett értékek: nincs, mindig vagy sikertelen. Alapértelmezett érték: nincs. | No       |
| argumentumok           | Argumentumok tömbjét adja meg egy Hadoop feladatokhoz. Az argumentumok parancssori argumentumként lesznek átadva az egyes feladatokhoz. | No       |
| meghatározza             | Adja meg a paramétereket kulcs/érték párokként a Pig-szkripten belüli hivatkozáshoz. | Nem       |

## <a name="next-steps"></a>Következő lépések
A következő cikkekből megtudhatja, hogyan alakíthat át más módon az adatátalakítást: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Struktúra tevékenysége](transform-data-using-hadoop-hive.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-tevékenység](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)