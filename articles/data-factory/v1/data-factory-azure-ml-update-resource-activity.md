---
title: Machine Learning modellek frissítése a Azure Data Factory használatával
description: Útmutató prediktív folyamatok létrehozásához Azure Data Factory v1 és Azure Machine Learning Studio (klasszikus) használatával
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 7a27ed657ba21d9e2125df903b40d74cd81eacf6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379301"
---
# <a name="updating-azure-machine-learning-studio-classic-models-using-update-resource-activity"></a>Azure Machine Learning Studio (klasszikus) modellek frissítése az erőforrás-frissítési tevékenység használatával

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
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a Machine learning-modellek frissítése a Data Factory-ben](../update-machine-learning-models.md)című témakört.

Ez a cikk a fő Azure Data Factory-Azure Machine Learning Studio (klasszikus) integrációs cikket egészíti ki: [prediktív folyamatok létrehozása Azure Machine learning Studio (klasszikus) és Azure Data Factory használatával](data-factory-azure-ml-batch-execution-activity.md). Ha még nem tette meg, tekintse át a fő cikket, mielőtt beolvassa ezt a cikket. 

## <a name="overview"></a>Áttekintés
Idővel a Azure Machine Learning Studio (klasszikus) pontozási kísérletek prediktív modelljeit új bemeneti adatkészletek használatával kell áttanítani. Miután végzett az újraképzéssel, frissítenie kell a pontozási webszolgáltatást az áttelepített ML-modellel. A Studio (klasszikus) modellek webszolgáltatásokon keresztül történő átképzésének és frissítésének tipikus lépései a következők:

1. Hozzon létre egy kísérletet [Azure Machine learning Studio (klasszikus)](https://studio.azureml.net).
2. Ha elégedett a modellel, használja a Azure Machine Learning Studio (klasszikus) lehetőséget a webes szolgáltatások közzétételére a **betanítási kísérlet** és a pontozási/**prediktív kísérlet** során.

A következő táblázat ismerteti az ebben a példában használt webszolgáltatásokat.  A részletekért lásd: [Azure Machine learning Studio (klasszikus) modellek átképzése programozott](../../machine-learning/classic/retrain-machine-learning-model.md) módon.

- A **betanítási webszolgáltatás** betanítási és betanított modelleket hoz létre. Az átképzés kimenete egy. ilearner fájl az Azure Blob Storage-ban. Az **alapértelmezett végpont** automatikusan létrejön, amikor webszolgáltatásként teszi közzé a betanítási kísérletet. Több végpontot is létrehozhat, de a példa csak az alapértelmezett végpontot használja.
- **Pontozási webszolgáltatás** – a rendszer címkézetlen adatpéldákat fogad, és előrejelzéseket készít. Az előrejelzés kimenete különböző formákat tartalmazhat, például egy. csv-fájlt vagy Azure SQL Database-sorokat a kísérlet konfigurációjától függően. A rendszer automatikusan létrehozza az alapértelmezett végpontot, amikor a prediktív kísérletet webszolgáltatásként teszi közzé. 

Az alábbi ábra a képzés és a pontozási végpontok közötti kapcsolatot ábrázolja Azure Machine Learning Studio (klasszikus).

![Webszolgáltatások](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

A **betanítási webszolgáltatás** a **Azure Machine learning Studio (klasszikus) batch-végrehajtási tevékenység** használatával hívható meg. A betanítási webszolgáltatás meghívása ugyanaz, mint egy Azure Machine Learning Studio (klasszikus) webszolgáltatás (pontozási webszolgáltatás) meghívása az adatpontozási szolgáltatáshoz. Az előző fejezetekben részletesen ismertetjük, hogyan hívhat meg egy Azure Machine Learning Studio (klasszikus) webszolgáltatást egy Azure Data Factory folyamatból. 

A **pontozási webszolgáltatás** meghívásához használja az **Azure Machine learning Studio (klasszikus) frissítési erőforrás tevékenységét** , hogy frissítse a webszolgáltatást az újonnan betanított modellel. A következő példák a társított szolgáltatás definícióit biztosítják: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>A pontozási webszolgáltatás egy klasszikus webszolgáltatás
Ha a pontozási webszolgáltatás egy **klasszikus webszolgáltatás**, akkor a Azure Portal használatával hozza létre a második **nem alapértelmezett és frissíthető végpontot** . A lépések a [végpontok létrehozása](../../machine-learning/classic/create-endpoint.md) című cikkben olvashatók. Miután létrehozta a nem alapértelmezett frissíthető végpontot, hajtsa végre a következő lépéseket:

* Kattintson a **Batch-végrehajtás** elemre a **mlEndpoint** JSON-tulajdonság URI-értékének lekéréséhez.
* Kattintson az **erőforrás frissítése** hivatkozásra az **updateResourceEndpoint** JSON-tulajdonság URI-értékének lekéréséhez. Az API-kulcs maga a végpont oldalon található (a jobb alsó sarokban).

![frissíthető végpont](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

Az alábbi példa egy JSON-definíciót biztosít a AzureML társított szolgáltatáshoz. A társított szolgáltatás a apiKey használja a hitelesítéshez.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>A pontozás webszolgáltatása Azure Resource Manager webszolgáltatás 
Ha a webszolgáltatás az új típusú webszolgáltatás, amely egy Azure Resource Manager-végpontot tesz elérhetővé, nem kell hozzáadnia a második **nem alapértelmezett** végpontot. A társított szolgáltatás **updateResourceEndpoint** formátuma: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Ha a webszolgáltatást a [Azure Machine learning Studio (klasszikus) webszolgáltatások portálján](https://services.azureml.net/)szeretné lekérdezni, megadhatja az URL-címeket a hely tulajdonosainak. Az új típusú frissítési erőforrás-végponthoz HRE (Azure Active Directory) token szükséges. Az **servicePrincipalId** és a **servicePrincipalKey** a Studio (klasszikus) társított szolgáltatásban adható meg. Lásd: [egyszerű szolgáltatásnév létrehozása és engedélyek kiosztása az Azure-erőforrások kezeléséhez](../../active-directory/develop/howto-create-service-principal-portal.md). Itt látható egy példa AzureML társított szolgáltatás definíciója: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

A következő forgatókönyv további részleteket tartalmaz. Ez egy példa a Studio (klasszikus) modellek Azure Data Factory folyamatból való átképzésére és frissítésére.

## <a name="scenario-retraining-and-updating-a-studio-classic-model"></a>Forgatókönyv: Studio (klasszikus) modell átképzése és frissítése
Ez a szakasz egy minta folyamatot biztosít, amely a **Azure Machine learning Studio (klasszikus) kötegelt végrehajtási tevékenységet** használja a modell újratanításához. A folyamat a **Azure Machine learning Studio (klasszikus) frissítési erőforrás tevékenységgel** is frissíti a modellt a pontozási webszolgáltatás szolgáltatásban. A szakasz a példában szereplő társított szolgáltatások, adatkészletek és folyamatok JSON-kódrészleteit is tartalmazza.

Itt látható a mintavételezési folyamat diagram nézete. Amint láthatja, a Studio (klasszikus) batch-végrehajtási tevékenység betanítja a betanítási adatokat, és egy képzési kimenetet (iLearner-fájlt) hoz létre. A Studio (klasszikus) frissítési erőforrás tevékenysége ezt a betanítási kimenetet veszi át, és frissíti a modellt a pontozási webszolgáltatás végpontján. Az erőforrás frissítése tevékenység nem hoz létre kimenetet. A placeholderBlob csak egy olyan próbabábu kimeneti adatkészlete, amelyre a Azure Data Factory szolgáltatásnak szüksége van a folyamat futtatásához.

![folyamat diagramja](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Azure Blob Storage társított szolgáltatás:
Az Azure Storage a következő adatkészleteket tartalmazza:

* betanítási adatgyűjtés. A Studio (klasszikus) betanítási webszolgáltatás bemeneti adata.  
* iLearner-fájl. A Studio (klasszikus) betanítási webszolgáltatás kimenete. Ez a fájl a frissítési erőforrás tevékenységének bemenete is.  

Itt látható a társított szolgáltatás JSON-definíciója:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Betanítás bemeneti adatkészlete:
A következő adatkészlet a Studio (klasszikus) betanítási webszolgáltatás bemeneti betanítási információit jelöli. A Studio (klasszikus) batch-végrehajtási tevékenység bemenetként veszi át ezt az adatkészletet.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
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

### <a name="training-output-dataset"></a>Kimeneti adatkészlet betanítása:
A következő adatkészlet a Azure Machine Learning Studio (klasszikus) betanítási webszolgáltatás kimeneti iLearner-fájlját jelöli. A Azure Machine Learning Studio (klasszikus) batch-végrehajtási tevékenység létrehozza ezt az adatkészletet. Ez az adatkészlet a Azure Machine Learning Studio (klasszikus) frissítési erőforrás tevékenységének bemenete is.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-studio-classic-training-endpoint"></a>Társított Service for Studio (klasszikus) betanítási végpont
A következő JSON-kódrészlet egy Studio (klasszikus) társított szolgáltatást definiál, amely az oktatási webszolgáltatás alapértelmezett végpontján mutat.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

A **Azure Machine learning Studio (klasszikus)** területen tegye a következőket a **MlEndpoint** és a **apiKey** értékeinek lekéréséhez:

1. A bal oldali menüben kattintson a **Web Services** elemre.
2. A webszolgáltatások listájában kattintson a **betanítási webszolgáltatás** elemre.
3. Kattintson az API- **kulcs** szövegmező melletti Másolás lehetőségre. Illessze be a vágólapra a kulcsot a Data Factory JSON-szerkesztőbe.
4. A **Azure Machine learning Studio (klasszikus)** területen kattintson a **Batch-végrehajtási** hivatkozás elemre.
5. Másolja a kérési **URI** -t a **kérelem** szakaszból, és ILLESSZE be a Data Factory JSON-szerkesztőbe.   

### <a name="linked-service-for-studio-classic-updatable-scoring-endpoint"></a>Társított szolgáltatás a Studio (klasszikus) frissíthető pontozási végpontja számára:
A következő JSON-kódrészlet egy Studio (klasszikus) társított szolgáltatást definiál, amely a pontozási webszolgáltatás nem alapértelmezett frissíthető végpontján mutat.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Helyőrző kimeneti adatkészlete:
A Studio (klasszikus) frissítési erőforrás tevékenysége nem eredményez kimenetet. Azure Data Factory azonban egy kimeneti adatkészletet kell vezetnie egy folyamat ütemtervének megadásához. Ezért ebben a példában egy dummy/helyőrző adathalmazt használunk.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Folyamat
A folyamat két tevékenységgel rendelkezik: **AzureMLBatchExecution** és **AzureMLUpdateResource**. A Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenység bemenetként veszi át a betanítási adatokat, és kimenetként létrehoz egy iLearner-fájlt. A tevékenység behívja a betanítási webszolgáltatást (webszolgáltatásként közzétett betanítási kísérlet) a bemeneti betanítási adatokkal, és a webszolgáltatásból fogadja a ilearner-fájlt. A placeholderBlob csak egy olyan próbabábu kimeneti adatkészlete, amelyre a Azure Data Factory szolgáltatásnak szüksége van a folyamat futtatásához.

![folyamat diagramja](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```