---
title: Hivatkozási adatkészletek hozzáadása a környezethez – Azure Time Series Insights | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan adhat hozzá egy hivatkozási adatkészletet a Azure Time Series Insights-környezetében lévő adatbővítéshez.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: d80d97a609aa3a464b9b114439fe7f4058e287c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001830"
---
# <a name="create-a-reference-data-set-for-your-azure-time-series-insights-gen1-environment-using-the-azure-portal"></a>Hozzon létre egy hivatkozási adatkészletet a Azure Time Series Insights Gen1-környezethez a Azure Portal használatával

> [!CAUTION]
> Ez egy Gen1-cikk.

Ez a cikk azt ismerteti, hogyan adhat hozzá egy hivatkozási adatkészletet a Azure Time Series Insights-környezethez. A hivatkozási adatok hasznosak a forrásadatok összekapcsolásához az értékek kibővítéséhez.

A hivatkozási adatkészletek olyan elemek gyűjteményei, amelyek kibővítik az esemény forrásának eseményeit. Azure Time Series Insights beáramlási motor az eseményforrás minden eseményét összekapcsolja a hivatkozási adathalmaz megfelelő adatsorával. Ez a kibővített esemény ezután lekérdezhető. Ez a csatlakozás a hivatkozási adatkészletben definiált elsődleges kulcs oszlop (ok) alapján történik.

A hivatkozási adathalmazok nem csatlakoznak visszamenőlegesen. Így a rendszer csak a jelenlegi és a jövőbeli bejövő adatok összeegyeztetését és a hivatkozási dátumhoz való csatlakozást követően csatlakozik, miután konfigurálta és feltöltötte.

## <a name="video"></a>Videó

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Tudnivalók a Time Series Insight hivatkozási adatmodelljéről.</br>

> [!VIDEO <https://www.youtube.com/embed/Z0NuWQUMv1o>]

## <a name="add-a-reference-data-set"></a>Hivatkozási adathalmaz hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg a meglévő Azure Time Series Insights-környezetét. A Azure Portal bal oldalán található menüben válassza az **összes erőforrás** lehetőséget. Válassza ki Azure Time Series Insights-környezetét.

1. Válassza az **Áttekintés** lapot. Az oldal tetején található **Essentials** szakasz kibontásával keresse meg a **Time Series Insights Explorer URL-címét** , és nyissa meg a hivatkozást.  

   [![Alapok kibontása szakasz](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Tekintse meg a Azure Time Series Insights-környezet Explorerjét.

1. Bontsa ki a környezeti választót a Azure Time Series Insights Explorerben. Válassza ki az aktív környezetet. Kattintson a jobb felső sarokban található hivatkozási adatikonra az Explorer oldalon.

   [![Hivatkozási érték hozzáadása](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Az új adatkészlet hozzáadásának megkezdéséhez kattintson az **+ adatkészlet hozzáadása** gombra.

   [![Adathalmaz hozzáadása](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. Az **új hivatkozási adatkészlet** lapon válassza ki az adatformátumot:

   - Vesszővel tagolt adatmennyiség esetén válassza a **CSV** lehetőséget. Az első sor fejléc sorként lesz kezelve.
   - Válassza a **JSON-tömböt** a JavaScript-objektumok jelölésére formázott adathoz.

   [![Válassza az adatformátum lehetőséget.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Adja meg az adatmennyiséget a két módszer egyikének használatával:

   - Illessze be az adatfájlokat a szövegszerkesztőbe. Ezután válassza a **hivatkozási adatelemzés** gomb lehetőséget.
   - Kattintson a **Fájl választása** gombra, ha helyi szövegfájlból kívánja felvenni az adatforrást.

   Másolja például a CSV-adathalmazt: [ ![ BEillesztett CSV-fájl](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Például illessze be a JSON-tömböt, és [ ![ illessze be a JSON-adatlapokat](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox) .

   Ha hiba történik az adatértékek elemzésekor, a hiba az oldal alján piros színnel jelenik meg, például: `CSV parsing error, no rows extracted` .

1. Az adatgyűjtés sikeres elemzése után egy adatrács jelenik meg, amely megjeleníti az adatoszlopokat és az azokat jelképező sorokat. A pontosság érdekében tekintse át az adatrácsot.

   [![Hivatkozási adatai áttekintése](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Tekintse át az egyes oszlopokat, hogy megértse a feltételezett adattípust, és szükség esetén módosítsa az adattípust.  Válassza ki az adattípus szimbólumát az oszlop fejlécében: **#** dupla (numerikus), **T | F** logikai vagy **ABC** karakterlánchoz.

   [![Válassza az oszlopok fejlécek adattípusok elemét.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. Szükség esetén nevezze át az oszlopfejléceket. A kulcs oszlopának neve szükséges az eseményforrás megfelelő tulajdonságához való csatlakozáshoz.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy a hivatkozási adatkulcs oszlopainak nevei pontosan egyeznek az esemény nevével a bejövő adataihoz, beleértve a kis-és nagybetűket is. A nem kulcsos oszlopnevek a beérkező adatoknak a megfelelő hivatkozási adatértékekkel való kiegészítésére szolgálnak.

1. Írjon be egy értéket a **sorok...** mezőre, és szükség szerint tekintse át az adott sorokat. A szűrő hasznos az adatelemzéshez, de a rendszer nem alkalmazza az adatfeltöltés során.

1. Nevezze el az adathalmazt az adatrács fölötti **adatkészlet neve** mező kitöltésével.

    [![Nevezze el az adathalmazt.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Az adatrács fölötti legördülő lista kiválasztásával adja meg az **elsődleges kulcs** oszlopát az adatkészletben.

    [![Válassza ki a kulcs oszlop (oka) t.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Nem kötelező)** Válassza a **+** gombot egy másodlagos kulcs oszlopának összetett elsődleges kulcsként való hozzáadásához. Ha vissza kell vonnia a kijelölést, válassza ki az üres értéket a legördülő menüből a másodlagos kulcs eltávolításához.

1. Az adatok feltöltéséhez kattintson a **sorok feltöltése** gombra.

    [![Sorok feltöltése és az adatok megerősítése.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    Az oldal megerősíti a befejezett feltöltést, és megjeleníti az üzenet **sikeresen feltöltött adatkészletét**.

    > [!WARNING]
    > A hivatkozási adatkészletek között megosztott oszlopok vagy tulajdonságok egy **ismétlődő tulajdonságnév** -feltöltési hibát fognak megjeleníteni. A hiba nem fogja megakadályozni a hivatkozási adatkészletek sikeres feltöltését. A duplikált tulajdonság nevét megosztó sorok kombinálásával lehet eltávolítani.

1. Válassza **a sor hozzáadása, a** **tömeges importálási sorok** vagy az **oszlop hozzáadása** lehetőséget a további hivatkozási adatértékek igény szerinti hozzáadásához.

    [![Sor hozzáadása, tömeges importálási sorok vagy oszlop hozzáadása.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Bármely olyan sor, amely egy egyedi kulcsot oszt meg egy másik sorral, az oszlopok felülbírálva lesznek az egyedi kulcsot használó megosztások utolsó sorában.

   > [!NOTE]
   > A hozzáadott soroknak **nem** kell *téglalap alakúnak* lenniük – előfordulhat, hogy a hivatkozási adatkészletben szereplő többi bejegyzésnél kevesebb, nagyobb vagy eltérő oszlop szerepel.

## <a name="next-steps"></a>Következő lépések

- [Referencia-adatok kezelése](time-series-insights-manage-reference-data-csharp.md) programozott módon.

- A teljes API-referenciáért olvassa el a [Reference-adatapi](/rest/api/time-series-insights/gen1-reference-data-api) -dokumentum című dokumentumot.