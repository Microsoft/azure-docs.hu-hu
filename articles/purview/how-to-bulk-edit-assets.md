---
title: Eszközök tömeges szerkesztése a besorolások címkézéséhez, Szószedet feltételei és névjegyek módosítása
description: Megtudhatja, hogyan szerkesztheti az adategységeket az Azure hatáskörébe.
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 4dc1af590a1965c155a7af7b233b431f982a73d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024234"
---
# <a name="how-to-bulk-edit-assets-to-annotate-classifications-glossary-terms-and-modify-contacts"></a>Eszközök tömeges szerkesztése a jegyzetekkel való besoroláshoz, Szószedet feltételei és névjegyek módosítása

Ez a cikk azt ismerteti, hogyan lehet több szószedetet, besorolást, tulajdonost és szakértőt felcímkézni egyetlen művelettel a kiválasztott eszközök listájára.

### <a name="add-assets-to-view-selected-list-using-search"></a>Eszközök hozzáadása a kiválasztott lista megtekintéséhez a keresés használatával

1. Keresse meg azt az adategységet, amelyet hozzá szeretne adni a tömeges szerkesztéshez a listához.

2. A keresési eredmény lapon vigye a kurzort arra az eszközre, amelyet hozzá szeretne adni a tömeges szerkesztés **nézet kiválasztott** listájához, és megjelenik egy jelölőnégyzet.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="Képernyőkép a jelölőnégyzetről.":::

3. Jelölje be a jelölőnégyzetet, ha hozzá szeretné adni a tömeges szerkesztési **nézet kiválasztott** listájához. A Hozzáadás után megjelenik a kijelölt elemek ikon az oldal alján.

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="Képernyőkép a listáról.":::

4. A fenti lépések megismétlésével adja hozzá az összes adategységet a listához.

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>Eszközök hozzáadása a kiválasztott lista az eszköz részletei lapról való megtekintéséhez

1. Az eszköz részletei lapon jelölje be a jelölőnégyzetet a jobb felső sarokban, hogy hozzáadja az eszközt a tömeges szerkesztési **nézet kiválasztott** listájához.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="Az eszköz képernyőképe.":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>Az eszközök tömeges szerkesztése a kiválasztott listában a Szószedet feltételeinek hozzáadásához, cseréjéhez vagy eltávolításához.

1. Az egyik készen áll az összes olyan adategység azonosítására, amelyet tömegesen kell szerkeszteni, válassza a **kiválasztott lista megtekintése** a keresési eredményekből vagy az eszköz adatai lapról lehetőséget.

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="Képernyőkép a nézetről.":::

2. Tekintse át a listát, és válassza az **Eltávolítás** lehetőséget, ha el szeretné távolítani az összes feltételt.

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="Képernyőkép az eltávolításról.":::

3. Válassza a tömeges szerkesztés lehetőséget a Szószedet feltételeit felvenni, eltávolítani vagy lecserélni az összes kiválasztott eszközhöz.

4. A Szószedet feltételeinek hozzáadásához válassza a művelet **Hozzáadás** lehetőséget. Válassza ki az új értékben hozzáadni kívánt szószedeti feltételeket. Válassza az alkalmaz lehetőséget a Befejezés gombra.
    - A hozzáadási művelet hozzáfűzi az új értéket az adategységek számára már címkézett Szószedet-kifejezések listájához.  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="Képernyőkép a hozzáadásról.":::

5. A Szószedet kifejezés helyett válassza a művelet **lecserélése a** következővel lehetőséget. Válassza ki az új értékben lecserélni kívánt szószedeti kifejezéseket. Válassza az alkalmaz lehetőséget a Befejezés gombra.
    - A Replace művelet lecseréli a kiválasztott adategységek összes szószedeti feltételét az új értékben kiválasztott kifejezésekre.
   
6. Ha el szeretné távolítani a Szószedet kifejezését, válassza a művelet **eltávolításként** lehetőséget. Válassza az alkalmaz lehetőséget a Befejezés gombra.
    - Az eltávolítási művelet eltávolítja a kiválasztott adategységek összes szószedeti feltételét.
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="Képernyőkép az eltávolítási feltételekről.":::

7. Ismételje meg a fentieket besorolások, tulajdonosok és szakértők számára.

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="Képernyőkép a besorolásokról és a névjegyekről.":::

8. Miután befejezte a tömeges szerkesztési panel bezárását, válassza a **Bezárás** vagy az **összes eltávolítása és bezárás** lehetőséget. A Bezárás nem távolítja el a kiválasztott eszközöket, míg az összes eltávolítása és lezárása eltávolítja az összes kijelölt eszközt.
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="Képernyőkép a bezárásról.":::

   > [!Important]
   > A tömeges szerkesztéshez javasolt eszközök száma 25. Ha több mint 25 elemet választ ki, a teljesítménnyel kapcsolatos problémák merülhetnek fel.
   > A **kiválasztott nézet** mező csak akkor látható, ha van legalább egy eszköz kiválasztva.


Kövesse az [oktatóanyagot: Szószedet-kifejezések létrehozása és importálása](how-to-create-import-export-glossary.md) további információért.
