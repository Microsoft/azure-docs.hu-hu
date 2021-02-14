---
title: 'ML Studio (klasszikus): kiértékeli & több ellenőrzési modelljét – Azure'
description: Ismerje meg a Azure Machine Learning Studio (klasszikus) modell teljesítményének figyeléséhez használható metrikákat.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: b2ca78d30659fce6e4246c81216cae94b404955e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520017"
---
# <a name="evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Modell teljesítményének kiértékelése Azure Machine Learning Studio (klasszikus)

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Ebből a cikkből megtudhatja, hogyan figyelheti a modell teljesítményének figyeléséhez használható mérőszámokat Azure Machine Learning Studio (klasszikus).  A modell teljesítményének kiértékelése az adatelemzési folyamat egyik alapvető fázisa. Azt jelzi, hogy egy adatkészlethez tartozó pontozási (előrejelzések) hogyan lett kiképezve egy betanított modellből. A Azure Machine Learning Studio (klasszikus) a modell kiértékelését két fő gépi tanulási modulon keresztül támogatja: 
+ [Modell értékelése][evaluate-model] 
+ [Modell keresztre ellenőrzése][cross-validate-model]

Ezek a modulok lehetővé teszik, hogy megtekintse, hogyan működik a modell a gépi tanulásban és a statisztikában leggyakrabban használt mérőszámok száma alapján.

A modellek értékelését az alábbiakkal együtt kell figyelembe venni:
+ [Paraméterek optimalizálása algoritmusokhoz](algorithm-parameters-optimize.md)
+ [Modell értelmezhetősége](interpret-model-results.md)

Három közös felügyelt tanulási forgatókönyv jelenik meg: 
* regressziós
* bináris besorolás 
* többosztályos besorolás


## <a name="evaluation-vs-cross-validation"></a>Értékelés és kereszt-ellenőrzés
A kiértékelés és a több ellenőrzés a modell teljesítményének mérésére szolgáló szabványos módszer. Mindkettő olyan értékelési mérőszámokat is létrehoz, amelyeket megvizsgálhat vagy összehasonlíthat más modellektől.

A [modell kiértékelése][evaluate-model] egy pontozásos adatkészletet vár bemenetként (vagy két esetben, ha két különböző modell teljesítményét szeretné összehasonlítani). Ezért az eredmények kiértékelése előtt be kell tanítania a modellt a betanítási [modell][train-model] modullal, és néhány adatkészletre vonatkozó előrejelzéseket kell készítenie a [pontszám modell][score-model] modul használatával. A kiértékelés a pontszámok által megjelenített címkéken/valószínűségeken, valamint az igaz címkéken alapul, amelyek mindegyike a [pontszám modell][score-model] modul kimenete.

Azt is megteheti, hogy több érvényesítést is használhat, hogy a bemeneti adatok különböző részhalmazán automatikusan elvégezze a betanítási pontszámok kiértékelésének műveleteit (10 hajtogatás). A bemeneti adatok 10 részre oszlanak, ahol az egyik a teszteléshez van lefoglalva, a másik 9 pedig a betanításhoz. Ez a folyamat 10 alkalommal ismétlődik, és az értékelési mérőszámok átlaga. Ez segít annak meghatározásában, hogy egy modell milyen jól általánosítható az új adatkészletekhez. A [modell][cross-validate-model] átértékelése modul egy nem betanított modellt és néhány címkézett adatkészletet is végrehajt, és az átlagos eredmények mellett kiértékeli az egyes 10 hajtogatások kiértékelésének eredményét.

A következő részekben egyszerű regressziós és besorolási modelleket hozunk létre, és kiértékeljük a teljesítményüket a [kiértékelési modell][evaluate-model] és a [modell-ellenőrzési][cross-validate-model] modulok használatával.

## <a name="evaluating-a-regression-model"></a>Regressziós modell kiértékelése
Feltételezzük, hogy az autó árát a méretek, a lóerő, a motor specs és egyéb funkciók használatával szeretnénk előre jelezni. Ez egy tipikus regressziós probléma, amelyben a célként megadott változó (*Ár*) folytonos számérték. Egy lineáris regressziós modellt is használhatunk, amely egy adott autó funkciójának értéke alapján előre jelezheti az autó árát. Ez a regressziós modell a betanított adatkészletek kiértékelésére használható. Az előre jelzett személygépkocsik díjszabásával kiértékeljük a modell teljesítményét, és megvizsgáljuk, hogy az előrejelzések mekkora mértékben térnek el a tényleges árak átlagával. Ennek szemléltetése érdekében a Machine Learning Studio (klasszikus) **tárolt adatkészletek** szakaszában elérhető *Automobile Price (nyers) adatkészletet* használjuk.

### <a name="creating-the-experiment"></a>A kísérlet létrehozása
Adja hozzá a következő modulokat a munkaterületéhez Azure Machine Learning Studio (klasszikus):

* Az autók árát (nyers)
* [Lineáris regresszió][linear-regression]
* [Modell betanítása][train-model]
* [Relevanciamodell][score-model]
* [Modell értékelése][evaluate-model]

Kapcsolja össze a portokat az 1. ábrán látható módon, és állítsa a [Train Model][train-model] modul Label (címke) oszlopát a *Price* értékre.

![Regressziós modell kiértékelése](./media/evaluate-model-performance/1.png)

1. ábra Regressziós modell kiértékelése.

### <a name="inspecting-the-evaluation-results"></a>A kiértékelés eredményeinek vizsgálata
A kísérlet futtatása után kattintson a [modell kiértékelése][evaluate-model] modul kimeneti portjára, és válassza a *Megjelenítés* elemet a kiértékelés eredményeinek megtekintéséhez. A regressziós modellekhez elérhető értékelési mérőszámok a következők: *abszolút hiba*, *gyökér középérték abszolút hiba*, *relatív abszolút hiba*, *relatív négyzetes hiba* és a *meghatározási együttható*.

A "hiba" kifejezés az előre jelzett érték és a True érték közötti különbséget jelenti. A különbség abszolút értékét vagy négyzetét általában úgy számítjuk ki, hogy az összes példányban a hibák teljes mértékét rögzítik, mivel az előre jelzett és a igaz érték közötti különbség bizonyos esetekben negatív lehet. A hiba mérőszámai egy regressziós modell prediktív teljesítményét mérik a valódi értékekkel kapcsolatos jóslatok középértéke alapján. Az alacsonyabb hibaérték azt jelenti, hogy a modell pontosabb az előrejelzések készítése során. A nulla általános hiba azt jelenti, hogy a modell tökéletesen illeszkedik az adatokhoz.

A meghatározási együttható, amely az R Squared néven is ismert, a szabványos módszer a modell megfelelő működésének mérésére is. Ezt a modellt a modell által kifejtett variációk arányában lehet értelmezni. Ebben az esetben nagyobb a jobb arány, ahol az 1 a tökéletes illeszkedést jelzi.

![Lineáris regressziós értékelési mérőszámok](./media/evaluate-model-performance/2.png)

2. ábra Lineáris regressziós értékelési mérőszámok.

### <a name="using-cross-validation"></a>Kereszt-ellenőrzés használata
Ahogy azt korábban említettük, az ismételt képzés, pontozás és értékelések automatikusan elvégezhető a [modell][cross-validate-model] átértékelése modul használatával. Ebben az esetben mindössze egy adatkészletet, egy nem betanított modellt és egy [modellen][cross-validate-model] alapuló modellt tartalmazó modult kell létrehoznia (lásd az alábbi ábrát). Be kell állítania a Label (címke) *oszlopot a* [modell kereszt-érvényesítő][cross-validate-model] moduljának tulajdonságai között.

![Regressziós modell több ellenőrzése](./media/evaluate-model-performance/3.png)

3. ábra Regressziós modell határokon átnyúló ellenőrzése.

A kísérlet futtatása után megtekintheti a kiértékelés eredményeit a [modell][cross-validate-model] átállítása modul megfelelő kimeneti portjára kattintva. Ez részletes áttekintést nyújt az egyes iterációk (fold) metrikáinak és az egyes mérőszámok átlagos eredményeiről (4. ábra).

![Regressziós modell több ellenőrzési eredményei](./media/evaluate-model-performance/4.png)

4. ábra Egy regressziós modell több ellenőrzési eredménye.

## <a name="evaluating-a-binary-classification-model"></a>Bináris besorolási modell kiértékelése
Bináris besorolási forgatókönyv esetén a célként megadott változónak csak két lehetséges eredménye van, például: {0, 1} vagy {FALSE, true}, {negatív, pozitív}. Tegyük fel, hogy a felnőtt alkalmazottak adatkészlete bizonyos demográfiai és foglalkoztatási változókkal rendelkezik, és a rendszer megkéri, hogy Jósolja meg a bevételi szintet, a {"<= 50 K", ">50 K"} értékkel rendelkező bináris változót. Ez azt jelenti, hogy a negatív osztály a 50 K/év alatti vagy azzal egyenlő alkalmazottakat jelöli, a pozitív osztály pedig az összes többi alkalmazottat jelképezi. A regressziós forgatókönyvhöz hasonlóan a modell betanítása, az egyes adatmennyiségek kiértékelése és az eredmények értékelése is megtörténik. A fő különbség itt a metrikák Azure Machine Learning Studio (klasszikus) számítások és kimenetek kiválasztása. A bevételi szint előrejelzési forgatókönyvének szemléltetéséhez a [felnőtt](https://archive.ics.uci.edu/ml/datasets/Adult) adatkészletet használjuk a Studio (klasszikus) kísérlet létrehozásához és a kétosztályos logisztikai regressziós modell teljesítményének kiértékeléséhez, amely egy gyakran használt bináris osztályozó.

### <a name="creating-the-experiment"></a>A kísérlet létrehozása
Adja hozzá a következő modulokat a munkaterületéhez Azure Machine Learning Studio (klasszikus):

* Felnőtt népszámlálás jövedelme bináris besorolási adatkészlet
* [Kétosztályos logisztikai regresszió][two-class-logistic-regression]
* [Modell betanítása][train-model]
* [Relevanciamodell][score-model]
* [Modell értékelése][evaluate-model]

Kösse össze a portokat az 5. ábrán látható módon, és állítsa a [Train Model][train-model] modul Label (címke) oszlopát a *bevétel* értékre.

![Bináris besorolási modell kiértékelése](./media/evaluate-model-performance/5.png)

5. ábra Bináris besorolási modell kiértékelése.

### <a name="inspecting-the-evaluation-results"></a>A kiértékelés eredményeinek vizsgálata
A kísérlet futtatása után kattintson a [modell kiértékelése][evaluate-model] modul kimeneti portjára, és válassza a *Megjelenítés* elemet a kiértékelés eredményeinek megtekintéséhez (7. ábra). A bináris besorolású modellekhez elérhető értékelési mérőszámok a következők: *pontosság*, *pontosság*, *visszahívás*, *F1 pontszám* és *AUC*. Emellett a modul egy zavart mátrixot jelenít meg, amely a valódi pozitív, a hamis negatív, a hamis pozitív és az igaz negatív érték, valamint a *Roc*, a *pontosság/visszahívás* és a *lift* görbék számát mutatja.

A pontosság egyszerűen a helyesen besorolt példányok aránya. Az osztályozó kiértékelése során általában az első mérőszámot kell megtekinteni. Ha azonban a teszt adatai kiegyensúlyozatlan (ahol a példányok többsége az egyik osztályhoz tartozik), vagy ha az egyik osztály teljesítményére kíváncsi, a pontosság nem igazán rögzíti az osztályozók hatékonyságát. A bevételi szint besorolási forgatókönyve feltételezi, hogy a példányok 99%-ában olyan személyeket vizsgál, akik évente kevesebb, mint 50 000-et keresnek. 0,99 pontosságot lehet elérni az összes példány "<= 50K" osztályának előrejelzésével. Az osztályozás ebben az esetben úgy tűnik, hogy összességében jó munkát végez, de a valóságban nem tudja osztályozni a magas jövedelmű személyeket (1%) helyesen.

Emiatt hasznos lehet olyan további mérőszámokat kiszámítani, amelyek a kiértékelés konkrétabb szempontjait rögzítik. Az ilyen mérőszámok részleteinek megismerése előtt fontos megérteni a bináris besorolás kiértékelésének összekeveredési mátrixát. A betanítási készletben lévő osztályok címkéi csak két lehetséges értéket vehetnek igénybe, ami általában pozitív vagy negatív. Az osztályozó becslésének megfelelő pozitív és negatív példányokat igaz pozitív (TP) és True negatives (TN) értékeknek nevezzük. Hasonlóképpen a helytelenül besorolt példányok neve hamis pozitív (FP) és hamis negatív (FN). A zűrzavaros mátrix egyszerűen egy olyan tábla, amely a négy kategóriába tartozó példányok számát jeleníti meg. A Azure Machine Learning Studio (klasszikus) automatikusan eldönti, hogy az adatkészlet két osztályának melyik a pozitív osztálya. Ha az osztályok neve logikai vagy egész szám, akkor a "true" vagy "1" címkézett példányok a pozitív osztályhoz vannak rendelve. Ha a címkék sztringek, például a bevételi adatkészlet, a feliratok betűrendbe vannak rendezve, és az első szint a negatív osztály lesz, míg a második szint a pozitív osztály.

![Bináris besorolás – zavart mátrix](./media/evaluate-model-performance/6a.png)

6. ábra Bináris besorolás – zavart mátrix.

Ha visszatér a bevétel besorolási problémára, több kiértékelési kérdést is szeretnénk megtenni, amelyek segítenek megérteni a felhasznált osztályozó teljesítményét. Természetes kérdés a következő: "a modell azon személyei számára, akik számára a modell >50 K (TP + FP) beszerzését, hány besorolása helyesen (TP)?" Ezt a kérdést a modell **pontosságának** megkeresésével választhatja ki, amely a megfelelő besorolású pozitívok aránya: TP/(TP + FP). Egy másik gyakori kérdés, hogy "az összes magas jövedelmű alkalmazottak bevétele >50k (TP + FN), hányan osztályozták helyesen a besorolást (TP)". Ez valójában a **visszahívás**, vagy a valódi pozitív arány: az osztályozó TP/(TP + FN). Észreveheti, hogy nyilvánvaló kompromisszum van a pontosság és a visszahívás között. Ha például egy viszonylag kiegyensúlyozott adatkészletet ad meg, egy olyan osztályozó, amely többnyire pozitív példányokat jósol, magas visszahívás lenne, de meglehetősen alacsony pontosságú, mivel a negatív példányok többsége helytelenül van besorolva, ami nagyszámú hamis pozitív értéket eredményez. Ha meg szeretné tekinteni a két metrika változásának feladatát, kattintson a kiértékelés eredményének kimenete oldalon látható **pontosság/visszahívás** görbére (a 7. ábrán a bal felső résznél).

![Bináris besorolás kiértékelésének eredményei](./media/evaluate-model-performance/7.png)

7. ábra A bináris besorolás kiértékelésének eredménye.

Egy másik kapcsolódó metrika, amelyet gyakran használnak az **F1 pontszám**, amely a pontosságot és a visszahívást is figyelembe veszi. Ez a két metrika harmonikus középértéke, és a következőképpen számítjuk ki: F1 = 2 (precíziós x visszahívás)/(pontosság + visszahívás). Az F1-es pontszám jó módszer arra, hogy egyetlen számon összesítse a kiértékelést, de mindig érdemes megtekinteni a pontosságot, és felidézni, hogy jobban megértse, hogyan viselkedik az osztályozó.

Emellett az is megvizsgálhatja a valódi pozitív arányt és a hamis pozitív arányt a **fogadó működési jellemző (Roc)** görbében és a **görbe (AUC) érték alatti megfelelő területen** . Minél közelebb van a görbe a bal felső sarokban, annál jobb az osztályozó teljesítménye (ami maximalizálja a valódi pozitív arányt, miközben minimalizálja a hamis pozitív arányt). A mintaterület átlója felé közeledő görbék olyan besorolásokból származnak, amelyek általában a véletlenszerű találgatáshoz közeledő előrejelzéseket készítenek.

### <a name="using-cross-validation"></a>Kereszt-ellenőrzés használata
A regressziós példához hasonlóan több ellenőrzés is elvégezhető az adatok különböző részhalmazának ismételt betanítása, pontszáma és kiértékelése során. Ehhez hasonlóan használhatjuk a [modell Átellenőrzése][cross-validate-model] modult, egy nem betanított logisztikai regressziós modellt és egy adatkészletet. A Label (címke) oszlopot a [modell][cross-validate-model] többtényezős moduljának tulajdonságainál a *bevétel* értékre kell beállítani. Miután futtatta a kísérletet, és rákattintott a [modell][cross-validate-model] átállítása modul jobb oldali kimeneti portjára, minden egyes fold esetében láthatjuk a bináris besorolási mérőszámot, az egyes értékek középértékének és szórásának megfelelően. 

![Bináris besorolási modell több ellenőrzése](./media/evaluate-model-performance/8.png)

8. ábra Bináris besorolási modell határokon átnyúló ellenőrzése.

![Bináris osztályozó több ellenőrzési eredménye](./media/evaluate-model-performance/9.png)

9. ábra. Bináris osztályozó több ellenőrzési eredménye.

## <a name="evaluating-a-multiclass-classification-model"></a>Többosztályos besorolási modell kiértékelése
Ebben a kísérletben a népszerű [írisz](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") -adatkészletet fogjuk használni, amely az írisz három különböző típusa (osztály) példányát tartalmazza. Az egyes példányok esetében négy szolgáltatási érték (a kétféle hosszúság/szélesség és a szirom hossza/szélessége) szerepel. Az előző kísérletek során az azonos adatkészleteket használó modelleket tanítjuk és teszteltük. Itt az [Adatfelosztási][split] modult használjuk az adathalmazok két részhalmazának létrehozásához, az elsőre a betanításhoz és a pontszám és a kiértékelés során. Az írisz-adatkészlet nyilvánosan elérhető az [UCI Machine learning adattárban](https://archive.ics.uci.edu/ml/index.html), és az [importálási][import-data] modul használatával tölthető le.

### <a name="creating-the-experiment"></a>A kísérlet létrehozása
Adja hozzá a következő modulokat a munkaterületéhez Azure Machine Learning Studio (klasszikus):

* [Adatok importálása][import-data]
* [Többosztályos döntési erdő][multiclass-decision-forest]
* [Adatok felosztása][split]
* [Modell betanítása][train-model]
* [Relevanciamodell][score-model]
* [Modell értékelése][evaluate-model]

A portok a 10. ábrán látható módon csatlakoztathatók.

Állítsa a [Train Model][train-model] modul felirat oszlopának indexét 5 értékre. Az adatkészlet nem tartalmaz fejlécsort, de tudjuk, hogy az osztály címkéi az ötödik oszlopban vannak.

Kattintson az [adatimportálási][import-data] modulra, és állítsa be az *adatforrás* tulajdonságot a *webes URL-címre http-n keresztül*, valamint a következő *URL-címet* : http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data .

Állítsa be, hogy a példányok milyen hányadát szeretné használni az [Adatfelosztási][split] modulban (0,7 például) a betanításhoz.

![Többosztályos osztályozó értékelése](./media/evaluate-model-performance/10.png)

10. ábra. Többosztályos osztályozó értékelése

### <a name="inspecting-the-evaluation-results"></a>A kiértékelés eredményeinek vizsgálata
Futtassa a kísérletet, és kattintson a [modell kiértékelése][evaluate-model]kimeneti portjára. A kiértékelés eredményei egy zűrzavaros mátrix formájában jelennek meg, ebben az esetben. A mátrix mindhárom osztály tényleges és előre jelzett példányait mutatja.

![A többosztályos besorolás kiértékelésének eredményei](./media/evaluate-model-performance/11.png)

11. ábra. A többosztályos besorolás kiértékelésének eredményei.

### <a name="using-cross-validation"></a>Kereszt-ellenőrzés használata
Ahogy azt korábban említettük, az ismételt képzés, pontozás és értékelések automatikusan elvégezhető a [modell][cross-validate-model] átértékelése modul használatával. Szüksége lesz egy adatkészletre, egy nem betanított modellre és egy többtényezős [modell][cross-validate-model] modulra (lásd az alábbi ábrát). Ismét be kell állítania a [modell kereszt-érvényesítő][cross-validate-model] moduljának felirat oszlopát (ebben az esetben az 5. oszlop). Miután futtatta a kísérletet, és rákattintott a [kereszt-érvényesítő modell][cross-validate-model]megfelelő kimeneti portjára, megvizsgálhatja az egyes kidobások metrikai értékeit, valamint az átlagot és a szórást is. Az itt megjelenő mérőszámok hasonlóak a bináris besorolási esetekben tárgyalt értékekhez. A többosztályos besorolásban azonban a valós pozitív/negatív és a téves pozitív/negatív értékeit a rendszer egy osztály alapján számítja ki, mivel nincs általános pozitív vagy negatív osztály. Például az "írisz-setosa" osztály pontosságának vagy visszahívásának számításakor feltételezzük, hogy ez a pozitív osztály, és az összes többi negatív.

![Többosztályos besorolási modell kölcsönös ellenőrzése](./media/evaluate-model-performance/12.png)

12. ábra. Többosztályos besorolási modell kölcsönös ellenőrzése.

![Többosztályos besorolási modell több ellenőrzési eredményei](./media/evaluate-model-performance/13.png)

13. ábra. Többosztályos besorolási modell több ellenőrzési eredménye.

<!-- Module References -->
[cross-validate-model]: /azure/machine-learning/studio-module-reference/cross-validate-model
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[multiclass-decision-forest]: /azure/machine-learning/studio-module-reference/multiclass-decision-forest
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-logistic-regression]: /azure/machine-learning/studio-module-reference/two-class-logistic-regression