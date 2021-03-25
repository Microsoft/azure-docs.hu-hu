---
title: 'Rövid útmutató: HoloLens-alkalmazás létrehozása a DirectX szolgáltatással'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre HoloLens alkalmazást az Object-horgonyok használatával.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: b5db9f3766bdd7d754f49403665a371f9d10afd7
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047609"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>Gyors útmutató: HoloLens-alkalmazás létrehozása Azure Object-Horgonyokkal, C++/WinRT és DirectX-ben

Ez a rövid útmutató bemutatja, hogyan hozhat létre HoloLens-alkalmazást az [Azure Object-horgonyokkal](../overview.md) a C++/WinRT és a DirectX használatával. Az Azure Object-horgonyok egy felügyelt felhőalapú szolgáltatás, amely a 3D-eszközöket olyan AI-modellekre alakítja át, amelyek lehetővé teszik az objektum-Aware vegyes valóságot a HoloLens. Ha elkészült, egy HoloLens-alkalmazással fog rendelkezni, amely képes érzékelni egy objektumot, és azt egy holografikus DirectX 11 (univerzális Windows) alkalmazásban.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * HoloLens-alkalmazás létrehozása és oldalsó betöltése
> * Objektum észlelése és a modell megjelenítése

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

* Egy fizikai objektum a környezetben és a 3D-modellben (CAD vagy beolvasott).
* Egy Windows rendszerű gép, amelyen telepítve van a következő:
  * <a href="https://git-scm.com" target="_blank">Git for Windows</a>
  * <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> a **univerzális Windows-platform fejlesztési** munkaterhelés és a **Windows 10 SDK (10.0.18362.0 vagy újabb)** összetevővel
* Egy naprakész HoloLens 2 eszköz, amelyen engedélyezve van a [fejlesztői mód](/windows/mixed-reality/using-visual-studio#enabling-developer-mode) .
  * A HoloLens legújabb kiadásának frissítéséhez nyissa meg a **Beállítások** alkalmazást, lépjen a **frissítés & biztonság** elemre, majd válassza a **frissítések keresése** lehetőséget.

## <a name="open-the-sample-project"></a>A minta projekt megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

Megnyitás `quickstarts/apps/directx/DirectXAoaSampleApp.sln` a Visual Studióban.

Módosítsa a **megoldás konfigurációját** a **kiadásra**, változtassa meg a **megoldási platformot** a **ARM64**, majd válassza az **eszköz** lehetőséget a telepítési cél beállításainál. Ezután hozza létre a **AoaSampleApp** projektet úgy, hogy a jobb gombbal a projektre kattint, és kiválasztja a **Build** lehetőséget.

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="A Visual Studio-projekt konfigurálása a telepítéshez":::

## <a name="deploy-the-app-to-hololens"></a>Az alkalmazás üzembe helyezése a HoloLens-ben

A minta projekt sikeres fordítása után az alkalmazást üzembe helyezheti a HoloLens.

Kapcsolja be a HoloLens eszközt, jelentkezzen be, és csatlakoztassa a számítógéphez egy USB-kábellel. Győződjön meg arról, hogy az **eszköz** a kiválasztott telepítési cél (lásd fent).

Kattintson a jobb gombbal a **AoaSampleApp** projekt elemre, majd az alkalmazás telepítéséhez az előugró menüben kattintson a **telepítés** elemre. Ha a Visual Studio **kimeneti ablakban** nem jelenik meg hiba, az alkalmazás a HoloLens lesz telepítve.

:::image type="content" source="./media/vs-deploy-app.png" alt-text="Alkalmazás üzembe helyezése a HoloLens-ben":::

Az alkalmazás elindítása előtt fel kell töltenie egy objektummodell-modellt. Kövesse az objektummodell betöltése című részben leírt utasításokat **, és vizsgálja meg az alábbi példány** szakaszát.

Az alkalmazás elindításához és hibakereséséhez válassza a hibakeresés **> a hibakeresés megkezdése** lehetőséget. Az alkalmazás leállításához válassza a **hibakeresés leállítása** lehetőséget, vagy nyomja le a **SHIFT + F5** billentyűkombinációt.

## <a name="ingest-object-model-and-detect-its-instance"></a>Objektummodell betöltése és a példány észlelése

A minta alkalmazás futtatásához létre kell hoznia egy objektummodell-modellt. Tegyük fel, hogy a tárhelyen már van egy objektum CAD vagy beolvasott 3D Mesh modellje. Tekintse meg a gyors útmutató [: 3D modell](./get-started-model-conversion.md) betöltését a modell létrehozásával kapcsolatban.

Töltse le a modellt, a **Tanszék. ou** -t az esetünkben a számítógépre. Ezután a HoloLens-eszköz portálon válassza a **System > fájlkezelő > LocalAppData > AoaSampleApp > LocalState** elemet, és válassza a **Tallózás** lehetőséget. Ezután válassza ki a modell fájlját, a **Tanszék. szervezeti egységet** , és válassza a **feltöltés** lehetőséget. Ekkor meg kell jelennie a modell fájljának a helyi gyorsítótárban.

:::image type="content" source="../../../includes/media/object-anchors-quickstarts/portal-upload-model.png" alt-text="Portál feltöltési modellje":::

A HoloLens indítsa el a **AoaSampleApp** alkalmazást (ha már meg van nyitva, zárjuk le, majd nyissa meg újra). Sétáljon le (2 méteres távolságon belül) a célobjektum (szék) számára, és vizsgálja meg, hogy több szemszögből is megtekinti. Az objektum körül egy rózsaszín határvonalat kell megadnia, amely az objektum felületének közelében megjelenített sárga pontokból áll, ami azt jelzi, hogy a rendszer észlelt.

:::image type="content" source="./media/chair-detection.png" alt-text="Szék észlelése":::

Ábra: egy észlelt szék, amely a határolókeret (Pink), a Cloud (sárga) és a keresési területek (nagy sárga doboz) alapján lett megjelenítve.

Az alkalmazásban található objektumhoz tartozó keresési területet az ujját a jobb vagy bal oldali gombra kattintva adhatja meg. A keresési terület két méter sugarú gömb, egy 4 m ^ 3 határolókeret és egy nézet csonkakúpot között vált. A nagyobb méretű objektumok, például az autók esetében a legjobb választás az, hogy a nézet csonkakúpot válassza a kijelölést, miközben az objektum egy sarkába kerül, körülbelül 2 méter távolságban.
A keresési területek minden változásakor az alkalmazás eltávolítja a jelenleg nyomon követett példányokat, majd megpróbálja újból megkeresni őket az új keresési területen.

Az alkalmazás egyszerre több objektumot is nyomon követhet. Ehhez töltsön fel több modellt a **LocalState** mappába, és állítson be egy olyan keresési területet, amely az összes cél objektumra kiterjed. Több objektum észlelése és nyomon követése hosszabb időt is igénybe vehet.

Az alkalmazás szorosan összehangolja a 3D modellt a fizikai munkatársaival. A felhasználók a bal kezét használva a nagy pontosságú nyomkövetési módot is bekapcsolhatják, ami pontosabban jelentheti a számítást. Ez továbbra is egy kísérleti funkció, amely több rendszererőforrást használ fel, és a becsült értéknél magasabb Jitter-t eredményezhet. A normál nyomkövetési módba való visszaváltáshoz kattintson a bal oldali levegőre.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: 3D modell betöltése](./get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Fogalmak: az SDK áttekintése](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Gyakori kérdések](../faq.md)