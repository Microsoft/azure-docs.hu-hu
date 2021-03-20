---
title: Azure Kinect Viewer
description: Ismerje meg, hogyan jelenítheti meg az összes eszköz adatfolyamát az Azure Kinect Viewer használatával.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, érzékelő, megjelenítő, vizualizáció, mélység, RGB, szín, IMU, hang, mikrofon, pont felhő
ms.openlocfilehash: f411de5854d516586dc64701cca729c692ce40e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92166105"
---
# <a name="azure-kinect-viewer"></a>Azure Kinect Viewer

A telepített eszközök könyvtárában található Azure Kinect Viewer `k4aviewer.exe` (például `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe` , ahol az az `X.Y.Z` SDK telepített verziója) a következő módon jeleníthető meg az összes eszközön lévő adatfolyam megjelenítéséhez:

* Ellenőrizze, hogy az érzékelők megfelelően működnek-e.
* Segítség az eszköz elhelyezéséhez.
* Kísérletezés a kamera beállításaival.
* Eszköz konfigurációjának olvasása.
* Az [Azure Kinect Recorder](azure-kinect-recorder.md)által készített felvételek lejátszása.

Az Azure Kinect Viewerrel kapcsolatos további információkért tekintse [meg az Azure Kinect videó használatát](https://www.microsoft.com/videoplayer/embed/RE3hNwG)ismertető témakört.

Az Azure Kinect Viewer [nyílt forráskódú](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer) , és példaként használható az API-k használatához.

## <a name="use-viewer"></a>Megjelenítő használata

A megjelenítő két üzemmódban működhet: az érzékelőből vagy a rögzített adatokból ([Azure Kinect Recording](azure-kinect-recorder.md)) származó élő adatokkal.

### <a name="start-application"></a>Alkalmazás elindítása

Indítsa el az alkalmazást a futtatásával `k4aviewer.exe` .

![Képernyőkép, amely az elindított megjelenítő alkalmazást jeleníti meg.](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>A megjelenítő használata élő adatértékekkel

1. Az **eszköz megnyitása** szakaszban válassza ki a megnyitni kívánt eszköz **sorozatszámát** . Ezután válassza a **frissítés** lehetőséget, ha az eszköz hiányzik.
2. Kattintson az **eszköz megnyitása** gombra.
3. A **Start** gombra kattintva megkezdheti az adatátvitelt az alapértelmezett beállításokkal.

### <a name="use-the-viewer-with-recorded-data"></a>A megjelenítőnek a rögzített adataival való használata

Az **Open Recording (rögzítés megnyitása** ) szakaszban navigáljon a rögzített fájlhoz, és jelölje ki.

## <a name="check-device-firmware-version"></a>Eszköz belső vezérlőprogram-verziójának keresése

Nyissa meg az eszköz belső vezérlőprogram-verzióját a konfigurációs ablakban az alábbi ábrán látható módon.

![Eszköz belső vezérlőprogram-verziójának ellenőrzése a megjelenítővel](./media/how-to-guides/check-firmware-update.png)

Például ebben az esetben a részletes kamera ISP az FW 1.5.63-t futtatja.

## <a name="depth-camera"></a>Mélységi kamera

A részletes kamera megjelenítője két ablakot fog megjeleníteni:

* Az egyiket *aktív fényerőnek* nevezzük, amely az IR fényerőt ábrázoló szürkeárnyalatos kép.
* A második neve *mélység*, amely a mélységi adat színezett ábrázolása.

Vigye a kurzort a mélységi ablak képpont pontjára, és a mélységi érzékelő értékét a lent látható módon jelenítse meg.

![Részletes nézet](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>RGB-kamera

Az alábbi képen a színes kamera nézet látható.

![RGB-nézet](./media/how-to-guides/viewer-rgb-camera.png)

Az RGB-kamera beállításait a folyamatos átvitel során a konfigurációs ablakból szabályozhatja.

![RGB-kamera vezérlői](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>Tehetetlenségi mértékegység (IMU)

A IMU ablakban két összetevő, egy gyorsulásmérő és egy giroszkóp látható.

A felső fél a gyorsulásmérő, és a lineáris gyorsulást mutatja a mérőórák/másodperc<sup>2</sup>.  Magában foglalja a gravitációs gyorsítást is, így ha egy táblázatra épül, a Z tengely valószínűleg körülbelül-9,8 m/s<sup>2</sup>lesz látható.

Az alsó rész a giroszkóp, a rotációs mozgás pedig radiánban/másodpercben jelenik meg.

![Mozgásérzékelő nézet](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>Mikrofon bemenete

A mikrofon nézet az egyes mikrofonokban hallható hang ábrázolását jeleníti meg. Ha nincs hang, a gráf üresként jelenik meg, ellenkező esetben egy sötétkék hullámformát fog látni, amely egy világoskék hullámformát tartalmaz.

A sötét hullám a mikrofon által az adott időszeleten megfigyelt minimális és maximális értéket jelöli. A világos hullám a mikrofon által az adott időszeleten megfigyelt értékek legfelső szintű négyzetét jelöli.

![Mikrofon bemeneti nézete](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>Pontszerű Felhőbeli vizualizáció

A 3D-ben megjelenített mélységgel áthelyezheti a képet a megtanított kulcsok használatával.

![Mélységi pont felhője](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>Szinkronizálás vezérlése

A megjelenítővel konfigurálhatja az eszközt önálló (alapértelmezett), fő vagy alárendelt módban a többeszközes szinkronizálás konfigurálásakor.
A konfiguráció módosításakor vagy a szinkronizálási kábel beszúrásakor vagy eltávolításakor **válassza a frissítés lehetőséget** .

![Külső szinkronizációs vezérlő](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[Külső szinkronizálás telepítési útmutatója](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)
