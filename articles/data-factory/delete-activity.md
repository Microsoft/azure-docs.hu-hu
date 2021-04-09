---
title: Törlési tevékenység az Azure Data Factoryben
description: Megtudhatja, hogyan törölheti a különböző tárakban található fájlokat a törlés tevékenységgel Azure Data Factoryban.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 3021d29f472dbbf43ae53981287b1f4676e8f932
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392714"
---
# <a name="delete-activity-in-azure-data-factory"></a>Törlési tevékenység az Azure Data Factoryben

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


A Azure Data Factory törlés tevékenységgel törölheti a fájlokat vagy mappákat a helyszíni tárolók vagy a Felhőbeli tárolók tárolójából. Ezzel a tevékenységgel törölheti vagy archiválhatja a fájlokat, ha már nincs rájuk szükség.

> [!WARNING]
> A törölt fájlok vagy mappák nem állíthatók vissza (kivéve, ha a tárolón engedélyezve van a helyreállított törlés). Körültekintően járjon el, amikor a Törlés művelettel töröl fájlokat vagy mappákat.

## <a name="best-practices"></a>Ajánlott eljárások

Íme néhány javaslat a törlési tevékenység használatához:

-   A fájlok biztonsági mentését, mielőtt törölné őket a törlés tevékenységgel, ha később vissza kell állítania őket.

-   Győződjön meg arról, hogy a Data Factory rendelkezik írási engedéllyel a mappák vagy fájlok törléséhez a tárolóból.

-   Győződjön meg arról, hogy nem törli az egyszerre megírt fájlokat. 

-   Ha a fájlokat vagy mappákat egy helyszíni rendszerből szeretné törölni, győződjön meg arról, hogy a saját üzemeltetésű integrációs modult használja a 3,14-nál nagyobb verziószámmal.

## <a name="supported-data-stores"></a>Támogatott adattárak

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [Fájlrendszer](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Google Cloud Storage](connector-google-cloud-storage.md)
-   [HDFS](connector-hdfs.md)

## <a name="syntax"></a>Syntax

```json
{
    "name": "DeleteActivity",
    "type": "Delete",
    "typeProperties": {
        "dataset": {
            "referenceName": "<dataset name>",
            "type": "DatasetReference"
        },
        "storeSettings": {
            "type": "<source type>",
            "recursive": true/false,
            "maxConcurrentConnections": <number>
        },
        "enableLogging": true/false,
        "logStorageSettings": {
            "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference"
            },
            "path": "<path to save log file>"
        }
    }
}
```

## <a name="type-properties"></a>Típus tulajdonságai

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| adatkészlet | Az adatkészlet hivatkozását adja meg a törlendő fájlok vagy mappák meghatározásához | Yes |
| rekurzív | Azt jelzi, hogy a rendszer törli-e a fájlokat rekurzív módon az almappákból, vagy csak a megadott mappából.  | Nem. A mező alapértelmezett értéke: `false`. |
| maxConcurrentConnections | Azon kapcsolatok száma, amelyek a Storage Store-hoz csatlakoznak egyidejűleg a mappák vagy fájlok törléséhez.   |  Nem. A mező alapértelmezett értéke: `1`. |
| enablelogging | Azt jelzi, hogy rögzíteni kell-e a törölt mappát vagy fájlneveket. Ha az értéke TRUE (igaz), meg kell adnia egy Storage-fiókot a naplófájl mentéséhez, hogy nyomon tudja követni a törlési tevékenység viselkedését a naplófájl olvasásával. | No |
| logStorageSettings | Csak akkor alkalmazható, ha a EnableLogging = True.<br/><br/>A tárolási tulajdonságok olyan csoportja, amely megadhatja, hogy hová szeretné menteni a DELETE tevékenység által törölt mappát vagy fájlneveket tartalmazó naplófájlt. | No |
| linkedServiceName | Csak akkor alkalmazható, ha a EnableLogging = True.<br/><br/>Az [Azure Storage](connector-azure-blob-storage.md#linked-service-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties)vagy [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) társított szolgáltatása, amely a DELETE tevékenység által törölt mappát vagy fájlneveket tartalmazó naplófájlt tárolja. Vegye figyelembe, hogy a fájlok törléséhez ugyanazt a Integration Runtime-típust kell konfigurálnia, mint amelyet a DELETE tevékenység használ. | No |
| path | Csak akkor alkalmazható, ha a EnableLogging = True.<br/><br/>A naplófájlnak a Storage-fiókba való mentésének elérési útja. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót. | No |

## <a name="monitoring"></a>Figyelés

A törlési tevékenység eredményét két helyen tekintheti meg és figyelheti: 
-   A törlési tevékenység kimenetéről.
-   A naplófájlból.

### <a name="sample-output-of-the-delete-activity"></a>A törlési tevékenység mintájának kimenete

```json
{ 
  "datasetName": "AmazonS3",
  "type": "AmazonS3Object",
  "prefix": "test",
  "bucketName": "adf",
  "recursive": true,
  "isWildcardUsed": false,
  "maxConcurrentConnections": 2,  
  "filesDeleted": 4,
  "logPath": "https://sample.blob.core.windows.net/mycontainer/5c698705-a6e2-40bf-911e-e0a927de3f07",
  "effectiveIntegrationRuntime": "MyAzureIR (West Central US)",
  "executionDuration": 650
}
```

### <a name="sample-log-file-of-the-delete-activity"></a>A törlési tevékenység minta naplófájlja

| Name | Kategória | Állapot | Hiba |
|:--- |:--- |:--- |:--- |
| test1/yyy.jsbekapcsolva | Fájl | Törölve |  |
| teszt2/hello789.txt | Fájl | Törölve |  |
| teszt2/test3/hello000.txt | Fájl | Törölve |  |
| teszt2/test3/zzz.jsbekapcsolva | Fájl | Törölve |  |

## <a name="examples-of-using-the-delete-activity"></a>Példák a DELETE tevékenység használatára

### <a name="delete-specific-folders-or-files"></a>Adott mappák vagy fájlok törlése

Az áruház a következő mappastruktúrát rendelkezik:

Legfelső szintű<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt

Most a DELETE tevékenységgel törli a mappát vagy a fájlokat az adatkészletből és a törlési tevékenységből származó eltérő tulajdonságértékek kombinációjával:

| folderPath | fileName | rekurzív | Kimenet |
|:--- |:--- |:--- |:--- |
| Gyökér/Folder_A_2 | NULL | Hamis | Legfelső szintű<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Gyökér/Folder_A_2 | NULL | Igaz | Legfelső szintű<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_A_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_1/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |
| Gyökér/Folder_A_2 | *. txt | Hamis | Legfelső szintű<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Gyökér/Folder_A_2 | *. txt | Igaz | Legfelső szintű<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>Az idő-particionált mappa vagy fájlok rendszeres törlése

Létrehozhat egy folyamatot, amely rendszeres időközönként törli az idő particionált mappáját vagy fájljait.  A mappa szerkezete például a következőhöz hasonló: `/mycontainer/2018/12/14/*.csv` .  Az ADF rendszerváltozó kihasználható az ütemezett triggerből annak azonosításához, hogy mely mappákat vagy fájlokat kell törölni minden egyes folyamat futtatásakor. 

#### <a name="sample-pipeline"></a>Mintavételezési folyamat

```json
{
    "name":"cleanup_time_partitioned_folder",
    "properties":{
        "activities":[
            {
                "name":"DeleteOneFolder",
                "type":"Delete",
                "dependsOn":[

                ],
                "policy":{
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[

                ],
                "typeProperties":{
                    "dataset":{
                        "referenceName":"PartitionedFolder",
                        "type":"DatasetReference",
                        "parameters":{
                            "TriggerTime":{
                                "value":"@formatDateTime(pipeline().parameters.TriggerTime, 'yyyy/MM/dd')",
                                "type":"Expression"
                            }
                        }
                    },
                    "logStorageSettings":{
                        "linkedServiceName":{
                            "referenceName":"BloblinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "path":"mycontainer/log"
                    },
                    "enableLogging":true,
                    "storeSettings":{
                        "type":"AzureBlobStorageReadSettings",
                        "recursive":true
                    }
                }
            }
        ],
        "parameters":{
            "TriggerTime":{
                "type":"string"
            }
        },
        "annotations":[

        ]
    }
}
```

#### <a name="sample-dataset"></a>Minta adatkészlet

```json
{
    "name":"PartitionedFolder",
    "properties":{
        "linkedServiceName":{
            "referenceName":"BloblinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "TriggerTime":{
                "type":"string"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "folderPath":{
                    "value":"@dataset().TriggerTime",
                    "type":"Expression"
                },
                "container":{
                    "value":"mycontainer",
                    "type":"Expression"
                }
            }
        }
    }
}
```

#### <a name="sample-trigger"></a>Mintatrigger

```json
{
    "name": "DailyTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "cleanup_time_partitioned_folder",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "TriggerTime": "@trigger().scheduledTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Day",
                "interval": 1,
                "startTime": "2018-12-13T00:00:00.000Z",
                "timeZone": "UTC",
                "schedule": {
                    "minutes": [
                        59
                    ],
                    "hours": [
                        23
                    ]
                }
            }
        }
    }
}
```

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>A 2018.1.1 előtti utolsó módosítás lejárt fájljainak törlése

Létrehozhat egy folyamatot a régi vagy a lejárt fájlok törléséhez a "LastModified" fájl attribútum-szűrő használatával a DataSet adatkészletben.  

#### <a name="sample-pipeline"></a>Mintavételezési folyamat

```json
{
    "name":"CleanupExpiredFiles",
    "properties":{
        "activities":[
            {
                "name":"DeleteFilebyLastModified",
                "type":"Delete",
                "dependsOn":[

                ],
                "policy":{
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[

                ],
                "typeProperties":{
                    "dataset":{
                        "referenceName":"BlobFilesLastModifiedBefore201811",
                        "type":"DatasetReference"
                    },
                    "logStorageSettings":{
                        "linkedServiceName":{
                            "referenceName":"BloblinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "path":"mycontainer/log"
                    },
                    "enableLogging":true,
                    "storeSettings":{
                        "type":"AzureBlobStorageReadSettings",
                        "recursive":true,
                        "modifiedDatetimeEnd":"2018-01-01T00:00:00.000Z"
                    }
                }
            }
        ],
        "annotations":[

        ]
    }
}
```

#### <a name="sample-dataset"></a>Minta adatkészlet

```json
{
    "name":"BlobFilesLastModifiedBefore201811",
    "properties":{
        "linkedServiceName":{
            "referenceName":"BloblinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"*",
                "folderPath":"mydirectory",
                "container":"mycontainer"
            }
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Fájlok áthelyezése a másolási tevékenység és a törlési tevékenység láncolásával

Egy fájl másolási tevékenységgel helyezhető át egy fájl másolásához, majd egy törlési tevékenységgel törölheti a folyamat egy fájlját.  Ha több fájlt szeretne áthelyezni, a GetMetadata tevékenység + szűrés tevékenység + foreach tevékenység + másolás tevékenység + törlés tevékenységet használhatja az alábbi példában látható módon:

> [!NOTE]
> Ha a teljes mappát a mappa elérési útját tartalmazó adatkészlet definiálásával szeretné áthelyezni, majd egy másolási tevékenységgel és a törlési tevékenységgel, hogy ugyanarra az adatkészletre hivatkozzon, amely egy mappát jelképez, nagyon Körültekintőnek kell lennie. Ennek az az oka, hogy meg kell győződnie arról, hogy a másolási művelet és a törlési művelet között nem lesznek új fájlok a mappába.  Ha a másolási tevékenység csak a másolási művelet befejezése után új fájlok érkeznek, de a törlési tevékenység nem lett kitöltve, lehetséges, hogy a törlési tevékenység törli ezt az új, a célhelyre még nem másolt fájlt, a teljes mappa törlésével. 

#### <a name="sample-pipeline"></a>Mintavételezési folyamat

```json
{
    "name":"MoveFiles",
    "properties":{
        "activities":[
            {
                "name":"GetFileList",
                "type":"GetMetadata",
                "dependsOn":[

                ],
                "policy":{
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[

                ],
                "typeProperties":{
                    "dataset":{
                        "referenceName":"OneSourceFolder",
                        "type":"DatasetReference",
                        "parameters":{
                            "Container":{
                                "value":"@pipeline().parameters.SourceStore_Location",
                                "type":"Expression"
                            },
                            "Directory":{
                                "value":"@pipeline().parameters.SourceStore_Directory",
                                "type":"Expression"
                            }
                        }
                    },
                    "fieldList":[
                        "childItems"
                    ],
                    "storeSettings":{
                        "type":"AzureBlobStorageReadSettings",
                        "recursive":true
                    },
                    "formatSettings":{
                        "type":"BinaryReadSettings"
                    }
                }
            },
            {
                "name":"FilterFiles",
                "type":"Filter",
                "dependsOn":[
                    {
                        "activity":"GetFileList",
                        "dependencyConditions":[
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties":[

                ],
                "typeProperties":{
                    "items":{
                        "value":"@activity('GetFileList').output.childItems",
                        "type":"Expression"
                    },
                    "condition":{
                        "value":"@equals(item().type, 'File')",
                        "type":"Expression"
                    }
                }
            },
            {
                "name":"ForEachFile",
                "type":"ForEach",
                "dependsOn":[
                    {
                        "activity":"FilterFiles",
                        "dependencyConditions":[
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties":[

                ],
                "typeProperties":{
                    "items":{
                        "value":"@activity('FilterFiles').output.value",
                        "type":"Expression"
                    },
                    "batchCount":20,
                    "activities":[
                        {
                            "name":"CopyAFile",
                            "type":"Copy",
                            "dependsOn":[

                            ],
                            "policy":{
                                "timeout":"7.00:00:00",
                                "retry":0,
                                "retryIntervalInSeconds":30,
                                "secureOutput":false,
                                "secureInput":false
                            },
                            "userProperties":[

                            ],
                            "typeProperties":{
                                "source":{
                                    "type":"BinarySource",
                                    "storeSettings":{
                                        "type":"AzureBlobStorageReadSettings",
                                        "recursive":false,
                                        "deleteFilesAfterCompletion":false
                                    },
                                    "formatSettings":{
                                        "type":"BinaryReadSettings"
                                    },
                                    "recursive":false
                                },
                                "sink":{
                                    "type":"BinarySink",
                                    "storeSettings":{
                                        "type":"AzureBlobStorageWriteSettings"
                                    }
                                },
                                "enableStaging":false,
                                "dataIntegrationUnits":0
                            },
                            "inputs":[
                                {
                                    "referenceName":"OneSourceFile",
                                    "type":"DatasetReference",
                                    "parameters":{
                                        "Container":{
                                            "value":"@pipeline().parameters.SourceStore_Location",
                                            "type":"Expression"
                                        },
                                        "Directory":{
                                            "value":"@pipeline().parameters.SourceStore_Directory",
                                            "type":"Expression"
                                        },
                                        "filename":{
                                            "value":"@item().name",
                                            "type":"Expression"
                                        }
                                    }
                                }
                            ],
                            "outputs":[
                                {
                                    "referenceName":"OneDestinationFile",
                                    "type":"DatasetReference",
                                    "parameters":{
                                        "Container":{
                                            "value":"@pipeline().parameters.DestinationStore_Location",
                                            "type":"Expression"
                                        },
                                        "Directory":{
                                            "value":"@pipeline().parameters.DestinationStore_Directory",
                                            "type":"Expression"
                                        },
                                        "filename":{
                                            "value":"@item().name",
                                            "type":"Expression"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "name":"DeleteAFile",
                            "type":"Delete",
                            "dependsOn":[
                                {
                                    "activity":"CopyAFile",
                                    "dependencyConditions":[
                                        "Succeeded"
                                    ]
                                }
                            ],
                            "policy":{
                                "timeout":"7.00:00:00",
                                "retry":0,
                                "retryIntervalInSeconds":30,
                                "secureOutput":false,
                                "secureInput":false
                            },
                            "userProperties":[

                            ],
                            "typeProperties":{
                                "dataset":{
                                    "referenceName":"OneSourceFile",
                                    "type":"DatasetReference",
                                    "parameters":{
                                        "Container":{
                                            "value":"@pipeline().parameters.SourceStore_Location",
                                            "type":"Expression"
                                        },
                                        "Directory":{
                                            "value":"@pipeline().parameters.SourceStore_Directory",
                                            "type":"Expression"
                                        },
                                        "filename":{
                                            "value":"@item().name",
                                            "type":"Expression"
                                        }
                                    }
                                },
                                "logStorageSettings":{
                                    "linkedServiceName":{
                                        "referenceName":"BloblinkedService",
                                        "type":"LinkedServiceReference"
                                    },
                                    "path":"container/log"
                                },
                                "enableLogging":true,
                                "storeSettings":{
                                    "type":"AzureBlobStorageReadSettings",
                                    "recursive":true
                                }
                            }
                        }
                    ]
                }
            }
        ],
        "parameters":{
            "SourceStore_Location":{
                "type":"String"
            },
            "SourceStore_Directory":{
                "type":"String"
            },
            "DestinationStore_Location":{
                "type":"String"
            },
            "DestinationStore_Directory":{
                "type":"String"
            }
        },
        "annotations":[

        ]
    }
}
```

#### <a name="sample-datasets"></a>Mintaadatkészletek

A GetMetadata tevékenység által a fájllista enumerálásához használt adatkészlet.

```json
{
    "name":"OneSourceFolder",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "Container":{
                "type":"String"
            },
            "Directory":{
                "type":"String"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "folderPath":{
                    "value":"@{dataset().Directory}",
                    "type":"Expression"
                },
                "container":{
                    "value":"@{dataset().Container}",
                    "type":"Expression"
                }
            }
        }
    }
}
```

A másolási tevékenység és a törlési tevékenység által használt adatforrás adatkészlete.

```json
{
    "name":"OneSourceFile",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "Container":{
                "type":"String"
            },
            "Directory":{
                "type":"String"
            },
            "filename":{
                "type":"string"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":{
                    "value":"@dataset().filename",
                    "type":"Expression"
                },
                "folderPath":{
                    "value":"@{dataset().Directory}",
                    "type":"Expression"
                },
                "container":{
                    "value":"@{dataset().Container}",
                    "type":"Expression"
                }
            }
        }
    }
}
```

Adathalmaz a másolási tevékenység által használt adat céljához.

```json
{
    "name":"OneDestinationFile",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "Container":{
                "type":"String"
            },
            "Directory":{
                "type":"String"
            },
            "filename":{
                "type":"string"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":{
                    "value":"@dataset().filename",
                    "type":"Expression"
                },
                "folderPath":{
                    "value":"@{dataset().Directory}",
                    "type":"Expression"
                },
                "container":{
                    "value":"@{dataset().Container}",
                    "type":"Expression"
                }
            }
        }
    }
}
```

Azt is megteheti, hogy a sablon segítségével áthelyezi [a fájlokat innen.](solution-template-move-files.md)

## <a name="known-limitation"></a>Ismert korlátozás

-   A törlési tevékenység nem támogatja a helyettesítő karakterrel leírt mappák listájának törlését.

-   Ha a DELETE tevékenységben a file Attribute szűrőt használja: modifiedDatetimeStart és modifiedDatetimeEnd a törlendő fájlok kiválasztásához, akkor ügyeljen arra, hogy a törlési tevékenységben a "wildcardFileName": "*" beállítást is állítsa be.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Data Factory lévő fájlok áthelyezéséről.

-   [Adatok másolása eszköz az Azure Data Factoryben](copy-data-tool.md)
