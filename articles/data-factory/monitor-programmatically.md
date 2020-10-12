---
title: Azure-beli adatgyár programozott figyelése
description: Megtudhatja, hogyan figyelheti a folyamatokat egy adatgyárban különböző szoftverfejlesztői készletek (SDK-k) használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.custom: devx-track-python
ms.openlocfilehash: 8c13e0e3f1cb8809a126b11409becd2211864c32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87876546"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Azure-beli adatgyár programozott figyelése

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk bemutatja, hogyan figyelheti a folyamatokat egy adatgyárban különböző szoftverfejlesztői készletek (SDK-k) használatával. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Adattartomány

Data Factory csak a 45 napig tárolja a folyamat futási értékeit. Amikor programozott módon kérdezi le az Data Factory folyamat futtatásával kapcsolatos adatokra vonatkozó információkat – például a PowerShell `Get-AzDataFactoryV2PipelineRun` -paranccsal –, a nem kötelező és a paraméterek esetében nincsenek maximális dátumok `LastUpdatedAfter` `LastUpdatedBefore` . Ha azonban az elmúlt év adatait kérdezi le, például a lekérdezés nem ad vissza hibát, de csak az utolsó 45 nap adatfeldolgozási folyamatát adja vissza.

Ha 45 napnál hosszabb ideig kívánja megőrizni a folyamat adatait, állítsa be a saját diagnosztikai naplózását [Azure monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
A folyamatok .NET SDK-val történő létrehozásával és figyelésével kapcsolatban lásd: az [adatfeldolgozó és a folyamat létrehozása a .NET használatával](quickstart-create-data-factory-dot-net.md).

1. Adja hozzá a következő kódot a folyamat futási állapotának folyamatos vizsgálatához, amíg az adatok másolása be nem fejeződik.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Adja hozzá a következő kódot, amely lekéri a másolási tevékenység futtatási részleteit, például az olvasott/írt adatok méretét.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

A .NET SDK-val kapcsolatos teljes dokumentációért tekintse meg [Data Factory .net SDK-referenciát](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
A folyamat Python SDK-val történő létrehozásával és figyelésével kapcsolatban lásd: [adatelőállító és-folyamat létrehozása a Python használatával](quickstart-create-data-factory-python.md).

A folyamat futtatásának figyeléséhez adja hozzá a következő kódot:

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

A Python SDK-val kapcsolatos teljes dokumentációért lásd [Data Factory PYTHON SDK-referenciát](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>REST API
A folyamatok REST API használatával történő létrehozásáról és figyeléséről a következő témakörben talál részletes útmutatót: [adatfeldolgozó és-folyamat létrehozása REST API használatával](quickstart-create-data-factory-rest-api.md).
 
1. A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi szkriptet.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. A másolási tevékenység futtatási részleteinek (például az írt vagy olvasott adatok méretének) lekéréséhez futtassa az alábbi szkriptet.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

A REST API teljes dokumentációját lásd: [Data Factory REST API-referenciák](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
A folyamat PowerShell használatával történő létrehozásával és figyelésével kapcsolatos teljes útmutatóért lásd: [adatelőállító és-folyamat létrehozása a PowerShell használatával](quickstart-create-data-factory-powershell.md).

1. A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi szkriptet.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. A másolási tevékenység futtatási részleteinek (például az írt vagy olvasott adatok méretének) lekéréséhez futtassa az alábbi szkriptet.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

A PowerShell-parancsmagokkal kapcsolatos teljes dokumentációért lásd: [Data Factory PowerShell-parancsmagok leírása](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Következő lépések
Lásd: [folyamatok figyelése Azure monitor cikk használatával](monitor-using-azure-monitor.md) , amelyből megtudhatja, hogyan használhatja a Azure Monitor a Data Factory folyamatok figyelésére. 

