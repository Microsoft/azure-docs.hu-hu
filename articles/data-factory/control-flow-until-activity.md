---
title: A tevékenység Azure Data Factory
description: A amíg tevékenység végrehajtja a tevékenységek egy csoportját, amíg a tevékenységhez rendelt feltétel igaz vagy időtúllépést nem ad vissza.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 6431321d788e7311e076d302173ffffb96e07876
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385370"
---
# <a name="until-activity-in-azure-data-factory"></a>A tevékenység Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az Until tevékenység ugyanazokat a funkciókat biztosítja, mint a programnyelvek Do-Until ciklusos szerkezete. Egy tevékenységkészletet futtat le ciklusosan addig, amíg a tevékenységhez rendelt feltétel igaz értéket nem ad vissza. Megadhat egy időtúllépési értéket az Until tevékenységhez a Data Factoryban. 

## <a name="syntax"></a>Syntax

```json
{
    "type": "Until",
    "typeProperties": {
        "expression":  {
            "value":  "<expression that evaluates to true or false>", 
            "type": "Expression"
        },
        "timeout": "<time out for the loop. for example: 00:01:00 (1 minute)>",
        "activities": [
            {
                "<Activity 1 definition>"
            },
            {
                "<Activity 2 definition>"
            },
            {
                "<Activity N definition>"
            }
        ]
    },
    "name": "MyUntilActivity"
}

```

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
name | A tevékenység neve `Until` . | Sztring | Yes
típus | **Csak** a értékre kell beállítani. | Sztring | Yes
kifejezés | Igaz vagy hamis értéket megadó kifejezés | Kifejezés.  | Yes
timeout | A do-ig hurkos időtúllépés a megadott idő után. | Sztring. `d.hh:mm:ss` (vagy) `hh:mm:ss` . Az alapértelmezett érték 7 nap. Maximális érték: 90 nap. | No
Tevékenységek | Azoknak a tevékenységeknek a készlete, amelyeket a kifejezés kiértékelése után hajt végre `true` . | Tevékenységek tömbje. |  Yes

## <a name="example-1"></a>1\. példa

> [!NOTE]
> Ez a szakasz JSON-definíciókat és PowerShell-parancsokat tartalmaz a folyamat futtatásához. A Data Factory-adatfolyamatok Azure PowerShell és JSON-definíciók használatával történő létrehozásával kapcsolatos részletes útmutatásért lásd [: oktatóanyag: az adatfeldolgozó létrehozása a Azure PowerShell használatával](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-until-activity"></a>Folyamat a tevékenységig
Ebben a példában a folyamat két tevékenységgel rendelkezik: **eddig** és **várni**. A várakozási tevékenység a megadott ideig várakozik a webes tevékenység ciklusban való futtatása előtt. A Data Factory kifejezésekkel és függvényekkel kapcsolatos információkért lásd a [kifejezés nyelvét és funkcióit](control-flow-expression-language-functions.md)ismertető témakört. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="example-2"></a>2\. példa 
Az ebben a példában szereplő folyamat adatokat másol egy bemeneti mappából egy hurok kimeneti mappájába. A hurok akkor leáll, ha az Ismétlési paraméter értéke false (hamis) értékre van állítva, vagy egy perc elteltével időtúllépés történik.   

### <a name="pipeline-with-until-activity-adfv2quickstartpipelinejson"></a>Folyamat a tevékenységig (Adfv2QuickStartPipeline.jsbekapcsolva)

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression":  {
                        "value":  "@equals('false', pipeline().parameters.repeat)", 
                        "type": "Expression"
                    },
                    "timeout": "00:01:00",
                    "activities": [
                        {
                            "name": "CopyFromBlobToBlob",
                            "type": "Copy",
                            "inputs": [
                                {
                                    "referenceName": "BlobDataset",
                                    "parameters": {
                                        "path": "@pipeline().parameters.inputPath"
                                    },
                                    "type": "DatasetReference"
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "BlobDataset",
                                    "parameters": {
                                        "path": "@pipeline().parameters.outputPath"
                                    },
                                    "type": "DatasetReference"
                                }
                            ],
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource"
                                },
                                "sink": {
                                    "type": "BlobSink"
                                }
                            },
                            "policy": {
                                "retry": 1,
                                "timeout": "00:10:00",
                                "retryIntervalInSeconds": 60
                            }
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            },
            "repeat": {
                "type": "String"
            }                        
        }        
    }
}

```


### <a name="azure-storage-linked-service-azurestoragelinkedservicejson"></a>Azure Storage társított szolgáltatás (AzureStorageLinkedService.jsbekapcsolva)

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<Azure Storage account name>;AccountKey=<Azure Storage account key>"
        }
    }
}
```

### <a name="parameterized-azure-blob-dataset-blobdatasetjson"></a>Paraméteres Azure Blob-adatkészlet (BlobDataset.jsbekapcsolva)
A folyamat beállítja a **folderPath** a folyamat **outputPath1** vagy **outputPath2** paraméterének értékére. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="pipeline-parameter-json-pipelineparametersjson"></a>Adatcsatorna-paraméter JSON-je (PipelineParameters.jsbekapcsolva)

```json
{
    "inputPath": "adftutorial/input",
    "outputPath": "adftutorial/outputUntil",
    "repeat": "true"
}
```

### <a name="powershell-commands"></a>PowerShell-parancsok

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ezek a parancsok feltételezik, hogy mentette a JSON-fájlokat a következő mappába: C:\ADF. 

```powershell
Connect-AzAccount
Select-AzSubscription "<Your subscription name>"

$resourceGroupName = "<Resource Group Name>"
$dataFactoryName = "<Data Factory Name. Must be globally unique>";
Remove-AzDataFactoryV2 $dataFactoryName -ResourceGroupName $resourceGroupName -force


Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile "C:\ADF\AzureStorageLinkedService.json"
Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile "C:\ADF\BlobDataset.json"
Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile "C:\ADF\Adfv2QuickStartPipeline.json"
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile C:\ADF\PipelineParameters.json

while ($True) {
    $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

    if ($run) {
        if ($run.Status -ne 'InProgress') {
            Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
            $run
            break
        }
        Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        Write-Host "Activity run details:" -foregroundcolor "Yellow"
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
        $result

        Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
        $result.Output -join "`r`n"
    }

    Start-Sleep -Seconds 15
}
```

## <a name="next-steps"></a>Következő lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
