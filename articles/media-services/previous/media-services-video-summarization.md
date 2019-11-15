---
title: Videók összefoglalásának létrehozása a Azure Media Video Thumbnails használatával | Microsoft Docs
description: A videó-összefoglalás segítségével összefoglalókat hozhat létre a hosszú videókról a forrás videóból származó érdekes kódrészletek automatikus kiválasztásával. Ez akkor hasznos, ha gyors áttekintést szeretne nyújtani arról, hogy mire számíthat egy hosszú videóban.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: da397c180b6ccaf91084a0ec22210c7bcc88a5dc
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084804"
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Videók összegzésének létrehozása a Azure Media Video Thumbnails használatával  
 
## <a name="overview"></a>Áttekintés

A **Azure Media Video thumbnails** Media Processor (mp) lehetővé teszi egy olyan videó összegzésének létrehozását, amely olyan ügyfelek számára hasznos, akik csak egy hosszú videó összegzését szeretnék előkészíteni. Előfordulhat például, hogy az ügyfelek egy rövid "összefoglaló videót" szeretnének látni, amikor az egérmutatót egy miniatűr fölé viszi. A **Azure Media Video thumbnails** paramétereinek egy konfigurációs készleten keresztüli megcsípésével az MP hatékony shot észlelési és összefűzési technológiája segítségével algorithmically elő egy leíró alklip.  

Az **Azure Media Video thumbnail** mp jelenleg előzetes verzióban érhető el.

Ez a cikk részletesen ismerteti az **Azure Media Video miniatűrjét** , és bemutatja, hogyan használhatja azt a .net-hez készült Media Services SDK-val.

## <a name="limitations"></a>Korlátozások

Bizonyos esetekben, ha a videó nem különböző jelenetekből áll, a kimenet csak egyetlen lövést eredményez.

## <a name="video-summary-example"></a>Videóösszegzés – példa
Íme néhány példa arra, hogy mit tehet a Azure Media Video Thumbnails Media Processor:

### <a name="original-video"></a>Eredeti videó
[Eredeti videó](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>Videó miniatűr eredménye
[Videó miniatűr eredménye](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>Feladat konfigurációja (előre beállított)
Ha **Azure Media Video Thumbnailskal**rendelkező videó miniatűr feladatot hoz létre, meg kell adnia egy konfigurációs beállításkészletet. A fenti miniatűr minta a következő alapszintű JSON-konfigurációval lett létrehozva:

```json
    {
        "version":"1.0"
    }
```

Jelenleg a következő paramétereket módosíthatja:

| Param | Leírás |
| --- | --- |
| outputAudio |Megadja, hogy az eredményül kapott videó tartalmaz-e hangot. <br/>Az engedélyezett értékek: true vagy FALSE. Az alapértelmezett érték a True (igaz). |
| fadeInFadeOut |Megadja, hogy a rendszer áttűnési átmeneteket használ-e a különálló mozgóképek bélyegképei között.  <br/>Az engedélyezett értékek: true vagy FALSE.  Az alapértelmezett érték a True (igaz). |
| maxMotionThumbnailDurationInSecs |Egész szám, amely azt határozza meg, hogy mennyi ideig kell a teljes eredményes videó.  Az alapértelmezett érték az eredeti videó időtartamától függ. |

Az alábbi táblázat az alapértelmezett időtartamot ismerteti, ha a **maxMotionThumbnailInSecs** nincs használatban.

|  |  |  |
| --- | --- | --- |
| Videó időtartama |d < 3 perc |3 perc < d < 15 perc |
| Miniatűr időtartama |15 mp (2-3 jelenetek) |30 mp (3-5 jelenetek) |

A következő JSON a rendelkezésre álló paramétereket állítja be.

```json
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }
```

## <a name="net-sample-code"></a>.NET-mintakód

A következő program a következőket mutatja be:

1. Hozzon létre egy adategységet, és töltsön fel egy médiafájlt az eszközre.
2. Egy videót tartalmazó miniatűr feladattal rendelkező feladatot hoz létre egy olyan konfigurációs fájl alapján, amely a következő JSON-beállításkészletet tartalmazza: 
    
    ```json
            {                
                "version": "1.0",
                "options": {
                    "outputAudio": "true",
                    "maxMotionThumbnailDurationInSecs": "30",
                    "fadeInFadeOut": "false"
                }
            }
    ```

3. Letölti a kimeneti fájlokat. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 

#### <a name="example"></a>Példa

```csharp
    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;

    namespace VideoSummarization
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


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

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

### <a name="video-thumbnail-output"></a>Videó miniatűr kimenete
[Videó miniatűr kimenete](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Azure Media Analytics bemutatók](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

