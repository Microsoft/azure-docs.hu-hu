---
title: 'Kereszt-ellenőrzési modell: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning Designerben használja a modell átállítása modult a besorolási vagy regressziós modellekhez tartozó paraméterek becslésének megadásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: d4099ecf6e6bcc6654391e54292878393fb22914
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421345"
---
# <a name="cross-validate-model"></a>Modell keresztellenőrzése

Ez a cikk azt ismerteti, hogyan használható a Azure Machine Learning Designerben az Cross validate Model modul. A többértékű *ellenőrzés* a gépi tanulásban gyakran használatos, és az adatkészletek változékonyságát, valamint az ezen adatokat használó modellek megbízhatóságát is felméri.  

A modell keresztbe állítása modul a címkével ellátott adatkészlet bemenetét, valamint egy képzetlen besorolást vagy regressziós modellt használ. Az adatkészletet bizonyos számú részhalmazra osztja (*hajtogatás*), minden egyes foldon létrehoz egy modellt, majd a pontossági statisztikák készletét adja vissza az egyes hajtogatásoknak. Az összes hajtogatás pontossági statisztikájának összehasonlításával értelmezheti az adathalmaz minőségét. Ezután megtudhatja, hogy a modell érzékeny-e az adatváltozásokra.  

A Cross Validation Model az előre jelzett eredményeket és az adatkészlethez tartozó valószínűségeket is visszaadja, így az előrejelzések megbízhatóságát is kiértékelheti.  

### <a name="how-cross-validation-works"></a>A kereszt-ellenőrzés működése

1. A többszörös érvényesítés véletlenszerűen felosztja a betanítási adatgyűjtést. 

   Az algoritmus alapértelmezett értéke 10, ha korábban még nem particionálta az adatkészletet. Ha az adatkészletet más számú bedobásra szeretné osztani, használhatja a [partíció és a minta](partition-and-sample.md) modult, és jelezheti, hogy hány hajtogatást használ.  

2.  A modul az 1. hajtogatott adategységet az ellenőrzéshez használja. (Ezt néha a *Holdout Foldnak* is nevezik.) A modul a fennmaradó ráncokat használja a modell betanításához. 

    Ha például öt betöltést hoz létre, a modul öt modellt hoz létre a kereszt-ellenőrzés során. A modul az egyes modelleket az adatmennyiségek négy egyötödét használva hajtja majd be. Teszteli az egyes modelleket a fennmaradó egy ötödik oldalon.  

3.  Az egyes hajtogatási modell tesztelése során a modul több pontossági statisztikát értékel ki. A modul által használt statisztikák a modell típusától függenek. A rendszer különböző statisztikákat használ a besorolási modellek és a regressziós modellek kiértékelésére.  

4.  Ha az épület és a kiértékelés folyamata befejeződött az összes betöltésnél, a kereszt-ellenőrzési modell teljesítmény-mérőszámokat generál, és az összes adathoz tartozó eredményeket jeleníti meg. Tekintse át ezeket a mérőszámokat, és ellenőrizze, hogy van-e magas vagy alacsony pontosságú egyetlen fold. 

### <a name="advantages-of-cross-validation"></a>A több ellenőrzés előnyei

A modellek kiértékelésének egy másik és gyakori módja az, hogy az adatmennyiséget a kiképzésre és a tesztelésre a [felosztott adat](split-data.md)használatával csoportosítsa, majd érvényesítse a modellt a betanítási adaton. A Cross-Validation azonban néhány előnyt nyújt:  

-   Az ellenőrzés több tesztet használ.

    A többértékű ellenőrzés a modell teljesítményét a megadott paraméterekkel méri nagyobb adatterületen. Ez azt is megtörténik, hogy a teljes betanítási adatkészletet a képzéshez és az értékeléshez használja, egy rész helyett. Ezzel szemben, ha a modellt egy véletlenszerű felosztásból generált adatok használatával érvényesíti, általában a modell kiértékelése csak a rendelkezésre álló adatok 30 vagy kevesebb százalékára történik.  

    Mivel azonban a kereszt-ellenőrzési vonatok és a modellt többször is érvényesítik egy nagyobb adatkészleten keresztül, sokkal több számítási feladatuk van. Sokkal hosszabb időt vesz igénybe, mint az ellenőrzés véletlenszerű felosztáskor.  

-   A többszörös ellenőrzés kiértékeli az adatkészletet és a modellt is.

    Az átellenőrzés nem csupán egy modell pontosságát méri. Emellett azt is megtudhatja, hogy az adatkészlet mennyire reprezentatív, és hogy mennyire érzékeny a modell az adatok változásaira.  

## <a name="how-to-use-cross-validate-model"></a>A Cross validate Model használata

A több érvényesítés hosszú időt is igénybe vehet, ha az adatkészlet nagy méretű.  Így a modell kiépítésének és tesztelésének kezdeti fázisában is használhat kereszt-ellenőrzési modellt. Ebben a fázisban kiértékelheti a modell paramétereinek jóságát (feltéve, hogy a számítási idő tűrhető). Ezután betaníthatja és kiértékelheti a modelljét a [betanítási modellel](train-model.md) létrehozott paraméterekkel, és [kiértékelheti a modell](evaluate-model.md) modulokat.

Ebben a forgatókönyvben a modellt többek között a modell betanításával és tesztelésével is elvégezheti.

1. Adja hozzá a Cross validate Model modult a folyamathoz. Megtalálhatja Azure Machine Learning Designerben, a **modell pontozási & próbaverzió** kategóriában. 

2. Bármely besorolási vagy regressziós modell kimenetének összekötése. 

    Ha például **két osztályt használó döntési fát** használ a besoroláshoz, konfigurálja a modellt a kívánt paraméterekkel. Ezután húzzon egy összekötőt az osztályozatlan **modell** portjáról a kereszt-ellenőrzési modell megfelelő portjára. 

    > [!TIP] 
    > Nem kell betanítania a modellt, mert a kereszt-ellenőrzési modell automatikusan betanítja a modellt a kiértékelés részeként.  
3.  A kereszt-ellenőrzési modell **adatkészletének** portjához csatlakoztasson bármely címkézett betanítási adatkészletet.  

4.  A kereszt-ellenőrzési modell jobb oldali paneljén kattintson az **oszlop szerkesztése** elemre. Válassza ki azt az egyetlen oszlopot, amely tartalmazza a osztály címkéjét vagy a kiszámítható értéket. 

5. Állítsa be a **véletlenszerű mag** paraméter értékét, ha meg szeretné ismételni a több ellenőrzés eredményét egymást követő futtatásokban ugyanazon az adategységen.  

6. A folyamat elküldése.

7. A jelentések leírását a [Results (eredmények](#results) ) című szakaszban találja.

## <a name="results"></a>Results (Eredmények)

Az összes iteráció befejezése után a Cross validate Model a teljes adatkészlet pontszámait hozza létre. Emellett teljesítménymutatókat is létrehoz, amelyeket a modell minőségének felméréséhez használhat.

### <a name="scored-results"></a>Felhorzsolt eredmények

A modul első kimenete tartalmazza az egyes sorokhoz tartozó forrásadatokat, valamint az előre jelzett értékeket és a kapcsolódó valószínűségeket. 

Az eredmények megtekintéséhez a folyamaton kattintson a jobb gombbal a modell keresztre ellenőrzése modulra. Válassza ki a **pontozásos eredmények megjelenítése** elemet.

| Új oszlop neve      | Description                              |
| -------------------- | ---------------------------------------- |
| Pontozott címkék        | Ezt az oszlopot az adatkészlet végén adja hozzá a rendszer. Az egyes sorok előre jelzett értékét tartalmazza. |
| Pontozásos valószínűségek | Ezt az oszlopot az adatkészlet végén adja hozzá a rendszer. Azt jelzi, hogy az érték becsült valószínűsége a **pontozásos címkékben**. |
| Fold száma          | A fold nulla alapú indexét jelzi, amelyet az egyes adatsorokhoz rendeltek a rendszer a kereszt-ellenőrzés során. |

 ### <a name="evaluation-results"></a>A kiértékelés eredménye

A második jelentés hajtogatás szerint van csoportosítva. Ne feledje, hogy a végrehajtás során a Cross validate Model véletlenszerűen felosztja a betanítási adatait *n* -ba (alapértelmezés szerint 10). Az adatkészletben lévő minden egyes iteráció során a Cross validate Model egy foldot használ érvényesítési adatkészletként. A fennmaradó *n-1* hajtogatást használja a modell betanításához. Az *n* -modellek mindegyike az összes többi karámban található adatra van tesztelve.

Ebben a jelentésben a kidobások index érték szerint, növekvő sorrendben vannak felsorolva.  Bármely más oszlop megrendeléséhez mentheti az eredményeket adatkészletként.

Az eredmények megtekintéséhez a folyamaton kattintson a jobb gombbal a modell keresztre ellenőrzése modulra. Válassza ki **a kiértékelési eredmények megjelenítése kidobással** lehetőséget.


|Oszlop neve| Leírás|
|----|----|
|Fold száma| Az egyes összecsukható azonosítók. Ha öt hajtogatást hozott létre, a 0 és 4 közötti számú adat öt részhalmaza lesz.
|A kiszorzott példák száma|Az egyes hajtogatáshoz rendelt sorok száma Nagyjából egyenlőnek kell lenniük. |


A modul a következő metrikákat is tartalmazza a kiértékeléshez használt modell típusától függően: 

+ **Besorolási modellek**: precizitás, visszahívás, F-pontszám, AUC, pontosság  

+ **Regressziós modellek**: abszolút hiba, legfelső szintű négyzetes hiba, relatív abszolút hiba, relatív négyzetes hiba és a meghatározási együttható


## <a name="technical-notes"></a>Technikai megjegyzések  

+ Az adatkészletek normalizálása az ajánlott eljárás, mielőtt a rendszer áttekintse azokat. 

+ A Cross validate Model sokkal több számítási igényű, és hosszabb időt vesz igénybe, mint ha a modellt egy véletlenszerűen osztott adatkészlet alapján érvényesítette. Ennek az az oka, hogy a modell többek között ellenőrzi a vonatokat, és többször is ellenőrzi a modellt.

+ Az adatkészletet nem kell kiosztani képzésre és tesztelési csoportokra, ha a modell pontosságának méréséhez kereszt-érvényesítést használ. 


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

