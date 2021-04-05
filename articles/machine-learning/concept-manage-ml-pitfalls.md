---
title: Kerülje a & kiegyensúlyozatlan adathalmazok AutoML való túlillesztését
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning gépi tanulási megoldásaival azonosíthatja és kezelheti a ML-modellek gyakori buktatóit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: ba85bda1e322d3efd467527b48bd4cd90eb7ce8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96922628"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Az automatizált gépi tanulással megakadályozhatja a túlilleszkedő és a kiegyensúlyozatlan adatkezelést

A túlságosan illeszkedő és kiegyensúlyozatlan adat a gépi tanulási modellek készítésekor előforduló gyakori buktatók. A Azure Machine Learning automatikus gépi tanulása alapértelmezés szerint diagramokat és mérőszámokat tartalmaz, amelyek segítenek a kockázatok azonosításában, és az ajánlott eljárások megvalósításában nyújtanak segítséget. 

## <a name="identify-over-fitting"></a>Túlillesztés azonosítása

A gépi tanulás túlterhelése akkor fordul elő, ha egy modell túl jól illeszkedik a betanítási adathoz, ezért nem lehet pontosan előre jelezni a láthatatlan tesztelési eredményeket. Más szóval a modell egyszerűen megjegyezte a betanítási adatmintákat és a zajt, de nem elég rugalmas ahhoz, hogy valós adatelemzéseket készítsen.

Vegye figyelembe a következő betanított modelleket és a hozzájuk kapcsolódó betanítási és tesztelési pontosság.

| Modellezés | Vonat pontossága | Teszt pontossága |
|-------|----------------|---------------|
| A | 99.9% | 95% |
| B | 87% | 87% |
| C | 99.9% | 45% |

Az **a** modellt figyelembe véve gyakori tévhit, hogy ha a láthatatlan adatokon a teszt pontossága alacsonyabb, mint a betanítási pontosság, a modell túl van szerelve. A teszt pontosságának azonban mindig kisebbnek kell lennie, mint a kiképzés pontossága, és a túlzottan illeszkedő és a megfelelő illeszkedéshez való különbségtétel nem *sokkal* kevésbé pontos. 

Az **a** és **B** modellek összehasonlításakor a Model **a** jobb modell, mert a teszt pontossága nagyobb, és bár a tesztelési pontosság valamivel alacsonyabb, mint 95%, nem jelent jelentős különbséget, amely a túlzottan illeszkedő megoldásra utal. Nem választhatja a **B** modellt egyszerűen azért, mert a vonat és a teszt pontosság egymáshoz közelednek.

A **C** modell a túlzott illesztések egyértelmű esetét jelöli. a betanítás pontossága nagyon magas, de a teszt pontossága nem a magashoz közel van. Ez a különbségtétel szubjektív, de a probléma és az adatok ismerete, valamint a hibák számának elfogadható.

## <a name="prevent-over-fitting"></a>Túlterhelések megakadályozása

A legtöbbször is felmerülő esetekben a túlterhelt modell azt feltételezi, hogy a betanítás során a funkció értékének kombinációja mindig ugyanazt a kimenetet eredményezi a célhoz.

A túlzott méretezés elkerülésének legjobb módja, ha a következő módszerekkel kapcsolatos ajánlott eljárásokat követi:

* Több betanítási információ használata és a statisztikai torzulások kiküszöbölése
* Cél szivárgásának megelőzése
* Kevesebb funkció használata
* **Regularizációs és hiperparaméter optimalizálás**
* **A modell összetettségi korlátai**
* **Keresztellenőrzés**

Az automatikus ML-ben a fenti első három elem az Ön által **megvalósított legjobb gyakorlat**. Az utolsó három félkövérrel szedett elem az ajánlott eljárás, amely az automatizált, a túlterhelések elleni védelem érdekében alapértelmezés szerint **megvalósítható** . Az automatizált ML-től eltérő beállításokban mind a hat ajánlott eljárás a következő lehet, hogy elkerülje a túlzottan illeszkedő modelleket.

### <a name="best-practices-you-implement"></a>Az Ön által megvalósított ajánlott eljárások

A **több adat** használata a legegyszerűbb és a lehető legjobb módszer a túlzott méretezés megelőzésére, és a hozzáadott bónusz általában növeli a pontosságot. Ha több adathalmazt használ, a modell nehezebbé válik a pontos mintázatok memorizálása érdekében, és olyan megoldások elérésére kényszerül, amelyek rugalmasabbak a további feltételek kielégítéséhez. Fontos továbbá a **statisztikai torzulások** felismerése is, hogy a betanítási adatai ne tartalmazzanak olyan elszigetelt mintákat, amelyek nem léteznek az élő előrejelzési adataiban. Ez a forgatókönyv nehezen oldható meg, mert előfordulhat, hogy nem lehet túlságosan összekapcsolni a vonat-és a tesztelési készletek között, de az élő tesztelési adathoz képest előfordulhat, hogy túl is illik.

A **célzott szivárgás** egy hasonló probléma, ahol előfordulhat, hogy nem jelenik meg a betanítási és a tesztelési készletek közötti túlterhelés, hanem az előrejelzési időpontban jelenik meg. A cél szivárgás akkor fordul elő, ha a "Cheats" modell a betanítás során olyan információhoz fér hozzá, amelyeknek általában nem kell előrejelzési időpontban lennie. Ha például a probléma a hétfő alapján várhatóan megjósolható, hogy Mennyibe kerül az áru díja, de az egyik funkció, amely véletlenül a csütörtöki adatokból származik, a modellnek nem lesz előrejelzési ideje, mert nem látja a jövőben. A cél szivárgás egyszerű tévedés, de gyakran a probléma szokatlanul nagy pontossága jellemzi. Ha a készlet árának előrejelzését és a modell 95%-os pontosságú betanítását kísérli meg, akkor valószínű, hogy valahol a funkciók között kell elszivárogni.

A **funkciók eltávolítása** a túlzottan illeszkedő megoldásokkal is segíthet, mivel megakadályozza, hogy a modell túl sok mezőt használjon a konkrét minták memorizálása érdekében, ami rugalmasabb lehet. A mennyiségi mérés nehéz lehet, de ha eltávolíthatja a funkciókat, és megtarthatja ugyanazt a pontosságot, valószínűleg rugalmasabban tette a modellt, és csökkentette a túlzott méretezés kockázatát.

### <a name="best-practices-automated-ml-implements"></a>Ajánlott eljárások automatizált ML-eszközökhöz

A **regularizációs** egy költséghatékony funkció minimalizálása a komplex és a túlzottan felszerelt modellek szankcionálására. A regularizációs függvények különböző típusai vannak, de általánosságban mind a modellnek, valamint az eltérésnek és a bonyolultságnak a büntetése. Az automatikus ML az L1 (lasszó), az L2 (Ridge) és a ElasticNet (L1 és L2) kombinációt használja különböző kombinációkban különböző modell-hiperparaméter beállításokkal, amelyek szabályozzák a terhelést. Egyszerű feltételek esetén az automatikus ML változó a modell szabályozása és a legjobb eredmény kiválasztásával változhat.

Az automatikus ML emellett explicit módon meggátolja a **modell bonyolultságának korlátozását** , hogy megakadályozza a túlzott méretezést. A legtöbb esetben ez a megvalósítás kifejezetten a döntési fa-vagy erdő-algoritmusokhoz tartozik, ahol az egyes faszerkezetek maximális mélysége korlátozott, és az erdőben vagy az Ensemble-technikákban felhasznált fák teljes száma korlátozott.

A többszörös **ellenőrzés (CV)** a teljes betanítási adat több részhalmazának betanítása, valamint az egyes alkészleteken található modellek képzésének folyamata. Az elképzelés az, hogy egy modell "szerencsés" lehet, és nagy pontossággal rendelkezik egy részhalmazsal, de a modell számos részhalmazának használatával nem éri el ezt a nagy pontosságot minden alkalommal. Az önéletrajz használatakor meg kell adnia egy érvényesítési Holdout adatkészletet, meg kell adnia az önéletrajz betöltését (az alkészletek számát) és az automatikus ML-t, majd a modell betanításával és a hiperparaméterek beállítása finomhangolásával csökkentheti az ellenőrzési csoport hibáját Az egyik CV-dobás túl nagy méretű lehet, de a sok közül többen is csökkenti annak a valószínűségét, hogy a végső modellje túlterhelt. A kompromisszum az, hogy az önéletrajz hosszabb időt és így nagyobb költségeket eredményez, mivel a modell betanítása helyett egyszer betanítjuk az egyes *n* CV-alkészletekre. 

> [!NOTE]
> Alapértelmezés szerint nincs engedélyezve a kereszt-ellenőrzés. az automatikus ML-beállításokban kell konfigurálni. A többszörös ellenőrzés konfigurálása és az érvényesítési adatkészletek megadása után azonban a folyamat automatizálható. További információ a több [ellenőrzési konfigurációról az automatikus ml-ben](how-to-configure-cross-validation-data-splits.md)

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>A kiegyensúlyozatlan adattal rendelkező modellek azonosítása

A rendszer a gépi tanulási besorolási forgatókönyvek esetében általában az adatokon alapuló, nem kiegyensúlyozott adatokra hivatkozik, és olyan adatokra utal, amelyek az egyes osztályokba tartozó megfigyelések aránytalan arányát tartalmazzák. Ez az egyensúlyhiány a modell pontosságának hamisan érzékelt pozitív hatását eredményezheti, mivel a bemeneti adatok az egyik osztályhoz képest elfogultak, ami azt eredményezi, hogy a betanított modell a torzítást utánozza. 

Emellett az automatikus ML-futtatások automatikusan létrehozzák a következő diagramokat, amelyek segítségével megismerheti a modell besorolásának helyességét, és azonosíthatja a kiegyensúlyozatlan adatok által potenciálisan érintett modelleket.

Diagram| Leírás
---|---
[Zavart mátrix](how-to-understand-automated-ml.md#confusion-matrix)| Kiértékeli a helyesen kategorizált címkéket az adatok tényleges címkéjén. 
[Pontosság – visszahívás](how-to-understand-automated-ml.md#precision-recall-curve)| Kiértékeli a helyes feliratok arányát az adatokban található címkézett példányok arányával. 
[ROC-görbék](how-to-understand-automated-ml.md#roc-curve)| Kiértékeli a helyes feliratok arányát a hamis pozitív feliratok arányával.

## <a name="handle-imbalanced-data"></a>Kiegyensúlyozatlan adatmennyiség kezelése 

A Machine learning-munkafolyamatok egyszerűsítésének céljaként az **automatikus ml beépített képességekkel rendelkezik** , amelyek segítenek a kiegyensúlyozatlan adatmennyiségek, például a 

- A **Weight oszlop**: az automatikus ml a súlyok oszlopát támogatja bemenetként, ami az adatokban lévő sorok súlyozását eredményezi, ami felhasználható egy osztály további vagy kisebb "fontos" értékének elvégzésére.

- Az automatikus ML-észlelési egyensúlyhiány által használt algoritmusok, ha a kisebbségi osztályban lévő minták száma egyenlő vagy kevesebb mint 20%-a a többségi osztályban található minták számával, ahol a kisebbségi osztály a legkevesebb minta és a többségi osztályra hivatkozik, és a legtöbb mintának megfelelő. Ezt követően a AutoML egy kísérletet fog futtatni a mintavételen alapuló adattal annak ellenőrzésére, hogy az osztályok súlyozása orvosolja-e a problémát, és javítja a teljesítményt. Ha a kísérlet során jobb teljesítményt biztosít, akkor ezt a megoldást alkalmazza a rendszer.

- Használjon olyan teljesítmény-mérőszámot, amely jobban bánik a kiegyensúlyozatlan adatokkal. Például az AUC_weighted egy elsődleges metrika, amely az osztályt jelképező minták relatív száma alapján kiszámítja az egyes osztályok hozzájárulását, ezért a rendszer sokkal megbízhatóbb az egyensúlyhiány ellen.

Az alábbi módszerek további lehetőségeket biztosítanak az **automatikus ml-n kívüli**, kiegyensúlyozatlan adatmennyiség kezelésére. 

- Újramintavételezés még az osztályra is, akár a kisebb osztályok mintavételezésével vagy a nagyobb osztályok mintavételezésével. Ezek a módszerek szaktudást igényelnek a feldolgozáshoz és az elemzéshez.

- Tekintse át a teljesítmény mérőszámait a kiegyensúlyozatlan adatokhoz. Az F1 pontszám például a pontosság és a visszahívás harmonikus középértéke. A pontosság mértéke az osztályozó pontossága, ahol a nagyobb pontosság kevesebb hamis pozitív értéket jelez, míg a visszahívás az osztályozó teljességét jelzi, ahol a nagyobb visszahívás kevesebb hamis negatív értéket jelez.

## <a name="next-steps"></a>Következő lépések

Tekintse át a példákat, és Ismerje meg, hogyan hozhat létre modelleket automatizált gépi tanulás használatával:

+ Kövesse az [oktatóanyagot: regressziós modell automatikus betanítása Azure Machine learning](tutorial-auto-train-models.md)

+ Konfigurálja az automatikus betanítási kísérlet beállításait:
  + A Azure Machine Learning Studióban [kövesse ezeket a lépéseket](how-to-use-automated-ml-for-ml-models.md).
  + A Python SDK használatával hajtsa [végre az alábbi lépéseket](how-to-configure-auto-train.md).


