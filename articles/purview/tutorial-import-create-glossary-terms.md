---
title: 'Oktatóanyag: Szószedet kifejezések létrehozása és importálása az Azure Purview-ban (előzetes verzió)'
description: Ez az oktatóanyag azt ismerteti, hogyan hozhat létre szószedeteket, hogyan adhat hozzá szószedeteket egy eszközhöz, és hogyan importálhatja a szószedet kifejezéseket.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: b240806b934cae54810cf9dfe1a6c1f369cede7e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587510"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>Oktatóanyag: Szószedet kifejezések létrehozása és importálása az Azure Purview-ban (előzetes verzió)

> [!IMPORTANT]
> Az Azure Purview jelenleg előzetes verzióban érhető el. A [Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziók kiegészítő használati feltételei további jogi feltételeket tartalmaznak, amelyek bétaverziós, előzetes verziójú vagy egyéb módon még nem általánosan elérhető Azure-funkciókra vonatkoznak.

A szószedet fontos eszköz a katalógus karbantartásához és rendszerezéséhez. A szószedetet úgy építi fel, hogy új kifejezéseket definiál vagy egy kifejezéslistát importál, majd alkalmazza ezeket a kifejezéseket az eszközökre.

Ez az oktatóanyag egy *ötrészes oktatóanyag-sorozat 5.* része, amelyben megismeri az adat tulajdonon keresztüli adatirányítás alapjait az Azure Purview használatával. Ez az oktatóanyag a 4. rész: Erőforráskészletek, részletek, sémák és besorolások megismerés az [Azure Purview (előzetes verzió) 4.](tutorial-schemas-and-classifications.md)részében befejezett munkára épül.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Szószedet kifejezések létrehozása.
> * Szószedet kifejezések hozzáadása egy eszközhöz.
> * Szószedet kifejezések importálása.

## <a name="prerequisites"></a>Előfeltételek

* Oktatóanyag befejezése: Az erőforráskészletek, a részletek, a sémák és a besorolások megismerása az [Azure Purview (előzetes verzió) nézetben.](tutorial-schemas-and-classifications.md)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-glossary-terms"></a>Szószedet-kifejezések létrehozása

A szószedetben üzleti vagy technikai kifejezéseket hozhat létre. Az eszközöket a feltételek alkalmazásával is jegyzetekkel is felhasználhatja.

Az alábbi táblázat a szószedet kifejezésoldalának leggyakoribb **mezőit** összegezve található:

* **Állapot:** Állapot hozzárendelése a kifejezéshez (**Vázlat,** **Jóváhagyott,** **Lejárt**, vagy **Riasztás**).

* **Definíció:** Adja meg a kifejezés definícióját.

* **Betűszó:** Adja meg a kifejezés rövidített verzióját az egyes szavak kezdőbetűivel.

* **Szinonimák:** Válasszon ki más, azonos vagy hasonló definíciókat definiáló kifejezéseket.

* **Kapcsolódó kifejezések:** A szószedetben válasszon ki más kifejezéseket, amelyek ehhez kapcsolódnak, de más definíciójuk van. Ilyenek például az üzleti kifejezésekhez, a kódnévhez vagy a kifejezéshez társítható egyéb kifejezések.

Hozzon létre egy szószedetet az alábbi lépésekkel:

1. Lépjen az Azure Purview-erőforráshoz a Azure Portal majd válassza az **Open Purview Studio (A Purview Studio megnyitása) lehetőséget.** A rendszer automatikusan a Purview Studio kezdőlapjára kerül.

1. A **bal oldali panelen** válassza a Szószedet lehetőséget a **Szószedet kifejezések oldal megnyitásához.**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="Képernyőkép a Szószedet kifejezések lapról.":::

1. Válassza **az Új kifejezés Rendszer**  >  **alapértelmezése Folytatás**  >  **lehetőséget.**

1. Az **Új kifejezés** lap Áttekintés lapján  **adja** meg az új kifejezés Nevét: *Pénzügyi*.

1. Adja meg **a Definíciót:** *Ez a kifejezés a Contoso Inc. pénzügyi információit jelöli.*

1. Az Állapot **legördülő** listában válassza a **Jóváhagyott lehetőséget.**

1. Ha elkészült, válassza a Létrehozás **lehetőséget.**

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="Képernyőkép egy új szószedet létrehozásáról.":::

## <a name="add-glossary-terms-to-an-asset"></a>Szószedet kifejezések hozzáadása egy eszközhöz

1. Az oktatóanyag-sorozat [1.](tutorial-scan-data.md) részében megtanult lépésekkel talál egy adateszközt. Például: **Contoso_CashFlow_{N}.csv.**

1. Az eszközadatok lapon válassza a Szerkesztés **lehetőséget.**

1. Az Áttekintés **lap Szószedet** kifejezések  legördülő listájában válassza a **Pénzügyi** lehetőséget, majd a Mentés **lehetőséget.**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="Képernyőkép egy szószedet egy eszközhöz való hozzáadásáról.":::

1. Az Áttekintés **lap Szószedet** kifejezések szakaszában figyelje meg, hogy a  *Pénzügyi* kifejezés az eszközre lett alkalmazva.

## <a name="import-glossary-terms"></a>Szószedet importálása

Kifejezések tömeges importáláshoz vagy meglévő kifejezések tömeges frissítéséhez töltsön fel egy előre feltöltött sablont a szószedetbe.

Ebben az eljárásban egy .csv-fájlon keresztül importálja a szószedet kifejezéseket:

1. Jegyezze fel, hol tárolta a *StarterKitTerms.csv* nevű fájlt, amely az [oktatóanyag-sorozat 1.](tutorial-scan-data.md)részében letöltött kezdőkészlet része.

   Ez a fájl tartalmazza az adat tulajdonához kapcsolódó előre kitöltve kifejezések listáját.

 > [!Important]
   > Az e-mail-címe a () A CSV-fájlnak az AAD-csoportban található felhasználó elsődleges címének kell lennie. A másodlagos e-mailek, az egyszerű felhasználónév és a nem AAD-e-mailek még nem támogatottak. Le kell cserélnie az e-mail-címeket a szervezet elsődleges AAD-címére.

1. Az importálás megkezdéséhez válassza a **Szószedet, majd** a **Kifejezések importálása lehetőséget.**

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="Képernyőkép a szószedetek importálási mikéntről.":::

1. Az Import **terms (Kifejezések importálása)** lapon válassza a **System default (Rendszer alapértelmezése)** lehetőséget, majd a Continue (Folytatás) **lehetőséget.**

1. Válassza **a Tallózás** lehetőséget, lépjen arra a helyre, ahová *letöltötteStarterKitTerms.csv,* majd válassza a **Megnyitás lehetőséget.**

1. A **feltételek importálásának** megkezdéséhez kattintson az OK gombra.

   Az importálás befejezése után az új szószedet kifejezése megjelenik a szószedet **kifejezési oldalán.**

1. Tekintse meg az újonnan importált kifejezéseket, hogy lássa, hogyan vannak definiálva.

## <a name="create-custom-term-templates"></a>Egyéni kifejezéssablonok létrehozása

Ebben a szakaszban megtudhatja, hogyan hozhat létre egyéni attribútumokkal egy egyéni kifejezéssablont, és hogyan importálhat adatokat csv-sablonfájl használatával.

Számos meglévő rendszerkifejezés-sablon létezik, amelyeket a Szószedet kifejezéssablonok kezelése System kiválasztásával  >    >  **megtekinthet.**

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="rendszerkifejezés-sablonok.":::

Új egyéni kifejezéssablon létrehozásához tegye a következőket:

1. A **bal oldali** menüben válassza a Szószedet lehetőséget.
1. Válassza **a Kifejezéssablonok kezelése**  >  **Egyéni**  >  **Új kifejezéssablon lehetőséget**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="új egyéni kifejezéssablon létrehozása.":::

Az Új **kifejezéssablon képernyőn** tegye a következőket:

1. Adja meg **a sablon nevét:** `Sensitivity level` .
1. Adja meg a kívánt leírást, például: `Indicates the level of sensitivity for this data.`
1. Válassza **az + Új attribútum** lehetőséget az attribútumok hozzáadására szolgáló párbeszédpanel megnyitásához.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="új kifejezés sablonképernyője.":::

1. Az Új **attribútum képernyőn** adja meg a következő paramétereket:

   |Beállítás|Ajánlott érték|
   |---------|-----------|
   |Attribútum neve |bizalmas információ|
   |Mező típusa | Egyetlen választás|
   |Megjelölés kötelezőként | Jelölje be ezt a négyzetet.|
   |+ Választási lehetőség hozzáadása | Adjon hozzá két lehetőséget. "Igen" és "Nem".|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="adjon hozzá egy új attribútumot.":::

1. Ismételje meg az előző lépéseket egy másik, nevű attribútum `can be shared externally` hozzáadásához. A két attribútum hozzáadása után válassza a **Létrehozás lehetőséget.**

## <a name="import-terms-from-a-template"></a>Kifejezések importálása sablonból

A következő lépés egy új kifejezés importálása a létrehozott **Érzékenységi** szint sablonnal. 

1. A **Szószedet kifejezések képernyőjén** válassza a **Kifejezések importálása lehetőséget.**

1. Az **Import terms (Kifejezések** importálása) képernyőn válassza a **Sensitivity level (Bizalmasság szintje)** lehetőséget. Válassza a **Folytatás** lehetőséget.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="Válassza ki a bizalmasság szintjét.":::

1. A Bizalmasság szint **kifejezéssablonja** tartalmazza az alapértelmezett rendszerattribútumokat, valamint a hozzáadott új attribútumokat: `can be shared externally` és `is sensitive information` . Válassza **a Minta letöltése lehetőséget. CSV-fájl.**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="Töltse le a CSV-mintafájlt.":::

1. A rendszer letölt egy sablonfájlt, hogy szerkesztsen és feltöltsön egy új szószedetet az ügyfélattribútumokkal. Nyissa meg a letöltött CSV-fájlt. Adjon hozzá új kifejezéseket és azok megfelelő értékeit új sorokként a CSV-fájlban.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="Kifejezés létrehozása a CSV-fájlban.":::

   A Kapcsolódó feltételek **vagy**  Szinonimák oszlopban felsorolt kifejezések, amelyek még nem léteznek, a fájl feltöltésekor lesznek létrehozva. Ezek a rendszer alapértelmezett **sablonjának használatával lesznek létrehozva.**

1. A fájl feltöltéshez térjen  vissza a Feltételek  importálása képernyőre, és válassza a Select the complete (Kész kiválasztása) melletti **Browse (Tallózás) lehetőséget. A feltölteni kívánt CSV-fájl.** Válassza ki a fájlt a megnyíló párbeszédpanelen, majd kattintson az **OK gombra.**

1. Az új kifejezések megjelenik a szószedet **kifejezési képernyőjén.** Az új kifejezések részleteinek megtekintéséhez kattintson a kifejezés nevére a listában.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Szószedet kifejezések létrehozása.
> * Szószedet kifejezések hozzáadása egy eszközhöz.
> * Szószedetek importálása.

A következő cikk a különböző katalóguselemzésekkel ismerkedik meg.

> [!div class="nextstepaction"]
> [Elemzések értelmezése az Azure Purview-ban](concept-insights.md)
