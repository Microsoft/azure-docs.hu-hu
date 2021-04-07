---
title: MapReduce program meghívása Azure Data Factory
description: Ismerje meg, hogyan dolgozhat fel az adatok egy Azure HDInsight-fürtön futó MapReduce-programok Azure-beli adatgyárból való futtatásával.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f1f54e972d59d3de3b0f93b3150ee1150eb6f612
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786396"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>MapReduce-programok meghívása Data Factory
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
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory-szolgáltatás aktuális verzióját használja, tekintse meg az [adatátalakítás az MapReduce-tevékenység használatával című részt a Data Factoryban](../transform-data-using-hadoop-map-reduce.md).


A Data Factory- [folyamat](data-factory-create-pipelines.md) HDInsight MapReduce-tevékenysége a MapReduce-programokat [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürtön hajtja végre. Ez a cikk az Adatátalakítási [tevékenységekről](data-factory-data-transformation-activities.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről.

> [!NOTE] 
> Ha még nem ismeri a Azure Data Factoryt, olvassa el az [Azure Data Factory bevezetését](data-factory-introduction.md) , és végezze el az oktatóanyagot: a cikk elolvasása előtt hozza [létre az első adatfolyamatát](data-factory-build-your-first-pipeline.md) .  

## <a name="introduction"></a>Bevezetés
Egy Azure-beli adatfeldolgozó folyamata összekapcsolt számítási szolgáltatások használatával dolgozza fel a társított tárolási szolgáltatásokban tárolt adatokkal. Olyan tevékenységek sorát tartalmazza, amelyekben minden tevékenység egy adott feldolgozási műveletet hajt végre. Ez a cikk a HDInsight MapReduce tevékenység használatát ismerteti.

A Pig/kaptár-parancsfájlok Windows-/Linux-alapú HDInsight-fürtökön való futtatásával kapcsolatos részletekért tekintse meg a [Pig](data-factory-pig-activity.md) és a [kaptár](data-factory-hive-activity.md) című témakört a HDInsight Pig és a kaptár tevékenységek használatával. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON a HDInsight MapReduce-tevékenységhez
A HDInsight tevékenység JSON-definíciójában: 

1. Állítsa be a **tevékenység** **típusát** a **HDInsight** értékre.
2. Adja meg a **Osztálynév** tulajdonság osztályának nevét.
3. Adja meg a JAR-fájl elérési útját, beleértve a **jarFilePath** tulajdonság fájlnevét.
4. A **jarLinkedService** tulajdonsághoz tartozó jar-fájlt tartalmazó Azure Blob Storagera hivatkozó társított szolgáltatás megadása.   
5. Az **argumentumok** szakaszban adhatja meg a MapReduce program argumentumait. Futásidőben a MapReduce-keretrendszer néhány további argumentuma (például: MapReduce. job. Tags) jelenik meg. Ha meg szeretné különböztetni az argumentumokat a MapReduce argumentumokkal, érdemes lehet mindkét beállítást és értéket argumentumként használni az alábbi példában látható módon (-s,--bemenet,--output stb.).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
   A HDInsight MapReduce tevékenység használatával bármilyen MapReduce jar-fájlt futtathat egy HDInsight-fürtön. A folyamat alábbi JSON-definíciójában a HDInsight tevékenység Mahout JAR-fájl futtatására van konfigurálva.

## <a name="sample-on-github"></a>Minta a GitHubon
Letölthető egy minta a HDInsight MapReduce-tevékenység használatáról: [Data Factory minták a githubon](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>A Word Count program futtatása
Az ebben a példában szereplő folyamat futtatja az Azure HDInsight-fürtön található Térkép és a program csökkentése programot.   

### <a name="linked-services"></a>Társított szolgáltatások
Először létre kell hoznia egy társított szolgáltatást, amely összekapcsolja az Azure HDInsight-fürt által az Azure-beli adatgyárhoz használt Azure-tárolót. Ha a következő kódot másolja/illeszti be, ne felejtse el lecserélni a **fiók nevét** és a **fiók kulcsát** az Azure Storage-beli nevére és kulcsára. 

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás

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
Ezután létrehoz egy társított szolgáltatást, amely összekapcsolja az Azure HDInsight-fürtöt az Azure-beli adatgyárral. Ha a következő kódot másolja/illeszti be, cserélje le a **HDInsight-fürtöt** a HDInsight-fürt nevére, és változtassa meg a Felhasználónév és a jelszó értékét.   

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
Az ebben a példában szereplő folyamat nem tesz elérhetővé semmilyen bemenetet. Meg kell adnia egy kimeneti adatkészletet a HDInsight MapReduce tevékenységhez. Ez az adatkészlet csak egy olyan próbabábu-adatkészlet, amely a folyamat ütemtervének megadásához szükséges.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Folyamat
Az ebben a példában szereplő folyamathoz csak egy: HDInsightMapReduce típusú tevékenység tartozik. A JSON legfontosabb tulajdonságai a következők: 

| Tulajdonság | Jegyzetek |
|:--- |:--- |
| típus |A típust **HDInsightMapReduce** értékre kell beállítani. |
| className |Az osztály neve: **WordCount** |
| jarFilePath |Az osztályt tartalmazó jar-fájl elérési útja. Ha a következő kódot másolja/illeszti be, ne felejtse el módosítani a fürt nevét. |
| jarLinkedService |Az Azure Storage társított szolgáltatása, amely tartalmazza a jar-fájlt. Ez a társított szolgáltatás a HDInsight-fürthöz társított tárterületre hivatkozik. |
| argumentumok |A WordCount program két argumentumot, egy bemenetet és egy kimenetet vesz igénybe. A bemeneti fájl a davinci.txt fájl. |
| frequency/interval |A tulajdonságok értékei egyeznek a kimeneti adatkészlettel. |
| linkedServiceName |a korábban létrehozott HDInsight társított szolgáltatásra hivatkozik. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Spark-programok futtatása
A MapReduce tevékenység használatával Spark-programokat futtathat a HDInsight Spark-fürtökön. A részletekért lásd: [Invoke Spark programs from Azure Data Factory](data-factory-spark.md) (Spark-programok meghívása az Azure Data Factory-ból).  

[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: /previous-versions/azure/dn834987(v=azure.100)
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Lásd még:
* [Struktúra tevékenysége](data-factory-hive-activity.md)
* [Pig-tevékenység](data-factory-pig-activity.md)
* [Hadoop streaming-tevékenység](data-factory-hadoop-streaming-activity.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)