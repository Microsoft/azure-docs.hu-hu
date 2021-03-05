---
title: Azure Kinect Sensor SDK letöltése
description: Ismerje meg, hogyan töltheti le és telepítheti az Azure Kinect Sensor SDK-t Windows és Linux rendszereken.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, frissítés letöltése, legújabb, elérhető, telepítés
ms.openlocfilehash: 591fcba4c887e298cf667c5d95c19184bc213ffe
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179629"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Azure Kinect Sensor SDK letöltése

Ezen a lapon az Azure Kinect Sensor SDK egyes verzióihoz tartozó letöltési hivatkozások találhatók. A telepítő biztosítja az Azure Kinect fejlesztéséhez szükséges összes fájlt.

## <a name="azure-kinect-sensor-sdk-contents"></a>Az Azure Kinect Sensor SDK tartalma

- Fejlécek és könyvtárak az Azure Kinect DK használatával történő alkalmazás létrehozásához.
- Az Azure Kinect DK-t használó alkalmazások számára szükséges újraterjeszthető DLL-ek.
- Az [Azure Kinect Viewer](azure-kinect-viewer.md).
- Az [Azure Kinect-felvevő](azure-kinect-recorder.md).
- Az [Azure Kinect belső vezérlőprogram eszköz](azure-kinect-firmware-tool.md).

## <a name="windows-installation-instructions"></a>Windows telepítési utasítások

[Itt](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md)megtalálhatja az Azure Kinect Sensor SDK és a belső vezérlőprogram legújabb és korábbi verzióinak telepítési részleteit.

[Itt](https://github.com/microsoft/Azure-Kinect-Sensor-SDK)megtalálhatja a forráskódot.

> [!NOTE]
> Az SDK telepítésekor jegyezze fel a telepítési útvonalat. Például: "C:\Program Files\Azure Kinect SDK 1,2". Az ezen az elérési úton található cikkekben hivatkozott eszközöket megtalálja.

## <a name="linux-installation-instructions"></a>Linuxos telepítési utasítások

Jelenleg az egyetlen támogatott disztribúció az Ubuntu 18,04. További terjesztések támogatásának kéréséhez tekintse meg [ezt a lapot](https://aka.ms/azurekinectfeedback).

Először konfigurálnia kell a [Microsoft Package repositoryját](https://packages.microsoft.com/), az [itt](/windows-server/administration/linux-package-repository-for-microsoft-software)található utasításokat követve.

Most már telepítheti a szükséges csomagokat. A `k4a-tools` csomag tartalmazza az [Azure Kinect Viewert](azure-kinect-viewer.md), az [Azure Kinect Recorder](record-sensor-streams-file.md)és az [Azure Kinect belső vezérlőprogram eszközét](azure-kinect-firmware-tool.md). A csomag telepítéséhez futtassa a következőt:

`sudo apt install k4a-tools`
 
Ez a parancs telepíti azokat a függőségi csomagokat, amelyek szükségesek ahhoz, hogy az eszközök megfelelően működjenek, beleértve a legújabb verzióját is `libk4a<major>.<minor>` . A root felhasználó nélkül hozzá kell adnia a udev-szabályokat az Azure Kinect DK eléréséhez. Útmutatásért lásd: [Linux-eszköz beállítása](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md#linux-device-setup). Alternatív megoldásként olyan alkalmazásokat is indíthat, amelyek root-ként használják az eszközt.

A `libk4a<major>.<minor>-dev` csomag tartalmazza a fejléceket és a CMAK-fájlokat, amelyekkel az alkalmazásait/végrehajtható fájljait felépítheti `libk4a` .

A `libk4a<major>.<minor>` csomag tartalmazza azokat a megosztott objektumokat, amelyek a-től függő alkalmazások vagy végrehajtható fájlok futtatásához szükségesek `libk4a` .

Az alapszintű oktatóanyagokhoz szükség van a `libk4a<major>.<minor>-dev` csomagra. A csomag telepítéséhez futtassa a következőt:

`sudo apt install libk4a<major>.<minor>-dev` 

Ha a parancs sikeres, az SDK készen áll a használatra.

Ügyeljen arra, hogy a megfelelő verzióját telepítse a alkalmazással `libk4a<major>.<minor>` `libk4a<major>.<minor>-dev` . Ha például telepíti a `libk4a4.1-dev` csomagot, telepítse a megfelelő `libk4a4.1` csomagot, amely tartalmazza a megosztott objektumok egyező verzióját. A legújabb verziójával `libk4a` kapcsolatban tekintse meg a következő szakaszban található hivatkozásokat.

## <a name="change-log-and-older-versions"></a>A napló és a régebbi verziók módosítása

[Itt](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md)megtalálhatja az Azure Kinect Sensor SDK változási naplóját.

Ha az Azure Kinect Sensor SDK régebbi verziójára van szüksége, keresse meg [itt](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Következő lépések

[Az Azure Kinect DK beállítása](set-up-azure-kinect-dk.md)
