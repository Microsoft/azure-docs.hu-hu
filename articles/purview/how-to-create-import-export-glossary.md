---
title: Szószedet használati feltételeinek létrehozása, importálása és exportálása
description: Megtudhatja, hogyan hozhat létre, importálhat és exportálhat Szószedet-kifejezéseket az Azure hatáskörébe.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 7466e143f345ea305c7e9ef118d09fb6f685ac16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694486"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>Szószedet használati feltételeinek létrehozása, importálása és exportálása

Ez a cikk bemutatja, hogyan hozhat létre egy üzleti szószedetet az Azure hatáskörébe tartozó adatkatalógusban, és hogyan importálhat és exportálhat Szószedet-kifejezéseket. CSV-fájlok használatával.

## <a name="create-a-new-term"></a>Új kifejezés létrehozása

Új Szószedet-kifejezés létrehozásához hajtsa végre a következő lépéseket:

1. Válassza ki a Szószedet ikont a Kezdőlap bal oldali navigációs sávján, hogy megnyissa a feltételek listázása lapot.

2. A **Szószedet feltételei** lapon válassza az **+ új kifejezés** lehetőséget. Megnyílik egy lap, amelyen a **rendszer alapértelmezett** sablon van kiválasztva. Válassza ki azt a sablont, amelyben létre szeretné hozni a Szószedet kifejezést, és válassza a **Folytatás** lehetőséget.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="Képernyőkép az új kifejezés létrehozásáról." border="true":::

3. Adjon egy nevet az új kifejezésnek, amelynek egyedinek kell lennie a katalógusban. A kifejezés neve megkülönbözteti a kis-és nagybetűket, ami azt jelenti, hogy a katalógusban a **minta** és a **minta** kifejezést kell megnevezni.

4. Adjon hozzá egy **definíciót**.

5. Állítsa be az **állapotot** a kifejezéshez. Az új kifejezések alapértelmezett értéke a **Piszkozat** állapota.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-options.png" alt-text="Képernyőkép az állapotok lehetőségeiről." border="true":::

   Ezek az állapotjelzők a kifejezéshez társított metaadatok. Jelenleg a következő állapotot állíthatja be az egyes feltételeknél:

   - **Draft**: Ez a kifejezés még nincs hivatalosan implementálva.
   - **Jóváhagyva**: Ez a kifejezés a hivatalos/standard/jóváhagyott.
   - **Lejárt**: Ez a kifejezés már nem használható.
   - **Riasztás**: Ez a kifejezés figyelmet igényel.

6. **Erőforrások** és **betűszó** hozzáadása. Ha a kifejezés a hierarchia részét képezi, az Áttekintés lapon szülő kifejezéseket adhat hozzá a **szülőhöz** .

7. **Szinonimák** és **kapcsolódó kifejezések** hozzáadása a kapcsolódó lapon.

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="Képernyőkép az új kifejezésről > kapcsolódó lapról." border="true":::

8. Ha szeretné, válassza a **névjegyek** fület, és adjon hozzá szakértőket és Sáfárokat a kifejezéshez.

9. Válassza a **Létrehozás** lehetőséget a kifejezés létrehozásához.

## <a name="import-terms-into-the-glossary"></a>Kifejezések importálása a szószedetbe

Az Azure-beli hatáskörébe Data Catalog egy sablon. csv fájlt biztosít a használati feltételek a szószedetbe való importálásához.

Importálhatja a kifejezéseket a katalógusba. A rendszer felülírja a fájl duplikált kifejezéseit.

Figyelje meg, hogy a kifejezések nevei megkülönböztetik a kis-és nagybetűket. Például, `Sample` és `saMple` egyszerre létezhet ugyanabban a szószedetben.

### <a name="to-import-terms-follow-these-steps"></a>A feltételek importálásához kövesse az alábbi lépéseket

1. A **Szószedet feltételei** lapon válassza a **feltételek importálása** lehetőséget.

2. Megnyílik a sablon kifejezése oldal. A sablon kifejezésének felel meg a típusnak. Az importálni kívánt CSV-fájl.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="Képernyőkép a Szószedet feltételei lap importálási feltételek gombján.":::

3. Töltse le a CSV-sablont, és adja meg a feltételeket, amelyeket hozzá szeretne adni.

   > [!Important]
   > A rendszer csak a sablonban elérhető oszlopok importálását támogatja. A "rendszer alapértelmezett" sablonja az összes alapértelmezett attribútummal rendelkezik.
   > Az egyéni lejárati sablonok azonban kimaradnak a sablonban definiált attribútumokból és a további egyéni attribútumokból is. Ezért a. A CSV-fájl az oszlopok és oszlopnevek teljes számával eltér a kiválasztott sablontól függően. A feltöltés után is áttekintheti a fájlt.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="Képernyőfelvétel a Szószedet feltételeiről lapon válassza a fájl importáláshoz lehetőséget.":::

4. Miután befejezte a. csv-fájl kitöltését, válassza ki az importálni kívánt fájlt, majd kattintson **az OK gombra**.

5. A rendszer feltölti a fájlt, és hozzáadja az összes feltételt a katalógushoz.

## <a name="export-terms-from-glossary-with-custom-attributes"></a>Kifejezések exportálása a szószedetből egyéni attribútumokkal

Ha a kiválasztott feltételek ugyanahhoz a lejárati sablonhoz tartoznak, exportálhatja a kifejezéseket a szószedetből.

1. Ha a szószedetben szerepel, alapértelmezés szerint az **Exportálás** gomb le van tiltva. Miután kiválasztotta az exportálni kívánt kifejezéseket, az **Exportálás** gomb engedélyezve lesz, ha a kijelölt feltételek ugyanahhoz a sablonhoz tartoznak.

2. Válassza az **Exportálás** lehetőséget a kiválasztott feltételek letöltéséhez.

 > [!Important]
   > Ha a hierarchiában lévő feltételek különböző lejárati sablonokhoz tartoznak, akkor azokat egymástól eltérő módon kell felosztani. CSV-fájlok importáláshoz. Egy kifejezés szülőjének frissítése jelenleg nem támogatott az importálási folyamat használatával.


## <a name="next-steps"></a>Következő lépések

Kövesse az [oktatóanyagot: Szószedet-kifejezések létrehozása és importálása](tutorial-import-create-glossary-terms.md) további információért.
