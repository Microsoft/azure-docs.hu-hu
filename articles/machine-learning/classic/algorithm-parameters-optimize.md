---
title: 'ML Studio (klasszikus): algoritmusok optimalizálása – Azure'
description: A cikk azt ismerteti, hogyan választhatja ki az algoritmushoz beállított optimális paramétereket Azure Machine Learning Studio (klasszikus).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: a216dc7cb17b9e35e412c6bebe34c0cccfb732e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519303"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-machine-learning-studio-classic"></a>Paraméterek kiválasztása az algoritmusok optimalizálásához Machine Learning Studio (klasszikus)

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Ez a témakör azt ismerteti, hogyan választhatja ki a megfelelő hiperparaméter-készletet Azure Machine Learning Studio (klasszikus) algoritmushoz. A legtöbb gépi tanulási algoritmushoz paraméterek vannak beállítva. A modellek betanításakor meg kell adnia a paraméterek értékeit. A betanított modell hatékonysága a választott modell-paraméterektől függ. A paraméterek optimális készletének megkeresésének folyamata modell- *kiválasztás* néven ismert.



A modell kiválasztásának számos módja van. A gépi tanulásban a kereszt-ellenőrzés az egyik legszélesebb körben használt módszer a modell kiválasztásához, és ez az alapértelmezett modell-kiválasztási mechanizmus a Azure Machine Learning Studio (klasszikus). Mivel a Azure Machine Learning Studio (klasszikus) az R és a Python használatát is támogatja, az R vagy a Python használatával mindig saját modell-kiválasztási mechanizmusokat alkalmazhat.

A legjobb beállításhalmaz megkeresésének folyamata négy lépésből áll:

1. **Adja meg a paraméter területét**: az algoritmushoz először döntse el, hogy pontosan milyen paramétereket szeretne figyelembe venni.
2. **Az átellenőrzési beállítások megadása**: döntse el, hogy az adatkészlethez hogyan kell kiválasztania a több érvényesítést.
3. **A metrika meghatározása**: döntse el, hogy melyik mérőszámot kell használni a legjobb paraméterek meghatározásához, például a pontosságot, a legfelső szintű négyzetes hibát, a pontosságot, a visszahívás vagy az f-score értékét.
4. **Betanítási, kiértékelési és összehasonlítási** lehetőségek: a paraméterek értékeinek minden egyedi kombinációja esetén a rendszer az Ön által megadott hiba alapján hajtja végre a kereszt-érvényesítést. A kiértékelés és az összehasonlítás után kiválaszthatja a legjobban teljesítő modellt.

Az alábbi ábrán látható, hogyan érhető el a Azure Machine Learning Studio (klasszikus).

![A legjobb beállításhalmaz megkeresése](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>A paraméter területének megadása
Megadhatja a paramétert a modell inicializálási lépése lépésnél. A gépi tanulási algoritmusok paraméter panelje két oktatói móddal rendelkezik: *egyetlen paraméter és egy* *paraméter tartománya*. Válassza a paraméter tartomány mód lehetőséget. A paraméter tartománya módban több értéket is megadhat az egyes paraméterekhez. Vesszővel tagolt értékeket is megadhat a szövegmezőben.

![Kétosztályos, megnövelt döntési fa, egyetlen paraméter](./media/algorithm-parameters-optimize/fig2.png)

 Másik lehetőségként megadhatja a rács maximális és minimális pontjait, valamint a **használati tartomány-szerkesztővel** előállított pontok teljes számát. Alapértelmezés szerint a paraméterek értékei lineáris skálán jönnek létre. Ha azonban a **naplózási skála be** van jelölve, az értékek a naplózási skálán jönnek létre (azaz a szomszédos pontok aránya állandó a különbség helyett). Az egész szám paraméterek esetében kötőjel használatával adhat meg tartományt. Például a "1-10" érték azt jelenti, hogy az 1 és 10 közötti egész szám (a kettőt is beleértve) a beállított paramétert alkotják. Vegyes mód is támogatott. Például a "1-10, 20, 50" set paraméter a következő egész számot tartalmazza: 1-10, 20 és 50.

![Kétosztályos növelt döntési fa, paraméter-tartomány](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Több érvényesítési hajtogatás megadása
A [partíció és a minta][partition-and-sample] modul használatával véletlenszerűen lehet kiosztani az adategységeket. A modul következő példájának konfigurációjában öt kiosztást definiálunk, és véletlenszerűen rendelünk hozzá egy hajtogatási számot a minta példányokhoz.

![Partíció és minta](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>A metrika meghatározása
A [Tune Model hiperparaméterek beállítása][tune-model-hyperparameters] modul támogatást nyújt az adott algoritmus és adatkészlet legjobb paramétereinek kiválasztásához. A modell betanításával kapcsolatos egyéb információk mellett a modul **Tulajdonságok** ablaktáblája tartalmazza a mérőszámot, amely meghatározza a legjobb paraméter-készletet. Két különböző legördülő listát tartalmaz a besorolási és regressziós algoritmusokhoz. Ha a megfontolás alatt álló algoritmus egy besorolási algoritmus, a regressziós metrika figyelmen kívül lesz hagyva, és fordítva. Ebben az adott példában a metrika **pontossággal** van elfoglalva.   

![Megtakarítási paraméterek](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Képzések betanítása, kiértékelése és összehasonlítása
Ugyanez a [hiperparaméterek beállítása][tune-model-hyperparameters] -modul az összes olyan modellt betanítja, amelyek megfelelnek a beállított paramétereknek, kiértékeli a különböző metrikákat, majd a kiválasztott mérőszám alapján létrehoz egy legjobban képzett modellt. Ez a modul két kötelező bemenettel rendelkezik:

* A nem betanított tanuló
* Az adatkészlet

A modul is tartalmaz egy opcionális adatkészlet-bemenetet. Kapcsolja össze az adathalmazt a betöltési információkkal a kötelező adatkészlet-bemenethez. Ha az adatkészlet nem kap semmilyen hajtogatási információt, a rendszer alapértelmezés szerint automatikusan végrehajtja a 10 szoros átellenőrzést. Ha a fold-hozzárendelés nem lett elvégezve, és egy érvényesítési adatkészlet van megadva a választható adatkészlet-porton, akkor a rendszer kijelöl egy vonat-tesztelési módot, és az első adatkészletet használja a modell betanításához az egyes paraméterek kombinációjára.

![Megnövelt döntési fa osztályozó](./media/algorithm-parameters-optimize/fig6a.png)

Ezt követően a modell kiértékelése az ellenőrzési adatkészlet alapján történik. A modul bal oldali kimeneti portja különböző mérőszámokat jelenít meg a paraméterek értékeinek függvényében. A megfelelő kimeneti port biztosítja a betanított modellt, amely megfelel a legjobban teljesítő modellnek a kiválasztott metrika (ebben az esetben a **pontosság** ) szerint.  

![Ellenőrzési adatkészlet](./media/algorithm-parameters-optimize/fig6b.png)

A pontos paramétereket a megfelelő kimeneti port megjelenítésével lehet megtekinteni. Ez a modell egy tesztelési készletben vagy egy működőképes webszolgáltatásban használható a betanított modellként való mentés után.

<!-- Module References -->
[partition-and-sample]: /azure/machine-learning/studio-module-reference/partition-and-sample
[tune-model-hyperparameters]: /azure/machine-learning/studio-module-reference/tune-model-hyperparameters