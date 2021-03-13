---
title: Speciális kódolás a Media Encoder Premium Workflowkal | Microsoft Docs
description: Útmutató a Media Encoder Premium Workflow való kódoláshoz. A kód minták C# nyelven íródtak, és a .NET-hez készült Media Services SDK-t használják.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b58a1ae2a20afb357f6363077e70b692256d2ce7
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013499"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Speciális kódolás a Media Encoder Premium-munkafolyamattal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A cikkben tárgyalt Media Encoder Premium Workflow adathordozó-feldolgozó Kínában nem érhető el.
>
>

## <a name="overview"></a>Áttekintés
Microsoft Azure Media Services bevezeti a **Media Encoder Premium workflow** adathordozó-processzort. Ez a processzor a prémium szintű igény szerinti munkafolyamatokhoz nyújt előzetes kódolási képességeket.

A következő témakörök a **Media Encoder Premium workflow** kapcsolatos részleteket ismertetik:

* [A Media Encoder Premium workflow által támogatott formátumok](./media-services-encode-asset.md) – a **Media Encoder Premium workflow** által támogatott fájlformátumokat és kodekeket tárgyalja.
* Az [Azure on-demand adathordozó-kódolók áttekintése és összehasonlítása](media-services-encode-asset.md) a **Media Encoder Premium workflow** és **Media Encoder standard** kódolási képességeit hasonlítja össze.

Ez a cikk bemutatja, hogyan kódolható a **Media Encoder Premium workflow** a .NET használatával.

A **Media Encoder Premium workflow** kódolási feladatai külön konfigurációs fájlt igényelnek, amelyet a munkafolyamat-fájlnak nevezünk. Ezeknek a fájloknak. munkafolyamat-bővítményük van, és a [munkafolyamat-tervező](media-services-workflow-designer.md) eszköz használatával jönnek létre.

Az alapértelmezett munkafolyamat-fájlokat [itt](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)is lekérheti. A mappa a fájlok leírását is tartalmazza.

A munkafolyamatok fájljait adategységként kell feltölteni a Media Services-fiókjába, és ezt az objektumot át kell adni a kódolási feladatba.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel a app.config fájlt a következő témakörben leírtak szerint: [Media Services fejlesztés a .net](media-services-dotnet-how-to-use.md)-tel. 

## <a name="encoding-example"></a>Kódolási példa

Az alábbi példa azt szemlélteti, hogyan lehet a kódolást **Media Encoder Premium workflow**.

A következő lépéseket hajtja végre:

1. Hozzon létre egy eszközt, és töltsön fel egy munkafolyamat-fájlt.
2. Hozzon létre egy objektumot, és töltse fel a forrás médiafájlt.
3. Szerezze be a "Media Encoder Premium Workflow" adathordozó-processzort.
4. Hozzon létre egy feladatot és egy feladatot.

    A legtöbb esetben a feladat konfigurációs karakterlánca üres (az alábbi példához hasonlóan). Vannak olyan speciális forgatókönyvek (amelyek a futásidejű tulajdonságok dinamikus beállítását igénylik) ebben az esetben egy XML-karakterláncot kell megadni a kódolási feladatnak. Ilyen forgatókönyvek például a következők: átfedés létrehozása, párhuzamos vagy szekvenciális adathordozó-fűzés, feliratozás.
5. Vegyen fel két bemeneti eszközt a feladatba.

   1. 1. – a munkafolyamat-eszköz.
   2. 2 – a videós eszköz.

      >[!NOTE]
      >A munkafolyamatot az eszköz előtt hozzá kell adni a feladathoz.
      A feladat konfigurációs karakterláncának üresnek kell lennie.
   
6. Küldje el a kódolási feladatot.

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderPremiumWorkflowSample
{
    class Program
    {
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _workflowFilePath =
            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

        private static readonly string _singleMP4InputFilePath =
            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

        static void Main(string[] args)
        {

            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

        }

        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                processor,
                "",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(workflow);
            task.InputAssets.Add(video); // we add one asset
                                         // Add an output asset to contain the results of the job.
                                         // This output is specified as AssetCreationOptions.None, which
                                         // means the output asset is not encrypted.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                throw new Exception("\nExiting method due to job error.");
            }

            return job.OutputMediaAssets[0];
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
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="need-help"></a>Segítségre van szüksége?

A támogatási jegy megnyitásához lépjen az [új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]