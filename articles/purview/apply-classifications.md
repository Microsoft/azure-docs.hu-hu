---
title: Besorolások alkalmazása az eszközökön (előzetes verzió)
description: Ez a dokumentum leírja, hogyan alkalmazhatja a besorolásokat az eszközökön.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: d12a7d52562fe32126e12a844c2d36c14cf01431
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553112"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Besorolások alkalmazása az eszközökön az Azure-beli hatáskörébe

Ez a cikk azt ismerteti, hogyan alkalmazhatja a besorolásokat az eszközökön.

## <a name="introduction"></a>Bevezetés

A besorolások lehetnek rendszerek vagy egyéni típusok. A rendszer-besorolások alapértelmezés szerint a hatáskörébe tartoznak. Az egyéni besorolások egy reguláris kifejezési minta alapján hozhatók létre. A besorolások automatikusan vagy manuálisan is alkalmazhatók az eszközökre.

Ez a dokumentum ismerteti, hogyan alkalmazhat besorolásokat az adataira.

## <a name="prerequisites"></a>Előfeltételek

- Egyéni besorolásokat hozhat létre a szükséglet alapján.
- Állítsa be az adatforrások vizsgálatát.

## <a name="apply-classifications"></a>Besorolások alkalmazása
Az Azure-ban a fájlokra, táblákra vagy oszlopokra vonatkozó rendszer-vagy egyéni besorolások alkalmazhatók. Ez a cikk azokat a lépéseket ismerteti, amelyekkel manuálisan alkalmazhatja a besorolásokat az eszközökön.

### <a name="apply-classification-to-a-file-asset"></a>Besorolás alkalmazása egy fájl-eszközre
Az Azure-beli hatáskörébe beolvashatja és automatikusan osztályozhatja a dokumentációs fájlokat. Ha például van egy *multiple.docx* nevű fájl, és a tartalma egy nemzeti azonosító számmal rendelkezik, az Azure hatáskörébe hozzáadja a besorolási **EU nemzeti azonosító számát** a fájl adategységének részletes oldalához.

Bizonyos esetekben előfordulhat, hogy manuálisan szeretne besorolásokat hozzáadni a fájlhoz. Ha több, erőforrás-készletbe csoportosított fájllal rendelkezik, adja hozzá a besorolásokat az erőforrás-készlet szintjén.

A következő lépésekkel adhat hozzá egyéni vagy rendszerbesorolást egy partíciós erőforrás-készlethez:

1. Keresse meg vagy tallózással keresse meg a partíciót, és keresse meg az eszköz részletei lapot.

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="Az eszköz részletei lapot ábrázoló képernyőkép.":::

1. Az **Áttekintés** lapon tekintse meg a **besorolások** szakaszt, és ellenőrizze, hogy vannak-e meglévő besorolások. Válassza a **Szerkesztés** elemet.

1. A **besorolások** legördülő listából válassza ki azokat a besorolásokat, amelyekre kíváncsi. Például a **hitelkártya száma**, amely a rendszer besorolása és **CustomerAccountID**, amely egyéni besorolás.

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="Az objektumhoz hozzáadandó besorolások kiválasztását bemutató képernyőkép.":::

1. Válassza a **Mentés** lehetőséget

1. Az **Áttekintés** lapon győződjön meg arról, hogy a kiválasztott besorolások megjelennek a **besorolások** szakaszban.

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="A besorolások egy eszközhöz való hozzáadását bemutató képernyőkép.":::

### <a name="apply-classification-to-a-table-asset"></a>Besorolás alkalmazása táblázatos eszközre

Amikor az Azure-beli hatáskörébe beolvassa az adatforrásait, nem rendel hozzá automatikusan besorolásokat a Table assets szolgáltatáshoz. Ha azt szeretné, hogy a tábla-eszköz besorolást adjon, manuálisan kell azt felvennie.

Besorolás hozzáadása egy tábla-eszközhöz:

1. Keressen egy olyan táblát, amely érdekli. Például: **Customer** table.

1. Győződjön meg arról, hogy a táblához nincsenek hozzárendelve besorolások. **Szerkesztés** kiválasztása

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="Képernyőfelvétel: a táblázatos eszközök besorolásának megtekintése és szerkesztése.":::

1. A **besorolások** legördülő listában válasszon ki egy vagy több besorolást. Ez a példa egy **CustomerInfo** nevű egyéni besorolást használ, de a lépéshez bármilyen besorolást választhat.

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="A táblázatos eszközhöz hozzáadandó besorolások kiválasztását bemutató képernyőkép.":::

1. A besorolások mentéséhez válassza a **Mentés** lehetőséget.

1. Az **Áttekintés** oldalon ellenőrizze, hogy az Azure-beli hatáskörébe került-e új besorolások.

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="Képernyőfelvétel: a besorolások tábla-eszközhöz való hozzáadásának ellenőrzése.":::

### <a name="add-classification-to-a-column-asset"></a>Besorolás hozzáadása egy oszlopbeli eszközhöz

Az Azure-beli hatáskörébe automatikusan beolvassa és hozzáadja a besorolásokat az oszlop összes objektumához. Ha azonban módosítani szeretné a besorolást, ezt az oszlop szintjén teheti meg.

Besorolás hozzáadása egy oszlophoz:

1. Keresse meg és válassza ki az oszlop objektumot, majd válassza a **Szerkesztés** lehetőséget az **Áttekintés** lapon.

1. Válassza a **séma** fület

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="Az oszlopok sémájának szerkesztését bemutató képernyőkép.":::

1. Azonosítsa az Önt érdeklő oszlopokat, és válassza a **besorolás hozzáadása** lehetőséget. Ez a példa a **PasswordHash** oszlop **általános jelszavas** besorolását adja hozzá.

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="A besorolás oszlopba való felvételét bemutató képernyőkép.":::

1. Válassza a **Mentés** lehetőséget

1. Válassza a **séma** fület, és győződjön meg arról, hogy a besorolás hozzá lett adva az oszlophoz.

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="Képernyőkép arról, hogy miként lehet megerősíteni, hogy egy besorolás hozzá lett adva egy oszlop sémához.":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>A meglévő besorolások újraellenőrzésének következményei

A besorolások alkalmazása az első alkalommal történik, az adatain alapuló minta-ellenőrzési szempontok alapján, és a megadott regex-mintának megfelelően illeszkednek. Az újraellenőrzéskor, ha az új besorolások érvényesek, az oszlop további besorolásokat kap. A meglévő besorolások az oszlopban maradnak, és manuálisan el kell távolítani.

## <a name="next-steps"></a>Következő lépések
Az egyéni besorolások létrehozásával kapcsolatos további információkért lásd: [Egyéni besorolás létrehozása](create-a-custom-classification-and-classification-rule.md).