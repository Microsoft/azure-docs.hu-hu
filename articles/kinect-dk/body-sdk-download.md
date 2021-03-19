---
title: Azure Kinect Body Tracking SDK letöltése
description: Ismerje meg, hogyan töltheti le az Azure Kinect Sensor SDK egyes verzióit Windows vagy Linux rendszeren.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: Azure, Kinect, SDK, frissítés letöltése, legújabb, elérhető, telepítés, törzs, nyomon követés
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654492"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Az Azure Kinect Body Tracking SDK letöltése

Ez a dokumentum az Azure Kinect Body Tracking SDK egyes verzióinak telepítésére mutató hivatkozásokat tartalmaz.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Az Azure Kinect Body Tracking SDK tartalma

- Fejlécek és kódtárak egy Body Tracking-alkalmazás létrehozásához az Azure Kinect DK használatával.
- A Body Tracking Applications által az Azure Kinect DK használatával szükséges újraterjeszthető DLL-ek.
- Minta törzs nyomon követésére szolgáló alkalmazások.

## <a name="windows-download-links"></a>Windows letöltési hivatkozások

Verzió       | Letöltés
--------------|----------
1.1.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100942) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100848) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)

## <a name="linux-installation-instructions"></a>Linuxos telepítési utasítások

Jelenleg az egyetlen támogatott disztribúció az Ubuntu 18,04 és a 20,04. További terjesztések támogatásának kéréséhez tekintse meg [ezt a lapot](https://aka.ms/azurekinectfeedback).

Először konfigurálnia kell a [Microsoft Package repositoryját](https://packages.microsoft.com/), az [itt](/windows-server/administration/linux-package-repository-for-microsoft-software)található utasításokat követve.

A `libk4abt<major>.<minor>-dev` csomag tartalmazza a felépíteni kívánt fejléceket és CMAK-fájlokat `libk4abt` .
A `libk4abt<major>.<minor>` csomag tartalmazza a végrehajtható fájlok futtatásához szükséges megosztott objektumokat, valamint `libk4abt` a példa megjelenítőjét.

Az alapszintű oktatóanyagokhoz szükség van a `libk4abt<major>.<minor>-dev` csomagra. A telepítéséhez futtassa a következőt

`sudo apt install libk4abt<major>.<minor>-dev`

Ha a parancs sikeres, az SDK készen áll a használatra.

> [!NOTE]
> Az SDK telepítésekor jegyezze fel a telepítési útvonalat. Például: "C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0". Az ezen az elérési úton található cikkekben hivatkozott mintákat fogja megtalálni.
> A szövegtörzs-követési minták az Azure-Kinect-Samples adattár [Body-Tracking-Samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) mappájában találhatók. A cikkekben hivatkozott mintákat itt találja.

## <a name="change-log"></a>Változási napló

### <a name="v110"></a>v 1.1.0
* Vonás Adja hozzá a DirectML (csak Windows) és a TensorRT-végrehajtás támogatását a póz-becslési modellhez. Lásd: gyakori kérdések az új végrehajtási környezetekről.
* Vonás Hozzáadás `model_path` a `k4abt_tracker_configuration_t` struct-hoz. Lehetővé teszi a felhasználók számára, hogy megadják a póz-becslési modell elérési útját. Az alapértelmezett érték a `dnn_model_2_0_op11.onnx` standard az aktuális könyvtárban található becslési modell.
* Vonás Tartalmazza a `dnn_model_2_0_lite_op11.onnx` Lite póz becslési modelljét. Ez a modell a ~ 5%-os pontosságú teljesítmény növelését eredményezi.
* Vonás Az ellenőrzött minták a Visual Studio 2019-es verziójával és a jelen kiadás használatához szükséges frissítési mintákkal vannak lefordítva.
* [A változás megszakítása] Frissítsen a ONNX Runtime 1,6-re a CPU, a CUDA 11,1, a DirectML (csak Windows) és a TensorRT 7.2.1 végrehajtási környezetek támogatásával. Az NVIDIA-illesztőprogram frissítését igényli a R455 vagy annál jobb.
* [A változás megszakítása] Nincs NuGet-telepítés.
* [Hibajavítás] Az NVIDIA RTX 30xx sorozat GPU- [hivatkozásának](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481) támogatása
* [Hibajavítás] Támogatás hozzáadása az AMD és az Intel integrált GPU-hoz (csak Windows) [hivatkozás](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [Hibajavítás] Frissítés a CUDA 11,1 [hivatkozásra](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125)
* [Hibajavítás] Frissítés a Sensor SDK 1.4.1-es [hivatkozására](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248)

### <a name="v101"></a>v 1.0.1
* [Hibajavítás] Javítsa ki a problémát, hogy az SDK összeomlik, ha onnxruntime.dll betöltését a Windows Build 19025 vagy újabb verziójának elérési útjáról: [hivatkozás](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v 1.0.0
* Vonás Adja hozzá a C# burkolót az MSI-telepítőhöz.
* [Hibajavítás] Javítsa ki a hibát, hogy a fej forgása nem észlelhető helyesen: [hivatkozás](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Hibajavítás] Javítsa ki a problémát, hogy a CPU-használat 100%-kal növekszik a Linux rendszerű gépen: [hivatkozás](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Minták Vegyen fel két mintát a minta tárházba. Az 1. minta bemutatja, hogyan alakíthatja át a törzs nyomon követésének eredményét a mélységi területről a színtérre [mutató hivatkozásig](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample). a 2. minta azt mutatja be, hogyan lehet felderíteni a padló síkja [hivatkozását](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample)

## <a name="next-steps"></a>Következő lépések

- [Az Azure Kinect DK áttekintése](about-azure-kinect-dk.md)

- [Az Azure Kinect DK beállítása](set-up-azure-kinect-dk.md)

- [Az Azure Kinect Body követésének beállítása](body-sdk-setup.md)