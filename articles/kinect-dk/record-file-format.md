---
title: Fájl formátumának rögzítése az Azure Kinect Sensor SDK használatával
description: Ismerje meg, hogyan használható az Azure Kinect Sensor SDK rögzített fájlformátuma.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: Kinect, Azure, szenzor, SDK, mélység, RGB, rekord, lejátszás, Matroska, MKV
ms.openlocfilehash: 3a78ed158c78df1c9a6eadcc0bfb2e40b22750bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501780"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>Fájl formátumának rögzítése az Azure Kinect Sensor SDK használatával

Az érzékelővel kapcsolatos adatok rögzítéséhez a rendszer a Matroska (. mkv) tároló formátumát használja, amely lehetővé teszi, hogy több zeneszámot is tároljon számos kodek használatával. A rögzítési fájl számokat tartalmaz a színek, a mélységek, az IR-képek és a IMU tárolásához.

Az. mkv-tároló formátumának alsó szintű részletei a [Matroska webhelyen](https://www.matroska.org/index.html)találhatók.

| Követés neve | Kodek formátuma                          |
|------------|---------------------------------------|
| SZÍNE      | Mode-Dependent (MJPEG, NV12 vagy YUY2) |
| MÉLYSÉG      | b16g (16 bites szürkeárnyalatos, Big-Endian)   |
| IR         | b16g (16 bites szürkeárnyalatos, Big-Endian)   |
| IMU        | Egyéni struktúra: az alábbi [IMU-minta szerkezete](record-file-format.md#imu-sample-structure) . |

## <a name="using-third-party-tools"></a>Külső gyártótól származó eszközök használata

Az eszközök, például a `ffmpeg` `mkvinfo` [MKVToolNix](https://mkvtoolnix.download/) Toolkit vagy a parancs segítségével megtekintheti és kinyerheti az adatokat a rögzítési fájlokból.

A következő parancs például kibontja a mélységi sávot a 16 bites png-sorozatból ugyanabba a mappába:

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

A `-map 0:1` paraméter kinyeri az 1. számú indexet, amely a legtöbb felvétel mélységét eredményezi. Ha a rögzítés nem tartalmaz színsávot, a rendszer `-map 0:0` használni fogja.

A `-vsync 0` paraméter úgy kényszeríti az FFmpeg-t, hogy kinyerje a képkockákat – ahelyett, hogy a framerátát 30 fps, 15 FPS vagy 5 FPS értékre próbálta volna megfeleltetni.

## <a name="imu-sample-structure"></a>IMU minta szerkezete

Ha a IMU adatok kinyerése a fájlból a lejátszási API használata nélkül történik, az adatok bináris formában jelennek meg.
A IMU-adat szerkezete alább látható. Az összes mező kis endian.

| Mező                        | Típus     |
|------------------------------|----------|
| Gyorsulásmérő timestamp (μs) | uint64   |
| Gyorsulásmérő-adathalmazok (x, y, z) | lebegőpontos [3] |
| Giroszkóp időbélyege (μs)     | uint64   |
| Adatgiroszkópok (x, y, z)     | lebegőpontos [3] |

## <a name="identifying-tracks"></a>Számok azonosítása

Előfordulhat, hogy meg kell határozni, hogy melyik követés tartalmaz színt, mélységet, IR-t stb. A zeneszámok azonosítására akkor van szükség, amikor harmadik féltől származó eszközökkel dolgozik a Matroska-fájl olvasásakor.
A számok követése a kamera módjától és az engedélyezett zeneszámok számától függően változhat. A címkék az egyes számok jelentésének azonosítására szolgálnak.

Az alábbi címkék listája egy adott Matroska-elemhez van csatolva, és felhasználható a megfelelő műsorszám vagy melléklet keresésére.

Ezek a címkék olyan eszközökkel is megtekinthetők, mint a `ffmpeg` és a `mkvinfo` .
A címkék teljes listája a [rekord és lejátszás](record-playback-api.md) oldalon látható.

| Címke neve             | Címke célja             | Címke értéke             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | Színnyomon követés            | Matroska Track UID    |
| K4A_DEPTH_TRACK      | Mélységi sáv            | Matroska Track UID    |
| K4A_IR_TRACK         | IR-követés               | Matroska Track UID    |
| K4A_IMU_TRACK        | IMU követése              | Matroska Track UID    |
| K4A_CALIBRATION_FILE | Kalibrálási melléklet | Melléklet fájlneve   |

## <a name="next-steps"></a>További lépések

[Rögzítés és lejátszás](record-playback-api.md)
