---
title: ForEach-tevékenység Azure Data Factory
description: Az minden tevékenység esetében egy ismétlődő vezérlési folyamat van definiálva a folyamatban. Ez a művelet egy gyűjtemény átugrására és meghatározott tevékenységek végrehajtására szolgál.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: aeabd74117f99c7cac9bde0eda02b9627caf0804
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177783"
---
# <a name="foreach-activity-in-azure-data-factory"></a>ForEach-tevékenység Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A ForEach tevékenység a folyamat ismétlődő vezérlési folyamatát határozza meg. Ez a tevékenység egy gyűjtemény megismétlésére, valamint egy megadott ciklustevékenység végrehajtására szolgál. E tevékenység ciklusos megvalósítása hasonló a Foreach ciklusos szerkezetéhez a programozási nyelvek esetében.

## <a name="syntax"></a>Syntax
A tulajdonságokat a cikk későbbi részében ismertetjük. Az Items tulajdonság a gyűjtemény és a gyűjtemény minden eleme a `@item()` következő szintaxissal látható módon hivatkozik rá:  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
name | A for-each tevékenység neve. | Sztring | Yes
típus | **Foreach** értékre kell állítani | Sztring | Yes
isSequential | Meghatározza, hogy a hurkot egymás után vagy párhuzamosan kell-e végrehajtani.  Egyidejűleg legfeljebb 20 hurok-iteráció hajtható végre. Ha például egy ForEach-tevékenység egy másolási tevékenységhez képest 10 különböző forrás-és fogadó adatkészlettel rendelkezik, és a **isSequential** értéke hamis, akkor az összes másolat egyszerre lesz végrehajtva. Az alapértelmezett érték a false. <br/><br/> Ha a "isSequential" értéke false (hamis), akkor ellenőrizze, hogy van-e megfelelő konfiguráció több végrehajtható fájl futtatásához. Ellenkező esetben ezt a tulajdonságot körültekintően kell használni az írási ütközések elkerülése érdekében. További információ: [párhuzamos végrehajtás](#parallel-execution) szakasz. | Logikai | Nem. Az alapértelmezett érték a false.
batchCount | A párhuzamos végrehajtások számának szabályozásához használandó kötegek száma (ha a isSequential hamis értékre van állítva). Ez a felső egyidejűségi korlát, de a for-each tevékenység nem mindig lesz végrehajtva ennél a számnál | Egész szám (legfeljebb 50) | Nem. Az alapértelmezett érték 20.
Elemek | Egy kifejezés, amely egy JSON-tömböt ad vissza, amelyet a rendszer megismétel. | Kifejezés (amely egy JSON-tömböt ad vissza) | Yes
Tevékenységek | A végrehajtandó tevékenységek. | Tevékenységek listája | Yes

## <a name="parallel-execution"></a>Párhuzamos végrehajtás
Ha a **isSequential** hamis értékre van állítva, a tevékenység párhuzamosan, legfeljebb 20 egyidejű ismétléssel közelíthető meg. Ezt a beállítást körültekintően kell használni. Ha az egyidejű ismétlések ugyanarra a mappára, de különböző fájlokra is érvényesek, ez a megközelítés rendben van. Ha az egyidejű ismétlések egyidejű, ugyanazon a fájlon vannak írva, ez a megközelítés valószínűleg hibát okoz. 

## <a name="iteration-expression-language"></a>Iterációs kifejezés nyelve
A ForEach tevékenységben adjon meg egy olyan tömböt, amelyet meg kell ismételni a tulajdonság **elemeinél**. " A használatával `@item()` megismételheti a foreach tevékenység egyetlen enumerálását. Ha például az **elemek** tömb: [1, 2, 3], `@item()` az első iterációban az 1 értéket adja vissza, a második iterációban 2, a harmadik iterációban pedig 3.

## <a name="iterating-over-a-single-activity"></a>Iteráció egyetlen tevékenységen keresztül
**Forgatókönyv:** Másolja az Azure blobban található ugyanabból a forrásfájl-fájlból az Azure blobban található több célfájlba.

### <a name="pipeline-definition"></a>Folyamat definíciója

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>BLOB-adatkészlet definíciója

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>Paraméterek értékének futtatása

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Iteráció több tevékenységhez
Több tevékenység is megismételhető (például másolási és webes tevékenységek) egy ForEach-tevékenységben. Ebben a forgatókönyvben azt javasoljuk, hogy több tevékenységet is elkülönítse egy külön folyamatba. Ezt követően használhatja a folyamat [ExecutePipeline tevékenységét](control-flow-execute-pipeline-activity.md) a foreach tevékenységgel, hogy meghívja a különálló folyamatot több tevékenységgel. 


### <a name="syntax"></a>Syntax

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>Példa
**Forgatókönyv:** Ismételje meg a InnerPipeline egy ForEach tevékenységen belül a folyamat végrehajtása tevékenységgel. A belső folyamat a séma-definíciók paraméterrel rendelkezik.

#### <a name="master-pipeline-definition"></a>Fő folyamat definíciója

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>Belső folyamat definíciója

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>Forrás adatkészlet definíciója

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>Fogadó adatkészlet definíciója

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>Fő folyamat paraméterei
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>Kimenetek összesítése

A __foreach__ tevékenység kimenetének összesítéséhez használjon _változókat_ , és adjon _hozzá változó_ tevékenységet.

Először deklaráljon egy `array` _változót_ a folyamatban. Ezután hívja meg a _változó hozzáfűzése_ tevékenységet az egyes __foreach__ -ciklusokon belül. Ezt követően lekérheti az összesítést a tömbből.

## <a name="limitations-and-workarounds"></a>Korlátozások és megkerülő megoldások

Íme néhány korlátozás a ForEach tevékenységről és a javasolt megkerülő megoldásokról.

| Korlátozás | Áthidaló megoldás |
|---|---|
| Egy ForEach hurok nem ágyazható be egy másik ForEach hurokba (vagy egy ciklusig). | Tervezzen olyan kétszintű folyamatot, amelyben a külső ForEach hurok külső folyamata egy belső folyamaton keresztül ismétli a beágyazott hurkot. |
| A ForEach tevékenység legfeljebb `batchCount` 50 párhuzamos feldolgozásra és legfeljebb 100 000 elemet tartalmaz. | Tervezzen olyan kétszintű folyamatot, amelyben a külső folyamat ForEach tevékenysége egy belső folyamaton keresztül megismétli a folyamatokat. |
| A SetVariable nem használható olyan ForEach-tevékenységen belül, amely párhuzamosan fut, mivel a változók globálisak a teljes folyamathoz, nem tartoznak a ForEach vagy más tevékenységekhez. | Érdemes lehet szekvenciális ForEach használni, vagy a ForEach belüli végrehajtási folyamatot (változó/paraméterrel kezelt).|
| | |

## <a name="next-steps"></a>Következő lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
