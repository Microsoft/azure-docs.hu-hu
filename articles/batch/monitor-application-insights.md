---
title: Batch figyelése az Azure Application Insights
description: Megtudhatja, hogyan Azure Batch .NET-alkalmazást az Azure Application Insights kódtárával.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 04/13/2021
ms.openlocfilehash: 8bc8ff0a04996d988a642062f118e9e6792abbf0
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389349"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>.NET-alkalmazás Azure Batch és hibakeresése a Application Insights

[Application Insights](../azure-monitor/app/app-insights-overview.md) és hatékony lehetőséget kínál a fejlesztőknek az Azure-szolgáltatásokban üzembe helyezett alkalmazások figyelére és hibakeresésére. A Application Insights teljesítményszámlálók és kivételek figyelése, valamint a kód egyéni metrikák és nyomkövetés beállítása. A Application Insights integrálása a Azure Batch alkalmazással lehetővé teszi, hogy mély betekintést nyerjen a viselkedésbe, és közel valós időben kivizsgálja a problémákat.

Ez a cikk bemutatja, hogyan adjuk hozzá és konfiguráljuk a Application Insights kódtárat a Azure Batch .NET-megoldáshoz, és hogyan lehet beállítani az alkalmazás kódját. Azt is bemutatja, hogyan figyelheti az alkalmazást a Azure Portal egyéni irányítópultok létrehozásához. A Application Insights nyelveken való támogatásról a nyelvek, platformok és integrációk [dokumentációjában tájékozódhat.](../azure-monitor/app/platforms.md)

A cikkhez tartozó kódot tartalmazó C#-mintamegoldás elérhető a [GitHubon.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) Ez a példa Application Insights kódot a [TopNWords példához.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) Ha még nem ismeri ezt a példát, először próbálja meg a TopNWords építést és futtatásokat. Ez segít megérteni egy alapszintű Batch-munkafolyamatot, amely több számítási csomóponton párhuzamosan feldolgoz egy bemeneti blobkészletet.

> [!TIP]
> Másik lehetőségként konfigurálja a Batch-megoldást úgy, hogy Application Insights adatokat, például a virtuális gépek teljesítményszámlálóit a Batch Explorer. [Batch Explorer](https://github.com/Azure/BatchExplorer) egy ingyenes, gazdag funkcionalitású, különálló ügyféleszköz, amely segítséget ad az alkalmazások létrehozásához, hibakereséséhez és Azure Batch figyelése érdekében. Töltse le a [telepítőcsomagot](https://azure.github.io/BatchExplorer/) Mac, Linux vagy Windows rendszerre. A [batch-insights adattára](https://github.com/Azure/batch-insights) gyors lépéseket tartalmaz a Application Insights adatok Batch Explorer.

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2017-es vagy újabb](https://www.visualstudio.com/vs)
- [Batch-fiók és csatolt tárfiók](batch-account-create-portal.md)
- [Application Insights erőforrás.](../azure-monitor/app/create-new-resource.md) A Azure Portal hozzon létre egy Application Insights *erőforrást.* Válassza az Általános **alkalmazástípust.** 
- Másolja ki [a eszközkulcsot](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key) a Azure Portal. Erre az értékre később még szüksége lesz.
  
  > [!NOTE]
  > A Application Insights. [](https://azure.microsoft.com/pricing/details/application-insights/) Ide tartoznak a cikkben tárgyalt diagnosztikai és monitorozási adatok.

## <a name="add-application-insights-to-your-project"></a>Új Application Insights hozzáadása a projekthez

A projekthez szükség van a **Microsoft.ApplicationInsights.WindowsServer** NuGet-csomagra és annak függőségeire. Adja hozzá vagy állítsa vissza őket az alkalmazás projektjéhez. A csomag telepítéséhez használja a parancsot vagy a `Install-Package` NuGet-Csomagkezelő.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```

Hivatkozhat Application Insights .NET-alkalmazásból a **Microsoft.ApplicationInsights névtér** használatával.

## <a name="instrument-your-code"></a>A kód megszerkesedő

A kód létrehozásához a megoldásnak létre kell hoznia egy Application Insights [TelemetryClient elemet.](/dotnet/api/microsoft.applicationinsights.telemetryclient) A példában a TelemetryClient betölti annak konfigurációját a [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md) fájlból. A következő projektekben ApplicationInsights.config mindenképpen frissítse a Application Insights kulcsával: Microsoft.Azure.Batch. Samples.TelemetryStartTask és TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```

Adja hozzá a eszközkulcsot is a TopNWords.cs fájlhoz.

A TopNWords.cs példája a [következő](../azure-monitor/app/api-custom-events-metrics.md) rendszerműveleti hívásokat használja a Application Insights API-ból:

- `TrackMetric()` – Nyomon követi, hogy átlagosan mennyi ideig tart egy számítási csomópont a szükséges szövegfájl letöltéséhez.
- `TrackTrace()` – Hibakeresési hívásokat ad hozzá a kódhoz.
- `TrackEvent()` – Nyomon követi a rögzítend kell érdekes eseményeket.

Ez a példa szándékosan kihagyja a kivételkezelést. Ehelyett a Application Insights automatikusan jelenti a nem kezelt kivételeket, ami jelentősen javítja a hibakeresési élményt.

Az alábbi kódrészlet ezeknek a metódusoknak a használatát mutatja be.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Azure Batch inicializáló segítője

Amikor telemetriát küld egy adott kiszolgálóhoz és példányhoz, a Application Insights az Azure-beli virtuális gép szerepkörét és virtuálisgép-nevét használja az alapértelmezett értékekhez. A példa a Azure Batch a készlet és a számítási csomópont nevének használatát mutatja be. [Telemetria-inicializálóval bírálja](../azure-monitor/app/api-filtering-sampling.md#add-properties) felül az alapértelmezett értékeket.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

A telemetria inicializáló engedélyezéséhez a TopNWordsSample ApplicationInsights.config fájl a következőket tartalmazza:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
```

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Frissítse a feladatot és a tevékenységeket úgy, hogy Application Insights bináris fájlokat

Annak érdekében, Application Insights megfelelően fusson a számítási csomópontokon, győződjön meg arról, hogy a bináris fájlok megfelelően vannak elhelyezve. Adja hozzá a szükséges bináris fájlokat a tevékenység erőforrásfájl-gyűjteményéhez, hogy a rendszer letöltse őket a feladat végrehajtásakor. Az alábbi kódrészletek hasonlóak a Job.cs fájlban kódhoz.

Először hozzon létre egy statikus listát a feltölt Application Insights fájlokról.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Ezután hozza létre a feladat által használt előkészítési fájlokat.

```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

A metódus egy segítő függvény a kódmintában, amely lehetővé teszi fájlok egyszerű feltöltését a helyi lemezről `FileToStage` egy Azure Storage-blobba. A rendszer később letölti az egyes fájlokat egy számítási csomópontra, és egy tevékenység hivatkozik rá.

Végül adja hozzá a tevékenységeket a feladathoz, és adja hozzá Application Insights bináris fájlokat.

```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Adatok megtekintése a Azure Portal

Most, hogy konfigurálta a feladatot és a tevékenységeket a Application Insights, futtassa a példafeladatot a készletben. Lépjen a Azure Portal, és nyissa meg Application Insights kiépített erőforrást. A készlet kiépítése után látnia kell az adatok áramlását és naplózását. A cikk további része csak néhány Application Insights érint, de nyugodtan ismerkedjen meg a teljes funkciókészlettel.

### <a name="view-live-stream-data"></a>Élő streamadatok megtekintése

Az Applications Insights-erőforrás nyomkövetési naplóinak megtekintéséhez kattintson a **Élő stream.** Az alábbi képernyőkép bemutatja, hogyan lehet megtekinteni a készlet számítási csomópontjairól származó élő adatokat, például a számítási csomópontonkénti CPU-használatot.

![Képernyőkép az élő stream számítási csomópontjának adatairól.](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Nyomkövetési naplók megtekintése

Az Applications Insights-erőforrás nyomkövetési naplóinak megtekintéséhez kattintson a **Keresés gombra.** Ez a nézet a naplók által rögzített diagnosztikai adatok listáját Application Insights nyomkövetéseket, eseményeket és kivételeket is beleértve. 

Az alábbi képernyőképen az látható, hogy a rendszer egy tevékenység egyetlen nyomkövetését naplózza, majd később lekérdezi hibakeresési célokból.

![Képernyőkép egyetlen nyomkövetés naplóiról.](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Nem kezelt kivételek megtekintése

Application Insights az alkalmazásból származó kivételeket naplózza. Ebben az esetben másodpercek alatt, amíg az alkalmazás kivételt dob, részletezhet egy adott kivételt, és diagnosztizálhatja a problémát.

![Képernyőkép a nem kezelt kivételekről.](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Blob letöltési ideje mérése

Az egyéni metrikák szintén értékes eszközök a portálon. Megjeleníthető például az az átlagos idő, amely alatt az egyes számítási csomópontok letöltötték a szükséges szövegfájlt.

Mintadiagram létrehozása:

1. A saját Application Insights kattintson a **diagram Metrikaböngésző**  >  **elemre.**
1. Kattintson **a hozzáadott** diagram Szerkesztés gombjára.
1. Frissítse a diagram részleteit a következőképpen:
   - A **Diagram típusaként adja meg** a **Rácsot.**
   - Állítsa **az Összesítést** **Átlagértékre.**
   - A **Csoportosítási beállításnál adja** meg **a NodeId (Csomópontazonosító) halmazt.**
   - A **Metrikák alatt** válassza az **Egyéni** blob  >  **letöltése másodpercben lehetőséget.**
   - A megjelenítési **színpaletta beállítása** a kívánthoz.

![Képernyőkép egy diagramról, amely a blobok letöltési idejét mutatja csomópontonként.](./media/monitor-application-insights/blobdownloadtime.png)

## <a name="monitor-compute-nodes-continuously&quot;></a>Számítási csomópontok folyamatos figyelése

Észreveheti, hogy a rendszer az összes metrikát, így a teljesítményszámlálókat is, csak akkor naplózza, amikor a tevékenységek futnak. Ez a viselkedés azért hasznos, mert korlátozza a naplókban Application Insights adatmennyiséget. Vannak azonban olyan esetek, amikor mindig figyelni szeretné a számítási csomópontokat. Előfordulhat például, hogy olyan háttér-munkát futtatnak, amely nincs ütemezve a Batch szolgáltatáson keresztül. Ebben az esetben állítson be egy monitorozási folyamatot, amely a számítási csomópont élettartamára fut. 

Ennek egyik módja, ha elindít egy folyamatot, amely betölti a Application Insights kódtárat, és a háttérben fut. A példában az indítási tevékenység betölti a bináris fájlokat a gépen, és határozatlan ideig futtat egy folyamatot. Konfigurálja Application Insights folyamat konfigurációs fájlját úgy, hogy további adatokat, például teljesítményszámlálókat bocsát ki.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = &quot;StartTask&quot;;
private const string BatchStartTaskTelemetryRunnerName = &quot;Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe&quot;;
private const string BatchStartTaskTelemetryRunnerAIConfig = &quot;ApplicationInsights.config&quot;;
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: &quot;standard_d1_v2&quot;,
    VirtualMachineConfiguration: new VirtualMachineConfiguration(
    imageReference: new ImageReference(
                        publisher: &quot;MicrosoftWindowsServer&quot;,
                        offer: &quot;WindowsServer&quot;,
                        sku: &quot;2019-datacenter-core&quot;,
                        version: &quot;latest"),
    nodeAgentSkuId: "batch.node.windows amd64");
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> A megoldás kezelhetősége érdekében a szerelvényt egy alkalmazáscsomagba [csomagolhatja.](./batch-application-packages.md) Ezután az alkalmazáscsomag készletekben való automatikus üzembe helyezéséhez adjon hozzá egy alkalmazáscsomag-hivatkozást a készletkonfigurációhoz.

## <a name="throttle-and-sample-data"></a>Adatátvitel és mintaadatok

Az éles környezetben futó Azure Batch nagy léptékű jellegéből adódóan érdemes lehet korlátozni a felhasználók által gyűjtött Application Insights a költségek kezelése érdekében. Az [ennek elérésére szolgáló Application Insights](../azure-monitor/app/sampling.md) mintavételezése a következőben: Mintavételezés a következőben: .

## <a name="next-steps"></a>Következő lépések

- További információ a [Application Insights.](../azure-monitor/app/app-insights-overview.md)
- A Application Insights nyelveken való támogatásról a nyelvek, platformok és integrációk [dokumentációjában talál további információt.](../azure-monitor/app/platforms.md)
