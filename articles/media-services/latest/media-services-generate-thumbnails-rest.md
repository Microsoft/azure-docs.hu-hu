---
title: Miniatűrök előállítása Media Encoder Standard REST használatával
description: Ez a cikk azt mutatja be, hogyan használható a REST egy eszköz kódolásához és a miniatűrök létrehozásához egyszerre Media Encoder Standard használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f4456a65f422c23da73f36dd74680fbb598db186
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955834"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>Bélyegképek előállítása a Encoder standard és a REST használatával

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Media Encoder Standard használatával létrehozhat egy vagy több miniatűrt a bemeneti videóból [JPEG](https://en.wikipedia.org/wiki/JPEG), [png](https://en.wikipedia.org/wiki/Portable_Network_Graphics)vagy [BMP](https://en.wikipedia.org/wiki/BMP_file_format) formátumú képfájlformátumban.

## <a name="recommended-reading-and-practice"></a>Ajánlott olvasás és gyakorlat

Azt javasoljuk, hogy ismerkedjen meg az egyéni átalakításokkal, ha beolvassa a [kódolást egyéni átalakítással – Rest](custom-preset-rest-howto.md).

## <a name="thumbnail-parameters"></a>Miniatűr paraméterek

A következő paramétereket kell beállítania:

- **Start** – a bemeneti videó pozíciója, ahonnan megkezdheti a miniatűrök generálását. Az érték lehet ISO 8601 formátumú (például PT05S 5 másodpercnél), vagy a keretek száma (például 10 a tizedik keretnél), vagy egy relatív érték az adatfolyam időtartamára (például 10%, hogy a stream időtartamának 10%-ában kezdődjön). A egy {Best} nevű makrót is támogat, amely azt jelzi, hogy a kódoló kijelöli a legjobb miniatűrt a videó első néhány másodpercében, és csak egy miniatűrt hoz létre, függetlenül attól, hogy milyen egyéb beállítások tartoznak a lépéshez és a tartományhoz. Az alapértelmezett érték a Macro {Best}.
- **lépés** – a bélyegképek generálásának intervallumai. Az érték ISO 8601 formátumú lehet (például 5 másodpercenként egy kép PT05S), vagy a keretek száma (például 30 képkockával 30 keretnél), vagy relatív érték az adatfolyam időtartamára (például 10% az egyik rendszerképhez a stream időtartamának 10%-ában). A lépés értéke hatással lesz az első generált miniatűrre, ami nem feltétlenül egyezik meg a beállított indítási időpontnál megadott értékkel. Ennek oka a kódoló, amely az első kimenetként a kezdő időpont és a lépés pozíciója közötti legjobb miniatűrt próbálja kiválasztani. Mivel az alapértelmezett érték 10%, az azt jelenti, hogy ha az adatfolyam hosszú ideig tart, az első generált miniatűr a kezdési időponttól távol lehet. Próbáljon meg egy ésszerű értéket választani a lépéshez, ha az első miniatűr várhatóan a kezdési időponthoz közeledik, vagy állítsa a tartomány értékét 1-re, ha a kezdéskor csak egy miniatűrre van szükség.
- **Range (tartomány** ) – a bemeneti videóban az előre beállított indítási időponthoz viszonyított pozíció, amelyen a miniatűrök generálásának leállítása megszakítható. Az érték ISO 8601 formátumú lehet (például a PT5M30S 5 percen belül, 30 másodperccel a kezdési időponttól), vagy a keretek száma (például 300, hogy a keretből leállítsa a 300th keretet a kezdő időpontnál. Ha ez az érték 1, az azt jelenti, hogy csak egy miniatűrt állít elő a kezdési időpontnál, vagy egy relatív értéket az adatfolyam időtartamára (például 50%, hogy leállítsa a stream időtartamának felét a kezdési időponttól számítva). Az alapértelmezett érték 100%, ami azt jelenti, hogy a stream végén le kell állítani.
- **rétegek** – a kódoló által előállított kimeneti képrétegek gyűjteménye.

## <a name="example-of-a-single-png-file-preset"></a>Példa az "egyetlen PNG-fájl" készletre

A következő JSON-beállításkészlet használatával egyetlen kimeneti PNG-fájl hozható létre a bemeneti videó első néhány másodpercében, ahol a kódoló a legjobb erőfeszítést tesz a "érdekes" keret megtalálásához. Vegye figyelembe, hogy a kimeneti képméret 100%-ra van állítva, ami azt jelenti, hogy ezek egyeznek a bemeneti videó méreteivel. Azt is vegye figyelembe, hogy a "kimenetek" formátuma beállításnak meg kell egyeznie a "PngLayers" a "kodekek" szakaszban való használatával.  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Példa "JPEG-képek sorozata" készletre

A következő JSON-készlettel 10 lemezképet hozhat létre a bemeneti ütemterv 5%-os, 15%-os,..., 95%-os időbélyegzővel, ahol a képméretet a bemeneti videó egynegyedének kell megadnia.

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

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Példa az "egy rendszerkép egy adott időbélyegen" beállításra

A következő JSON-beállításkészlet használatával egyetlen JPEG-képet hozhat létre a bemeneti videó 30 másodperces jelölése alapján. Ez az előre beállított érték 30 másodpercnél hosszabb időt vesz igénybe a bemeneti videóban (máskülönben a feladatok meghiúsulnak).

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

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Példa a "miniatűrök a különböző felbontásokban" beállításra

A következő beállításkészlet használatával különböző felbontásban hozhatja elő a miniatűröket egy adott feladatban. A példában az 5%, 15%,..., a bemeneti idősor 95% 100-ában a kódoló két rendszerképet hoz létre: a bemeneti videó felbontásának és a másiknak a (z) 50%-ban.

Jegyezze fel a {Resolution} makró használatát a fájlnévben. azt jelzi, hogy a kódoló azt a szélességet és magasságot használja, amelyet az beállításkészlet kódolás szakaszában adott meg, miközben létrehozza a kimeneti lemezképek fájlnevét. Ez megkönnyíti a különböző rendszerképek megkülönböztetését is.

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

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Példa a miniatűr generálására kódolás közben

Noha az összes fenti példa azt ismerteti, hogyan küldhet olyan kódolási feladatot, amely csak képeket hoz létre, a videó/hang kódolását a miniatűr generálásával is kombinálhatja. A következő JSON-beállításkészlet azt mutatja be, hogy a kódoló standard létrehozza a miniatűrt a kódolás során.

### <a name="json-preset"></a>JSON-készlet

A sémával kapcsolatos további információkért tekintse meg [ezt a](../previous/media-services-mes-schema.md) cikket.

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

## <a name="next-steps"></a>Következő lépések
[Miniatűrök készítése a .NET használatával](media-services-generate-thumbnails-dotnet.md)