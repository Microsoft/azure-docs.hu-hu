---
title: Feladat függőségeinek létrehozása feladatok futtatásához
description: Olyan feladatokat hozhat létre, amelyek a MapReduce stílusának és a hasonló big data munkaterheléseknek a Azure Batch-ban történő feldolgozásához szükségesek.
ms.topic: how-to
ms.date: 12/28/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: ef05a98fffc3c0684ad0fa29f2f9f039b388f5ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803935"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Tevékenységek függőségeinek létrehozása a más feladatoktól függő feladatok futtatásához

A Batch-feladatok függőségeivel olyan feladatokat hozhat létre, amelyek egy vagy több szülő feladat befejezése után a számítási csomópontokon való végrehajtásra vannak ütemezve. Létrehozhat például egy olyan feladatot, amely egy 3D-film egyes kereteit különálló, párhuzamos feladatokkal jeleníti meg. Végső feladat – az "egyesítési feladat" – a megjelenített kereteket csak akkor egyesíti a teljes filmben, ha az összes keret sikeresen meg lett jelenítve.

Néhány esetben a feladatok függőségei hasznosak lehetnek:

- MapReduce-stílusú számítási feladatok a felhőben.
- Azok a feladatok, amelyek adatfeldolgozási feladatait irányított aciklikus gráfként (DAG) lehet kifejezni.
- A renderelés előtti és utáni folyamatokat, ahol minden tevékenységnek a következő feladat megkezdése előtt végre kell hajtania.
- Minden egyéb feladat, amelyben az alárendelt feladatok a felsőbb rétegbeli tevékenységek kimenetének függvényei.

Alapértelmezés szerint a függő tevékenységek csak a fölérendelt feladat sikeres befejezése után lesznek ütemezve végrehajtásra. Megadhat egy [függőségi műveletet](#dependency-actions)  , amely felülbírálja az alapértelmezett viselkedést, és futtatja a feladatokat, ha a fölérendelt feladat meghiúsul.

## <a name="task-dependencies-with-batch-net"></a>Feladatok függőségei a Batch .NET-tel

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a feladatok függőségeit a [Batch .net](/dotnet/api/microsoft.azure.batch) -kódtár használatával. Először bemutatjuk, hogyan lehet engedélyezni a feladatok [függőségét](#enable-task-dependencies) a feladatokon, majd bemutatjuk, hogyan [konfigurálhatja a feladatokat függőségekkel](#create-dependent-tasks). Azt is leírjuk, hogyan adhat meg függőségi műveletet a függő feladatok futtatásához, ha a szülő meghibásodik. Végezetül megbeszéljük a Batch által támogatott [függőségi helyzeteket](#dependency-scenarios) .

## <a name="enable-task-dependencies"></a>Feladat függőségeinek engedélyezése

A feladatok függőségeinek a Batch-alkalmazásban való használatához először a feladat-függőségek használatára kell konfigurálnia a feladatot. A Batch .NET-ben engedélyezze a [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) a [UsesTaskDependencies](/dotnet/api/microsoft.azure.batch.cloudjob.usestaskdependencies) tulajdonságának beállításához a következőre `true` :

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Az előző kódrészletben a "batchClient" a [batchClient](/dotnet/api/microsoft.azure.batch.batchclient) osztály egy példánya.

## <a name="create-dependent-tasks"></a>Függő feladatok létrehozása

Egy vagy több fölérendelt feladat befejeződésétől függő feladat létrehozásához megadhatja, hogy a feladat a többi feladattól függ. A Batch .NET-ben konfigurálja a [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) tulajdonságot a [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies) osztály egy példányával:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Ez a kódrészlet létrehoz egy függő feladatot a "Flowers" AZONOSÍTÓJÚ feladathoz. A "virágok" feladat az "Rain" és a "Sun" tevékenységtől függ. A "Flowers" feladat csak akkor lesz ütemezve a számítási csomópontokon való futtatásra, ha az "Rain" és a "Sun" feladatok végrehajtása sikeresen befejeződött.

> [!NOTE]
> Alapértelmezés szerint a rendszer úgy tekinti a feladatot, hogy a befejezett állapotban van, és a kilépési kódja is sikeres `0` . A Batch .NET-ben ez azt jelenti, hogy a [CloudTask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state) tulajdonság értéke, `Completed` a CloudTask [TaskExecutionInformation. ExitCode](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.exitcode) tulajdonság értéke pedig `0` . Ennek módjáról a [függőségi műveletek](#dependency-actions) című szakaszban olvashat.

## <a name="dependency-scenarios"></a>Függőségi helyzetek

Három alapvető feladat-függőségi forgatókönyv használható Azure Batchban: egy-az-egyhez, egy-a-többhöz és a feladat-azonosító tartomány függőségei. Ez a három forgatókönyv össze lehet kapcsolni egy negyedik forgatókönyvet: több-a-többhöz.

| Forgatókönyv&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Példa | Ábrán |
|:---:| --- | --- |
|  [Egy-az-egyhez](#one-to-one) |a *taskB* a *Taska* függvénytől függ <p/> a *taskB* nem lesz ütemezve végrehajtásra, amíg a *Taska* sikeresen nem fejeződött be. |:::image type="content" source="media/batch-task-dependency/01_one_to_one.png" alt-text="Az egy-az-egyhez feladat-függőségi forgatókönyvet ábrázoló diagram."::: |
|  [Egy-a-többhöz](#one-to-many) |A *taskC* a *taskA* és a *taskB* tevékenységtől is függ <p/> a *taskC* nem lesz ütemezve a végrehajtáshoz, amíg a *taske* és a *taskB* sikeresen nem fejeződött be. |:::image type="content" source="media/batch-task-dependency/02_one_to_many.png" alt-text="Az egy-a-többhöz feladat-függőségi forgatókönyvet ábrázoló diagram."::: |
|  [Feladat-azonosító tartománya](#task-id-range) |a *feladat* számos feladattól függ <p/> a *feladat* nem lesz ütemezve végrehajtásra, amíg az *1* – *10* azonosítójú feladatok sikeresen befejeződtek. |:::image type="content" source="media/batch-task-dependency/03_task_id_range.png" alt-text="A feladat-azonosító tartomány feladatának függőségi forgatókönyvét ábrázoló diagram."::: |

> [!TIP]
> **Több-a-többhöz** kapcsolatot is létrehozhat, például a C, D, E és F feladatok mindegyike az a és B feladatoktól függ. Ez hasznos lehet például olyan párhuzamos előfeldolgozási forgatókönyvekben, ahol az alsóbb rétegbeli tevékenységek több felsőbb rétegbeli feladat kimenetének függvényei.
> 
> Az ebben a szakaszban szereplő példákban egy függő feladat csak a fölérendelt feladatok sikeres befejeződése után fut le. Ez a viselkedés egy függő feladat alapértelmezett viselkedése. A fölérendelt feladat végrehajtása után futtathat egy függő feladatot egy [függőségi művelet](#dependency-actions)  megadásával, amely felülbírálja az alapértelmezett viselkedést.

### <a name="one-to-one"></a>Egy-az-egyhez

Egy-az-egyhez kapcsolat esetén a feladat egy fölérendelt feladat sikeres befejezését határozza meg. A függőség létrehozásához adjon meg egyetlen [TaskDependencies. OnId](/dotnet/api/microsoft.azure.batch.taskdependencies.onid) statikus metódust az [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) tulajdonság feltöltésekor.

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Egy-a-többhöz

Egy-a-többhöz kapcsolat esetén a feladatok több szülő feladat befejeződésétől függenek. A függőség létrehozásához adjon meg egy [TaskDependencies. OnIds](/dotnet/api/microsoft.azure.batch.taskdependencies.onids) statikus metódust a [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) tulajdonság feltöltésekor.

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Feladat-azonosító tartománya

A fölérendelt feladatok számos függősége esetén a feladatok attól függnek, hogy az azonosítók milyen tartományon belül találhatók.
A függőség létrehozásához adja meg a tartomány első és utolsó feladatának azonosítóját a [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies.onidrange) statikus metódushoz, amikor feltölti az [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) tulajdonságot.

> [!IMPORTANT]
> Ha a függőségek esetében feladatsor-tartományt használ, csak az egész értékeket megadó feladatokat fogja kiválasztani a tartomány. A tartomány például kiválasztja a `1..10` feladatokat `3` `7` , de nem `5flamingoes` .
>
> A bevezető nullák nem jelentősek a tartomány függőségeinek kiértékelése során, így a karakterlánc-azonosítókkal rendelkező tevékenységek `4` , `04` és `004` mind a tartományon *belül* lesznek, és az összes feladatot feladatnak tekinti `4` , így az első befejezési művelet megfelel a függőségnek.
>
> A tartomány minden tevékenységének meg kell felelnie a függőségnek, akár a sikeres befejezést követően, akár egy, a [függőségi művelethez](#dependency-actions) beállított hibával, amely **megfelel a követelményeknek**.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Függőségi műveletek

Alapértelmezés szerint a függő feladat vagy a tevékenységek halmaza csak a fölérendelt feladat sikeres befejeződése után fut. Bizonyos helyzetekben érdemes lehet függő feladatokat futtatni, még akkor is, ha a fölérendelt feladat meghiúsul. A függőségi műveletek megadásával felülbírálhatja az alapértelmezett viselkedést.

A függőségi műveletek határozzák meg, hogy egy függő feladat futtatható-e a fölérendelt feladat sikeressége vagy meghibásodása alapján. Tegyük fel például, hogy egy függő feladat a felsőbb rétegbeli feladat befejezésekor vár az adatokra. Ha a felsőbb rétegbeli feladat meghiúsul, előfordulhat, hogy a függő feladat továbbra is futtatható a régebbi adatok használatával. Ebben az esetben a függőségi művelet megadhatja, hogy a függő tevékenység a fölérendelt feladat meghibásodása ellenére is futtatható legyen.

A függőségi műveletek a fölérendelt feladat kilépési feltételén alapulnak. A következő kilépési feltételek bármelyikéhez megadhat függőségi műveletet:

- Előfeldolgozási hiba esetén.
- Fájl feltöltésekor felmerülő hiba esetén. Ha a feladat kilép egy **exitCodes** -vagy **exitCodeRanges**-n keresztül megadott kilépési kóddal, és a fájl feltöltési hibába ütközik, a kilépési kód által megadott művelet elsőbbséget élvez.
- Ha a feladat kilép a **ExitCodes** tulajdonság által meghatározott kilépési kóddal.
- Ha a feladat kilép egy kilépési kóddal, amely a **ExitCodeRanges** tulajdonság által megadott tartományon belül esik.
- Az alapértelmezett eset, ha a feladat kilép egy **ExitCodes** vagy **ExitCodeRanges** által nem definiált kilépési kóddal, vagy ha a feladat egy előfeldolgozási hiba miatt kilép, és a **PreProcessingError** tulajdonság nincs beállítva, vagy ha a feladat meghiúsul, és a **FileUploadError** tulajdonság nincs beállítva. 

A .NET esetében tekintse meg a [ExitConditions](/dotnet/api/microsoft.azure.batch.exitconditions) osztályt az ilyen feltételekkel kapcsolatos további részletekért.

Ha függőségi műveletet szeretne megadni a .NET-ben, állítsa a kilépési feltételhez a [ExitOptions. DependencyAction](/dotnet/api/microsoft.azure.batch.exitoptions.dependencyaction) tulajdonságot a következők egyikére:

- **Megfelelő**: azt jelzi, hogy a függő tevékenységek futtatására jogosultak, ha a fölérendelt feladat megadott hibával kilép.
- **Letiltás**: azt jelzi, hogy a függő tevékenységek nem jogosultak a futtatásra.

A **DependencyAction** tulajdonság alapértelmezett beállítása a 0. kilépési kód és az összes többi kilépési feltétel **blokkolását** is **kielégíti** .

A következő kódrészlet beállítja a **DependencyAction** tulajdonságot egy fölérendelt feladathoz. Ha a fölérendelt feladat egy előfeldolgozási hibával vagy a megadott hibakódokkal kilép, a függő feladat blokkolva lesz. Ha a fölérendelt feladat bármilyen más nullától eltérő hibával kilép, a függő feladat jogosult a futtatásra.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Kódminta

A GitHubon a [TaskDependencies](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) -minta projekt a következőket mutatja be:

- Feladat-függőség engedélyezése egy feladathoz.
- Más feladatoktól függő feladatok létrehozása.
- A feladatok végrehajtása számítási csomópontok készletén.

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg a Batch [alkalmazáscsomag](batch-application-packages.md) szolgáltatásával, amely egyszerű módszert biztosít a tevékenységek által a számítási csomópontokon futtatott alkalmazások üzembe helyezésére és verziójának telepítésére.
- A [feladatok és a feladatok hibakeresésének](batch-job-task-error-checking.md)ismertetése.
