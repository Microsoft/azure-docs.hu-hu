---
title: 'Gyors útmutató: Unity Android-alkalmazás létrehozása'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre a térbeli Horgonyokkal rendelkező Android-alkalmazást az Unity használatával.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1554b1728b120145a06124e4703065a98a4e466
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670096"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Gyors útmutató: Unity Android-alkalmazás létrehozása az Azure térbeli Horgonyokkal

Ez a rövid útmutató ismerteti, hogyan hozhat létre egy Unity Android-alkalmazást az [Azure térbeli horgonyok](../overview.md)használatával. Az Azure térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóságot hozzon létre olyan objektumok használatával, amelyek az adott helyen maradnak a helyükön az egyes eszközökön. Ha elkészült, egy olyan egységgel rendelkező ARCore Android-alkalmazás fog rendelkezni, amely képes a térbeli horgonyok mentésére és visszahívására.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * Az egység létrehozási beállításainak előkészítése
> * A térbeli horgonyok fiókazonosító és a fiók kulcsának konfigurálása
> * A Android Studio projekt exportálása
> * Üzembe helyezés és Futtatás Android-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy <a href="https://unity3d.com/get-unity/download" target="_blank">Unity (LTS)</a>Windows vagy MacOS rendszerű gép, beleértve az Android SDK-val **& NDK-eszközökkel** és **OpenJDK** -modulokkal rendelkező **Android Build-támogatást** . Használja az **2020 LTS** -et az asa SDK 2,9-es vagy újabb verziójával (amely a [Unity XR beépülő modul keretrendszert](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)használja) vagy az **Unity 2019 LTS** -et az ASA SDK 2,8-es vagy korábbi verziójával.
  - Ha Windows rendszeren fut, <a href="https://git-scm.com/download/win" target="_blank">a git for Windows</a> és a <a href="https://git-lfs.github.com/">git LFS</a>is szüksége lesz.
  - Ha macOS rendszeren fut, a git a HomeBrew használatával telepíthető. Írja be a következő parancsot a terminál egyetlen sorába: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Ezután futtassa `brew install git` a és a parancsot `brew install git-lfs` .
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">fejlesztők számára engedélyezett</a> és <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore alkalmas</a> Android-eszköz.
  - Előfordulhat, hogy a számítógépe számára további eszközillesztők szükségesek az Android-eszközkel való kommunikációhoz. További információkért és útmutatásért lásd [itt](https://developer.android.com/studio/run/device.html).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Az Unity Sample projekt letöltése és megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>A Android Studio projekt exportálása

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Válassza ki az eszközt az **eszköz futtatása** területen, majd válassza a **Létrehozás és Futtatás** lehetőséget. A rendszer megkéri, hogy mentsen egy `.apk` fájlt, amelyből bármilyen nevet kiválaszthat.

Az alkalmazásban válassza a **BasicDemo** lehetőséget a nyilak használatával, majd nyomja meg a **Go!** gombra a bemutató futtatásához. A horgonyok elhelyezéséhez és felidézéséhez kövesse az utasításokat.

![Képernyőfelvétel 1 ](./media/get-started-unity-android/screenshot-1.jpg)
 ![ képernyőkép 2 ](./media/get-started-unity-android/screenshot-2.jpg)
 ![ képernyőkép 3](./media/get-started-unity-android/screenshot-3.jpg)

A horgonyok elhelyezéséhez és felidézéséhez kövesse az alkalmazás utasításait.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="rendering-issues"></a>Renderelési problémák

Ha az alkalmazás futtatásakor nem látja a kamerát háttérként (például üres, kék vagy más textúra jelenik meg), akkor valószínűleg újra kell importálnia az egységben lévő eszközöket. Állítsa le az alkalmazást. Az egység felső menüjében válassza az **eszközök – > az összes újraimportálása** elemet. Ezután futtassa újra az alkalmazást.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Útmutató: Azure térbeli horgonyok konfigurálása Unity-projektben](../how-tos/setup-unity-project.md)
