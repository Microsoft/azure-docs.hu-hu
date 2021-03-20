---
title: A feladatokhoz és a csomópontokhoz tartozó állapotok száma
description: Azure Batch feladatok és számítási csomópontok állapotának megszámlálása a Batch-megoldások kezeléséhez és figyeléséhez.
ms.date: 06/18/2020
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 90f741b9ec5e17da4fd0cc95ef921e116b0c27dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85960588"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Batch-megoldások figyelése a feladatok és a csomópontok állapot szerinti számlálásával

A nagyméretű Azure Batch megoldások figyeléséhez és kezeléséhez előfordulhat, hogy meg kell határoznia a különböző állapotokban lévő erőforrások számát. Azure Batch hatékony műveleteket biztosít a Batch-feladatok és a számítási csomópontok számának beolvasásához. Ezeket a műveleteket a lehetséges időigényes lekérdezések helyett használhatja, amelyek részletes információkat adnak vissza a tevékenységek vagy csomópontok nagy gyűjteményéről.

- A feladatok [beolvasása](/rest/api/batchservice/job/gettaskcounts) érték a feladat aktív, futó és befejezett feladatai, valamint a sikeres vagy sikertelen feladatok összesített számát mutatja be. 

  Az egyes állapotokban megjelenő feladatok megszámlálásával egyszerűbben jelenítheti meg a feladat előrehaladását egy felhasználónál, vagy észlelheti a feladattal esetlegesen befolyásoló váratlan késéseket és hibákat. A lekérési feladatok száma a Batch Service API 2017 -06-01.5.1 és a kapcsolódó SDK-k és eszközök használatával érhető el.

- A [lista csomópontjainak](/rest/api/batchservice/account/listpoolnodecounts) száma lekéri a dedikált és alacsony prioritású számítási csomópontok számát a különböző állapotú készletekben: létrehozás, üresjárat, offline, előzik, újraindítás, lemezkép-készítés, Indítás és egyebek.

  Az egyes állapotokban lévő csomópontok megszámlálásával meghatározhatja, hogy mikor van elegendő számítási erőforrás a feladatok futtatásához és a készletekkel kapcsolatos lehetséges problémák azonosításához. A lista készlet-csomópontjainak száma a Batch szolgáltatás API 2018 -03-01.6.1 és a kapcsolódó SDK-k és eszközök használatával érhető el.

Vegye figyelembe, hogy időnként a műveletek által visszaadott számok nem naprakészek. Ha meg kell győződnie arról, hogy a darabszám pontos, a lista lekérdezés használatával megszámolhatja ezeket az erőforrásokat. A listák lekérdezése lehetővé teszi az egyéb batch-erőforrásokkal, például az alkalmazásokkal kapcsolatos információk lekérését. A szűrők lekérdezésekre való alkalmazásával kapcsolatos további információkért lásd: [Lekérdezések létrehozása a Batch-erőforrások hatékony listázásához](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Feladat állapotának száma

A lekérési feladatok számlálása művelet a következő állapotok szerint számítja ki a feladatokat:

- **Aktív** – a várólistára helyezett és futtatandó feladat, de jelenleg nincs hozzárendelve számítási csomóponthoz. A feladat akkor is fennáll, `active` Ha [egy olyan fölérendelt feladattól függ](batch-task-dependencies.md) , amely még nem fejeződött be. 
- **Futtatás** – egy számítási csomóponthoz rendelt feladat, de még nem fejeződött be. A feladatok akkor számítanak bele, `running` Ha `preparing` az állapota vagy `running` a, ahogy azt a feladat műveletével kapcsolatos [információk lekérése](/rest/api/batchservice/task/get) jelzi.
- **Befejezve** – olyan feladat, amely már nem jogosult a futtatásra, mert az sikeresen befejeződött, vagy a művelet sikertelenül fejeződött be, és kimerítette az újrapróbálkozási korlátot is. 
- **Sikeres** – A feladat végrehajtásának eredményét eredményező feladat `success` . A Batch meghatározza, hogy a feladat sikeres volt-e, vagy sikertelen volt-e a `TaskExecutionResult` [executionInfo](/rest/api/batchservice/task/get) tulajdonság tulajdonságának ellenőrzésével.
- **Sikertelen művelet** Egy feladat, amelynek eredményeként a feladat végrehajtása történik `failure` .

A következő .NET-kód minta bemutatja, hogyan lehet lekérdezni a tevékenységek számát állapot szerint:

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

A REST és más támogatott nyelvek esetében hasonló mintát használhat a feladatok számának lekéréséhez. 

> [!NOTE]
> A Batch szolgáltatás API-verziói 2018 előtt -08-01.7.0 egy `validationStatus` tulajdonságot is visszaadnak a feladat lekérése válaszban. Ez a tulajdonság azt jelzi, hogy a Batch bejelölte-e az állapotot a List Tasks API-ban jelentett állapotokkal való konzisztencia érdekében. A érték `validated` azt jelzi, hogy a rendszer csak azt a köteget ellenőrzi, hogy legalább egyszer konzisztencia van-e a feladatokhoz. A `validationStatus` tulajdonság értéke nem jelzi, hogy a lekérési feladatokhoz tartozó Counts függvény jelenleg naprakész-e.

## <a name="node-state-counts"></a>Csomópont-állapotok száma

A címkészlet-csomópontok számlálása művelet a következő állapotok alapján számítja ki a számítási csomópontokat az egyes készletekben. A dedikált csomópontok és az egyes készletekben az alacsony prioritású csomópontok külön összesített számadatokat biztosítanak.

- Olyan Azure-beli virtuális gép **létrehozása** , amely még nem indult el egy készlethez való csatlakozáshoz.
- **Tétlen** – egy olyan rendelkezésre álló számítási csomópont, amely jelenleg nem futtat feladatot.
- **LeavingPool** – egy csomópont, amely elhagyja a készletet, vagy azért, mert a felhasználó explicit módon eltávolította, vagy mert a készlet átméretezés vagy automatikus skálázás.
- **Offline** – olyan csomópont, amelyet a Batch nem használhat az új feladatok beosztására.
- **Előzik** – egy alacsony prioritású csomópont, amely el lett távolítva a készletből, mert az Azure visszaigényelte a virtuális gépet. A `preempted` csomópontok újrainicializálása akkor lehetséges, ha az alacsony prioritású virtuális gép kapacitása elérhető.
- **Újraindítás** – újraindítást biztosító csomópont.
- **Reimaging** – a csomópont, amelyen az operációs rendszer újratelepítése zajlik.
- **Futtatás** – egy vagy több feladatot futtató csomópont (az indítási tevékenységen kívül).
- **Indítás** – a Batch szolgáltatást indító csomópont. 
- **StartTaskFailed** – olyan csomópont, amelyen az [indítási feladat](/rest/api/batchservice/pool/add#starttask) meghiúsult, és az összes újrapróbálkozást kimerítette, és amelyen az `waitForSuccess` indítási tevékenység van beállítva. A csomópont nem használható a feladatok futtatásához.
- **Ismeretlen** – egy olyan csomópont, amely elvesztette a kapcsolatot a Batch szolgáltatással, és amelynek állapota nem ismert.
- **Használhatatlan** – olyan csomópont, amely hibák miatt nem használható a feladatok végrehajtásához.
- **WaitingForStartTask** – olyan csomópont, amelyen az indítási tevékenység fut, de `waitForSuccess` be van állítva, és az indítási tevékenység nem fejeződött be.

A következő C# kódrészlet bemutatja, hogyan listázható a csomópontok száma az aktuális fiókban található összes készletnél:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

A következő C# kódrészlet azt mutatja be, hogyan listázható a csomópontok száma az adott készlethez az aktuális fiókban.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

A REST és más támogatott nyelvek esetében hasonló mintát használhat a készletek csomópontjainak megszerzéséhez.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- Ismerje meg, hogyan alkalmazhat szűrőket a Batch-erőforrásokat listázó lekérdezésekhez: [Lekérdezések létrehozása a Batch-erőforrások hatékony listázásához](batch-efficient-list-queries.md).
