---
title: Arcok kivonása a Azure Media Analyticskal | Microsoft Docs
description: A Azure Media Redactor egy Azure Media Analytics adathordozó-feldolgozó, amely méretezhető Arcfelismerés a felhőben. Ez a cikk bemutatja, hogyan lehet kitakarni az arcokat az Azure Media Analytics használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7d416810f6a39fb36bfa3c5225301fe87fdd128c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013465"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Arcok kivonása a Azure Media Analytics

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Áttekintés

A **Azure Media redactor** egy [Azure Media Analytics](./legacy-components.md) Media Processor (mp), amely méretezhető arc-kivonást biztosít a felhőben. Az Arcfelismerés lehetővé teszi a videó módosítását, hogy a kijelölt személyek ne legyenek elmosódottak. Érdemes lehet a Face kivonási szolgáltatást használni a közbiztonság és a hírek adathordozóján. Néhány perces felvétel, amely több arcot tartalmaz, manuálisan is eltarthat, de ezzel a szolgáltatással az arc-kivonási folyamat csak néhány egyszerű lépést igényelhet.

Ez a cikk részletesen ismerteti a **Azure Media redactor** , és bemutatja, hogyan használható a Media Services SDK for .net szolgáltatással.

## <a name="face-redaction-modes"></a>Szembenéző kivonási módok

Az arc-kivonás úgy működik, hogy a videó minden képkockájában felderíti az arcokat, és nyomon követi a Face objektumot az időben és visszafelé, így ugyanazokat a személyeket más nézőpontokból is el lehet tekinteni. Az automatikus kivonási folyamat összetett, és nem mindig a kívánt kimenet 100%-át hozza létre, ezért Media Analytics a végső kimenet módosításának néhány módját is lehetővé teszi.

A teljesen automatikus mód mellett van egy kétlépéses munkafolyamat is, amely lehetővé teszi, hogy a talált arcok kiválasztása/kiválasztása az azonosítók listáján keresztül történjen. Azt is megteheti, hogy a frame-beállítások alapján tetszőlegesen beállítható, hogy a felügyeleti csomag JSON formátumú metaadat-fájlt használ. A munkafolyamat **elemzése** és **kivonási** módokra van bontva. A két mód egyetlen menetben is egyesíthető, amely egy feladatban mindkét feladatot futtatja; ezt a módot **Összevontnak** nevezzük.

   > [!NOTE]
   > Az arc-érzékelő adathordozó-processzora 2020 júniusa óta elavult, [Azure Media Services örökölt összetevőket](./legacy-components.md)tartalmaz. Érdemes lehet Azure Media Services V3 API-t használni.

### <a name="combined-mode"></a>Kombinált mód

Ez automatikusan létrehoz egy kivont MP4-t manuális bevitel nélkül.

| Fázis | Fájlnév | Jegyzetek |
| --- | --- | --- |
| Bemeneti eszköz |foo.bar |Videó WMV, MOV vagy MP4 formátumban |
| Bemeneti konfiguráció |Feladatokhoz beállított konfiguráció |{"version": "1.0", "Options": {"Mode": "combined"}} |
| Kimeneti eszköz |foo_redacted.mp4 |Videó az elmosódás alkalmazásával |

### <a name="analyze-mode"></a>Elemzési mód

A kétlépéses munkafolyamat **elemzése** átveszi a videó bemenetét, és az összes észlelt arc egy JSON-fájlját, valamint a jpg-képeket hozza létre.

| Fázis | Fájlnév | Jegyzetek |
| --- | --- | --- |
| Bemeneti eszköz |foo.bar |Videó WMV, MPV vagy MP4 formátumban |
| Bemeneti konfiguráció |Feladatokhoz beállított konfiguráció |{"version": "1.0", "Options": {"Mode": "elemzés"}} |
| Kimeneti eszköz |foo_annotations.jsbekapcsolva |Az arc helyeinek megjegyzései JSON formátumban. Ezt a felhasználó módosíthatja az elmosódást határoló mezők módosításához. Lásd az alábbi mintát. |
| Kimeneti eszköz |foo_thumb% 06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Az egyes észlelt arcoknál az összes megjelenített jpg, ahol a szám az arc labelId jelöli. |

#### <a name="output-example"></a>Példa kimenetre

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-mode"></a>Kivonási mód

A munkafolyamat második lépése nagyobb mennyiségű bemenetet igényel, amelyeket egyetlen objektumba kell összevonni.

Ide tartozik az életlenítés, az eredeti videó és a jegyzetek JSON-azonosítóinak listája. Ez a mód a jegyzeteket használja a bemeneti videó elmosódásának alkalmazásához.

Az elemzés menetének kimenete nem tartalmazza az eredeti videót. A videót fel kell tölteni a bemeneti eszközbe a kivonási mód feladathoz, és az elsődleges fájlként kell kiválasztani.

| Fázis | Fájlnév | Jegyzetek |
| --- | --- | --- |
| Bemeneti eszköz |foo.bar |Videó WMV-, MPV-vagy MP4-formátumban. Ugyanaz a videó, mint az 1. lépésben. |
| Bemeneti eszköz |foo_annotations.jsbekapcsolva |Megjegyzések metaadatainak fájlja az első fázisból, választható módosításokkal. |
| Bemeneti eszköz |foo_IDList.txt (nem kötelező) |A kibontani kívánt arc-azonosítók új, sorba tagolt listája. Ha üresen hagyja, az elmossa az összes arcot. |
| Bemeneti konfiguráció |Feladatokhoz beállított konfiguráció |{"version": "1.0", "Options": {"Mode": "kivonás"}} |
| Kimeneti eszköz |foo_redacted.mp4 |Videó az elmosódást alkalmazva a jegyzetek alapján |

#### <a name="example-output"></a>Példa kimenetre

Egy kiválasztott AZONOSÍTÓval rendelkező IDList kimenete.

Példa foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Életlenítési típusok

A **kombinált** vagy a **kivonási** módban 5 különböző életlenítési mód közül választhat a JSON bemeneti konfigurációjának használatával: **alacsony**, **Med**, **magas**, **doboz** és **fekete**. Alapértelmezés szerint a **Med** használatos.

Az alábbi életlenítési típusok mintáit találhatja meg.

### <a name="example-json"></a>Példa JSON-ra

```json
{
    'version':'1.0',
    'options': {
        'Mode': 'Combined',
        'BlurType': 'High'
    }
}
```

#### <a name="low"></a>Alacsony

![Alacsony](./media/media-services-face-redaction/blur1.png)

#### <a name="med"></a>Med

![Med](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Magas

![Magas](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Fekete

![Fekete](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>A kimeneti JSON-fájl elemei

A kivonási MP magas pontosságú arcfelismerés és-követést biztosít, amely a videó keretén belül akár 64 emberi arcot képes észlelni. Az elülső arcok biztosítják a legjobb eredményeket, míg az arcok és a kis arcok (24x24 képpontnál kisebb vagy egyenlő) kihívást jelentenek.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET-mintakód

A következő program a következőket mutatja be:

1. Hozzon létre egy adategységet, és töltsön fel egy médiafájlt az eszközre.
2. A következő JSON-beállításkészletet tartalmazó konfigurációs fájl alapján hozzon létre egy arc kivonási feladattal rendelkező feladatot: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
                }
            }
    ```

3. Töltse le a kimeneti JSON-fájlokat.

### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel a app.config fájlt a következő témakörben leírtak szerint: [Media Services fejlesztés a .net](media-services-dotnet-how-to-use.md)-tel.

#### <a name="example"></a>Példa

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace FaceRedaction
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                error.Code,
                                error.Message));
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                       mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások

[Azure Media Services Analytics áttekintése](./legacy-components.md)

[Azure Media Analytics bemutatók](http://amslabs.azurewebsites.net/demos/Analytics.html)
