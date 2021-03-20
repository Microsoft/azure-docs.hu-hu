---
title: 'ML Studio (klasszikus): a modell eredményeinek értelmezése – Azure'
description: Válassza ki az algoritmushoz beállított optimális paramétert a pontszám-modell kimenetének használatával és megjelenítésével.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 11/29/2017
ms.openlocfilehash: f824cf6e30ed795ef0fe4468542ae16c801bd61b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516124"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>A modell eredményeinek értelmezése Azure Machine Learning Studio (klasszikus)

**a következőkre vonatkozik:** ![ Ez egy pipa, ami azt jelenti, hogy ez a cikk a Machine Learning Studio (klasszikus) elemre vonatkozik. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasszikus) ![ Ez egy X, ami azt jelenti, hogy ez a cikk Azure Machine learningre vonatkozik.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Ez a témakör bemutatja, hogyan jelenítheti meg és értelmezheti az előrejelzési eredményeket Azure Machine Learning Studio (klasszikus). Miután betanított egy modellt, és elvégezte az előrejelzések megtételét ("a modell értékelése"), meg kell ismernie és el kell értelmezni az előrejelzés eredményét.

A gépi tanulási modellek négy fő típusa Azure Machine Learning Studio (klasszikus):

* Osztályozás
* Fürtözés
* Regresszió
* Ajánló rendszerek

Az ezen modellek alapján történő előrejelzéshez használt modulok a következők:

* [Pontszám modell][score-model] modul a besoroláshoz és a regresszióhoz
* [Hozzárendelés fürtökhöz][assign-to-clusters] modul a fürtözéshez
* [Matchbox-ajánló][score-matchbox-recommender] a javaslati rendszerekhez

Megtudhatja, hogyan [választhat paramétereket az algoritmusok optimalizálásához ml Studio (klasszikus)](algorithm-parameters-optimize.md).

A modellek kiértékeléséről a [modell teljesítményének kiértékelését](evaluate-model-performance.md)ismertető témakörben olvashat bővebben.

Ha új ML Studio (klasszikus), [Ismerje meg, hogyan hozhat létre egy egyszerű kísérletet](create-experiment.md).

## <a name="classification"></a>Osztályozás
A besorolási problémák két alkategóriája van:

* Problémák csak két osztállyal (kétosztályos vagy bináris besorolású)
* Problémák több mint két osztállyal (többosztályos besorolás)

A Azure Machine Learning Studio (klasszikus) különböző modulokat tartalmaz az ilyen típusú besorolások kezeléséhez, de az előrejelzési eredmények értelmezésének módszerei hasonlóak.

### <a name="two-class-classification"></a>Kétosztályos besorolás
**Példakísérlet**

A kétosztályos besorolási probléma példája az írisz virágok besorolása. A feladat az írisz-virágok osztályozása a funkcióik alapján. A Azure Machine Learning Studio (klasszikus) által megadott Iris-adathalmaz a népszerű [Iris-adathalmaz](https://en.wikipedia.org/wiki/Iris_flower_data_set) egy részhalmaza, amely csak két virágos fajt tartalmaz (0 és 1. osztály). Az egyes virágoknak négy funkciója van (a SEPA hossza, a SEPA szélessége, a szirom hossza és a szirom szélessége).

![Az írisz-kísérlet képernyőképe](./media/interpret-model-results/1.png)

1. ábra Iris kétosztályos besorolási problémás kísérlet

Kísérlet történt a probléma megoldására, ahogy az 1. ábrán is látható. Egy kétosztályos, megnövelt döntési famodell lett kitanítva és felhorzsolve. Most már megjelenítheti a [pontszám modell][score-model] modul előrejelzési eredményeit a [pontszám modell][score-model] modul kimeneti portjára kattintva, majd a **vizualizáció elemre kattintva.**

![Pontszám modell modul](./media/interpret-model-results/1_1.png)

Ez a pontozási eredményeket a 2. ábrán látható módon mutatja be.

![Iris kétosztályos besorolási kísérletének eredményei](./media/interpret-model-results/2.png)

2. ábra Pontszám modelljének megjelenítése kétosztályos besorolással

**Eredmény értelmezése**

Az eredmények táblában hat oszlop található. A bal oldali négy oszlop a négy funkció. A jobb oldali két oszlop, a pontozott címkék és a mutatós valószínűségek a jóslat eredményei. A pontszám valószínűsége oszlop azt a valószínűséget mutatja, hogy a virág a pozitív osztályhoz tartozik (1. osztály). Például az oszlop első száma (0,028571) azt jelenti, hogy 0,028571 a valószínűsége annak, hogy az első virág az 1. osztályhoz tartozik. A pontozott feliratok oszlop az egyes virágokhoz tartozó előre jelzett osztályt jeleníti meg. Ez a pontozásos valószínűségek oszlopán alapul. Ha a virág pontozásos valószínűsége nagyobb, mint 0,5, akkor az 1. osztályba kerül. Ellenkező esetben a 0. osztályba kerül.

**Webszolgáltatás kiadványa**

Miután megértette az előrejelzési eredményeket, és megítélte a hangot, a kísérletet közzéteheti webszolgáltatásként, hogy különböző alkalmazásokban is üzembe helyezhető, és meghívhatja, hogy minden új írisz-virághoz beszerezze az osztályra vonatkozó előrejelzéseket. Ha szeretné megtudni, hogyan válthat ki egy képzési kísérletet pontozási kísérletre, és hogyan tehet közzé webszolgáltatásként, tekintse meg a [3. Oktatóanyag: hitelkockázat-modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md)című témakört. Ez az eljárás egy pontozási kísérletet biztosít a 3. ábrán látható módon.

![A pontozási kísérlet képernyőképe](./media/interpret-model-results/3.png)

3. ábra Az írisz kétosztályos besorolási problémás kísérletének pontozása

Most be kell állítania a webszolgáltatás bemenetét és kimenetét. A bemenet a [pontszám modell][score-model]megfelelő bemeneti portja, amely az írisz virág funkcióinak bemenete. A kimenet kiválasztása attól függ, hogy érdekli-e az előre jelzett osztály (a mutató címkéje), a kiszámított valószínűség vagy mindkettő. Ebben a példában feltételezzük, hogy mindkét esetben érdekli Önt. A kívánt kimeneti oszlopok kiválasztásához használjon egy [Select oszlopot az adatkészlet][select-columns] modulban. Kattintson [az Oszlopok kiválasztása az adatkészletben][select-columns]lehetőségre, kattintson az **oszlop kiválasztásának indítása** elemre, és válassza a pontozott **címkék** és a **mutatós valószínűségek** lehetőséget. Miután beállította az [oszlopok kiválasztását az adatkészletben][select-columns] , és újra futtatja, készen kell állnia a pontozási kísérlet webszolgáltatásként való közzétételére a **webszolgáltatás közzététele** lehetőségre kattintva. A végső kísérlet a 4. ábrára hasonlít.

![Az írisz kétosztályos besorolási kísérlet](./media/interpret-model-results/4.png)

4. ábra Egy Iris kétosztályos besorolási problémájának végső pontozási kísérlete

Miután futtatta a webszolgáltatást, és megadhat egy tesztelési példány egyes szolgáltatási értékeit, az eredmény két számot ad vissza. Az első szám a pontozásos címke, a második pedig a pontozásos valószínűség. Ez a virág az 1. osztályba kerül, 0,9655-as valószínűséggel.

![Teszt értelmezése pontszám modell](./media/interpret-model-results/4_1.png)

![Pontozási tesztek eredményei](./media/interpret-model-results/5.png)

5. ábra Az írisz kétosztályos besorolásának webszolgáltatási eredménye

### <a name="multi-class-classification"></a>Többosztályos besorolás
**Példakísérlet**

Ebben a kísérletben egy levél-felismerési feladatot hajt végre, példaként a többosztályos besorolásra. Az osztályozó kísérletet tesz a (z)% 28class %29 adott levél előrejelzésére a kézzel írt képekből kinyert, kézzel írt attribútumok alapján.

![Példa a levél felismerésére](./media/interpret-model-results/5_1.png)

A betanítási adatokban 16 funkció van kinyerve a kézzel írt levelekből származó képekből. A 26 karakter a 26 osztályt képezi. A 6. ábra egy olyan kísérletet mutat be, amely egy többosztályos besorolási modellt fog betanítani a levelek felismeréséhez, és előre jelezni fogja a tesztelési adatkészleten beállított funkciót.

![Betűk felismerése többosztályos besorolási kísérlet](./media/interpret-model-results/6.png)

6. ábra A betűk felismerése többosztályos besorolással kapcsolatos probléma

A [pontszám modell][score-model] modul eredményeinek megjelenítéséhez kattintson a [pontszám modell][score-model] modul kimeneti portjára, majd a vizualizáció elemre **, a** 7. ábrán látható módon kell látnia a tartalmat.

![Pontszám modell eredményei](./media/interpret-model-results/7.png)

7. ábra A pontszám modelljének megjelenítése többosztályos besorolást eredményez

**Eredmény értelmezése**

A bal oldali 16 oszlop a tesztkörnyezet szolgáltatási értékeit jelöli. A "XX" osztályhoz hasonló, pontozott valószínűséggel rendelkező oszlopok ugyanúgy hasonlítanak a kétosztályos esetekhez tartozó pontszámok oszlopához. Annak a valószínűségét mutatják, hogy a megfelelő bejegyzés egy adott osztályba tartozik. Az első bejegyzés esetében például 0,003571 az a valószínűsége, hogy az "A", a 0,000451 valószínűsége pedig "B", és így tovább. Az utolsó oszlop (pontozott feliratok) ugyanaz, mint a kétosztályos esethez tartozó pontozásos címkék. Kiválasztja az osztályt a legnagyobb pontszámú valószínűséggel, a megfelelő bejegyzés előrejelzett osztályával. Az első bejegyzés esetében például az "F" felirat látható, mivel a legnagyobb valószínűséggel "F" (0,916995).

**Webszolgáltatás kiadványa**

Az egyes bejegyzésekhez tartozó mutatós címkét és a pontozásos címke valószínűségét is lekérheti. Az alapszintű logika az összes mutatós valószínűség közül a legnagyobb valószínűséget keresi. Ehhez az [R szkript végrehajtása][execute-r-script] modult kell használnia. Az R-kód a 8. ábrán látható, a kísérlet eredménye pedig a 9. ábrán látható.

![Példa R-kódra](./media/interpret-model-results/8.png)

8. ábra R-kód a pontozott feliratok kinyeréséhez és a címkék kapcsolódó valószínűségéhez

![Kísérlet eredménye](./media/interpret-model-results/9.png)

9. ábra. A levél-felismerés többosztályos besorolási problémájának végső pontozási kísérlete

Miután közzétette és futtatta a webszolgáltatást, és megadta a bemeneti szolgáltatások egyes értékeit, a visszaadott eredmény a 10. ábrára hasonlít. A kinyert 16 funkció alapján ez a kézzel írt levél a 0,9715 valószínűséggel rendelkező "T".

![Teszt értelmezése – pontszám modul](./media/interpret-model-results/9_1.png)

![Teszt eredménye](./media/interpret-model-results/10.png)

10. ábra. A többosztályos besorolás webszolgáltatás-eredménye

## <a name="regression"></a>Regresszió
A regressziós problémák eltérnek a besorolási problémáktól. Besorolási probléma esetén olyan diszkrét osztályokat próbál meg megjósolni, mint például az, hogy az írisz virág melyik osztályhoz tartozik. De ahogy az alábbi példában is látható egy regressziós problémára, egy folytonos változót próbál meg előre jelezni, például egy autó árát.

**Példakísérlet**

A regressziós példaként használja az autó árának előrejelzését. Egy autó árát próbálja megjósolni a funkciói, például a gyártmány, az üzemanyag típusa, a törzs típusa és a meghajtó kereke alapján. A kísérlet a 11. ábrán látható.

![Autó árának regressziós kísérlete](./media/interpret-model-results/11.png)

11. ábra. Az autó árának regressziós problémáinak kísérlete

A pontszám- [modell][score-model] modul megjelenítése, az eredmény a 12. ábrán látható.

![Az autó árának előrejelzési problémájának pontozási eredményei](./media/interpret-model-results/12.png)

12. ábra. Az autó árának előrejelzési problémájának pontozási eredménye

**Eredmény értelmezése**

A pontozásos címkék az eredmény oszlopban jelennek meg. A számok az egyes autók előre jelzett árai.

**Webszolgáltatás kiadványa**

A regressziós kísérletet közzéteheti egy webszolgáltatásban, és megtekintheti az autó árának előrejelzéséhez hasonló módon, ugyanúgy, mint a kétosztályos besorolási használati esetnél.

![Pontozási kísérlet az autó árának regressziós problémájára](./media/interpret-model-results/13.png)

13. ábra. Pontozási kísérlet egy autó árának regressziós problémájára

A webszolgáltatás futtatásakor a visszaadott eredmény a 14. ábrára hasonlít. Az autóhoz tartozó előre jelzett díj $15 085,52.

![Tesztelési pontozási modul tesztelése](./media/interpret-model-results/13_1.png)

![Pontozási modul eredményei](./media/interpret-model-results/14.png)

14. ábra. Webszolgáltatás-ár regressziós problémájának webszolgáltatási eredménye

## <a name="clustering"></a>Fürtözés
**Példakísérlet**

Hozzon létre egy fürtszolgáltatási kísérletet a szivárványhártya-adathalmaz használatával. Itt kiszűrheti az adatkészletben szereplő címkéket, így csak funkciókkal rendelkezhet, és fürtözéshez is használható. Ebben az írisz-használati esetben a betanítási folyamat során a két fürt számát kell megadnia, ami azt jelenti, hogy a virágok két osztályba csoportosulnak. A kísérlet a 15. ábrán látható.

![Írisz-fürtszolgáltatási probléma kísérlete](./media/interpret-model-results/15.png)

15. ábra. Írisz-fürtszolgáltatási probléma kísérlete

A fürtözés eltér a besorolástól, hogy a betanítási adatkészletben ne legyenek saját maguk az alapigazságok feliratai. Csoportosítja a betanítási adatkészlet példányait különálló fürtökre. A betanítási folyamat során a modell felcímkézi a bejegyzéseket úgy, hogy megtanítja a funkcióik közötti különbségeket. Ezt követően a betanított modell a jövőbeli bejegyzések további besorolására is használható. Az eredmény két részből áll, ezért egy fürtözési problémán belül érdeklik. Az első rész a betanítási adatkészletet jelöli, a második pedig egy új adathalmazt osztályoz a betanított modellel.

Az eredmény első részének **megjelenítéséhez** kattintson a [vonat fürtözési modell][train-clustering-model] bal oldali kimeneti portjára, majd kattintson a vizualizáció lehetőségre. A vizualizáció a 16. ábrán látható.

![Fürtözés eredménye](./media/interpret-model-results/16.png)

16. ábra. A betanítási adathalmaz fürtözési eredményének megjelenítése

A második rész eredménye, amely az új bejegyzéseket a betanított fürtözési modellel csoportosítja, a 17. ábrán látható.

![Fürtszolgáltatás eredményének megjelenítése](./media/interpret-model-results/17.png)

17. ábra. Fürtszolgáltatás eredményének megjelenítése egy új adatkészleten

**Eredmény értelmezése**

Bár a két rész eredményei különböző kísérletezési fázisokból származnak, ugyanúgy néznek ki, és ugyanúgy vannak értelmezve. Az első négy oszlop a funkciók. Az utolsó oszlop, a hozzárendelések, az előrejelzés eredménye. Az ugyanahhoz a számhoz rendelt bejegyzések ugyanabban a fürtben vannak, azaz a hasonlóságok megosztása valamilyen módon (ez a kísérlet az alapértelmezett euklideszi távolsági metrikát használja). Mivel a 2. számú szektorcsoportot adta meg, a hozzárendelések bejegyzései 0 vagy 1 címkével vannak ellátva.

**Webszolgáltatás kiadványa**

Közzéteheti a fürtszolgáltatási kísérletet egy webszolgáltatásban, és meghívja azt a fürtözési előrejelzésekhez, ugyanúgy, mint a kétosztályos besorolási használati esetnél.

![Az írisz-fürtözési probléma pontozási kísérlete](./media/interpret-model-results/18.png)

18. ábra. Írisz-fürtszolgáltatási probléma pontozási kísérlete

A webszolgáltatás futtatása után a visszaadott eredmény a 19. ábrára hasonlít. A virág várhatóan a 0. fürtbe kerül.

![Az értelmező pontozási modul tesztelése](./media/interpret-model-results/18_1.png)

![Pontozási modul eredménye](./media/interpret-model-results/19.png)

19. ábra. Az írisz kétosztályos besorolásának webszolgáltatási eredménye

## <a name="recommender-system"></a>Ajánlott rendszerek
**Példakísérlet**

Az ajánló rendszerek esetében példaként használhatja az éttermi javaslattal kapcsolatos problémát: a minősítési előzmények alapján javaslatokat tehet az ügyfeleknek. A bemeneti adatok három részből állnak:

* Éttermek minősítése az ügyfelektől
* Az ügyfél funkcióinak adatszolgáltatása
* Az éttermi funkciókról

A Azure Machine Learning Studio (klasszikus) [Matchbox ajánló][train-matchbox-recommender] modullal több dolog is elvégezhető:

* Egy adott felhasználó és tétel minősítésének előrejelzése
* Elemek ajánlása egy adott felhasználónak
* Egy adott felhasználóhoz kapcsolódó felhasználók keresése
* Egy adott elemhez kapcsolódó elemek keresése

Kiválaszthatja, hogy mit szeretne tenni, ha kiválasztja az **Ajánlói** betekintő típusa menü négy lehetőségét. Itt mind a négy forgatókönyvet áttekintheti.

![Matchbox ajánló](./media/interpret-model-results/19_1.png)

Az ajánló rendszer tipikus Azure Machine Learning Studio (klasszikus) kísérlete a 20. ábrára hasonlít. További információ az ajánlott rendszermodulok használatáról: a Matchbox- [ajánló][train-matchbox-recommender] és a [score Matchbox ajánló][score-matchbox-recommender].

![Ajánlói rendszerkísérlet](./media/interpret-model-results/20.png)

20. ábra. Ajánlói rendszerkísérlet

**Eredmény értelmezése**

**Egy adott felhasználó és tétel minősítésének előrejelzése**

Ha kijelöli a **minősítés előrejelzését** az **Ajánlói előrejelzési típus** alatt, arra kéri az ajánlót, hogy Jósolja meg az adott felhasználó és elem minősítését. A [pontszám Matchbox-ajánlati][score-matchbox-recommender] kimenetének vizualizációja a 21. ábrán látható.

![Az Ajánlói rendszerek pontszámának eredményei – értékelés előrejelzése](./media/interpret-model-results/21.png)

21. ábra. Az ajánlórendszer-minősítési előrejelzés pontszámának megjelenítése

Az első két oszlop a bemeneti adatok által megadott felhasználó-tétel párok. A harmadik oszlop egy adott tétel felhasználójának előrejelzett minősítése. Az első sorban például az ügyfél U1048 a 135026-es, a 2. típusú étterem díjszabása alapján számítjuk ki.

**Elemek ajánlása egy adott felhasználónak**

Ha az **Ajánlói előrejelzési típus** **elemre** kattint, az ajánló rendszer javaslatot tesz az elemek egy adott felhasználóhoz való ajánlására. Az ebben a forgatókönyvben kiválasztott utolsó paraméter *ajánlott elem kiválasztása*. A **névleges elemek (modell kiértékelése)** lehetőségeit elsősorban a betanítási folyamat során a modell kiértékelésére használják. Ebben az előrejelzési szakaszban az **összes elemet** választjuk. A [pontszám Matchbox-ajánlati][score-matchbox-recommender] kimenetének vizualizációja a 22. ábrán látható.

![Az ajánlórendszer pontszámának eredményei – tételre vonatkozó javaslat](./media/interpret-model-results/22.png)

22. ábra. Az ajánlórendszer eredményének megjelenítése – tételre vonatkozó javaslat

A hat oszlop első eleme a megadott felhasználói azonosítókat jelöli, amelyek a bemeneti adatok által megadott elemeket javasolják. A másik öt oszlop a felhasználó számára a relevancia csökkenő sorrendben javasolt elemeit jelöli. Az első sorban például a 134986-as ügyfél-U1048 ajánlott étterme a következő, amelyet 135018, 134975, 135021 és 132862 követ.

**Egy adott felhasználóhoz kapcsolódó felhasználók keresése**

A **kapcsolódó felhasználók** kiválasztásával az **Ajánlói előrejelzési típus** szerint az ajánló rendszer megkeresi a kapcsolódó felhasználókat egy adott felhasználóhoz. A kapcsolódó felhasználók a hasonló beállításokkal rendelkező felhasználók. A forgatókönyvben kiválasztott utolsó paraméter a *kapcsolódó felhasználó kijelölése*. A **felhasználóktól, akik a minősítéssel rendelkező elemeket (a modell kiértékelése esetében)** elsődlegesen a modell kiértékelésére használják a betanítási folyamat során. Az előrejelzési fázis **minden felhasználója** közül választhat. A [pontszám Matchbox-ajánlati][score-matchbox-recommender] kimenetének vizualizációja a 23. ábrát hasonlítja.

![Az ajánlott rendszerszintű felhasználók pontszámának eredménye](./media/interpret-model-results/23.png)

23. ábra. Az ajánló rendszerrel kapcsolatos felhasználók pontszámának megjelenítése

A hat oszlop első része a megadott felhasználói azonosítókat jeleníti meg, amelyek szükségesek a kapcsolódó felhasználók kereséséhez, a bemeneti adatok által megadott módon. A másik öt oszlop a felhasználó előre jelzett kapcsolódó felhasználóit a relevancia csökkenő sorrendjében tárolja. Az első sorban például az ügyfél U1048 legfontosabb ügyfele a U1051, majd a U1066, a U1044, a U1017 és a U1072.

**Egy adott elemhez kapcsolódó elemek keresése**

A **kapcsolódó elemek** kiválasztásával az **Ajánlói előrejelzési típus** alatt megkérdezi, hogy az ajánló rendszer megkeresse-e az adott elemhez kapcsolódó elemeket. A kapcsolódó elemek azok az elemek, amelyek valószínűleg ugyanazt a felhasználót szeretik. A forgatókönyvben kiválasztott utolsó paraméter a *kapcsolódó elem kijelölése*. A **névleges elemek (modell kiértékelése)** lehetőségeit elsősorban a betanítási folyamat során a modell kiértékelésére használják. Az előrejelzési fázis **összes elemét** kiválasztjuk. A [pontszám Matchbox-ajánlati][score-matchbox-recommender] kimenetének vizualizációja a 24. ábrán látható.

![Az Ajánlói rendszerek által kapcsolódó elemek eredményei](./media/interpret-model-results/24.png)

24. ábra. Az Ajánlói rendszeren kapcsolódó elemek eredményeinek megjelenítése

A hat oszlop első eleme a kapcsolódó elemek kereséséhez szükséges, a bemeneti adatok által megadott elem-azonosítókat jelöli. A másik öt oszlop az elem előre jelzett kapcsolódó elemeit a relevancia szempontjából csökkenő sorrendben tárolja. Például az első sorban a 135026 elem legfontosabb eleme a 135074, amelyet 135035, 132875, 135055 és 134992 követ.

**Webszolgáltatás kiadványa**

A kísérletek webszolgáltatásként való közzétételének folyamata hasonló a négy forgatókönyvhöz. Íme a második forgatókönyv (az elemek ajánlása egy adott felhasználónak) példaként. Ugyanezt az eljárást követheti a másik hármat is.

A betanított ajánló rendszer betanított modellként való mentése és a bemeneti adatok egyetlen felhasználói azonosító oszlopba való szűrése igény szerint, a kísérlet összekapcsolható a 25. ábrán, és webszolgáltatásként teheti közzé.

![Az éttermi javaslat problémájának pontozási kísérlete](./media/interpret-model-results/25.png)

25. ábra. Az éttermi javaslat problémájának pontozási kísérlete

A webszolgáltatás futtatásakor a visszaadott eredmény a 26. ábrára hasonlít. A felhasználói U1048 öt ajánlott étterme 134986, 135018, 134975, 135021 és 132862.

![Az Ajánlói rendszerszolgáltatás mintája](./media/interpret-model-results/25_1.png)

![Példa a kísérlet eredményeire](./media/interpret-model-results/26.png)

26. ábra. Webszolgáltatás-szolgáltatás eredménye – probléma

<!-- Module References -->
[assign-to-clusters]: /azure/machine-learning/studio-module-reference/assign-data-to-clusters
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[score-matchbox-recommender]: /azure/machine-learning/studio-module-reference/score-matchbox-recommender
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-clustering-model]: /azure/machine-learning/studio-module-reference/train-clustering-model
[train-matchbox-recommender]: /azure/machine-learning/studio-module-reference/train-matchbox-recommender