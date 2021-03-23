---
title: Azure Machine Learning Studio (klasszikus) modellek frissítése Azure Data Factory használatával
description: Útmutató prediktív folyamatok létrehozásához Azure Data Factory és Azure Machine Learning Studio használatával (klasszikus)
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 5eac6f939ffcc39961f508a993ca17757221503f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783098"
---
# <a name="update-azure-machine-learning-studio-classic-models-by-using-update-resource-activity"></a>Azure Machine Learning Studio (klasszikus) modellek frissítése az erőforrás frissítése tevékenység használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a fő Azure Data Factory-Azure Machine Learning Studio (klasszikus) integrációs cikket egészíti ki: [prediktív folyamatok létrehozása Azure Machine learning Studio (klasszikus) és Azure Data Factory használatával](transform-data-using-machine-learning.md). Ha még nem tette meg, tekintse át a fő cikket, mielőtt beolvassa ezt a cikket.

## <a name="overview"></a>Áttekintés
A végrehajtott Azure Machine Learning Studio (klasszikus) modellek folyamatának részeként a rendszer betanítja és menti a modellt. Ezt követően egy prediktív webszolgáltatás létrehozására használhatja. A webszolgáltatás ezután felhasználható a webhelyeken, az irányítópultokon és a Mobile apps szolgáltatásban.

A Azure Machine Learning Studio (klasszikus) használatával létrehozott modellek általában nem statikusak. Mivel az új adatforgalom elérhetővé válik, vagy ha az API felhasználója saját adatmennyiséggel rendelkezik, a modellt újra kell tanítani. 

Előfordulhat, hogy az újraképzés gyakran előfordul. A kötegelt végrehajtási tevékenységgel és az erőforrás-frissítési tevékenységgel működővé tenni a Azure Machine Learning Studio (klasszikus) modell átképzését és a prediktív webszolgáltatás frissítését a Data Factory használatával.

Az alábbi ábra a képzés és a prediktív webszolgáltatások közötti kapcsolatot ábrázolja.

![Webszolgáltatások](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Azure Machine Learning Studio (klasszikus) erőforrás-frissítési tevékenység

A következő JSON-kódrészlet egy Azure Machine Learning Studio (klasszikus) batch-végrehajtási tevékenységet határoz meg.

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| Tulajdonság                      | Leírás                              | Kötelező |
| :---------------------------- | :--------------------------------------- | :------- |
| name                          | A folyamatban szereplő tevékenység neve     | Igen      |
| leírás                   | A tevékenység működését leíró szöveg  | Nem       |
| típus                          | A Azure Machine Learning Studio (klasszikus) erőforrás-frissítési tevékenység esetén a  **AzureMLUpdateResource** a tevékenység típusa. | Igen      |
| linkedServiceName             | Azure Machine Learning Studio (klasszikus) társított szolgáltatás, amely a updateResourceEndpoint tulajdonságot tartalmazza. | Igen      |
| trainedModelName              | A frissítendő webszolgáltatási kísérletben szereplő betanított modell modul neve | Igen      |
| trainedModelLinkedServiceName | Az Azure Storage társított szolgáltatás neve, amely a frissítési művelettel feltöltött ilearner fájlt tartja | Igen      |
| trainedModelFilePath          | A trainedModelLinkedService relatív fájljának elérési útja, amely a frissítési művelet által feltöltött ilearner-fájlt jelöli. | Igen      |

## <a name="end-to-end-workflow"></a>Teljes körű munkafolyamat

A végrehajtott teljes folyamata és a prediktív webszolgáltatások frissítése a következő lépésekkel jár:

- Hívja meg a **betanítási webszolgáltatást** a **Batch végrehajtási tevékenység** használatával. A betanítási webszolgáltatás meghívása ugyanaz, mint a prediktív [adatcsatornák létrehozása Azure Machine learning Studio (klasszikus) és Data Factory batch-végrehajtási tevékenység használatával](transform-data-using-machine-learning.md). A betanítási webszolgáltatás kimenete egy iLearner-fájl, amelyet a prediktív webszolgáltatás frissítésére használhat.
- A **prediktív webszolgáltatás** frissítési **erőforrás-végpontjának** meghívásával frissítse a webszolgáltatást az újonnan betanított modellel a webszolgáltatás **frissítése tevékenységgel** .

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Azure Machine Learning Studio (klasszikus) társított szolgáltatás

A fent említett végpontok közötti munkafolyamat működéséhez két Azure Machine Learning Studio (klasszikus) társított szolgáltatást kell létrehoznia:

1. Egy Azure Machine Learning Studio (klasszikus) társított szolgáltatás a betanítási webszolgáltatás számára, ezt a társított szolgáltatást a Batch-végrehajtási tevékenységek ugyanúgy használják, mint a [prediktív folyamatok létrehozása Azure Machine learning Studio (klasszikus) és Data Factory batch-végrehajtási tevékenység használatával](transform-data-using-machine-learning.md). Különbség a betanítási webszolgáltatás kimenete egy iLearner-fájl, amelyet a rendszer a prediktív webszolgáltatás frissítéséhez használ.
2. Egy Azure Machine Learning Studio (klasszikus) társított szolgáltatás a prediktív webszolgáltatás frissítési erőforrás-végpontján. Ez a társított szolgáltatás az erőforrás frissítése tevékenység használatával frissíti a prediktív webszolgáltatást a fenti lépésből visszaadott iLearner-fájllal.

A második Azure Machine Learning Studio (klasszikus) társított szolgáltatás esetében a konfiguráció eltérő, ha a Azure Machine Learning Studio (klasszikus) webszolgáltatás klasszikus webszolgáltatás vagy új webszolgáltatás. A különbségeket az alábbi szakaszokban külön tárgyaljuk.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>A webszolgáltatás új Azure Resource Manager webszolgáltatás

Ha a webszolgáltatás az új típusú webszolgáltatás, amely egy Azure Resource Manager-végpontot tesz elérhetővé, nem kell hozzáadnia a második **nem alapértelmezett** végpontot. A társított szolgáltatás **updateResourceEndpoint** formátuma:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Ha a webszolgáltatást a [Azure Machine learning Studio (klasszikus) webszolgáltatások portálján](https://services.azureml.net/)szeretné lekérdezni, megadhatja az URL-címeket a hely tulajdonosainak.

Az új típusú frissítési erőforrás-végponthoz egyszerű szolgáltatásnév-hitelesítés szükséges. Az egyszerű szolgáltatás hitelesítésének használatához regisztráljon egy alkalmazás-entitást Azure Active Directory (Azure AD-ben), és adja meg az előfizetés **közreműködői** vagy **tulajdonosi** szerepkörét, vagy azt az erőforráscsoportot, amelyben a webszolgáltatás a webszolgáltatáshoz tartozik. Lásd: [egyszerű szolgáltatásnév létrehozása és engedélyek kiosztása az Azure-erőforrások kezeléséhez](../active-directory/develop/howto-create-service-principal-portal.md). Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához használ:

- Alkalmazásazonosító
- Alkalmazás kulcsa
- Bérlőazonosító

Itt látható egy példa társított szolgáltatás definíciója:

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

A következő forgatókönyv további részleteket tartalmaz. Azure Machine Learning Studio (klasszikus) modellek átképzésére és frissítésére például egy Azure Data Factory folyamatból.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-studio-classic-model"></a>Minta: Azure Machine Learning Studio (klasszikus) modell átképzése és frissítése

Ez a szakasz egy minta folyamatot biztosít, amely a **Azure Machine learning Studio (klasszikus) kötegelt végrehajtási tevékenységet** használja a modell újratanításához. A folyamat a **Azure Machine learning Studio (klasszikus) frissítési erőforrás tevékenységgel** is frissíti a modellt a pontozási webszolgáltatás szolgáltatásban. A szakasz a példában szereplő társított szolgáltatások, adatkészletek és folyamatok JSON-kódrészleteit is tartalmazza.

### <a name="azure-blob-storage-linked-service"></a>Azure Blob Storage társított szolgáltatás:
Az Azure Storage a következő adatkészleteket tartalmazza:

* betanítási adatgyűjtés. A Azure Machine Learning Studio (klasszikus) betanítási webszolgáltatás bemeneti adata.
* iLearner-fájl. A Azure Machine Learning Studio-(klasszikus) betanítási webszolgáltatás kimenete. Ez a fájl a frissítési erőforrás tevékenységének bemenete is.

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

### <a name="linked-service-for-azure-machine-learning-studio-classic-training-endpoint"></a>Társított szolgáltatás a Azure Machine Learning Studio (klasszikus) betanítási végponthoz
A következő JSON-kódrészlet egy Azure Machine Learning Studio (klasszikus) társított szolgáltatást határoz meg, amely a betanítási webszolgáltatás alapértelmezett végpontján mutat.

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

### <a name="linked-service-for-azure-machine-learning-studio-classic-updatable-scoring-endpoint"></a>Társított szolgáltatás Azure Machine Learning Studio (klasszikus) frissíthető pontozási végponthoz:
A következő JSON-kódrészlet egy Azure Machine Learning Studio (klasszikus) társított szolgáltatást határoz meg, amely a pontozási webszolgáltatás frissíthető végpontján mutat.

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

### <a name="pipeline"></a>Folyamat
A folyamat két tevékenységgel rendelkezik: **AzureMLBatchExecution** és **AzureMLUpdateResource**. A kötegelt végrehajtási tevékenység bemenetként veszi át a betanítási adatokat, és kimenetként létrehoz egy iLearner-fájlt. Ezután az erőforrás frissítése tevékenység elvégzi ezt a iLearner-fájlt, és ezzel frissíti a prediktív webszolgáltatást.

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
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
