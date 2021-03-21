---
title: 'Útmutató: a Data Catalog tallózása'
description: Ez a cikk áttekintést nyújt arról, hogyan böngészhet az Azure-beli hatáskörébe Data Catalog az eszköz típusa alapján.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: dd2fd4fbc9d8aecd9821d668dd02bd4b68659cf6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97695054"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása

Ez a cikk azt ismerteti, hogyan derítheti fel az Azure-beli hatáskörébe Data Catalog adatait az adatforrás hierarchikus névtérének használatával.

## <a name="browse-experience"></a>Böngészési élmény

Az adatfogyasztók az egyes adatforrások esetében az ismerős hierarchikus névtérrel tudják felderíteni az összes adatforrást egy Explorer-nézet használatával. Miután regisztrálta és beolvasta az adatforrást, az adatleképezés kinyeri az adatforrás struktúrájának (hierarchikus névterének) adatait. Ezek az adatok az adatfelderítési böngészési élmény összeállítására szolgálnak.

Könnyen megtalálhatja például a *DateDimension* nevű adatkészletet egy *dimenziók* nevű mappában a 2. Azure-beli adat-Lake Storage-ban. A "Tallózás az eszköz típusa szerint" lehetőséget használhatja a ADLS Gen 2 Storage-fiók eléréséhez, majd tallózással keresse meg a szolgáltatás > tároló > mappája (ke) t az adott *dimenziók* mappához, majd tekintse meg a *DateDimension* táblát.

A kapcsolódó adatforrások esetében a hierarchikus névtérrel rendelkező natív böngészési élmény van megadva.

## <a name="browse-the-data-catalog-by-asset-type"></a>Tallózás a Data Catalog az eszköz típusa szerint

1. Az adategységek böngészéséhez válassza a kezdőlapon a **Tallózás az eszköz alapján** lehetőséget.

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="A hatáskörébe tartozó Kezdőlap" border="true":::

1. A **tallózási eszközök típusai** lapon a csempék az adatforrások szerint vannak kategorizálva. Az egyes adatforrások eszközeinek további megismeréséhez válassza ki a megfelelő csempét.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="Eszközök típusának tallózása oldal" border="true":::

1. A következő oldalon a kiválasztott adattípusú legfelső szintű eszközök szerepelnek a listában. Válassza ki az egyik eszközt a tartalom további megismeréséhez.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="Az eszköz típusa megadott tallózási oldal. Példa az Azure Storage-fiókra" border="true":::

1. Ekkor megnyílik az Explorer nézet. A böngészés megkezdéséhez válassza ki az eszközt a bal oldali panelen. Az alárendelt eszközök az oldal jobb oldali paneljén lesznek felsorolva.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="Tallózó nézet" border="true":::

1. Egy eszköz részleteinek megtekintéséhez válassza ki a nevet vagy az ellipszisek gombot a jobb szélen.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="Válassza az ellipszisek gombot az eszköz részletei lap megjelenítéséhez." border="true":::

## <a name="view-related-data-assets"></a>Kapcsolódó adategységek megtekintése

Az eszköz részletei lapon megtekintheti az egyéb kapcsolódó adategységeket is. A *DateDimension* szülő mappájába például megtekintheti a *dimenziók* mappát, vagy akár a tárolóhoz is navigálva megtekintheti az adott hierarchiában található eszközöket.

1. Az eszköz részletei lapon válassza a **kapcsolódó** fület a többi kapcsolódó eszköz megjelenítéséhez.

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="Kapcsolódó indítás lap" border="true":::

1. Az aktuális eszköz teljes hierarchiája a bal oldalon jelenik meg.

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="Hierarchikus struktúra" border="true":::

1. A hierarchia bármelyik szintjének kiválasztásával listázhatja az adott szint alá tartozó közvetlen eszközöket.

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="Válasszon másik hierarchiát" border="true":::

## <a name="next-steps"></a>Következő lépések

- [Szószedet használati feltételeinek létrehozása, importálása és exportálása](how-to-create-import-export-glossary.md)
- [Az üzleti kifejezések sablonjának kezelése Szószedet](how-to-manage-term-templates.md)
