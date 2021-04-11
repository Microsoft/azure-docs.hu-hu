---
title: Feladatok futtatása párhuzamosan a Batch számítási csomópontok használatának maximalizálása érdekében
description: Növelje a hatékonyságot és az alacsonyabb költségeket azáltal, hogy kevesebb számítási csomópontot használ, és párhuzamosan futtatja a feladatokat egy Azure Batch készlet minden csomópontján.
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 2a8f2d6a040bee0e32359f4860d7b346ac08c48e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607983"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Feladatok futtatása párhuzamosan a Batch számítási csomópontok használatának maximalizálása érdekében

Az erőforrás-használatot a készlet kisebb számú számítási csomópontján is maximalizálhatja, ha egyszerre több feladatot futtat az egyes csomópontokon.

Habár egyes forgatókönyvek a legalkalmasabbak egy adott feladathoz rendelt összes csomópont erőforrásaihoz, bizonyos munkaterhelések rövidebb munkaidőt és alacsonyabb költségeket láthatnak, amikor több feladat is megosztja ezeket az erőforrásokat. Vegyük példaként a következő forgatókönyveket:

- **Csökkentse az adatátvitelt** az adatok megosztására képes feladatok számára. A megosztott adatok kisebb számú csomópontra másolásával, majd az egyes csomópontokon a feladatok párhuzamos végrehajtásával jelentősen csökkentheti az adatátviteli díjakat. Ez különösen akkor érvényes, ha az egyes csomópontokra másolandó adatforgalomot át kell vinni a földrajzi régiók között.
- **Maximalizálja a memória használatát** olyan feladatokhoz, amelyek nagy mennyiségű memóriát igényelnek, de csak rövid időszakokban és a végrehajtás során változó időpontokban. Kevesebb, de nagyobb számítási csomópontot is alkalmazhat több memóriával az ilyen tüskék hatékony kezelésére. Ezek a csomópontok több, párhuzamosan futó feladattal fognak rendelkezni az egyes csomópontokon, de minden feladat különböző időpontokban használhatja ki a csomópontok bőséges memóriáját.
- A **csomópontok számának korlátozása** , ha csomópontok közötti kommunikációra van szükség a készleten belül. A csomópontok közötti kommunikációra konfigurált készletek jelenleg 50 számítási csomópontra korlátozódnak. Ha egy adott készlet minden csomópontja képes párhuzamosan végrehajtani a feladatokat, több feladat is végrehajtható egyszerre.
- **Replikálhat egy helyszíni számítási fürtöt**, például amikor először helyez át egy számítási környezetet az Azure-ba. Ha a jelenlegi helyszíni megoldás több feladatot hajt végre egy számítási csomóponton, akkor a csomópontok maximális számát növelheti a konfiguráció alaposabb tükrözéséhez.

## <a name="example-scenario"></a>Példaforgatókönyv

Példaként Képzelje el, hogy a processzor-és memória-követelményekkel rendelkező feladathoz szükséges, hogy a [standard \_ D1](../cloud-services/cloud-services-sizes-specs.md#d-series) -csomópontok elegendőek legyenek. Azonban ahhoz, hogy a feladatot a szükséges időn belül be lehessen fejezni, a csomópontok 1 000-as szükségesek.

A standard \_ D1-es és 1 CPU-csomópontok használata helyett olyan [szabványos \_ D14](../cloud-services/cloud-services-sizes-specs.md#d-series) -csomópontokat használhat, amelyekben 16 mag van, és lehetővé teszi a párhuzamos feladatok végrehajtását. Ez azt jelenti, hogy 16 alkalommal kevesebb csomópontot lehet használni – a 1 000 csomópontok helyett csak 63 szükséges. Ha nagy méretű alkalmazásfájlok vagy hivatkozási adatok szükségesek az egyes csomópontokhoz, a feladatok időtartama és hatékonysága javul, mivel az adatok másolása csak 63 csomópontra történik.

## <a name="enable-parallel-task-execution"></a>Párhuzamos feladatok végrehajtásának engedélyezése

A számítási csomópontokat a készlet szintjén konfigurálhatja párhuzamos feladatok végrehajtásához. A Batch .NET-kódtár használatával készlet létrehozásakor állítsa be a [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) tulajdonságot. Ha a Batch-REST API használja, állítsa a [taskSlotsPerNode](/rest/api/batchservice/pool/add) elemet a kérelem törzsében a készlet létrehozása során.

> [!NOTE]
> Az `taskSlotsPerNode` Element és a [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) tulajdonság csak a készlet létrehozási idején állítható be. A készlet már létrejötte után nem módosítható.

Azure Batch lehetővé teszi, hogy a csomópontok száma legfeljebb (4x) a csomóponti magok számát adja meg. Ha például a készlet a "Large" (négy mag) méretű csomópontokkal van konfigurálva, akkor előfordulhat, `taskSlotsPerNode` hogy 16 értékre van állítva. Azonban függetlenül attól, hogy a csomópont hány magot tartalmaz, a csomópontok száma legfeljebb 256 feladat lehet. A csomópontok méretével kapcsolatos további részletekért lásd: [Cloud Services méretei](../cloud-services/cloud-services-sizes-specs.md). A szolgáltatási korlátokkal kapcsolatos további információkért lásd: [kvóták és korlátozások a Azure batch szolgáltatáshoz](batch-quota-limit.md).

> [!TIP]
> Ügyeljen arra, hogy a `taskSlotsPerNode` készlethez tartozó [autoskálázási képlet](/rest/api/batchservice/pool/enableautoscale) létrehozásakor vegye figyelembe az értéket. Előfordulhat például, hogy egy olyan képletet, amely kiértékeli, a `$RunningTasks` csomópontok számának növekedése jelentősen befolyásolhatja. További információ: [számítási csomópontok automatikus méretezése egy Azure batch készletben](batch-automatic-scaling.md).

## <a name="specify-task-distribution"></a>Feladat terjesztésének meghatározása

Az egyidejű feladatok engedélyezésekor fontos megadnia, hogyan szeretné elosztani a feladatokat a készlet csomópontjai között.

A [CloudPool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) tulajdonság használatával megadhatja, hogy a tevékenységek egyenletesen legyenek hozzárendelve a készlet összes csomópontján ("terjesztés"). Azt is megadhatja, hogy a lehető legtöbb feladat legyen hozzárendelve az egyes csomópontokhoz, mielőtt a feladatok hozzá vannak rendelve a készlet egy másik csomópontjára ("csomagolás").

Tegyük fel például, hogy a [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) értékkel konfigurált [standard \_ D14](../cloud-services/cloud-services-sizes-specs.md#d-series) -csomópontok készletét (a fenti példában). Ha a [CloudPool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) konfigurálva van egy [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) , az maximalizálja az egyes csomópontok 16 maggal való *használatát, és* lehetővé teszi az automatikus [skálázási készlet](batch-automatic-scaling.md) számára a nem használt csomópontok (csomópontok nélküli csomópontok) eltávolítását a készletből. Ez csökkenti az erőforrás-használatot, és pénzt takarít meg.

## <a name="define-variable-slots-per-task"></a>Változó tárolóhelyek definiálása feladat szerint

A [CloudTask. RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) tulajdonsággal meghatározható egy feladat, amely meghatározza, hogy hány tárolóhelyre van szükség a számítási csomópontokon való futtatáshoz. Az alapértelmezett érték az 1. Ha a feladatok különböző súlyozással rendelkeznek a számítási csomópont erőforrás-felhasználására vonatkozóan, beállíthatja a változó feladat tárolóhelyét. Ez lehetővé teszi, hogy az egyes számítási csomópontok ésszerű számú párhuzamosan futó feladatot használjanak a rendszererőforrások, például a processzor vagy a memória elárasztása nélkül.

Egy tulajdonsággal rendelkező készlet esetében például `taskSlotsPerNode = 8` elküldheti a több magos processzor-igényes feladatokat a alkalmazásban `requiredSlots = 8` , míg más feladatok is megadhatók `requiredSlots = 1` . Ha ez a vegyes számítási feladat ütemezve van, a CPU-igényes feladatok kizárólag a számítási csomópontokon futnak, míg más feladatok egyidejűleg (akár nyolc feladattal) is futhatnak más csomópontokon. Ez segít a számítási csomópontok közötti terheléselosztásban és az erőforrás-használat hatékonyságának növelésében.

Ügyeljen arra, hogy ne határozzon meg olyan tevékenységet `requiredSlots` , amely nagyobb, mint a készlet `taskSlotsPerNode` . Ez azt eredményezi, hogy a feladat soha nem fog futni. A Batch szolgáltatás jelenleg nem ellenőrzi ezt az ütközést a feladatok elküldésekor, mert előfordulhat, hogy egy feladathoz nem tartozik készlet a beküldési időpontban, vagy a letiltási/ismételt engedélyezéssel egy másik készletre módosítható.

> [!TIP]
> A változó feladat tárolóhelyek használata esetén előfordulhat, hogy a több szükséges tárolóhelytel rendelkező nagyméretű feladatok átmenetileg nem lesznek ütemezve, mert nem állnak rendelkezésre elegendő tárolóhelyek bármely számítási csomóponton, még akkor is, ha egyes csomópontokon még mindig tétlen tárolóhelyek vannak. Ezekhez a feladatokhoz növelheti a feladat prioritását, hogy a csomópontokon elérhető bővítőhelyek számára is növekedjen a verseny.
>
> A Batch szolgáltatás akkor bocsátja ki a [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) , amikor nem tud ütemezni egy feladatot, és az ütemezést addig próbálkozik, amíg a szükséges bővítőhelyek elérhetővé válnak. Ezt az eseményt figyelheti a lehetséges feladatütemezés-problémák észleléséhez és ennek megfelelő enyhítéséhez.

## <a name="batch-net-example"></a>Batch .NET-példa

A következő [Batch .net](/dotnet/api/microsoft.azure.batch) API-kódrészletek bemutatják, hogyan hozhat létre csomóponton több feladattal rendelkező készletet, és hogyan küldhet el egy feladatot a szükséges tárolóhelyekkel.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Több feladattal rendelkező készlet létrehozása csomópontok szerint

Ez a kódrészlet egy négy csomópontot tartalmazó készlet létrehozására vonatkozó kérést mutat be, amely a csomópontok között négy feladatsor megengedett. Meghatározza azt a feladatütemezés-házirendet, amely minden egyes csomópontot kitölti a feladatok a készlet egy másik csomópontjára való hozzárendelését megelőzően.

A készleteknek a Batch .NET API használatával történő hozzáadásával kapcsolatos további információkért lásd: [BatchClient. PoolOperations. CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>Feladat létrehozása a szükséges tárolóhelyekkel

Ez a kódrészlet egy nem alapértelmezett állapotú feladatot hoz létre `requiredSlots` . Ez a feladat csak akkor fut le, ha rendelkezésre áll elegendő szabad tárolóhely a számítási csomóponton.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Számítási csomópontok listázása a feladatok és a bővítőhelyek futtatásához

Ez a kódrészlet a készlet összes számítási csomópontját listázza, és kinyomtatja az egyes csomópontokon futó feladatok és feladatok tárolóhelyek számát.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Feladathoz tartozó tevékenységek felsorolása

Ez a kódrészlet beolvassa a feladat feladatait, beleértve a feladatok és a feladatok tárolóhelyek számát a feladat állapotában.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch – REST-példa

A következő [Batch Rest](/rest/api/batchservice/) API-kódrészletek bemutatják, hogyan hozhat létre csomóponton több feladattal rendelkező készletet, és hogyan küldhet el egy feladatot a szükséges tárolóhelyekkel.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Több feladattal rendelkező készlet létrehozása csomópontok szerint

Ez a kódrészlet egy olyan készlet létrehozására vonatkozó kérést mutat be, amely két nagy csomópontot tartalmaz, és a csomópontok száma legfeljebb négy feladat.

A készletek a REST API használatával történő hozzáadásával kapcsolatos további információkért lásd [a készlet hozzáadása egy fiókhoz](/rest/api/batchservice/pool/add)című témakört.

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>Feladat létrehozása a szükséges tárolóhelyekkel

Ez a kódrészlet egy olyan feladat hozzáadására vonatkozó kérést mutat be, amely nem alapértelmezett `requiredSlots` . Ez a feladat csak akkor fut le, ha rendelkezésre áll elegendő szabad tárolóhely a számítási csomóponton.

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>Mintakód a GitHubon

A GitHubon a [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) -projekt az [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) tulajdonság használatát mutatja be.

Ez a C# konzolos alkalmazás a [Batch .net](/dotnet/api/microsoft.azure.batch) -függvénytárat használja egy vagy több számítási csomóponttal rendelkező készlet létrehozásához. Konfigurálható számú feladatot hajt végre ezeken a csomópontokon egy változó terhelésének szimulálása érdekében. Az alkalmazás kimenete megmutatja, hogy mely csomópontok hajtották végre az egyes feladatokat. Az alkalmazás a feladatok paramétereinek és időtartamának összegzését is tartalmazza.

Az alábbi példa az ParallelTasks-minta alkalmazás két különböző futtatási kimenetének összefoglaló részét képezi. Az itt látható feladatok időtartama nem tartalmazza a készlet létrehozásának idejét, mivel minden egyes feladatot egy korábban létrehozott készletbe küldtek, amelynek számítási csomópontjai *inaktív* állapotban voltak a beküldési időpontban.

A minta alkalmazás első végrehajtása azt mutatja, hogy a készlet egyetlen csomópontja és egy feladat alapértelmezett beállítása egy csomóponton, a feladat időtartama több mint 30 percet vesz igénybe.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A minta második futtatása a feladatok időtartamának jelentős csökkenését mutatja. Ennek az az oka, hogy a készletet egy csomóponton belül négy feladattal konfigurálták, ami lehetővé teszi a párhuzamos feladatok végrehajtását a feladat közel egynegyedében való végrehajtásához.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Következő lépések

- Próbálja ki a [Batch Explorer](https://azure.github.io/BatchExplorer/) Heat térképet. A Batch Explorer egy ingyenes, gazdag funkcionalitású, önálló ügyfél-eszköz, amely a Azure Batch alkalmazások létrehozását, hibakeresését és figyelését segíti elő. A [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) -minta alkalmazás végrehajtásakor a Batch Explorer Heat Map szolgáltatás lehetővé teszi a párhuzamos feladatok végrehajtásának egyszerű megjelenítését az egyes csomópontokon.
- Ismerkedjen [meg Azure batch mintákkal a githubon](https://github.com/Azure/azure-batch-samples).
- További információ a [Batch-feladatok függőségeiről](batch-task-dependencies.md).
