---
title: 'Oktatóanyag: navigáljon az Azure-beli helyi webhelyre, és keressen rá egy eszközre'
description: Ez az oktatóanyag azt ismerteti, hogyan használhatók a szolgáltatások az Azure hatáskörébe kezdőlapon, és hogyan kereshetők meg a katalógusban.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 02/22/2020
ms.openlocfilehash: 5285d5b2c7cbe6845fb6043fa1096f94254602e7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677830"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>Oktatóanyag: navigáljon az Azure-beli hatáskörébe (előzetes verzió) kezdőlapra, és keressen rá egy adott eszközre.

> [!IMPORTANT]
> Az Azure-beli hatáskörébe jelenleg előzetes verzió érhető el. A [Microsoft Azure előzetes verzióinak kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) közé tartoznak azok a további jogi feltételek, amelyek az Azure olyan szolgáltatásaira vonatkoznak, amelyek béta-, előzetes verziójú vagy egyéb módon még nem lettek nyilvánosan elérhetők.

Ebben az oktatóanyagban megismerheti az Azure-t, és megkeresi a Kezdőlap funkcióit, és megkeresi a katalógusban szereplő eszközöket.

Ez a cikk *egy öt részből álló oktatóanyag-Sorozat 2. része* , amelyben megismerheti, hogyan kezelhető az adatkezelés az adatközpontok között az Azure hatáskörébe. Ez az oktatóanyag az [1. rész: az Azure hatáskörébe tartozó adatvizsgálat](tutorial-scan-data.md) során végzett munkára épül.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
>
> * Navigáljon az Azure-beli helyi weboldalára.
> * Eszköz keresése.
> * Adja hozzá az eszköz tulajdonosát.

## <a name="prerequisites"></a>Előfeltételek

* [Oktatóanyag: az Azure hatáskörébe tartozó adatvizsgálat](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="navigate-the-azure-purview-home-page"></a>Navigáljon az Azure-beli helyi weboldalára

Az alábbi lépések végigvezetik az Azure hatáskörébe tartozó kezdőlapon.

1. Nyissa meg az Azure-beli hatáskörébe tartozó erőforrást a Azure Portalon, és válassza a **hatáskörébe Studio megnyitása** lehetőséget. A rendszer automatikusan átveszi a hatáskörébe Studio kezdőlapját.

   A Kezdőlap tetején megjelenik a katalógus neve és a katalógus-elemzések készlete. Ide tartozik a források, az adategységek és a Szószedet feltételeinek száma. Az összegzésben láthatja, hogy a teljes és a 113-es szószedetben több mint 200 eszköz található. Ez a szám frissül, mivel a szervezet megvizsgálja és további feltételeket hoz létre az Azure hatáskörébe.

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="Az Azure hatáskörébe tartozó kezdőlapot bemutató képernyőkép.":::

1. Válassza az **eszközök tallózása** lehetőséget az összes eszköz megjelenítéséhez.

## <a name="search-for-an-asset"></a>Eszköz keresése

Mielőtt elkezdené, itt egy gyors frissítő néhány alapvető terminológiával:

* **Eszköz**: a katalógus egyetlen objektuma, amely tömör, és az adatbirtokban lévő entitások definícióját tartalmazza. Az entitások közé tartoznak például egy SQL-táblázat, egy Power BI jelentés és a saját adatfeldolgozó-tevékenység.
  
* **Séma**: más néven oszlop vagy attribútum, a séma az eszköz vagy az erőforráscsoport szerkezetét jelöli.

* **Erőforrás-készlet**: a katalógus egyetlen objektuma, amely számos fizikai objektumot jelöl a tárolóban. Ezek az objektumok általában közös sémát, és a legtöbb esetben az elnevezési konvenciót vagy a mappa struktúráját használják. A dátumformátum például *éééé/hh/nn*. További információ: az [erőforrás-készletek ismertetése](concept-resource-sets.md).

* **Eszköz típusa**: az eszközök és az erőforrások csoportjának logikai névvel való csoportosítása, amely általában az adatplatform nevéhez van hozzárendelve.

Az alábbi lépéseket követve megkeresheti az adategységeket, és megjelölheti magát tulajdonosként:

1. A Kezdőlap **keresési katalógus** mezőjébe írja be az adatkészletet tartalmazó erőforráscsoport nevét (az előző oktatóanyagban létrehozott erőforráscsoportot). Megjelenik a javaslatok listája.

1. Válassza a **keresési eredmények megtekintése** lehetőséget. Mivel az összes eszköz az erőforráscsoport nevével kezdődik, mind megjelennek a keresési eredmények között. Az oktatóanyagon kívül az egyes adategységek nevét, nem pedig az erőforráscsoportok keresését kell megkeresnie.

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="A katalógus keresési javaslatainak listáját bemutató képernyőkép.":::

1. A bal oldali navigációs sávon található szűrők használatával szűrheti az eszköz típusát, besorolását, elérhetőségét, címkéjét és szószedetét.

1. Ha meg szeretne keresni egy erőforrás-készletet, kezdje meg beírni a készlet nevét. Ekkor megjelenik a megfelelő kulcsszavakat tartalmazó keresési javaslatok listája. Az abszolút nevekre is rákereshet, ha idézőjelek közé helyezi őket.

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Az Azure hatáskörébe kulcsszó-eszköz keresése":::

## <a name="edit-an-asset"></a>Eszköz szerkesztése

1. Válassza ki az egyik eszközt a keresési eredmények közül. Ezután válassza a **Szerkesztés** lehetőséget.

1. Az **Áttekintés** lapon adhat hozzá leírást.

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="Az Áttekintés lapon található leírás mezőt ábrázoló képernyőkép.":::

1. Válassza a **névjegyek** lapot. A **tulajdonosok** elem mellett, a **felhasználó vagy csoport kiválasztása** területen kezdje el beírni a vállalati e-mail-címét.

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="A feltöltött mezőket bemutató képernyőkép.":::

    A rendszer automatikusan megjeleníti a nevet.

1. A **szakértők** mellett a **felhasználó vagy csoport kiválasztása** területen adjon meg egy nevet (például a felettes nevét), majd kattintson a **Mentés** gombra.

    A leírás, a tulajdonos neve és a szakértő neve mező már fel van töltve.

## <a name="next-steps"></a>Következő lépések

Mielőtt továbblép a következő oktatóanyagra ebben a sorozatban, szánjon néhány további időt az Azure hatáskörébe tartozó Kezdőlap megismerésére. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Navigáljon az Azure-beli helyi weboldalára.
> * Eszköz keresése.
> * Adja hozzá az eszköz tulajdonosát.

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan böngészhet az adategységek között az Azure-ban, és hogyan derítheti fel az eszközök

> [!div class="nextstepaction"]
> [Adatforrások tallózása és a kifejlődésük megtekintése](tutorial-browse-and-view-lineage.md)
