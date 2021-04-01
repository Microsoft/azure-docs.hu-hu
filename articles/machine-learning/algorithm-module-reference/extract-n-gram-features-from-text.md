---
title: N-Gram-funkciók kinyerése a szöveges modul hivatkozása alapján
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használható a Azure Machine Learning Designer N-Gram-moduljának kinyerése a szöveges adatok szabadkézi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/08/2019
ms.openlocfilehash: 37a10d90fa0e277fbe45d9f1377e365cb3d42996
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96861460"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>N-Gram-funkciók kinyerése a szöveges modul hivatkozása alapján

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti. A nem strukturált szöveges adatok *szabadkézi* használja az N-Gram-funkciók a szöveges modulból való kinyerését. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Az N-Gram-funkciók kinyerésének konfigurációja a szöveges modulból

A modul a következő forgatókönyveket támogatja egy n-Gram szótár használatához:

* [Hozzon létre egy új n-Gram szótárt](#create-a-new-n-gram-dictionary) egy szabad szövegű oszlopból.

* Egy [meglévő szöveges funkciók használatával](#use-an-existing-n-gram-dictionary) szabadkézi egy ingyenes szöveges oszlopot.

* Az n-grammot használó [modellek pontszáma vagy üzembe helyezése](#build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint) .

### <a name="create-a-new-n-gram-dictionary"></a>Új n-Gram szótár létrehozása

1.  Adja hozzá az N-Gram-szolgáltatások kinyerése a folyamathoz szöveget a folyamathoz, és kapcsolódjon a feldolgozni kívánt szöveggel rendelkező adatkészlethez.

1.  A **text oszlop** használatával kiválaszthatja a kiolvasni kívánt szöveget tartalmazó karakterlánc típusú oszlopot. Mivel az eredmények részletesek, egyszerre csak egyetlen oszlopot dolgozhat fel.

1. A **szókincs mód** beállítása, hogy jelezze, hogy az n-Gram-funkciók új listáját **hozza létre.** 

1. Állítsa be az **n-gramm méretet** , hogy jelezze a kinyerni és tárolni kívánt n-grammos *maximális* méretet. 

    Ha például a 3, a unigrams, a bigrams és a Trigrams értéket adja meg, a rendszer létrehozza a következőt:.

1. A **súlyozási függvény** meghatározza, hogyan kell felépíteni a dokumentum-szolgáltatás vektort, és hogyan lehet kinyerni a szókincset a dokumentumokból

    * **Bináris súlyozás**: egy bináris jelenléti értéket rendel a kinyert n-grammhoz. Az egyes n-grammok értéke 1, ha létezik a dokumentumban, és 0 másként.

    * **TF Weight**: egy kifejezési gyakoriság (TF) pontszámot rendel a kinyert n-grammhoz. Az egyes n-grammok értéke az előfordulás gyakorisága a dokumentumban.

    * Az **IDF súlyozása**: egy inverz dokumentum-gyakoriság (IDF) pontszámot rendel a kinyert n-grammhoz. Az egyes n-gramok értéke a corpus méretének a teljes corpusban való előfordulási gyakorisága alapján felosztott naplója.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **TF-IDF súlyozás**: a kinyert n-g értékhez egy kifejezés gyakorisága/inverz dokumentum gyakorisága (TF/IDF) pontszámot rendel. Az egyes n-grammok értéke a TF pontszám szorozva az IDF pontszámával.

1. Megadhatja a **minimális szó hosszát** az n-Gram *egyetlen szavában* használható minimális számú betűvel.

1. A **maximális hossz** értékének megadásával beállíthatja, hogy legfeljebb hány betűt használjon egy n-Gram *egyetlen szavában* .

    Alapértelmezés szerint a Word vagy a token esetében legfeljebb 25 karakter adható meg.

1. Az n-grammos szótárban az n-Gram minimálisan szükséges értékének megadásához használja a **minimum n-Gram-dokumentum abszolút gyakoriságát** . 

    Ha például az alapértelmezett 5 értéket használja, minden n-grammnak legalább ötször szerepelnie kell a corpusban az n-Gram szótárban való felvételhez. 

1.  Adja meg a **maximális n-grammos dokumentum arányát** az adott n-grammot tartalmazó sorok számának maximális arányához a teljes Corpus soraiban.

    Az 1 arány például azt jelzi, hogy ha egy adott n-gramm szerepel az összes sorban, akkor az n-Gram az n-Gram szótárhoz adható. Az egyes sorokban előforduló szavak általában egy zaj szót vesznek, és el lesznek távolítva. A tartománytól függő zajok kiszűréséhez próbálja meg csökkenteni ezt az arányt.

    > [!IMPORTANT]
    > Az adott szavak előfordulási aránya nem egységes. Ez a dokumentumtól eltérő dokumentumra változik. Ha például egy adott termékkel kapcsolatos vásárlói megjegyzéseket elemez, a termék neve nagyon magas gyakorisággal és a zajszinthez közel van, azonban más kontextusban jelentős kifejezésnek kell lennie.

1. Válassza az **n-Gram funkció-vektorok normalizálása** lehetőséget a szolgáltatás-vektorok normalizálása érdekében. Ha ez a beállítás engedélyezve van, az összes n-Gram-szolgáltatás vektora az L2-normával van elosztva.

1. A folyamat elküldése.

### <a name="use-an-existing-n-gram-dictionary"></a>Meglévő n-Gram szótár használata

1.  Adja hozzá az N-Gram-funkciók kibontása a szöveg-modulból a folyamatot, és kapcsolja össze azt az adatkészletet, amely az **adatkészlet** portjára feldolgozni kívánt szöveggel rendelkezik.

1.  A Text ( **szöveg) oszlop** kiválasztásával válassza ki azt a szöveges oszlopot, amely a szabadkézi szöveget tartalmazza. Alapértelmezés szerint a modul a **String** típusú összes oszlopot kiválasztja. A legjobb eredmények érdekében egyszerre csak egyetlen oszlopot kell feldolgoznia.

1. Adja hozzá a korábban generált n-Gram-szótárt tartalmazó mentett adatkészletet, és kapcsolja össze a **bemeneti szókincs** portjával. Az **eredményként szolgáló szókincs** kimenetét is összeállíthatja az N-Gram-funkciók szöveges modulból való kinyeréséhez.

1. **Szókincs mód** esetén a legördülő listából válassza a ReadOnly Update ( **írásvédett** frissítés) lehetőséget.

   A **readonly** kapcsoló a bemeneti szókincshez tartozó bemeneti corpusot jelöli. Az új szöveges adatkészletből (a bal oldali bemeneten) a számítástechnikai kifejezés gyakorisága helyett a bemeneti szókincs n-grammos súlyozását is alkalmazza a rendszer.

   > [!TIP]
   > Akkor használja ezt a beállítást, ha szöveges osztályozó pontozását végzi.

1.  Az összes többi beállításnál tekintse meg az [előző szakaszban](#create-a-new-n-gram-dictionary)található tulajdonságok leírását.

1.  A folyamat elküldése.

### <a name="build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint"></a>A valós idejű végpont üzembe helyezéséhez n-grammot használó következtetési folyamat létrehozása

A betanítási folyamat, amely az **N-gramm kinyerését tartalmazza a szövegből** és a **pontszám-modellből** a tesztelési adatkészlet előrejelzéséhez, a következő struktúrára épül:

:::image type="content" source="./media/module/extract-n-gram-training-pipeline-score-model.png" alt-text="Példa az N-gramm betanítási folyamat kinyerésére" border="true":::

A bekarikázott **kinyerés N-g funkciójának** **szókincs mód** **létrehozása** a szöveges modulból, és a modulhoz kapcsolódó **szókincs mód** , amely a **pontszám modell** modulhoz csatlakozik, **readonly**.

A betanítási folyamat sikeres elküldése után a bekarikázott modul kimenetét adatkészletként regisztrálhatja.

:::image type="content" source="./media/module/extract-n-gram-output-voc-register-dataset.png" alt-text="adatkészlet regisztrálása" border="true":::

Ezután létrehozhat valós idejű következtetési folyamatokat. A következtetési folyamat létrehozása után manuálisan kell módosítania a következtetési folyamatot a következőhöz hasonló módon:

:::image type="content" source="./media/module/extract-n-gram-inference-pipeline.png" alt-text="következtetési folyamat" border="true":::

Ezután küldje el a következtetési folyamatot, és helyezzen üzembe egy valós idejű végpontot.

## <a name="results"></a>Results (Eredmények)

A Text modul N-Gram-funkcióinak kinyerése két típusú kimenetet hoz létre: 

* **Eredmény-adatkészlet**: Ez a kimenet az elemzett szöveg összegzése, amely a kinyert n-grammtal együtt szerepel. A **text (szöveg) oszlopban** nem kiválasztott oszlopok a kimenetre lesznek átadva. Az elemzett szöveg minden egyes oszlopához a modul a következő oszlopokat hozza létre:

  * **N-Gram-előfordulások mátrixa**: a modul létrehoz egy oszlopot a teljes corpusban található minden n-grammhoz, és egy pontszámot ad hozzá az egyes oszlopokban az adott sorhoz tartozó n-gramm súlyozásának jelzéséhez. 

* **Eredmény szókincse**: a szókincs a tényleges n-grammos szótárt tartalmazza, valamint az elemzés részeként generált kifejezés gyakorisági pontszámait. Az adatkészletet a különböző bemenetekkel való újrafelhasználáshoz, vagy egy későbbi frissítéshez is mentheti. A szókincset a modellezéshez és pontozáshoz is felhasználhatja.

### <a name="result-vocabulary"></a>Eredmény szókincse

A szószedet tartalmazza az n-Gram-szótárt, amely az elemzés részeként generált kifejezés gyakorisági pontszámait tartalmazza. A DF és az IDF pontszámok a többi lehetőségtől függetlenül jönnek létre.

+ **ID**: az egyes egyedi n-grammokhoz generált azonosító.
+ **NGram**: az n-Gram. A szóközöket vagy más Word-elválasztó karaktereket az aláhúzás karakter váltja fel.
+ **DF**: az n-gramm kifejezés gyakorisági pontszáma az eredeti corpusban.
+ **IDF**: az eredeti Corpus n-grammjának inverz dokumentumának gyakorisági pontszáma.

Manuálisan frissítheti ezt az adatkészletet, de hibákat is bevezethet. Például:

* A rendszer hibát jelez, ha a modul ismétlődő sorokat talál a bemeneti szókincsben található ugyanazzal a kulccsal. Ügyeljen arra, hogy a szókincsben ne legyen két sor ugyanazzal a Szóval.
* A szókincs-adatkészletek bemeneti sémájának pontosan egyeznie kell, beleértve az oszlopnevek és az oszlopok típusát is. 
* Az **ID** oszlopnak és a **DF** oszlopnak egész típusúnak kell lennie. 
* Az **IDF** -oszlopnak lebegőpontos típusúnak kell lennie.

> [!Note]
> Ne kapcsolja közvetlenül az adatkimenetet a Train Model modulhoz. A kiépítési modellbe való beetetés előtt távolítsa el a szabadszöveges oszlopokat. Ellenkező esetben a szabad szöveges oszlopok kategorikus funkciókként lesznek kezelve.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .
