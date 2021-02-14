---
title: AutoML kísérletek eredményeinek kiértékelése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan tekintheti meg és értékelheti ki az egyes automatizált gépi tanulási kísérletek futtatásához szükséges diagramokat és mérőszámokat.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: d5556ffb18a7a67e9415310f221e470761bf8cb8
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098639"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Az automatizált Machine learning-kísérletek eredményeinek kiértékelése

Ebből a cikkből megtudhatja, hogyan értékelheti ki és hasonlíthatja össze az automatizált gépi tanulás (automatizált ML) kísérlet által képzett modelleket. Az automatizált ML-kísérletek során számos Futtatás jön létre, és mindegyik futtatása létrehoz egy modellt. Az automatizált ML minden modell esetében kiértékelési mérőszámokat és diagramokat hoz létre, amelyek segítenek a modell teljesítményének mérésében. 

Az automatikus ML például a következő diagramokat hozza létre a kísérlet típusa alapján.

| Besorolás| Regresszió/előrejelzés |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Keveredési mátrix](#confusion-matrix)                       | [Reziduálisok hisztogramja](#residuals)        |
| [Fogadó működési karakterisztika (ROC) görbe](#roc-curve) | [Előre jelzett vagy igaz](#predicted-vs-true) |
| [Precíziós visszahívás (PR) görbe](#precision-recall-curve)      |                                          |
| [Emelő görbe](#lift-curve)                                   |                                          |
| [Halmozott nyereségek görbe](#cumulative-gains-curve)           |                                          |
| [Kalibrációs görbe](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. (Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot a](https://aka.ms/AMLFree) Kezdés előtt)
- Egy Azure Machine Learning kísérlet, amely a következőkkel lett létrehozva:
  - A [Azure Machine learning Studio](how-to-use-automated-ml-for-ml-models.md) (nincs szükség kód megírására)
  - A [PYTHON SDK Azure Machine learning](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Futtatási eredmények megtekintése

Az automatikus ML-kísérlet befejezése után a futtatások előzményei a következőn keresztül találhatók:
  - Böngésző [Azure Machine learning Studióval](overview-what-is-machine-learning-studio.md)
  - Jupyter-jegyzetfüzet a [RunDetails Jupyter widget](/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true) használatával

A következő lépések és videók megmutatják, hogyan tekintheti meg a futtatási előzmények és a modell kiértékelésének mérőszámait és diagramokat a Studióban:

1. [Jelentkezzen be a studióba](https://ml.azure.com/) , és navigáljon a munkaterületére.
1. A bal oldali menüben válassza a **kísérletek** lehetőséget.
1. A kísérletek listájából válassza ki a kísérletet.
1. Az oldal alján található táblázatban válassza ki az automatikus ML-futtatást.
1. A **modellek** lapon válassza ki az kiértékelni kívánt modellhez tartozó **algoritmus nevét** .
1. A **metrikák** lapon a bal oldali jelölőnégyzetek használatával megtekintheti a metrikákat és a diagramokat.

![A mérőszámok megtekintéséhez szükséges lépések a Studióban](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Besorolási metrikák

Az automatikus ML kiszámítja a kísérlethez generált egyes besorolási modellek teljesítmény-mérőszámait. Ezek a metrikák a scikit tanulásának megvalósításán alapulnak. 

Számos besorolási metrika van definiálva két osztály bináris besorolásához, és a többosztályos besoroláshoz egy pontszámot kell megkövetelni a több osztályra vonatkozóan. Scikit – a Learn több átlagos módszert is kínál, amelyek közül hármat az automatizált ML a következőkből bocsát ki: **makro**, **mikro** és **súlyozott**.

- **Makró** – az egyes osztályok metrikájának kiszámításához és a súlyozás nélküli átlag meghozatalához
- **Micro** – a metrika globális kiszámításához a teljes igaz pozitív, a hamis negatív és a hamis pozitív érték (az osztályoktól független) számlálásával.
- **Súlyozott** – az egyes osztályok metrikájának kiszámításához és a súlyozott átlagot a kategóriánkénti minták száma alapján kell kiszámítani.

Habár az egyes átlagos módszerek előnyeit a megfelelő módszer kiválasztásakor egy közös megfontolás okozza. Ha az osztályok különböző számú mintát tartalmaznak, érdemes lehet a makrogazdasági átlagot használni, ahol a kisebbségi osztályok a többségi osztályokhoz egyenlő súlyozást kapnak. További információ a [bináris és a többosztályos metrikáról az automatikus ml-ben](#binary-vs-multiclass-classification-metrics). 

A következő táblázat összefoglalja a modell teljesítményének mérőszámait, amelyeket az automatizált ML kiszámít a kísérlethez generált egyes besorolási modellekhez. További részletekért tekintse meg a scikit-Learn dokumentációt, amely az egyes mérőszámok **számítás** mezőjében van társítva. 

|Metric|Leírás|Számítás|
|--|--|---|
|AUC | A AUC a [fogadó operációs karakterisztika görbe](#roc-curve)alatti terület.<br><br> **Cél:** Közelebb 1 annál jobb <br> **Tartomány:** [0, 1]<br> <br>A támogatott metrikák nevei a következők: <li>`AUC_macro`az egyes osztályok AUC számtani középértéke.<li> `AUC_micro`, amely az egyes osztályok valódi pozitív és hamis pozitív értékének kombinálásával lett kiszámítva. <li> `AUC_weighted`a pontszám számtani középértéke az egyes osztályokra vonatkozóan, az egyes osztályokban lévő valódi példányok számával súlyozottan.   |[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| A pontosság az olyan előrejelzések aránya, amelyek pontosan egyeznek a valódi osztályok címkéjével. <br> <br>**Cél:** Közelebb 1 annál jobb <br> **Tartomány:** [0, 1]|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|Az átlagos pontosság egy precíziós visszahívás görbét összegzi, amely az egyes küszöbértékekben elért pontosságok súlyozott középértékét jelenti, és a rendszer visszahívja az előző küszöbértéket, amelyet a súlyozáshoz használtak. <br><br> **Cél:** Közelebb 1 annál jobb <br> **Tartomány:** [0, 1]<br> <br>A támogatott metrikák nevei a következők:<li>`average_precision_score_macro`az egyes osztályok átlagos pontossági pontszámának számtani középértéke.<li> `average_precision_score_micro`a kiszámításához a valódi pozitív és a hamis pozitív értéket kombinálja minden egyes levágáskor.<li>`average_precision_score_weighted`az egyes osztályok átlagos pontossági pontszámának számtani középértéke, az egyes osztályokban lévő valódi példányok számával súlyozottan.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|A kiegyensúlyozott pontosság az egyes osztályok visszahívásának számtani középértéke.<br> <br>**Cél:** Közelebb 1 annál jobb <br> **Tartomány:** [0, 1]|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|Az F1 pontszám a pontosság és a visszahívás harmonikus középértéke. Ez a hamis pozitív és a hamis negatív értéknek megfelelő kiegyensúlyozott mérték. Azonban nem veszi figyelembe az igaz negatív értéket. <br> <br>**Cél:** Közelebb 1 annál jobb <br> **Tartomány:** [0, 1]<br> <br>A támogatott metrikák nevei a következők:<li>  `f1_score_macro`: az F1 pontszám számtani középértéke minden osztálynál. <li> `f1_score_micro`: kiszámítva, a teljes valódi pozitív, a hamis negatív és a hamis pozitív érték megszámlálásával. <li> `f1_score_weighted`: súlyozott középérték az egyes osztályokhoz tartozó F1-pontszámok osztályának gyakorisága alapján.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Ez a (MULTINOMIAL) logisztikai regresszió és bővítmények (például neurális hálózatok) által használt veszteséges függvény, amely negatív naplózási valószínűséggel van meghatározva az igaz címkék valószínűsége alapján, az osztályozó jóslatai. <br><br> **Cél:** Közelebb 0 annál jobb <br> **Tartomány:** [0, inf)|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| A normalizált makrók felidézése a makró-átlagot és normalizált, így a véletlenszerű teljesítmény 0, a tökéletes teljesítmény pedig 1. <br> <br>**Cél:** Közelebb 1 annál jobb <br> **Tartomány:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>ahol `R` a a véletlenszerű előrejelzések várt értéke `recall_score_macro` .<br><br>`R = 0.5`&nbsp;a &nbsp; bináris &nbsp; besoroláshoz. <br>`R = (1 / C)` a C osztályú besorolási problémákhoz.|
matthews_correlation | A Matthews korrelációs együtthatója kiegyensúlyozott mérési pontosság, amely akkor is használható, ha az egyik osztály sokkal több mint egy másikat tartalmaz. Az 1. együttható a tökéletes előrejelzést, 0 véletlenszerű előrejelzést és-1 inverz előrejelzést jelez.<br><br> **Cél:** Közelebb 1 annál jobb <br> **Tartomány:** [-1, 1]|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
pontosság|A pontosság a modell azon képessége, hogy elkerülje a negatív minták pozitívként való címkézését. <br><br> **Cél:** Közelebb 1 annál jobb <br> **Tartomány:** [0, 1]<br> <br>A támogatott metrikák nevei a következők: <li> `precision_score_macro`, az egyes osztályok pontosságának számtani középértéke. <li> `precision_score_micro`, a kiszámított globálisan a teljes igaz pozitív és a hamis pozitív érték megszámlálásával. <li> `precision_score_weighted`az egyes osztályok pontosságának számtani középértéke, az egyes osztályokban található igaz példányok számával súlyozottan.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
felidézés| A visszahívás a modell azon képessége, amely az összes pozitív mintát felismeri. <br><br> **Cél:** Közelebb 1 annál jobb <br> **Tartomány:** [0, 1]<br> <br>A támogatott metrikák nevei a következők: <li>`recall_score_macro`: az egyes osztályok visszahívásának számtani középértéke. <li> `recall_score_micro`: kiszámított globálisan a teljes igaz pozitív érték, a hamis negatív és a hamis pozitív érték megszámlálásával.<li> `recall_score_weighted`: az egyes osztályok visszahívásának számtani középértéke, az egyes osztályokban található igaz példányok számával súlyozottan.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|A súlyozott pontosság olyan pontosság, amelyben az egyes mintákat az ugyanahhoz az osztályhoz tartozó minták teljes száma alapján súlyozzák. <br><br>**Cél:** Közelebb 1 annál jobb <br>**Tartomány:** [0, 1]|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Bináris és többosztályos besorolási metrikák

Az automatikus ML nem tesz különbséget a bináris és a többosztályos metrikák között. Ugyanazokat az érvényesítési metrikákat kell jelenteni, hogy az adatkészlet két osztályból vagy kettőből áll-e. Néhány metrika azonban többosztályos besorolásra szolgál. Bináris adatkészletre alkalmazva ezek a mérőszámok nem kezelik osztályként az osztályt `true` , ahogy az várható. A többosztályos használatra szánt metrikák a, a vagy a utótaggal vannak ellátva `micro` `macro` `weighted` . Ilyenek például a következők:,,, `average_precision_score` `f1_score` `precision_score` `recall_score` és `AUC` .

Például a felidézés helyett `tp / (tp + fn)` a többosztályos átlagú visszahívás ( `micro` , `macro` , vagy `weighted` ) átlaga szerepel a bináris besorolási adatkészlet mindkét osztályán. Ez egyenértékű az osztály és az osztály visszahívásának kiszámításával `true` `false` , majd a kettő átlagát veszi figyelembe.

## <a name="confusion-matrix"></a>Keveredési mátrix

A zavartsági mátrixok olyan vizualizációt biztosítanak, hogy a gépi tanulási modell a besorolási modellekhez tartozó előrejelzések szerint szisztematikus hibákat eredményez. A "félreértés" szó a névben "zavaros" vagy nem címkézett mintákból származik. A `i` zűrzavaros mátrix sorban és oszlopában lévő egyik cella `j` tartalmazza a kiértékelési adatkészlet azon mintáit, amelyek osztályhoz tartoznak, `C_i` és a modell osztályként vannak osztályozva `C_j` .

A Studióban a sötétebb cella nagyobb számú mintát jelez. Ha **a** legördülő menüben a normalizált nézet lehetőségre kattint, a rendszer az egyes mátrixok soraiban kijelöli az osztálynak előrejelzett osztály százalékos arányát `C_i` `C_j` . Az alapértelmezett **nyers** nézet előnye, hogy megtudhatja, hogy a tényleges osztályok eloszlása milyen egyensúlyhiányt okozott a modellnek a kisebbségi osztályból származó minták, a kiegyensúlyozatlan adatkészletek gyakori problémája miatt.

A jó modell összekeveredési mátrixa a legtöbb mintát az átló mentén fogja tartalmazni.

### <a name="confusion-matrix-for-a-good-model"></a>A jó modell összekeveredési mátrixa 
![A jó modell összekeveredési mátrixa ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Hibás modellhez tartozó összekeveredési mátrix
![Hibás modellhez tartozó összekeveredési mátrix](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC-görbe

A fogadó működési karakterisztika (ROC) görbe a valódi pozitív arány (TPR) és a hamis pozitív arány (FPR) közötti kapcsolatot ábrázolja a döntési küszöbérték változásakor. A ROC-görbe kevésbé informatív lehet, ha magas osztályú egyensúlyhiánysal rendelkező adatkészleteken tanít modelleket, mivel a többségi osztály a kisebbségi osztályokból származó hozzájárulásokat is kiszoríthatja.

A görbe alatti terület (AUC) a helyesen besorolt minták arányában értelmezhető. Pontosabban a AUC a valószínűsége annak, hogy az osztályozó a véletlenszerűen kiválasztott negatív mintának megfelelő, véletlenszerűen kiválasztott pozitív mintát rangsorolja. A görbe alakja megkerüli a TPR és a FPR közötti kapcsolatot a besorolási küszöb vagy a döntés határának függvényében.

A diagram bal felső sarkát megközelítő görbe 100% TPR és 0% FPR, a lehető legjobb modellhez közeledik. A véletlenszerűen kiválasztott modell egy ROC-görbét hoz létre a `y = x` bal alsó sarokban lévő sorban a jobb felső sarokban. Egy rosszabb, mint a véletlenszerű modell egy ROC-görbe lenne, amely a sor alatt van `y = x` .
> [!TIP]
> A besorolási kísérletek esetében az automatikus ML-modellekhez készített összes diagramot felhasználhatja a modell osztályok szerinti kiértékelésére vagy az összes osztály átlagára. A különböző nézetek közötti váltáshoz kattintson a diagram jobb oldalán található jelmagyarázat elemre.
### <a name="roc-curve-for-a-good-model"></a>ROC-görbe jó modellhez
![ROC-görbe jó modellhez](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>Rossz modellhez tartozó ROC-görbe
![Rossz modellhez tartozó ROC-görbe](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Pontosság – visszahívás görbe

A precíziós visszahívás görbe a pontosság és a visszahívás közötti kapcsolatot ábrázolja a döntési küszöbérték változásakor. A visszahívás a modell azon képessége, hogy az összes pozitív mintát és pontosságot felderítse a modell azon képessége, amellyel elkerülhető a negatív minták pozitívként való címkézése. Bizonyos üzleti problémák esetén a hamis negatív és a hamis pozitív állapotok elkerülésének relatív fontossága miatt magasabb szintű visszahívás és nagyobb pontosság szükséges.
> [!TIP]
> A besorolási kísérletek esetében az automatikus ML-modellekhez készített összes diagramot felhasználhatja a modell osztályok szerinti kiértékelésére vagy az összes osztály átlagára. A különböző nézetek közötti váltáshoz kattintson a diagram jobb oldalán található jelmagyarázat elemre.
### <a name="precision-recall-curve-for-a-good-model"></a>Precíziós visszahívás görbéje jó modellhez
![Precíziós visszahívás görbéje jó modellhez](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Precíziós visszahívás görbe hibás modellhez
![Precíziós visszahívás görbe hibás modellhez](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Halmozott nyereségek görbe

Az összesítő nyereség görbe a minták százalékos arányának megfelelően besorolt pozitív minták százalékos arányát ábrázolja az előre jelzett valószínűségi sorrendben vett minták szerint.

A nyereség kiszámításához először rendezze az összes mintát a modell által előre jelzett legmagasabb és legalacsonyabbra. Ezután végezze `x%` el a legmagasabb szintű megbízhatósági előrejelzéseket. Ossza meg az általa észlelt pozitív minták számát `x%` a pozitív minták teljes számával a nyereség beszerzéséhez. Az összesített nyereség azon pozitív minták százalékos aránya, amelyeket a rendszer a pozitív osztályhoz legvalószínűbben használt adatmennyiség százalékos arányának figyelembevételével érzékel.

A tökéletes modell az összes negatív minta fölé rangsorolja az összes pozitív mintát, amely egy halmozott nyereségi görbét mutat be két egyenes szegmensből. Az első egy olyan vonal a- `1 / x` tól, `(0, 0)` ahol `(x, 1)` a `x` pozitív osztályhoz tartozó minták töredékei ( `1 / num_classes` Ha az osztályok egyensúlyban vannak). A második a és a közötti vízszintes `(x, 1)` vonal `(1, 1)` . Az első szegmensben az összes pozitív minta besorolása helyesen történik, és az összesített nyereség `100%` az elsőben `x%` figyelembe vett mintákon belülre esik.

Az alapkonfiguráció véletlenszerűen kiválasztott modellje egy összesített nyereségi görbével fog rendelkezni `y = x` , amely után a rendszer a `x%` csak `x%` a teljes pozitív mintákra vonatkozó példákat észlelte. A tökéletes modell egy olyan mikro-átlag görbével fog rendelkezni, amely a bal felső sarokban található, és egy, az `1 / num_classes` összesített nyereség 100%-os, majd vízszintes, míg az adatszázalék 100.
> [!TIP]
> A besorolási kísérletek esetében az automatikus ML-modellekhez készített összes diagramot felhasználhatja a modell osztályok szerinti kiértékelésére vagy az összes osztály átlagára. A különböző nézetek közötti váltáshoz kattintson a diagram jobb oldalán található jelmagyarázat elemre.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Halmozott nyereség görbéje jó modellhez
![Halmozott nyereség görbéje jó modellhez](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Hibás modell összesített nyereségének görbéje
![Hibás modell összesített nyereségének görbéje](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Emelő görbe

A lift görbe azt mutatja, hogy a modellek hányszor jobbak egy véletlenszerű modellhez képest. A lift a véletlenszerű modell kumulatív nyereségének halmozott nyereségének arányát határozza meg.

Ez a relatív teljesítmény azt a tényt veszi figyelembe, hogy az osztályok számának növelésével nehezebb a besorolás. (A véletlenszerű modell helytelenül Jósolja meg, hogy egy adatkészletből származó minták nagyobb hányada 10 osztályral van összehasonlítva két osztállyal rendelkező adatkészlethez képest)

Az alaptervi görbe az a `y = 1` vonal, ahol a modell teljesítménye konzisztens a véletlenszerű modellekkel. Általánosságban elmondható, hogy a megfelelő modellhez tartozó lift-görbe nagyobb lesz a diagramon, és távolabb van az x tengelytől, ami azt mutatja, hogy ha a modell a legmegbízhatóbb, a becslések szerint több alkalommal is elvégezhető, mint a véletlenszerű találgatás.

> [!TIP]
> A besorolási kísérletek esetében az automatikus ML-modellekhez készített összes diagramot felhasználhatja a modell osztályok szerinti kiértékelésére vagy az összes osztály átlagára. A különböző nézetek közötti váltáshoz kattintson a diagram jobb oldalán található jelmagyarázat elemre.
### <a name="lift-curve-for-a-good-model"></a>A lift görbéje jó modellhez
![A lift görbéje jó modellhez](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Rossz modellhez tartozó lift-görbe
![Rossz modellhez tartozó lift-görbe](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Kalibrációs görbe

A kalibrációs görbe kirajzolja a modell megbízhatóságát az előrejelzések szerint a pozitív minták arányában minden egyes megbízhatósági szinten. Egy jól kalibrált modell megfelelően osztályozza az előrejelzések 100%-át, amelyekhez 100%-os megbízhatóságot rendel hozzá 50, az előrejelzések pedig az 50%-os megbízhatóságot, az előrejelzések 20%-át pedig 20%-os megbízhatóságot rendel hozzá, és így tovább. A tökéletesen kalibrált modell kalibrációs görbével fog rendelkezni a `y = x` vonal után, ahol a modell tökéletesen megjósolja, hogy a minták az egyes osztályokhoz tartoznak-e.

A túlzottan nagy valószínűséggel megjósolható, hogy az egyes minták osztályán felüli valószínűségek, és a kalibrációs görbe a visszafelé "S" értékhez hasonlóan fog megjelenni. A nem megfelelő modell az előre jelzett osztályhoz képest alacsonyabb valószínűséggel rendeli hozzá az átlagot, a társított kalibrációs görbe pedig az "S"-hoz hasonlít. A kalibrációs görbe nem ábrázolja a modell megfelelő besorolási képességét, hanem inkább az előrejelzésekhez való biztonságos hozzárendelés lehetőségét. Egy hibás modell továbbra is jó kalibrációs görbével rendelkezhet, ha a modell helyesen rendeli hozzá az alacsony megbízhatóságot és a magas bizonytalanságot.

> [!NOTE]
> A kalibrációs görbe érzékeny a minták számára, így a kis ellenőrzési csoport olyan zajos eredményeket hozhat létre, amelyeket nehéz lehet értelmezni. Ez nem feltétlenül jelenti azt, hogy a modell nem megfelelően van kalibrálva.

### <a name="calibration-curve-for-a-good-model"></a>Kalibrációs görbe jó modellhez
![Kalibrációs görbe jó modellhez](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Hibás modellhez tartozó kalibrációs görbe
![Hibás modellhez tartozó kalibrációs görbe](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Regresszió/előrejelzési mérőszámok

Az automatikus ML kiszámítja az egyes generált modellekhez tartozó teljesítménymutatókat, függetlenül attól, hogy ez egy regressziós vagy előrejelzési kísérlet. Ezek a metrikák a normalizálás során is lehetővé teszik a különböző tartományokból származó adatokra képzett modellek összehasonlítását. További információ: [metrika normalizálása](#metric-normalization).  

A következő táblázat összefoglalja a regressziós és előrejelzési kísérletekhez generált modellek teljesítményének mérőszámait. A besorolási mérőszámokhoz hasonlóan ezek a metrikák is a scikit-tanulás megvalósításán alapulnak. A megfelelő scikit tanulja a dokumentációt ennek megfelelően a **számítás** mezőben.

|Metric|Leírás|Számítás|
--|--|--|
explained_variance|Az elmagyarázott variancia azt méri, hogy a modell milyen mértékben számítja ki a cél változóban lévő változatot. Ez az eredeti és a hibák eltérésének százalékos csökkenése. Ha a hibák középértéke 0, akkor a meghatározás együtthatója egyenlő (lásd alább r2_score). <br> <br> **Cél:** Közelebb 1 annál jobb <br> **Tartomány:** (-inf, 1]|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|Abszolút érték: a cél és az előrejelzés közötti különbség abszolút értéke a várt érték.<br><br> **Cél:** Közelebb 0 annál jobb <br> **Tartomány:** [0, inf) <br><br> Típusú <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`a mean_absolute_error az adattartományon belül vannak elosztva. | [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|Átlagosan abszolút százalékos hiba (MAPE) az előre jelzett érték és a tényleges érték közötti átlagos különbség mértéke.<br><br> **Cél:** Közelebb 0 annál jobb <br> **Tartomány:** [0, inf) ||
median_absolute_error|A medián abszolút hiba a cél és az előrejelzés közötti összes abszolút különbség középértéke. Ez a veszteség robusztus a kiugró értékeknél.<br><br> **Cél:** Közelebb 0 annál jobb <br> **Tartomány:** [0, inf)<br><br>Típusú <br> `median_absolute_error`<br> `normalized_median_absolute_error`: a median_absolute_error az adattartományon belül vannak elosztva. |[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|Az R ^ 2 a meghatározási együttható vagy a négyzetes hibák százalékos csökkenése egy olyan alapmodellhez képest, amely a középértéket adja eredményül. <br> <br> **Cél:** Közelebb 1 annál jobb <br> **Tartomány:** [-1, 1] <br><br> Megjegyzés: az R ^ 2 gyakran a következő tartományhoz tartozik (-inf, 1], de az automatikus ML-klipek negatív értékeket tartalmaznak a nagyon rossz modellektől a-1 értékig.|[Számítás](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |A legfelső szintű meaned error (GYÖKÁTLAGOS) a cél és az előrejelzés között a várt négyzetes különbség négyzetének gyökere. Elfogulatlan becslés esetén a GYÖKÁTLAGOS egyenlő a szórással.<br> <br> **Cél:** Közelebb 0 annál jobb <br> **Tartomány:** [0, inf)<br><br>Típusú<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: a root_mean_squared_error az adattartományon belül vannak elosztva. |[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|A legfelső szintű négyzetes naplózási hiba a várt szögletes logaritmikus hiba négyzetének gyökere.<br><br>**Cél:** Közelebb 0 annál jobb <br> **Tartomány:** [0, inf) <br> <br>Típusú <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: a root_mean_squared_log_error az adattartományon belül vannak elosztva.  |[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| A lándzsás korreláció a két adathalmaz közötti kapcsolat monotonicity nem számszerű mérőszáma. A Pearson korrelációtól eltérően a lándzsás korreláció nem feltételezi, hogy mindkét adathalmazt általában kiosztják. A többi korrelációs tényezőhöz hasonlóan a lándzsás-1 és 1 közötti változó a 0 értéket jelenti, amely nem jelent korrelációt. Az-1 vagy az 1 korrelációk pontos monoton kapcsolatot jelentenek. <br><br> A spearer egy rangsorok közötti korrelációs metrika, amely azt jelenti, hogy az előre jelzett vagy tényleges értékek módosítása nem változtatja meg a lándzsás eredményt, ha nem változtatják meg az előre jelzett vagy a tényleges értékek rangsorolási sorrendjét.<br> <br> **Cél:** Közelebb 1 annál jobb <br> **Tartomány:** [-1, 1]|[Számítás](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Metrika normalizálása

Az automatikus ML normalizálja a regressziós és előrejelzési mérőszámokat, amelyek lehetővé teszik a különböző tartományokban lévő adatokra kiképzett modellek összehasonlítását. Egy nagyobb tartománnyal rendelkező, nagy mennyiségű adattal rendelkező modellnél nagyobb hiba történt, mint az ugyanazon modell, amely kisebb tartománnyal rendelkező, de nem normalizált adatmennyiséggel rendelkezik.

Habár a hibák mérőszámai nem szabványosítanak szabványos módon, a (z) automatizált ML a következő általános megközelítést alkalmazza a hiba az adatok tartománya szerinti felosztására: `normalized_error = error / (y_max - y_min)`

Az idősorozat-adatokra vonatkozó előrejelzési modell kiértékelése során a (z) automatikus ML további lépéseket tesz annak biztosítására, hogy a normalizálás idősorozat-azonosító (Grain) alapján történjen, mivel minden egyes idősorozathoz a célértékek eltérő eloszlása szükséges.
## <a name="residuals"></a>Reziduálisok

A maradék diagram a regressziós és előrejelzési kísérletekhez generált előrejelzési hibák (maradványok) hisztogramja. A maradékok kiszámítása `y_predicted - y_true` az összes minta alapján történik, majd a modell torzításának megjelenítéséhez hisztogramként jelenik meg.

Ebben a példában azt vegye figyelembe, hogy mindkét modell kis mértékben elfogult, hogy a tényleges értéknél alacsonyabb legyen. Ez nem ritka, ha egy adatkészlet a tényleges célok ferde eloszlásával rendelkezik, de rosszabb modell-teljesítményt jelez. A jó modellnek van egy fennmaradó eloszlása, amely nullánál nagyobb, és a szélsőségesnél több maradványt is tartalmaz. Egy rosszabb modell kiterjesztheti a maradványok eloszlását, és kevesebb mint nulla körüli mintát fog kiosztani.

### <a name="residuals-chart-for-a-good-model"></a>Fennmaradó diagram a jó modellhez
![Fennmaradó diagram a jó modellhez](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Hibás modellhez tartozó fennmaradó diagram
![Hibás modellhez tartozó fennmaradó diagram](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Előre jelzett vagy igaz

A regressziós és előrejelzési kísérletekhez az előre jelzett és a True (igaz) és a modellhez tartozó előrejelzések közötti kapcsolat ábrázolása a megcélzott funkció (igaz/tényleges értékek) és a modell előrejelzései között. A True értékeket az x tengely és az egyes raktárhelyek dobozolnija, és a középértékes előre jelzett értéket a rendszer a hibasávok alapján ábrázolja. Így megtekintheti, hogy egy modell elfogult-e bizonyos értékek előrejelzése felé. A sor az átlagos előrejelzést jeleníti meg, az árnyékolt terület pedig az azt jelenti, hogy az előrejelzések az átlag körüli eltéréseket jelzik.

Gyakran a leggyakoribb igaz érték a legpontosabb előrejelzések a legalacsonyabb variancia esetén. A trendvonal távolsága az ideális `y = x` sorból, ahol kevés igaz érték van a modell teljesítményének megfelelő mértéke a kiugró értékeknél. A diagram alján található hisztogramot a tényleges adateloszlásra vonatkozó okból használhatja. Több olyan adatmintát is beleértve, ahol az eloszlás ritka lehet, így javíthatja a modell teljesítményét a láthatatlan adatforgalomban.

Ebben a példában azt vegye figyelembe, hogy a jobb modell előre jelzett és igaz vonallal rendelkezik, amely közelebb van az ideális `y = x` vonalhoz.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>A jó modellhez tartozó, előre jelzett és igaz diagram
![A jó modellhez tartozó, előre jelzett és igaz diagram](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Az előre jelzett és a True (rossz) modell valódi diagramja
![Az előre jelzett és a True (rossz) modell valódi diagramja](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>A modell magyarázatai és a funkció fontossága

Míg a modell-értékelési mérőszámok és diagramok jól használhatók a modell általános minőségének méréséhez, és azt is megvizsgálják, hogy mely adatkészletek használják az előrejelzések alapjául szolgáló modelleket a felelős AI gyakorlása során. Ezért az automatizált ML modell-értelmező irányítópultot biztosít az adatkészlet funkcióinak relatív hozzájárulásainak méréséhez és jelentéséhez.

![Funkciók fontossága](./media/how-to-understand-automated-ml/how-to-feature-importance.gif)

Az értelmező irányítópult megtekintése a Studióban:

1. [Jelentkezzen be a studióba](https://ml.azure.com/) , és navigáljon a munkaterületre
2. A bal oldali menüben válassza a **kísérletek** elemet.
3. Válassza ki a kísérletet a kísérletek listájából
4. A lap alján található táblázatban válasszon ki egy AutoML-futtatást
5. A Models ( **modellek** ) lapon válassza ki az **algoritmus nevét** , amelyet meg szeretne magyarázni
6. A **magyarázatok** lapon láthatja, hogy már létrejött egy magyarázat, ha a modell a legjobb
7. Új magyarázat létrehozásához válassza a **modell** meghatározása lehetőséget, és válassza ki azt a távoli számítást, amellyel a magyarázatokat szeretné kiszámítani

> [!NOTE]
> Az automatikus ML-magyarázatok és az egyéb előrejelzési modellek jelenleg nem támogatják az ForecastTCN modellt, így korlátozott mértékben férhetnek hozzá az értelmező eszközökhöz.

## <a name="next-steps"></a>Következő lépések
* Próbálja ki a [gépi tanulási modell magyarázó mintájának jegyzetfüzeteit](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
* További információ a [felelős AI-ajánlatokról az automatikus ml-ben](how-to-machine-learning-interpretability-automl.md).
* Az automatizált ML-specifikus kérdésekre való kapcsolódáshoz askautomatedml@microsoft.com .
