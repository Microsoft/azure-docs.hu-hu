---
title: 'Modell kiértékelése: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning modell kiértékelése modult a betanított modell pontosságának méréséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/27/2020
ms.openlocfilehash: 39bdf9cb0c97e19a67b23046c6f06b60daa30147
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584940"
---
# <a name="evaluate-model-module"></a>Modell modul kiértékelése

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal mérhető a betanított modell pontossága. Olyan adatkészletet ad meg, amely egy modellből generált pontszámokat tartalmaz, és a **modell kiértékelése** modul az iparági szabványnak megfelelő értékelési metrikákat számítja ki.
  
 A **kiértékelési modell** által visszaadott mérőszámok az Ön által kiértékelt modell típusától függenek:  
  
-   **Besorolási modellek**    
-   **Regressziós modellek**  
-   **Fürtözési modellek**  


> [!TIP]
> Ha még nem ismeri a modell értékelését, javasoljuk, hogy Dr. Stephen Elston a videó sorozatot a EdX [Machine learning tanfolyamának](/archive/blogs/machinelearning/new-edx-course-data-science-machine-learning-essentials) részeként. 


## <a name="how-to-use-evaluate-model"></a>Az értékelés modell használata
1. Kapcsolja össze a pontszám [-](./assign-data-to-clusters.md) [modell](./score-model.md) vagy az eredmények adatkészlet **-kimenetét** a **kiértékelési modell** bal oldali bemeneti portjához. 
    > [!NOTE] 
    > Ha olyan modulokat használ, mint például az "Oszlopok kiválasztása az adatkészletben" lehetőséget a bemeneti adatkészlet egy részének kiválasztásához, győződjön meg róla, hogy a tényleges címke oszlop (tanításban használatos), a "pontszámmal rendelkező valószínűségek" oszlop és a "pontozott feliratok" oszlop létezik a metrikák, például a AUC, a bináris besorolás
    > A tényleges felirat oszlop, a "felhorzsolt címkék" oszlop létezik a többosztályos besorolás/regresszió metrikáinak kiszámításához.
    > "Hozzárendelések" oszlop, oszlop: "DistancesToClusterCenter". X ' (X a középpontját index, amely a 0,... számú centroids-1) létezik a fürtözés metrikáinak kiszámításához.

    > [!IMPORTANT]
    > + Az eredmények kiértékeléséhez a kimeneti adatkészletnek tartalmaznia kell egy adott pontszám-oszlop nevét, amely megfelel a modell modul követelményeinek.
    > + Az `Labels` oszlop tényleges feliratként lesz értelmezve.
    > + A regressziós feladathoz a kiértékelni kívánt adatkészletnek egy nevű oszlopból kell állnia, amely a pontozott `Regression Scored Labels` címkéket jelképezi.
    > + A bináris besorolási feladathoz a kiértékelni kívánt adatkészlet két oszlopból áll, amelyek neve a következő: `Binary Class Scored Labels` `Binary Class Scored Probabilities` .
    > + A többtényezős besorolás feladathoz a kiértékelni kívánt adatkészletnek egy nevű oszlopból kell állnia, amely a pontozott `Multi Class Scored Labels` címkéket jelképezi.
    > Ha a felsőbb rétegbeli modul kimenete nem rendelkezik ezekkel az oszlopokkal, a fenti követelményeknek megfelelően módosítania kell.

2. Választható Kapcsolja össze **a** [pontszám-modell](./score-model.md) vagy az eredmények adatkészlet-kimenetét az adatok kiosztása fürtökhöz a második modellnek **megfelelő** bemeneti portra a **modell kiértékeléséhez**. Az eredményeket egyszerűen összehasonlíthatja két különböző modellből ugyanazon adatok alapján. A két bemeneti algoritmusnak azonos algoritmus típusúnak kell lennie. Másik lehetőségként összehasonlíthatja azokat a két különböző futtatásból származó pontszámokat, amelyek különböző paraméterekkel rendelkeznek.

    > [!NOTE]
    > Az algoritmus típusa a "kétosztályos besorolás", a "többosztályos besorolás", a "regresszió", a "fürtözés" kifejezésre hivatkozik a "Machine Learning algoritmusok" alatt. 

3. Küldje el a folyamatot a kiértékelési pontszámok létrehozásához.

## <a name="results"></a>Results (Eredmények)

A **kiértékelési modell** futtatása után válassza ki a modult, és nyissa meg a **modell kiértékelése** navigációs panelt a jobb oldalon.  Ezután válassza a **kimenetek + naplók** fület, és a lapon az **adatkimenetek** szakaszban több ikon látható. A **vizualizáció ikon egy** oszlopdiagram ikont tartalmaz, és első módszer az eredmények megjelenítésére.

Ha bináris besorolást szeretne, az ikon **megjelenítése** lehetőségre kattintva megjelenítheti a bináris zavart mátrixot.
A többszörös besorolás érdekében a következőhöz hasonló **kimenetek és naplók** lapon találhatja meg a zavart mátrix nyomtatási fájlját:
> [!div class="mx-imgBorder"]
> ![Feltöltött rendszerkép előnézete](media/module/multi-class-confusion-matrix.png)

Ha adatkészleteket hoz létre a **kiértékelési modell** mindkét bemenetéhez, akkor az eredmények mindkét adathalmazra, vagy mindkét modellre vonatkozó metrikákat tartalmaznak.
A bal oldali porthoz csatolt modellnek vagy adatoknak először a jelentésben kell megjelenni, amelyet a DataSet adatkészlethez tartozó metrikák, illetve a jobb oldali porthoz csatolt modell mutat.  

Az alábbi ábrán például az azonos adatokra épülő két fürtözött modell eredményeinek összehasonlítása, de különböző paraméterekkel.  

![Comparing2Models](media/module/evaluate-2-models.png)  

Mivel ez egy fürtözési modell, a kiértékelési eredmények eltérnek, mint ha két regressziós modellből származó pontszámokat hasonlítanak össze, vagy két besorolási modellel hasonlították össze. A teljes bemutató azonban ugyanaz. 

## <a name="metrics"></a>Mérőszámok

Ez a szakasz a **kiértékelési modellel** használható, adott típusú modellek által visszaadott mérőszámokat ismerteti:

+ [besorolási modellek](#metrics-for-classification-models)
+ [regressziós modellek](#metrics-for-regression-models)
+ [fürtözési modellek](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Besorolási modellek metrikái


A következő metrikákat kell jelenteni a bináris besorolási modellek kiértékelése során.
  
-   A **pontosság** a besorolási modell jóságát méri az igaz eredményeknek az összes esethez viszonyított arányában.  
  
-   A **pontosság** a valódi eredmények aránya az összes pozitív eredménynél. Precíziós = TP/(TP + FP)  
  
-   A **visszahívás** a ténylegesen beolvasott érintett példányok teljes mennyisége. Visszahívás = TP/(TP + FN)  
  
-   Az F1-es **pontszám** kiszámítása a pontosság súlyozott átlaga, a 0 és az 1 közötti visszahívás pedig az ideális F1-es pontszám értéke 1.  
  
-   A **AUC** méri a görbe alatti terület kirajzolását az y tengelyen található igaz pozitív értékkel, az x tengelyen pedig téves pozitív értéket. Ez a metrika azért hasznos, mert egyetlen számot biztosít, amely lehetővé teszi különböző típusú modellek összehasonlítását. A AUC besorolás-küszöbérték – Invariant. A modell előrejelzésének minőségét méri, függetlenül attól, hogy milyen besorolási küszöbérték van kiválasztva.


### <a name="metrics-for-regression-models"></a>Regressziós modellek metrikái
 
A regressziós modellekhez visszaadott metrikák a hibák mennyiségének becslésére lettek kialakítva.  A modell akkor tekinthető megfelelőnek, ha a megfigyelt és az előre jelzett értékek közötti különbség kicsi. A maradékok mintázatának (az egy előre jelzett pont és a hozzá tartozó tényleges érték közötti különbség) megvizsgálása azonban sokat jelenthet a modell lehetséges torzításával kapcsolatban.  
  
 A regressziós modellek kiértékeléséhez a következő metrikákat kell jelenteni.
  
- Az **átlagos abszolút hiba (Mae)** méri, hogy az előrejelzések hogyan zárulnak le a tényleges eredményekhez képest. így jobb az alacsonyabb pontszám.  
  
- A **legfelső szintű középre állított hiba (gyökátlagos)** egyetlen értéket hoz létre, amely összegzi a modellben található hibát. A különbség négyszögesítése a metrika figyelmen kívül hagyja a túlzott előrejelzés és az előrejelzés közötti különbséget.  
  
- **Relatív abszolút hiba (Rae)** a várt és a tényleges értékek közötti relatív abszolút különbség; relatív, mert az átlagos különbség a számtani középérték alapján oszlik meg.  
  
- A **relatív négyzetes hiba (RSE)** hasonló módon normalizálja az előre jelzett értékek teljes négyzetes hibáját azáltal, hogy a tényleges értékek teljes négyzetes hibáját választja.  
  

  
- A **meghatározási együttható**(más néven R <sup>2</sup>) a modell prediktív erejét mutatja 0 és 1 közötti értékként. Nulla érték azt jelenti, hogy a modell véletlenszerű (semmit sem jelent); 1 a tökéletes illeszkedést jelenti. Azonban körültekintően kell használni az R<sup>2</sup> értékek értelmezését, mivel az alacsony értékek teljesen normálisak lehetnek, és a magas értékek gyanúja is lehet.

###  <a name="metrics-for-clustering-models"></a>A fürtözési modellek metrikái

Mivel a fürtözési modellek nagy mértékben különböznek a besorolási és a regressziós modelltől, a [modell kiértékelése](evaluate-model.md) a különböző statisztikai adatokat is visszaadja a fürtözési modellekhez.  
  
 A fürtszolgáltatási modell által visszaadott statisztikák azt írják le, hogy hány adatpontot rendeltek hozzá az egyes fürtökhöz, a fürtök közötti elkülönítés mennyiségét, valamint azt, hogy az adatpontok milyen szorosan legyenek az egyes fürtökön belül.  
  
 A fürtözési modell statisztikái a teljes adatkészlet átlagát képezik, és a fürt statisztikáit tartalmazó további sorokkal.  
  
A következő metrikákat kell jelenteni a fürtszolgáltatási modellek kiértékeléséhez.
    
-   Az oszlopban lévő pontszámok, az **átlagos távolság a másik központtól**, a fürt minden egyes pontja esetében az összes többi fürt centroids.   

-   Az oszlopban lévő pontszámok, amelyek a **fürt középpontjának átlagos távolsága**, a fürt összes pontjának a fürt középpontját való közelségét jelölik.  
  
-   A **pontok száma** oszlopban látható, hogy hány adatpontot rendeltek hozzá az egyes fürtökhöz, valamint a fürtben lévő adatpontok összesített számát.  
  
     Ha a fürtökhöz rendelt adatpontok száma kevesebb, mint az elérhető adatpontok teljes száma, az azt jelenti, hogy az adatpontokat nem lehet fürthöz rendelni.  
  
-   Az oszlopban lévő pontszámok, a **cluster Center értékének maximális távolsága**, az egyes pontok közötti távolságok maximális száma, valamint az adott pont fürt középpontját.  
  
     Ha ez a szám magas, akkor azt jelentheti, hogy a fürt széles körben elszórtan van. Tekintse át ezt a statisztikát, valamint az **átlagos távolságot a fürt közepétől** a fürt eloszlásának meghatározásához.   

-   Az eredmények minden szakaszának alján az **összesített értékelési** pontszám felsorolja az adott modellben létrehozott fürtök átlagos pontszámait.  
  

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .