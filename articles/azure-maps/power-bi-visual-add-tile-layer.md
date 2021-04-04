---
title: Csempe réteg hozzáadása a Azure Maps Power BI vizualizációhoz | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan használhatja a csempe réteget a Microsoft Azure Maps-vizualizációban Power BIhoz.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6ab99f32932f39d5ad140b7a16d16ceae30fff54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896208"
---
# <a name="add-a-tile-layer"></a>Mozaikréteg hozzáadása

A csempe réteg funkció, például a hivatkozási réteg funkció, lehetővé teszi, hogy a Térkép további információkkal lássa el a kontextust. A csempe rétegek lehetővé teszik, hogy az alapszintű Térkép csempék fölé írja a képeket Azure Maps. Ez nagyszerű módja a nagyméretű vagy összetett adatkészletek, például a herék vagy több millió sornyi adat ábrázolásának.

> [!div class="mx-imgBorder"]
> ![Egy buborék réteget ábrázoló Térkép, amely a Azure Maps aktuális infravörös időjárási adatait jeleníti meg.](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

Egy csempe réteg tölti be a csempéket egy kiszolgálóról. Ezeket a lemezképeket lehet előre megjeleníteni vagy dinamikusan megjeleníteni. Az előre megjelenített lemezképek, mint a kiszolgálók bármely más lemezképe, egy elnevezési konvenció használatával, amely a csempe rétegének ismerete. A dinamikusan renderelt képek egy szolgáltatás használatával töltik be a képeket valós időben. A csempe rétegei nagyszerű lehetőséget mutatnak a nagyméretű adathalmazok megjelenítésére a térképen. Nem csak a csempe réteg hozható létre egy képből, a vektoros adatok csempe rétegként is megjeleníthető.

Ha a csempe szolgáltatás elérhetővé válik, a határolókeret és a nagyítási tartomány beállítható úgy, hogy korlátozza a csempék igénylésének helyét. Ez mind a vizualizáció, mind a csempe szolgáltatás teljesítményének növelése. Az alábbiakban áttekintheti a **formázó** panelen elérhető összes beállítást a **csempe réteg** szakaszban.

| Beállítás        | Leírás   |
|----------------|---------------|
| URL-cím            | Egy csempe szolgáltatásra mutató formázott URL-cím.  |
| Csempeméret      | Egész érték, amely megadja a csempék szélességét és magasságát.   |
| Észak-Bound    | A szegélyek elérhetővé tételének északi szélességi helye. |
| Dél-Bound    | A határoló mező déli szélessége, ahol a csempék elérhetők. |
| Keleti határ     | A határoló mező keleti hosszúsága, ahol a csempék elérhetők.  |
| Nyugat-Bound     | A határoló mező nyugati hosszúsága, ahol a csempék elérhetők.   |
| Átláthatóság   | A csempe rétegének átlátszósága   |
| TMS         | A csempe-réteg Y koordináta-tengelyét visszafordító, a térképes szolgáltatások. |
| Minimális nagyítás       | A minimális nagyítási szint csempéi elérhetők. |
| Maximális nagyítás       | A maximális nagyítási szint csempe elérhető.  |
| Réteg pozíciója | Meghatározza a réteg pozícióját a többi térképi réteghez képest. |

## <a name="tile-url-formatting"></a>Csempe URL-címének formázása

A Azure Maps vizualizációban három különböző csempe-szolgáltatás elnevezési konvenciója támogatott:

-   **X, y, nagyítás jelölése** – az x az oszlop, az Y a csempe rácsában lévő csempe sora, a nagyítási szint pedig a nagyítási szint alapján van megadva.
-   **Quadkey-jelölés** – az x, y és nagyítási adatokat egyetlen karakterlánc-értékre kombinálja. Ez a karakterlánc-érték egyetlen csempe egyedi azonosítója lesz.
-   **Határolókeret** – a határolókeret koordinátáit tartalmazó képet ad meg: `{west},{south},{east},{north}` . Ezt a formátumot általában a [webes leképezési szolgáltatások (WMS)](https://www.opengeospatial.org/standards/wms)használják.

A csempe URL-címe egy, a csempe URL-sablonjának HTTPS-URL-címe, amely a következő paramétereket használja:

-   `{x}` -A csempe X pozíciója `{y}`A és a is szükséges `{z}` .
-   `{y}` -A csempe Y pozíciója `{x}`A és a is szükséges `{z}` .
-   `{z}` – A csempe nagyítási szintje `{x}`A és a is szükséges `{y}` .
-   `{quadkey}` – `quadkey` A csempe azonosítója a Bing Maps csempe rendszerelnevezési konvenciója alapján.
-   `{bbox-epsg-3857}` – Egy határolókeret karakterlánca, amely a `{west},{south},{east},{north}` EPSG 3857 térbeli hivatkozási rendszer formátumát jelöli.

Az alábbi példa egy formázott csempe-URL-címet mutat be a Azure Maps [időjárási radar csempe szolgáltatásához](/rest/api/maps/renderv2/getmaptilepreview) . Vegye figyelembe, hogy a `[subscription-key]` Azure Maps előfizetési kulcsának helyőrzője.

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

A Azure Maps csempe rendszerével kapcsolatos további információkért lásd: [nagyítási szintek és csempék rácsa](zoom-levels-and-tile-grid.md).

## <a name="next-steps"></a>Következő lépések

További környezet hozzáadása a térképhez:

> [!div class="nextstepaction"]
> [Valós idejű forgalom megjelenítése](power-bi-visual-show-real-time-traffic.md)