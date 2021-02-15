---
title: Adatátalakítás az Hadoop streaming-tevékenység használatával – Azure
description: Megtudhatja, hogyan alakíthatja át az Hadoop streaming-tevékenységet egy Azure-beli adatgyárban az Hadoop streaming-programok futtatásával egy igény szerinti vagy saját HDInsight-fürtön.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 532ece3bcc8c62771511fb2608e824de846b54fe
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383092"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Adatátalakítás Hadoop-adatfolyam-továbbítási tevékenység használatával Azure Data Factory
> [!div class="op_single_selector" title1="Átalakítási tevékenységek"]
> * [Struktúra tevékenysége](data-factory-hive-activity.md) 
> * [Pig-tevékenység](data-factory-pig-activity.md)
> * [MapReduce tevékenység](data-factory-map-reduce.md)
> * [Hadoop streaming-tevékenység](data-factory-hadoop-streaming-activity.md)
> * [Spark-tevékenység](data-factory-spark.md)
> * [Az Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenysége](data-factory-azure-ml-batch-execution-activity.md)
> * [Az Azure Machine Learning Studio (klasszikus) erőforrás-frissítési tevékenysége](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az adatátalakítás a Data Factory Hadoop streaming Activity használatával](../transform-data-using-hadoop-streaming.md)című témakört.


Használhatja a HDInsightStreamingActivity tevékenységet egy Hadoop streaming-feladatoknak egy Azure Data Factory folyamatból való meghívásához. A következő JSON-kódrészlet a HDInsightStreamingActivity egy folyamat JSON-fájlban való használatának szintaxisát mutatja be. 

A Data Factory- [folyamat](data-factory-create-pipelines.md) HDInsight adatfolyam-továbbítási tevékenysége a [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürtön hajtja végre a Hadoop streaming-programokat. Ez a cikk az Adatátalakítási [tevékenységekről](data-factory-data-transformation-activities.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről.

> [!NOTE] 
> Ha még nem ismeri a Azure Data Factoryt, olvassa el az [Azure Data Factory bevezetését](data-factory-introduction.md) , és végezze el az oktatóanyagot: a cikk elolvasása előtt hozza [létre az első adatfolyamatát](data-factory-build-your-first-pipeline.md) . 

## <a name="json-sample"></a>JSON-minta
A rendszer automatikusan kitölti a HDInsight-fürtöt például programok (wc.exe és cat.exe) és az adatok (davinci.txt) alapján. Alapértelmezés szerint a HDInsight-fürt által használt tároló neve a fürt neve. Ha például a fürt neve myhdicluster, a társított blob-tároló neve myhdicluster lesz. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Vegye figyelembe a következő szempontokat:

1. Állítsa be a **linkedServiceName** a társított szolgáltatás nevére, amely arra a HDInsight-fürtre mutat, amelyen a streaming MapReduce-feladatot futtatja.
2. Állítsa be a tevékenység típusát a **HDInsightStreaming** értékre.
3. A **Mapper** tulajdonságnál adja meg a Mapper végrehajtható fájljának nevét. A példában a cat.exe a Mapper végrehajtható fájl.
4. A **csökkentő** tulajdonságnál adja meg a csökkentő végrehajtható fájl nevét. A példában a wc.exe a csökkentő végrehajtható fájl.
5. A **bemeneti** típus tulajdonságnál adja meg a Mapper bemeneti fájlját (beleértve a helyét is). A példában:: adfsample a blob-tároló, például a (z), a (z) és a (z) `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt` mappa, a davinci.txt pedig a blob.
6. A **kimeneti** típus tulajdonságnál adja meg a redukáló kimeneti fájlját (beleértve a helyét is). Az Hadoop streaming-feladatok kimenete a tulajdonsághoz megadott helyre íródik.
7. A **filePaths** szakaszban határozza meg a mapper és a csökkentő végrehajtható fájlok elérési útját. A példában a "adfsample/example/apps/wc.exe" adfsample a blob tároló, például az alkalmazások mappa, a wc.exe pedig a végrehajtható fájl.
8. A **fileLinkedService** tulajdonságnál adja meg az Azure Storage társított szolgáltatást, amely az filePaths szakaszban megadott fájlokat tartalmazó Azure Storage-tárolót jelöli.
9. Az **argumentumok** tulajdonságnál határozza meg a folyamatos átviteli feladatokhoz tartozó argumentumokat.
10. A **getDebugInfo** tulajdonság egy opcionális elem. Ha a hiba értékre van állítva, a naplók letöltése csak meghibásodás esetén történik meg. Ha a mindig értékre van állítva, a rendszer mindig letölti a naplókat a végrehajtási állapottól függetlenül.

> [!NOTE]
> Ahogy az a példában is látható, a **kimenetek** tulajdonsághoz meg kell adnia egy kimeneti adatkészletet a Hadoop adatfolyam-továbbítási tevékenységhez. Ez az adatkészlet csak egy olyan próbabábu-adatkészlet, amely a folyamat ütemtervének megadásához szükséges. A **bemenetek** tulajdonsághoz nem kell megadnia a tevékenységhez tartozó bemeneti adatkészletet.  
> 
> 

## <a name="example"></a>Példa
Az ebben a bemutatóban található folyamat futtatja az Azure HDInsight-fürtön lévő "streaming Map/csökkentse" programot. 

### <a name="linked-services"></a>Társított szolgáltatások
#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Először létre kell hoznia egy társított szolgáltatást, amely összekapcsolja az Azure HDInsight-fürt által az Azure-beli adatgyárhoz használt Azure-tárolót. Ha a következő kódot másolja/illeszti be, ne felejtse el lecserélni a fiók nevét és a fiók kulcsát az Azure Storage-beli nevére és kulcsára. 

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight társított szolgáltatás
Ezután létrehoz egy társított szolgáltatást, amely összekapcsolja az Azure HDInsight-fürtöt az Azure-beli adatgyárral. Ha a következő kódot másolja/illeszti be, cserélje le a HDInsight-fürtöt a HDInsight-fürt nevére, és változtassa meg a Felhasználónév és a jelszó értékét. 

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Adathalmazok
#### <a name="output-dataset"></a>Kimeneti adatkészlet
Az ebben a példában szereplő folyamat nem tesz elérhetővé semmilyen bemenetet. Meg kell adnia egy kimeneti adatkészletet a HDInsight streaming tevékenységhez. Ez az adatkészlet csak egy olyan próbabábu-adatkészlet, amely a folyamat ütemtervének megadásához szükséges. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Folyamat
Az ebben a példában szereplő folyamathoz csak egy: **HDInsightStreaming** típusú tevékenység tartozik. 

A rendszer automatikusan kitölti a HDInsight-fürtöt például programok (wc.exe és cat.exe) és az adatok (davinci.txt) alapján. Alapértelmezés szerint a HDInsight-fürt által használt tároló neve a fürt neve. Ha például a fürt neve myhdicluster, a társított blob-tároló neve myhdicluster lesz.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Lásd még:
* [Struktúra tevékenysége](data-factory-hive-activity.md)
* [Pig-tevékenység](data-factory-pig-activity.md)
* [MapReduce tevékenység](data-factory-map-reduce.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

