---
title: Adategység kódolása a Media Encoder Standard .NET használatával | Microsoft Docs
description: Ez a cikk bemutatja, hogyan lehet a .NET használatával kódolni az eszközöket Media Encoder Standard használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: anilmur
ms.custom: devx-track-csharp
ms.openlocfilehash: b94ec0b50182381d2a4e1bca09f4c8bc482d13ee
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014094"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Adategység kódolása Media Encoder Standard a .NET használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

A kódolási feladatok a Media Services leggyakoribb feldolgozási műveleteinek egyike. Kódolási feladatokat hozhat létre a médiafájlok egyik kódolásból a másikba való konvertálásához. A kódolás során a Media Services beépített adathordozó-kódolót használhatja. Használhat egy Media Services Partner által biztosított kódolót is; a külső gyártótól származó kódolók az Azure Piactéren keresztül érhetők el. 

Ez a cikk bemutatja, hogyan kódolhatja az eszközöket a .NET-tel Media Encoder Standard (MES) használatával. Media Encoder Standard az [itt](./media-services-mes-presets-overview.md)ismertetett kódolók egyikének használatával van konfigurálva.

Javasoljuk, hogy mindig kódolja a forrásfájlokat egy adaptív sávszélességű MP4-készletbe, majd alakítsa át a készletet a kívánt formátumra a [dinamikus csomagolás](media-services-dynamic-packaging-overview.md)használatával. 

Ha a kimeneti eszköz titkosított tároló, konfigurálnia kell az eszköz kézbesítési házirendjét. További információ: az [eszközök kézbesítési házirendjének konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> A MES létrehoz egy olyan nevű kimeneti fájlt, amely a bemeneti fájl nevének első 32 karakterét tartalmazza. A név az előre definiált fájlban megadott értéken alapul. Például: "fájlnév": "{basename} _ {index} {Extension}". A (z) {basename} karakterláncot a bemeneti fájl nevének első 32 karaktere váltja fel.
> 
> 

### <a name="mes-formats"></a>MES-formátumok
[Formátumok és kodekek](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>MES-beállításkészletek
Media Encoder Standard az [itt](./media-services-mes-presets-overview.md)ismertetett kódolók egyikének használatával van konfigurálva.

### <a name="input-and-output-metadata"></a>Bemeneti és kimeneti metaadatok
Ha a MES használatával kódol egy bemeneti adategységet (vagy eszközöket), a rendszer az adott kódolási feladat sikeres befejezésekor egy kimeneti eszközt kap. A kimeneti eszköz a használt kódolási beállításkészlet alapján video-, hang-, miniatűr-, jegyzékfájl-és egyéb adatokat tartalmaz.

A kimeneti eszköz egy olyan fájlt is tartalmaz, amely tartalmazza a bemeneti adategység metaadatait. A metaadatok XML-fájljának neve a következő formátumú: <asset_id # C1_metadata.xml (például 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), ahol <asset_id> a bemeneti eszköz AssetId értéke. A bemeneti metaadatok XML-fájljának sémája [itt](media-services-input-metadata-schema.md)van leírva.

A kimeneti eszköz egy olyan fájlt is tartalmaz, amely tartalmazza a kimeneti eszköz metaadatait. A metaadatok XML-fájljának neve a következő formátumú: <source_file_name # C1_manifest.xml (például BigBuckBunny_manifest.xml). A kimeneti metaadatok XML-fájljának sémája [itt](media-services-output-metadata-schema.md)van leírva.

Ha meg szeretné vizsgálni a két metaadat-fájl egyikét, létrehozhat egy SAS-keresőt, és letöltheti a fájlt a helyi számítógépre. Talál egy példát arra, hogyan hozhat létre SAS-lokátort, és hogyan tölthet le egy fájlt a Media Services .NET SDK-bővítmények használatával.

## <a name="download-sample"></a>Minta letöltése
Beolvashat és futtathat egy mintát, amely bemutatja, hogyan kódolhatja a MES [-t innen.](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/)

## <a name="net-sample-code"></a>.NET-mintakód

A következő kódrészlet a Media Services .NET SDK-t használja a következő feladatok elvégzéséhez:

* Hozzon létre egy kódolási feladatot.
* A Media Encoder Standard kódolóra mutató hivatkozás beszerzése.
* Itt adhatja meg az [adaptív adatfolyam](media-services-autogen-bitrate-ladder-with-mes.md) -készlet használatát. 
* Egyetlen kódolási feladat hozzáadása a feladathoz. 
* Adja meg a kódolni kívánt bemeneti objektumot.
* Hozzon létre egy kimeneti eszközt, amely tartalmazza a kódolt objektumot.
* Adjon hozzá egy eseménykezelőt a feladatok előrehaladásának ellenőrzéséhez.
* Küldje el a feladatot.

#### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel a app.config fájlt a következő témakörben leírtak szerint: [Media Services fejlesztés a .net](media-services-dotnet-how-to-use.md)-tel. 

#### <a name="example"></a>Példa 

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderStandardSample
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

            // Encode and generate the output using the "Adaptive Streaming" preset.
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

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "Adaptive Streaming",
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

## <a name="advanced-encoding-features-to-explore"></a>Speciális kódolási funkciók a felderítéshez
* [Miniatűrök készítése](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Miniatűrök létrehozása a kódolás során](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Videók körülvágása a kódolás során](media-services-crop-video.md)
* [Kódolási beállításkészletek testreszabása](media-services-custom-mes-presets-with-dotnet.md)
* [Videó befedése vagy vízjele képpel](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések
[Miniatűr készítése a .net](media-services-dotnet-generate-thumbnail-with-mes.md) 
 -Media Encoder standard használatával [Media Services kódolás áttekintése](media-services-encode-asset.md)