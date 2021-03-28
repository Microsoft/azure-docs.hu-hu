---
title: 'Gyors útmutató: Xamarin Android-alkalmazás létrehozása'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Android-alkalmazást a Xamarin térbeli horgonyok használatával.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 874b59b7439621c9d2777a55065cd769a5434567
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641332"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Gyors útmutató: Xamarin Android-alkalmazás létrehozása az Azure térbeli Horgonyokkal

Ez a rövid útmutató ismerteti, hogyan hozhat létre Android-alkalmazást az [Azure térbeli](../overview.md)Xamarin használatával. Az Azure térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóságot hozzon létre olyan objektumok használatával, amelyek az adott helyen maradnak a helyükön az egyes eszközökön. Ha elkészült, egy olyan Android-alkalmazással fog rendelkezni, amely képes a térbeli horgonyok mentésére és visszahívására.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A térbeli horgonyok fiókazonosító és a fiók kulcsának konfigurálása
> * Üzembe helyezés és Futtatás Android-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:
- Windows vagy macOS rendszerű számítógép:
  - Windows használata esetén:
    - A <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2 +</a>naprakész verziója.
    - <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>.
    - <a href="https://git-lfs.github.com/">Git-LFS</a>.
  - MacOS használata esetén:
    - A <a href="/visualstudio/mac/installation?view=vsmac-2019&preserve-view=true" target="_blank">Visual Studio for Mac 8.1 +</a>verziójának naprakész verziója.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git MacOS rendszerhez</a>.
    - <a href="https://git-lfs.github.com/">Git-LFS</a>.
- A Xamarin. Android legújabb verziója telepítve van és fut a választott platformon. A Xamarin. Android telepítésével kapcsolatos útmutatóért tekintse meg a [Xamarin. Android telepítési](/xamarin/android/get-started/installation/index) útmutatóit.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">fejlesztők számára engedélyezett</a> és <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore alkalmas</a> Android-eszköz.
  - Előfordulhat, hogy a számítógépe számára további eszközillesztők szükségesek az Android-eszközkel való kommunikációhoz. További információ: [itt](https://developer.android.com/studio/run/device.html).
- Az alkalmazásnak a **1,8** ARCore kell megcéloznia.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A minta projekt megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Megnyitás `Xamarin/SampleXamarin.sln` a Visual Studióban.

## <a name="configure-account-identifier-and-key"></a>Fiók azonosítójának és kulcsának konfigurálása

A következő lépés az alkalmazás konfigurálása a fiók azonosítójának és a fiók kulcsának használatára. [A térbeli horgonyok erőforrásának beállításakor](#create-a-spatial-anchors-resource)egy szövegszerkesztőbe másolta őket.

Nyissa meg a következő fájlt: `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Keresse meg a `SpatialAnchorsAccountKey` mezőt, és cserélje le a `Set me` fiókot a fiók kulcsára.

Keresse meg a `SpatialAnchorsAccountId` mezőt, és cserélje le a azonosítót `Set me` a fiókazonosító értékre.

Keresse meg a `SpatialAnchorsAccountDomain` mezőt, és cserélje le a `Set me` fiókot a fiók tartományára.

## <a name="deploy-the-app-to-your-android-device"></a>Az alkalmazás üzembe helyezése Android-eszközön

Kapcsolja be az androidos eszközt, jelentkezzen be, és csatlakoztassa a számítógéphez USB-kábellel.

Állítsa az indítási projektet a **SampleXamarin. Android** értékre, módosítsa a **megoldás konfigurációját** a **kiadásra**, majd válassza ki azt az eszközt, amelyet telepíteni kíván az eszköz-választó legördülő menüben.

# <a name="windows"></a>[Windows](#tab/deploy-windows)

![Képernyőkép, amely megjeleníti a menüt a projekt és az eszköz kiválasztásához a Windowsban.](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

  >  Az alkalmazás üzembe helyezéséhez és elindításához válassza a hibakeresés **megkezdése** lehetőséget.

# <a name="macos"></a>[macOS](#tab/deploy-macos)

![Visual Studio-konfiguráció](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

  >  Az alkalmazás üzembe helyezéséhez és elindításához válassza a Futtatás **indításkor hibakeresés nélkül** lehetőséget.

---

Az alkalmazásban válassza az **alapszintű** lehetőséget a bemutató futtatásához, és kövesse az utasításokat a horgony elhelyezéséhez és felidézéséhez.

> ![Képernyőfelvétel 1 ](./media/get-started-xamarin-android/screenshot-1.jpg)
>  ![ képernyőkép 2 ](./media/get-started-xamarin-android/screenshot-2.jpg)
>  ![ képernyőkép 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)