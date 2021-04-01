---
title: Az Azure Kinect DK belső vezérlőprogram frissítése
description: Ismerje meg, hogyan frissítheti az Azure Kinect DK eszköz belső vezérlőprogramot az Azure Kinect firmware eszköz használatával.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, belső vezérlőprogram, frissítés, helyreállítás
ms.openlocfilehash: 71557798a584635356cc47fd7c4a0309df4d018a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94356226"
---
# <a name="update-azure-kinect-dk-firmware"></a>Az Azure Kinect DK belső vezérlőprogram frissítése

Ez a dokumentum útmutatást nyújt az eszköz belső vezérlőprogram Azure Kinect DK-ben való frissítéséhez.

Az Azure Kinect DK nem frissíti automatikusan a belső vezérlőprogram szolgáltatását. Az [Azure Kinect belső vezérlőprogram eszköz](azure-kinect-firmware-tool.md) használatával manuálisan frissítheti a belső vezérlőprogramot a legújabb elérhető verzióra.

## <a name="prepare-for-firmware-update"></a>Felkészülés a belső vezérlőprogram frissítésére

1. [SDK letöltése](sensor-sdk-download.md).
2. Telepítse az SDK-t.
3. Az SDK telepítési helye (SDK telepítési helye) területen keresse meg a következő \tools\:

    - AzureKinectFirmwareTool.exe
    - Egy belső vezérlőprogram. bin fájl a belső vezérlőprogram mappában, például *AzureKinectDK_Fw_1.5.926614. bin*.

4. Az eszköz csatlakoztatása a SZÁMÍTÓGÉPekhez és a hálózatra kapcsolása is.

> [!IMPORTANT]
> A belső vezérlőprogram frissítése közben tartsa a kapcsolatot az USB-vel és az áramellátással. Ha a frissítés során nem távolítja el a csatlakozást, a belső vezérlőprogram sérült állapotba kerül.

## <a name="update-device-firmware"></a>Eszköz belső vezérlőprogram frissítése

1. Nyisson meg egy parancssort a (SDK telepítési hely) \tools\ mappában.
2. A belső vezérlőprogram frissítése az Azure Kinect firmware eszköz használatával

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    Példa:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. Várjon, amíg a belső vezérlőprogram frissítése befejeződik. A képmérettől függően néhány percet is igénybe vehet.

### <a name="verify-device-firmware-version"></a>Eszköz belső vezérlőprogram-verziójának ellenőrzése

1. Ellenőrizze a belső vezérlőprogram frissítését.

    `AzureKinectFirmwareTool.exe -q`

2. Tekintse meg a következő példát.

    ```console
       >AzureKinectFirmwareTool.exe -q
       == Azure Kinect DK Firmware Tool ==
        Device Serial Number: 000805192412
        Current Firmware Versions:
        RGB camera firmware:      1.6.102
        Depth camera firmware:    1.6.75
        Depth config file:        6109.7
        Audio firmware:           1.6.14
        Build Config:             Production
        Certificate Type:         Microsoft
    ```

3. Ha a fenti kimenet látható, a rendszer frissíti a belső vezérlőprogram frissítését.

4. A belső vezérlőprogram frissítése után az [Azure Kinect Viewert](azure-kinect-viewer.md) futtatva ellenőrizheti, hogy minden érzékelő a várt módon működik-e.

## <a name="troubleshooting"></a>Hibaelhárítás

A belső vezérlőprogram frissítései több okból is sikertelenek lehetnek. Ha egy belső vezérlőprogram frissítése sikertelen, próbálja meg a következő kockázatcsökkentő lépéseket:

1. Próbálja meg másodszor futtatni a belső vezérlőprogram frissítési parancsát.

2. Erősítse meg, hogy az eszköz továbbra is csatlakoztatva van a belső vezérlőprogram verziójának lekérdezésével.        AzureKinectFirmareTool.exe

3. Ha az összes kötél meghibásodik, kövesse a [helyreállítási](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) lépéseket a gyári belső vezérlőprogram visszaállításához, majd próbálkozzon újra.

További problémák: [Microsoft támogatási lapok](./index.yml)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[Azure Kinect belső vezérlőprogram eszköz](azure-kinect-firmware-tool.md)