---
title: Nagy számú feladat elküldése egy batch-feladatba
description: Megtudhatja, hogyan lehet hatékonyan beküldeni egy Azure Batch feladat nagyon nagy számú feladatát.
ms.topic: how-to
ms.date: 12/30/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 08cf92507a4556afbf56c9cb7e2c9c1b3a6c9479
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831516"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Nagy számú feladat elküldése egy batch-feladatba

Nagyméretű Azure Batch számítási feladatok futtatásakor előfordulhat, hogy több tízezer, több százezer vagy akár több feladatot szeretne elküldeni egyetlen feladatnak.

Ebből a cikkből megtudhatja, hogyan küldhet el nagy mennyiségű feladatot lényegesen megnövelt átviteli sebességgel egyetlen batch-feladathoz. A feladatok elküldése után megadják a Batch-várólistát a feladathoz megadott készlet feldolgozásához.

## <a name="use-task-collections"></a>Feladatok gyűjteményének használata

Nagy mennyiségű feladat hozzáadásakor használja a Batch API-k által biztosított megfelelő metódusokat vagy túlterheléseket a feladatok *gyűjteményként* való hozzáadásához, nem pedig egyszerre. Általában úgy hozhat létre egy feladatot, hogy meghatározza a feladatokat, és megismétli a feladathoz tartozó bemeneti fájlok vagy paraméterek körét.

Az egyetlen hívásban felvehető feladatok gyűjteményének maximális mérete a használt batch API-val függ.

### <a name="apis-allowing-collections-of-up-to-100-tasks"></a>Legfeljebb 100 feladat gyűjteményeit lehetővé tevő API-k

Ezek a Batch API-k az 100-es feladatokra korlátozzák a gyűjteményt. A korlát a feladatok méretétől függően kisebb lehet (például ha a feladatok nagy számú erőforrás-fájllal vagy környezeti változóval rendelkeznek).

- [REST API](/rest/api/batchservice/task/addcollection)
- [Python API](/python/api/azure-batch/azure.batch.operations.TaskOperations)
- [Node.js API](/javascript/api/@azure/batch/task)

Ezen API-k használatakor meg kell adni a logikát, hogy elossza a feladatok számát, hogy megfeleljen a gyűjtési korlátnak, valamint a hibák kezeléséhez és az újrapróbálkozásokhoz a feladat-hozzáadási hibák esetén. Ha egy feladatsor túl nagy a hozzáadáshoz, a kérelem hibát generál, és kevesebb feladattal újra próbálkozni fog.

### <a name="apis-allowing-collections-of-larger-numbers-of-tasks"></a>Az API-k nagyobb számú feladat gyűjteményeit teszik lehetővé

A többi batch API sokkal nagyobb méretű feladatokat támogat, amelyeket csak a RAM rendelkezésre állása korlátoz a beküldő ügyfélen. Ezek az API-k transzparens módon kezelik a feladatok gyűjteményét az alsó szintű API-k számára, és újrapróbálkoznak a feladat-hozzáadási hibákkal.

- [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)
- [Java API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync)
- [CLI-bővítmény Azure batch](batch-cli-templates.md) batch CLI-sablonokkal
- [Python SDK-bővítmény](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>A feladat beküldési teljesítményének növelése

Hosszabb időt is igénybe vehet, ha a feladatok nagy gyűjteményét szeretné felvenni egy feladatba. Például az 20 000-es feladatok .NET API-n keresztüli hozzáadása akár egy percet is igénybe vehet. A Batch API-tól és a számítási feladattól függően javíthatja a feladatok átviteli sebességét, ha módosítja az alábbiak közül egyet vagy többet.

### <a name="task-size"></a>Feladat mérete

A nagyméretű feladatok hozzáadása hosszabb időt vesz igénybe, mint a kisebbek hozzáadása. A gyűjtemények egyes feladatainak méretének csökkentéséhez egyszerűsítheti a feladat parancssorát, csökkentheti a környezeti változók számát, vagy hatékonyabban kezelheti a feladatok végrehajtásának követelményeit.

Ha például nagy mennyiségű erőforrást használ, telepítse a feladat függőségeit a készlet [indítási feladatának](jobs-and-tasks.md#start-task) használatával, vagy használjon [alkalmazáscsomag](batch-application-packages.md) vagy [Docker-tárolót](batch-docker-container-workloads.md).

### <a name="number-of-parallel-operations"></a>Párhuzamos műveletek száma

A Batch API-tól függően növelheti az átviteli sebességet úgy, hogy növeli az egyidejű műveletek maximális számát a Batch-ügyfél számára. Ezt a beállítást a .NET API [BatchClientParallelOptions. maxanalyticsunits](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) tulajdonságával vagy a `threads` Batch Python SDK-bővítményben található metódusok (például [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations) ) használatával konfigurálhatja. (Ez a tulajdonság nem érhető el a natív batch Python SDK-ban.)

Alapértelmezés szerint ez a tulajdonság 1 értékre van állítva, de magasabbra állíthatja a műveletek átviteli sebességének növelését. A hálózati sávszélesség és a CPU-teljesítmény növelésével kihasználhatja a megnövekedett adatátvitelt. A feladat átviteli sebessége a vagy a 100-szor értékével nő `MaxDegreeOfParallelism` `threads` . A gyakorlatban az egyidejű műveletek számát 100-re kell állítani.

 A Batch-sablonokkal Azure Batch CLI bővítmény az elérhető magok száma alapján automatikusan növeli az egyidejű műveletek számát, de ez a tulajdonság nem konfigurálható a CLI-ben.

### <a name="http-connection-limits"></a>HTTP-kapcsolatok korlátai

Ha sok párhuzamos HTTP-kapcsolattal rendelkezik, a Batch-ügyfél teljesítménye szabályozható, ha nagy számú feladatot ad hozzá. Egyes API-k korlátozzák a HTTP-kapcsolatok számát. A .NET API-val való fejlesztéskor például a [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) tulajdonság értéke alapértelmezés szerint 2. Azt javasoljuk, hogy az értéket a párhuzamos műveletek számánál közelebbi vagy nagyobb számra növelje.

## <a name="example-batch-net"></a>Példa: Batch .NET

A következő C# kódrészletek a Batch .NET API-val nagy számú feladat hozzáadásakor konfigurált beállításokat jelenítik meg.

A feladatok átviteli sebességének növeléséhez növelje a BatchClient [maxanalyticsunits](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) tulajdonságának értékét [](/dotnet/api/microsoft.azure.batch.batchclient). Például:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```

Vegyen fel egy feladatot a feladatba a [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync) vagy a [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask
) metódus megfelelő túlterhelése alapján. Például:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```

## <a name="example-batch-cli-extension"></a>Példa: batch CLI-bővítmény

A [Batch CLI-sablonokkal](batch-cli-templates.md)rendelkező Azure batch CLI-bővítmények használatával hozzon létre egy feladat- [előállítót](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md)tartalmazó sablon JSON-fájlt. A feladat-előállító egy adott feladathoz kapcsolódó feladatok gyűjteményét konfigurálja egy adott feladat-definícióból.  

A következő példa egy minta feladatot tartalmazó sablon egy nagy mennyiségű feladattal rendelkező egydimenziós parametrikus feladathoz (ebben az esetben 250 000). A feladat parancssora egy egyszerű `echo` parancs.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Ha feladatot szeretne futtatni a sablonnal, tekintse meg a következőt: [Azure batch CLI-sablonok és fájlátvitel használata](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Példa: batch Python SDK-bővítmény

A Python SDK-bővítmény Azure Batch használatához először telepítse a Python SDK-t és a bővítményt:

```
pip install azure-batch
pip install azure-batch-extensions
```

`BatchExtensionsClient`Az SDK-bővítményt használó a beállítása:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Feladathoz hozzáadandó tevékenységek gyűjteményének létrehozása. Például:

```python
tasks = list()
# Populate the list with your tasks
...
```

Adja hozzá a feladat-gyűjteményt [Task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations)használatával. Állítsa be a `threads` paramétert az egyidejű műveletek számának növeléséhez:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

A Batch Python SDK bővítmény a feladat-előállító JSON-specifikációjának használatával is támogatja a feladat-paraméterek feladathoz való hozzáadását. Például az előző batch CLI-sablonban a következőhöz hasonló, a paraméteres sweephöz tartozó feladatok paramétereinek konfigurálása:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Adja hozzá a feladatok paramétereit a feladatokhoz. Állítsa be a `threads` paramétert az egyidejű műveletek számának növeléséhez:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Következő lépések

- További információ az Azure Batch CLI-bővítmény [Batch CLI-sablonokkal](batch-cli-templates.md)történő használatáról.
- További információ a [Batch PYTHON SDK bővítményről](https://pypi.org/project/azure-batch-extensions/).
- További információ [a Azure batch ajánlott eljárásairól](best-practices.md).
