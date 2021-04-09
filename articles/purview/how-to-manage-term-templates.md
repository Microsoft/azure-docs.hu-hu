---
title: Az üzleti kifejezések sablonjának kezelése Szószedet
description: Megtudhatja, hogyan kezelheti az üzleti szószedetet az Azure-ban az Azure hatáskörébe tartozó adatkatalógusban.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553346"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>Az üzleti kifejezések sablonjának kezelése Szószedet

Az Azure-beli hatáskörébe olyan fogalmakat hozhat létre, amelyek az adatgazdagítás szempontjából fontosak. A hatáskörébe Data Catalog Szószedethez hozzáadott minden új kifejezés egy olyan term sablonon alapul, amely meghatározza a kifejezéshez tartozó mezőket. Ez a cikk azt ismerteti, hogyan hozható létre egy olyan term sablon és egyéni attribútum, amely Szószedet-kifejezésekhez társítható.

## <a name="manage-term-templates-and-custom-attributes"></a>A lejárati sablonok és az egyéni attribútumok kezelése

A term sablonok használatával egyéni attribútumokat hozhat létre, csoportosíthatja őket, és alkalmazhat egy sablont a feltételek létrehozásakor. A kifejezés létrehozása után a kifejezéshez társított sablon nem módosítható.

1. A **Szószedet feltételei** lapon válassza a **Sablonok kezelése** lehetőséget.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="Képernyőkép a Szószedet feltételeiről > a sablonok kifejezésének kezelése gomb.":::

2. Az oldalon a rendszerek és az egyéni attribútumok is láthatók. Válassza az **Egyéni** fület a term sablonok létrehozásához vagy szerkesztéséhez.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="Képernyőkép a Szószedet feltételeiről > a term sablonok kezelése lap.":::

3. Válassza az **+ új kifejezés sablon** lehetőséget, és adja meg a sablon nevét és leírását.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="Képernyőkép a Szószedet feltételeiről > a term sablonok kezelése > új term sablonok":::

4. Válassza az **+ új attribútum** lehetőséget, ha új egyéni attribútumot szeretne létrehozni a term sablonhoz. Adja meg az attribútum nevét és leírását. Az egyéni attribútum nevének egyedinek kell lennie egy lejárati sablonon belül, de lehet, hogy ugyanazt a nevet lehet használni a sablonok között.

   Válassza ki a mező típusát a következő lehetőségek közül: **szöveg**, **egyetlen választás**, **több választási** lehetőség vagy  **dátum**. A szövegmezők alapértelmezett értékének karakterláncát is megadhatja.  Az attribútum **kötelezőként** is megjelölhető.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="Képernyőkép a Szószedet-kifejezések > új attribútum oldaláról.":::

5. Az összes egyéni attribútum létrehozása után az **előnézet** gombra kattintva rendezheti az egyéni attribútumok sorrendjét. Egyéni attribútumokat húzhat át a kívánt sorba.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="Képernyőkép a Szószedet feltételeiről > előzetes verzió sablonja.":::

6. Ha az összes egyéni attribútum definiálva van, a **Létrehozás** gombra kattintva hozhat létre egyéni attribútumokkal rendelkező term sablont.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="Képernyőkép a Szószedet feltételeiről > új term sablon – létrehozás gomb.":::

7. A meglévő egyéni attribútumok érvényessége lejártként jelölhető meg, ha a **megjelölés lejártként** van megjelölve. A lejárt után az attribútum nem aktiválható újra. A lejárt attribútum szürkén jelenik meg a meglévő feltételeknél. Az ezzel a term sablonnal létrehozott jövőbeli új kifejezések többé nem fogják megjeleníteni a lejártként megjelölt attribútumot.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="Képernyőkép a Szószedet feltételeiről > az attribútum szerkesztése a lejártként való megjelöléséhez.":::

## <a name="next-steps"></a>Következő lépések

Kövesse az [oktatóanyagot: Szószedet-kifejezések létrehozása és importálása](tutorial-import-create-glossary-terms.md) további információért.
