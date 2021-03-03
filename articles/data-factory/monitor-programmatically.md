---
title: Azure-beli adatgyár programozott figyelése
description: Megtudhatja, hogyan figyelheti a folyamatokat egy adatgyárban különböző szoftverfejlesztői készletek (SDK-k) használatával.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/16/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 6c913c7c623c77baea0c575d06d2c44709af43fa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740439"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Azure-beli adatgyár programozott figyelése

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk bemutatja, hogyan figyelheti a folyamatokat egy adatgyárban különböző szoftverfejlesztői készletek (SDK-k) használatával. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Adattartomány

Data Factory csak a 45 napig tárolja a folyamat futási értékeit. Amikor programozott módon kérdezi le az Data Factory folyamat futtatásával kapcsolatos adatokra vonatkozó információkat – például a PowerShell `Get-AzDataFactoryV2PipelineRun` -paranccsal –, a nem kötelező és a paraméterek esetében nincsenek maximális dátumok `LastUpdatedAfter` `LastUpdatedBefore` . Ha azonban az elmúlt év adatait kérdezi le, például nem kap hibaüzenetet, de csak az utolsó 45 nap adatait fogja futtatni.

Ha több mint 45 napig szeretné megőrizni a folyamat adatait, állítsa be a saját diagnosztikai naplózását [Azure monitor](monitor-using-azure-monitor.md).

## <a name="pipeline-run-information"></a>Folyamat futtatási adatai

A folyamat futási tulajdonságainak megtekintéséhez tekintse meg a [PIPELINERUN API-referenciát](/rest/api/datafactory/pipelineruns/get#pipelinerun). Egy folyamat futása eltérő állapotú az életciklusa során, a futtatási állapot lehetséges értékei a következők:

* Várólistán
* Folyamatban
* Sikeres
* Sikertelen
* Megszakítás
* Megszakítva

## <a name="net"></a>.NET
A folyamatok .NET SDK-val történő létrehozásának és figyelésének teljes körű ismertetését lásd: [adatfeldolgozó és-folyamat létrehozása a .NET használatával](quickstart-create-data-factory-dot-net.md).

1. Adja hozzá a következő kódot a folyamat futási állapotának folyamatos vizsgálatához, amíg az adatok másolása be nem fejeződik.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
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

A .NET SDK-val kapcsolatos teljes dokumentációért tekintse meg [Data Factory .net SDK-referenciát](/dotnet/api/microsoft.azure.management.datafactory).

## <a name="python"></a>Python
A folyamat Python SDK-val történő létrehozásának és figyelésének teljes körű ismertetését a következő témakörben tekintheti meg: az [adatelőállító és-folyamat létrehozása a Python használatával](quickstart-create-data-factory-python.md).

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

A Python SDK-val kapcsolatos teljes dokumentációért lásd [Data Factory PYTHON SDK-referenciát](/python/api/overview/azure/datafactory).

## <a name="rest-api"></a>REST API
A folyamatok REST API használatával történő létrehozásának és figyelésének teljes körű ismertetését a következő témakörben tekintheti meg: az [adatfeldolgozó és-folyamat létrehozása REST API használatával](quickstart-create-data-factory-rest-api.md).
 
1. A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi szkriptet.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") ) {
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
A folyamat PowerShell használatával történő létrehozásának és figyelésének teljes körű ismertetését a következő témakörben tekintheti meg: az [adatelőállító és-folyamat létrehozása a PowerShell használatával](quickstart-create-data-factory-powershell.md).

1. A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi szkriptet.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ( ($run.Status -ne "InProgress") -and ($run.Status -ne "Queued") ) {
                Write-Output ("Pipeline run finished. The status is: " +  $run.Status)
                $run
                break
            }
            Write-Output ("Pipeline is running...status: " + $run.Status)
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