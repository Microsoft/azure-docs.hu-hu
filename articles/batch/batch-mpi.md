---
title: Többpéldányos feladatok használata MPI-alkalmazások futtatásához
description: Megtudhatja, hogyan hajthatnak végre Message Passing Interface (MPI) alkalmazásokat a többpéldányos feladattípussal a Azure Batch.
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: e96cfb89b186d69f6ad969949b8df609956114d2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389400"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Többpéldányos feladatok használata Message Passing Interface (MPI) alkalmazások futtatásához a Batchben

A többpéldányos tevékenységek lehetővé teszik egy Azure Batch több számítási csomóponton egyidejűleg történő futtatását. Ezek a feladatok olyan nagy teljesítményű számítási forgatókönyveket tesz lehetővé, mint Message Passing Interface (MPI) alkalmazások a Batchben. Ebből a cikkből megtudhatja, hogyan hajthat végre többpéldányos feladatokat a [Batch .NET-kódtár](/dotnet/api/microsoft.azure.batch) használatával.

> [!NOTE]
> Bár a cikkben található példák a Batch .NET,MS-MPI és Windows számítási csomópontokra vonatkoznak, az itt tárgyalt többpéldányos feladatfogalmak más platformokra és technológiákra is vonatkoznak (például Python és Intel MPI Linux-csomópontokon).

## <a name="multi-instance-task-overview"></a>Többpéldányos feladatok áttekintése

A Batchben minden tevékenység általában egyetlen számítási csomóponton van végrehajtva – több tevékenységet is elküld egy feladatnak, és a Batch szolgáltatás ütemezi az egyes tevékenységek végrehajtását egy csomóponton. Egy tevékenység többpéldányos beállításainak konfigurálásával azonban a Batch-nek egy elsődleges tevékenységet és több alfeladatot kell létrehoznia, amelyek aztán több csomóponton vannak végrehajtva. 

:::image type="content" source="media/batch-mpi/batch-mpi-01.png" alt-text="A többpéldányos beállítások áttekintését bemutató ábra.":::

Amikor többpéldányos beállításokkal elküld egy tevékenységet egy feladatnak, a Batch több, a többpéldányos tevékenységekre vonatkozó lépést is végrehajt:

1. A Batch szolgáltatás egy elsődleges **és** több **alfeladatot hoz létre** a többpéldányos beállítások alapján. A tevékenységek teljes száma (elsődleges és az összes alfeladat) megegyezik a többpéldányos beállításokban megadott példányok **(számítási** csomópontok) számával.
2. A Batch kijelöli az egyik számítási csomópontot fő csomópontként, és ütemezi az elsődleges tevékenységet a főkiszolgálón való végrehajtásra. Úgy ütemezi az alfeladatokat, hogy a többpéldányos tevékenységhez lefoglalt számítási csomópontok fennmaradó részfeladatán hajtsanak végre végrehajtást, csomópontonként egy alfeladatot.
3. Az elsődleges és az összes  alfeladat letölti a többpéldányos beállításokban megadott közös erőforrásfájlokat.
4. A közös erőforrásfájlok letöltése után az elsődleges és az  alfeladatok végrehajtják a többpéldányos beállításokban megadott koordinációs parancsot. A koordinációs parancs általában a csomópontok előkészítésére használatos a feladat végrehajtásához. Ez magában foglalhatja a háttérszolgáltatások (például [a Microsoft MPI-k)](/message-passing-interface/microsoft-mpi) indítását és annak ellenőrzését, hogy a csomópontok készen állnak-e a csomópontok közötti `smpd.exe` üzenetek feldolgozására.
5. Az elsődleges tevékenység  végrehajtja az alkalmazásparancsot a fő csomóponton, miután az elsődleges és az összes alfeladat sikeresen befejezte a koordinációs parancsot.  Az alkalmazásparancs maga a többpéldányos feladat parancssora, és csak az elsődleges tevékenység hajt végre. [Ms-MPI-alapú](/message-passing-interface/microsoft-mpi) megoldás esetén itt hajtható végre az MPI-kompatibilis alkalmazás a `mpiexec.exe` használatával.

> [!NOTE]
> Bár funkcionálisan eltérő, a "többpéldányos tevékenység" nem egyedi feladattípus, például a [StartTask](/dotnet/api/microsoft.azure.batch.starttask) vagy [a JobPreparationTask.](/dotnet/api/microsoft.azure.batch.jobpreparationtask) A többpéldányos tevékenység egyszerűen egy szabványos Batch-feladat ([CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) a Batch .NET-en), amelynek többpéldányos beállításait konfigurálták. Ebben a cikkben erre többpéldányos **feladatként hivatkozunk.**

## <a name="requirements-for-multi-instance-tasks"></a>A többpéldányos feladatokra vonatkozó követelmények

A többpéldányos feladatokhoz olyan készletre van szükség, amelynél engedélyezve van a csomópontok közötti **kommunikáció,** és le kell tiltani **az egyidejű feladat-végrehajtást.** Az egyidejű feladat-végrehajtás letiltásához állítsa a [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) tulajdonságot 1-re.

> [!NOTE]
> A Batch [korlátozza](batch-quota-limit.md#pool-size-limits) a csomópontok közötti kommunikációt engedélyező készlet méretét.

Ez a kódrészlet bemutatja, hogyan hozhat létre készletet többpéldányos feladatokhoz a Batch .NET-kódtár használatával.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
        imageReference: new ImageReference(
                        publisher: "MicrosoftWindowsServer",
                        offer: "WindowsServer",
                        sku: "2019-datacenter-core",
                        version: "latest"),
        nodeAgentSkuId: "batch.node.windows amd64");

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> Ha olyan készletben próbál többpéldányos feladatot futtatni, amelynél a csomópontok közötti kommunikáció le van tiltva, vagy ha a *taskSlotsPerNode* értéke nagyobb, mint 1, a feladat soha nincs ütemezve – határozatlan ideig aktív állapotban marad.

### <a name="use-a-starttask-to-install-mpi"></a>Az MPI telepítése StartTask használatával

Az MPI-alkalmazások többpéldányos feladattal való futtatásához először telepítenie kell egy MPI-implementációt (MS-MPI vagy Intel MPI) a készlet számítási csomópontjain. Itt a [StartTask](/dotnet/api/microsoft.azure.batch.starttask)használata a megfelelő időpont, amely akkor fut le, amikor egy csomópont csatlakozik egy készlethez, vagy újraindul. Ez a kódrészlet létrehoz egy StartTask kódot, amely erőforrásfájlként megadja az MS-MPI [telepítőcsomagot.](/dotnet/api/microsoft.azure.batch.resourcefile) Az indítási tevékenység parancssorának végrehajtása az erőforrásfájl csomópontra való letöltése után történik. Ebben az esetben a parancssor az MS-MPI felügyelet nélküli telepítését végzi el.

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

Ha [RDMA-kompatibilis](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json) méretet (például A9) választ a Batch-készletben lévő számítási csomópontokhoz, az MPI-alkalmazás kihasználhatja az Azure nagy teljesítményű, kis késleltetésű távoli közvetlen memória-hozzáférési (RDMA) hálózatát.

Keresse meg az "RDMA-kompatibilis" [](../virtual-machines/sizes.md) méreteket az Azure-beli virtuális gépek méretei (VirtualMachineConfiguration-készletek esetén) vagy [a Sizes for Cloud Services](../cloud-services/cloud-services-sizes-specs.md) (CloudServicesConfiguration-készletek esetén) alatt.

> [!NOTE]
> Az RDMA Linux számítási csomópontokon való [kihasználása előtt](batch-linux-nodes.md)az **Intel MPI-t** kell használnia a csomópontokon.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Többpéldányos feladat létrehozása a Batch .NET segítségével

Most, hogy már megfeleltünk a készlet követelményeinek és az MPI-csomagok telepítésének, létrehozására is van szükségünk. Ebben a kódrészletben létrehozunk egy standard [CloudTaskot,](/dotnet/api/microsoft.azure.batch.cloudtask)majd konfiguráljuk annak [MultiInstanceSettings tulajdonságát.](/dotnet/api/microsoft.azure.batch.cloudtask) Ahogy korábban említettük, a többpéldányos feladat nem egy különálló tevékenységtípus, hanem egy standard Batch-feladat, amely többpéldányos beállításokkal van konfigurálva.

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

## <a name="primary-task-and-subtasks"></a>Elsődleges tevékenység és alfeladatok

Amikor többpéldányos beállításokat hoz létre egy feladathoz, meg kell adnia a feladat végrehajtásához szükséges számítási csomópontok számát. Amikor elküldi a tevékenységet egy feladatnak,  a Batch  szolgáltatás létrehoz egy elsődleges tevékenységet és elegendő alfeladatot, amelyek együtt megfelelnek a megadott csomópontok számának.

Ezek a tevékenységek egy 0 és *numberOfInstances* – 1 tartományba eső egész számazonosítót kapnak. A 0 azonosítójú feladat az elsődleges feladat, az összes többi azonosító pedig alfeladat. Ha például a következő többpéldányos beállításokat hozza létre egy tevékenységhez, az elsődleges tevékenység azonosítója 0 lesz, az alfeladatok pedig 1–9 azonosítóval.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Fő csomópont

Többpéldányos tevékenység elküldésekor a Batch szolgáltatás kijelöli az egyik számítási csomópontot fő csomópontként, és ütemezi az elsődleges tevékenységet a főcsomóponton való végrehajtásra. Az alfeladatok végrehajtása a többpéldányos feladathoz lefoglalt többi csomóponton van ütemezve.

## <a name="coordination-command"></a>Koordináció parancs

A **koordinációs** parancsot az elsődleges és az alfeladat is végrehajtja.

A koordinációs parancs meghívása blokkoló – a Batch nem hajtja végre az alkalmazásparancsot, amíg a koordinációs parancs sikeresen vissza nem tér az összes alfeladathoz. A koordinációs parancsnak ezért el kell kezdenie minden szükséges háttérszolgáltatást, ellenőriznie kell, hogy készen állnak-e a használatra, majd ki kell lépnie. Ez a koordinációs parancs például egy MS-MPI 7-es verziót használó megoldáshoz elindítja az SMPD szolgáltatást a csomóponton, majd kilép:

`cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d`

Figyelje meg a használatát `start` ebben a koordináló parancsban. Erre azért van szükség, mert `smpd.exe` az alkalmazás nem tér vissza azonnal a végrehajtás után. A start parancs használata nélkül ez a koordináló parancs nem adná vissza a parancsot, ezért blokkolná az alkalmazásparancs futását.

## <a name="application-command"></a>Alkalmazás parancs

Miután az elsődleges tevékenység és az összes alfeladat befejezte a koordinációs parancs végrehajtását, a többpéldányos tevékenység parancssorát csak az elsődleges tevékenység *hajtja végre.* Ezt **alkalmazásparancsnak hívjuk, hogy** megkülönböztetjük a koordinációs parancstól.

MS-MPI-alkalmazások esetén az alkalmazásparancs használatával hajtsa végre az MPI-kompatibilis alkalmazást az `mpiexec.exe` paranccsal. Itt például egy alkalmazásparancsot mutatunk be egy MS-MPI 7-es verziót használó megoldáshoz:

`cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe`

> [!NOTE]
> Mivel az MS-MPI-k alapértelmezés szerint a változót használják (lásd: Környezeti változók), a fenti példaalkalmazás parancssora `mpiexec.exe` `CCP_NODES` kizárja azt. [](#environment-variables)

## <a name="environment-variables"></a>Környezeti változók

A Batch több, [többpéldányos](batch-compute-node-environment-variables.md) tevékenységekre jellemző környezeti változót hoz létre a többpéldányos tevékenységekhez lefoglalt számítási csomópontokon. A koordináció és az alkalmazás parancssorai hivatkozhatnak ezekre a környezeti változókra, ahogyan az által futtatott szkriptek és programok is.

A Batch szolgáltatás a következő környezeti változókat hozta létre a többpéldányos tevékenységek számára:

- `CCP_NODES`
- `AZ_BATCH_NODE_LIST`
- `AZ_BATCH_HOST_LIST`
- `AZ_BATCH_MASTER_NODE`
- `AZ_BATCH_TASK_SHARED_DIR`
- `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Ezekről és a Batch számítási csomópont egyéb környezeti változóiról, beleértve azok tartalmát és láthatóságát is, lásd: Számítási csomópont környezeti [változói.](batch-compute-node-environment-variables.md)

> [!TIP]
> A [Batch Linux MPI-kódmintája](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi) egy példát tartalmaz arra, hogyan használható több ilyen környezeti változó.

## <a name="resource-files"></a>Erőforrásfájlok

A többpéldányos feladatokhoz két erőforrásfájlkészletet kell figyelembe  **venni:** az összes tevékenység által letöltött  közös erőforrásfájlokat (az elsődleges és  az alfeladatokat egyaránt), valamint a többpéldányos tevékenységhez megadott erőforrásfájlokat, amelyeket csak az elsődleges tevékenység tölt le.

Egy feladat **többpéldányos** beállításaiban megadhat egy vagy több gyakori erőforrásfájlt. Az elsődleges és az összes alfeladat letölti  ezeket a közös erőforrásfájlokat az [Azure Storage-ból](../storage/common/storage-introduction.md) az egyes csomópontok tevékenység megosztott könyvtárába. A tevékenység megosztott könyvtárát az alkalmazás és a koordináció parancssoraiból is elérheti a környezeti `AZ_BATCH_TASK_SHARED_DIR` változó használatával. Az elérési út a többpéldányos tevékenységhez lefoglalt összes csomóponton azonos, így egyetlen koordináló parancsot oszthat meg az elsődleges és az összes `AZ_BATCH_TASK_SHARED_DIR` alfeladat között. A Batch nem "osztja meg" a könyvtárat távelérési értelemben, de használhatja csatlakoztatási vagy megosztási pontként, ahogy azt a környezeti változókról korábban már említettük.

A többpéldányos feladathoz megadott erőforrásfájlok alapértelmezés szerint a tevékenység munkakönyvtárába vannak `AZ_BATCH_TASK_WORKING_DIR` letöltve. Ahogy említettük, a gyakori erőforrásfájlokkal ellentétben csak az elsődleges tevékenység tölt le olyan erőforrásfájlokat, amelyek magára a többpéldányos feladatra vannak megadva.

> [!IMPORTANT]
> Mindig használja a és a környezeti változókat a könyvtárakra való hivatkozáshoz `AZ_BATCH_TASK_SHARED_DIR` `AZ_BATCH_TASK_WORKING_DIR` a parancssorban. Ne próbálja meg manuálisan létrehozni az elérési utakat.

## <a name="task-lifetime"></a>Feladat élettartama

Az elsődleges feladat élettartama a teljes többpéldányos feladat élettartamát szabályozza. Amikor az elsődleges feladat kilép, az összes alfeladat leáll. Az elsődleges elem kilépési kódja a feladat kilépési kódja, ezért újrapróbálkozási célokból a feladat sikeres vagy sikertelen műveletének meghatározására szolgál.

Ha az alfeladatok bármelyike meghiúsul, a kilépés nem nulla visszatérési kóddal történik, például a teljes többpéldányos feladat meghiúsul. A többpéldányos feladat ezután leáll, és újrapróbálkozás a rá vonatkozó újrapróbálkozási korlátig.

Többpéldányos feladat törlésekor a Batch szolgáltatás az elsődleges és az összes alfeladatot is törli. A rendszer az összes alfeladat-könyvtárat és azok fájljait törli a számítási csomópontról, csakúgy, mint a normál tevékenységek.

A többpéldányos feladatok, például a [MaxTaskRetryCount,](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount)a [MaxWallClockTime](/dotnet/api/microsoft.azure.batch.taskconstraints.maxwallclocktime)és a [RetentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) tulajdonságok [taskConstraints](/dotnet/api/microsoft.azure.batch.taskconstraints) tulajdonságát a rendszer normál tevékenységként alkalmazza, és az elsődleges és az összes alfeladatra alkalmazza őket. Ha azonban módosítja aRetentionTime tulajdonságot, miután hozzáadta a többpéldányos tevékenységet a feladathoz, a módosítás csak az elsődleges tevékenységre lesz alkalmazva, és az összes alfeladat továbbra is az eredeti RetentionTime-t használja.

A számítási csomópont legutóbbi tevékenységlistái az alfeladatok azonosítóját tükrözik, ha a legutóbbi feladat egy többpéldányos feladat része volt.

## <a name="obtain-information-about-subtasks"></a>Az alfeladatokkal kapcsolatos információk beszerzése

Az alfeladatokkal kapcsolatos információk Batch .NET-kódtár használatával való beszerzéséhez hívja meg a [CloudTask.ListSubtasks](/dotnet/api/microsoft.azure.batch.cloudtask.listsubtasks) metódust. Ez a metódus információt ad vissza az összes alfeladatról, valamint a tevékenységeket végrehajtott számítási csomópontról. Ezen információk alapján meghatározhatja az egyes alfeladatok gyökérkönyvtárát, a készlet azonosítóját, az aktuális állapotát, a kilépési kódot stb. Ezeket az információkat a [PoolOperations.GetNodeFile](/dotnet/api/microsoft.azure.batch.pooloperations.getnodefile) metódussal együtt használva szerezheti be az alfeladat fájljait. Vegye figyelembe, hogy ez a metódus nem ad vissza információt az elsődleges feladatról (0 azonosító).

> [!NOTE]
> Ha másként nincs kikötve, a többpéldányos [CloudTaskon](/dotnet/api/microsoft.azure.batch.cloudtask) működő Batch .NET-metódusok csak az *elsődleges* tevékenységre vonatkoznak. Ha például a [CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask.listnodefiles) metódust hívja meg egy többpéldányos feladaton, a rendszer csak az elsődleges tevékenység fájljait visszaadja.

Az alábbi kódrészlet bemutatja, hogyan szerezheti be az alfeladatok adatait, és hogyan kérhet le fájltartalmakat a csomópontról, amelyen végrehajtották őket.

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

A [MultiInstanceTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks) kódminta a GitHubon bemutatja, hogyan futtathat [MS-MPI-alkalmazásokat](/message-passing-interface/microsoft-mpi) többpéldányos feladatokkal Batch számítási csomópontokon. A minta futtatásához kövesse az alábbi lépéseket.

### <a name="preparation"></a>Előkészítés

1. Töltse le és telepítse az [MS-MPI SDK- és Redist-telepítőket.](/message-passing-interface/microsoft-mpi) A telepítés után ellenőrizheti, hogy az MS-MPI környezeti változók be vannak-e állítva.
1. Készítse *el az* [MPIHelloWorld minta MPI-program](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld) kiadási verzióját. Ez az a program, amelyet a többpéldányos tevékenység fog futtatni a számítási csomópontokon.
1. Hozzon létre egy zip-fájlt, amely tartalmazza (amelyet a 2. lépésben létrehozott) és (amelyet az `MPIHelloWorld.exe` `MSMpiSetup.exe` 1. lépésben letöltött). Ezt a zip-fájlt a következő lépésben alkalmazáscsomagként fogja feltölteni.
1. A [Azure Portal](https://portal.azure.com) hozzon létre egy [](batch-application-packages.md) "MPIHelloWorld" nevű Batch-alkalmazást, és adja meg az előző lépésben létrehozott zip-fájlt az alkalmazáscsomag 1.0-s verziójaként. További [információ: Alkalmazások feltöltése](batch-application-packages.md#upload-and-manage-applications) és kezelése.

> [!TIP]
> A *kiadási* verziójának kiépítése biztosítja, hogy ne legyen szükség további függőségek (például vagy `MPIHelloWorld.exe` `msvcp140d.dll` ) `vcruntime140d.dll` alkalmazáscsomagba való beépítésére.

### <a name="execution"></a>Futtatási

1. Töltse le [az azure-batch-samples .zip fájlt a](https://github.com/Azure/azure-batch-samples/archive/master.zip) GitHubról.
1. Nyissa meg a MultiInstanceTasks **megoldást** a 2019 Visual Studio ban. A `MultiInstanceTasks.sln` megoldásfájl a következő helyen található:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
1. Adja meg Batch- és Storage-fiókja hitelesítő adatait aMicrosoft.Azure.Bat`AccountSettings.settings` **ch.Samples.Common projektben.**
1. **A** MultiInstanceTasks megoldás összeállításával és futtatásával hajtsa végre az MPI-mintaalkalmazást egy Batch-készlet számítási csomópontjain.
1. *Választható:* A [Azure Portal](https://portal.azure.com) vagy az [Batch Explorer](https://azure.github.io/BatchExplorer/) használatával az erőforrások törlése előtt megvizsgálhatja a mintakészletet, a feladatot és a tevékenységet ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask").

> [!TIP]
> Ha még [nem Visual Studio Community,](https://visualstudio.microsoft.com/vs/community/) ingyenesen letöltheti a Visual Studio.

A `MultiInstanceTasks.exe` kimenete az alábbihoz hasonló:

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

- További információ a Linuxon való [MPI-támogatásról a Azure Batch.](https://docs.microsoft.com/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch)
- Megtudhatja, [hogyan hozhat létre linuxos számítási](batch-linux-nodes.md) csomópontok készleteket az MPI Azure Batch való használatra.
