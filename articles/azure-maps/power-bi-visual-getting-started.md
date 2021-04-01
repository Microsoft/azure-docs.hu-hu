---
title: Első lépések a Azure Maps Power BI vizualizációval | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan használhatja a Power BI Microsoft Azure Maps-vizualizációját.
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a563a7776e84289e38743057778e8fe10fd17503
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904703"
---
# <a name="getting-started-with-the-azure-maps-power-bi-visual"></a>Bevezetés a Azure Maps Power BI vizualizáció használatába

<Token>**a következőkre vonatkozik:** ![ Zöld pipa. ](media/power-bi-visual/yes.png) Power BI szolgáltatás a **_fogyasztók_** ![ zöld pipa jelzéssel. ](media/power-bi-visual/yes.png) Power BI szolgáltatás tervezők számára & a fejlesztők ![ zöld pipa jel. ](media/power-bi-visual/yes.png) Power BI Desktop ![ X, amely nem. ](media/power-bi-visual/no.png) Pro vagy Premium licenc szükséges</Token>

Ez a cikk bemutatja, hogyan használható a Power BI Microsoft Azure Maps-vizualizációja.

> [!NOTE]
> Ezt a vizualizációt a Power BI Desktop és a Power BI szolgáltatás is létrehozhatja és megtekintheti. A cikk lépései és ábrái a Power BI Desktopra vonatkoznak.

A Power BI Azure Maps vizualizációja az adatvizualizációk gazdag készletét kínálja a térbeli adatvizualizációk számára a térképen felül. A becslések szerint az üzleti adatmennyiség több mint 80%-a rendelkezik helyi környezettel. A Azure Maps vizualizáció segítségével betekintést nyerhet, hogy a hely környezete hogyan kapcsolódik az üzleti adataihoz, és hogyan befolyásolja azt.

![Power BI asztal a Azure Maps vizualizáció üzleti adataival](media/power-bi-visual/azure-maps-visual-hero.png)

## <a name="what-is-sent-to-azure"></a>Mi történik az Azure-ba való elküldésekor?

A Azure Maps vizualizáció csatlakozik az Azure-ban üzemeltetett felhőalapú szolgáltatáshoz, hogy lekérje a helyadatok, például a térképi vizualizáció létrehozásához használt képek és koordinátákat. 

-   Annak a területnek a részleteit, amelyre a Térkép vonatkozik, az Azure-ba küldik, hogy lekérje a Térkép vászon megjelenítéséhez szükséges képeket (más néven a Térkép csempéit). 
-   A hely, a szélesség és a hosszúság gyűjtőben lévő, az Azure-ba irányuló, a Térkép koordinátáinak (helymeghatározáshoz nevű folyamat) beolvasása céljából elküldhető. 
-   A telemetria adatokat gyűjthet a vizualizáció állapotáról (azaz az összeomlási jelentésekről), ha a Power BI Telemetria beállítása engedélyezve van.

A fent ismertetett forgatókönyveken kívül a rendszer nem küldi el a térképen a Azure Maps kiszolgálókra. Az összes adatmegjelenítés helyileg történik az ügyfélen belül.

Előfordulhat, hogy a rendszergazdának frissítenie kell a tűzfalat, hogy engedélyezze a hozzáférést a Azure Maps platformhoz, amely a következő URL-címet használja.

> `https://atlas.microsoft.com`

További információ az adatvédelemről és a Azure Maps vizualizációval kapcsolatos használati feltételekről: [Microsoft Azure jogi információk](https://azure.microsoft.com/support/legal/).

## <a name="azure-maps-visual-preview-behavior-and-requirements"></a>A vizualizáció (előzetes verzió) működésének és követelményeinek Azure Maps

Az **Azure Maps** vizualizációval kapcsolatban néhány szempontot és követelményt figyelembe kell venni. :

-   A **Azure Maps** vizualizációt (előzetes verzió) engedélyezve kell lennie a Power bi Desktopban. **Azure Maps** vizualizáció engedélyezéséhez válassza a **fájl** &gt; **beállításai és beállítások** &gt; **Beállítások** &gt; **előnézet funkciók** lehetőséget, majd jelölje be a **Azure Maps vizualizáció** jelölőnégyzetet. Ha a Azure Maps vizualizáció ezt követően nem érhető el, akkor valószínű, hogy a felügyeleti portálon engedélyezni kell a bérlői rendszergazdai kapcsolót.
-   Az adatkészletnek olyan mezőket kell tartalmaznia, amelyek **szélességi** és **hosszúsági** adatokat tartalmaznak. A helymeghatározáshoz egy későbbi frissítésben lesznek hozzáadva.
-   A Power BI beépített jelmagyarázat-vezérlője jelenleg nem jelenik meg ebben az előzetes verzióban. A későbbiekben egy későbbi frissítés lesz hozzáadva.

## <a name="use-the-azure-maps-visual-preview"></a>A Azure Maps vizualizáció használata (előzetes verzió)

Ha a **Azure Maps** vizualizáció engedélyezve van, válassza a **megjelenítések** ablaktábla **Azure Maps** ikonját.

![Azure Maps vizuális gomb a vizualizációk ablaktáblán](media/power-bi-visual/azure-maps-in-visualizations-pane.png)

Power BI létrehoz egy üres Azure Maps vizuális tervezési vászon. Előzetes verzióban megjelenik egy további jogi nyilatkozat.

![Power BI asztal betöltve a Azure Maps vizualizáció kezdeti állapotában](media/power-bi-visual/visual-initial-load.png)

A Azure Maps vizualizáció betöltéséhez hajtsa végre a következő lépéseket:

1.  A **mezők** ablaktáblán húzzon át egy szélességi és hosszúsági koordinátákat tartalmazó adatmezőket a **szélesség** és/vagy **hosszúság** gyűjtőbe. Ez a Azure Maps vizualizáció betöltéséhez szükséges minimális adatmennyiség.
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps vizualizáció megjelenítése buborékokként a térképen a szélességi és a hosszúsági mezők megadott időpontját követően](media/power-bi-visual/bubble-layer.png)

2.  Az adat kategorizálás alapján történő színezéséhez húzzon egy kategorikus mezőt a **mezők** ablaktábla **Jelmagyarázat** -gyűjtőbe. Ebben a példában a **AdminDistrict** oszlopot (más néven államot vagy tartományt) használjuk.  
    
    > [!div class="mx-imgBorder"]
    > ![Vizuális megjelenítés Azure Maps a térképen színes buborékokként a Térkép után a jelmagyarázat mező megjelenítése után](media/power-bi-visual/bubble-layer-with-legend-color.png)

    > [!NOTE]
    > A Power BI beépített jelmagyarázat-vezérlője jelenleg nem jelenik meg ebben az előzetes verzióban. A későbbiekben egy későbbi frissítés lesz hozzáadva.

3.  Az adat viszonylagi méretezéséhez húzzon egy mértéket a **mezők** ablaktábla **Size (méret** ) paneljére. Ebben a példában a **Sales** oszlopot használjuk.  
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps vizuális megjelenítések színesként és méretezett buborékokként a térképen a megadott méret után.](media/power-bi-visual/bubble-layer-with-legend-color-and-size.png)

4.  A **Formátum** ablaktáblán található beállításokkal testreszabhatja az adatmegjelenítés módját. A következő képen a fenti Térkép látható, de a buborék rétegek kitöltése átlátszóság beállítás értéke 50%, a magas kontrasztú tagolási lehetőség pedig engedélyezve van.  
    
    > [!div class="mx-imgBorder"]
    > ![Vizuális megjelenítések Azure Maps a térképen buborékként egyéni stílussal](media/power-bi-visual/bubble-layer-styled.png)

## <a name="fields-pane-buckets"></a>Mezők ablaktábla gyűjtői

A következő adatgyűjtők érhetők el a Azure Maps vizualizáció **mezői** ablaktábláján.

| Mező     | Leírás  |
|-----------|--------------|
| Szélesség  | Az adatpontok szélességi értékének megadására szolgáló mező. A szélességi értékeknek-90 és 90 közöttinek kell lennie decimális fok formátumban.  |
| Hosszúság | Az adatpontok hosszúsági értékének megadására szolgáló mező. A hosszúsági értékeknek-180 és 180 közöttinek kell lennie decimális fok formátumban.  |
| Jelmagyarázat    | Az egyes kategóriákban lévő adatpontok egyedi színének kategorizálására szolgáló mező. A gyűjtő kitöltése után az **adatszínek** szakasz megjelenik a **Formátum** ablaktáblán, amely lehetővé teszi a színek módosítását. |
| Méret      | A térképen lévő adatpontok relatív méretezéséhez használt mérték   |
| Elemleírások  | További adatmezők, amelyek az elemleírásokban jelennek meg, amikor az alakzatok láthatók. |

## <a name="map-settings"></a>Térkép beállításai

A formátum ablaktábla **Térkép beállításai** szakasza lehetőséget biztosít a Térkép megjelenítésének és a frissítések működésének testreszabására.

| Beállítás             | Leírás  |
|---------------------|--------------|
| Automatikus nagyítás           | A automatikusan a vizualizáció **mezői** ablaktábláján betöltött adatra nagyítja a leképezést. Az adatváltozások miatt a Térkép ennek megfelelően frissíti a pozícióját. Ha a csúszka **ki van kapcsolva a kikapcsolási** pozícióban, a Térkép további beállításai is megjelennek az alapértelmezett leképezési nézethez. |
| Globális Körbefuttatás          | Lehetővé teszi a felhasználó számára, hogy a leképezést vízszintesen, függőlegesen. |
| Style Picker        | Egy olyan gombot ad hozzá a térképhez, amely lehetővé teszi, hogy a jelentés olvasói megváltoztassák a Térkép stílusát. |
| Navigációs vezérlők | Gombokat ad hozzá a térképhez egy másik módszerként, amely lehetővé teszi, hogy a jelentés olvasói a Térkép nagyítását, elforgatását és módosítását. További információkért tekintse meg ezt a dokumentumot a [Térkép navigálásához](map-accessibility.md#navigating-the-map) , hogy a felhasználók milyen különböző módokon navigálhatnak a térképen. |
| Térkép stílusa           | A Térkép stílusa További információkért tekintse meg ezt a dokumentumot a [támogatott térképi stílusokkal](supported-map-styles.md)kapcsolatos további információkért. |

### <a name="map-view-settings"></a>Térkép nézet beállításai

Ha az **Automatikus nagyítás** csúszka **ki van kapcsolva a kikapcsolt** állapotban, a következő beállítások jelennek meg, és lehetővé teszik a felhasználó számára az alapértelmezett Térkép nézet adatainak megadását.

| Beállítás          | Leírás   |
|------------------|---------------|
| Zoom             | A Térkép alapértelmezett nagyítási szintje 0 és 22 közötti szám lehet. |
| Középső szélesség  | A Térkép középpontjában lévő alapértelmezett szélesség |
| Középpont hosszúsága | A Térkép középpontjának alapértelmezett hosszúsága |
| Vámtarifaszám          | A Térkép alapértelmezett tájolása fokban, ahol a 0 Észak, 90 Kelet, 180 déli, és 270 nyugati. 0 és 360 közötti szám lehet. |
| Betűközű            | A Térkép alapértelmezett megdőlése 0 és 60 közötti fokban, ahol a 0 a térképen keres. |

## <a name="considerations-and-limitations"></a>Megfontolandó szempontok és korlátozások

A Azure Maps vizualizáció a következő szolgáltatásokban és alkalmazásokban érhető el:

| Szolgáltatás/alkalmazás                              | Rendelkezésre állás |
|------------------------------------------|--------------|
| Power BI Desktop                         | Igen          |
| Power BI service (app.powerbi.com)       | Igen          |
| Power BI mobilalkalmazások             | Igen          |
| Power BI webes közzététel                  | Nem           |
| Power BI Embedded                        | Nem           |
| Power BI szolgáltatás beágyazása (PowerBI.com) | Yes          |

További Power BI szolgáltatások/alkalmazások támogatása a jövőbeli frissítésekben lesz hozzáadva.

**Hol érhető el Azure Maps?**

Jelenleg Azure Maps az összes országban és régióban elérhető, a következők kivételével:

- Kína
- Dél-Korea

A vizualizációt használó különböző Azure Maps szolgáltatások lefedettségi részleteiért tekintse meg a [földrajzi lefedettséggel kapcsolatos információkat](geographic-coverage.md) tartalmazó dokumentumot.

**Mely webböngészőket támogatja a Azure Maps vizualizáció?**

A [Azure Maps web SDK által támogatott böngészőkkel](supported-browsers.md)kapcsolatos információkért tekintse meg ezt a dokumentációt.

**Hány adatpontot lehet megjeleníteni?**

Ez a vizualizáció legfeljebb 30 000 adatpontot támogat.

**A vizualizációban használhatók a címek vagy más hely sztringek?**

A vizualizáció kezdeti előzetes verziója csak a szélességi és a hosszúsági értékeket decimális fokban támogatja. A jövőbeli frissítések támogatják a címek és más helyekhez tartozó karakterláncok támogatását.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Maps Power BI vizualizációról:

> [!div class="nextstepaction"]
> [A Azure Maps rétegeinek ismertetése Power BI vizualizációban](power-bi-visual-understanding-layers.md)

> [!div class="nextstepaction"]
> [A Azure Maps vizualizáció kezelése a szervezeten belül](power-bi-visual-manage-access.md)

A vizualizáció testreszabása:

> [!div class="nextstepaction"]
> [Tippek és trükkök a színformázáshoz a Power BI-ban](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [A vizualizáció címeinek, háttereinek és jelmagyarázatainak testreszabása](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)