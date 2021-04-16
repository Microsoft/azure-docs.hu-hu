---
title: Tevékenységek egyidejű futtatása a Batch számítási csomópontok használatának maximalizálása érdekében
description: A hatékonyság növelése és a költségek csökkentése kevesebb számítási csomópont és a tevékenységek párhuzamos futtatása egy erőforráskészlet minden csomópontján Azure Batch használatával
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 81648f30a7a02f702dcb189aa7df27e5a82e2b07
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389298"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Tevékenységek egyidejű futtatása a Batch számítási csomópontok használatának maximalizálása érdekében

Maximalizálhatja a készlet kevesebb számítási csomópontjának erőforrás-használatát, ha egyidejűleg több feladatot futtat minden csomóponton.

Bár egyes forgatókönyvek a legjobban működnek egy csomópont egyetlen tevékenységhez dedikált erőforrásaival, bizonyos számítási feladatok rövidebb feladat-időket és alacsonyabb költségeket láthatnak, ha több tevékenység osztozik ezen erőforrásokon. Vegyük példaként a következő forgatókönyveket:

- **Minimalizálja az adatátvitelt** az olyan feladatok esetén, amelyek képesek adatokat megosztani. Jelentősen csökkentheti az adatátviteli díjakat, ha a megosztott adatokat kisebb számú csomópontra másolja, majd párhuzamosan végrehajtja a feladatokat az egyes csomópontokon. Ez különösen akkor érvényes, ha az egyes csomópontra másolt adatokat földrajzi régiók között kell átvini.
- **Maximalizálja a memóriahasználatot** a nagy mennyiségű memóriát igénylő feladatoknál, de csak rövid ideig és változó időpontokban a végrehajtás során. Az ilyen kiugró csúcsok hatékony kezeléséhez kevesebb, de nagyobb, több memóriával rendelkező számítási csomópontot is lehet használni. Ezek a csomópontok több, párhuzamosan futó feladattal fognak futni minden csomóponton, de minden tevékenység különböző időpontokban kihasználhatja a csomópontok bőséges memóriáját.
- **Csomópontok számának korlátozásának csökkentése,** ha csomópontok közötti kommunikációra van szükség egy készleten belül. Jelenleg a csomópontok közötti kommunikációhoz konfigurált készletek legfeljebb 50 számítási csomópontra korlátozódnak. Ha egy ilyen készlet minden csomópontja képes párhuzamosan végrehajtani feladatokat, egyszerre több tevékenység is végrehajtható.
- **Replikáljon egy helyszíni számítási fürtöt,** például amikor először áthelyez egy számítási környezetet az Azure-ba. Ha a jelenlegi helyszíni megoldás számítási csomópontonként több feladatot hajt végre, megnövelheti a csomópontfeladatok maximális számát, hogy jobban tükrözni tudja ezt a konfigurációt.

## <a name="example-scenario"></a>Példaforgatókönyv

Képzeljen el például egy olyan feladatalkalmazást, amely processzor- és memóriakövetelményekkel rendelkezik, hogy a [Standard \_ D1](../cloud-services/cloud-services-sizes-specs.md#d-series) csomópontok elegendőek. Ahhoz azonban, hogy a feladatot a szükséges időben befejezze, 1000 csomópontra van szükség.

Az 1 processzormaggal és standard D1 csomópontok használata helyett használhatja a 16 magos \_ [Standard \_ D14-csomópontokat,](../cloud-services/cloud-services-sizes-specs.md#d-series) és engedélyezheti a tevékenységek párhuzamos végrehajtását. Ez azt jelenti, hogy 16-szor kevesebb csomópont használható – 1000 csomópont helyett csak 63-ra lenne szükség. Ha nagy méretű alkalmazásfájlokra vagy referenciaadatokra van szükség minden csomóponthoz, javul a feladat időtartama és hatékonysága, mivel a rendszer csak 63 csomópontra másolja az adatokat.

## <a name="enable-parallel-task-execution"></a>Párhuzamos feladat-végrehajtás engedélyezése

A számítási csomópontokat a készlet szintjén konfigurálhatja párhuzamos tevékenységek végrehajtásához. A Batch .NET-kódtárban állítsa be a [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) tulajdonságot a készlet létrehozásakor. Ha a Batch-erőforráscsoportot REST API, állítsa be a [taskSlotsPerNode](/rest/api/batchservice/pool/add) elemet a kérés törzsében a készlet létrehozása során.

> [!NOTE]
> Az elemet és a `taskSlotsPerNode` [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) tulajdonságot csak a készlet létrehozásakor állíthatja be. A készlet létrehozása után ezek nem módosíthatók.

Azure Batch lehetővé teszi, hogy csomópontonként legfeljebb (4-edik) számú csomópontmagot állítson be. Ha például a készlet "Nagy" (négy magos) csomópontokkal van konfigurálva, akkor lehet, hogy `taskSlotsPerNode` 16-ra van beállítva. Azonban attól függetlenül, hogy a csomópont hány maggal rendelkezik, csomópontonként nem lehet 256-osnál több tevékenységhely. Az egyes csomópontméretek magszámának részleteiért lásd: [A](../cloud-services/cloud-services-sizes-specs.md)csomópontok Cloud Services. A szolgáltatási korlátokkal kapcsolatos további információkért lásd: A Azure Batch [korlátai.](batch-quota-limit.md)

> [!TIP]
> A készlet automatikus méretezési képletének létrehozásakor vegye figyelembe `taskSlotsPerNode` az értéket. [](/rest/api/batchservice/pool/enableautoscale) Egy kiértékelt képletre például jelentős hatással lehet a tevékenységek `$RunningTasks` csomópontonkénti növekedése. További információ: [Számítási csomópontok automatikus méretezése egy Azure Batch készletben.](batch-automatic-scaling.md)

## <a name="specify-task-distribution"></a>Feladatelosztás megadása

Az egyidejű feladatok engedélyezésekor fontos megadni, hogyan szeretné elosztani a tevékenységeket a készlet csomópontjai között.

A [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) tulajdonság használatával megadhatja, hogy a tevékenységeket egyenletesen kell hozzárendelni a készlet összes csomópontja között ("osztás"). Azt is megadhatja, hogy a lehető legtöbb feladatot kell minden csomóponthoz hozzárendelni, mielőtt a tevékenységeket a készlet egy másik csomópontja ("csomagolás") rendeli hozzá.

Vegyük példaként a [Standard \_ D14-csomópontok](../cloud-services/cloud-services-sizes-specs.md#d-series) készletét (a fenti példában), amely 16-os [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) értékkel van konfigurálva. Ha a [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) a [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) csomaggal van konfigurálva, akkor maximalizálná az egyes csomópontok mind a 16 magjának használatát, és lehetővé tenné egy automatikus skálázású készlet számára, hogy eltávolítsa a készletből a nem használt csomópontokat (tevékenységek nélküli csomópontokat).  [](batch-automatic-scaling.md) Ez minimalizálja az erőforrás-használatot, és pénzt takarít meg.

## <a name="define-variable-slots-per-task"></a>Változó tárolóhelyek meghatározása feladatonként

Egy tevékenység a [CloudTask.RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) tulajdonsággal határozható meg, megadva, hogy hány tárolóhelyre van szükség a számítási csomóponton való futtatáshoz. Az alapértelmezett érték az 1. Akkor állíthat be változó tevékenységhelyeket, ha a tevékenységek különböző súlyozással vannak kapcsolatban a számítási csomópont erőforrás-használatát illetően. Ez lehetővé teszi, hogy minden számítási csomópont ésszerű számú egyidejű futó feladattal rendelkezik anélkül, hogy túlterheli a rendszer erőforrásait, például a processzort vagy a memóriát.

A tulajdonsággal beállított készlet esetén például elküldheti a többmagos, processzorigényes feladatokat a alkalmazással, míg más feladatok `taskSlotsPerNode = 8` `requiredSlots = 8` beállíthatók a következőre: `requiredSlots = 1` . Ha ez a vegyes számítási feladat ütemezve van, a processzorigényes feladatok kizárólag a számítási csomópontjaikon futnak, míg más tevékenységek egyidejűleg (legfeljebb nyolc feladattal) futnak más csomópontokon. Ez segít a számítási feladatok számítási csomópontok közötti elosztásában és az erőforrás-használat hatékonyságának javításában.

Győződjön meg arról, hogy nem a készletben található értéknél nagyobb tevékenységeket `requiredSlots` ad `taskSlotsPerNode` meg. Ez azt eredményezi, hogy a feladat soha nem fog tudni futni. A Batch szolgáltatás jelenleg nem ellenőrzi ezt az ütközést a tevékenységek elküldésekor, mert előfordulhat, hogy egy feladathoz nem tartozik készlet a beküldéskor, vagy a letiltás/újra engedélyezés letiltásával vagy újra engedélyezésével másik készletre módosítható.

> [!TIP]
> Változó tevékenység-tárolóhelyek használata esetén előfordulhat, hogy a nagyobb, több szükséges tárolóhelyet használó tevékenységeket átmenetileg nem lehet ütemezni, mert nem áll rendelkezésre elegendő tárolóhely egyik számítási csomóponton sem, még akkor is, ha egyes csomópontokon még mindig vannak tétlen tárolóhelyek. Növelheti ezeknek a tevékenységeknek a feladat prioritását, így növelheti annak esélyét, hogy versenyezzenek a csomópontokon elérhető tárolóhelyekért.
>
> A Batch szolgáltatás akkor bocsátja ki a [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) et, amikor nem tud ütemezni egy feladatot a futtatáshoz, és addig próbálkozik újra az ütemezéssel, amíg a szükséges tárolóhelyek elérhetővé nem válnak. Az eseményt figyelve észlelheti a tevékenységek ütemezésével kapcsolatos lehetséges problémákat, és ennek megfelelően mérsékelheti azokat.

## <a name="batch-net-example"></a>Batch .NET-példa

Az alábbi [Batch .NET](/dotnet/api/microsoft.azure.batch) API-kódrészletek azt mutatják be, hogyan hozhat létre csomópontonként több tevékenységponttal rendelkező készletet, és hogyan küldhet el egy feladatot a szükséges tárolóhelyekkel.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Készlet létrehozása csomópontonként több tevékenységponttal

Ez a kódrészlet egy olyan készlet létrehozására vonatkozó kérést mutat be, amely négy csomópontot tartalmaz, és csomópontonként négy tevékenységhely engedélyezett. Olyan tevékenységütemezési szabályzatot ad meg, amely az egyes csomópontokat tevékenységekkel tölti ki, mielőtt tevékenységeket rendel a készlet egy másik csomópontjára.

A készletek Batch .NET API-val való hozzáadásával kapcsolatos további információkért lásd: [BatchClient.PoolOperations.CreatePool.](/dotnet/api/microsoft.azure.batch.pooloperations.createpool)

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
            imageReference: new ImageReference(
                                publisher: "MicrosoftWindowsServer",
                                offer: "WindowsServer",
                                sku: "2019-datacenter-core",
                                version: "latest"),
            nodeAgentSkuId: "batch.node.windows amd64");

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>Feladat létrehozása a szükséges tárolóhelyekkel

Ez a kódrészlet nem alapértelmezett feladattal hoz létre `requiredSlots` feladatot. Ez a feladat csak akkor fut, ha elegendő szabad tárolóhely áll rendelkezésre egy számítási csomóponton.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Számítási csomópontok felsorolása a futó feladatok és tárolóhelyek számával

Ez a kódrészlet felsorolja a készletben lévő összes számítási csomópontot, és megjeleníti a futó tevékenységek és tevékenységhelyek számát csomópontonként.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>A feladat tevékenységszámának felsorolása

Ez a kódrészlet a feladat tevékenységszámát kapja meg, beleértve a tevékenységek és a tevékenységhelyek számát tevékenységállapotonként.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch – REST-példa

Az alábbi [Batch REST](/rest/api/batchservice/) API-kódrészletek azt mutatják be, hogyan hozhat létre csomópontonként több tevékenységponttal rendelkező készletet, és hogyan küldhet el egy feladatot a szükséges tárolóhelyekkel.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Készlet létrehozása csomópontonként több tevékenységponttal

Ez a kódrészlet egy olyan készlet létrehozására vonatkozó kérést mutat be, amely két nagy csomópontot tartalmaz, csomópontonként legfeljebb négy feladattal.

További információ a készletek hozzáadásáról a REST API: Készlet hozzáadása [fiókhoz.](/rest/api/batchservice/pool/add)

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "canonical",
      "offer": "ubuntuserver",
      "sku": "18.04-lts"
    },
    "nodeAgentSKUId": "batch.node.ubuntu 16.04"
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>Feladat létrehozása a szükséges tárolóhelyekkel

Ez a kódrészlet egy nem alapértelmezett feladat hozzáadására vonatkozó kérést mutat `requiredSlots` be. Ez a feladat csak akkor fut, ha elegendő szabad tárolóhely áll rendelkezésre a számítási csomóponton.

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

## <a name="code-sample-on-github"></a>Kódminta a GitHubon

A [ParallelTasks projekt](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) a GitHubon a [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) tulajdonság használatát mutatja be.

Ez a C#-konzolalkalmazás a [Batch .NET-kódtár használatával](/dotnet/api/microsoft.azure.batch) hoz létre egy készletet egy vagy több számítási csomóponttal. Konfigurálható számú feladatot hajt végre a csomópontokon a változók terhelésének szimulálása érdekében. Az alkalmazás kimenete megjeleníti, hogy mely csomópontok hajtották végre az egyes feladatokat. Az alkalmazás emellett a feladat paramétereinek és időtartamának összegzését is biztosítja.

Az alábbiakban például a ParallelTasks mintaalkalmazás két különböző futtatása kimenetének összegző része látható. Az itt látható feladat-időtartamok nem tartalmazzák a készlet létrehozásának idejét, mivel minden feladat  egy korábban létrehozott készletbe lett elküldve, amelynek számítási csomópontjai tétlen állapotban voltak a beküldéskor.

A mintaalkalmazás első végrehajtása azt mutatja, hogy a készletben egyetlen csomóponttal és a csomópontonként egy tevékenység alapértelmezett beállításával a feladat időtartama több mint 30 perc.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A minta második futtatása a feladat időtartamának jelentős csökkenését mutatja. Ennek az az oka, hogy a készlet csomópontonként négy tevékenységre lett konfigurálva, így a párhuzamos tevékenység-végrehajtás közel negyedévente befejezheti a feladatot.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Következő lépések

- Próbálja ki [Batch Explorer](https://azure.github.io/BatchExplorer/) Hőtérképet. Batch Explorer egy ingyenes, gazdag funkcionalitású, különálló ügyféleszköz, amely segítséget ad az alkalmazások létrehozásához, hibakereséséhez és Azure Batch figyelése érdekében. A [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) mintaalkalmazás végrehajtásakor az Batch Explorer Heat Map funkcióval egyszerűen vizualizálhatja a párhuzamos tevékenységek végrehajtását az egyes csomópontok esetében.
- Ismerje [meg Azure Batch mintákat a GitHubon.](https://github.com/Azure/azure-batch-samples)
- További információ a [Batch-tevékenységek függőségeiről.](batch-task-dependencies.md)
