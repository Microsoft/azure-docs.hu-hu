---
title: Buborék réteg hozzáadása a Azure Maps Power BI vizualizációhoz | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan használhatja a buborék réteget a Microsoft Azure Maps-vizualizációban Power BIhoz.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ff0d2c8e90995222af79d5be63ca3e9da24b3c45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896225"
---
# <a name="add-a-bubble-layer"></a>Buborékréteg hozzáadása

A **buborék réteg** a térképen méretezett körökként jeleníti meg a helyadatok mennyiségét.

> [!div class="mx-imgBorder"]
> ![Térképi pont adatai a buborék réteg használatával](media/power-bi-visual/bubble-layer-with-legend-color.png)

Kezdetben az összes buboréknak ugyanaz a kitöltési színe. Ha a **mezők** ablaktábla **Jelmagyarázat** -gyűjtője egy mezőt ad át, a buborékok a kategorizálásuk alapján színezve lesznek. A buborékok vázlata alapértelmezés szerint fehér, de új színre vagy a nagy kontrasztú körvonal beállítás engedélyezésével módosítható. A **nagy kontrasztú tagolási** lehetőség dinamikusan rendeli hozzá a kitöltési szín kontrasztos változatát. Ezzel biztosítható, hogy a buborékok jól láthatók legyenek a Térkép stílusa nélkül. A következő az elsődleges beállítások a **buborék réteg** szakaszban elérhető **Format** ablaktáblán.

| Beállítás               | Leírás    |
|-----------------------|----------------|
| Méret                  | Az egyes buborékok mérete Ez a beállítás akkor jelenik meg, ha a mező a **mezők** ablaktábla **Size (méret** ) paneljére kerül át. Ebben a cikkben további beállítások jelennek meg a [buborék méretének skálázása](#bubble-size-scaling) című témakörben leírtak szerint. |
| Kitöltési szín            | Az egyes buborékok színe. Ez a beállítás akkor jelenik meg, ha a **mezők** ablaktábla **Jelmagyarázat** -gyűjtője egy mezőt ad át, és a **Formátum** ablaktáblán megjelenik egy külön **adatszíni** szakasz. |
| Kitöltés átlátszósága     | Az egyes buborékok átlátszósága. |
| Nagy kontrasztú körvonal | A kitöltési szín kontrasztos változatának használatával a jobb hozzáférhetőség érdekében a kitöltési szín kontrasztját adja meg. |
| Körvonal színe         | A buborékot meghatározó szín Ez a beállítás akkor van elrejtve, ha engedélyezve van a **nagy kontrasztú tagolási** beállítás. |
| Vázlat átlátszósága  | A vázlat átlátszósága |
| Körvonal szélessége         | A körvonal szélessége képpontban megadva |
| Elmossa                  | A körvonalra alkalmazott életlenítési mennyiség. Az 1 érték elmossa a buborékok körét, így csak a középpontnak nincs átlátszósága. A 0 érték bármilyen életlenítési hatást alkalmaz. |
| Szurok igazítása       | Meghatározza, hogy a buborékok hogyan legyenek kijelölve a Térkép feldobásakor. <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Nézőpont – a buborékok a Térkép szélén, a nézőponthoz viszonyítva jelennek meg. (alapértelmezett)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Térkép – a buborékok a Térkép felületén jelennek meg. |
| Nagyítási skála            | A buborékok méretét a nagyítási szinthez képest kell méretezni. Az egyik nagyítási skála nem méretezést jelent. A nagyméretű értékek kisebbek lesznek, ha a nagyítása és kicsinyítése nagyobb méretben történik. Ezzel csökkenthető a Térkép zsúfoltsága, ha a nagyítás megtörtént, de a nagyítási pontok továbbra is biztosítanak. Az 1 érték nem alkalmaz méretezést. |
| Minimális nagyítás              | A minimális nagyítási szint csempéi elérhetők. |
| Maximális nagyítás              | A maximális nagyítási szint csempe elérhető. |
| Réteg pozíciója        | Meghatározza a réteg pozícióját a többi térképi réteghez képest. |

## <a name="bubble-size-scaling"></a>Buborék méretének skálázása

Ha egy mezőt a **mezők** ablaktábla **Size (méret** ) gyűjtője adja át, a buborékok az egyes adatpontok mértékének megfelelő méretezéssel lesznek méretezve. A **Formátum** ablaktábla **buborék réteg** szakaszában a **méret** lehetőség eltűnik, ha a rendszer átadja a mezőt a **méret** gyűjtőnek, mivel a buborékok a minimális és a maximális érték között méretezhetők. A következő beállítások jelennek meg a **Formátum** ablaktábla **buborék réteg** szakaszában, ha a **méret** gyűjtőhöz mező van megadva.

| Beállítás             | Leírás  |
|---------------------|--------------|
| Minimális méret            | A buborék minimális mérete az adatskálázáskor.|
| Maximális méret            | A buborék maximális mérete az adatméretezés során.|
| Méretezési módszer | A relatív buborék méretének meghatározásához használt méretezési algoritmus.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• A bemeneti adatok lineáris tartománya lineárisan leképezve a minimális és a maximális méretre. (alapértelmezett)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• A logaritmikusan a minimális és a maximális mérethez leképezett bemeneti adatok naplózási tartománya.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cubic-Bezier – a Cubic-Bezier görbe X1, y1, x2, y2 értékének megadása egyéni skálázási módszer létrehozásához. |

Ha a méretezési **módszer** **naplózásra** van beállítva, a következő beállítások lesznek elérhetők.

| Beállítás   | Leírás      |
|-----------|------------------|
| Naplózási skála | A buborékok méretének kiszámításakor alkalmazandó logaritmikus skála. |

Ha a méretezési **módszer** a **köbös-Bezier** értékre van állítva, a rendszer a következő beállításokat fogja elérhetővé tenni a skálázási görbe testreszabásához.

| Beállítás | Leírás                           |
|---------|---------------------------------------|
| X1      | A köbös Bezier-görbe X1 paramétere. |
| Y1      | A köbös Bezier-görbe x2 paramétere. |
| X2      | Egy köbméter Bezier-görbe y1 paramétere. |
| Y2      | Egy köbméter Bezier-görbe y2 paramétere. |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) a tartalmaz egy praktikus eszközt a Cubic-Bezier görbék paramétereinek létrehozásához.

## <a name="next-steps"></a>Következő lépések

Megváltoztathatja, hogyan jelenjenek meg az adatai a térképen:

> [!div class="nextstepaction"]
> [Oszlopdiagram-réteg hozzáadása](power-bi-visual-add-bar-chart-layer.md)

További környezet hozzáadása a térképhez:

> [!div class="nextstepaction"]
> [Referenciaréteg hozzáadása](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Mozaikréteg hozzáadása](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Valós idejű forgalom megjelenítése](power-bi-visual-show-real-time-traffic.md)

A vizualizáció testreszabása:

> [!div class="nextstepaction"]
> [Tippek és trükkök a színformázáshoz a Power BI-ban](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [A vizualizáció címeinek, háttereinek és jelmagyarázatainak testreszabása](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)