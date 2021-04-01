---
title: 'Oktatóanyag: az Azure hatáskörébe tartozó erőforrás-készletek, részletek, sémák és besorolások megismerése (előzetes verzió)'
description: Ez az oktatóanyag leírja, hogyan használhatók az erőforrás-készletek, az eszköz adatai, a sémák és a besorolások.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c74324ebeeefeed361c0557c45a280a411effa22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693324"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>Oktatóanyag: az Azure hatáskörébe tartozó erőforrás-készletek, részletek, sémák és besorolások megismerése (előzetes verzió)

> [!IMPORTANT]
> Az Azure-beli hatáskörébe jelenleg előzetes verzió érhető el. A [Microsoft Azure előzetes verzióinak kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) közé tartoznak azok a további jogi feltételek, amelyek az Azure olyan szolgáltatásaira vonatkoznak, amelyek béta-, előzetes verziójú vagy egyéb módon még nem lettek nyilvánosan elérhetők.

Ebben az oktatóanyagban a katalógus fő összetevőit tárja fel: erőforrás-készletek, eszköz adatai, sémák és besorolások.

Ez az oktatóanyag *egy öt részből álló oktatóanyag-Sorozat 4. része* , amelyben megtudhatja, hogyan kezelheti az adatkezelési szabályozást az Azure hatáskörébe. Ez az oktatóanyag a 3. részben befejezett munkavégzésre épül: az adategységek [böngészése az Azure hatáskörébe (előzetes verzió), és megtekintheti a leszármazást](tutorial-browse-and-view-lineage.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Erőforrás-készletek megtekintése.
> * Eszköz adatainak megtekintése
> * Szerkessze a sémát, és adja hozzá a besorolásokat.

## <a name="prerequisites"></a>Előfeltételek

* Teljes [oktatóanyag: az adategységek böngészése az Azure hatáskörébe (előzetes verzió), valamint a kifejlődésük megtekinthető](tutorial-browse-and-view-lineage.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="view-resource-sets"></a>Erőforrás-készletek megtekintése

Az erőforrás-készlet egy olyan objektum a katalógusban, amely számos fizikai objektumot jelöl a tárolóban. Az objektumok általában közös sémát, és a legtöbb esetben egy elnevezési konvenciót vagy mappastruktúrát használják. A dátumformátum például *éééé/hh/nn*. További információ az erőforrás-készletekről: az [erőforrás-készletek ismertetése](concept-resource-sets.md).

1. Nyissa meg az Azure-beli hatáskörébe tartozó erőforrást a Azure Portalon, és válassza a **hatáskörébe Studio megnyitása** lehetőséget. A rendszer automatikusan átveszi a hatáskörébe Studio kezdőlapját.

2. Adja meg a **contoso_staging_positivecashflow_ n** értéket a **Keresés az eszközök** között mezőben, majd válassza ki **Contoso_Staging_PositiveCashFlow_ {n}. csv** fájlt a keresési eredmények közül.

## <a name="view-asset-details"></a>Eszköz adatainak megtekintése

1. Az **Áttekintés** lap megjeleníti a **leírást**, a **Szószedet feltételeit** és a **tulajdonságokat**, például a **qualifiedName**.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="Az erőforrás-készlet eszközének áttekintés lapját ábrázoló képernyőkép.":::

1. A **Tulajdonságok** területen jegyezze fel a következő két mezőt:

   * **partitionCount**: az ehhez az erőforráshoz társított fizikai fájlok számát adja meg.
   * **schemaCount**: a séma azon variációinak számát jelzi, amelyek ezen az erőforrás-készleten belül találhatók.

   Az Azure-beli hatáskörébe az [oktatóanyag-sorozat 1. részében](tutorial-scan-data.md)szereplő vizsgálat befejezése után 24 órán belül feltölti ezeket a tulajdonságokat.

1. Válassza a **névjegyek** lapot a **szakértők** és a **tulajdonosok** értékének áttekintéséhez.

## <a name="edit-the-schema-and-add-classifications"></a>Séma szerkesztése és besorolások hozzáadása

1. Válassza a **séma** fület. Figyelje meg az oszlopnevek és a hozzájuk társított besorolások nevét. Figyelje meg, hogy a vizsgálat automatikusan feltölti a tulajdonságokat.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="A séma lapot ábrázoló képernyőfelvétel":::

1. Az eszköz szerkesztéséhez kattintson a bal felső sarokban található **Szerkesztés** gombra. Ezután válassza a **séma** fület.

1. Adja meg egy oszlop **leírását** , vagy nevezze át az oszlopot egy felhasználóbarát névre.

1. Adjon hozzá egy besorolást az egység szintjén, és válassza ki az oszlop **szintű besorolás** legördülő listát, amely nem rendelkezik beállított besorolással.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="Séma szerkesztése lap":::

1. Ha végzett a módosításokkal, válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Erőforrás-készletek megtekintése.
> * Eszköz adatainak megtekintése
> * Szerkessze a sémát, és adja hozzá a besorolásokat.

Folytassa a következő oktatóanyaggal, amelyből megismerheti a szószedetet, valamint az új használati feltételek definiálásának és importálásának módját.

> [!div class="nextstepaction"]
> [Szószedet-kifejezések létrehozása és importálása](tutorial-import-create-glossary-terms.md)