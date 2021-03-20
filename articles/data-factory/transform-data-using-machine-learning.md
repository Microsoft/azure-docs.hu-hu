---
title: Prediktív adatfolyamatok létrehozása
description: Ebből a témakörből megtudhatja, hogyan hozhat létre prediktív folyamatot a Azure Data Factory Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenység használatával.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/16/2020
ms.openlocfilehash: 37a31891c3c1d812b396548036c4b59cc6523c2d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375663"
---
# <a name="create-a-predictive-pipeline-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Prediktív folyamat létrehozása Azure Machine Learning Studio (klasszikus) és Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Aktuális verzió](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A [Azure Machine learning Studio (klasszikus)](https://azure.microsoft.com/documentation/services/machine-learning/) lehetővé teszi prediktív elemzési megoldások összeállítását, tesztelését és üzembe helyezését. A magas szintű nézetből három lépésben végezhető el:

1. **Hozzon létre egy képzési kísérletet**. Ezt a lépést a Azure Machine Learning Studio (klasszikus) használatával hajthatja végre. A Azure Machine Learning Studio (klasszikus) egy együttműködési vizuális fejlesztési környezet, amely a prediktív elemzési modellek betanítására és tesztelésére használható.
2. **Alakítsa át prediktív kísérletre**. Ha a modell már meglévő adataival lett betanítva, és készen áll arra, hogy az új adatait is felhasználja, előkészíti és egyszerűsíti a kísérletet a pontozáshoz.
3. **Webszolgáltatásként való üzembe helyezése**. A pontozási kísérletet Azure-webszolgáltatásként teheti közzé. Ezt a webszolgáltatás-végponton keresztül is elküldheti a modellnek, és a modellből eredményül kapott előrejelzéseket kaphat.

### <a name="data-factory-and-azure-machine-learning-studio-classic-together"></a>Data Factory és Azure Machine Learning Studio (klasszikus) együtt
A Azure Data Factory lehetővé teszi, hogy könnyedén hozzon létre egy közzétett [Azure Machine learning Studio (klasszikus)](https://azure.microsoft.com/documentation/services/machine-learning) webszolgáltatást használó folyamatokat a prediktív elemzésekhez. A **Batch végrehajtási tevékenység** Azure Data Factory folyamatokban való használatával meghívhat egy Azure Machine learning Studio (klasszikus) webszolgáltatást, hogy előrejelzéseket készítsen a Batch-ben lévő adatairól.

Idővel a Azure Machine Learning Studio (klasszikus) pontozási kísérletek prediktív modelljeit új bemeneti adatkészletek használatával kell áttanítani. Az alábbi lépések végrehajtásával Újrataníthatja a modelleket egy Data Factory folyamatból:

1. Tegye közzé a betanítási kísérletet (nem prediktív kísérletet) webszolgáltatásként. Ezt a lépést a Azure Machine Learning Studioon (klasszikus) kell végrehajtania, mivel a prediktív kísérletet webszolgáltatásként teszi elérhetővé az előző forgatókönyvben.
2. A betanítási kísérlethez használja a Azure Machine Learning Studio (klasszikus) batch-végrehajtási tevékenységet a webszolgáltatás meghívásához. Alapvetően a Azure Machine Learning Studio (klasszikus) batch-végrehajtási tevékenységet használhatja a betanítási webszolgáltatás és a pontozási webszolgáltatás meghívásához is.

Miután elvégezte a betanítást, frissítse a pontozási webszolgáltatást (webszolgáltatásként elérhető prediktív kísérlet) az újonnan betanított modellel a **Azure Machine learning Studio (klasszikus) frissítési erőforrás-tevékenység** használatával. Részletekért lásd: [modellek frissítése a frissítés erőforrás-tevékenységgel](update-machine-learning-models.md) című cikkben.

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Azure Machine Learning Studio (klasszikus) társított szolgáltatás

Létrehoz egy **Azure Machine learning Studio (klasszikus)** társított szolgáltatást, amely egy Azure Machine learning Studio (klasszikus) webszolgáltatás egy Azure-beli adatgyárhoz való csatolására szolgál. A társított szolgáltatást a Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenység használja, és [frissíti az erőforrás-tevékenységet](update-machine-learning-models.md).

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

A JSON-definíció tulajdonságaival kapcsolatos leírásért tekintse meg a [számítási társított szolgáltatások](compute-linked-services.md) című cikket.

A Azure Machine Learning Studio (klasszikus) a klasszikus webszolgáltatásokat és az új webszolgáltatásokat egyaránt támogatja a prediktív kísérlethez. Kiválaszthatja, hogy melyik elemet szeretné használni Data Factoryból. A Azure Machine Learning Studio (klasszikus) társított szolgáltatás létrehozásához szükséges információk beszerzéséhez nyissa meg a https://services.azureml.net következőt:, ahol megjelenik az összes (új) webszolgáltatás és a klasszikus webszolgáltatás. **Kattintson az** elérni kívánt webszolgáltatásra, majd kattintson a felhasználás lap elemre. Másolja a **apiKey** tulajdonság **elsődleges kulcsát** és a **mlEndpoint** tulajdonsághoz tartozó **Batch-kérelmeket** .

![Azure Machine Learning Studio (klasszikus) webszolgáltatások](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenység

A következő JSON-kódrészlet egy Azure Machine Learning Studio (klasszikus) batch-végrehajtási tevékenységet határoz meg. A tevékenység definíciója a korábban létrehozott Azure Machine Learning Studio (klasszikus) társított szolgáltatásra mutató hivatkozást tartalmaz.

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Tulajdonság          | Leírás                              | Kötelező |
| :---------------- | :--------------------------------------- | :------- |
| name              | A folyamatban szereplő tevékenység neve     | Yes      |
| leírás       | A tevékenység működését leíró szöveg  | No       |
| típus              | Data Lake Analytics U-SQL tevékenység esetén a tevékenység típusa **AzureMLBatchExecution**. | Yes      |
| linkedServiceName | Társított szolgáltatások a Azure Machine Learning Studio (klasszikus) társított szolgáltatáshoz. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk. | Yes      |
| webServiceInputs  | Kulcs-érték párok, Azure Machine Learning Studio (klasszikus) webszolgáltatás-bemenetek nevének leképezése. A kulcsnak meg kell egyeznie a közzétett Azure Machine Learning Studio (klasszikus) webszolgáltatásban definiált bemeneti paraméterekkel. Az érték egy Azure Storage-beli társított szolgáltatások és FilePath tulajdonságok pár, amely a bemeneti Blobok helyét adja meg. | No       |
| webServiceOutputs | Kulcs-érték párok, Azure Machine Learning Studio (klasszikus) webszolgáltatás-kimenetek nevének leképezése. A kulcsnak meg kell egyeznie a közzétett Azure Machine Learning Studio (klasszikus) webszolgáltatásban definiált kimeneti paraméterekkel. Az érték egy Azure Storage-beli társított szolgáltatások és FilePath tulajdonságok pár, amely a kimeneti Blobok helyét adja meg. | No       |
| globalParameters  | A Azure Machine Learning Studio (klasszikus) batch-végrehajtási szolgáltatási végpontnak átadandó kulcs-érték párok. A kulcsoknak meg kell egyezniük a közzétett Azure Machine Learning Studio (klasszikus) webszolgáltatásban definiált webszolgáltatás-paraméterek neveivel. Az értékek a Azure Machine Learning Studio (klasszikus) batch-végrehajtási kérelem GlobalParameters tulajdonságában lesznek átadva | No       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>1. forgatókönyv: az Azure-ban tárolt adatokra hivatkozó webszolgáltatási bemeneteket/kimeneteket használó kísérletek Blob Storage

Ebben az esetben a Azure Machine Learning Studio (klasszikus) webszolgáltatás előrejelzést készít az Azure Blob Storage-ból származó fájlokból származó adatokról, és a blob Storage-ban tárolja az előrejelzési eredményeket. A következő JSON egy Data Factory folyamatot határoz meg egy AzureMLBatchExecution-tevékenységgel. A bemeneti és kimeneti adatok az Azure blog Storage-ban LinkedName és FilePath pár használatával hivatkoznak. A bemenetek és a kimenetek mintául szolgáló társított szolgáltatásában különböző társított szolgáltatások használhatók a Data Factory összes bemenetéhez/kimenetéhez, hogy képes legyen a megfelelő fájlok felvételére és a Azure Machine Learning Studio (klasszikus) webszolgáltatásba való küldésre.

> [!IMPORTANT]
> A Azure Machine Learning Studio (klasszikus) kísérlet, a webszolgáltatás bemeneti és kimeneti portjai, valamint a globális paraméterek alapértelmezett neve ("input1", "input2") a testre szabható. A webServiceInputs, a webServiceOutputs és a globalParameters-beállításokhoz használt névnek pontosan egyeznie kell a kísérletekben szereplő nevekkel. Az Azure Machine Learning Studio (klasszikus) végpontjának batch-végrehajtási Súgó lapján megtekintheti a minta kérések hasznos adatait a várt leképezés ellenőrzéséhez.


```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>2. forgatókönyv: az olvasó/író modulok használatával végzett kísérletek különböző tárolókban lévő adataira
Azure Machine Learning Studio (klasszikus) kísérletek létrehozásakor egy másik gyakori forgatókönyv az adatimportálás és a kimeneti adatmodulok használata. Az adatok importálása modul használatával betöltheti az adatokat egy kísérletbe, és a kimeneti adatok modulban adatokat menthet a kísérletekből. Az adatok és a kimeneti adatmodulok importálásával kapcsolatos további információkért lásd: adatok és [kimeneti adatok](/azure/machine-learning/studio-module-reference/export-data) [importálása](/azure/machine-learning/studio-module-reference/import-data) témakörök az MSDN Library webhelyen.

Az Adatimportálási és-kimeneti adatmodulok használatakor célszerű webszolgáltatási paramétert használni a modulok minden tulajdonságához. Ezek a webes paraméterek lehetővé teszik az értékek konfigurálását futásidőben. Létrehozhat például egy kísérletet egy olyan adatimportálási modullal, amely Azure SQL Database: XXX.database.windows.net-t használ. A webszolgáltatás üzembe helyezését követően engedélyezni szeretné a webszolgáltatás felhasználóinak, hogy megadják a nevű másik logikai SQL Servert `YYY.database.windows.net` . Webszolgáltatási paraméter használatával engedélyezheti ezt az értéket.

> [!NOTE]
> A webszolgáltatások bemenete és kimenete eltér a webszolgáltatás paramétereinek. Az első forgatókönyvben azt tapasztalja, hogy egy Azure Machine Learning Studio (klasszikus) webszolgáltatás bemenete és kimenete megadható. Ebben a forgatókönyvben egy olyan webszolgáltatás paramétereit adja át, amely megfelel az importálási adatok/kimeneti adatmodulok tulajdonságainak.

Nézzük meg a webszolgáltatási paraméterek használatának forgatókönyvét. Van egy telepített Azure Machine Learning Studio (klasszikus) webszolgáltatás, amely egy olvasó modult használ a Azure Machine Learning Studio (klasszikus) által támogatott adatforrásokból származó adatok olvasásához (például: Azure SQL Database). A Batch-végrehajtás elvégzése után az eredményeket egy író modul (Azure SQL Database) segítségével kell megírni.  A kísérletekben nincs definiálva webszolgáltatás-bemenet és-kimenet. Ebben az esetben javasoljuk, hogy konfigurálja az olvasó és az író modulok releváns webszolgáltatás-paramétereit. Ez a konfiguráció lehetővé teszi, hogy az olvasó/író modulok a AzureMLBatchExecution tevékenység használatakor legyenek konfigurálva. A webszolgáltatás paramétereit az alábbi módon adhatja meg a tevékenység JSON- **globalParameters** szakaszában.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> A webszolgáltatás paraméterei megkülönböztetik a kis-és nagybetűket, ezért ügyeljen arra, hogy a JSON-tevékenységben megadott nevek megfeleljenek a webszolgáltatás által elérhetőnek.

Miután elvégezte a betanítást, frissítse a pontozási webszolgáltatást (webszolgáltatásként elérhető prediktív kísérlet) az újonnan betanított modellel a **Azure Machine learning Studio (klasszikus) frissítési erőforrás-tevékenység** használatával. Részletekért lásd: [modellek frissítése a frissítés erőforrás-tevékenységgel](update-machine-learning-models.md) című cikkben.

## <a name="next-steps"></a>Következő lépések
A következő cikkekből megtudhatja, hogyan alakíthat át más módon az adatátalakítást:

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Struktúra tevékenysége](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-tevékenység](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)