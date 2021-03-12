---
title: Azure Media Services kimeneti metaadatok sémája
description: Ez a cikk áttekintést nyújt Azure Media Services v3 kimeneti metaadat-sémáról.
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
ms.openlocfilehash: f26905366949c2c198e52f78bc7adb734cbb7f90
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612456"
---
# <a name="output-metadata"></a>Kimeneti metaadatok

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A kódolási feladatok olyan bemeneti adategységekhez (vagy eszközökhöz) vannak társítva, amelyeken bizonyos kódolási feladatokat kíván végrehajtani. Például kódoljon egy MP4-fájlt H. 264 MP4 adaptív sávszélességű készletbe; miniatűr létrehozása; hozzon létre átfedéseket. Egy feladat befejezése után a rendszer kimeneti eszközt állít elő.  A kimeneti eszköz videó-, hang-, miniatűr-és egyéb fájlokat tartalmaz. A kimeneti eszköz egy olyan fájlt is tartalmaz, amely tartalmazza a kimeneti eszköz metaadatait. A metaadatok JSON-fájljának neve a következő formátumú: `<source_file_name>_manifest.json` (például `BigBuckBunny_manifest.json` ). A forrás fájlnév (csonkítás nélkül) megkereséséhez ellenőrizze, hogy a * _metadata.jsbe van-e kapcsolva, és lekérdezi-e a filepath karakterláncot.

Media Services nem megelőző jelleggel a bemeneti eszközöket a metaadatok létrehozásához. A bemeneti metaadatok csak olyan összetevőként jönnek létre, amikor egy bemeneti objektumot dolgoz fel egy feladatban. Ezért ez az összetevő a kimeneti eszközre íródik. A bemeneti eszközök és a kimeneti eszközök metaadatainak létrehozásához különböző eszközök használhatók. Ezért a bemeneti metaadatok némileg eltérő sémával rendelkeznek, mint a kimeneti metaadatok.

Ez a cikk a JSON-séma azon elemeit és típusait ismerteti, amelyeken a kimeneti metaadatok ( &lt; source_file_name &gt;_manifest.js) alapulnak. <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

A teljes séma kód és a JSON-példa a cikk végén található.  

## <a name="assetfile"></a>AssetFile

A kódolási feladatokhoz tartozó AssetFile-bejegyzések gyűjteménye.  

| Név | Leírás |
| --- | --- |
| **Források** |A AssetFile létrehozásához feldolgozott bemeneti/forrás médiafájlok gyűjteménye.<br />Például: `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|Minden fizikai AssetFile tartalmazhatnak nulla vagy több, a megfelelő tároló formátumba felhasználható videó-zeneszámot. <br />Lásd: [VideoTracks](#videotracks). |
| **AudioTracks**|Minden fizikai AssetFile tartalmazhatnak nulla vagy több hangsávot a megfelelő tároló formátumba. Ezen hangsávok gyűjteménye.<br /> További információ: [AudioTracks](#audiotracks). |
| **Név**<br />Kötelező |A Media Asset fájljának neve. <br /><br />Például: `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **Méret**<br />Kötelező |Az adategység fájljának mérete bájtban megadva. <br /><br />Például: `"Size": 32414631`|
| **Időtartam**<br />Kötelező |Tartalom lejátszási ideje – időtartam. További információ: [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html) -formátum. <br /><br />Például: `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

Minden fizikai AssetFile tartalmazhatnak nulla vagy több, a megfelelő tároló formátumba felhasználható videó-zeneszámot. A **VideoTracks** elem az összes videó sáv gyűjteményét jelöli.  

| Név | Leírás |
| --- | --- |
| **ID**<br /> Kötelező |A videó nyomon követésének nulla alapú indexe. **Megjegyzés:**  Ez az **azonosító** nem feltétlenül az MP4-fájlban használt TrackID. <br /><br />Például: `"Id": 1`|
| **FourCC**<br />Kötelező | Az FFmpeg által jelentett video codec FourCC-kód.  <br /><br />Például: `"FourCC": "avc1" | "hev1" | "hvc1"`|
| **Profil** |H264-profil (csak H264-kodekre alkalmazható)  <br /><br />Például: `"Profile": "High"` |
| **Szintű** |H264-szint (csak H264-kodekre alkalmazható).  <br /><br />Például: `"Level": "3.2"`|
| **Szélesség**<br />Kötelező |Kódolt videó szélessége képpontban megadva  <br /><br />Például: `"Width": "1280"`|
| **Magasság**<br />Kötelező |Kódolt videó magassága képpontban megadva.  <br /><br />Például: `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />Kötelező|Videó megjelenítési oldalarányának számlálója  <br /><br />Például: `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominator**<br />Kötelező |Videó megjelenítési méretarányának nevezője  <br /><br />Például: `"DisplayAspectRatioDenominator": 9.0`|
| **Képkockasebesség**<br />Kötelező |A képkockák sebességét. 3F formátumban mérjük.  <br /><br />Például: `"Framerate": 29.970`|
| **Sávszélességű**<br />Kötelező |A bitek másodpercenkénti átlagos átviteli sebessége a AssetFile számítva. Csak az elemes adatfolyam-adattartalmat számolja, és nem tartalmazza a csomagolási terhelést.  <br /><br />Például: `"Bitrate": 3551567`|
| **TargetBitrate**<br />Kötelező |A videó nyomon követésére irányuló átlagos bitráta, amelyet a rendszer az előre megadott kódolási készleten keresztül kér a bit/másodpercben. <br /><br />Például: `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTracks 

Minden fizikai AssetFile tartalmazhatnak nulla vagy több hangsávot a megfelelő tároló formátumba. A **AudioTracks** elem az összes hangsávok gyűjteményét jelöli.  

| Név  | Leírás |
| --- | --- |
| **ID**<br />Kötelező  |A hangsávok nulla alapú indexe. **Megjegyzés:**  Ez nem feltétlenül az MP4-fájlokban használt TrackID.  <br /><br />Például: `"Id": 2`|
| **Codec**  |Hangsávok kodekének karakterlánca  <br /><br />Például: `"Codec": "aac"`|
| **Nyelv**|Például: `"Language": "eng"`|
| **Csatornák**<br />Kötelező|Hangcsatornák száma  <br /><br />Például: `"Channels": 2`|
| **SamplingRate**<br />Kötelező |Mintavételezési sebesség (minta/mp vagy Hz)  <br /><br />Például: `"SamplingRate": 48000`|
| **Sávszélességű**<br />Kötelező |A bitek másodpercenkénti átlagos átviteli sebessége a AssetFile számítva. Csak az elemes adatfolyam-adattartalmat számolja, és nem tartalmazza a csomagolási terhelést.  <br /><br />Például: `"Bitrate": 128041`|

## <a name="json-schema-example"></a>Példa JSON-sémára

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>Következő lépések

[Feladathoz tartozó bemenet létrehozása HTTPS URL-címről](job-input-from-http-how-to.md)
