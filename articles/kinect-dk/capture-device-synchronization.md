---
title: Az Azure Kinect-eszközök szinkronizálásának rögzítése
description: Ismerje meg, hogyan szinkronizálhat Azure Kinect Capture-eszközöket az Azure Kinect Sensor SDK használatával.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, szenzor, SDK, mélység, RGB, belső, külső, szinkronizálás, százszorszép lánc, fázis eltolása
ms.openlocfilehash: 914a29882277c11c388d3f81f0b1fe827fc2f156
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277300"
---
# <a name="capture-azure-kinect-device-synchronization"></a>Az Azure Kinect-eszközök szinkronizálásának rögzítése

Az Azure Kinect-hardver képes a színek és a részletes képek rögzítési idejének igazítására. Az ugyanazon az eszközön lévő kamerák közötti igazítás **belső szinkronizálás**. Az idő igazításának rögzítése több csatlakoztatott eszközön **külső szinkronizálás**.

## <a name="device-internal-synchronization"></a>Eszköz belső szinkronizálása

Az egyes kamerák közötti képrögzítés a hardverben szinkronizálva van. Minden olyan [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html) , amely a szín és a mélység érzékelőből származó képeket tartalmaz, a lemezképek időbélyege a hardver működési módjától függően igazodik. Alapértelmezés szerint a rögzítés lemezképe az expozíció középpontja. A mélység és a SZÍNRÖGZÍTÉS relatív időzítését a `depth_delay_off_color_usec` [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html)mezője alapján lehet módosítani.

## <a name="device-external-synchronization"></a>Eszköz külső szinkronizálása

Lásd: [külső szinkronizálás beállítása](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices) a hardver telepítéséhez.

Az egyes csatlakoztatott eszközökhöz tartozó szoftvereket **fő** vagy **alárendelt** módban való működésre kell konfigurálni. Ez a beállítás a [k4a_device_configuration_ton](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html)van konfigurálva.

Külső szinkronizálás használatakor az alárendelt kamerákat mindig el kell indítani, mielőtt a főkiszolgáló helyesen igazítja az időbélyeget.

### <a name="subordinate-mode"></a>Alárendelt mód

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>Fő mód

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>A szinkronizálási Jack állapotának beolvasása

A szinkronizálási bemeneti és szinkronizálási kimeneti csatlakozók aktuális állapotának programozott beolvasásához használja a [k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962) függvényt.

## <a name="next-steps"></a>További lépések

Most már tudja, hogyan engedélyezheti és rögzítheti az eszközök szinkronizálását. Azt is megtekintheti, hogyan használhatja a 

>[!div class="nextstepaction"]
>[Azure Kinect Sensor SDK-rekord és lejátszási API](record-playback-api.md)
