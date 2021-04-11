---
title: 'Gyors útmutató: HoloLens-alkalmazás létrehozása Unity'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre HoloLens-alkalmazást az Unity használatával a térbeli Horgonyokkal.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c0cce7717dda73a381ec977c3bf520bf8db5bbb3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105850"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Gyors útmutató: Azure térbeli horgonyokat használó Unity HoloLens-alkalmazás létrehozása

Ebben a rövid útmutatóban egy olyan Unity HoloLens-alkalmazást fog létrehozni, amely [Azure térbeli horgonyokat](../overview.md)használ. A térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi vegyes valósági tapasztalatok létrehozását olyan objektumokkal, amelyek a helyükön maradnak az eszközökön az idő múlásával. Ha elkészült, egy olyan egységgel rendelkező HoloLens-alkalmazás fog rendelkezni, amely képes a térbeli horgonyok mentésére és visszahívására.

A következőket fogja megtanulni:

- Hozzon létre egy térbeli horgonyokat tartalmazó fiókot.
- Készítse elő az egység létrehozási beállításait.
- Konfigurálja a térbeli horgonyok fiókjának azonosítóját és a fiók kulcsát.
- Exportálja a HoloLens Visual Studio-projektet.
- Telepítse az alkalmazást, és futtassa egy HoloLens-eszközön.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Szüksége van egy <a href="https://unity3d.com/get-unity/download" target="_blank">Unity (LTS)</a> és a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> -es vagy újabb verzióját futtató Windows-számítógépre. Használja az **2020 LTS** -et az asa SDK 2,9-es vagy újabb verziójával (amely a [Unity XR beépülő modul keretrendszert](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)használja) vagy az **Unity 2019 LTS** -et az ASA SDK 2,8-es vagy korábbi verziójával. A Visual Studio-telepítésnek tartalmaznia kell a **univerzális Windows-platform fejlesztési** munkaterhelést és a **Windows 10 SDK (10.0.18362.0 vagy újabb)** összetevőt. <a href="https://git-scm.com/download/win" target="_blank">A git for Windows és a</a> <a href="https://git-lfs.github.com/">git LFS</a>is telepítenie kell.
- Szüksége van egy olyan HoloLens-eszközre, amelyen engedélyezve van a [fejlesztői mód](/windows/mixed-reality/using-visual-studio) . A [Windows 10 2020-es frissítését](/windows/mixed-reality/whats-new/release-notes-may-2020) telepíteni kell az eszközre. A HoloLens legújabb kiadásának frissítéséhez nyissa meg a **Beállítások** alkalmazást, lépjen a **frissítés & biztonság** elemre, majd válassza a **frissítések keresése** lehetőséget.
- Az alkalmazásban engedélyeznie kell a **SpatialPerception** képességet. Ez a beállítás a beállítások **kiépítése** a  >  **lejátszó beállításaiban**  >  **közzétételi beállítások**  >  **lehetőségre**.
- Az alkalmazásban engedélyeznie kell a **Windows Mixed Reality SDK**-val **támogatott virtuális valóságot** . Ez a beállítás a **Build Settings**  >  **Player Settings**  >  **XR** beállításokban található.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Az Unity Sample projekt letöltése és megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

A HoloLens platformhoz szükséges ASA SDK-csomagok letöltéséhez és importálásához kövesse az [itt](../how-tos/setup-unity-project.md#download-asa-packages) található utasításokat.

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

A **Build-beállítások** megnyitásához válassza a **fájl**-  >  **létrehozási beállítások** lehetőséget.

A **platform** szakaszban válassza a **univerzális Windows-platform** lehetőséget. Módosítsa a **céleszköz** **HoloLens**.

Válassza a **platform váltása** lehetőséget a platform **univerzális Windows-platformre** való módosításához. Az egység kérheti, hogy telepítse a UWP-támogatási összetevőket, ha hiányoznak.

![Unity-létrehozási beállítások ablak](./media/get-started-unity-hololens/unity-build-settings.png)

A **létrehozási beállítások** ablak bezárásához.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-hololens-visual-studio-project"></a>A HoloLens Visual Studio-projekt exportálása

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Válassza a **Létrehozás** lehetőséget. A párbeszédpanelen válassza ki azt a mappát, amelybe exportálni szeretné a HoloLens Visual Studio-projektet.

Az Exportálás befejeztével megjelenik az exportált HoloLens projektet tartalmazó mappa.

## <a name="deploy-the-hololens-application"></a>A HoloLens alkalmazás üzembe helyezése

A mappában kattintson duplán a **HELLOAR U3D. SLN** elemre a projekt a Visual Studióban való megnyitásához.

Módosítsa a **megoldás konfigurációját** a **kiadásra**, módosítsa a **megoldás platformját** **x86**-ra, majd válassza az **eszköz** lehetőséget a telepítési cél beállításai közül.

Ha a 2. HoloLens használja, a **ARM64** -et a **megoldási platformként** használhatja az **x86** helyett.

   ![Visual Studio-konfiguráció](./media/get-started-unity-hololens/visual-studio-configuration.png)

Kapcsolja be a HoloLens eszközt, jelentkezzen be, és csatlakoztassa az eszközt a számítógéphez USB-kábellel.

Válassza a **hibakeresés**  >  **indítása** az alkalmazás üzembe helyezéséhez és a hibakeresés megkezdéséhez lehetőséget.

Az alkalmazásban válassza a **BasicDemo** lehetőséget a nyilak használatával, majd nyomja meg a **Go!** gombra a bemutató futtatásához. A horgonyok elhelyezéséhez és felidézéséhez kövesse az utasításokat.

![Képernyőfelvétel 1 ](./media/get-started-unity-hololens/screenshot-1.jpg)
 ![ képernyőkép 2 képernyőkép ](./media/get-started-unity-hololens/screenshot-2.jpg)
 ![ 3 képernyőkép ](./media/get-started-unity-hololens/screenshot-3.jpg)
 ![ 4](./media/get-started-unity-hololens/screenshot-4.jpg)

A Visual Studióban állítsa le az alkalmazást a **hibakeresés leállítása** vagy a SHIFT + F5 billentyűkombináció kiválasztásával.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Útmutató: Azure térbeli horgonyok konfigurálása Unity-projektben](../how-tos/setup-unity-project.md)