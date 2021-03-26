---
title: Batch figyelése az Azure Application Insights
description: Ismerje meg, hogyan hozhat Azure Batch .NET-alkalmazást az Azure Application Insights Library használatával.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 03/25/2021
ms.openlocfilehash: 251f02f145e8f450b1528bf8676cffdc61a6f051
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607881"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Azure Batch .NET-alkalmazás figyelése és hibakeresése Application Insights

A [Application Insights](../azure-monitor/app/app-insights-overview.md) elegáns és hatékony módszert kínál a fejlesztők számára az Azure-szolgáltatásokban üzembe helyezett alkalmazások figyelésére és hibakeresésére. A Application Insights segítségével figyelheti a teljesítményszámlálók és a kivételek figyelését, valamint a kód egyéni metrikákkal és nyomkövetéssel való elvégzéséhez. A Application Insights és a Azure Batch alkalmazás integrálásával mélyreható elemzéseket nyerhet a viselkedésekről, és közel valós időben vizsgálhatja meg a problémákat.

Ez a cikk bemutatja, hogyan adhatja hozzá és konfigurálhatja a Application Insights könyvtárat a Azure Batch .NET-megoldásba, és hogyan állíthatja be az alkalmazás kódját. Emellett azt is bemutatja, hogyan figyelheti meg az alkalmazást a Azure Portalon keresztül, és hogyan hozhat létre egyéni irányítópultokat. A más nyelveken való Application Insights támogatásáról a [nyelvek, platformok és integrációs dokumentációban](../azure-monitor/app/platforms.md)olvashat.

A [githubon](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)elérhető egy minta C#-megoldás, amely kóddal kíséri ezt a cikket. Ez a példa Application Insights rendszerállapot-kódot adja hozzá a [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) példához. Ha még nem ismeri ezt a példát, próbálja meg először kiépíteni és futtatni a TopNWords. Ez segít megérteni egy alapszintű batch-munkafolyamatot, amely a bemeneti Blobok egy csoportjának párhuzamos feldolgozását végzi több számítási csomóponton.

> [!TIP]
> Alternatív megoldásként konfigurálhatja a Batch-megoldást úgy, hogy az Application Insights adatait, például a virtuális gépek teljesítményszámlálói a Batch Explorerben jelenjenek meg. A [Batch Explorer](https://github.com/Azure/BatchExplorer) egy ingyenes, gazdag funkcionalitású, önálló ügyfél-eszköz, amely a Azure batch alkalmazások létrehozását, hibakeresését és figyelését segíti elő. Töltse le a [telepítőcsomagot](https://azure.github.io/BatchExplorer/) Mac, Linux vagy Windows rendszerre. A Application Insights-adatBatch Explorerokban való engedélyezésének gyors lépéseiért tekintse meg a [Batch-](https://github.com/Azure/batch-insights) elemzések tárházát.

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2017 vagy újabb](https://www.visualstudio.com/vs)
- [Batch-fiók és társított Storage-fiók](batch-account-create-portal.md)
- [Application Insights erőforrás](../azure-monitor/app/create-new-resource.md). Application Insights *erőforrás* létrehozásához használja a Azure Portal. Válassza ki az *általános* **alkalmazás típusát**.
- Másolja a kialakítási [kulcsot](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key) a Azure Portal. Ezt az értéket később kell megadnia.
  
  > [!NOTE]
  > Előfordulhat, hogy a Application Insights tárolt adatmennyiségért kell [fizetnie](https://azure.microsoft.com/pricing/details/application-insights/) . Ebbe beletartozik a jelen cikkben ismertetett diagnosztikai és figyelési adatértékek is.

## <a name="add-application-insights-to-your-project"></a>Application Insights hozzáadása a projekthez

A projekthez a **Microsoft. ApplicationInsights. windowsserver** NuGet-csomag és annak függőségei szükségesek. Adja hozzá vagy állítsa vissza az alkalmazás projektjét. A csomag telepítéséhez használja a `Install-Package` parancsot vagy a NuGet csomagkezelő eszközt.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```

A .NET-alkalmazásból a **Microsoft. ApplicationInsights** névtér használatával Application Insights referenciát.

## <a name="instrument-your-code"></a>A kód műszere

A kód megadásához a megoldásnak létre kell hoznia egy Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). A példában a TelemetryClient betölti a konfigurációját a [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md) fájlból. Ügyeljen arra, hogy a következő projektekben ApplicationInsights.config frissítse a Application Insights-kialakítási kulccsal: Microsoft.Azure.BatCH. Samples. TelemetryStartTask és TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```

Adja hozzá a kialakítási kulcsot is a TopNWords. cs fájlban.

A TopNWords. cs példa a következő Instrumentation- [hívásokat](../azure-monitor/app/api-custom-events-metrics.md) használja a Application Insights API-ból:

- `TrackMetric()` -Nyomon követi, hogy a számítási csomópontok átlagosan mennyi ideig tartanak a szükséges szövegfájl letöltésével.
- `TrackTrace()` – Hibakeresési hívásokat szúr be a kódra.
- `TrackEvent()` – Nyomon követi a rögzítendő érdekes eseményeket.

Ez a példa szándékosan kihagyja a kivételek kezelését. Ehelyett Application Insights automatikusan jelentést készít a nem kezelt kivételekről, ami jelentősen javítja a hibakeresési élményt.

A következő kódrészlet a módszerek használatát mutatja be.

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Azure Batch telemetria inicializáló segítője

Egy adott kiszolgáló és példány telemetria jelentésekor Application Insights az Azure virtuálisgép-szerepkört és a virtuális gép nevét használja az alapértelmezett értékekhez. A Azure Batch kontextusában a példa azt szemlélteti, hogyan használható a készlet neve és a számítási csomópont neve. A [telemetria inicializáló](../azure-monitor/app/api-filtering-sampling.md#add-properties) használatával felülbírálhatja az alapértelmezett értékeket.

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

A telemetria inicializálásának engedélyezéséhez a TopNWordsSample projektben található ApplicationInsights.config fájl a következőket tartalmazza:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
```

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>A feladat és a feladatok frissítése Application Insights bináris fájlok belefoglalásához

Ahhoz, hogy a Application Insights megfelelően fusson a számítási csomópontokon, győződjön meg arról, hogy a bináris fájlok megfelelően vannak elhelyezve. Adja hozzá a szükséges bináris fájlokat a feladat erőforrásfájl-gyűjteményéhez, hogy azok a feladat végrehajtásának időpontjában letöltve legyenek. A következő kódrészletek hasonlóak a job. cs kódban található kódhoz.

Először hozza létre a feltölteni kívánt Application Insights-fájlok statikus listáját.

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

Ezután hozza létre a feladat által használt átmeneti fájlokat.

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

A `FileToStage` metódus egy segítő függvény a Code (kód) mintában, amely lehetővé teszi, hogy könnyedén feltölt egy fájlt a helyi lemezről egy Azure Storage-blobba. A rendszer minden fájlt később letölt egy számítási csomópontra, és egy feladat hivatkozik rá.

Végül adja hozzá a feladatokat a feladathoz, és adja meg a szükséges Application Insights bináris fájlokat.

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

## <a name="view-data-in-the-azure-portal"></a>A Azure Portalban lévő adatmegjelenítés

Most, hogy konfigurálta a feladatot és a feladatokat a Application Insights használatához, futtassa a példában szereplő feladatot a készletben. Navigáljon a Azure Portal, és nyissa meg a kiépített Application Insights erőforrást. A készlet kiépítés után meg kell kezdenie az adatforgalom megtekintését és a naplózott beolvasást. A cikk többi része csak néhány Application Insights funkciót érint, de a teljes szolgáltatáskészlet megismeréséhez.

### <a name="view-live-stream-data"></a>Élő stream-adattartalom megtekintése

Ha szeretné megtekinteni a nyomkövetési naplókat az Applications-elemzések erőforrásaiban, kattintson a **élő stream** elemre. Az alábbi képernyőfelvételen a készlet számítási csomópontjaiból érkező élő adatok tekinthetők meg, például a CPU-használat számítási csomóponton.

![Képernyőkép az élő stream számítási csomópont-adatokról.](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Nyomkövetési naplók megtekintése

Ha szeretné megtekinteni a nyomkövetési naplókat az Applications-elemzések erőforrásaiban, kattintson a **Keresés** gombra. Ez a nézet a Application Insights által rögzített diagnosztikai adat listáját jeleníti meg, beleértve a nyomkövetést, az eseményeket és a kivételeket. 

Az alábbi képernyőfelvételen látható, hogyan történik egy adott tevékenység egyetlen nyomkövetésének naplózása, és a rendszer később hibakeresés céljából kérdezi le azokat.

![A naplók egyetlen nyomkövetési naplókat jelenítenek meg.](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Kezeletlen kivételek megtekintése

Application Insights naplózza az alkalmazásból kiváltott kivételeket. Ebben az esetben az alkalmazás kivételt okozó másodpercen belül egy adott kivételt is megvizsgálhat, és diagnosztizálhatja a problémát.

![A nem kezelt kivételeket bemutató képernyőkép.](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>BLOB letöltési idejének mérése

Az egyéni metrikák szintén értékes eszköznek bizonyulnak a portálon. Megjelenítheti például az egyes számítási csomópontok átlagos időpontját, hogy letöltse a feldolgozás alatt lévő szükséges szövegfájlt.

Minta diagram létrehozása:

1. A Application Insights erőforrásban kattintson a **Metrikaböngésző**  >  **diagram hozzáadása** lehetőségre.
1. Kattintson a **Szerkesztés** elemre a hozzáadott diagramon.
1. A diagram részleteit a következőképpen frissítheti:
   - **Diagram típusának** beállítása **rácsra**
   - Állítsa az **összesítést** **átlag** értékre.
   - Állítsa be a **Group By** **NodeId**.
   - A **metrikák** területen válassza   >  **az egyéni blob Letöltés másodpercben** lehetőséget.
   - A megjelenítési **színpaletta** beállítása tetszés szerinti értékre

![Képernyőfelvétel a Blobok letöltési idejét ábrázoló diagramról.](./media/monitor-application-insights/blobdownloadtime.png)

## <a name="monitor-compute-nodes-continuously"></a>Számítási csomópontok folyamatos figyelése

Előfordulhat, hogy észrevette, hogy az összes mérőszám, beleértve a teljesítményszámlálókat is, csak akkor van naplózva, amikor a feladatok futnak. Ez a viselkedés azért hasznos, mert korlátozza a naplókban Application Insights adatmennyiséget. Vannak azonban olyan esetek, amikor mindig szeretné figyelni a számítási csomópontokat. Előfordulhat például, hogy olyan háttér-munkát futtat, amely nem a Batch szolgáltatáson keresztül van ütemezve. Ebben az esetben állítson be egy figyelési folyamatot, amely a számítási csomópont élettartamára fut. 

A viselkedés elérésének egyik módja egy olyan folyamat elkészítése, amely betölti a Application Insights könyvtárat, és a háttérben fut. A példában az indítási tevékenység betölti a bináris fájlokat a gépen, és határozatlan ideig fut egy folyamat. A folyamat Application Insights konfigurációs fájljának konfigurálásával további, például teljesítményszámlálók számára elérhetővé teheti azokat.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
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
> A megoldás kezelhetőségének növeléséhez csomagolja ki a szerelvényt egy [alkalmazáscsomag](./batch-application-packages.md)használatával. Ezután az alkalmazáscsomag automatikus üzembe helyezése a készletekben: adjon hozzá egy alkalmazáscsomag-hivatkozást a készlet konfigurációjához.

## <a name="throttle-and-sample-data"></a>Szabályozás és mintaadatok

Az éles környezetben futó Azure Batch alkalmazások nagy léptékű jellege miatt érdemes korlátozni a Application Insights által összegyűjtött adatok mennyiségét a költségek kezelésére. Ennek eléréséhez tekintse meg a [mintavétel Application Insightsban](../azure-monitor/app/sampling.md) című témakört.

## <a name="next-steps"></a>Következő lépések

- További információ a [Application Insightsról](../azure-monitor/app/app-insights-overview.md).
- A más nyelveken való Application Insights támogatásáról a [nyelvek, platformok és integrációs dokumentációban](../azure-monitor/app/platforms.md)olvashat.
