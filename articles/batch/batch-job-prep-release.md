---
title: Feladatok létrehozása a számítási csomópontokon & teljes feladatok előkészítéséhez
description: Az adatátvitelt az Azure Batch számítási csomópontokra, valamint a feladat befejezésekor a csomópont-karbantartási feladatok kiadására szolgáló feladat-előkészítési feladatokkal csökkentheti.
ms.topic: how-to
ms.date: 02/17/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 5b1084cfdd5995b7983badcdce71460f7bdec3d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88919454"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Feladat-előkészítési és feladat-kiadási feladatok futtatása kötegelt számítási csomópontokon

 Egy Azure Batch feladathoz gyakran szükség van valamilyen beállításra a feladatok végrehajtása előtt, és a feladatok befejezése után a feladat befejezését követően karbantartást kell végezni. Előfordulhat, hogy a feladat befejezése után le kell töltenie az általános feladathoz tartozó bemeneti adatokat a számítási csomópontokra, vagy fel kell töltenie a feladat kimeneti adatait az Azure Storage-ba. Ezeket a műveleteket a **feladat-előkészítési** és a **feladat-kiadási** feladatok használatával végezheti el.

## <a name="what-are-job-preparation-and-release-tasks"></a>Mik a feladat-előkészítési és-kiadási feladatok?
A feladat műveleteinek futtatása előtt a feladat-előkészítési feladat minden olyan számítási csomóponton fut, amely legalább egy feladat futtatására van ütemezve. A feladat befejezése után a feladat kiadására szolgáló tevékenység a készlet minden olyan csomópontján fut, amely legalább egy feladatot végrehajtott. A normál batch-feladatokhoz hasonlóan megadhat egy parancssort is, amelyet a feladat-előkészítés vagy a kiadási feladat futtatásakor kell meghívni.

A feladat-előkészítési és-kiadási feladatok olyan jól ismert batch feladat-funkciókat kínálnak, mint a fájlok letöltése ([erőforrás-fájlok][net_job_prep_resourcefiles]), a megemelt végrehajtás, az egyéni környezeti változók, a maximális végrehajtási időtartam, az újrapróbálkozások száma

A következő szakaszokban megtudhatja, hogyan használhatja a [Batch .net][api_net] -könyvtárban található [JobPreparationTask][net_job_prep] és [jobreleasetask tevékenységtípust][net_job_release] osztályokat.

> [!TIP]
> A feladat-előkészítési és-kiadási feladatok különösen a "megosztott készlet" környezetekben hasznosak, amelyekben a számítási csomópontok készlete továbbra is fennáll a feladat-futtatások között, és számos feladat használja.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Mikor kell használni a feladat-előkészítési és-kiadási feladatokat
A feladat-előkészítési és a feladat-kiadási feladatok a következő helyzetekben megfelelőek:

**Gyakori tevékenységadatok letöltése**

A Batch-feladatokhoz gyakran szükség van egy közös adathalmazra, mint a feladat tevékenységeinek bemenete. A napi kockázatelemzési számításokban például a piaci adatok a feladat-specifikusak, de a feladatban szereplő összes feladathoz is általánosak. Ezt a piaci adatmennyiséget – gyakran több gigabájtos méretet – csak egyszer kell letölteni az egyes számítási csomópontokra, hogy a csomóponton futó összes feladat használhassa azt. Feladat- **előkészítési feladattal** letöltheti ezeket az adatok az egyes csomópontokra a feladat egyéb feladatainak végrehajtása előtt.

**Feladat és feladat kimenetének törlése**

A "megosztott készlet" környezetében, ahol a készlet számítási csomópontjai nem vannak leszerelve a feladatok között, előfordulhat, hogy a futtatások között törölnie kell a feladatait. Előfordulhat, hogy lemezterületet kell biztosítania a csomópontokon, vagy meg kell felelnie a szervezet biztonsági házirendjének. Feladat- **felszabadítási feladat** használatával törölheti a feladat-előkészítési feladattal letöltött vagy a feladat végrehajtása során létrehozott adatmennyiséget.

**Napló megőrzése**

Előfordulhat, hogy meg kívánja őrizni a feladatok által létrehozott naplófájlok másolatát, vagy esetleg összeomlási memóriaképeket, amelyeket a hibás alkalmazások okozhatnak. Ebben az esetben egy **feladat kiadási feladattal** tömörítheti és feltöltheti ezeket az adatok egy [Azure Storage][azure_storage] -fiókba.

> [!TIP]
> A naplók és egyéb feladatok és tevékenységek kimeneti adatainak egy másik módja a [Azure batch file Conventions](batch-task-output.md) Library használata.
>
>

## <a name="job-preparation-task"></a>Feladat-előkészítési feladat


A feladatok végrehajtásának megkezdése előtt a Batch végrehajtja a feladat-előkészítési feladatot a feladatok futtatására ütemezett összes számítási csomóponton. Alapértelmezés szerint a Batch megvárja a feladat-előkészítési feladat befejeződését, mielőtt futtatja a csomóponton végrehajtott végrehajtásra ütemezett feladatokat. Azonban beállíthatja, hogy a szolgáltatás ne várjon. Ha a csomópont újraindul, a feladat-előkészítési feladat újra lefut. Ezt a viselkedést is letilthatja. Ha a feladat egy feladat-előkészítési feladattal és egy Feladatkezelő feladattal van konfigurálva, a feladat-előkészítési feladat a Feladatkezelő feladat előtt fut, ugyanúgy, mint az összes többi feladathoz. A feladat-előkészítési feladat mindig először fut.

A feladat-előkészítési feladat csak a feladat futtatására ütemezett csomópontokon hajtható végre. Ez megakadályozza az előkészítési feladatok szükségtelen végrehajtását abban az esetben, ha egy csomóponthoz nincs hozzárendelve feladat. Ez akkor fordulhat elő, ha egy adott feladathoz tartozó tevékenységek száma kisebb, mint a készletben lévő csomópontok száma. Ez akkor is érvényes, ha az [egyidejű feladat-végrehajtás](batch-parallel-node-tasks.md) engedélyezve van, így a csomópontok üresjáratban maradnak, ha a feladatok száma nem éri el az összes lehetséges egyidejű feladatot. Ha nem futtatja a feladat-előkészítési feladatot az üresjárati csomópontokon, kevesebb pénzt tölthet fel az adatátviteli költségekkel.

> [!NOTE]
> A [JobPreparationTask][net_job_prep_cloudjob] eltér a [CloudPool. StartTask][pool_starttask] , amely az adott JobPreparationTask az egyes feladatok elején hajtja végre, míg a StartTask csak akkor fut le, ha egy számítási csomópont először csatlakozik egy készlethez vagy újraindításhoz.
>


>## <a name="job-release-task"></a>Feladat kiadási feladata

Ha egy feladat befejezettként van megjelölve, a feladat kiadási feladata a készlet minden olyan csomópontján fut, amely legalább egy feladatot végrehajt. A feladatot befejezettként kell megjelölni egy megszakítási kérelem kiállításával. A Batch szolgáltatás ezután a feladat állapotát leállítja, leállítja a feladathoz társított aktív vagy futó *feladatokat, és* futtatja a feladat kiadási feladatát. A feladatot ezután a *befejezett* állapotba helyezi.

> [!NOTE]
> A feladat törlése a feladat kiadási feladatát is végrehajtja. Ha azonban egy feladat már meg van szakítva, a kiadási feladat nem fut le Másodszor, ha a feladat később törlődik.

A feladatok kiadási feladatai legfeljebb 15 percig futhatnak, mielőtt a Batch szolgáltatás leállította. További információ: [REST API dokumentáció](/rest/api/batchservice/job/add#jobreleasetask).
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Feladat-előkészítési és-kiadási feladatok a Batch .NET-tel
Feladat-előkészítési feladat használatához rendeljen egy [JobPreparationTask][net_job_prep] -objektumot a feladat [CloudJob. JobPreparationTask][net_job_prep_cloudjob] tulajdonságához. Hasonlóképpen inicializáljon egy [jobreleasetask tevékenységtípust][net_job_release] , és rendelje hozzá a feladat [CloudJob. jobreleasetask tevékenységtípust][net_job_prep_cloudjob] tulajdonságához a feladat kiadási feladatának beállításához.

Ebben a kódrészletben a `myBatchClient` [BatchClient][net_batch_client]egy példánya, amely `myPool` egy meglévő készlet a Batch-fiókon belül.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Ahogy azt korábban említettük, a kiadási feladat a feladat leállításakor vagy törlésekor kerül végrehajtásra. Leállít egy feladatot a [JobOperations. TerminateJobAsync][net_job_terminate]. Törölje a [JobOperations. DeleteJobAsync][net_job_delete]-mel rendelkező feladatot. Általában leáll vagy töröl egy feladatot, amikor a feladatai befejeződtek, vagy ha elérte az időtúllépést.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Mintakód a GitHubon
A feladat-előkészítési és-kiadási feladatok működés közbeni megtekintéséhez tekintse meg a [JobPrepRelease][job_prep_release_sample] -minta projektet a githubon. Ez a konzolszoftver a következő műveleteket végzi el:

1. Két csomóponttal rendelkező készletet hoz létre.
2. Feladat-előkészítési, kiadási és általános feladatokkal rendelkező feladatot hoz létre.
3. Futtatja a feladat-előkészítési feladatot, amely először a csomópont-azonosítót írja a csomópont "Shared" könyvtárában lévő szövegfájlba.
4. Futtat egy feladatot minden olyan csomóponton, amely ugyanazt a szövegfájlba írja a feladat AZONOSÍTÓját.
5. Ha az összes feladat befejeződött (vagy elérte az időtúllépést), az egyes csomópontok szövegfájljának tartalmát kinyomtatja a konzolra.
6. A feladat befejezésekor futtatja a feladat kiadása feladatot a fájl törléséhez a csomópontból.
7. Kiírja a feladat-előkészítési és a kiadási feladatok kilépési kódjait minden olyan csomópontnál, amelyen azokat végrehajtják.
8. Szünetelteti a végrehajtást, hogy erősítse a feladat és/vagy a készlet törlését.

A minta alkalmazás kimenete a következőhöz hasonló:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> Egy új készletben lévő csomópontok létrehozásának és kezdési idejének (egyes csomópontok, mielőtt mások elvégezték a feladatokat), eltérő kimenet jelenhet meg. Különösen azért, mert a feladatok gyorsan fejeződnek be, a készlet egyik csomópontja is végrehajthatja a feladat összes feladatát. Ha ez történik, megfigyelheti, hogy a feladat-előkészítési és-kiadási feladatok nem léteznek olyan csomóponthoz, amely nem végez feladatokat.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>A feladat-előkészítési és-kiadási feladatok vizsgálata a Azure Portal
A minta alkalmazás futtatásakor a [Azure Portal][portal] használatával megtekintheti a feladat tulajdonságait és a feladatait, vagy akár le is töltheti a feladat tevékenységei által módosított megosztott szövegfájlt.

Az alábbi képernyőképen az **előkészítési feladatok** panel látható a Azure Portal a minta alkalmazás futtatása után. Navigáljon a *JobPrepReleaseSampleJob* tulajdonságaihoz a feladatok befejeződése után (de a feladat és a készlet törlése előtt), majd kattintson az **előkészítési feladatok** vagy a **kiadási feladatok** elemre a tulajdonságok megtekintéséhez.

![A Azure Portal feladatok előkészítésének tulajdonságai][1]

## <a name="next-steps"></a>Következő lépések
### <a name="application-packages"></a>Alkalmazáscsomagok
A feladat-előkészítési feladaton kívül a Batch [alkalmazáscsomag](batch-application-packages.md) szolgáltatásával is előkészítheti a számítási csomópontokat a feladatok végrehajtásához. Ez a funkció különösen hasznos olyan alkalmazások telepítéséhez, amelyek nem igénylik a telepítő futtatását, a sok (100 +) fájlt tartalmazó alkalmazásokat, illetve a szigorú verziókövetés használatát igénylő alkalmazásokat.

### <a name="installing-applications-and-staging-data"></a>Alkalmazások telepítése és az előkészítési adatgyűjtés
Az MSDN fórum ezen bejegyzése áttekintést nyújt a csomópontok futtatására szolgáló különféle módszerekről:

[Alkalmazások telepítése és előkészítési adatok kötegelt számítási csomópontokon][forum_post]

A Azure Batch csapat tagjainak egyike írta, és számos olyan technikát ismertet, amelyekkel alkalmazásokat és adatok üzembe helyezését végezheti el a számítási csomópontokon.

[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: /dotnet/api/microsoft.azure.batch.jobpreparationtask
[net_job_prep_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_job_prep_resourcefiles]: /dotnet/api/microsoft.azure.batch.jobpreparationtask
[net_job_delete]: /previous-versions/azure/mt281411(v=azure.100)
[net_job_terminate]: /previous-versions/azure/mt188985(v=azure.100)
[net_job_release]: /dotnet/api/microsoft.azure.batch.jobreleasetask
[net_job_release_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[pool_starttask]: /dotnet/api/microsoft.azure.batch.cloudpool

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
