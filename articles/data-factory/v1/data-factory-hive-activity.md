---
title: Adatátalakítás a méhkas tevékenységgel – Azure
description: Ismerje meg, hogyan használhatja a kaptár-tevékenységeket a Azure Data Factory v1-ben a kaptár-lekérdezések futtatásához egy igény szerinti vagy saját HDInsight-fürtön.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9d14ddb172546e062b62a5a8dd98b49a0a6e1c6f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383024"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Az adatátalakítás a kaptár tevékenységgel Azure Data Factory 
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
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory-szolgáltatás aktuális verzióját használja, tekintse meg az [adatátalakítás a Data Factory-ben](../transform-data-using-hadoop-hive.md)című témakört.

A Data Factory [folyamat](data-factory-create-pipelines.md) HDInsight-struktúrájának tevékenysége a [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürtön hajtja végre a kaptár-lekérdezéseket. Ez a cikk az Adatátalakítási [tevékenységekről](data-factory-data-transformation-activities.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről.

> [!NOTE] 
> Ha még nem ismeri a Azure Data Factoryt, olvassa el az [Azure Data Factory bevezetését](data-factory-introduction.md) , és végezze el az oktatóanyagot: a cikk elolvasása előtt hozza [létre az első adatfolyamatát](data-factory-build-your-first-pipeline.md) . 

## <a name="syntax"></a>Syntax

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
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
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Szintaxis részletei
| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| name |A tevékenység neve |Yes |
| leírás |A tevékenység által használt szöveg leírása |No |
| típus |HDinsightHive |Yes |
| bemenetek |A kaptár tevékenység által felhasznált bemenetek |No |
| kimenetek |A kaptár tevékenység által létrehozott kimenetek |Yes |
| linkedServiceName |Hivatkozás a Data Factory társított szolgáltatásként regisztrált HDInsight-fürtre |Yes |
| parancsfájl |A struktúra-parancsfájl beágyazottként való megadásának meghatározása |No |
| scriptPath |Tárolja a kaptár parancsfájlt egy Azure Blob Storage-tárolóban, és adja meg a fájl elérési útját. Használja a "script" vagy a "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájl neve megkülönbözteti a kis-és nagybetűket. |No |
| meghatározza |Adja meg a paramétereket kulcs/érték párokként a kaptár parancsfájlban a "hiveconf" használatával való hivatkozáshoz. |No |

## <a name="example"></a>Példa
Vegyünk egy példát a game logs analyticsre, ahol a felhasználók által a vállalat által elindított játékokkal töltött idő azonosítására van szükség. 

A következő napló egy példaként szolgáló, vesszővel ( `,` ) elválasztott napló, amely a következő mezőket tartalmazza – ProfileID, SessionStart, időtartam, SrcIPAddress és szakasz.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Az adatfeldolgozáshoz használandó **struktúra-parancsfájl** :

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

A kaptár parancsfájl Data Factory-folyamatban való végrehajtásához a következőket kell tennie

1. Hozzon létre egy társított szolgáltatást a [saját HDInsight számítási fürt](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) regisztrálásához vagy [igény szerinti HDInsight számítási fürt](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)konfigurálásához. Hívjuk ezt a társított szolgáltatást "HDInsightLinkedService".
2. Hozzon létre egy [társított szolgáltatást](data-factory-azure-blob-connector.md) a kapcsolat konfigurálásához az Azure Blob Storage-hoz, amely az adattárolót üzemelteti. Hívjuk ezt a társított szolgáltatást "StorageLinkedService"
3. A bemeneti és a kimeneti adatokra mutató [adatkészletek](data-factory-create-datasets.md) létrehozása. Hívjuk meg a "HiveSampleIn" bemeneti adatkészletet és a "HiveSampleOut" kimeneti adatkészletet
4. Másolja a kaptár-lekérdezést fájlként az Azure-ba Blob Storage a következő lépésben konfigurálva: #2. Ha az adatok tárolására szolgáló tároló eltér a lekérdezési fájllal, hozzon létre egy külön Azure Storage-beli társított szolgáltatást, és tekintse meg azt a tevékenységben. A **scriptPath** használatával adja meg a kaptár-lekérdezési fájl és a **scriptlinkedservice szolgáltatás** elérési útját a parancsfájlt tartalmazó Azure-tároló megadásához. 
   
   > [!NOTE]
   > A kaptár parancsfájlt a tevékenység definíciójában is megadhatja a **szkript** tulajdonság használatával. Ezt a megközelítést nem javasoljuk, mert a parancsfájlban szereplő összes speciális karakternek meg kell menekülnie, és hibakeresési problémákhoz vezethet. Az ajánlott eljárás a #4 lépés végrehajtása.
   > 
   > 
5. Hozzon létre egy folyamatot a HDInsightHive tevékenységgel. A tevékenység feldolgozza/átalakítja az adatfeldolgozást.

  ```json
  {
    "name": "HiveActivitySamplePipeline",
       "properties": {
    "activities": [
      {
        "name": "HiveActivitySample",
        "type": "HDInsightHive",
        "inputs": [
        {
          "name": "HiveSampleIn"
        }
        ],
             "outputs": [
               {
                "name": "HiveSampleOut"
               }
             ],
             "linkedServiceName": "HDInsightLinkedService",
             "typeproperties": {
                 "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                 "scriptLinkedService": "StorageLinkedService"
             },
              "scheduler": {
          "frequency": "Hour",
                   "interval": 1
             }
           }
      ]
    }
  }
  ```

6. A folyamat üzembe helyezése. Részletekért lásd: [folyamatok létrehozása](data-factory-create-pipelines.md) című cikk. 
7. A folyamat figyelése a adatfeldolgozó-figyelési és-felügyeleti nézetek használatával. Részletekért lásd: [Data Factory folyamatok figyelése és kezelése](data-factory-monitor-manage-pipelines.md) . 

## <a name="specifying-parameters-for-a-hive-script"></a>Egy struktúra-parancsfájl paramétereinek megadása
Ebben a példában a rendszer naponta betölti a játék naplóit az Azure Blob Storageba, és egy olyan mappában tárolja őket, amely a dátummal és idővel particionálva van. Parametrizálja a struktúra parancsfájlját, és a bemeneti mappa helyét dinamikusan át kell adni a futtatókörnyezet során, és a kimenetet a dátummal és idővel is létrehozva.

A paraméteres struktúra parancsfájljának használatához tegye a következőket

* Határozza meg a paramétereket a **definiálva**.

  ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* A kaptár parancsfájlban tekintse át a következőt **: $ {hiveconf: parameterName}**. 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
  ## <a name="see-also"></a>Lásd még:
* [Pig-tevékenység](data-factory-pig-activity.md)
* [MapReduce tevékenység](data-factory-map-reduce.md)
* [Hadoop streaming-tevékenység](data-factory-hadoop-streaming-activity.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

