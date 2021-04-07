---
title: Adatátalakítás a Pig tevékenységgel Azure Data Factory
description: Megtudhatja, hogyan használhatja a Pig-tevékenységet a Azure Data Factory v1-ben a Pig-parancsfájlok futtatásához igény szerinti vagy saját HDInsight-fürtön.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 85c34bfc306b49bb826a342846bf5e4557e3052f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786362"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Adatátalakítás a Pig tevékenységgel Azure Data Factory
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
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory-szolgáltatás aktuális verzióját használja, tekintse meg az [adatátalakítás Data Factory a Pig tevékenységgel](../transform-data-using-hadoop-pig.md)című témakört.


A HDInsight Pig-tevékenység egy Data Factory [folyamat](data-factory-create-pipelines.md) során a Pig-lekérdezéseket a [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürtön hajtja végre. Ez a cikk az Adatátalakítási [tevékenységekről](data-factory-data-transformation-activities.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről.

> [!NOTE] 
> Ha még nem ismeri a Azure Data Factoryt, olvassa el az [Azure Data Factory bevezetését](data-factory-introduction.md) , és végezze el az oktatóanyagot: a cikk elolvasása előtt hozza [létre az első adatfolyamatát](data-factory-build-your-first-pipeline.md) . 

## <a name="syntax"></a>Syntax

```JSON
{
  "name": "HiveActivitySamplePipeline",
  "properties": {
    "activities": [
      {
        "name": "Pig Activity",
        "description": "description",
        "type": "HDInsightPig",
        "inputs": [
          {
            "name": "input tables"
          }
        ],
        "outputs": [
          {
            "name": "output tables"
          }
        ],
        "linkedServiceName": "MyHDInsightLinkedService",
        "typeProperties": {
          "script": "Pig script",
          "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
      }
    ]
  }
}
```

## <a name="syntax-details"></a>Szintaxis részletei

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| name |A tevékenység neve |Yes |
| leírás |A tevékenység által használt szöveg leírása |No |
| típus |HDinsightPig |Yes |
| bemenetek |A Pig tevékenység által felhasznált egy vagy több bemenet |No |
| kimenetek |A Pig tevékenység által előállított egy vagy több kimenet |Yes |
| linkedServiceName |Hivatkozás a Data Factory társított szolgáltatásként regisztrált HDInsight-fürtre |Yes |
| parancsfájl |Adja meg a Pig-szkriptet beágyazottként |No |
| scriptPath |Tárolja a Pig-szkriptet egy Azure Blob Storage-tárolóban, és adja meg a fájl elérési útját. Használja a "script" vagy a "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájl neve megkülönbözteti a kis-és nagybetűket. |No |
| meghatározza |Adja meg a paramétereket kulcs/érték párokként a Pig-szkripten belüli hivatkozáshoz |No |

## <a name="example"></a>Példa
Vegyünk például egy példa a game logs analyticsre, ahol a játékosok által a vállalat által elindított játékokkal töltött időt szeretné azonosítani.

A következő példa egy vesszővel (,) tagolt fájlt mutat be. A következő mezőket tartalmazza – ProfileID, SessionStart, időtartam, SrcIPAddress és szakasz.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Az adatfeldolgozásra szolgáló **Pig-szkript** :

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

A Pig szkript Data Factory folyamaton való végrehajtásához hajtsa végre a következő lépéseket:

1. Hozzon létre egy társított szolgáltatást a [saját HDInsight számítási fürt](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) regisztrálásához vagy [igény szerinti HDInsight számítási fürt](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)konfigurálásához. Hívjuk ezt a társított szolgáltatás **HDInsightLinkedService**.
2. Hozzon létre egy [társított szolgáltatást](data-factory-azure-blob-connector.md) a kapcsolat konfigurálásához az Azure Blob Storage-hoz, amely az adattárolót üzemelteti. Hívjuk ezt a társított szolgáltatás **StorageLinkedService**.
3. A bemeneti és a kimeneti adatokra mutató [adatkészletek](data-factory-create-datasets.md) létrehozása. Hívjuk a bemeneti adatkészlet **PigSampleIn** és a kimeneti adatkészlet **PigSampleOut**.
4. Másolja a Pig-lekérdezést egy olyan fájlba, amelyet az Azure Blob Storage a #2 lépésben konfigurált. Ha az adatok tárolására szolgáló Azure Storage nem azonos a lekérdezési fájllal, hozzon létre egy külön Azure Storage-beli társított szolgáltatást. Tekintse át a társított szolgáltatást a tevékenység konfigurációjában. A **scriptPath** használatával adja meg a Pig-parancsfájl és a **scriptlinkedservice szolgáltatás** elérési útját. 
   
   > [!NOTE]
   > A Pig szkriptet a tevékenység definíciójában is megadhatja a **script** tulajdonság használatával. Ez a megközelítés azonban nem ajánlott, mivel a parancsfájlban szereplő összes speciális karakternek el kell menekülnie, és hibakeresési problémákhoz vezethet. Az ajánlott eljárás a #4 lépés végrehajtása.
   >
   >
5. Hozza létre a folyamatot a HDInsightPig tevékenységgel. Ez a tevékenység a HDInsight-fürtön futó Pig-szkript futtatásával dolgozza fel a bemeneti adatokat.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
6. A folyamat üzembe helyezése. Részletekért lásd: [folyamatok létrehozása](data-factory-create-pipelines.md) című cikk. 
7. A folyamat figyelése a adatfeldolgozó-figyelési és-felügyeleti nézetek használatával. Részletekért lásd: [Data Factory folyamatok figyelése és kezelése](data-factory-monitor-manage-pipelines.md) .

## <a name="specifying-parameters-for-a-pig-script"></a>A Pig-szkript paramétereinek megadása
Vegye figyelembe a következő példát: a rendszer naponta betölti a játék naplóit az Azure Blob Storageba, és egy, a dátum és idő alapján particionált mappában tárolja. Parametrizálja a Pig-szkriptet, és a bemeneti mappa helyét dinamikusan át kell adni a futtatókörnyezet során, és a kimenetet is a dátummal és idővel kell elkészíteni.

A paraméteres Pig-parancsfájl használatához tegye a következőket:

* Határozza meg a paramétereket a **definiálva**.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
* A Pig-szkriptben a következő példában látható módon tekintse át a paramétereket a "**$parameterName**" használatával:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Lásd még:
* [Struktúra tevékenysége](data-factory-hive-activity.md)
* [MapReduce tevékenység](data-factory-map-reduce.md)
* [Hadoop streaming-tevékenység](data-factory-hadoop-streaming-activity.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
