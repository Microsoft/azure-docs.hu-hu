---
title: Több példányos feladatok használata MPI-alkalmazások futtatásához
description: Megtudhatja, hogyan hajthat végre Message Passing Interface (MPI) alkalmazásokat a többpéldányos feladattípus használatával Azure Batchban.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 6aa6a910dd57a255d9ec9292119bc692edf4946f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96351520"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Message Passing Interface-(MPI-) alkalmazások futtatása többpéldányos feladatokkal a Batch szolgáltatásban

A többpéldányos feladatok lehetővé teszik, hogy egy Azure Batch feladatot egyszerre több számítási csomóponton futtasson. Ezek a feladatok olyan nagy teljesítményű számítási forgatókönyveket tesznek lehetővé, mint például az Message Passing Interface (MPI) alkalmazások a Batchben. Ebből a cikkből megtudhatja, hogyan hajthat végre több példányos feladatot a [Batch .net][api_net] -kódtár használatával.

> [!NOTE]
> Habár a cikkben szereplő példák a Batch .NET, az MS-MPI és a Windows számítási csomópontokra összpontosítanak, az itt tárgyalt többpéldányos feladatok a más platformokra és technológiákra (például a Python és az Intel MPI Linux-csomópontokon) alkalmazhatók.
>
>

## <a name="multi-instance-task-overview"></a>A többpéldányos feladatok áttekintése
A Batch-ben minden feladat általában egyetlen számítási csomóponton fut – több feladatot küld egy feladatba, és a Batch szolgáltatás ütemezi az egyes feladatokat egy csomóponton végzett végrehajtásra. A feladatok **többpéldányos beállításainak** konfigurálásával azonban megadhatja, hogy a Batch ne hozzon létre egy elsődleges feladatot és több alfeladatot, amelyek ezután több csomóponton lesznek végrehajtva.

![A többpéldányos feladatok áttekintése][1]

Ha többpéldányos beállításokkal rendelkező feladatot küld egy feladathoz, a Batch a többpéldányos feladatok esetében több lépést hajt végre:

1. A Batch szolgáltatás egy **elsődleges** és több **alfeladatot** hoz létre a több példányos beállítások alapján. A feladatok teljes száma (az elsődleges és az összes Alfeladat) megegyezik a többpéldányos beállításokban megadott **példányok** (számítási csomópontok) számával.
2. A Batch kijelöli az egyik számítási csomópontot a **főkiszolgálóként**, és az elsődleges feladatot a főkiszolgálón hajtja végre. A többpéldányos feladathoz lefoglalt számítási csomópontok hátralévő részén hajtja végre az altevékenységeket, és a csomópontok egy alfeladata.
3. Az elsődleges és az összes Alfeladat letölti a több példányos beállításokban megadott **általános erőforrás-fájlokat** .
4. A közös erőforrás-fájlok letöltése után az elsődleges és alfeladatok végrehajtják a több példányos beállításokban megadott **koordinációs parancsot** . A koordinációs parancs általában a csomópontok előkészítéséhez használatos a feladat végrehajtásához. Ebbe beletartozhatnak a háttérben futó szolgáltatások (például a [Microsoft MPI][msmpi_msdn] `smpd.exe` -k) indítása, és ellenőrizhető, hogy a csomópontok készen állnak-e a csomópontok közötti üzenetek feldolgozására.
5. Az elsődleges feladat végrehajtja az **alkalmazás parancsát** a főcsomóponton, *miután* az elsődleges és az összes Alfeladat sikeresen végrehajtotta a koordinációs parancsot. Az Application parancs maga a több példányból álló feladat parancssora, amelyet csak az elsődleges feladat hajt végre. Egy [MS-MPI][msmpi_msdn]-alapú megoldásban itt hajthatja végre az MPI-kompatibilis alkalmazást a használatával `mpiexec.exe` .

> [!NOTE]
> Bár ez a függvény eltérő, a "többpéldányos feladat" nem egyedi feladattípus, például a [StartTask][net_starttask] vagy a [JobPreparationTask][net_jobprep]. A többpéldányos feladat egyszerűen egy standard batch-feladat ([CloudTask][net_task] a Batch .net-ben), amelynek a többpéldányos beállításai konfigurálva vannak. Ebben a cikkben erre a **többpéldányos feladatra** hivatkozunk.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>A többpéldányos feladatokra vonatkozó követelmények
A többpéldányos feladatok esetében engedélyezni kell a **csomópontok közötti kommunikációt**, és az **egyidejű feladat-végrehajtás le van tiltva**. Az egyidejű feladatok végrehajtásának letiltásához állítsa a [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) tulajdonságot 1-re.

> [!NOTE]
> A Batch [korlátozza](batch-quota-limit.md#pool-size-limits) a csomópontok közötti kommunikációt engedélyező készlet méretét.


Ez a kódrészlet azt mutatja be, hogyan lehet készletet létrehozni a többpéldányos feladatokhoz a Batch .NET-kódtár használatával.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> Ha olyan készletben próbál meg többpéldányos feladatot futtatni, amelyben a csomópontok közötti kommunikáció le van tiltva, vagy ha a *taskSlotsPerNode* értéke 1-nél nagyobb, a feladat soha nem lesz ütemezve – határozatlan ideig marad az "aktív" állapotban.


### <a name="use-a-starttask-to-install-mpi"></a>StartTask használata az MPI telepítéséhez
Ha MPI-alkalmazásokat szeretne futtatni egy többpéldányos feladattal, először telepítenie kell egy MPI-implementációt (például MS-MPI vagy Intel MPI) a készlet számítási csomópontjaira. Ez egy jó alkalom a [StartTask][net_starttask]használatára, amely akkor fut le, amikor egy csomópont egy készlethez csatlakozik, vagy újraindul. Ez a kódrészlet egy StartTask hoz létre, amely az MS-MPI telepítési csomagot adja meg [erőforrásfájlként][net_resourcefile]. Az indítási tevékenység parancssora akkor fut le, amikor az erőforrást letölti a csomópontra. Ebben az esetben a parancssor az MS-MPI felügyelet nélküli telepítését végzi.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Távoli közvetlen memória-hozzáférés (RDMA)
Ha egy [RDMA-kompatibilis méretet](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json) választ, például A9-t a Batch-készlet számítási csomópontjaihoz, az MPI-alkalmazás kihasználhatja az Azure nagy teljesítményű, kis késleltetésű távoli közvetlen memória-hozzáférési (RDMA) hálózatát.

Keresse meg a "RDMA-kompatibilis" értékkel megadott méreteket a következő cikkekben:

* **CloudServiceConfiguration** -készletek

  * [Cloud Services mérete](../cloud-services/cloud-services-sizes-specs.md) (csak Windows esetén)
* **VirtualMachineConfiguration** -készletek

  * [Virtuális gépek méretei az Azure-ban](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Virtuális gépek méretei az Azure-ban](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> A Linux-alapú [számítási csomópontok](batch-linux-nodes.md)RDMA kihasználásához az **Intel MPI** -t kell használnia a csomópontokon.
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Többpéldányos feladat létrehozása a Batch .NET-tel
Most, hogy lefedettük a készletre vonatkozó követelményeket és az MPI-csomag telepítését, hozzuk létre a több példányból álló feladatot. Ebben a kódrészletben egy standard [CloudTask][net_task]hozunk létre, majd konfigurálni kell a [MultiInstanceSettings][net_multiinstance_prop] tulajdonságot. Ahogy azt korábban említettük, a többpéldányos feladat nem egy különálló feladattípus, hanem egy szabványos batch-feladat, amely többpéldányos beállításokkal van konfigurálva.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Elsődleges feladat és alfeladatok
Amikor létrehoz egy tevékenység többpéldányos beállításait, a feladat végrehajtásához szükséges számítási csomópontok számát kell megadnia. Ha a feladatot egy feladatba küldi, a Batch szolgáltatás létrehoz egy **elsődleges** feladatot és elegendő **alfeladatot** , amelyek együttesen egyeznek a megadott csomópontok számával.

Ezek a feladatok a 0 és *numberOfInstances* -1 közötti egész szám-azonosítóhoz vannak rendelve. A 0 azonosítójú feladat az elsődleges feladat, és az összes többi azonosító Alfeladat. Ha például a következő többpéldányos beállításokat hozza létre egy feladathoz, az elsődleges feladat 0 azonosítóval fog rendelkezni, és az alfeladatok 1 – 9 azonosítóval rendelkeznek.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Fő csomópont
Többpéldányos feladat elküldésekor a Batch szolgáltatás kijelöli az egyik számítási csomópontot a "fő" csomópontként, és az elsődleges feladatot a főcsomóponton hajtja végre. Az alfeladatok a többpéldányos feladathoz lefoglalt csomópontok hátralevő részén végrehajtandó végrehajtásra vannak ütemezve.

## <a name="coordination-command"></a>Koordinációs parancs
A **koordinációs parancsot** az elsődleges és az Alfeladat is végrehajtja.

A koordinációs parancs meghívása blokkolja a-Batch nem hajtja végre az alkalmazás parancsát, amíg a koordinációs parancs sikeresen nem tért vissza az összes alfeladathoz. A koordinációs parancsnak ezért el kell indítania a szükséges háttér-szolgáltatásokat, ellenőriznie kell, hogy készen állnak-e a használatra, majd kilép. Az MS-MPI 7-es verzióját használó megoldáshoz tartozó koordinációs parancs például elindítja a SMPD szolgáltatást a csomóponton, majd kilép:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Jegyezze fel a használatát ebben a `start` koordinációs parancsban. Erre azért van szükség, mert az `smpd.exe` alkalmazás nem ad vissza azonnal a végrehajtás után. A [Start][cmd_start] parancs használata nélkül ez a koordinációs parancs nem tér vissza, ezért az alkalmazás futtatását letiltja.

## <a name="application-command"></a>Alkalmazás parancs
Miután az elsődleges feladat és az összes Alfeladat befejezte a koordinációs parancs végrehajtását, a több példányból álló tevékenység parancssorát *csak* az elsődleges feladat hajtja végre. Ezt az alkalmazás- **parancsot** hívjuk a koordinációs parancsból való megkülönböztetéshez.

MS-MPI-alkalmazások esetén az Application paranccsal futtassa az MPI-kompatibilis alkalmazást a következővel: `mpiexec.exe` . Íme például egy, az MS-MPI 7-es verziót használó megoldáshoz tartozó alkalmazási parancs:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Mivel az MS-MPI-k `mpiexec.exe` `CCP_NODES` alapértelmezés szerint a változót használják (lásd: [környezeti változók](#environment-variables)), a fenti példában szereplő alkalmazás-parancssor kizárja azt.
>
>

## <a name="environment-variables"></a>Környezeti változók
A Batch több [környezeti változót][msdn_env_var] hoz létre a többpéldányos feladatokhoz a többpéldányos feladathoz lefoglalt számítási csomópontokon. A koordináció és az alkalmazás parancssora hivatkozhat ezekre a környezeti változókra, ahogy az általuk futtatott parancsfájlok és programok is.

A Batch szolgáltatás a következő környezeti változókat hozza létre a több példányos feladatok általi használatra:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Ezen és a többi batch számítási csomópont környezeti változóinak részletes ismertetését, beleértve azok tartalmát és láthatóságát lásd: [számítási csomópont környezeti változói][msdn_env_var].

> [!TIP]
> A Batch Linux MPI-kód minta példa arra, hogy a környezeti változók közül több is használható.

## <a name="resource-files"></a>Erőforrás-fájlok
A többpéldányos feladatok esetében két erőforrás-fájl található: az *összes* feladat által letöltött **általános erőforrás-fájlok** (elsődleges és alfeladatok), valamint a többpéldányos feladathoz megadott **erőforrás-fájlok** , amelyek *csak az elsődleges* feladat letöltését jelentik.

Egy vagy több **általános erőforrás-fájlt** is megadhat egy adott tevékenység többpéldányos beállításaiban. Ezeket az általános erőforrás-fájlokat az [Azure Storage](../storage/common/storage-introduction.md) -ból az elsődleges és az összes alfeladatnak az egyes csomópontok **megosztott könyvtárába** való letöltésével tölti le a rendszer. A feladat megosztott könyvtárát az alkalmazás-és koordinációs parancssorból a környezeti változó használatával érheti el `AZ_BATCH_TASK_SHARED_DIR` . Az `AZ_BATCH_TASK_SHARED_DIR` elérési út megegyezik a többpéldányos feladathoz lefoglalt összes csomóponton, így egyetlen koordinációs parancsot is megoszthat az elsődleges és az összes Alfeladat között. A Batch nem "osztja meg" a könyvtárat a távoli elérési értelemben, de azt csatlakoztatási vagy megosztási pontként is használhatja, ahogy azt korábban említettük a környezeti változókra vonatkozó tippben.

A több példányos feladathoz megadott erőforrás-fájlokat a rendszer alapértelmezés szerint letölti a feladat munkakönyvtárára `AZ_BATCH_TASK_WORKING_DIR` . Ahogy azt korábban említettük, a közös erőforrás-fájlokkal szemben csak az elsődleges feladat tölti le a több példányos feladathoz megadott erőforrás-fájlokat.

> [!IMPORTANT]
> Mindig használja a környezeti változókat `AZ_BATCH_TASK_SHARED_DIR` , és a `AZ_BATCH_TASK_WORKING_DIR` parancssorban tekintse át ezeket a címtárakat. Ne próbálja meg manuálisan létrehozni az elérési utakat.
>
>

## <a name="task-lifetime"></a>Feladat élettartama
Az elsődleges feladat élettartama vezérli a teljes több példányos feladat élettartamát. Az elsődleges kilépés után az összes Alfeladat leáll. Az elsődleges kilépési kódja a feladat kilépési kódja, ezért a feladat sikeres vagy sikertelen próbálkozásának megállapítására szolgál.

Ha az alfeladatok bármelyike meghibásodik, a nullától eltérő visszatérési kóddal való kilépés sikertelen lesz, például a teljes többpéldányos feladat. A többpéldányos feladat ezután leáll, és újra próbálkozik, az újrapróbálkozási korlátig.

Többpéldányos feladat törlésekor az elsődleges és az összes alfeladatot is törli a Batch szolgáltatás. Az alfeladatok összes könyvtára és fájlja törlődik a számítási csomópontokból, ugyanúgy, mint a normál feladatokhoz.

A többpéldányos feladatokhoz, például a [MaxTaskRetryCount][net_taskconstraint_maxretry], a [MaxWallClockTime][net_taskconstraint_maxwallclock]és a [RetentionTime][net_taskconstraint_retention] tulajdonsághoz tartozó [TaskConstraints][net_taskconstraints] a standard feladathoz tartoznak, és az elsődleges és az összes alfeladatra érvényesek. Ha azonban a többpéldányos feladat feladathoz való hozzáadása után megváltoztatja a [RetentionTime][net_taskconstraint_retention] tulajdonságot, ez a módosítás csak az elsődleges feladatra lesz alkalmazva. Az összes Alfeladat továbbra is az eredeti [RetentionTime][net_taskconstraint_retention]használja.

A számítási csomópontok legutóbbi feladatlistája az alfeladatok azonosítóját tükrözi, ha a legutóbbi feladat egy több példányból álló feladat része volt.

## <a name="obtain-information-about-subtasks"></a>Az altevékenységekkel kapcsolatos információk beszerzése
Ha az altevékenységekkel kapcsolatos információkat a Batch .NET-kódtár használatával szeretné beszerezni, hívja meg a [CloudTask. ListSubtasks][net_task_listsubtasks] metódust. Ez a metódus az összes alfeladatra vonatkozó információt, valamint a feladatokat végrehajtó számítási csomópont adatait adja vissza. Ezen információk alapján meghatározhatja az egyes altevékenységek gyökérkönyvtárát, a készlet azonosítóját, a jelenlegi állapotát, a kilépési kódot és egyebeket. Ezeket az információkat a [PoolOperations. GetNodeFile][poolops_getnodefile] metódussal együtt használhatja az Alfeladat fájljainak beszerzéséhez. Vegye figyelembe, hogy ez a metódus nem ad vissza adatokat az elsődleges feladathoz (0. azonosító).

> [!NOTE]
> Ha másként nincs megadva, a többpéldányos [CloudTask][net_task] működő Batch .net-metódusok *csak* az elsődleges feladatra érvényesek. Ha például a [CloudTask. ListNodeFiles][net_task_listnodefiles] metódust egy többpéldányos feladaton hívja meg, akkor csak az elsődleges feladat fájljai lesznek visszaadva.
>
>

A következő kódrészlet bemutatja, hogyan szerezhet be alfeladati adatokat, valamint kérheti le a fájl tartalmát a futtatott csomópontokból.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Kódminta
A GitHubon a [MultiInstanceTasks][github_mpi] -kód minta azt mutatja be, hogyan használható egy többpéldányos feladat egy [MS-MPI-][msmpi_msdn] alkalmazás futtatására batch számítási csomópontokon. A minta futtatásához kövesse az [előkészítés](#preparation) és [végrehajtás](#execution) lépéseit.

### <a name="preparation"></a>Előkészítés
1. Kövesse az [egyszerű MS-MPI-program fordításának és futtatásának][msmpi_howto]első két lépését. Ez megfelel a következő lépés előfeltételeinek.
2. Hozza létre a [MPIHelloWorld][helloworld_proj] minta MPI-program *kiadásának* verzióját. Ez az a program, amely a többpéldányos feladat számítási csomópontjain fut majd.
3. Hozzon létre egy zip-fájlt, amely tartalmazza a `MPIHelloWorld.exe` (2. lépés) és `MSMpiSetup.exe` (amelyről az 1. lépést letöltötte). A következő lépésben feltöltheti a zip-fájlt alkalmazáscsomagként.
4. A [Azure Portal][portal] használatával hozzon létre egy "MPIHelloWorld" nevű batch- [alkalmazást](batch-application-packages.md) , és az alkalmazáscsomag "1,0" verziójával hozza létre az előző lépésben létrehozott zip-fájlt. További információt az [alkalmazások feltöltése és kezelése](batch-application-packages.md#upload-and-manage-applications) című témakörben talál.

> [!TIP]
> Hozzon létre egy *kiadási* verziót, `MPIHelloWorld.exe` így nem kell további függőségeket (például vagy) felvennie az `msvcp140d.dll` `vcruntime140d.dll` alkalmazáscsomagba.
>
>

### <a name="execution"></a>Futtatási
1. Töltse le az [Azure-batch-Samples-mintákat][github_samples_zip] a githubról.
2. Nyissa meg a MultiInstanceTasks **megoldást** a Visual Studio 2019-ben. A `MultiInstanceTasks.sln` megoldás fájljának helye:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Adja meg a Batch-és a Storage-fiók hitelesítő adatait a `AccountSettings.settings` **Microsoft.Azure.BatCH. Samples. Common** projektben.
4. Hozza **létre és futtassa** a MultiInstanceTasks-megoldást az MPI-minta alkalmazás batch-készletben lévő számítási csomópontokon való végrehajtásához.
5. Nem *kötelező*: a [Azure Portal][portal] vagy [Batch Explorer][batch_labs] használatával vizsgálja meg a minta készletet, a feladatot és a feladatot ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") az erőforrások törlése előtt.

> [!TIP]
> Ha nem rendelkezik a Visual Studióval, ingyenesen letöltheti a [Visual Studio Communityt][visual_studio] .
>
>

A kimenete a `MultiInstanceTasks.exe` következőhöz hasonló:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Következő lépések
* A Microsoft HPC & Azure Batch csapatának blogja a Azure Batch-alapú [Linux MPI-támogatását][blog_mpi_linux]tárgyalja, és információt tartalmaz a [OpenFOAM][openfoam] a Batch használatával történő használatáról. A [githubon a OpenFOAM-példához][github_mpi]Python-kódrészletek is megtalálhatók.
* Ismerje meg, hogyan [hozhat létre a Linux számítási csomópontok készleteit](batch-linux-nodes.md) az Azure batch MPI-megoldásokban való használatra.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: /dotnet/api/microsoft.azure.batch
[api_rest]: /rest/api/batchservice/
[batch_labs]: https://azure.github.io/BatchExplorer/
[blog_mpi_linux]: /archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch
[cmd_start]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc770297(v=ws.11)
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: ./batch-compute-node-environment-variables.md
[msmpi_msdn]: /message-passing-interface/microsoft-mpi
[msmpi_sdk]: https://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: /archive/blogs/windowshpc/how-to-compile-and-run-a-simple-ms-mpi-program
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: /dotnet/api/microsoft.azure.batch.jobpreparationtask
[net_multiinstance_class]: /dotnet/api/microsoft.azure.batch.multiinstancesettings
[net_multiinstance_prop]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_multiinsance_commonresfiles]: /dotnet/api/microsoft.azure.batch.multiinstancesettings
[net_multiinstance_coordcmdline]: /dotnet/api/microsoft.azure.batch.multiinstancesettings
[net_multiinstance_numinstances]: /dotnet/api/microsoft.azure.batch.multiinstancesettings
[net_pool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_pool_create]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_pool_starttask]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_resourcefile]: /dotnet/api/microsoft.azure.batch.resourcefile
[net_starttask]: /dotnet/api/microsoft.azure.batch.starttask
[net_starttask_cmdline]: /dotnet/api/microsoft.azure.batch.starttask
[net_task]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_taskconstraints]: /dotnet/api/microsoft.azure.batch.taskconstraints
[net_taskconstraint_maxretry]: /dotnet/api/microsoft.azure.batch.taskconstraints
[net_taskconstraint_maxwallclock]: /dotnet/api/microsoft.azure.batch.taskconstraints
[net_taskconstraint_retention]: /dotnet/api/microsoft.azure.batch.taskconstraints
[net_task_listsubtasks]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_task_listnodefiles]: /dotnet/api/microsoft.azure.batch.cloudtask
[poolops_getnodefile]: /dotnet/api/microsoft.azure.batch.pooloperations

[portal]: https://portal.azure.com
[rest_multiinstance]: /previous-versions/azure/mt637905(v=azure.100)

[1]: ./media/batch-mpi/batch_mpi_01.png "Többpéldányos áttekintés"