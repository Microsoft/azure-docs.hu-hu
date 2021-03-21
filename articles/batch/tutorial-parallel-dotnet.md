---
title: Oktatóanyag – párhuzamos számítási feladatok futtatása a .NET API használatával
description: Oktatóanyag – Médiafájlok párhuzamos átkódolása ffmpeg segítségével az Azure Batchben a Batch .NET ügyfélkódtár használatával
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/29/2020
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: a990a5480a8a6462bb6ef9f84070b78768628fd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106529"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-net-api"></a>Oktatóanyag: Párhuzamos számításifeladat-futtatás az Azure Batchben a .NET API használatával

Az Azure Batch használatával hatékonyan futtathat nagy méretű párhuzamos és nagy teljesítményű feldolgozási (high-performance computing, HPC) Batch-feladatokat az Azure-ban. Ez az oktatóanyag végigvezeti egy, a Batch segítségével párhuzamos számításifeladat-futtatást bemutató C#-példán. Megismerheti a Batch-alkalmazások általános munkafolyamatát, valamint azt, hogyan kommunikálhat programkódon keresztül a Batch- és Storage-erőforrásokkal. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Alkalmazáscsomag hozzáadása a Batch-fiókhoz
> * Hitelesítés Batch- és Storage-fiókokkal
> * Bemeneti fájlok feltöltése a Storage-ba
> * Számításicsomópont-készlet létrehozása alkalmazás futtatásához
> * Feladatok és tevékenységek létrehozása bemeneti fájlok feldolgozásához
> * Tevékenységek végrehajtásának figyelése
> * Kimeneti fájlok lekérése

Ebben az oktatóanyagban MP4-médiafájlokat konvertál párhuzamosan MP3 formátumba az [ffmpeg](https://ffmpeg.org/) nyílt forráskódú eszköz segítségével. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2017 vagy újabb](https://www.visualstudio.com/vs), vagy [.net Core 2,1](https://www.microsoft.com/net/download/dotnet-core/2.1) Linux, MacOS vagy Windows rendszerhez.

* Egy Batch-fiók és egy társított Azure Storage-fiók. A fiókok létrehozásához tekintse meg a Batch az [Azure Portallal](quick-create-portal.md) vagy az [Azure CLI-vel](quick-create-cli.md) történő használatát ismertető rövid útmutatókat.

* [Az FFmpeg 4.3.1 Windows 64 bites verziója](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) (. zip). Töltse le a .zip-fájlt a helyi számítógépére. Ebben az oktatóanyagban csak a zip-fájlra van szükség. A fájlt nem kell sem kibontania, sem helyileg telepítenie.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="add-an-application-package"></a>Alkalmazáscsomag hozzáadása

Adja hozzá az Azure Portal segítségével az ffmpeg-et a Batch-fiókjához [alkalmazáscsomagként](batch-application-packages.md). Az alkalmazáscsomagok segítenek a tevékenységalkalmazások kezelésében, valamint a készlet számítási csomópontjain való üzembe helyezésükben. 

1. A Azure Portal kattintson a **További szolgáltatások**  >  **Batch-fiókok** elemre, majd kattintson a Batch-fiókja nevére.
3. Kattintson az **alkalmazások**  >  **Hozzáadás** gombra.
4. Az **alkalmazás azonosítója** mezőben adja meg az *FFmpeg* értéket, valamint a *4.3.1* csomag verzióját. Válassza ki a korábban letöltött ffmpeg zip-fájlt, és kattintson az **OK** gombra. Ezzel hozzáadta az ffmpeg alkalmazáscsomagját a Batch-fiókjához.

![Alkalmazáscsomag hozzáadása](./media/tutorial-parallel-dotnet/add-application.png)

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-and-run-the-sample"></a>A minta letöltése és futtatása

### <a name="download-the-sample"></a>A minta letöltése

[Töltse le vagy klónozza a mintaalkalmazást](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) a GitHubról. A mintaalkalmazás adattárának Git-ügyféllel történő klónozásához használja az alábbi parancsot:

```
git clone https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial.git
```

Váltson a Visual Studio `BatchDotNetFfmpegTutorial.sln` nevű megoldásfájlját tartalmazó könyvtárra.

Nyissa meg a megoldásfájlt a Visual Studióban, és frissítse a `Program.cs` hitelesítő adatait a fiókokhoz beszerzett értékekkel. Például:

```csharp
// Batch account credentials
private const string BatchAccountName = "mybatchaccount";
private const string BatchAccountKey  = "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==";
private const string BatchAccountUrl  = "https://mybatchaccount.mybatchregion.batch.azure.com";

// Storage account credentials
private const string StorageAccountName = "mystorageaccount";
private const string StorageAccountKey  = "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ==";
```

[!INCLUDE [batch-credentials-include](../../includes/batch-credentials-include.md)]

Győződjön meg arról is, hogy az ffmpeg-alkalmazáscsomag a megoldásban szereplő hivatkozásában szereplő azonosító és verzió megfelel a Batch-fiókjába feltöltött ffmpeg-csomagéval.

```csharp
const string appPackageId = "ffmpeg";
const string appPackageVersion = "4.3.1";
```

### <a name="build-and-run-the-sample-project"></a>A mintaprojekt létrehozása és futtatása

Hozza létre és futtassa az alkalmazást a Visual Studióban vagy a parancssorban a `dotnet build` és a `dotnet run` paranccsal. Az alkalmazás futtatása után tekintse át a kódot annak megismerése érdekében, hogy mit csinálnak az alkalmazás egyes részei. Ha például a Visual Studióban:

* Kattintson a jobb gombbal a megoldásra Megoldáskezelő majd kattintson a **megoldás létrehozása** lehetőségre. 

* Erősítse meg a NuGet-csomagok visszaállítását, ha a rendszer erre kéri. Ha hiányzó csomagokat kell letöltenie, győződjön meg arról, hogy a [NuGet-csomagkezelő](https://docs.nuget.org/consume/installing-nuget) telepítve van.

Ezután futtassa azt. A mintaalkalmazás futtatásakor a konzol kimenete az alábbihoz hasonló lesz. A futtatás során szünet jelentkezhet a következőnél a készlet számítási csomópontjainak indításakor: `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...`. 

```
Sample start: 11/19/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [WinFFmpegPool]...
Creating job [WinFFmpegJob]...
Adding 5 tasks to job [WinFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]...

Sample end: 11/19/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

A készlet, a számítási csomópontok, a feladat és a tevékenységek figyeléséhez lépjen az Azure Portalon a Batch-fiókjába. Ha például meg szeretné tekinteni a készletben lévő számítási csomópontok Heat térképét, kattintson a **készletek**  >  *WinFFmpegPool* elemre.

A tevékenységek futásakor a hőtérkép az alábbihoz hasonló:

![Készlet hőtérképe](./media/tutorial-parallel-dotnet/pool.png)

A jellemző végrehajtási idő körülbelül **10 perc**, ha az alapértelmezett konfigurációban futtatja az alkalmazást. A készlet létrehozása veszi igénybe a legtöbb időt.

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>A kód áttekintése

A következő szakaszok a mintaalkalmazást felosztják azokra a lépésekre, amelyeket az alkalmazás végrehajt a számítási feladatok a Batch szolgáltatásban történő feldolgozásához. A cikk további részének olvasása közben használja a megoldásban lévő `Program.cs` fájlt, mert a minta nem minden kódsoráról esik szó a cikkben.

### <a name="authenticate-blob-and-batch-clients"></a>Blob- és Batch-ügyfelek hitelesítése

A társított Storage-fiókkal való kommunikációhoz az alkalmazás a .NET-hez készült Azure Storage ügyféloldali kódtárat használja. Ez létrehoz egy hivatkozást a fiókra egy [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) objektummal, és ehhez megosztott kulcsos hitelesítést használ. Ezután létrehoz egy [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient) objektumot.

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                                StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Az alkalmazás létrehoz egy [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) objektumot a Batch szolgáltatásban lévő készletek, feladatok és tevékenységek létrehozásához és kezeléséhez. A példákban szereplő Batch-ügyfél megosztott kulcsos hitelesítést használ. A Batch a [Azure Active Directoryon](batch-aad-auth.md) keresztüli hitelesítést is támogatja az egyes felhasználók vagy a felügyelet nélküli alkalmazások hitelesítéséhez.

```csharp
BatchSharedKeyCredentials sharedKeyCredentials = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(sharedKeyCredentials))
...
```

### <a name="upload-input-files"></a>Bemeneti fájlok feltöltése

Az alkalmazás továbbítja a `CreateContainerIfNotExistAsync` objektumot a `blobClient` metódusnak, hogy az létrehozzon egy Storage-tárolót a bemeneti MP4-fájlokhoz, valamint egy tárolót a tevékenység kimenetének.

```csharp
CreateContainerIfNotExistAsync(blobClient, inputContainerName);
CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

Ezt követően a rendszer feltölti a fájlokat a bemeneti tárolóba a helyi `InputFiles` mappából. A tárolóban lévő fájlokat a rendszer a Batch által később a számítási csomópontra letölthető Batch [ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile)-objektumként határozza meg. 

A `Program.cs` két metódusa vesz részt a fájlok feltöltésében:

* `UploadFilesToContainerAsync`: A ResourceFile-objektumok gyűjteményét adja vissza, és belsőleg meghívja az `UploadResourceFileToContainerAsync` metódust, hogy feltöltse a `inputFilePaths` paraméterben átadott fájlokat.
* `UploadResourceFileToContainerAsync`: Minden fájlt blobként tölt fel a bemeneti tárolóba. A fájl feltöltése után közös hozzáférésű jogosultságkódot (SAS) szerez be a blobhoz, és visszaadja az azt jelölő ResourceFile-objektumot.

```csharp
string inputPath = Path.Combine(Environment.CurrentDirectory, "InputFiles");

List<string> inputFilePaths = new List<string>(Directory.GetFileSystemEntries(inputPath, "*.mp4",
    SearchOption.TopDirectoryOnly));

List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
  blobClient,
  inputContainerName,
  inputFilePaths);
```

További részleteket a fájlok egy Storage-fiókba a .NET segítségével blobként történő feltöltéséről a [blobok .NET segítségével való feltöltését, letöltését és listázását](../storage/blobs/storage-quickstart-blobs-dotnet.md) ismertető cikkben talál.

### <a name="create-a-pool-of-compute-nodes"></a>Számításicsomópont-készlet létrehozása

A következő lépésben a minta létrehozza a számítási csomópontok készletét a Batch-fiókban a `CreatePoolIfNotExistAsync` hívásával. Ez a meghatározott metódus a [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool) metódussal adja meg a csomópontok számát, a virtuális gép méretét és a készletkonfigurációt. Itt egy [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) objektum megad egy [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) objektumot egy, az Azure Marketplace-en közzétett Windows Server-rendszerképhez. A Batch az Azure Marketplace virtuálisgép-rendszerképeinek széles választékát támogatja, de egyéni rendszerképeket is használhat.

A csomópontok száma és a virtuális gépek mérete meghatározott állandókkal van megadva. A Batch támogatja a dedikált csomópontokat és az [alacsony prioritású csomópontokat](batch-low-pri-vms.md), és a készletekben akár mindkettőt is használhatja. A dedikált csomópontok a készlet számára vannak fenntartva. Az alacsony prioritású csomópontok kedvezményes áron érhetők el az Azure többlet VM-kapacitásából. Ha az Azure nem rendelkezik elegendő kapacitással, az alacsony prioritású csomópontok elérhetetlenné válnak. A minta alapértelmezés szerint egy csupán 5 alacsony prioritású, *Standard_A1_v2* méretű csomópontot tartalmazó készletet hoz létre.

>[!Note]
>Ügyeljen rá, hogy ellenőrizze a csomópontok kvótáit. A kvóta-kérelem létrehozásával kapcsolatos utasításokért tekintse meg a [Batch szolgáltatás kvótái és korlátai](batch-quota-limit.md#increase-a-quota) című témakört.

Az ffmpeg alkalmazás a számítási csomópontokon egy [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) a készletkonfigurációhoz történő hozzáadásával lesz telepítve.

A [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync) metódus elküldi a készletet a Batch szolgáltatásnak.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-smalldisk",
    version: "latest");

VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.windows amd64");

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: DedicatedNodeCount,
    targetLowPriorityComputeNodes: LowPriorityNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

pool.ApplicationPackageReferences = new List<ApplicationPackageReference>
    {
    new ApplicationPackageReference {
    ApplicationId = appPackageId,
    Version = appPackageVersion}};

await pool.CommitAsync();  
```

### <a name="create-a-job"></a>Feladat létrehozása

Egy Batch-feladat meghatároz egy készletet, amelyen futtathatók tevékenységek, valamint opcionális beállításokat, például a prioritást és az ütemezést a munkához. A minta a `CreateJobAsync` hívásával létrehoz egy feladatot. Ez a meghatározott metódus a [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob) metódussal hoz létre egy feladatot a készleten.

A [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudjob.commitasync) metódus elküldi a feladatot a Batch szolgáltatásnak. A feladat kezdetben nem tartalmaz tevékenységeket.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob();
job.Id = JobId;
job.PoolInformation = new PoolInformation { PoolId = PoolId };

await job.CommitAsync();
```

### <a name="create-tasks"></a>Tevékenységek létrehozása

A minta tevékenységeket hoz létre a feladatban az `AddTasksAsync` metódus meghívásával, amely létrehoz egy listát a [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask)-objektumokról. Minden `CloudTask` az ffmpeg futtatásával dolgoz fel egy bemeneti `ResourceFile`-objektumot egy [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline) tulajdonság segítségével. Az ffmpeg már korábban, a készlet létrehozásakor telepítve lett minden egyes csomóponton. Itt a parancssor az ffmpeg futtatásával konvertálja az egyes bemeneti MP4-videofájlokat MP3-hangfájllá.

A minta a parancssor futtatása után létrehoz egy [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile) objektumot az MP3-fájlhoz. A rendszer az összes tevékenység kimeneti fájlját (ebben az esetben egyet) feltölti egy, a társított Storage-fiókban lévő tárolóba a tevékenység [OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles) tulajdonsága segítségével. Korábban a kód mintájában egy közös hozzáférésű aláírási URL-címet ( `outputContainerSasUrl` ) kapott, amely írási hozzáférést biztosít a kimeneti tárolóhoz. Jegyezze fel az `outputFile` objektumon beállított feltételeket. Egy tevékenységből származó kimeneti fájl csak a feladat sikeres befejeződése után lesz feltöltve a tárolóba ( `OutputFileUploadCondition.TaskSuccess` ). További részletekért tekintse meg a GitHubon a teljes [kód mintát](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) .

Ezt követően a minta tevékenységeket ad a feladathoz az [AddTaskAsync](/dotnet/api/microsoft.azure.batch.joboperations.addtaskasync) metódussal, amely várólistára helyezi azokat a számítási csomópontokon való futtatáshoz.

```csharp
 // Create a collection to hold the tasks added to the job.
List<CloudTask> tasks = new List<CloudTask>();

for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);

    // Define task command line to convert each input file.
    string appPath = String.Format("%AZ_BATCH_APP_PACKAGE_{0}#{1}%", appPackageId, appPackageVersion);
    string inputMediaFile = inputFiles[i].FilePath;
    string outputMediaFile = String.Format("{0}{1}",
        System.IO.Path.GetFileNameWithoutExtension(inputMediaFile),
        ".mp3");
    string taskCommandLine = String.Format("cmd /c {0}\\ffmpeg-4.3.1-2020-09-21-full_build\\bin\\ffmpeg.exe -i {1} {2}", appPath, inputMediaFile, outputMediaFile);

    // Create a cloud task (with the task ID and command line)
    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };

    // Task output file
    List<OutputFile> outputFileList = new List<OutputFile>();
    OutputFileBlobContainerDestination outputContainer = new OutputFileBlobContainerDestination(outputContainerSasUrl);
    OutputFile outputFile = new OutputFile(outputMediaFile,
       new OutputFileDestination(outputContainer),
       new OutputFileUploadOptions(OutputFileUploadCondition.TaskSuccess));
    outputFileList.Add(outputFile);
    task.OutputFiles = outputFileList;
    tasks.Add(task);
}

// Add tasks as a collection
await batchClient.JobOperations.AddTaskAsync(jobId, tasks);
return tasks
```

### <a name="monitor-tasks"></a>Tevékenységek figyelése

Amikor a Batch tevékenységeket ad egy feladathoz, a szolgáltatás automatikusan várólistára helyezi és ütemezi azok végrehajtását a társított készlet számítási csomópontjain. A Batch a megadott beállítások alapján elvégzi a tevékenységek várólistára helyezésével, ütemezésével, újrapróbálásával kapcsolatos összes feladatot, valamint a tevékenységekhez kapcsolódó egyéb rendszergazdai teendőket.

A tevékenységek végrehajtása sokféleképpen megfigyelhető. Ez a minta meghatároz egy `MonitorTasks` metódust, amely csak a tevékenységek befejezéséről, illetve a meghiúsult vagy a sikeres állapotról küld jelentést. A `MonitorTasks` kód meghatároz egy [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) objektumot, amely hatékonyan kiválasztja a lehető legkevesebb információt a tevékenységekről. Ezután létrehoz egy [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor) objektumot, amely a tevékenységállapotok figyeléséhez biztosít segédeszközöket. A `MonitorTasks` metódusban a minta megvárja, amíg minden tevékenység el nem éri a `TaskState.Completed` állapotot egy adott időkorláton belül. Ezután megszakítja a feladatot, és jelentést készít a befejeződött, de esetleg hibába ütköző tevékenységekről (ahol például a program nem nullás kilépési kódot adott).

```csharp
TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
try
{
    await taskStateMonitor.WhenAll(addedTasks, TaskState.Completed, timeout);
}
catch (TimeoutException)
{
    batchClient.JobOperations.TerminateJob(jobId);
    Console.WriteLine(incompleteMessage);
    return false;
}
batchClient.JobOperations.TerminateJob(jobId);
 Console.WriteLine(completeMessage);
...

```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tevékenységek futtatása után az alkalmazás automatikusan törli a létrehozott bemeneti Storage-tárolót, és felkínálja a Batch-készlet és -feladat törlésének lehetőségét. A BatchClient [JobOperations](/dotnet/api/microsoft.azure.batch.batchclient.joboperations) és [PoolOperations](/dotnet/api/microsoft.azure.batch.batchclient.pooloperations) osztálya is rendelkezik megfelelő törlési metódusokkal, amelyeket a rendszer meghív, ha megerősíti a törlést. Bár magukért a feladatokért és tevékenységekért nem kell fizetnie, a számítási csomópontokért igen. Ezért ajánlott csak szükség szerint lefoglalni a készleteket. A készlet törlésekor a rendszer a csomópont összes tevékenységének kimenetét is törli. A kimeneti fájlok azonban megmaradnak a Storage-fiókban.

Ha már nincs rájuk szükség, törölje az erőforráscsoportot, a Batch-fiókot és a Storage-fiókot. Ehhez az Azure Portalon válassza ki a Batch-fiókhoz tartozó erőforráscsoportot, és kattintson az **Erőforráscsoport törlése** elemre.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Alkalmazáscsomag hozzáadása a Batch-fiókhoz
> * Hitelesítés Batch- és Storage-fiókokkal
> * Bemeneti fájlok feltöltése a Storage-ba
> * Számításicsomópont-készlet létrehozása alkalmazás futtatásához
> * Feladatok és tevékenységek létrehozása bemeneti fájlok feldolgozásához
> * Tevékenységek végrehajtásának figyelése
> * Kimeneti fájlok lekérése

Batch-számításifeladatok .NET API használatával történő ütemezésére és feldolgozására a GitHub mintáiban találhat további példákat.

> [!div class="nextstepaction"]
> [A Batch C#-mintái](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)
