---
title: A Azure Maps rétegeinek ismertetése Power BI vizualizációban | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan használhatók a Microsoft Azure Maps-vizualizációban elérhető különböző rétegek Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a5c7296a0e7b7f6ea33c1f4a669675efd90f9e9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86261844"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>A Azure Maps rétegeinek ismertetése Power BI vizualizációban

A Azure Maps vizualizációban két típusú réteg érhető el. Az első típus a vizualizáció **mezői** ablaktábláján átadott megjelenítési adatra koncentrál, és a következő rétegekből áll, ezért hívjuk ezeket az adatmegjelenítési rétegeket.

:::row:::
    :::column span="":::
        **Buborék réteg**

        A renderelési pontokat a térképen méretezett körökként jeleníti meg.

        ![Buborék réteg a térképen](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Oszlopdiagram réteg**

        A pontok 3D sávokként jeleníti meg a térképen.
        
        ![Térképes sávdiagram réteg](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

A második típusú réteg összekapcsolja a külső adatforrásokat a leképezéshez, hogy további kontextust biztosítson, és a következő rétegekből áll.

:::row:::
    :::column span="":::
        **Referenciaréteg**

        Egy feltöltött GeoJSON-fájl átfedésben van a Térkép tetején.

        ![Hivatkozási réteg a térképen](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Csempe réteg**

        Egyéni csempe rétegének átfedése a Térkép tetején
        
        ![Réteg csempéje a térképen](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Forgalmi réteg**

        Átfedő valós idejű forgalmi információk a térképen.
        
        ![Forgalmi réteg a térképen](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

Az összes adatmegjelenítési réteg, valamint a **csempe réteg** a minimális és a maximális nagyítási szintet is használhatja a nagyítási szint megadásához, ezeket a rétegeket a következő helyen kell megjeleníteni:. Ez lehetővé teszi, hogy egy megjelenítési réteg egyetlen nagyítási szinten legyen használva, és egy másik megjelenítési rétegre váltson át egy másik nagyítási szinten.

Ezek a rétegek a Térkép más rétegeihez viszonyítva is megadhatók. Több adatmegjelenítési réteg használata esetén a térképhez hozzáadott sorrend határozza meg, hogy a rendszer milyen sorrendben adja hozzá a diagramhoz a relatív rétegbeli **pozíció** értékét.

## <a name="general-layer-settings"></a>Általános réteg beállításai

A **Formátum** ablaktábla általános réteg szakasza olyan általános beállítások, amelyek a **mezők** panelen a Power bi adatkészlethez csatlakozó rétegekre vonatkoznak (buborék réteg, sávdiagram).

| Beállítás     | Leírás   |
|-------------|---------------|
| Nem kijelölt átlátszóság | A nem kijelölt alakzatok átlátszósága, ha egy vagy több alakzat van kiválasztva.  |
| Nullák megjelenítése              | Meghatározza, hogy a minimális RADIUS-értékkel rendelkező pontok jelenjenek-e meg a térképen. |
| Negatívok megjelenítése          | Meghatározza, hogy a negatív méret értékeinek abszolút értékét kell-e ábrázolni.   |
| Minimális adatérték          | A bemeneti adatok minimális értéke, amelyet a méretre kell méretezni. A kiugró értékek kivágására jó.  |
| Maximális adatérték          | A skálázáshoz használandó bemeneti adatok maximális értéke. A kiugró értékek kivágására jó.  |

## <a name="next-steps"></a>Következő lépések

Megváltoztathatja, hogyan jelenjenek meg az adatai a térképen:

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Oszlopdiagram-réteg hozzáadása](power-bi-visual-add-bar-chart-layer.md)

További környezet hozzáadása a térképhez:

> [!div class="nextstepaction"]
> [Referenciaréteg hozzáadása](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Mozaikréteg hozzáadása](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Valós idejű forgalom megjelenítése](power-bi-visual-show-real-time-traffic.md)
