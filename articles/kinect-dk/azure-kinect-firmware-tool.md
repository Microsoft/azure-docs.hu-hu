---
title: Azure Kinect Firmware Tool
description: Ismerje meg, hogyan kérdezheti le és frissítheti az eszköz belső vezérlőprogramja az Azure Kinect firmware eszköz használatával.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, belső vezérlőprogram, frissítés
ms.openlocfilehash: f8b89751d27fb5a4b18d635f45f63f4f36bd05f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277311"
---
# <a name="azure-kinect-dk-firmware-tool"></a>Azure Kinect DK belső vezérlőprogram eszköz

Az Azure Kinect belső vezérlőprogram eszköz segítségével lekérdezheti és frissítheti az Azure Kinect DK eszközön lévő belső vezérlőprogramot.

## <a name="list-connected-devices"></a>Csatlakoztatott eszközök listázása

A csatlakoztatott eszközök listáját az-l kapcsoló használatával kérheti le.  `AzureKinectFirmwareTool.exe -l`

```console
 == Azure Kinect DK Firmware Tool ==
Found 2 connected devices:
0: Device "000036590812"
1: Device "000274185112"
```

## <a name="check-device-firmware-version"></a>Eszköz belső vezérlőprogram-verziójának keresése

Az első csatlakoztatott eszköz aktuális belső vezérlőprogram-verzióit a-q lehetőség használatával tekintheti meg, például: `AzureKinectFirmwareTool.exe -q` .

```console
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000036590812
Current Firmware Versions:
    RGB camera firmware:      1.5.92
    Depth camera firmware:    1.5.66
    Depth config file:        6109.7
    Audio firmware:           1.5.14
    Build Config:             Production
    Certificate Type:         Microsoft
```

Ha egynél több eszköz van csatlakoztatva, megadhatja a lekérdezni kívánt eszközt úgy, hogy hozzáadja a teljes sorozatszámot a parancshoz, például:

`AzureKinectFirmwareTool.exe -q 000036590812`

## <a name="update-device-firmware"></a>Eszköz belső vezérlőprogram frissítése

Ennek az eszköznek a leggyakoribb használata az eszköz belső vezérlőprogram frissítése. A frissítés elvégzéséhez hívja meg az eszközt a `-u` kapcsoló használatával. A belső vezérlőprogram frissítése néhány percet is igénybe vehet, attól függően, hogy melyik belső vezérlőprogram-fájlokat kell frissíteni.

A belső vezérlőprogram frissítésének részletes utasításait az [Azure Kinect belső vezérlőprogram frissítése](update-device-firmware.md)című részben tekintheti meg.  

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

Ha egynél több eszköz van csatlakoztatva, megadhatja a lekérdezni kívánt eszközt úgy, hogy hozzáadja a teljes sorozatszámot a parancshoz.

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin 000036590812`

## <a name="reset-device"></a>Eszköz alaphelyzetbe állítása

Az-r kapcsolóval egy csatlakoztatott Azure Kinect DK állítható vissza, ha az eszközt ismert állapotba kell beolvasni.

Ha egynél több eszköz van csatlakoztatva, megadhatja a lekérdezni kívánt eszközt úgy, hogy hozzáadja a teljes sorozatszámot a parancshoz.

`AzureKinectFirmwareTool.exe -r 000036590812`

## <a name="inspect-firmware"></a>Belső vezérlőprogram vizsgálata

A belső vezérlőprogram megvizsgálása lehetővé teszi, hogy a tényleges eszköz frissítése előtt letöltse a verziószámot a belső vezérlőprogram-fájlból.

`AzureKinectFirmwareTool.exe -i firmware\AzureKinectDK_Fw_1.5.926614.bin`

```console
 == Azure Kinect DK Firmware Tool ==
Loading firmware package ..\tools\updater\firmware\AzureKinectDK_Fw_1.5.926614.bin.
File size: 1228844 bytes
This package contains:
  RGB camera firmware:      1.5.92
  Depth camera firmware:    1.5.66
  Depth config files: 6109.7 5006.27
  Audio firmware:           1.5.14
  Build Config:             Production
  Certificate Type:         Microsoft
  Signature Type:           Microsoft
```

## <a name="firmware-update-tool-options"></a>A belső vezérlőprogram frissítési eszközének beállításai

```console
 == Azure Kinect DK Firmware Tool ==
* Usage Info *
    AzureKinectFirmwareTool.exe <Command> <Arguments>

Commands:
    List Devices: -List, -l
    Query Device: -Query, -q
        Arguments: [Serial Number]
    Update Device: -Update, -u
        Arguments: <Firmware Package Path and FileName> [Serial Number]
    Reset Device: -Reset, -r
        Arguments: [Serial Number]
    Inspect Firmware: -Inspect, -i
        Arguments: <Firmware Package Path and FileName>

    If no Serial Number is provided, the tool will just connect to the first device.

Examples:
    AzureKinectFirmwareTool.exe -List
    AzureKinectFirmwareTool.exe -Update c:\data\firmware.bin 0123456
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[Részletes útmutató az eszköz belső vezérlőprogram frissítéséhez](update-device-firmware.md)
