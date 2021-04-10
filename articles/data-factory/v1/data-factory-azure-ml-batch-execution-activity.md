---
title: Prediktív adatfolyamatok létrehozása Azure Data Factory használatával
description: Ismerteti, hogyan hozhat létre prediktív folyamatokat Azure Data Factory és Azure Machine Learning Studio használatával (klasszikus)
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 2773ab2a7caa1eb4d198495a3ebe4ef0c14a5a32
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785512"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Prediktív folyamatok létrehozása Azure Machine Learning Studio (klasszikus) és Azure Data Factory használatával

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

## <a name="introduction"></a>Bevezetés
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [adatátalakítás a Data Factory Machine learning használatával](../transform-data-using-machine-learning.md)című témakört.

### <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasszikus)
A [Azure Machine learning Studio (klasszikus)](https://azure.microsoft.com/documentation/services/machine-learning/) lehetővé teszi prediktív elemzési megoldások összeállítását, tesztelését és üzembe helyezését. A magas szintű nézetből három lépésben végezhető el:

1. **Hozzon létre egy képzési kísérletet**. Ezt a lépést Azure Machine Learning Studio (klasszikus) használatával hajthatja végre. A Studio (klasszikus) egy együttműködésen alapuló vizuális fejlesztési környezet, amely a prediktív elemzési modellek betanítására és tesztelésére használható.
2. **Alakítsa át prediktív kísérletre**. Ha a modell már meglévő adataival lett betanítva, és készen áll arra, hogy az új adatait is felhasználja, előkészíti és egyszerűsíti a kísérletet a pontozáshoz.
3. **Webszolgáltatásként való üzembe helyezése**. A pontozási kísérletet Azure-webszolgáltatásként teheti közzé. Ezt a webszolgáltatás-végponton keresztül is elküldheti a modellnek, és a modellbe beérkező eredményekre vonatkozó előrejelzéseket kaphat.

### <a name="azure-data-factory"></a>Azure Data Factory
**A Data Factory** egy felhőalapú adatintegrációs szolgáltatás, amely összehangolja és automatizálja az adatátvitelt és- **átalakítást** . Olyan Azure Data Factory használatával hozhat létre adatintegrációs megoldásokat, amelyek különböző adattárakból származó adatok betöltését, az adatok átalakítását és feldolgozását, valamint az eredmények közzétételét teszik közzé az adattárakban.

A Data Factoryval az adatok továbbítására és átalakítására szolgáló adatfolyamatokat hozhat létre, majd ütemezés szerint futtathatja a folyamatot (óránként, napi, heti stb. gyakorisággal). Ezenkívül látványos vizualizációkkal jelenítheti meg az adatfolyamatok közötti leszármaztatási és függőségi kapcsolatokat, valamint egyetlen, egységesített nézetben figyelheti az összes folyamatot, így egyszerűen kiszűrheti a problémákat és beállíthatja a figyelési riasztásokat.

A Azure Data Factory szolgáltatás használatának gyors megkezdéséhez tekintse meg a [Azure Data Factory bemutatása](data-factory-introduction.md) című cikket, és hozza [létre első folyamatát](data-factory-build-your-first-pipeline.md) .

### <a name="data-factory-and-machine-learning-studio-classic-together"></a>Data Factory és Machine Learning Studio (klasszikus) együtt
A Azure Data Factory lehetővé teszi, hogy könnyedén hozzon létre egy közzétett [Azure Machine learning Studio (klasszikus)][azure-machine-learning] webszolgáltatást használó folyamatokat a prediktív elemzésekhez. A **Batch-végrehajtási tevékenység** egy Azure Data Factory folyamat során meghívja a Studio (klasszikus) webszolgáltatás használatát, hogy előrejelzéseket készítsen a Batch-beli adatairól. A részletekért lásd: Azure Machine Learning Studio (klasszikus) webszolgáltatás meghívása a Batch végrehajtási tevékenység szakaszával.

Az idő múlásával a Studio (klasszikus) pontozási kísérletek prediktív modelljeit új bemeneti adatkészletek használatával kell áttanítani. Az alábbi lépések végrehajtásával Újrataníthatja a Studio (klasszikus) modellt egy Data Factory folyamatból:

1. Tegye közzé a betanítási kísérletet (nem prediktív kísérletet) webszolgáltatásként. Ezt a lépést a Studio (klasszikus) használatával teheti meg, mivel a prediktív kísérletet webszolgáltatásként teszi elérhetővé az előző forgatókönyvben.
2. A Studio (klasszikus) batch-végrehajtási tevékenység használatával meghívja a webszolgáltatást a betanítási kísérlethez. Alapvetően a Studio (klasszikus) batch végrehajtási tevékenységgel is meghívhatja a betanítási webszolgáltatás és a pontozási webszolgáltatás szolgáltatást.

Miután elvégezte a betanítást, frissítse a pontozási webszolgáltatást (webszolgáltatásként elérhető prediktív kísérlet) az újonnan betanított modellel a **Azure Machine learning Studio (klasszikus) frissítési erőforrás-tevékenység** használatával. Részletekért lásd: [modellek frissítése a frissítés erőforrás-tevékenységgel](data-factory-azure-ml-update-resource-activity.md) című cikkben.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Webszolgáltatások kötegelt végrehajtási tevékenységgel történő meghívása
A Azure Data Factory segítségével hangolhatja az adatáthelyezést és a feldolgozást, majd elvégezheti a Batch-végrehajtást a Studio (klasszikus) használatával. A legfelső szintű lépések:

1. Hozzon létre egy Azure Machine Learning Studio (klasszikus) társított szolgáltatást. A következő értékeket kell megadnia:

   1. **Kérelem URI-ja** a Batch végrehajtási API-hoz. A kérés URI-JÁT a webszolgáltatások lapon található **Batch-végrehajtás** hivatkozásra kattintva érheti el.
   2. A közzétett Studio (klasszikus) webszolgáltatás **API-kulcsa** . Az API-kulcsot a közzétett webszolgáltatásra kattintva érheti el.
   3. Használja az **AzureMLBatchExecution** tevékenységet.

      ![Machine Learning Studio (klasszikus) irányítópult](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Forgatókönyv: az Azure-beli adatokra hivatkozó webszolgáltatási bemeneteket/kimeneteket használó kísérletek Blob Storage
Ebben az esetben a Studio (klasszikus) webszolgáltatás előrejelzéseket készít az Azure Blob Storage-ból származó fájlokból származó adatokról, és a blob Storage-ban tárolja az előrejelzési eredményeket. A következő JSON egy Data Factory folyamatot határoz meg egy AzureMLBatchExecution-tevékenységgel. A tevékenység kimenetként a bemeneti és a **DecisionTreeResultBlob** adatkészletet **DecisionTreeInputBlob** . A **DecisionTreeInputBlob** a **webServiceInput** JSON-tulajdonság használatával továbbítja a webszolgáltatásnak. A **DecisionTreeResultBlob** a webszolgáltatás kimenetként adja át a **webServiceOutputs** JSON-tulajdonság használatával.

> [!IMPORTANT]
> Ha a webszolgáltatás több bemenetet is igénybe vesz, használja a **webServiceInputs** tulajdonságot a **webServiceInput** használata helyett. Tekintse meg a [webszolgáltatáshoz több bemenet szükséges](#web-service-requires-multiple-inputs) szakaszt a webServiceInputs tulajdonság használatához.
>
> A **webServiceInput** / **webServiceInputs** és a **webServiceOutputs** tulajdonságok ( **typeProperties**) által hivatkozott adatkészleteket is fel kell venni a tevékenység **bemenetei** és **kimenetei** közé.
>
> A Studio (klasszikus) kísérlet, a webszolgáltatás bemeneti és kimeneti portjai és a globális paraméterek alapértelmezett neve ("input1", "input2") testreszabható. A webServiceInputs, a webServiceOutputs és a globalParameters-beállításokhoz használt névnek pontosan egyeznie kell a kísérletekben szereplő nevekkel. A minta kérések hasznos adatait a Studio (klasszikus) végpontjának batch-végrehajtási Súgó lapján tekintheti meg a várt leképezés ellenőrzéséhez.
>
>

```json
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

> [!NOTE]
> A webszolgáltatásnak csak a AzureMLBatchExecution tevékenység bemeneteit és kimeneteit lehet paraméterként átadni. A fenti JSON-kódrészletben például a DecisionTreeInputBlob a AzureMLBatchExecution tevékenység bemenete, amelyet a rendszer a webServiceInput paraméterrel a webszolgáltatásnak bemenetként továbbít.
>
>

### <a name="example"></a>Példa
Ez a példa az Azure Storage-t használja a bemeneti és kimeneti adatok tárolásához.

Javasoljuk, hogy az [első folyamat létrehozása Data Factory][adf-build-1st-pipeline] oktatóanyaggal, mielőtt elkezdené a példát. Ebben a példában a Data Factory Editor használatával hozhat létre Data Factory összetevőket (társított szolgáltatások, adatkészletek, folyamat).

1. Hozzon létre egy **társított szolgáltatást** az **Azure Storage**-hoz. Ha a bemeneti és kimeneti fájlok különböző tárolási fiókokban vannak, két társított szolgáltatásra van szükség. A következő egy JSON-példa:

   ```json
   {
     "name": "StorageLinkedService",
     "properties": {
       "type": "AzureStorage",
       "typeProperties": {
         "connectionString": "DefaultEndpointsProtocol=https;AccountName= [acctName];AccountKey=[acctKey]"
       }
     }
   }
   ```

2. Hozza létre a **bemeneti** Azure Data Factory **adatkészletet**. A többi Data Factory adatkészlettől eltérően ezek az adatkészletek mind a **folderPath** , mind a **filename** értékeket tartalmazniuk kell. A particionálással az egyes batch-végrehajtásokat (minden adatszeletet) feldolgozhatja, illetve egyedi bemeneti és kimeneti fájlokat hozhat létre. Előfordulhat, hogy egy felsőbb rétegbeli tevékenységet is tartalmaznia kell a CSV-fájlformátumba való átalakításhoz, és az egyes szeletekhez tartozó Storage-fiókba kell helyeznie azt. Ebben az esetben nem kell megadnia az alábbi példában látható **külső** és **externalData** beállításokat, és a DecisionTreeInputBlob egy másik tevékenység kimeneti adatkészlete lenne.

   ```json
   {
     "name": "DecisionTreeInputBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/input",
         "fileName": "in.csv",
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "external": true,
       "availability": {
         "frequency": "Day",
         "interval": 1
       },
       "policy": {
         "externalData": {
           "retryInterval": "00:01:00",
           "retryTimeout": "00:10:00",
           "maximumRetry": 3
         }
       }
     }
   }
   ```

   A bemeneti CSV-fájlnak tartalmaznia kell az oszlop fejlécének sorát. Ha a **másolási tevékenység** használatával hozza létre vagy helyezi át a CSV-fájlt a blob Storage-ba, a fogadó tulajdonságot **igaz** értékre kell állítania **blobWriterAddHeader** . Például:

   ```json
   sink:
   {
     "type": "BlobSink",
     "blobWriterAddHeader": true
     }
   ```

   Ha a CSV-fájl nem tartalmazza a fejlécet, a következő hibaüzenet jelenhet meg: **hiba a tevékenységben: hiba történt a karakterlánc olvasása során. Váratlan jogkivonat: StartObject. Elérési út: "", 1. sor, 1. pozíció**.

3. Hozza létre a **kimeneti** Azure Data Factory **adatkészletet**. Ez a példa particionálással hoz létre egyedi kimeneti útvonalat az egyes szeletek végrehajtásához. A particionálás nélkül a tevékenység felülírja a fájlt.

   ```json
   {
     "name": "DecisionTreeResultBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/scored/{folderpart}/",
         "fileName": "{filepart}result.csv",
         "partitionedBy": [
           {
             "name": "folderpart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "yyyyMMdd"
             }
           },
           {
             "name": "filepart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "HHmmss"
             }
           }
         ],
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "availability": {
         "frequency": "Day",
         "interval": 15
       }
     }
   }
   ```

4. Hozzon létre egy típusú **társított szolgáltatást** : **AzureMLLinkedService**, amely biztosítja az API-kulcs és a modell batch-végrehajtási URL-címét.

   ```json
   {
     "name": "MyAzureMLLinkedService",
     "properties": {
       "type": "AzureML",
       "typeProperties": {
         "mlEndpoint": "https://[batch execution endpoint]/jobs",
         "apiKey": "[apikey]"
       }
     }
   }
   ```

5. Végül létrehozhat egy **AzureMLBatchExecution** tevékenységet tartalmazó folyamatot. Futásidőben a folyamat a következő lépéseket hajtja végre:

   1. Lekéri a bemeneti fájl helyét a bemeneti adatkészletből.
   2. Meghívja a Studio (klasszikus) batch végrehajtási API-ját
   3. A Batch-végrehajtási kimenetet a kimeneti adatkészletben megadott blobba másolja.

      > [!NOTE]
      > A AzureMLBatchExecution tevékenység nulla vagy több bemenettel és egy vagy több kimenettel is rendelkezhet.
      >
      >

      ```json
      {
        "name": "PredictivePipeline",
        "properties": {
          "description": "use AzureML model",
          "activities": [
            {
              "name": "MLActivity",
              "type": "AzureMLBatchExecution",
              "description": "prediction analysis on batch input",
              "inputs": [
                {
                  "name": "DecisionTreeInputBlob"
                }
                ],
              "outputs": [
                {
                  "name": "DecisionTreeResultBlob"
                }
                ],
              "linkedServiceName": "MyAzureMLLinkedService",
              "typeProperties":
                {
                "webServiceInput": "DecisionTreeInputBlob",
                "webServiceOutputs": {
                  "output1": "DecisionTreeResultBlob"
                }
                },
              "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
              }
            }
          ],
          "start": "2016-02-13T00:00:00Z",
          "end": "2016-02-14T00:00:00Z"
        }
      }
      ```

      A **kezdő** és a **záró** dátum/idő értékének [ISO formátumúnak](https://en.wikipedia.org/wiki/ISO_8601)kell lennie. Például: 2014-10-14T16:32:41Z. A **befejezési** időpont nem kötelező. Ha nem ad meg értéket a **Befejezés** tulajdonsághoz, a rendszer a következőt számítja ki: "**Start + 48 óra".** A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz. A JSON-tulajdonságokkal kapcsolatos információkért lásd: [JSON Scripting Reference](/previous-versions/azure/dn835050(v=azure.100)) (Referencia a JSON-parancsprogramokhoz).

      > [!NOTE]
      > A AzureMLBatchExecution tevékenység bemenetének megadása nem kötelező.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Forgatókönyv: az olvasó/író modulok használatával végzett kísérletek különböző tárolókban lévő adataira vonatkoznak
Egy másik gyakori forgatókönyv a Studio (klasszikus) kísérletek létrehozásakor az olvasó és az író modulok használata. Az olvasó modul az adatok kísérletbe való betöltésére szolgál, az író modul pedig az adatok a kísérletekből való mentéséhez. Az olvasó és az író modulok részletes ismertetését az MSDN Library [olvasó](/azure/machine-learning/studio-module-reference/import-data) és [író](/azure/machine-learning/studio-module-reference/export-data) témaköreiben találja.

Az olvasó-és író modulok használata esetén célszerű webszolgáltatási paramétert használni az olvasó/író modulok minden tulajdonságához. Ezek a webes paraméterek lehetővé teszik az értékek konfigurálását futásidőben. Létrehozhat például egy kísérletet egy olvasó modullal, amely egy Azure SQL Database: XXX.database.windows.net-t használ. A webszolgáltatás üzembe helyezését követően engedélyezni szeretné a webszolgáltatás felhasználói számára, hogy megadják a YYY.database.windows.net nevű másik logikai SQL Server-kiszolgálót. Webszolgáltatási paraméter használatával engedélyezheti ezt az értéket.

> [!NOTE]
> A webszolgáltatások bemenete és kimenete eltér a webszolgáltatás paramétereinek. Az első forgatókönyvben azt tapasztalja, hogy a Studio (klasszikus) webszolgáltatás bemenete és kimenete megadható. Ebben a forgatókönyvben egy olyan webszolgáltatás paramétereit adja át, amely az olvasó/író modulok tulajdonságainak felel meg.
>
>

Nézzük meg a webszolgáltatási paraméterek használatának forgatókönyvét. Van egy telepített Studio (klasszikus) webszolgáltatás, amely egy olvasó modult használ a Studio (klasszikus) által támogatott adatforrásokból származó adatok olvasásához (például: Azure SQL Database). A Batch-végrehajtás elvégzése után az eredményeket egy író modul (Azure SQL Database) segítségével kell megírni.  A kísérletekben nincs definiálva webszolgáltatás-bemenet és-kimenet. Ebben az esetben javasoljuk, hogy konfigurálja az olvasó és az író modulok releváns webszolgáltatás-paramétereit. Ez a konfiguráció lehetővé teszi, hogy az olvasó/író modulok a AzureMLBatchExecution tevékenység használatakor legyenek konfigurálva. A webszolgáltatás paramétereit az alábbi módon adhatja meg a tevékenység JSON- **globalParameters** szakaszában.

```json
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

A webszolgáltatás paramétereinek átadásához [Data Factory függvények](data-factory-functions-variables.md) is használhatók, ahogy az alábbi példában is látható:

```json
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> A webszolgáltatás paraméterei megkülönböztetik a kis-és nagybetűket, ezért ügyeljen arra, hogy a JSON-tevékenységben megadott nevek megfeleljenek a webszolgáltatás által elérhetőnek.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Olvasó modul használata az Azure blobban található több fájlból származó adatok olvasásához
A Pig és a kaptár tevékenységekkel rendelkező Big adatfolyamatok egy vagy több kimeneti fájlt is létrehozhatnak kiterjesztés nélkül. Ha például külső kaptár-táblázatot ad meg, a külső struktúra táblázatának az Azure Blob Storage-ban tárolt adattára a következő névvel 000000_0. Egy kísérlet olvasó moduljának használatával több fájlt is beolvashat, és az előrejelzésekhez használhatja őket.

Ha a Studio (klasszikus) kísérletben az olvasó modult használja, az Azure blobot bemenetként is megadhatja. Az Azure Blob Storage-ban található fájlok lehetnek a HDInsight-on futó Pig-és kaptár-parancsfájlok által előállított kimeneti fájlok (például: 000000_0). Az olvasó modul lehetővé teszi a fájlok (kiterjesztés nélküli) olvasását a **tároló, a könyvtár és a blob elérési útjának** konfigurálásával. A **tárolóra mutató elérési út** a tárolóra, a **könyvtárra és a blobra** mutat, hogy a fájlokat tartalmazó mappa a következő képen látható módon jelenjen meg. A csillag \* **() azt adja meg, hogy a tárolóban/mappában lévő összes fájl (azaz az aggregateddata/év = 2014/hónap-6/ \* )** a kísérlet részeként legyen beolvasva.

![Azure-Blob tulajdonságai](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Példa
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>AzureMLBatchExecution-tevékenységet tartalmazó folyamat webszolgáltatási paraméterekkel

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning Studio (classic) model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

A fenti JSON-példában:

* Az üzembe helyezett Studio (klasszikus) webszolgáltatás egy olvasót és egy író modult használ az adatok olvasására/írására egy Azure SQL Database. Ez a webszolgáltatás a következő négy paramétert teszi elérhetővé: az adatbázis-kiszolgáló neve, az adatbázis neve, a kiszolgáló felhasználói fiókjának neve és a kiszolgáló felhasználói fiókjának jelszava.
* A **kezdő** és a **záró** dátum/idő értékének [ISO formátumúnak](https://en.wikipedia.org/wiki/ISO_8601)kell lennie. Például: 2014-10-14T16:32:41Z. A **befejezési** időpont nem kötelező. Ha nem ad meg értéket a **Befejezés** tulajdonsághoz, a rendszer a következőt számítja ki: "**Start + 48 óra".** A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz. A JSON-tulajdonságokkal kapcsolatos információkért lásd: [JSON Scripting Reference](/previous-versions/azure/dn835050(v=azure.100)) (Referencia a JSON-parancsprogramokhoz).

### <a name="other-scenarios"></a>Egyéb forgatókönyvek
#### <a name="web-service-requires-multiple-inputs"></a>A webszolgáltatás több bemenetet igényel
Ha a webszolgáltatás több bemenetet is igénybe vesz, használja a **webServiceInputs** tulajdonságot a **webServiceInput** használata helyett. A **webServiceInputs** hivatkozott adatkészleteket is szerepelnie kell a tevékenység **bemenetei** között.

A Azure Machine Learning Studio (klasszikus) kísérlet, a webszolgáltatás bemeneti és kimeneti portjai és a globális paraméterek alapértelmezett neve ("input1", "input2") a testre szabható. A webServiceInputs, a webServiceOutputs és a globalParameters-beállításokhoz használt névnek pontosan egyeznie kell a kísérletekben szereplő nevekkel. A minta kérések hasznos adatait a Studio (klasszikus) végpontjának batch-végrehajtási Súgó lapján tekintheti meg a várt leképezés ellenőrzéséhez.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>A webszolgáltatás nem igényel bemenetet
A Azure Machine Learning Studio (klasszikus) batch-végrehajtási webszolgáltatások bármilyen munkafolyamatok, például R-vagy Python-parancsfájlok futtatására használhatók, amelyek nem igényelnek semmilyen bemenetet. Emellett előfordulhat, hogy a kísérlet olyan olvasó modullal van konfigurálva, amely nem tesz elérhetővé semmilyen GlobalParameters. Ebben az esetben a AzureMLBatchExecution tevékenység a következőképpen lesz konfigurálva:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>A webszolgáltatás nem igényel bemenetet/kimenetet
Előfordulhat, hogy a Azure Machine Learning Studio (klasszikus) batch-végrehajtási webszolgáltatás nem rendelkezik konfigurált webszolgáltatási kimenettel. Ebben a példában nincs webszolgáltatás-bemenet vagy-kimenet, és nincs GlobalParameters konfigurálva. Maga a tevékenység még mindig konfigurálva van, de nincs megadva webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>A webszolgáltatás olvasókat és írókat használ, és a tevékenység csak akkor fut le, ha más tevékenységek sikeresek voltak.
Lehetséges, hogy a Azure Machine Learning Studio (klasszikus) webszolgáltatás-olvasó és-író modulok úgy vannak konfigurálva, hogy GlobalParameters-mel vagy anélkül fussanak. Előfordulhat azonban, hogy olyan folyamatba kívánja beágyazni a szolgáltatási hívásokat, amely adatkészlet-függőségeket használ a szolgáltatás meghívására, ha egy felsőbb rétegbeli folyamat befejeződött. Ezt a módszert követve más műveleteket is elindíthat, miután a kötegelt végrehajtás befejeződött. Ebben az esetben a függőségeket a tevékenység bemenetei és kimenetei alapján fejezheti ki, anélkül, hogy azokat webszolgáltatási bemenetként vagy kimenetként nevezze el.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

Az **elvihetők** a következők:

* Ha a kísérleti végpont egy webServiceInput használ: azt egy blob-adatkészlet jelképezi, és a tevékenység bemenetei és a webServiceInput tulajdonság tartalmazza. Ellenkező esetben a webServiceInput tulajdonság nincs megadva.
* Ha a kísérleti végpont webServiceOutput (ka) t használ: ezeket a blob-adatkészletek jelölik, és a tevékenységek kimenetében és a webServiceOutputs tulajdonságban szerepelnek. A tevékenység kimeneteit és webServiceOutputs a kísérletben szereplő egyes kimenetek nevével kell leképezni. Ellenkező esetben a webServiceOutputs tulajdonság nincs megadva.
* Ha a kísérleti végpont globalParameter (ka) t tesz elérhetővé, azok a tevékenység globalParameters tulajdonságában kulcs, érték párokként jelennek meg. Ellenkező esetben a globalParameters tulajdonság nincs megadva. A kulcsok megkülönböztetik a kis-és nagybetűket. Az értékekben [Azure Data Factory függvények](data-factory-functions-variables.md) is használhatók.
* További adatkészletek is szerepelhetnek a tevékenység bemenetei és kimenetei tulajdonságaiban, a tevékenység typeProperties való hivatkozás nélkül. Ezek az adatkészletek a szelet függőségeivel szabályozzák a végrehajtást, de a AzureMLBatchExecution tevékenység más módon figyelmen kívül hagyja őket.


## <a name="updating-models-using-update-resource-activity"></a>Modellek frissítése a frissítési erőforrás tevékenység használatával
Miután elvégezte a betanítást, frissítse a pontozási webszolgáltatást (webszolgáltatásként elérhető prediktív kísérlet) az újonnan betanított modellel a **Azure Machine learning Studio (klasszikus) frissítési erőforrás-tevékenység** használatával. Részletekért lásd: [modellek frissítése a frissítés erőforrás-tevékenységgel](data-factory-azure-ml-update-resource-activity.md) című cikkben.

### <a name="reader-and-writer-modules"></a>Olvasó és író modulok
A webszolgáltatás paramétereinek használatának gyakori forgatókönyve az Azure SQL-olvasók és-írók használata. Az olvasó modul használatával adatok tölthetők be a studión kívüli adatkezelési szolgáltatásokból (klasszikus). Az író modul a kísérletekben tárolt adatok mentése a studión kívüli adatkezelési szolgáltatásba (klasszikus).

Az Azure Blob/Azure SQL Reader/Writer szolgáltatással kapcsolatos további információkért lásd: [olvasó](/azure/machine-learning/studio-module-reference/import-data) és [író](/azure/machine-learning/studio-module-reference/export-data) témakörök az MSDN Library-ben. Az előző szakaszban szereplő példa az Azure Blob Readert és az Azure Blob-írót használta. Ez a szakasz az Azure SQL Reader és az Azure SQL Writer használatát ismerteti.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
**K:** Több fájl is van, amelyeket az big data-folyamatok hoztak létre. Használhatom az AzureMLBatchExecution tevékenységet az összes fájlon?

**V:** Igen. További részletekért tekintse meg az **olvasói modul használata az Azure blobban található több fájlból származó adatok beolvasásához** című szakaszt.

## <a name="azure-machine-learning-studio-classic-batch-scoring-activity"></a>Azure Machine Learning Studio (klasszikus) batch pontozási tevékenység
Ha a **AzureMLBatchScoring** tevékenységet használja a Azure Machine learning Studio (klasszikus) integrálásához, javasoljuk, hogy használja a legújabb **AzureMLBatchExecution** -tevékenységet.

A AzureMLBatchExecution tevékenység az Azure SDK és a Azure PowerShell 2015-es kiadásában jelent meg.

Ha továbbra is a AzureMLBatchScoring tevékenységet szeretné használni, folytassa a szakasz olvasásával.

### <a name="azure-machine-learning-studio-classic-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Azure Machine Learning Studio (klasszikus) batch-pontozási tevékenység az Azure Storage használatával bemenet/kimenet esetén

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Webszolgáltatás paraméterei
A webszolgáltatás paramétereinek értékeinek megadásához vegyen fel egy **typeProperties** szakaszt a folyamat JSON-fájljának **AzureMLBatchScoringActivity** szakaszába, ahogy az alábbi példában is látható:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
A webszolgáltatás paramétereinek átadásához [Data Factory függvények](data-factory-functions-variables.md) is használhatók, ahogy az alábbi példában is látható:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> A webszolgáltatás paraméterei megkülönböztetik a kis-és nagybetűket, ezért ügyeljen arra, hogy a JSON-tevékenységben megadott nevek megfeleljenek a webszolgáltatás által elérhetőnek.
>
>

## <a name="see-also"></a>Lásd még:
* [Azure blogbejegyzés: a Azure Data Factory és a Azure Machine Learning első lépései](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/