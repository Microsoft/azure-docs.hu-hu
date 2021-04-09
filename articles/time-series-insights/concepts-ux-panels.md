---
title: Adatmegjelenítés a Time Series Insights Explorerben – Azure Time Series Insights Gen2 | Microsoft Docs
description: Ismerkedjen meg a Azure Time Series Insights Explorerben elérhető funkciókkal és lehetőségekkel.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 77c6e8790451ef830b37cc5914ce9de8e92174f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464088"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights Explorer

Ez a cikk a Azure Time Series Insights Gen2 [bemutató környezetében](https://insights.timeseries.azure.com/preview/demo)elérhető különböző funkciókat és lehetőségeket ismerteti.

## <a name="prerequisites"></a>Előfeltételek

A Azure Time Series Insights Explorer megkezdéséhez a következőket kell tennie:

* Azure Time Series Insights Gen2-környezet van kiépítve. További információ a példányok üzembe helyezéséről: [Azure Time Series Insights Gen2](./tutorial-set-up-environment.md) oktatóanyag.
* Adja meg a fiókhoz létrehozott Azure Time Series Insights Gen2-környezet [adathozzáférését](./concepts-access-policies.md) . Mások számára is biztosíthat hozzáférést.
* Adjon hozzá egy eseményforrás a Azure Time Series Insights Gen2-környezethez az adatküldés a környezetbe:
  * További információ az [Event hub-hoz való kapcsolódásról](./how-to-ingest-data-event-hub.md)
  * További információ az [IoT hub-hoz való kapcsolódásról](./how-to-ingest-data-iot-hub.md)

## <a name="explore-the-azure-time-series-insights-explorer"></a>Ismerkedés a Azure Time Series Insights Explorerrel

A Azure Time Series Insights Explorer a következő hét elemből áll:

[![Azure Time Series Insights Explorer – áttekintés](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Környezeti panel](#1-environment-panel): megjeleníti az összes Azure Time Series Insights Gen2 környezetét.
1. [Navigációs sáv](#2-navigation-bar): lehetővé teszi az **elemzés** és a **modell** oldalai közötti váltást.
1. [Hierarchia és keresési panel](#3-hierarchy-tree-and-search-panel): lehetővé teszi, hogy kiválassza és megkeresse a diagramba felvenni kívánt adatelemeket.
1. [Idősorozatok](#4-time-series-well): a jelenleg kijelölt adatelemek megjelenítése.
1. [Diagram panel](#5-chart-panel): az aktuális munkadiagramot jeleníti meg.
1. [Idősor](#6-time-editor-panel): lehetővé teszi a munkaidő-tartomány módosítását.
1. [Alkalmazás-sáv](#7-app-bar): a felhasználói felügyeleti beállításokat (például az aktuális bérlőt) tartalmazza, és lehetővé teszi a módosítását és a nyelvi beállításokat.

## <a name="1-environment-panel"></a>1. környezeti panel

A környezet panel megjeleníti az összes olyan Azure Time Series Insights Gen2 környezetet, amelyhez hozzáféréssel rendelkezik. A lista Gen2-környezeteket és Gen1-környezeteket is tartalmaz. Egyszerűen válassza ki azt a környezetet, amelyet azonnal el szeretne végezni.

1. Válassza ki a megjelenített környezet melletti legördülő nyilat.

   [![Környezeti panel](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Ezután válassza ki a kívánt környezetet.

## <a name="2-navigation-bar"></a>2. navigációs sáv

  [![A navigációs sáv](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Két nézet között a navigációs sáv használatával választhat:

* **Elemzés**: használja a táblázatos és a részletes elemzések elvégzésére a modellezett vagy nem modellezett idősorozat-adatokon.
* **Modell**: ezzel a paranccsal leküldheti az új Azure Time Series Insights Gen2 típusokat, hierarchiákat és példányokat az idősorozat-modellbe.

### <a name="model-authoring"></a>Modell készítése

Az Azure Time Series Insights Gen2 támogatja a teljes létrehozási, olvasási, frissítési és törlési (szifilisz) műveleteket az idősorozat-modellben.

[![A modell keresési panelje](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Idősorozat-modell típusa**: az idősorozat-modellezési típusok használatával a számítások végrehajtásához változókat vagy képleteket adhat meg. Egy adott idősorozat-modell példányhoz vannak társítva. Egy típus egy vagy több változóval is rendelkezhet.
* **Idősorozat-modell hierarchiája**: a hierarchiák az adataik szisztematikus szervezetei. A hierarchiák a különböző példányok közötti kapcsolatokat ábrázolják az idősorozat-modellben.
* **Idősorozat-modell példány**: a példányok maguk az idősorozatok. A legtöbb esetben ezek a **DeviceID** vagy a **assetid**, amely az eszköz egyedi azonosítója a környezetben.

Az idősorozat modelljével kapcsolatos további információkért olvassa el a [Times Series-modelleket](./concepts-model-overview.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. hierarchia faszerkezete és keresési panelje

A hierarchia faszerkezetének és a keresési paneljének segítségével könnyedén keresheti meg és navigálhatja az [Idősorozat-modell](./concepts-model-overview.md) hierarchiáját, hogy megkeresse a diagramon megjeleníteni kívánt idősorozat-példányokat. Ha kiválasztja a példányokat, azok nem csak az aktuális diagramhoz lesznek hozzáadva, hanem az adatterülethez is hozzáadódnak.

[![Hierarchia faszerkezete és keresési panelje](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

A keresési eredmények ablaktábla azt is lehetővé teszi, hogy megtekintse az eredményeket egy hierarchia nézetben vagy egy listanézet segítségével, így könnyen megtalálhatja a megjeleníteni kívánt példányokat.

## <a name="4-time-series-well"></a>4. idősorozat – Well

A jól jeleníti meg a példányok mezőit és a kiválasztott idősorozat-modell példányaihoz társított egyéb metaadatokat. A jobb oldalon található jelölőnégyzetek bejelölésével elrejtheti vagy megjelenítheti az aktuális diagram adott példányait.

  [![A Gen2 is](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

A jelenlegi adatokból is eltávolíthat bizonyos adatelemeket, ha kiválasztja a vörös **Törlés** (kuka) vezérlőt az elem bal oldalán. A jól szabályozza azt is, hogy az egyes elemek hogyan jelenjenek meg a diagramon. Dönthet úgy, hogy felveszi a min/max árnyékot, az adatpontokat, átváltja az elemet, és láthatóvá teszi a példányt egy lépcsőzetes módon.

Emellett a feltárások vezérlőelem lehetővé teszi, hogy könnyedén hozza létre az időeltolódásokat és a scatter-ábrákat.

  [![Kiváló elrendezési beállítások](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Ha a következő üzenet jelenik meg, a példány nem rendelkezik a kiválasztott időtartományon belül semmilyen adattal. A probléma megoldásához növelje az időtartamot, vagy győződjön meg arról, hogy a példány az adattovábbítást hajtja végre.
>
> ![Nincs értesítés](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. diagram panel

A diagram lehetővé teszi, hogy a Time Series-példányokat vonalakként jelenítse meg. A diagram méretének növeléséhez a webes vezérlőkre kattintva összecsukhatja a környezeti panelt, az adatmodellt és az időtartományt.

  [![Gen2 diagram – áttekintés](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Diagram típusa**: meghatározza, hogy mely adatelemek érhetők el a vizualizációhoz.

1. **Intervallum mérete**: az intervallum mérete csúszka eszköz lehetővé teszi az intervallumok közötti nagyítást és kicsinyítést az adott időtartományon belül. Ez precízebb vezérlést biztosít a nagy szeletek közötti mozgáshoz, amely az ezredmásodpercben lévő, kis méretű szeleteket mutatja, és lehetővé teszi a részletes, nagy felbontású adatmennyiségek áttekintését. A csúszka alapértelmezett kiindulási pontja a kiválasztott adatok legoptimálisabb nézete lesz. az elosztás feloldása, a lekérdezés sebessége és a részletesség.

1. **Nagyítás és pásztázás**: válassza ezt a vezérlőt a diagram nagyításához és pásztázásához.

1. **Y tengely vezérlőelem**: az elérhető y tengelyes nézet beállításai:

    * `Stacked`: Minden sorban külön Y tengely szerepel.
    * `Overlap`: Több, ugyanazon Y tengelyen lévő sor halmozásához használja az Y tengely adatváltozását a kiválasztott vonal alapján.
    * `Shared`: Az Y tengely összes adathalmaza együtt jelenik meg.

1. **Jelölő elem**: az aktuálisan kijelölt adatelem és a hozzájuk tartozó részletek.

További részletezést adhat egy adott adatszelethez, ha az egér **bal oldali gombjával kattint** egy adatpontra, és lenyomja az egeret, majd a kijelölt területre húzza a kívánt végpontot. **Kattintson a jobb gombbal** a kék, a kijelölt területen, majd válassza a **Nagyítás** lehetőséget az alább látható módon. A kiválasztott TimeSpan is megtekintheti és letöltheti a telemetria eseményeket.

  [![Gen2 diagram nagyítása](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

A **nagyítási** művelet elvégzése után a kiválasztott adatkészlet jelenik meg. Válassza ki a formátum vezérlőelemet, hogy az adatain belüli három y tengelyen történjen a váltás.

  [![Gen2 diagram y tengelye](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Íme egy példa az **átfedésben lévő diagramra** :

  [![Átfedésben lévő diagram beállítása](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

A **További műveletek** gomb kibontja a **Letöltés CSV-ként** való megjelenítését, a **Power BIhoz való kapcsolódást**, a **diagram adatai táblázatként** való megjelenítését és a **nyers események beállításainak megismerését** .

  [![További műveletek lehetőség](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

További információ a [Power bi-összekötő](concepts-power-bi.md) **Csatlakozás Power bi** lehetőségéről.

## <a name="6-time-editor-panel"></a>6. időszerkesztő panel

Azure Time Series Insights Gen2 használatakor először ki kell választania egy időtartományt. A kiválasztott időtartomány szabályozza a Azure Time Series Insights Gen2 Update widgetekkel való manipulációhoz elérhető adathalmazt.

  [![Időkiválasztás panel](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Az idősor egy része sárga vagy narancssárga színnel van kiemelve, hogy jelezze a meleg tárban elérhető adatmennyiséget.

A következő webes vezérlők Azure Time Series Insights Gen2 érhetők el a munkaidő-tartomány kiválasztásához.

  [![Kutatási és ellenőrzési lehetőségek](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Belső dátumtartomány csúszka vezérlőelem**: használja a két végpont vezérlőelemet a kívánt időtartományra húzva. Ezt a belső dátumtartományt a külső dátumtartomány csúszka vezérlőeleme korlátozza.

1. **Dátumtartomány gombjának növelése és csökkentése**: növelje vagy csökkentse az időtartományt úgy, hogy kijelöli a kívánt időszakra vonatkozó gombot.

1. Időtartomány **összeomlásának vezérlése**: Ez a webes vezérlőelem lehetővé teszi az összes vezérlő elrejtését, kivéve a belső dátumtartomány csúszka eszközét.

1. **Külső dátumtartomány csúszka vezérlő**: a végpont vezérlőelemekkel kiválaszthatja a külső dátumtartományt, amely a belső dátumtartomány vezérlőelemhez lesz elérhető.

1. **Időtartomány csúszka vezérlő**: Ezzel gyorsan válthat az előre beállított időtartományok kiválasztásai között, például az elmúlt **30 percben**, az **utolsó 12 órában** vagy egy **Egyéni tartományon**. Az érték módosítása az intervallum-méret csúszka eszközben tárgyalt elérhető intervallum-tartományokat is megváltoztatja.

   [![A és a kiválasztása a kiválasztási panelen](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. app-sáv

Az alkalmazás tetején megjelenik a Azure Time Series Insights Gen2 navigációs panel. A következő funkciókat biztosítja:

### <a name="current-session-share-link-control"></a>Munkamenet jelenlegi megosztási hivatkozásának vezérlője

  [![Megosztás ikon](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Az új **megosztás** ikon kiválasztásával megoszthatja az URL-hivatkozást a csapatával.

  [![Példány URL-címének megosztása](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Bérlő szakasz

  [![Bérlő kiválasztása](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Megjeleníti a jelenlegi Azure Time Series Insights Gen2 bejelentkezési fiókjának adatait.
* Használja az elérhető témák közötti váltáshoz.
* Használatával megtekintheti a Gen2 [bemutató környezetét](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Téma kiválasztása

Új téma kiválasztásához válassza a jobb felső sarokban található profil ikont. Ezután válassza a **téma módosítása** lehetőséget.

  [![Téma kiválasztása](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> A nyelv kiválasztása a profil ikonjának kiválasztásával is elérhető.

Azure Time Series Insights Explorer két témát támogat:

* **Világos téma**: az alapértelmezett téma jelenik meg a dokumentumban.
* **Sötét téma**: a kezelőt az itt látható módon jeleníti meg:

  [![Kiválasztott sötét téma](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="gen1-environment-controls"></a>Gen1 környezeti vezérlők

### <a name="gen2-terms-panel"></a>Gen2 feltételek panel

Ez a szakasz csak olyan meglévő Gen1-környezetekre vonatkozik, amelyek a frissített felhasználói felületen a Explorert használják. Előfordulhat, hogy a Gen1 termék-és Gen2 termékét együtt szeretné használni. Felvettünk néhány funkciót a meglévő felhasználói felületen a frissített Explorerrel, de a Gen1-környezet teljes felhasználói felületi élményét az új Azure Time Series Insights Explorerben érheti el.

A hierarchia helyett megjelenik a Azure Time Series Insights Gen2 terms panel. A feltételek panel lehetővé teszi lekérdezések definiálását a környezetben. Az adathalmaz alapján is szűrheti az adatszűrést.

  [![A lekérdezési panel helye](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

A Azure Time Series Insights Gen2 feltételek szerkesztő panelje a következő paramétereket veszi figyelembe:

**Hol**: a WHERE záradék használatával gyorsan szűrheti az eseményeket az alábbi táblázatban felsorolt operandusok használatával. Ha egy operandus kiválasztásával végez keresést, a rendszer automatikusan frissíti a predikátumot a keresés alapján. A támogatott operandusok típusai a következők:

| Művelet    | Támogatott típusok    | Jegyzetek |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Karakterlánc, bool, Double, DateTime, TimeSpan, NULL |
| `IN` | Karakterlánc, bool, Double, DateTime, TimeSpan, NULL | Az összes operandusnak azonos típusúnak vagy NULL konstansnak kell lennie. |
| `HAS` | Sztring | A jobb oldalon csak konstans karakterlánc-literálok engedélyezettek. Üres karakterlánc és NULL érték nem engedélyezett. |

Ha többet szeretne megtudni a támogatott lekérdezési műveletekről és az adattípusokról, olvassa el a [Time Series kifejezés (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax)című témakört.

### <a name="examples-of-where-clauses"></a>Példák a WHERE záradékokra

  [![WHERE záradék – példák](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Mérték**: egy legördülő lista, amely az aktuális diagram elemeiként használható numerikus oszlopokat (**Double**) jeleníti meg.

**Felosztás**: Ez a legördülő lista megjeleníti a modellben található összes rendelkezésre álló kategorikus oszlopot (karakterláncot), amely alapján csoportosíthatja adatait. Akár öt kifejezést is hozzáadhat ugyanazon az x tengelyen. Adja meg a kívánt paramétereket, majd a **Hozzáadás** gombra kattintva vegyen fel egy friss kifejezést.

  [![Lekérdezés és szűrt nézet](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

A diagram panel elemeinek megjelenítéséhez és elrejtéséhez válassza a látható ikont, ahogy az alábbi képen is látható. A lekérdezések teljes eltávolításához válassza a piros **X** elemet.

  [![Lekérdezett és szűrt lehetőség megszakítása](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Következő lépések

* Tudnivalók az [adatfeldolgozásról](./concepts-ingestion-overview.md) a környezetbe.

* Tekintse meg a cikket a [Storage](concepts-storage.md)szolgáltatásban.

* További információ az [adatmodellezésről](./concepts-model-overview.md) Azure Time Series Insights Gen2.

* Ismerje meg, [Hogyan diagnosztizálhatja és elháríthatja](./how-to-diagnose-troubleshoot.md) a környezetét.
