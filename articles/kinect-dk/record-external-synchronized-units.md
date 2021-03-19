---
title: Külső szinkronizált eszközök használata az Azure Kinect Recorder használatával
description: Megtudhatja, hogyan rögzíthet adatok külső szinkronizálásra konfigurált eszközökről az Azure Kinect Recorder használatával.
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, érzékelő, megjelenítő, külső szinkronizálás, fázis késleltetése, mélység, RGB, kamera, hangkábel, felvevő
ms.openlocfilehash: 052f6f1ac9f90e764de25d1d4d1b25b3d50a848d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277272"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>Külső szinkronizált eszközök használata az Azure Kinect Recorder használatával

Ez a cikk útmutatást nyújt arról, hogy az [Azure Kinect Recorder](azure-kinect-recorder.md) hogyan rögzíthet adatkülső szinkronizálással konfigurált eszközöket.

## <a name="prerequisites"></a>Előfeltételek

- [Több Azure Kinect DK-egység beállítása külső szinkronizáláshoz](https://support.microsoft.com/help/4494429).

## <a name="external-synchronization-constraints"></a>Külső szinkronizálási megkötések

- A fő eszközön nem lehet SZINKRONIZÁLni a csatlakoztatott kábelen.
- A fő eszköznek RGB-kamerát kell továbbítania a szinkronizálás engedélyezéséhez.
- Minden egységnek ugyanazt a kamera-konfigurációt kell használnia (frameráta és feloldása).
- Minden egységnek ugyanazt az eszköz belső vezérlőprogramot kell futtatnia ([belső vezérlőprogram](update-device-firmware.md) -utasítások frissítése).
- Az összes alárendelt eszközt el kell indítani a Főeszköz előtt.
- Ugyanezt a kitettségi értéket minden eszközön be kell állítani.
- Minden alárendelt késés a főeszközhöz képest a *főkiszolgálói* beállításnál

## <a name="record-when-each-unit-has-a-host-pc"></a>Rekord, ha minden egység rendelkezik gazdagép-számítógéppel

Az alábbi példában minden eszköz saját dedikált gazdagéptel rendelkezik.
Javasoljuk, hogy az eszközök csatlakoztatását dedikált számítógépekhez az USB-sávszélesség és a CPU/GPU-használat problémáinak megelőzése érdekében.

### <a name="subordinate-1"></a>Alárendelt – 1

1. A felvevő beállítása az első egységhez

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. Az eszköz várakozik

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>Alárendelt – 2

1. A második egységhez tartozó felvevő beállítása

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. Az eszköz várakozik

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>Mester

1. Rögzítés indítása a főkiszolgálón

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. Várjon, amíg a rögzítés befejeződött

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>Rögzítés, ha több egység csatlakozik egy gazdagéphez

Több Azure Kinect-DKs is csatlakozhat egyetlen gazdagép számítógéphez. Azonban az USB-sávszélességre és a gazdagépek számítási feladatokra nagyon nehéz lehet. Az igény csökkentése érdekében:

- Csatlakoztasson minden eszközt a saját USB-állomás vezérlőhöz.
- Olyan hatékony GPU-val rendelkezik, amely képes kezelni a mélységi motort az egyes eszközökön.
- Csak a szükséges érzékelők rögzítése és az alsó frameráta használata.

Mindig először az alárendelt eszközöket és a főkiszolgálót indítsa el.

## <a name="subordinate-1"></a>Alárendelt – 1

1. Felvevő indítása alárendelt

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. Az eszköz várakozik állapotba kerül

## <a name="master"></a>Mester

1. Master eszköz indítása

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. Várakozás a rögzítés befejezésére

## <a name="playing-recording"></a>Rögzítés lejátszása

Az [Azure Kinect Viewer](azure-kinect-viewer.md) használatával visszajátszhatja a rögzítést.



## <a name="tips"></a>Tippek

- A szinkronizált kamerák felvételéhez használjon manuális expozíciót. Az RGB-kamera automatikus expozíciója hatással lehet a szinkronizálás időpontjára.
- Az alárendelt eszköz újraindítása azt eredményezi, hogy a szinkronizálás elvész.
- Néhány [kamera-üzemmód](hardware-specification.md#depth-camera-supported-operating-modes) támogatja a 15 fps max-ot. Azt javasoljuk, hogy ne keverje a módokat és a képkockák sebességét az eszközök között
- Több egység csatlakoztatása egyetlen számítógéphez könnyedén leterhelhető az USB-sávszélesség, érdemes lehet külön gazdagépet használni az eszközön. Ügyeljen a CPU/GPU számítási feladatokra is.
- Tiltsa le a mikrofont és a IMU, ha nincs szükségük a megbízhatóság növelésére.

Az esetleges problémákkal kapcsolatban lásd: [Hibaelhárítás](troubleshooting.md)

## <a name="see-also"></a>Lásd még

- [Külső szinkronizálás beállítása](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- [Azure Kinect-felvevő](azure-kinect-recorder.md) a felvevő beállításaihoz és további információkhoz.
- Az [Azure Kinect Viewer](azure-kinect-viewer.md) a felvételek lejátszásához vagy az RGB kamera tulajdonságainak beállításához nem érhető el a Recorder használatával.
- [Azure Kinect belső vezérlőprogram eszköz](azure-kinect-firmware-tool.md) az eszköz belső vezérlőprogram frissítéséhez.
