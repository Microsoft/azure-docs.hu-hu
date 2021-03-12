---
title: Speciális kódolás végrehajtása a MES-beállításkészletek testreszabásával | Microsoft Docs
description: Ez a témakör bemutatja, hogyan hajthat végre speciális kódolást Media Encoder Standard feladat-előállítók testreszabásával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 345fd56e5ff069ea222661a4820e75d96a9e2395
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014502"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Speciális kódolás végrehajtása a MES-beállításkészletek testreszabásával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Áttekintés

Ez a témakör bemutatja, hogyan szabhatja testre a Media Encoder Standard beállításkészleteit. A [Media Encoder standard egyéni beállításkészletek használatával történő kódolás](media-services-custom-mes-presets-with-dotnet.md) azt mutatja be, hogyan használható a .net egy kódolási feladat létrehozásához és egy feladat végrehajtásához. Az előre beállított beállítások testreszabása után adja meg az egyéni beállításkészletet a kódolási feladathoz. 

Ha XML-készletet használ, ügyeljen arra, hogy megőrizze az elemek sorrendjét, ahogy az alábbi XML-példákban is látható (például a KeyFrameInterval előtt kell megelőznie a SceneChangeDetection).

> [!NOTE] 
> A Media Encoder Standard Advanced Media Services v2 számos funkciója jelenleg nem érhető el a v3-as verzióban. További információt [az áttelepítési útmutatóban](../latest/migrate-v-2-v-3-migration-introduction.md)talál.

## <a name="support-for-relative-sizes"></a>Relatív méretek támogatása

Miniatűrök létrehozásakor nem kell mindig a kimeneti szélességet és a magasságot megadni képpontban. Megadhatja azokat százalékban, az [1%,..., 100%] tartományban.

### <a name="json-preset"></a>JSON-készlet

```json
"Width": "100%",
"Height": "100%"
```

### <a name="xml-preset"></a>XML-beállításkészlet

```xml
<Width>100%</Width>
<Height>100%</Height>
```

## <a name="generate-thumbnails"></a>Miniatűrök létrehozása

Ebből a szakaszból megtudhatja, hogyan szabhatja testre a miniatűröket létrehozó beállításkészletet. Az alábbi előre definiált készlet a fájl kódolásához és a miniatűrök létrehozásához szükséges információkhoz tartalmaz információkat. A [jelen](media-services-mes-presets-overview.md) szakaszban ismertetett MES-előállítók bármelyikét elvégezheti, és hozzáadhat olyan kódot, amely miniatűröket hoz létre.  

> [!NOTE]
> A következő beállításkészlet **SceneChangeDetection** beállítása csak akkor állítható be igaz értékre, ha egyetlen bitráta-videó kódolást használ. Ha többszörös sávszélességű videót kódol, és a **SceneChangeDetection** igaz értékre állítja, a kódoló hibát ad vissza.  
>
>

A sémával kapcsolatos további információkért tekintse meg [ezt a](media-services-mes-schema.md) témakört.

Ügyeljen rá, hogy ellenőrizze a [szempontok](#considerations) szakaszt.

### <a name="json-preset"></a>JSON-készlet

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
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "PngLayers": [
        {
          "Type": "PngLayer",
          "Width": 640,
          "Height": 360,
        }
      ],
      "Start": "00:00:01",
      "Step": "00:00:10",
      "Range": "00:00:58",
      "Type": "PngImage"
    },
    {
      "BmpLayers": [
        {
          "Type": "BmpLayer",
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "10%",
      "Step": "10%",
      "Range": "90%",
      "Type": "BmpImage"
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
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "PngFormat"
      }
    },
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "BmpFormat"
      }
    },
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
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
          <Width>640</Width>
          <Height>360</Height>
          <Quality>90</Quality>
        </JpgLayer>
      </JpgLayers>
    </JpgImage>
    <BmpImage Start="10%" Step="10%" Range="90%">
      <BmpLayers>
        <BmpLayer>
          <Width>640</Width>
          <Height>360</Height>
        </BmpLayer>
      </BmpLayers>
    </BmpImage>
    <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
      <PngLayers>
        <PngLayer>
          <Width>640</Width>
          <Height>360</Height>
        </PngLayer>
      </PngLayers>
    </PngImage>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <JpgFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <BmpFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <PngFormat />
    </Output>
  </Outputs>
</Preset>
```

### <a name="considerations"></a>Megfontolandó szempontok

A következő szempontokat kell figyelembe venni:

* A kezdés/lépés/tartomány explicit időbélyegek használata feltételezi, hogy a bemeneti forrás legalább 1 percet vesz igénybe.
* A jpg/PNG/BmpImage elemek indítási, Step és Range karakterlánc-attribútumokkal rendelkeznek – ezeket a következőképpen lehet értelmezni:

  * A keret száma, ha nem negatív egész számok, például "Start": "120",
  * A forrás időtartamához képest, ha%-utótagként van kifejezve, például "Start": "15%", vagy
  * Időbélyeg, ha a következőképpen van megadva: óó: PP: mm... formátum, például "Start": "00:01:00"

    A jelöléseket a kívánt módon keverheti és párosíthatja.

    Emellett a Start egy speciális makrót is támogat: {Best}, amely megpróbálja meghatározni a tartalom első "érdekes" keretét: (a lépés és a tartomány figyelmen kívül lesz hagyva, ha a Start értéke {Best})
  * Alapértékek: Start: {Best}
* A kimeneti formátumot explicit módon meg kell adni az egyes képformátumokhoz: jpg/PNG/BmpFormat. Ha jelen van, a MES megfelel a JpgVideo JpgFormat, és így tovább. A OutputFormat egy új rendszerkép-kodek specifikus makrót ({index}) vezet be, amely a képkimeneti formátumok esetében (egyszer és egyszer) is szerepelnie kell.

## <a name="trim-a-video-clipping"></a><a id="trim_video"></a>Videó levágása (vágás)
Ez a szakasz a kódoló-előállítók módosítását tárgyalja a bemeneti videóhoz, ahol a bemenet egy úgynevezett köztes fájl vagy igény szerinti fájl. A kódoló egy élő streamből rögzített vagy archivált adategység rögzítésére vagy levágására is használható – ennek részletei ebben a [blogban](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)érhetők el.

A videók körülvágásához bármely [, a következő szakaszban](media-services-mes-presets-overview.md) dokumentált MES-beállításkészletet végrehajthat, és módosíthatja a **Sources** elemet (az alább látható módon). A kezdő időpont értékének meg kell egyeznie a bemeneti videó abszolút időbélyegzővel. Ha például a bemeneti videó első képkockája 12:00:10.000, akkor a kezdő időpontnak legalább 12:00:10.000 és nagyobb számnak kell lennie. Az alábbi példában feltételezzük, hogy a bemeneti videó kezdő időbélyege nulla. A **forrásokat** a beállításkészlet elején kell elhelyezni.

### <a name="json-preset"></a><a id="json"></a>JSON-készlet

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "StartTime": "00:00:04",
      "Duration": "00:00:16"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "StretchMode": "AutoSize",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1500,
          "MaxBitrate": 1500,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
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

### <a name="xml-preset"></a>XML-beállításkészlet
A videók körülvágása érdekében az [itt](media-services-mes-presets-overview.md) dokumentált MES-készletek bármelyikét elvégezheti, és módosíthatja a **Sources** elemet (az alább látható módon).

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source StartTime="PT4S" Duration="PT14S"/>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>3400</Bitrate>
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
          <MaxBitrate>3400</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>2250</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>2250</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1500</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1500</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1000</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1000</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>650</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>650</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>400</Bitrate>
          <Width>320</Width>
          <Height>180</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>400</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>128</Bitrate>
    </AACAudio>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="create-an-overlay"></a><a id="overlay"></a>Átfedés létrehozása

A Media Encoder Standard lehetővé teszi egy rendszerkép átfedését egy meglévő videóra. Jelenleg a következő formátumok támogatottak: PNG, jpg, GIF és BMP. Az alábbi előre megadott beállításkészlet egy videó átfedésének alapszintű példája.

Egy előre definiált fájl definiálásán kívül azt is meg kell adnia, hogy Media Services tudja, hogy az eszköz mely fájlja az átfedésben lévő rendszerkép, és hogy melyik fájl a forrásként szolgáló videó, amelybe a képet át kívánja adni. A videofájl **elsődleges** fájlnak kell lennie.

Ha .NET-et használ, adja hozzá a következő két függvényt [a témakörben](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) definiált .net-példához. A **UploadMediaFilesFromFolder** függvény fájlokat tölt fel egy mappából (például BigBuckBunny.mp4 és Image001.png), és beállítja, hogy az MP4-fájl az eszköz elsődleges fájlja legyen. A **EncodeWithOverlay** függvény a megadott egyéni előre definiált fájlt (például a következő készletet) használja a kódolási feladat létrehozásához.

```csharp
static public IAsset UploadMediaFilesFromFolder(string folderPath)
{
    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);

    foreach (var af in asset.AssetFiles)
    {
        // The following code assumes 
        // you have an input folder with one MP4 and one overlay image file.
        if (af.Name.Contains(".mp4"))
            af.IsPrimary = true;
        else
            af.IsPrimary = false;

        af.Update();
    }

    return asset;
}

static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
{
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(customPresetFileName);

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input assets to be encoded.
    // This asset contains a source file and an overlay file.
    task.InputAssets.Add(assetSource);

    // Add an output asset to contain the results of the job. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

    return job.OutputMediaAssets[0];
}
```

> [!NOTE]
> Jelenlegi korlátozások:
>
> Az átfedés opacitásának beállítása nem támogatott.
>
> A forrásként szolgáló videofájl és az átfedésben lévő képfájlnak ugyanabban az adategységben kell lennie, és a videofájl az adategységben lévő elsődleges fájlként kell megadnia.
>
>

### <a name="json-preset"></a>JSON-készlet

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "VideoOverlay": {
          "Position": {
            "X": 100,
            "Y": 100,
            "Width": 100,
            "Height": 50
          },
          "AudioGainLevel": 0.0,
          "MediaParams": [
            {
              "OverlayLoopCount": 1
            },
            {
              "IsOverlay": true,
              "OverlayLoopCount": 1
            }
          ],
          "Source": "Image001.png",
          "Clip": {
            "Duration": "00:00:05"
          },
          "FadeInDuration": {
            "Duration": "00:00:01"
          },
          "FadeOutDuration": {
            "StartTime": "00:00:03",
            "Duration": "00:00:04"
          }
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1045,
          "MaxBitrate": 1045,
          "BufferWindow": "00:00:05",
          "ReferenceFrames": 3,
          "EntropyMode": "Cavlc",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Type": "CopyAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}{Extension}",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="xml-preset"></a>XML-beállításkészlet

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source>
      <Streams />
      <Filters>
        <VideoOverlay>
          <Source>Image001.png</Source>
          <Clip Duration="PT5S" />
          <FadeInDuration Duration="PT1S" />
          <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
          <Position X="100" Y="100" Width="100" Height="50" />
          <Opacity>0</Opacity>
          <AudioGainLevel>0</AudioGainLevel>
          <MediaParams>
            <MediaParam>
              <IsOverlay>false</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
            <MediaParam>
              <IsOverlay>true</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
          </MediaParams>
        </VideoOverlay>
      </Filters>
      <Pad>true</Pad>
    </Source>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>1045</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>0</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cavlc</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1045</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <CopyAudio />
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}{Extension}">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>Csendes hangfelvétel beszúrása, ha a bemenet nem rendelkezik hanggal
Alapértelmezés szerint ha olyan bemenetet küld a kódolónak, amely csak videót tartalmaz, és nincs hang, akkor a kimeneti eszköz csak videó adatokat tartalmazó fájlokat tartalmaz. Előfordulhat, hogy egyes játékosok nem tudják kezelni az ilyen kimeneti adatfolyamokat. Ezzel a beállítással kényszerítheti a kódolót, hogy egy csendes hangsávot adjon hozzá a forgatókönyv kimenetéhez.

Ha úgy szeretné kényszeríteni a kódolót, hogy olyan objektumot hozzon létre, amely csendes hangsávot tartalmaz, ha a bemenet nem rendelkezik hanggal, adja meg a "InsertSilenceIfNoAudio" értéket.

Az [ebben](media-services-mes-presets-overview.md) a szakaszban ISMERTETett MES-előállítók bármelyikét elvégezheti, és a következő módosítást végezheti el:

### <a name="json-preset"></a>JSON-készlet

```json
{
  "Channels": 2,
  "SamplingRate": 44100,
  "Bitrate": 96,
  "Type": "AACAudio",
  "Condition": "InsertSilenceIfNoAudio"
}
```

### <a name="xml-preset"></a>XML-beállításkészlet

```xml
<AACAudio Condition="InsertSilenceIfNoAudio">
  <Channels>2</Channels>
  <SamplingRate>44100</SamplingRate>
  <Bitrate>96</Bitrate>
</AACAudio>
```

## <a name="disable-auto-de-interlacing"></a><a id="deinterlacing"></a>Automatikus letiltás letiltása
Az ügyfeleknek nem kell bármit megtenniük, ha úgy tetszik, hogy az interlaced tartalma automatikusan összefűzött. Ha az automatikus kikapcsolási funkció be van kapcsolva (alapértelmezett), a MES az összefűzött keretek automatikus észlelését végzi el, és csak az egymással összefűzött képkockákat jelöli.

Kikapcsolhatja az automatikus kikapcsolást. Ez a beállítás nem ajánlott.

### <a name="json-preset"></a>JSON-készlet

```json
"Sources": [
  {
    "Filters": {
      "Deinterlace": {
        "Mode": "Off"
      }
    },
  }
]
```

### <a name="xml-preset"></a>XML-beállításkészlet

```xml
<Sources>
  <Source>
    <Filters>
      <Deinterlace>
        <Mode>Off</Mode>
      </Deinterlace>
    </Filters>
  </Source>
</Sources>
```

## <a name="audio-only-presets"></a><a id="audio_only"></a>Csak hangbeállítások
Ez a szakasz két csak hangra vonatkozó MES-beállításkészletet mutat be: AAC hang-és AAC jó minőségű hang.

### <a name="aac-audio"></a>AAC hang

```json
{
  "Version": 1.0,
  "Codecs": [
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
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="aac-good-quality-audio"></a>AAC jó minőségű hang

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 192,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="concatenate-two-or-more-video-files"></a><a id="concatenate"></a>Két vagy több videofájl összefűzése

Az alábbi példa azt szemlélteti, hogyan lehet előállítani egy készletet két vagy több videofájl összefűzésére. A leggyakoribb forgatókönyv az, amikor egy fejlécet vagy egy trailert szeretne hozzáadni a fő videóhoz. A rendeltetésszerű használat akkor történik meg, amikor a szerkesztett videofájlok közösen használják a megosztási tulajdonságokat (videó felbontása, Képkockasebesség, hangsávok száma stb.). Ügyeljen arra, hogy ne keverje a különböző képarányú videókat vagy különböző számú hangsávot.

>[!NOTE]
>Az összefűzési funkció aktuális terve arra vár, hogy a bemeneti videoklipek konzisztensek legyenek a felbontás, a képkockák aránya stb. alapján. 

### <a name="requirements-and-considerations"></a>Követelmények és megfontolások

* A bemeneti videók csak egyetlen hangsávval rendelkezhetnek.
* A bemeneti videóknak azonos képaránysal kell rendelkezniük.
* Fel kell töltenie a videóit különálló eszközökre, és minden egyes eszközön meg kell adnia a videókat elsődleges fájlként.
* Ismernie kell a videók időtartamát.
* Az alábbi előre definiált példák azt feltételezik, hogy az összes bemeneti videó üres időbélyeggel kezdődik. Ha a videók eltérő kezdő időbélyegzővel rendelkeznek, akkor módosítania kell a kezdő időpontokat, ahogy az az élő archívumok esetében is jellemző.
* A JSON-beállításkészlet explicit módon hivatkozik a bemeneti eszközök AssetID értékeire.
* A mintakód azt feltételezi, hogy a JSON-beállításkészlet helyi fájlba lett mentve, például: "C:\supportFiles\preset.jsbe". Azt is feltételezi, hogy két eszköz lett létrehozva két videofájl feltöltésével, és ismeri az eredményül kapott AssetID értékeket.
* A kódrészlet és a JSON-készlet egy példát mutat be két videofájl összefűzésére. A következő lehetőségek közül kettőnél több videóra is kiterjesztheti:

  1. Feladat hívása. A InputAssets. Add () elem ismételt hozzáadásával további videókat adhat hozzá a sorrendben.
  2. A JSON "Sources" elemének megfelelő szerkesztése a további bejegyzések hozzáadásával ugyanabban a sorrendben.

### <a name="net-code"></a>.NET-kód

```csharp
IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

// Declare a new job.
IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
// Get a media processor reference, and pass to it the name of the
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

// Load the XML (or JSON) from the local file.
string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

// Create a task
ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
    processor,
    configuration,
    TaskOptions.None);

// Specify the input videos to be concatenated (in order).
task.InputAssets.Add(asset1);
task.InputAssets.Add(asset2);
// Add an output asset to contain the results of the job.
// This output is specified as AssetCreationOptions.None, which
// means the output asset is not encrypted.
task.OutputAssets.AddNew("Output asset",
    AssetCreationOptions.None);

job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
job.Submit();
job.GetExecutionProgressTask(CancellationToken.None).Wait();
```

### <a name="json-preset"></a>JSON-készlet

Frissítse az egyéni készletet az összefűzni kívánt eszközök azonosítói alapján, valamint az egyes videók megfelelő időszegmensével.

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
      "StartTime": "00:00:01",
      "Duration": "00:00:15"
    },
    {
      "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
      "StartTime": "00:00:02",
      "Duration": "00:00:05"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": true,
      "H264Layers": [
        {
          "Level": "auto",
          "Bitrate": 1800,
          "MaxBitrate": 1800,
          "BufferWindow": "00:00:05",
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
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
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="crop-videos-with-media-encoder-standard"></a><a id="crop"></a>Videók körülvágása a Media Encoder Standarddel
Tekintse meg a [videók körülvágása Media Encoder standard](media-services-crop-video.md) témakört.

## <a name="insert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>Videó követése, ha a bemenet nem tartalmaz videót

Alapértelmezés szerint, ha olyan bemenetet küld a kódolónak, amely csak hangot vagy videót tartalmaz, akkor a kimeneti eszköz csak hangadatokat tartalmazó fájlokat tartalmaz. Néhány játékos, beleértve a Azure Media Playert is (lásd [ezt](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) előfordulhat, hogy nem tudja kezelni az ilyen adatfolyamokat. Ezzel a beállítással kényszerítheti a kódolót, hogy adjon hozzá egy monokróm videó-követést az adott forgatókönyv kimenetéhez.

> [!NOTE]
> Ha a kódolót egy kimeneti videó beszúrására kényszeríti, a kimeneti eszköz mérete megnő, és így a kódolási feladathoz felmerülő költségek is növekednek. A tesztek futtatásával ellenőrizheti, hogy ez az eredő növekedés csak mérsékelt hatással van-e a havi díjakra.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Videó beszúrása csak a legalacsonyabb sávszélességű

Tegyük fel, hogy több bitrátát használó kódolási beállításkészletet használ, mint például a ["H264 Multiple bitráta 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) , hogy kódolja a teljes bemeneti katalógust a folyamatos átvitelhez, amely a videofájlok és a csak hangfájlok kombinációját tartalmazza. Ebben a forgatókönyvben, ha a bemenet nem tartalmaz videót, érdemes lehet kényszeríteni a kódolót úgy, hogy a lehető legalacsonyabb sávszélességgel illesszen be egy monokróm videó-követést, és ne helyezzen be videót minden kimeneti bitrátánál. Ennek eléréséhez a **InsertBlackIfNoVideoBottomLayerOnly** jelzőt kell használnia.

Az [ebben](media-services-mes-presets-overview.md) a szakaszban ISMERTETett MES-előállítók bármelyikét elvégezheti, és a következő módosítást végezheti el:

#### <a name="json-preset"></a>JSON-készlet

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>XML-beállításkészlet

XML használata esetén használja a Condition = "InsertBlackIfNoVideoBottomLayerOnly" attribútumot a **H264Video** elemhez és a Condition = "InsertSilenceIfNoAudio" attribútumként a **AACAudio**.

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>Videó beszúrása minden kimeneti bitrátánál
Tegyük fel, hogy több bitrátát használó kódolási beállításkészletet használ, például a ["H264 Multiple bitráta 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) -t a teljes bemeneti katalógus kódolásához a folyamatos átvitelhez, amely a videofájlok és a csak hangfájlok kombinációját tartalmazza. Ebben a forgatókönyvben, ha a bemenetnek nincs videója, érdemes lehet a kódolót kényszeríteni, hogy az összes kimeneti bitrátán beillesszen egy monokróm videó sávot. Ez biztosítja, hogy a kimeneti eszközök mind homogének legyenek a videók és hangsávok számának tekintetében. Ennek eléréséhez meg kell adnia a "InsertBlackIfNoVideo" jelzőt.

Az [ebben](media-services-mes-presets-overview.md) a szakaszban ISMERTETett MES-előállítók bármelyikét elvégezheti, és a következő módosítást végezheti el:

#### <a name="json-preset"></a>JSON-készlet

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideo",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>XML-beállításkészlet

XML használata esetén használja a Condition = "InsertBlackIfNoVideo" attribútumot a **H264Video** elemhez és a Condition = "InsertSilenceIfNoAudio" attribútumként a **AACAudio**.

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a name="rotate-a-video"></a><a id="rotate_video"></a>Videó elforgatása
A [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) 0/90/180/270-es szögben támogatja a rotációt. Az alapértelmezett viselkedés az "automatikus", ahol a rendszer megpróbálja felderíteni a beérkező videofájl forgási metaadatait, és kompenzálja azt. Adja meg a következő **források** elemet az [ebben](media-services-mes-presets-overview.md) a szakaszban meghatározott előzetes beállítások egyikéhez:

### <a name="json-preset"></a>JSON-készlet

```json
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
  ],
  "Codecs": [

    ...
```

### <a name="xml-preset"></a>XML-beállításkészlet

```xml
<Sources>
  <Source>
    <Streams />
    <Filters>
      <Rotation>90</Rotation>
    </Filters>
  </Source>
</Sources>
```

Tekintse meg [ezt](media-services-mes-schema.md#PreserveResolutionAfterRotation) a témakört, amely arról nyújt tájékoztatást, hogy a kódoló hogyan értelmezi az előre beállított szélességi és magassági beállításokat a rotációs kompenzáció elindításakor.

A "0" érték megadásával jelezheti, hogy a kódoló figyelmen kívül hagyja a rotációs metaadatokat, ha vannak ilyenek a bemeneti videóban.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Media Services kódolás áttekintése](media-services-encode-asset.md)
