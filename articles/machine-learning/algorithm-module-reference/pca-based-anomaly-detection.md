---
title: 'PCA-Based anomália észlelése: modul leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre a PCA-Based rendellenesség-észlelési modullal egy, a fő összetevő-elemzésen (PCA) alapuló anomália-észlelési modellt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: fa90fcb9ebc17be9a658b08873234eada98b0fba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898465"
---
# <a name="pca-based-anomaly-detection-module"></a>PCA-Based rendellenesség-észlelési modul

Ez a cikk azt ismerteti, hogyan használható a PCA-Based anomáliák észlelése modul a Azure Machine Learning Designerben, hogy a fő összetevők elemzése (PCA) alapján hozzon létre egy anomália-észlelési modellt.

Ez a modul segítséget nyújt olyan modellek létrehozásában, amelyekben könnyű beolvasni az egyik osztályból származó betanítási adatait, például az érvényes tranzakciókat, de nehéz a megcélzó rendellenességeket kielégítő mintákat beolvasni. 

Ha például a csalárd tranzakciókat szeretné felderíteni, gyakran nincs elég példa arra, hogy betanítsa a csalást. Előfordulhat azonban, hogy sok példája van a jó tranzakciókkal kapcsolatban. A PCA-Based rendellenesség-észlelési modul megoldja a problémát az elérhető szolgáltatások elemzésével, amely meghatározza, hogy mit jelent a "normál" osztály. A modul ezután a távolsági metrikákat alkalmazza a rendellenességeket képviselő esetek azonosítására. Ez a megközelítés lehetővé teszi a modell betanítását meglévő, kiegyensúlyozatlan adatértékek használatával.

## <a name="more-about-principal-component-analysis"></a>További tudnivalók a fő összetevők elemzéséről

A PCA a gépi tanulásban kialakított módszer. Gyakran használják a felderítő adatelemzés során, mert az az adat belső struktúráját mutatja be, és ismerteti az adateltérést.

A PEM a több változót tartalmazó adatok elemzésével működik. A változók közötti korrelációkat keres, és meghatározza, hogy a legjobban milyen értékeket kell rögzíteni a kimenetben. Ezeknek az egyesített szolgáltatásoknak a használatával a rendszer a *fő összetevőknek* nevezett, kompakt szolgáltatási terület létrehozására szolgál.

A anomáliák észleléséhez minden új bemenet elemzése megtörténik. Az anomáliák észlelési algoritmusa kiszámítja a eigenvectors kivetítését, és normalizált újraépítési hibával együtt. A normalizált hiba az anomália pontszámként szolgál. Minél nagyobb a hiba, annál több rendellenes a példány.

A PEM működéséről és az anomáliák észlelésének megvalósításáról a következő dokumentumokban talál további információt:

- [Randomizált algoritmus a fő összetevők elemzéséhez](https://arxiv.org/abs/0809.2274), Rokhlin, Szlan és Tygert alapján

- [Véletlenszerű szerkezetű struktúra keresése: valószínűségi algoritmusok a](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) Halko, a Martinsson és a TROPP

## <a name="how-to-configure-pca-based-anomaly-detection"></a>PCA-Based anomáliák észlelésének konfigurálása

1. Adja hozzá a **PCA-alapú anomália-észlelési** modult a folyamathoz a tervezőben. Ez a modul az **anomáliák észlelése** kategóriában található.

2. A modul jobb oldali paneljén válassza a **betanítási mód** lehetőséget. Jelezze, hogy a modellt egy meghatározott paraméterek használatával kívánja-e betanítani, vagy ha egy paraméterrel szeretné megkeresni a legjobb paramétereket.

    Ha tudja, hogyan szeretné konfigurálni a modellt, válassza az **egyetlen paramétert** , és adjon meg egy adott értékeket argumentumként.

3. A **PCA-ben használni kívánt összetevők száma** mezőben adja meg a kimeneti szolgáltatások vagy összetevők számát.

    A felvenni kívánt összetevők közül a megoldás a PCA-t használó Experiment design fontos részét képezi. Általános útmutató, hogy ne tartalmazzon azonos számú PCA-összetevőt, mivel vannak változók. Ehelyett kisebb számú összetevővel kell kezdődnie, és csak néhány feltétel teljesülése esetén növelheti őket.

    A legjobb eredmények akkor érhetők el, ha a kimeneti összetevők száma *kevesebb, mint* az adatkészletben elérhető funkciók oszlopainak száma.

4. A véletlenszerű PCA-képzés során elvégzendő túlmintavételezési mennyiség meghatározása. Az anomáliák észlelésével kapcsolatos problémák esetén a kiegyensúlyozatlan adatelemzések megnehezítik a szabványos PEM-technikák alkalmazását. Bizonyos mennyiségű túlmintavételezés megadásával növelheti a célként megadott példányok számát.

    Ha az **1** érték van megadva, a rendszer nem végez túlmintavételezést. Ha **1-nél** nagyobb értéket ad meg, a modell betanításakor további minták jönnek létre.

    Két lehetőség közül választhat, attól függően, hogy a paramétert használja-e vagy sem:

    - **Túlmintavételezési paraméter a randomizált PCA esetében**: adjon meg egy egész számot, amely a kisebbségi osztály a normál osztályban való túlmintavételezésének arányát jelöli. (Ez a beállítás csak akkor érhető el, ha az **egyetlen paraméteres** betanítási módszert használja.)

    > [!NOTE]
    > A túlmintavételezéses adatkészletet nem lehet megtekinteni. További információ arról, hogyan használják a túlmintavételezést a PCA használatával: [technikai megjegyzések](#technical-notes).

5. Jelölje be a **beviteli funkció engedélyezése kifejezés normalizálása** lehetőséget, hogy az összes bemeneti funkciót nulla középértékre állítsa be. A PEM esetében általában a normalizálás vagy a méretezés nullára javasolt, mivel a PCA célja, hogy maximalizálja a változók közötti eltérést.

    Alapértelmezés szerint ez a beállítás van kijelölve. Törölje a jelölést, ha az értékek már normalizálva lettek egy másik módszerrel vagy méretezéssel.

6. Egy címkézett betanítási adatkészletet és egy betanítási modult is csatlakoztasson.

   Ha az **oktatói mód létrehozása** beállítást **egyetlen paraméterre** állítja be, használja a [Train rendellenesség-észlelési modell](train-anomaly-detection-model.md) modult.

7. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

Ha betanítást végez, mentheti a betanított modellt. Azt is megteheti, hogy a [pontszám modell](score-model.md) modulhoz kapcsolódik a anomáliák eredményeinek előrejelzéséhez.

Egy anomália-észlelési modell eredményének kiértékeléséhez:

1. Győződjön meg arról, hogy a pontszám oszlop mindkét adatkészletben elérhető.

    Ha megpróbál kiértékelni egy anomália-észlelési modellt, és a "nincs pontszám oszlop az összehasonlítható adatkészletben" hibaüzenet jelenik meg, akkor egy olyan tipikus értékelési adatkészletet használ, amely tartalmaz egy címke oszlopot, de nem rendelkezik valószínűségi pontszámokkal. Olyan adatkészletet válasszon, amely megfelel az anomáliák észlelési modelljeinek, beleértve a **pontszámmal** ellátható címkéket és a **felhorzsolt valószínűségi** oszlopokat.

2. Győződjön meg arról, hogy a felirat oszlopai vannak megjelölve.

    Előfordul, hogy a Label oszlophoz társított metaadatokat a folyamat gráfja eltávolítja. Ha ez történik, a [modell kiértékelése](evaluate-model.md) modul használatával összehasonlíthatja a két anomália-észlelési modell eredményét, előfordulhat, hogy a "nincs címke oszlop a pontozásos adatkészletben" hibaüzenet jelenik meg. Vagy előfordulhat, hogy a "nincs címke oszlop szerepel az összehasonlítható, a pontszámmal rendelkező adatkészletben" hibaüzenet jelenik meg.

    Ezeket a hibákat a [modell kiértékelése](evaluate-model.md) modul előtt a [metaadatok szerkesztése](edit-metadata.md) modul hozzáadásával lehet elkerülni. Az oszlop kiválasztó segítségével válassza ki az osztály oszlopot, és a **mezők** listában válassza a **címke** lehetőséget.

3. A [Python-parancsfájl végrehajtása](execute-python-script.md) modul használatával állítsa be a címke oszlopainak kategóriáit **1 (pozitív, normál)** és **0 (negatív, rendellenes)** értékre.

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Technikai megjegyzések

Ez az algoritmus PEM használatával közelíti meg a normál osztályt tartalmazó alterületet. Az alterületet az adatszórási mátrix legfelső eigenvalues társított eigenvectors öleli fel. 

Minden új bemenetnél az anomália detektor először kiszámítja a eigenvectors való leképezését, majd kiszámítja a normalizált újraépítési hibát. Ez a hiba a rendellenesség pontszáma. Minél nagyobb a hiba, annál rendellenesebb a példány. A normál terület kiszámításának részletes ismertetését a wikipedia: [fő összetevő elemzése](https://wikipedia.org/wiki/Principal_component_analysis)című témakörben tekintheti meg. 


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

A Designer modulokra jellemző hibák listáját lásd: [kivételek és hibakódok a tervezőhöz](designer-error-codes.md) .