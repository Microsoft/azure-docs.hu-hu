---
title: Azure Kinect DK-felvevő
description: Ismerje meg, hogyan rögzíthet adatfolyamokat az érzékelő SDK-ból egy fájlba az Azure Kinect Recorder használatával.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Record, lejátszás, olvasó, Matroska, MKV, streamek, mélység, RGB, kamera, szín, IMU, hang
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277303"
---
# <a name="azure-kinect-dk-recorder"></a>Azure Kinect DK-felvevő

Ez a cikk azt ismerteti, hogy a `k4arecorder` parancssori segédprogram használatával hogyan rögzíthet adatstreameket az érzékelő SDK-ból egy fájlba.

>[!NOTE]
>Az Azure Kinect Recorder nem rögzíti a hangot.

## <a name="recorder-options"></a>Rögzítő beállításai

A `k4arecorder` különböző parancssori argumentumokkal határozza meg a kimeneti fájlt és a rögzítési módokat.

A felvételek a [Matroska. mkv formátumban](record-file-format.md)tárolódnak. A rögzítés több videós sávot használ a szín és a mélység számára, továbbá további információkat, például a kamera kalibrálását és a metaadatokat.

```console
k4arecorder [options] output.mkv

 Options:
  -h, --help              Prints this help
  --list                  List the currently connected K4A devices
  --device                Specify the device index to use (default: 0)
  -l, --record-length     Limit the recording to N seconds (default: infinite)
  -c, --color-mode        Set the color sensor mode (default: 1080p), Available options:
                            3072p, 2160p, 1536p, 1440p, 1080p, 720p, 720p_NV12, 720p_YUY2, OFF
  -d, --depth-mode        Set the depth sensor mode (default: NFOV_UNBINNED), Available options:
                            NFOV_2X2BINNED, NFOV_UNBINNED, WFOV_2X2BINNED, WFOV_UNBINNED, PASSIVE_IR, OFF
  --depth-delay           Set the time offset between color and depth frames in microseconds (default: 0)
                            A negative value means depth frames will arrive before color frames.
                            The delay must be less than 1 frame period.
  -r, --rate              Set the camera frame rate in Frames per Second
                            Default is the maximum rate supported by the camera modes.
                            Available options: 30, 15, 5
  --imu                   Set the IMU recording mode (ON, OFF, default: ON)
  --external-sync         Set the external sync mode (Master, Subordinate, Standalone default: Standalone)
  --sync-delay            Set the external sync delay off the master camera in microseconds (default: 0)
                            This setting is only valid if the camera is in Subordinate mode.
  -e, --exposure-control  Set manual exposure value (-11 to 1) for the RGB camera (default: auto exposure)
```

## <a name="record-files"></a>Fájlok rögzítése

1. példa. Jegyezze fel a NFOV unbinned (640x576) üzemmódot, amely RGB 1080p, 30 fps, IMU.
A rögzítés leállításához nyomja le a **CTRL-C** billentyűket.

```
k4arecorder.exe output.mkv
```

2. példa. WFOV nem dobozolni (1MP), RGB 3072p 15 fps-nél, IMU nélkül, 10 másodpercig.

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

3. példa. Rögzítse a WFOV 2x2 dobozolni-t 5 másodpercig 30 fps-re, és mentse a kimenet. mkv fájlba.

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>Az [Azure Kinect Viewer](azure-kinect-viewer.md) használatával a rögzítés előtt konfigurálhat RGB kamera-vezérlőket (például a kézi fehéregyensúly beállításához).

## <a name="verify-recording"></a>Rögzítés ellenőrzése

A kimeneti. mkv fájlt megnyithatja az [Azure Kinect Viewer](azure-kinect-viewer.md)használatával.

A zeneszámok kinyeréséhez vagy a fájlinformációk megtekintéséhez az eszközök, például `mkvinfo` a [MKVToolNix](https://mkvtoolnix.download/) Toolkit részeként érhetők el.

## <a name="next-steps"></a>Következő lépések

[Külső szinkronizált egységekkel rendelkező felvevő használata](record-external-synchronized-units.md)