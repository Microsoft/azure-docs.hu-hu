---
title: Azure Media Services v3 bemenet metaadat-sémája
description: Ez a cikk áttekintést nyújt Azure Media Services v3 bemeneti metaadat-sémáról.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0acb882410d103cf6f6c34bbecf2006094437b04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634685"
---
# <a name="input-metadata"></a>Bemeneti metaadatok

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A kódolási feladatok olyan bemeneti adategységekhez (vagy eszközökhöz) vannak társítva, amelyeken bizonyos kódolási feladatokat kíván végrehajtani.  Egy feladat befejezése után a rendszer kimeneti eszközt állít elő. A kimeneti eszköz videó-, hang-, miniatűr-, jegyzékfájl-és egyéb fájlokat tartalmaz. 

A kimeneti eszköz egy olyan fájlt is tartalmaz, amely tartalmazza a bemeneti adategység metaadatait. A metaadatok JSON-fájljának neve véletlenszerű AZONOSÍTÓval rendelkezik, ne használja azt a bemeneti adategység azonosítására, amely a kimeneti eszközhöz tartozik. A mezőhöz tartozó bemeneti adategység azonosításához használja a `Uri` mezőt (További információ: [más gyermek elemek](#other-child-elements)).  

Media Services nem megelőző jelleggel a bemeneti eszközöket a metaadatok létrehozásához. A bemeneti metaadatok csak olyan összetevőként jönnek létre, amikor egy bemeneti objektumot dolgoz fel egy feladatban. Ezért ez az összetevő a kimeneti eszközre íródik. A bemeneti eszközök és a kimeneti eszközök metaadatainak létrehozásához különböző eszközök használhatók. Ezért a bemeneti metaadatok némileg eltérő sémával rendelkeznek, mint a kimeneti metaadatok.

Ez a cikk a JSON-séma azon elemeit és típusait ismerteti, amelyeken a bemeneti metada ( &lt; asset_id &gt;_metadata.js) alapul. További információ a kimeneti eszköz metaadatait tartalmazó fájlról: [kimeneti metaadatok](output-metadata-schema.md).  

A JSON-séma példáját a cikk végén találja.  

## <a name="assetfile"></a>AssetFile  

A kódolási feladatokhoz tartozó AssetFile-elemek gyűjteményét tartalmazza.  

> [!NOTE]
> A következő négy alárendelt elemnek egy sorozatban kell megjelennie.

| Név  | Leírás |
| --- | --- | 
| **VideoTracks**|Az egyes fizikai adatfájlok tartalmazhatnak nulla vagy több, a megfelelő tároló formátumba felhasználható videókat. További információ: [VideoTracks](#videotracks). |
| **AudioTracks**|Az egyes fizikai adatfájlok tartalmazhatnak nulla vagy több, a megfelelő tároló formátumba felhasználható hangsávot. További információ: [AudioTracks](#audiotracks) |
| **Metaadatok**  |Az adatfájl metaadatai key\value karakterláncként jelennek meg. <br />Például: `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>Egyéb alárendelt elemek

| Név | Leírás |
| --- | --- |
| **Név**<br />Kötelező |Az eszköz fájljának neve. <br /><br />Például: `"Name": "Ignite-short.mp4"` |
| **URI**<br />Kötelező |Az URL-cím, ahol a bemeneti eszköz található. A kimeneti eszközhöz tartozó bemeneti eszköz azonosításához használja az `Uri` azonosító helyett a mezőt.|
| **Méret**<br />Kötelező |Az adategység fájljának mérete bájtban megadva.  <br /><br />Például: `"Size": 75739259`|
| **Időtartam**<br />Kötelező |Tartalom lejátszási ideje – időtartam. <br /><br />Példa: `"Duration": "PT1M10.304S"`. |
| **NumberOfStreams**<br />Kötelező |Az adatfájlban lévő adatfolyamok száma.  <br /><br />Például: `"NumberOfStreams": 2`|
| **FormatNames**<br />Kötelező |Formátumnevek.  <br /><br />Például: `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **FormatVerboseName**<br /> Kötelező |Részletes nevek formázása <br /><br />Például: `"FormatVerboseName": "QuickTime / MOV"` |
| **StartTime** |Tartalom kezdési ideje  <br /><br />Például: `"StartTime": "PT0S"` |
| **OverallBitRate** |Az adategység-fájl átlagos bitrátája bit/másodpercben.  <br /><br />Például: `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| Név | Leírás |
| --- | --- |
| **FourCC**<br />Kötelező |Az FFmpeg által jelentett video codec FourCC-kód.<br /><br />Például: `"FourCC": "avc1" | "hev1" | "hvc1"` |
| **Profil** |A Video Track profilja. <br /><br />Például: `"Profile": "Main"`|
| **Szintű** |A videó követési szintje. <br /><br />Például: `"Level": "3.2"`|
| **PixelFormat** |A Video Track képpontjának formátuma <br /><br />Például: `"PixelFormat": "yuv420p"`|
| **Szélesség**<br />Kötelező |Kódolt videó szélessége képpontban megadva <br /><br />Például: `"Width": "1280"`|
| **Magasság**<br />Kötelező |Kódolt videó magassága képpontban megadva.<br /><br />Például: `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />Kötelező |Videó megjelenítési oldalarányának számlálója<br /><br />Például: `"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominator**<br />Kötelező |Videó megjelenítési méretarányának nevezője <br /><br />Például: `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |Videó minta oldalarányának számlálója <br /><br />Például: `"SampleAspectRatioNumerator": 1.0`|
| **SampleAspectRatioDenominator**|Például: `"SampleAspectRatioDenominator": 1.0`|
| **Frameráta**<br />Kötelező |A képkockák sebességét. 3F formátumban mérjük. <br /><br />Például: `"FrameRate": 29.970`|
| **Sávszélességű** |Az adatfájlból kiszámított, bit/másodpercben mért átlagos video-átviteli sebesség. A rendszer csak az elemes adatfolyam-adattartalmat számítja ki, és a csomagolási terhelés nem szerepel benne. <br /><br />Például: `"Bitrate": 8421583`|
| **HasBFrames** |A B képkockák számának nyomon követése. <br /><br />Például: `"HasBFrames": 2`|
| **Metaadatok** |Általános kulcs/érték karakterláncok, amelyek különféle információk tárolására használhatók. <br />Tekintse meg a cikk végén található teljes példát. |
| **ID**<br />Kötelező |A hang-vagy videó nyomon követésének nulla alapú indexe.<br /><br /> Ez az **azonosító** nem feltétlenül az MP4-fájlban használt TrackID. <br /><br />Például: `"Id": 2`|
| **Codec** |Videó Track kodek karakterlánca <br /><br />Például: `"Codec": "h264 | hev1"`|
| **CodecLongName** |Hang-vagy Video Track-kodek hosszú neve. <br /><br />Például: `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **Codec** |Videó Track kodek karakterlánca <br /><br />Például: `"Codec": "h264 | hev1"`|
| **Időalap**<br />Kötelező |Idő alapja.<br /><br />Például: `"TimeBase": "1/30000"`|
| **NumberOfFrames** |Képkockák száma (a videó sávok esetében). <br /><br />Például: `"NumberOfFrames": 2107`|
| **StartTime** |A kezdési idő nyomon követése.<br /><br />Például: `"StartTime": "PT0.033S"` |
| **Időtartam** |Követés időtartama. <br /><br />Például: `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTracks

| Név  | Leírás |
| --- | --- | 
| **SampleFormat** |Minta formátuma <br /><br />Például: `"SampleFormat": "fltp"`|
| **ChannelLayout** |Csatorna elrendezése <br /><br />Például: `"ChannelLayout": "stereo"`|
| **Csatornák**<br />Kötelező |Az audio csatornák száma (0 vagy több). <br /><br />Például: `"Channels": 2`|
| **SamplingRate**<br />Kötelező |Mintavételezési sebesség (minta/mp vagy Hz) <br /><br />Például: `"SamplingRate": 48000`|
| **Sávszélességű** |Az adatfájlból kiszámított bit/másodperc átlagos hangátviteli sebessége. A rendszer csak az elemes adatfolyam-adattartalmat számítja ki, és a csomagolási terhelés nem szerepel ebben a számban. <br /><br />Például: `"Bitrate": 192080`|
| **Metaadatok** |Általános kulcs/érték karakterláncok, amelyek különféle információk tárolására használhatók.  <br />Tekintse meg a cikk végén található teljes példát. |
| **ID**<br />Kötelező |A hang-vagy videó nyomon követésének nulla alapú indexe.<br /><br /> Ez nem feltétlenül jelenti azt, hogy az MP4-fájlban használt TrackID. <br /><br />Például: `"Id": 1`|
| **Codec** |Videó Track kodek karakterlánca <br /><br />Például: `"Codec": "aac"`|
| **CodecLongName** |Hang-vagy Video Track-kodek hosszú neve. <br /><br />Például: `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **Időalap**<br />Kötelező |Idő alapja.<br /><br />Például: `"TimeBase": "1/48000"` |
| **NumberOfFrames** |Képkockák száma (a videó sávok esetében). <br /><br />Például: `"NumberOfFrames": 3294`|
| **StartTime** |A kezdési idő nyomon követése. További információ: [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html). <br /><br />Például: `"StartTime": "PT0S"` |
| **Időtartam** |Követés időtartama. <br /><br />Például: `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>Metaadatok

| Név | Leírás |
| --- | --- |
| **kulcs**<br />Kötelező |A kulcs/érték párokban található kulcs. |
| **value**<br /> Kötelező |A kulcs/érték párokban szereplő érték. |

## <a name="schema-example"></a>Séma – példa

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>Következő lépések

[Kimeneti metaadatok](output-metadata-schema.md)
