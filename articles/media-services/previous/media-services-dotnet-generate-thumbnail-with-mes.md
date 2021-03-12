---
title: Miniatűrök létrehozása a .NET-es Media Encoder Standard használatával
description: Ez a témakör azt mutatja be, hogyan lehet a .NET használatával kódolni az eszközöket, és a miniatűröket egyszerre a Media Encoder Standard használatával előkészíteni.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 0159a706bd5f5229a12ab52bc92d240f01a2ed11
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014043"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Miniatűrök létrehozása a .NET-es Media Encoder Standard használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

A Media Encoder Standard használatával létrehozhat egy vagy több miniatűrt a bemeneti videóból [JPEG](https://en.wikipedia.org/wiki/JPEG), [png](https://en.wikipedia.org/wiki/Portable_Network_Graphics)vagy [BMP](https://en.wikipedia.org/wiki/BMP_file_format) formátumú képfájlformátumban. Küldhet olyan feladatokat, amelyek csak képeket hoznak létre, vagy kombinálhatja a miniatűrt a kódolással. Ebből a cikkből megtudhatja, hogyan használhatók az ilyen forgatókönyvekhez tartozó XML-és JSON-alapú miniatűr-készletek. A cikk végén található egy [mintakód](#code_sample) , amely bemutatja, hogyan használhatja a Media Services .net SDK-t a kódolási feladat végrehajtásához.

A minta-előállítók által használt elemekkel kapcsolatos további részletekért tekintse át [Media Encoder standard sémát](media-services-mes-schema.md).

Ügyeljen rá, hogy ellenőrizze a [szempontok](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) szakaszt.
    
## <a name="example-of-a-single-png-file-preset"></a>Példa az "egyetlen PNG-fájl" készletre

A következő JSON-és XML-beállításkészlet használatával egyetlen kimeneti PNG-fájl hozható létre a bemeneti videó első néhány másodpercében, ahol a kódoló a legjobb erőfeszítést tesz a "érdekes" keret megtalálásához. Vegye figyelembe, hogy a kimeneti képméret 100%-ra van állítva, ami azt jelenti, hogy ezek egyeznek a bemeneti videó méreteivel. Azt is vegye figyelembe, hogy a "kimenetek" formátuma beállításnak meg kell egyeznie a "PngLayers" a "kodekek" szakaszban való használatával. 

### <a name="json-preset"></a>JSON-készlet

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
```
    
### <a name="xml-preset"></a>XML-beállításkészlet

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Példa "JPEG-képek sorozata" készletre

A következő JSON-és XML-készlettel 10 lemezképet hozhat létre a bemeneti ütemterv 5%-os, 15%-os,..., 95%-os időbélyegzővel, ahol a képméret a bemeneti videó egy negyedévére van beállítva.

### <a name="json-preset"></a>JSON-készlet

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-beállításkészlet
    
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Példa az "egy rendszerkép egy adott időbélyegen" beállításra

A következő JSON-és XML-beállításkészlet használatával egyetlen JPEG-képet hozhat létre a bemeneti videó 30 másodperces jelölése alapján. Ez az előre beállított érték 30 másodpercnél hosszabb időt vesz igénybe a bemeneti videóban (máskülönben a feladatok meghiúsulnak).

### <a name="json-preset"></a>JSON-készlet

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-beállításkészlet
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Példa a "miniatűrök a különböző felbontásokban" beállításra

A következő beállításkészlet használatával különböző felbontásban hozhatja elő a miniatűröket egy adott feladatban. A példában az 5%, 15%,..., a bemeneti idősor 95% 100-ában a kódoló két rendszerképet hoz létre: a bemeneti videó felbontásának és a másiknak a (z) 50%-ban.

Jegyezze fel a {Resolution} makró használatát a fájlnévben. azt jelzi, hogy a kódoló azt a szélességet és magasságot használja, amelyet az beállításkészlet kódolás szakaszában adott meg, miközben létrehozza a kimeneti lemezképek fájlnevét. Ez megkönnyíti a különböző rendszerképek megkülönböztetését is

### <a name="json-preset"></a>JSON-készlet

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-beállításkészlet
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Példa a miniatűr generálására kódolás közben

Noha az összes fenti példa azt ismerteti, hogyan küldhet olyan kódolási feladatot, amely csak képeket hoz létre, a videó/hang kódolását a miniatűr generálásával is kombinálhatja. A következő JSON-és XML-beállításkészlet **Media Encoder standard** a miniatűr létrehozásához a kódolás során.

### <a name="json-preset"></a><a id="json"></a>JSON-készlet
A sémával kapcsolatos további információkért tekintse meg [ezt a](./media-services-mes-schema.md) cikket.

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a><a id="xml"></a>XML-beállításkészlet
A sémával kapcsolatos további információkért tekintse meg [ezt a](./media-services-mes-schema.md) cikket.

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   
```

## <a name="encode-video-and-generate-thumbnail-with-net"></a><a id="code_sample"></a>Videó kódolása és miniatűr készítése a .NET-tel

A következő kódrészlet a Media Services .NET SDK-t használja a következő feladatok elvégzéséhez:

* Hozzon létre egy kódolási feladatot.
* A Media Encoder Standard kódolóra mutató hivatkozás beszerzése.
* Töltse be a kódolási beállításkészletet tartalmazó előre definiált XML-vagy [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) [-](media-services-dotnet-generate-thumbnail-with-mes.md#xml) t, valamint a miniatűrök létrehozásához szükséges információkat. Ezt az  [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) -t vagy [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) -fájlt mentheti egy fájlba, és a következő kód használatával töltheti be a fájlt.

    ```csharp
    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(fileName);  
    ```

* Egyetlen kódolási feladat hozzáadása a feladathoz. 
* Adja meg a kódolni kívánt bemeneti objektumot.
* Hozzon létre egy kimeneti eszközt, amely tartalmazza a kódolt objektumot.
* Adjon hozzá egy eseménykezelőt a feladatok előrehaladásának ellenőrzéséhez.
* Küldje el a feladatot.

A fejlesztői környezet beállításával kapcsolatos útmutatásért tekintse meg a [Media Services a .net](media-services-dotnet-how-to-use.md) -ben című cikket.

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace EncodeAndGenerateThumbnails
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

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
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

## <a name="considerations"></a>Megfontolandó szempontok
A következő szempontokat kell figyelembe venni:

* A kezdés/lépés/tartomány explicit időbélyegek használata feltételezi, hogy a bemeneti forrás legalább 1 percet vesz igénybe.
* A jpg/PNG/BmpImage elemek indítási, Step és Range karakterlánc-attribútumokkal rendelkeznek – ezeket a következőképpen lehet értelmezni:
  
  * A keret száma, ha nem negatív egész számok, például "Start": "120",
  * A forrás időtartamához képest, ha%-utótagként van kifejezve, például "Start": "15%", vagy
  * Időbélyeg, ha a következőképpen van megadva: óó: PP: mm... formátumban. Például "Start": "00:01:00"
    
    A jelöléseket a kívánt módon keverheti és párosíthatja.
    
    Emellett a Start egy speciális makrót is támogat: {Best}, amely megpróbálja meghatározni a tartalom első "érdekes" keretét: (a lépés és a tartomány figyelmen kívül lesz hagyva, ha a Start értéke {Best})
  * Alapértékek: Start: {Best}
* A kimeneti formátumot explicit módon meg kell adni az egyes képformátumokhoz: jpg/PNG/BmpFormat. Ha jelen van, a MES megfelel a JpgVideo JpgFormat, és így tovább. A OutputFormat egy új rendszerkép-kodek specifikus makrót ({index}) vezet be, amely a képkimeneti formátumok esetében (egyszer és egyszer) is szerepelnie kell.

## <a name="next-steps"></a>Következő lépések

A [folyamat előrehaladását](media-services-check-job-progress.md) a kódolási feladatok függőben állapotában tekintheti meg.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Media Services kódolás áttekintése](media-services-encode-asset.md)