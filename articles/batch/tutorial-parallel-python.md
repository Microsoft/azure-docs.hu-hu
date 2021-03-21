---
title: Oktatóanyag – párhuzamos számítási feladatok futtatása a Python API használatával
description: Oktatóanyag – Médiafájlok párhuzamos feldolgozása ffmpeg segítségével az Azure Batchben a Batch Python ügyfélkódtár használatával
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2018
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7013ef1e9b6dda4554b665fa8199edb86a8a9adb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106512"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-python-api"></a>Oktatóanyag: Párhuzamos számításifeladat-futtatás az Azure Batchben a Python API használatával

Az Azure Batch használatával hatékonyan futtathat nagy méretű párhuzamos és nagy teljesítményű feldolgozási (high-performance computing, HPC) Batch-feladatokat az Azure-ban. Ez az oktatóanyag végigvezeti egy, a Batch segítségével párhuzamos számításifeladat-futtatást bemutató Python-példán. Megismerheti a Batch-alkalmazások általános munkafolyamatát, valamint azt, hogyan kommunikálhat programkódon keresztül a Batch- és Storage-erőforrásokkal. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hitelesítés Batch- és Storage-fiókokkal
> * Bemeneti fájlok feltöltése a Storage-ba
> * Számításicsomópont-készlet létrehozása alkalmazás futtatásához
> * Feladatok és tevékenységek létrehozása bemeneti fájlok feldolgozásához
> * Tevékenységek végrehajtásának figyelése
> * Kimeneti fájlok lekérése

Ebben az oktatóanyagban MP4-médiafájlokat konvertál párhuzamosan MP3 formátumba az [ffmpeg](https://ffmpeg.org/) nyílt forráskódú eszköz segítségével. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [A Python 2.7-es, 3.3-as vagy újabb verziója](https://www.python.org/downloads/)

* [pip](https://pip.pypa.io/en/stable/installing/) Package Manager

* Egy Azure Batch-fiók és egy társított Azure Storage-fiók. A fiókok létrehozásához tekintse meg a Batch az [Azure Portallal](quick-create-portal.md) vagy az [Azure CLI-vel](quick-create-cli.md) történő használatát ismertető rövid útmutatókat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 

## <a name="download-and-run-the-sample"></a>A minta letöltése és futtatása

### <a name="download-the-sample"></a>A minta letöltése

[Töltse le vagy klónozza a mintaalkalmazást](https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial) a GitHubról. A mintaalkalmazás adattárának Git-ügyféllel történő klónozásához használja az alábbi parancsot:

```
git clone https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial.git
```

Váltson a `batch_python_tutorial_ffmpeg.py` fájlt tartalmazó könyvtárra.

A Python-környezetében telepítse a szükséges csomagokat a `pip` segítségével.

```bash
pip install -r requirements.txt
```

Nyissa meg a `config.py` fájlt. Frissítse a Batch- és Storage-fiók hitelesítési sztringjét a fiókjaira jellemző egyedi értékekkel. Például:


```Python
_BATCH_ACCOUNT_NAME = 'mybatchaccount'
_BATCH_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ=='
_BATCH_ACCOUNT_URL = 'https://mybatchaccount.mybatchregion.batch.azure.com'
_STORAGE_ACCOUNT_NAME = 'mystorageaccount'
_STORAGE_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=='
```

### <a name="run-the-app"></a>Az alkalmazás futtatása

A szkript futtatása:

```
python batch_python_tutorial_ffmpeg.py
```

A mintaalkalmazás futtatásakor a konzol kimenete az alábbihoz hasonló lesz. A futtatás során szünet jelentkezhet a következőnél a készlet számítási csomópontjainak indításakor: `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...`. 
   
```
Sample start: 11/28/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [LinuxFFmpegPool]...
Creating job [LinuxFFmpegJob]...
Adding 5 tasks to job [LinuxFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]....

Sample end: 11/28/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

A készlet, a számítási csomópontok, a feladat és a tevékenységek figyeléséhez lépjen az Azure Portalon a Batch-fiókjába. Ha például meg szeretné tekinteni a készletben lévő számítási csomópontok Heat térképét, kattintson a **készletek**  >  *LinuxFFmpegPool* elemre.

A tevékenységek futásakor a hőtérkép az alábbihoz hasonló:

![Készlet hőtérképe](./media/tutorial-parallel-python/pool.png)

A jellemző végrehajtási idő körülbelül **5 perc** , ha az alapértelmezett konfigurációban futtatja az alkalmazást. A készlet létrehozása veszi igénybe a legtöbb időt. 

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>A kód áttekintése

A következő szakaszok a mintaalkalmazást felosztják azokra a lépésekre, amelyeket az alkalmazás végrehajt a számítási feladatok a Batch szolgáltatásban történő feldolgozásához. A cikk további részének olvasása közben használja a Python-kódot, mert a minta nem minden kódsoráról esik szó a cikkben.

### <a name="authenticate-blob-and-batch-clients"></a>Blob- és Batch-ügyfelek hitelesítése

Az alkalmazás az [azure-storage-blob](https://pypi.python.org/pypi/azure-storage-blob) csomag használatával létrehozott [BlockBlobService](/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice) objektum segítségével kommunikál a Storage-fiókkal.

```python
blob_client = azureblob.BlockBlobService(
    account_name=_STORAGE_ACCOUNT_NAME,
    account_key=_STORAGE_ACCOUNT_KEY)
```

Az alkalmazás létrehoz egy [BatchServiceClient](/python/api/azure.batch.batchserviceclient) objektumot a Batch szolgáltatásban lévő készletek, feladatok és tevékenységek létrehozásához és kezeléséhez. A példákban szereplő Batch-ügyfél megosztott kulcsos hitelesítést használ. A Batch az [Azure Active Directory](batch-aad-auth.md) segítségével történő hitelesítést is támogatja az egyéni felhasználók vagy a felügyelet nélküli alkalmazások hitelesítéséhez.

```python
credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                             _BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=_BATCH_ACCOUNT_URL)
```

### <a name="upload-input-files"></a>Bemeneti fájlok feltöltése

Az alkalmazás a `blob_client` hivatkozás segítségével hoz létre egy Storage-tárolót a bemeneti MP4-fájlokhoz, valamint egy tárolót a tevékenység kimenetének. Ezután az `upload_file_to_container` meghívásával feltölti a helyi `InputFiles` mappában lévő MP4-fájlokat a tárolóba. A tárolóban lévő fájlokat a rendszer a Batch által később a számítási csomópontra letölthető Batch [ResourceFile](/python/api/azure-batch/azure.batch.models.resourcefile)-objektumként határozza meg.

```python
blob_client.create_container(input_container_name, fail_on_exist=False)
blob_client.create_container(output_container_name, fail_on_exist=False)
input_file_paths = []

for folder, subs, files in os.walk(os.path.join(sys.path[0], './InputFiles/')):
    for filename in files:
        if filename.endswith(".mp4"):
            input_file_paths.append(os.path.abspath(
                os.path.join(folder, filename)))

# Upload the input files. This is the collection of files that are to be processed by the tasks.
input_files = [
    upload_file_to_container(blob_client, input_container_name, file_path)
    for file_path in input_file_paths]
```

### <a name="create-a-pool-of-compute-nodes"></a>Számításicsomópont-készlet létrehozása

A következő lépésben a minta létrehozza a számítási csomópontok készletét a Batch-fiókban a `create_pool` hívásával. Ez a meghatározott funkció a Batch [PoolAddParameter](/python/api/azure-batch/azure.batch.models.pooladdparameter) osztályával adja meg a csomópontok számát, a virtuális gép méretét és a készletkonfigurációt. Itt egy [VirtualMachineConfiguration](/python/api/azure-batch/azure.batch.models.virtualmachineconfiguration) objektum az Azure Marketplace-en közzétett Ubuntu Server 18,04 LTS-rendszerképhez [ImageReference](/python/api/azure-batch/azure.batch.models.imagereference) ad meg. A Batch az Azure Marketplace virtuálisgép-rendszerképeinek széles választékát támogatja, de egyéni rendszerképeket is használhat.

A csomópontok száma és a virtuális gépek mérete meghatározott állandókkal van megadva. A Batch támogatja a dedikált csomópontokat és az [alacsony prioritású csomópontokat](batch-low-pri-vms.md), és a készletekben akár mindkettőt is használhatja. A dedikált csomópontok a készlet számára vannak fenntartva. Az alacsony prioritású csomópontok kedvezményes áron érhetők el az Azure többlet VM-kapacitásából. Ha az Azure nem rendelkezik elegendő kapacitással, az alacsony prioritású csomópontok elérhetetlenné válnak. A minta alapértelmezés szerint egy csupán 5 alacsony prioritású, *Standard_A1_v2* méretű csomópontot tartalmazó készletet hoz létre. 

A fizikai csomópont tulajdonságain felül ez a készletkonfiguráció tartalmaz egy [StartTask](/python/api/azure-batch/azure.batch.models.starttask) objektumot. A StartTask mindegyik csomóponton fut, amikor a csomópont csatlakozik a készlethez, vagy amikor újraindul. Ebben a példában a StartTask Bash-rendszerhéjparancsokat futtat az ffmpeg csomag és a függőségek csomópontokon való telepítéséhez.

A [pool.add](/python/api/azure-batch/azure.batch.operations.pooloperations) metódus elküldi a készletet a Batch szolgáltatásnak.

```python
new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="18.04-LTS",
            version="latest"
        ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_DEDICATED_POOL_NODE_COUNT,
    target_low_priority_nodes=_LOW_PRIORITY_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install -y ffmpeg\"",
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(new_pool)
```

### <a name="create-a-job"></a>Feladat létrehozása

Egy Batch-feladat meghatároz egy készletet, amelyen futtathatók tevékenységek, valamint opcionális beállításokat, például a prioritást és az ütemezést a munkához. A minta a `create_job` hívásával létrehoz egy feladatot. Ez a meghatározott függvény a [JobAddParameter](/python/api/azure-batch/azure.batch.models.jobaddparameter) osztály használatával létrehoz egy feladatot a készleten. A [job.add](/python/api/azure-batch/azure.batch.operations.joboperations) metódus elküldi a készletet a Batch szolgáltatásnak. A feladat kezdetben nem tartalmaz tevékenységeket.

```python
job = batch.models.JobAddParameter(
    id=job_id,
    pool_info=batch.models.PoolInformation(pool_id=pool_id))

batch_service_client.job.add(job)
```

### <a name="create-tasks"></a>Tevékenységek létrehozása

Az alkalmazás az `add_tasks` meghívásával hoz létre tevékenységeket a feladatban. Ez a megadott függvény létrehoz egy listát a tevékenységobjektumokról a [TaskAddParameter](/python/api/azure-batch/azure.batch.models.taskaddparameter) osztály segítségével. Minden feladat az ffmpeg futtatásával dolgoz fel egy bemeneti `resource_files`-objektumot egy `command_line` paraméter segítségével. Az ffmpeg már korábban, a készlet létrehozásakor telepítve lett minden egyes csomóponton. Itt a parancssor az ffmpeg futtatásával konvertálja az egyes bemeneti MP4-videofájlokat MP3-hangfájllá.

A minta a parancssor futtatása után létrehoz egy [OutputFile](/python/api/azure-batch/azure.batch.models.outputfile) objektumot az MP3-fájlhoz. A rendszer az összes tevékenység kimeneti fájlját (ebben az esetben egyet) feltölti egy, a társított Storage-fiókban lévő tárolóba a tevékenység `output_files` tulajdonsága segítségével.

Ezt követően az alkalmazás tevékenységeket ad a feladathoz a [task.add_colection](/python/api/azure-batch/azure.batch.operations.taskoperations) metódussal, amely várólistára helyezi azokat a számítási csomópontokon való futtatáshoz. 

```python
tasks = list()

for idx, input_file in enumerate(input_files):
    input_file_path = input_file.file_path
    output_file_path = "".join((input_file_path).split('.')[:-1]) + '.mp3'
    command = "/bin/bash -c \"ffmpeg -i {} {} \"".format(
        input_file_path, output_file_path)
    tasks.append(batch.models.TaskAddParameter(
        id='Task{}'.format(idx),
        command_line=command,
        resource_files=[input_file],
        output_files=[batchmodels.OutputFile(
            file_pattern=output_file_path,
            destination=batchmodels.OutputFileDestination(
                container=batchmodels.OutputFileBlobContainerDestination(
                    container_url=output_container_sas_url)),
            upload_options=batchmodels.OutputFileUploadOptions(
                upload_condition=batchmodels.OutputFileUploadCondition.task_success))]
    )
    )
batch_service_client.task.add_collection(job_id, tasks)
```

### <a name="monitor-tasks"></a>Tevékenységek figyelése

Amikor az alkalmazás tevékenységeket ad egy feladathoz, a Batch automatikusan várólistára helyezi és ütemezi azok végrehajtását a társított készlet számítási csomópontjain. A Batch a megadott beállítások alapján elvégzi a tevékenységek várólistára helyezésével, ütemezésével, újrapróbálásával kapcsolatos összes feladatot, valamint a tevékenységekhez kapcsolódó egyéb rendszergazdai teendőket. 

A tevékenységek végrehajtása sokféleképpen megfigyelhető. A példában szereplő `wait_for_tasks_to_complete` funkció a [TaskState](/python/api/azure-batch/azure.batch.models.taskstate) objektum segítségével figyel tevékenységeket egy adott (ebben az esetben a befejezett) állapotra vonatkozóan megadott időkorláton belül.

```python
while datetime.datetime.now() < timeout_expiration:
    print('.', end='')
    sys.stdout.flush()
    tasks = batch_service_client.task.list(job_id)

    incomplete_tasks = [task for task in tasks if
                        task.state != batchmodels.TaskState.completed]
    if not incomplete_tasks:
        print()
        return True
    else:
        time.sleep(1)
...
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tevékenységek futtatása után az alkalmazás automatikusan törli a létrehozott bemeneti Storage-tárolót, és felkínálja a Batch-készlet és -feladat törlésének lehetőségét. A BatchClient [JobOperations](/python/api/azure-batch/azure.batch.operations.joboperations) és [PoolOperations](/python/api/azure-batch/azure.batch.operations.pooloperations) osztálya is rendelkezik törlési metódusokkal, amelyeket a rendszer meghív, ha megerősíti a törlést. Bár magukért a feladatokért és tevékenységekért nem kell fizetnie, a számítási csomópontokért igen. Ezért ajánlott csak szükség szerint lefoglalni a készleteket. A készlet törlésekor a rendszer a csomópont összes tevékenységének kimenetét is törli. A bemeneti és kimeneti fájlok azonban megmaradnak a Storage-fiókban.

Ha már nincs rájuk szükség, törölje az erőforráscsoportot, a Batch-fiókot és a Storage-fiókot. Ehhez az Azure Portalon válassza ki a Batch-fiókhoz tartozó erőforráscsoportot, és kattintson az **Erőforráscsoport törlése** elemre.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hitelesítés Batch- és Storage-fiókokkal
> * Bemeneti fájlok feltöltése a Storage-ba
> * Számításicsomópont-készlet létrehozása alkalmazás futtatásához
> * Feladatok és tevékenységek létrehozása bemeneti fájlok feldolgozásához
> * Tevékenységek végrehajtásának figyelése
> * Kimeneti fájlok lekérése

Batch-számításifeladatok Python API használatával történő ütemezésére és feldolgozására a GitHub mintáiban találhat további példákat.

> [!div class="nextstepaction"]
> [A Batch Python-mintái](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch)

