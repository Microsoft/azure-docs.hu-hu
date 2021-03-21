---
title: Media Encoder Standard-készletek testreszabása | Microsoft Docs
description: Ez a témakör bemutatja, hogyan hajthat végre speciális kódolást Media Encoder Standard feladat-előállítók testreszabásával. A témakör bemutatja, hogyan használható a Media Services .NET SDK kódolási feladat és feladat létrehozásához. Azt is bemutatja, hogyan lehet egyéni beállításkészleteket megadni a kódolási feladathoz.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: c2ec7c8fe312787d153c76b0b0194354e1717db7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016712"
---
# <a name="customizing-media-encoder-standard-presets"></a>Media Encoder Standard-beállításkészletek testreszabása

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan végezhető el a Media Encoder Standard (MES) speciális kódolása egyéni beállításkészlet használatával. A cikk a .NET használatával hoz létre egy kódolási feladatot és egy feladatot, amely végrehajtja ezt a feladatot.  

Ebből a cikkből megtudhatja, hogyan szabhatja testre a készletet a [H264 több bitráta 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) -készletének és a rétegek számának csökkentésével. Az [Media Encoder standard-készletek testreszabása](media-services-advanced-encoding-with-mes.md) cikk a speciális kódolási feladatok végrehajtásához használható egyéni előbeállításokat mutatja be.

> [!NOTE]
> Az ebben a cikkben ismertetett egyéni beállításkészletek nem használhatók [Media Services v3](../latest/index.yml) átalakításokban vagy a CLI-parancsokban. További részletekért tekintse meg a [v2 és v3 közötti áttelepítési útmutatót](../latest/migrate-v-2-v-3-migration-introduction.md) .

## <a name="customizing-a-mes-preset"></a><a id="customizing_presets"></a> MES-beállításkészlet testreszabása

### <a name="original-preset"></a>Eredeti beállításkészlet

Mentse a JSON [több bitráta 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) -cikkben definiált JSON-fájlt egy. JSON kiterjesztésű fájlban. **CustomPreset_JSON.js** például:.

### <a name="customized-preset"></a>Testreszabott beállításkészlet

Nyissa meg a **CustomPreset_JSON.js** fájlt, és távolítsa el az első három réteget a **H264Layers** , így a fájl így néz ki.

```json 
  {  
    "Version": 1.0,  
    "Codecs": [  
      {  
        "KeyFrameInterval": "00:00:02",  
        "H264Layers": [  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 1000,  
            "MaxBitrate": 1000,  
            "BufferWindow": "00:00:05",  
            "Width": 640,  
            "Height": 360,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          },  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 650,  
            "MaxBitrate": 650,  
            "BufferWindow": "00:00:05",  
            "Width": 640,  
            "Height": 360,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          },  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 400,  
            "MaxBitrate": 400,  
            "BufferWindow": "00:00:05",  
            "Width": 320,  
            "Height": 180,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          }  
        ],  
        "Type": "H264Video"  
      },  
      {  
        "Profile": "AACLC",  
        "Channels": 2,  
        "SamplingRate": 48000,  
        "Bitrate": 128,  
        "Type": "AACAudio"  
      }  
    ],  
    "Outputs": [  
      {  
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
        "Format": {  
          "Type": "MP4Format"  
        }  
      }  
    ]  
  }  
```

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Kódolás Media Services .NET SDK-val

A következő kódrészlet a Media Services .NET SDK-t használja a következő feladatok elvégzéséhez:

- Hozzon létre egy kódolási feladatot.
- A Media Encoder Standard kódolóra mutató hivatkozás beszerzése.
- Töltse be az előző szakaszban létrehozott egyéni JSON-készletet. 

    ```csharp
    // Load the JSON from the local file.
    string configuration = File.ReadAllText(fileName);  
    ```

- Kódolási feladat hozzáadása a feladathoz. 
- Adja meg a kódolni kívánt bemeneti objektumot.
- Hozzon létre egy kimeneti eszközt, amely tartalmazza a kódolt objektumot.
- Adjon hozzá egy eseménykezelőt a feladatok előrehaladásának ellenőrzéséhez.
- Küldje el a feladatot.
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel a app.config fájlt a következő témakörben leírtak szerint: [Media Services fejlesztés a .net](media-services-dotnet-how-to-use.md)-tel. 

#### <a name="example"></a>Példa   

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace CustomizeMESPresests
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

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using custom presets.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("CustomPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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

## <a name="see-also"></a>Lásd még

- [Kódolás egyéni átalakítással a CLI használatával](../latest/custom-preset-cli-howto.md)
- [Kódolás a Media Services v3 használatával](../latest/encoding-concept.md)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
