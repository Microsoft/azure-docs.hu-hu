---
title: AutoML-kísérlet eredményeinek kiértékelése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan lehet megtekinteni és kiértékelni diagramokat és metrikákat az egyes automatizált gépi tanulási kísérletek futtatásaihoz.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: 2bed95385823a167c7a31eed11d752894984ea38
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791876"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Automatizált gépi tanulási kísérlet eredményeinek kiértékelése

Ebből a cikkből megtudhatja, hogyan értékelheti ki és hasonlíthatja össze az automatizált gépi tanulási (automatizált gépi tanulási) kísérlet által betanított modelleket. Az automatizált gépi tanulási kísérlet során számos futtatás jön létre, és mindegyik futtatás létrehoz egy modellt. Az automatizált ml minden modellhez olyan értékelési metrikákat és diagramokat hoz létre, amelyek segítenek a modell teljesítményének mérésében. 

Az automatizált gépi tanulási folyamat például a következő diagramokat hozza létre a kísérlet típusa alapján.

| Osztályozás| Regresszió/előrejelzés |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Keveredési mátrix](#confusion-matrix)                       | [Reziduálisok hisztogramja](#residuals)        |
| [Fogadó működési jellemzők (ROC) görbéje](#roc-curve) | [Előrejelezni és igaz](#predicted-vs-true) |
| [Pontosság-visszahívási (PR) görbe](#precision-recall-curve)      |                                          |
| [Görbét átemelt](#lift-curve)                                   |                                          |
| [Halmozott nyereséggörbéje](#cumulative-gains-curve)           |                                          |
| [Görbe görbe](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. (Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre](https://aka.ms/AMLFree) egy ingyenes fiókot a kezdés előtt)
- Egy Azure Machine Learning, amely a következővel jött létre:
  - A [Azure Machine Learning stúdió](how-to-use-automated-ml-for-ml-models.md) (nincs szükség kódra)
  - Az [Azure Machine Learning Python SDK](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Futtatás eredményeinek megtekintése

Az automatizált gépi tanulási kísérlet befejezése után a futtatás előzményei a következőn keresztül találhatók meg:
  - Böngésző [Azure Machine Learning stúdió](overview-what-is-machine-learning-studio.md)
  - Jupyter-notebook a [RunDetails Jupyter widgettel](/python/api/azureml-widgets/azureml.widgets.rundetails)

A következő lépések és videó bemutatja, hogyan lehet megtekinteni a futtatáselőzményeket és a modellértékelési metrikákat és diagramokat a studióban:

1. [Jelentkezzen be a Studióba,](https://ml.azure.com/) és lépjen a munkaterületre.
1. A bal oldali menüben válassza a **Kísérletek lehetőséget.**
1. Válassza ki a kísérletét a kísérletek listájából.
1. Az oldal alján található táblázatban válasszon ki egy automatizált ML-futtatás lehetőséget.
1. A **Modellek lapon** válassza  ki az értékelni kívánt modell algoritmusnevét.
1. A **Metrikák lapon** a bal oldali jelölőnégyzetekkel megtekintheti a metrikákat és diagramokat.

![A metrikák Studióban való megtekintésének lépései](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Besorolási metrikák

Az automatizált gépi tanulási modell kiszámítja a kísérlethez létrehozott egyes besorolási modellek teljesítménymetrikákat. Ezek a metrikák a scikit learn implementáción alapulnak. 

Számos besorolási metrika van definiálva két osztály bináris besorolására, és a többosztályos besoroláshoz szükséges átlagolás az osztályokon. A scikit-learn számos átlagolási módszert biztosít, amelyek közül három automatizált ML elérhetővé teszi a **makrót,** a **mikrot** és **a súlyozott metódust.**

- **Makró** – Kiszámítja az egyes osztályok metrikaértékét, és kiszámítja a nem súlyozási átlagot
- **Mikro** – A metrika globális kiszámítása az összes valódi pozitív, hamis negatív és téves pozitív (az osztályoktól független) megszámlálása alapján.
- **Súlyozott –** Kiszámítja az egyes osztályok metrikáit, és a súlyozott átlagot a minták osztályonkénti száma alapján számítja ki.

Bár minden átlagolási módszernek vannak előnyei, a megfelelő módszer kiválasztásakor az osztályegyensúlyosság az egyik gyakori szempont. Ha az osztályok különböző számú mintával vannak, hasznosabb lehet olyan makróátlagot használni, ahol a kisebb osztályok egyenlő súlyozást kapnak a többségi osztályokhoz. További információ a bináris [és a többosztályos metrikákról az automatizált gépi tanulásban.](#binary-vs-multiclass-classification-metrics) 

Az alábbi táblázat összefoglalja azokat a modellteljesítményre vonatkozó metrikákat, amelyek alapján az automatizált gépi tanulási modell kiszámítja a kísérlethez létrehozott egyes besorolási modelleket. További részletekért tekintse meg az egyes metrikák **Számítás** mezőjében hivatkozott scikit-learn dokumentációt. 

|Metric|Leírás|Számítás|
|--|--|---|
|AUC | Az AUC a fogadó működési [jellemző görbe alatti terület.](#roc-curve)<br><br> **Objective (Célkitűzés):** Minél közelebb van az 1-hez, annál jobb <br> **Tartomány:** [0, 1]<br> <br>A támogatott metrikanevek a következők: <li>`AUC_macro`, az egyes osztályok AUC-ének aritmetikai átlagos része.<li> `AUC_micro`, amely az egyes osztályok valódi pozitív és téves pozitív elemeinek kombinálásával van kiszámítva. <li> `AUC_weighted`, az egyes osztályok pontszámának aritmetikai átlagos száma, az egyes osztályokban a valódi példányok számának súlyozása alapján.<br><br>Megjegyzés: Előfordulhat, hogy az automatizált gépi tanulási folyamat által jelentett AUC-értékek nem egyeznek meg a ROC-diagrammal, ha csak két osztály van. Bináris besoroláshoz az AUC mögöttes scikit-learn implementációja valójában nem alkalmaz makró-/mikro-/súlyozott átlagolást. Ehelyett a legvalószínűbb pozitív osztály AUC-ját juk vissza. A ROC-diagram továbbra is bináris besorolásra alkalmazza az osztály átlagolását, ahogy a többosztályos besorolásnál is.  |[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| A pontosság az olyan előrejelzések aránya, amelyek pontosan megfelelnek a valódi osztálycímkéknek. <br> <br>**Objective (Célkitűzés):** Minél közelebb van az 1-hez, annál jobb <br> **Tartomány:** [0, 1]|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|Az átlagos pontosság a pontosság-visszahívási görbét foglalja össze az egyes küszöbértékek során elért pontosság súlyozott középértékével, és a felidézéses görbét az előző küszöbértékhez képest súlyozásként használva. <br><br> **Objective (Célkitűzés):** Minél közelebb van az 1-hez, annál jobb <br> **Tartomány:** [0, 1]<br> <br>A támogatott metrikanevek a következők:<li>`average_precision_score_macro`, az egyes osztályok átlagos pontossági pontszámának számtani középértékét.<li> `average_precision_score_micro`, amely a valódi pozitív és a téves pozitív eredmények kombinálásával számítható ki minden egyes levágásnál.<li>`average_precision_score_weighted`, az egyes osztályok átlagos pontossági pontszámának számtani középértékét az egyes osztályok valódi példányainak számmal súlyozása alapján.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Az kiegyensúlyozott pontosság az egyes osztályok előhívásának aritmetikai meanát jelenti.<br> <br>**Célkitűzés:** Minél közelebb van az 1-hez, annál jobb <br> **Tartomány:** [0, 1]|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|Az F1 pontszám a pontosság és a felidézás harmonikus meanja. Ez a hamis pozitív és a hamis negatív eredmények kiegyensúlyozott mértéke. Azonban nem veszi figyelembe a valódi negatív értékeket. <br> <br>**Célkitűzés:** Minél közelebb van az 1-hez, annál jobb <br> **Tartomány:** [0, 1]<br> <br>A támogatott metrikanevek a következők:<li>  `f1_score_macro`: az F1 pontszám aritmetikai átlagos száma minden osztályban. <li> `f1_score_micro`: az összes valódi pozitív, hamis negatív és téves pozitív megszámlálása alapján számítható ki. <li> `f1_score_weighted`: az egyes osztályok F1 pontszámának osztálygyakorra szerint súlyozott átlagos száma.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Ez a (többnomiális) logisztikai regresszióban és bővítményekben, például neurális hálózatokban használt veszteségi függvény, amely a valódi címkék negatív napló valószínűségeként van meghatározva egy valószínűségi osztályozó előrejelzései alapján. <br><br> **Célkitűzés:** Minél közelebb van a 0-hoz, annál jobb <br> **Tartomány:** [0, inf)|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| A normalizált makró-előhívás a makróátlagos és normalizált előhívás, így a véletlenszerű teljesítmény 0, a tökéletes teljesítmény pedig 1. <br> <br>**Objective (Célkitűzés):** Minél közelebb van az 1-hez, annál jobb <br> **Tartomány:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>ahol a a véletlenszerű előrejelzések `R` `recall_score_macro` várt értéke.<br><br>`R = 0.5`&nbsp;bináris &nbsp; &nbsp; besoroláshoz. <br>`R = (1 / C)` C-osztályú besorolási problémák esetén.|
matthews_correlation | A Matthews korrelációs együttható a pontosság kiegyensúlyozott mértéke, amely akkor is használható, ha az egyik osztály sokkal több mint egy másik mintával rendelkezik. Az 1 együtthatója tökéletes előrejelzést, 0 véletlenszerű előrejelzést és -1 inverz előrejelzést jelez.<br><br> **Objective (Célkitűzés):** Minél közelebb van az 1-hez, annál jobb <br> **Tartomány:** [-1, 1]|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
pontosság|A pontosság a modell azon képessége, hogy elkerülje a negatív minták pozitívként való címkézését. <br><br> **Objective (Célkitűzés):** Minél közelebb van az 1-hez, annál jobb <br> **Tartomány:** [0, 1]<br> <br>A támogatott metrikanevek a következők: <li> `precision_score_macro`, az egyes osztályok pontosságának aritmetikai átlagos számában. <li> `precision_score_micro`, globálisan kiszámítva az összes valódi pozitív és téves pozitív megszámlálása alapján. <li> `precision_score_weighted`, az egyes osztályok pontosságának aritmetikai átlagos száma, az egyes osztályok valódi példányainak szám szerint súlyozása.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
felidézés| A felidézés az a képesség, hogy a modell képes észlelni az összes pozitív mintát. <br><br> **Célkitűzés:** Minél közelebb van az 1-hez, annál jobb <br> **Tartomány:** [0, 1]<br> <br>A támogatott metrikanevek a következők: <li>`recall_score_macro`: az egyes osztályok előhívásának számtani átlagos száma. <li> `recall_score_micro`: globálisan van kiszámítva az összes valódi pozitív, hamis negatív és téves pozitív megszámlálása alapján.<li> `recall_score_weighted`: az egyes osztályok visszahívási aritmetikai átlagos száma, az egyes osztályokban a valódi példányok szám szerint súlyozása.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|A súlyozott pontosság olyan pontosság, amelyben az egyes minták súlyozása az egy osztályba tartozó minták teljes számával van megszűkülve. <br><br>**Célkitűzés:** Minél közelebb van az 1-hez, annál jobb <br>**Tartomány:** [0, 1]|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Bináris és többosztályos besorolási metrikák

Az automatizált ml nem tesz különbséget a bináris és a többosztályos metrikák között. A rendszer ugyanezeket az érvényesítési metrikákat jelenti, ha egy adatkészlet két vagy több osztályból áll. Egyes metrikák azonban többosztályos besorolásra szolgálnak. Bináris adatkészletre alkalmazva ezek a metrikák nem kezelik osztályként a várt `true` módon. A többosztályos besoroláshoz egyértelműen szánt metrikák utótagja `micro` , `macro` vagy `weighted` . Ilyenek például a `average_precision_score` `f1_score` következők: , `precision_score` , , `recall_score` és `AUC` .

Például ahelyett, hogy a visszahívást a értékként számítajuk ki, a többosztályos átlagolás ( , vagy ) egy bináris besorolási adatkészlet mindkét osztályára `tp / (tp + fn)` `micro` `macro` `weighted` átlagot ad. Ez egyenértékű a osztály és az osztály előhívásának külön kiszámításával, majd a kettő `true` `false` átlagának kiszámításával.

Az automatizált ml nem számítja ki a bináris metrikákat, azaz a bináris besorolási adatkészletek metrikákat. Ezek a metrikák azonban manuálisan is kiszámíthatók az automatizált gépi tanulási által az adott futtatáshoz létrehozott keveredő mátrix használatával. [](#confusion-matrix) Kiszámíthatja például a pontosságot( ) egy 2x2-es keveredő mátrixdiagramon látható valódi pozitív és hamis pozitív `tp / (tp + fp)` értékekkel.

## <a name="confusion-matrix"></a>Keveredési mátrix

A keveredés mátrixok vizualizációt biztosítanak a gépi tanulási modellek szisztematikus hibáinak a besorolási modellekre vonatkozó előrejelzései során. A "keveredés" kifejezés a modell "zavaros" vagy félrecímkés mintáiból származik. A keveredési mátrix sorában és oszlopában lévő cella tartalmazza a kiértékelési adatkészlet azon mintáit, amelyek osztályba tartoznak, és a modell osztályként `i` `j` `C_i` osztályba sorolta `C_j` azokat.

A studióban a sötétebb cella több mintát jelez. A **legördülő menü Normalized** (Normalizált) nézetének kiválasztása normalizálja az egyes mátrixsorokat, hogy az osztálynak előrejelezett osztály százalékát `C_i` `C_j` mutassa. Az alapértelmezett nyers  nézet előnye, hogy láthatja, hogy a tényleges osztályok eloszlása kiegyensúlyozatlanság miatt a modell tévesen sorolta-e be a mintákat a kisebb osztályból, ami a kiegyensúlyozatlan adatkészletek gyakori problémája.

Egy jó modell keveredő mátrixa a legtöbb mintát átlósan tartalmazza.

### <a name="confusion-matrix-for-a-good-model"></a>Keveredés mátrix egy jó modellhez 
![Keveredés mátrix egy jó modellhez ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Hibás modell keveredő mátrixa
![Hibás modell keveredő mátrixa](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC-görbe

A fogadó működési jellemzője (ROC) görbéje a valódi pozitív arány (TPR) és a téves pozitív arány (FPR) közötti kapcsolatot ábrázolja a döntési küszöbérték változásának során. A ROC-görbe kevésbé informatív lehet a modellek nagy osztályegyensúlyos adatkészleteken való betanítása során, mivel a többségi osztály kierőltetheti a kisebb osztályok hozzájárulását.

A görbe alatti terület (AUC) a helyesen besorolt minták arányaként értelmezhető. Pontosabban az AUC annak a valószínűsége, hogy az osztályozó egy véletlenszerűen kiválasztott pozitív mintát magasabbra rangsorolja, mint egy véletlenszerűen kiválasztott negatív minta. A görbe alakja intuíciót ad a TPR és az FPR közötti kapcsolathoz a besorolási küszöbérték vagy a döntési határ függvényeként.

A diagram bal felső sarkához közeledő görbe a lehető legjobb modell, a 100%-os TPR és 0% FPR felé közelít. Egy véletlenszerű modell egy ROC-görbét hozna létre a bal alsó saroktól a jobb felső sarokig. `y = x` A véletlenszerűnél rosszabb modell ROC-görbéje a vonal alá `y = x` esne.
> [!TIP]
> A besorolási kísérletek esetében az automatizált gépi tanulási modellekhez készült vonaldiagramok felhasználhatók a modell osztályonkénti kiértékeléséhez vagy az összes osztály átlagolásának kiértékeléséhez. A különböző nézetek között úgy válthat, hogy a diagram jobb oldalon található jelmagyarázatban az osztálycímkékre kattint.

### <a name="roc-curve-for-a-good-model"></a>ROC-görbe egy jó modellhez
![ROC-görbe egy jó modellhez](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>ROC-görbe rossz modellhez
![ROC-görbe rossz modellhez](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Pontosság-visszahívási görbe

A pontosság-visszahívási görbe a döntési küszöbérték változásának során ábrázolja a pontosság és az előhívás közötti kapcsolatot. Az előhívás az, hogy a modell képes észlelni az összes pozitív mintát és pontosságot, az a képesség, hogy a modell képes elkerülni a negatív minták pozitívként való címkézését. Egyes üzleti problémák nagyobb előhívást és némi pontosságot követelnek meg attól függően, hogy mennyire fontos a téves negatív eredmények és a téves riasztások elkerülése.
> [!TIP]
> A besorolási kísérletek esetében az automatizált gépi tanulási modellekhez készült vonaldiagramok felhasználhatók a modell osztályonkénti kiértékeléséhez vagy az összes osztály átlagolásának kiértékeléséhez. A különböző nézetek között úgy válthat, hogy a diagram jobb oldalon található jelmagyarázatban az osztálycímkékre kattint.
### <a name="precision-recall-curve-for-a-good-model"></a>Pontosság-visszahívási görbe jó modellhez
![Pontosság-visszahívási görbe jó modellhez](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Hibás modell pontossági-visszahívási görbéje
![Hibás modell pontossági-visszahívási görbéje](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Halmozott nyereséggörbéje

Az halmozott nyereség görbéje a pozitív minták százalékos arányát ábrázolja helyesen besorolva a minták százalékos arányának függvényeként, ahol a mintákat az előrejelelt valószínűségi sorrendben tekintjük.

A nyereség kiszámításához először rendezheti az összes mintát a modell által előrejel jelzett legmagasabbtól a legkisebb valószínűségig. Ezután vegye `x%` ki a legnagyobb megbízhatósági előrejelzéseket. Ossza el az észlelt pozitív minták számát a pozitív minták teljes számával a `x%` nyereség leszerzése érdekében. A kumulatív nyereség a pozitív minták százalékos aránya, amit akkor észlelünk, ha figyelembe vesszük az adatok egy olyan százalékát, amely a legnagyobb valószínűséggel a pozitív osztályhoz tartozik.

Egy tökéletes modell az összes pozitív mintát az összes negatív minta fölé rangsorolja, így egy két egyenes szegmensből álló halmozott nyereséggörbét kap. Az első egy meredekségű vonal, ahol a pozitív osztályhoz tartozó minták törtrésze ( ha az osztályok `1 / x` `(0, 0)` `(x, 1)` `x` `1 / num_classes` kiegyensúlyozottak). A második egy vízszintes vonal a és a `(x, 1)` `(1, 1)` között. Az első szegmensben minden pozitív minta megfelelően van besorolva, és a kumulatív nyereség az első figyelembe vett `100%` `x%` mintán belülre kerül.

A kiinduló véletlenszerű modell kumulatív nyereséggörbét mutat a következő után, ahol a csak az összes pozitív minta közül körülbelülinak tekintett minták észlelése `y = x` `x%` `x%` történik. A tökéletes modell mikroátlagos görbével rendelkezik, amely a bal felső sarokhoz ér, és egy makróátlagos egyenes, amely meredeksége addig tart, amíg az összesített nyereség nem 100%, majd vízszintes, amíg az adatok százalékos aránya `1 / num_classes` nem 100.
> [!TIP]
> Besorolási kísérletek esetén az automatizált gépi tanulási modellekhez előállított minden vonaldiagram felhasználható a modell osztályonkénti kiértékeléséhez vagy az összes osztály átlagolásának kiértékeléséhez. A különböző nézetek között a diagram jobb oldalon található jelmagyarázatban lévő osztálycímkékre kattintva válthat.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Halmozott nyereséggörbéje egy jó modellhez
![Halmozott nyereséggörbéje egy jó modellhez](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Halmozott nyereséggörbe rossz modell esetében
![Halmozott nyereséggörbe rossz modell esetében](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Görbét átemelt

Az átemelhető görbe azt mutatja meg, hogy egy modell hányszor teljesít jobban egy véletlenszerű modellhez képest. Az átemelhető érték az összesített nyereség és egy véletlenszerű modell halmozott nyereségének aránya.

Ez a relatív teljesítmény azt a tényt veszi figyelembe, hogy a besorolás nehezebbé válik az osztályok számának növelésével. (A véletlenszerű modellek helytelenül előrejeleik egy adatkészletből származó minták magasabb hányadát, 10 osztályt tartalmaznak, míg a két osztályt tartalmaznak)

Az alapérték görbéje az a vonal, amelyben a modell teljesítménye konzisztens `y = 1` egy véletlenszerű modell teljesítményével. Általánosságban elmondható, hogy egy jó modell görbéje magasabb lesz a diagramon, és távolabb van az X tengelytől, és azt mutatja, hogy ha a modell a legbizalmasabb az előrejelzésében, sokkal jobban teljesít, mint a véletlenszerű találgatás.

> [!TIP]
> A besorolási kísérletek esetében az automatizált gépi tanulási modellekhez készült vonaldiagramok felhasználhatók a modell osztályonkénti kiértékeléséhez vagy az összes osztály átlagolásának kiértékeléséhez. A különböző nézetek között úgy válthat, hogy a diagram jobb oldalon található jelmagyarázatban az osztálycímkékre kattint.
### <a name="lift-curve-for-a-good-model"></a>Görbét emelő görbe egy jó modellhez
![Görbét emelő görbe egy jó modellhez](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Görbe átemelhető rossz modellhez
![Görbe átemelhető rossz modellhez](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Görbe görbe

A görbe a modell előrejelzései megbízhatóságát ábrázolja a pozitív minták arányával szemben az egyes megbízhatósági szinten. Egy jól be van állítva modell helyesen besorolja az előrejelzések 100%-át, amelyekhez 100%-os megbízhatóságot rendel, az előrejelzések 50%-a 50%-os megbízhatóságot, az előrejelzések 20%-át 20%-os megbízhatósággal és így tovább. Egy tökéletesen hitelesített modellnek van egy görbe görbee, amely azt a vonalat követi, ahol a modell tökéletesen előrejelezni fogja annak valószínűségét, hogy a minták az egyes `y = x` osztályokhoz tartoznak.

Egy túl biztos modell túl jósolni fogja a valószínűségek nullához és egyhez közeli valószínűségét, és ritkán bizonytalan az egyes minták osztályával kapcsolatban, és a görbe görbe hasonló lesz a visszamenőleges "S"-hez. Az alacsony magabiztosságú modellek átlagosan alacsonyabb valószínűséget rendelnek az előrejelelt osztályhoz, a társított görbe pedig az "S"-hez hasonlóan fog kinézni. A görbe nem a modell helyes besorolási képességét ábrázolja, hanem azt, hogy az előrejelzései megbízhatóságát helyesen tudja hozzárendelni. Egy rossz modell akkor is jó görbét kaphat, ha a modell megfelelően alacsony megbízhatóságot és nagy bizonytalanságot rendel hozzá.

> [!NOTE]
> A görbe érzékeny a minták számára, ezért egy kisebb ellenőrzési készlet nehezen értelmezhető, zajos eredményeket állíthat elő. Ez nem feltétlenül jelenti azt, hogy a modell nincs megfelelően beállítva.

### <a name="calibration-curve-for-a-good-model"></a>A jó modellhez való görbe
![A jó modellhez való görbe](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Rossz modellhez való görbe
![Rossz modellhez való görbe](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Regressziós/előrejelzési metrikák

Az automatizált ml minden létrehozott modellhez kiszámítja ugyanazt a teljesítménymetrikát, függetlenül attól, hogy regressziós vagy előrejelzési kísérletről van-e szükség. Ezek a metrikák normalizáláson is átesnek, így lehetővé teszik a különböző tartományokkal betanított adatokon betanított modellek összehasonlítását. További információ: [metrika normalizálása.](#metric-normalization)  

Az alábbi táblázat összefoglalja a regressziós és előrejelzési kísérletekhez létrehozott modellteljesítmény-mérőszámokat. A besorolási metrikákhoz hasonlóak ezek a metrikák is a scikit Learn-implementációkon alapulnak. A megfelelő scikit learn-dokumentáció ennek megfelelően kapcsolódik a **Számítás mezőben.**

|Metric|Leírás|Számítás|
--|--|--|
explained_variance|A magyarázat szerinti variancia azt méri, hogy a modell milyen mértékben adhatja meg a célváltozó eltérését. Ez az eredeti adatok és a hibák varianciájának százalékos csökkenése. Ha a hibák átlaga 0, akkor megegyezik a meghatározási együtthatóval (lásd r2_score alábbi táblázatot). <br> <br> **Célkitűzés:** Minél közelebb van az 1-hez, annál jobb <br> **Tartomány:** (-inf, 1]|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|A átlagos abszolút hiba a cél és az előrejelzés közötti különbség abszolút értékének várt értéke.<br><br> **Célkitűzés:** Minél közelebb van a 0-hoz, annál jobb <br> **Tartomány:** [0, inf) <br><br> Típusok: <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, a mean_absolute_error el van osztva az adatok tartománya között. | [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|A átlagos abszolút százalékos eltérés (MAPE) az előrejel jelzett érték és a tényleges érték közötti átlagos különbség mértékegysége.<br><br> **Célkitűzés:** Minél közelebb van a 0-hoz, annál jobb <br> **Tartomány:** [0, inf) ||
median_absolute_error|A medián abszolút hiba a cél és az előrejelzés közötti összes abszolút különbség mediánja. Ez a veszteség robusztus a ki- és kihozott adatokkal.<br><br> **Célkitűzés:** Minél közelebb van a 0-hoz, annál jobb <br> **Tartomány:** [0, inf)<br><br>Típusok: <br> `median_absolute_error`<br> `normalized_median_absolute_error`: a median_absolute_error elosztva az adatok tartománya között. |[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|Az R<sup>2</sup> (a meghatározási együttható) az átlagos négyzetes eltérés (MSE) arányos csökkenését méri a megfigyelt adatok teljes varianciájával viszonyítva. <br> <br> **Objective (Célkitűzés):** Minél közelebb van az 1-hez, annál jobb <br> **Tartomány:** [-1, 1]<br><br>Megjegyzés: Az R<sup>2</sup> gyakran a tartományba esik (-inf, 1]. Az MSE nagyobb lehet a megfigyelt variancián, így az R<sup>2</sup> tetszőlegesen nagy negatív értékeket kaphat az adatoktól és a modell előrejelzésétől függően. Az automatizált ML-videóklipek az R<sup>2</sup> pontszámait -1-re jelentették, így az R<sup>2-hez</sup> a -1 érték valószínűleg azt jelenti, hogy a valódi R<sup>2</sup> pontszám -1-esnél kisebb. A negatív R 2 pontszám értelmezésekor vegye figyelembe a többi metrikaértéket és az adatok<sup>tulajdonságait.</sup>|[Számítás](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |Az átlagos négyzetes eltérés (RMSE) a cél és az előrejelzés közötti várható négyzetes eltérés négyzetgyöke. A nem elfogult becslevők esetében az RMSE egyenlő a szórásokkal.<br> <br> **Objective (Célkitűzés):** Minél közelebb van a 0-hoz, annál jobb <br> **Tartomány:** [0, inf)<br><br>Típusok:<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: a root_mean_squared_error elosztva az adatok tartománya között. |[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|Az átlagos négyzetes naplóhiba a várható négyzetgyöke a logaritmikus hiba négyzetgyöke.<br><br>**Objective (Célkitűzés):** Minél közelebb van a 0-hoz, annál jobb <br> **Tartomány:** [0, inf) <br> <br>Típusok: <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: root_mean_squared_log_error elosztva az adatok tartománya között.  |[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| A Spearman-korreláció a két adatkészlet közötti kapcsolat monotonitásának nemparametrikus mértéke. A Pearson-korrelációval ellentétben a Spearman-korreláció nem feltételezi, hogy mindkét adatkészlet normál módon oszlik meg. Más korrelációs együtthatókhoz hasonló, a Spearman is -1 és 1 között változik, és a 0 nem utal korrelációra. A -1 vagy az 1 korrelációi pontos monoton kapcsolatot jelentenek. <br><br> A Spearman egy rangsorrendbeli korrelációs metrika, amely azt jelenti, hogy az előrejelezhető vagy a tényleges értékek módosításai nem változtatják meg a Spearman-eredményt, ha nem változtatják meg az előrejel jelzett vagy tényleges értékek rangsorolási sorrendjét.<br> <br> **Célkitűzés:** Minél közelebb van az 1-hez, annál jobb <br> **Tartomány:** [-1, 1]|[Számítás](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Metrika normalizálása

Az automatizált gépi tanulási funkció normalizálja a regressziós és előrejelzési metrikákat, ami lehetővé teszi a különböző tartományokkal betanított adatokkal betanított modellek összehasonlítását. Egy nagyobb tartományú adatokkal betanított modellben nagyobb a hiba, mint a kisebb tartományba került adatokkal betanított modellben, hacsak nem normalizálja a hibát.

Bár nincs szabványos módszer a hibametrikák normalizálására, az automatizált gépi tanulási módszer az általános megközelítést használja a hiba az adatok tartományával való osztása érdekében: `normalized_error = error / (y_max - y_min)`

Az idősorozat-adatok előrejelzési modelljének kiértékelése során az automatizált ML további lépéseket tesz annak biztosításához, hogy a normalizálás idősorozat-azonosítónként (grain) történjen, mivel minden idősorozat valószínűleg eltérő célérték-eloszlást használ.
## <a name="residuals"></a>Reziduálisok

A reziduálisok diagramja a regressziós és előrejelzési kísérletekhez létrehozott előrejelzési hibák (reziduálisok) hisztogramja. A reziduálisokat a rendszer az összes mintára kiszámítja, majd hisztogramként jeleníti meg a modell `y_predicted - y_true` torzításának megjelenítéséhez.

Ebben a példában mindkét modell kissé elfogult, hogy a tényleges értéknél alacsonyabb előrejelzést ad. Ez nem ritka olyan adatkészletek esetében, amelyek a tényleges célok eltolt eloszlása mellett rosszabb modellteljesítményt jeleznek. Egy jó modell reziduális-eloszlása nullára csökken, és kevés a szélsőséges reziduális. Ennél rosszabb modell esetén a reziduálisok eloszlása kevesebb mintával, körülbelül nulla lesz.

### <a name="residuals-chart-for-a-good-model"></a>Egy jó modell reziduális-diagramja
![Egy jó modell reziduális-diagramja](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Rossz modell reziduális-diagramja
![Rossz modell reziduális-diagramja](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Előrejelezni és igaz

A regressziós és előrejelzési kísérlethez az előrejelzott és a valódi diagram a cél jellemző (igaz/tényleges értékek) és a modell előrejelzései közötti kapcsolatot ábrázolja. A valódi értékek az x tengely mentén vannak eltárolni, az előrejelzett középértékeket pedig hibasávok ábrázolják. Így láthatja, hogy egy modell elfogult-e bizonyos értékek előrejelzése felé. A vonal az átlagos előrejelzést jeleníti meg, az árnyékolt terület pedig a középérték körüli előrejelzések varianciáját jelzi.

A leggyakoribb valódi érték gyakran a legpontosabb előrejelzésekkel, a legkisebb eltéréssel. A trendvonal távolsága az ideális vonaltól való távolság, ahol kevés valódi érték van, jó mérőszám a modell teljesítményéről `y = x` a ki- és kijönő értékeken. A diagram alján található hisztogrammal a tényleges adateloszlásról is szólhat. Ha további adatmintákat is tartalmaz, ahol az eloszlás ritka, javíthatja a modell teljesítményét a nem ismeretlen adatok esetében.

Ebben a példában figyelje meg, hogy a jobb modell előrejelelt és igaz vonala közelebb van az ideális `y = x` vonalhoz.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>Egy jó modell előrejelált és igaz diagramja
![Egy jó modell előrejelált és igaz diagramja](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Rossz modell előrejelzése és valós diagramja
![Rossz modell előrejelzése és valós diagramja](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>A modell magyarázatai és a funkciók fontossága

Bár a modellértékelési metrikák és diagramok jól mérik egy modell általános minőségét, a felelős AI-használat során elengedhetetlen annak vizsgálata, hogy mely adatkészlet tartalmaz olyan modellt, amelyet az előrejelzésekhez használnak. Ezért biztosít az automatizált gépi tanulási modell magyarázatokat irányítópultot az adatkészlet-funkciók relatív hozzájárulásának méréséhez és jelentéséhez. Tekintse meg [a magyarázatok irányítópultját a Azure Machine Learning stúdió.](how-to-use-automated-ml-for-ml-models.md#model-explanations-preview)

A kód első lépéseként tekintse meg, hogyan állíthatja be a modell magyarázatát automatizált gépi tanulási kísérletekhez a [Azure Machine Learning Python SDK-val.](how-to-machine-learning-interpretability-automl.md)

> [!NOTE]
> Az automatizált gépi tanulási magyarázatok jelenleg nem támogatják az ForecastTCN modellt, és előfordulhat, hogy más előrejelzési modellek korlátozott hozzáféréssel rendelkezik az értelmezhetőségi eszközökhöz.

## <a name="next-steps"></a>Következő lépések
* Próbálja ki [az automatizált gépi tanulási modell magyarázatát tartalmazó mintajegyzetfüzeteket.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
* Az automatizált gépi tanulási kérdésekhez a következő e-t kell feltennie: askautomatedml@microsoft.com .
