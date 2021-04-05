---
title: 'Oktatóanyag: Szószedetbeli kifejezések létrehozása és importálása az Azure hatáskörébe (előzetes verzió)'
description: Ez az oktatóanyag leírja, hogyan hozhat létre Szószedet-kifejezéseket, hogyan adhat hozzá Szószedet-kifejezéseket egy adott objektumhoz, és hogyan importálhatja a szószedetet.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 0ea6fcaff1ec699431da8b67adee68735a8611a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696085"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>Oktatóanyag: Szószedetbeli kifejezések létrehozása és importálása az Azure hatáskörébe (előzetes verzió)

> [!IMPORTANT]
> Az Azure-beli hatáskörébe jelenleg előzetes verzió érhető el. A [Microsoft Azure előzetes verzióinak kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) közé tartoznak azok a további jogi feltételek, amelyek az Azure olyan szolgáltatásaira vonatkoznak, amelyek béta-, előzetes verziójú vagy egyéb módon még nem lettek nyilvánosan elérhetők.

A Szószedet egy fontos eszköz a katalógus karbantartásához és rendszerezéséhez. A Szószedet létrehozásához adjon meg új feltételeket, vagy importáljon egy kifejezési listát, majd alkalmazza ezeket a kifejezéseket az eszközeire.

Ez az oktatóanyag *egy öt részből álló oktatóanyag-Sorozat 5. része* , amelyben megtudhatja, hogyan kezelheti az adatkezelési szabályozást az Azure hatáskörébe. Ez az oktatóanyag a 4. részben elvégzett munkára épül [: az Azure hatáskörébe (előzetes verzió) az erőforrás-készletek, a részletek, a sémák és a besorolások megismerése](tutorial-schemas-and-classifications.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Szószedet-kifejezések létrehozása.
> * Szószedet-kifejezések hozzáadása egy eszközhöz.
> * Szószedet-kifejezések importálása.

## <a name="prerequisites"></a>Előfeltételek

* Teljes [oktatóanyag: az Azure hatáskörébe (előzetes verzió) az erőforrás-készletek, a részletek, a sémák és a besorolások megismerése](tutorial-schemas-and-classifications.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-glossary-terms"></a>Szószedet-kifejezések létrehozása

A szószedetben üzleti vagy technikai feltételek is létrehozhatók. Az adategységeket a feltételek alkalmazásával is láthatja.

Íme egy összefoglaló a **Szószedet időszakának** leggyakoribb mezőiről:

* **Állapot**: rendeljen hozzá egy állapotot a kifejezéshez (**Piszkozat**, **jóváhagyott**, **lejárt** vagy **riasztás**).

* **Definíció**: adja meg a kifejezés definícióját.

* **Betűszó**: adja meg a kifejezés rövidített verzióját az egyes szavak kezdeti betűi alapján.

* **Szinonimák**: válasszon más kifejezéseket azonos vagy hasonló definíciókkal.

* **Kapcsolódó feltételek**: válassza ki a Szószedet más kifejezéseit, amelyek ehhez kapcsolódnak, de eltérő definíciókkal rendelkeznek. A példák olyan technikai feltételek, amelyek egy üzleti kifejezéssel, egy kód nevével vagy más, a kifejezéssel társítandó kifejezéssel kapcsolatosak.

Hozzon létre egy Szószedet-kifejezést a következő lépésekkel:

1. Nyissa meg az Azure-beli hatáskörébe tartozó erőforrást a Azure Portalon, és válassza a **hatáskörébe Studio megnyitása** lehetőséget. A rendszer automatikusan átveszi a hatáskörébe Studio kezdőlapját.

1. Válassza a **Szószedet** lehetőséget a bal oldali ablaktáblán a **Szószedet feltételei** lap megnyitásához.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="A Szószedet feltételei lapot ábrázoló képernyőfelvétel":::

1. Válassza az **új időszak**  >  **rendszer alapértelmezett**  >  **Folytatás** lehetőséget.

1. Az **új kifejezés** lap **Áttekintés** lapján adja meg az új kifejezés **nevét** : *pénzügyi*.

1. Adja meg a **definíciót**: *Ez a kifejezés a contoso Inc pénzügyi információit jeleníti meg.*

1. Az **állapot** legördülő listában válassza a **jóváhagyva** lehetőséget.

1. Ha elkészült, válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="A Szószedet új kifejezés létrehozásának módját bemutató képernyőkép.":::

## <a name="add-glossary-terms-to-an-asset"></a>Szószedet feltételeinek hozzáadása egy eszközhöz

1. Az [oktatóanyag-sorozat 1. részében](tutorial-scan-data.md) ismertetett lépésekkel megkeresheti az adott eszközt. Például **Contoso_CashFlow_ {N}. csv**.

1. Az eszköz adatai lapon válassza a **Szerkesztés** lehetőséget.

1. Az **Áttekintés** lap **Szószedet-feltételek** legördülő listájában válassza a **pénzügyi** lehetőséget, majd kattintson a **Mentés** gombra.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="Képernyőfelvétel: Szószedet-kifejezés hozzáadása egy eszközhöz.":::

1. Az **Áttekintés** lapon keresse meg a **Szószedet feltételei** szakaszt, és vegye figyelembe, hogy a *pénzügyi* kifejezés az eszközre lett alkalmazva.

## <a name="import-glossary-terms"></a>Szószedet-kifejezések importálása

A kifejezések importálásához vagy a meglévő kifejezések tömeges frissítéséhez töltsön fel egy előre feltöltött sablont a szószedetbe.

Ebben az eljárásban egy. csv-fájlon keresztül importálja a Szószedet kifejezéseit:

1. Vegye figyelembe, hogy hol tárolta a *StarterKitTerms.csv* nevű fájlt, amely a [jelen oktatóanyag-sorozat 1. részében](tutorial-scan-data.md)letöltött Starter Kit része.

   Ez a fájl tartalmazza az adatközponthoz kapcsolódó előre kitöltött feltételek listáját.

1. Az importálás megkezdéséhez válassza a **Szószedet** lehetőséget, majd válassza a **feltételek importálása** lehetőséget.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="A Szószedet használati feltételeinek importálását bemutató képernyőkép.":::

1. Az **importálási feltételek** lapon válassza a **rendszer alapértelmezése** lehetőséget, majd kattintson a **Folytatás** gombra.

1. Válassza a **Tallózás** lehetőséget, lépjen arra a helyre, ahová a letöltötte *StarterKitTerms.csv*, majd válassza a **Megnyitás** lehetőséget.

1. A feltételek importálásának megkezdéséhez kattintson **az OK gombra** .

   Az importálás befejezése után az új szószedeti feltételek a **Szószedet feltételei** lapon jelennek meg.

1. Tekintse meg, hogy az újonnan importált kifejezések hogyan vannak meghatározva.

## <a name="create-custom-term-templates"></a>Egyéni lejáratú sablonok létrehozása

Ebből a szakaszból megtudhatja, hogyan hozhat létre egyéni és egyedi attribútumokkal rendelkező egyéni kifejezéseket tartalmazó sablont, és hogyan importálhat adatimportálást sablon CSV-fájl használatával.

Számos meglévő rendszerkifejezési sablon létezik, amelyet a **Szószedet** a  >  **term templates** System (fogalomtár kezelése  >  )**elem** kiválasztásával tekinthet meg.

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="rendszerállapot-sablonok.":::

Új egyéni lejárati sablon létrehozásához hajtsa végre a következő lépéseket:

1. Válassza a **Szószedet** lehetőséget a bal oldali menüben.
1. Válassza a **feltételek kezelése**  >  **Egyéni**  >  **új term sablon**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="screate egy új egyéni kifejezés sablonját.":::

Az **új term sablon** képernyőn hajtsa végre a következő lépéseket:

1. Adja meg a **sablon nevét**: `Sensitivity level` .
1. Adja meg a kívánt leírást, például `Indicates the level of sensitivity for this data.`
1. Válassza az **+ új attribútum** lehetőséget az attribútumok hozzáadására szolgáló párbeszédpanel megnyitásához.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="az új term sablon képernyő indítása.":::

1. Az **új attribútum** képernyőn adja meg a következő paramétereket:

   |Beállítás|Ajánlott érték|
   |---------|-----------|
   |Attribútum neve|bizalmas információ|
   |Mező típusa|legördülő|Egyetlen választás|
   |Megjelölés kötelezőként|Jelölje be ezt a négyzetet.|
   |+ Választási lehetőség hozzáadása| Adjon hozzá két lehetőséget. "Igen" és "nem".|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="Adjon hozzá egy új attribútumot.":::

1. Az előző lépések megismétlésével adjon hozzá egy másik attribútumot a névvel `can be shared externally` . Mindkét attribútum hozzáadása után végül válassza a **Létrehozás** lehetőséget.

## <a name="import-terms-from-a-template"></a>Kifejezések importálása sablonból

Ezután importáljon egy új kifejezést a létrehozott **érzékenységi szint** sablonnal. 

1. A **Szószedet feltételei** képernyőn válassza a **feltételek importálása** elemet.

1. Az **importálási feltételek** képernyőn válassza az **érzékenységi szint** lehetőséget. Válassza a **Folytatás** lehetőséget.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="Válassza az érzékenységi szint lehetőséget.":::

1. Az **érzékenységi szint** sablonjának kifejezése alapértelmezett rendszerattribútumokat tartalmaz, valamint a hozzáadott új attribútumok: `can be shared externally` és `is sensitive information` . Válassza **a minta letöltése lehetőséget. CSV** -fájl.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="Minta CSV-fájl letöltése.":::

1. A rendszer letölti az új szószedetet az ügyfél-attribútumokkal, és feltölti azokat. Nyissa meg a letöltött CSV-fájlt. Adja hozzá az új kifejezéseket és a megfelelő értékeket új sorokként a CSV-fájlban.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="Hozzon létre egy kifejezést a CSV-fájlban.":::

   A **kapcsolódó feltételek** vagy **szinonimák** oszlopban felsorolt feltételek a fájl feltöltésekor jönnek létre. Ezek a **rendszer alapértelmezett** sablon használatával jönnek létre.

1. A fájl feltöltéséhez térjen vissza az **importálási feltételek** képernyőre, majd válassza a Befejezés lehetőséget a **Tallózás** gombra kattintva **. A feltölteni kívánt CSV-fájl** . Válassza ki a fájlt a megnyíló párbeszédpanelen, majd kattintson **az OK gombra**.

1. Az új feltételek mostantól a **Szószedet feltételei** képernyőn jelennek meg. Az új feltételek részleteit a listában szereplő kifejezés nevére kattintva tekintheti meg.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Szószedet-kifejezések létrehozása.
> * Szószedet-kifejezések hozzáadása egy eszközhöz.
> * Szószedet-kifejezések importálása.

Folytassa a következő cikkel, amely a különböző katalógus-információk megismerését ismerteti.

> [!div class="nextstepaction"]
> [Elemzések értelmezése az Azure Purview-ban](concept-insights.md)
