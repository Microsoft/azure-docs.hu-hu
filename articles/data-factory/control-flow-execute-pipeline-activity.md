---
title: Folyamat végrehajtása tevékenység Azure Data Factory
description: Ebből a témakörből megtudhatja, hogy a folyamat végrehajtása tevékenység használatával hogyan hívhat meg egy Data Factory folyamatot egy másik Data Factory folyamatból.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: a24bd6988a9bc8f15723ebf931f2751a09c42e26
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783744"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Folyamat végrehajtása tevékenység Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Folyamat végrehajtása tevékenység lehetővé teszi egy Data Factory-folyamat számára egy másik folyamat meghívását.



## <a name="syntax"></a>Syntax

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
name | A folyamat végrehajtása tevékenység neve. | Sztring | Yes
típus | A következő értékre kell állítani: **ExecutePipeline**. | Sztring | Yes
folyamat | A folyamat által meghívott függő folyamathoz tartozó folyamatra mutató hivatkozás. A folyamat hivatkozási objektumának két tulajdonsága van: **hivatkozásnév** és **Type**. A hivatkozásnév tulajdonság a hivatkozási folyamat nevét adja meg. A Type tulajdonságot PipelineReference értékre kell beállítani. | PipelineReference | Yes
parameters | A meghívott folyamatnak átadandó paraméterek | Egy JSON-objektum, amely a paraméterek nevét az argumentum értékére képezi. | No
waitOnCompletion | Meghatározza, hogy a tevékenység-végrehajtás megvárja-e a függő folyamat végrehajtásának befejeződését. Az alapértelmezett érték a false (hamis). | Logikai | No

## <a name="sample"></a>Sample
Ennek a forgatókönyvnek két folyamata van:

- **Főfolyamat** – ez a folyamat egyetlen végrehajtási folyamattal rendelkezik, amely meghívja a meghívott folyamatot. A fő folyamat két paramétert vesz igénybe: `masterSourceBlobContainer` , `masterSinkBlobContainer` .
- **Meghívott folyamat** – ez a folyamat egy másolási tevékenységgel rendelkezik, amely egy Azure Blob-forrásból származó adatok másolását végzi az Azure Blob fogadóba. A meghívott folyamat két paramétert vesz igénybe: `sourceBlobContainer` , `sinkBlobContainer` .

### <a name="master-pipeline-definition"></a>Fő folyamat definíciója

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Meghívott folyamat definíciója

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**Társított szolgáltatások**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=*****;AccountKey=*****"
    }
  }
}
```

**Forrásadatkészlet**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**Fogadó-adatkészlet**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>A folyamat futtatása

A fő folyamat ebben a példában való futtatásához a masterSourceBlobContainer és a masterSinkBlobContainer paraméterek a következő értékeket adja át: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

A fő folyamat továbbítja ezeket az értékeket a meghívott folyamatnak az alábbi példában látható módon: 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Következő lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat: 

- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
