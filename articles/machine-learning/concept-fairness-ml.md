---
title: A gépi tanulási modellekben való méltánytalanság enyhítése (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy a Fairlearn Python-csomag hogyan hozhat létre tisztességesebb modelleket, és hogyan segíthet a Fair-modellek kiépítésében.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 01/26/2021
ms.custom: responsible-ml
ms.openlocfilehash: 276c91ad7fb5b09dbe18d989741f0f54b8b0eb09
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101659681"
---
# <a name="mitigate-unfairness-in-machine-learning-models-preview"></a>A gépi tanulási modellekben való méltánytalanság enyhítése (előzetes verzió)

Ismerje meg, hogy a gépi tanulás és a [Fairlearn](https://fairlearn.github.io/) nyílt forráskódú Python-csomag milyen módon segít enyhíteni a gépi tanulási modellek méltánytalan problémáit. Ha nem tesz erőfeszítéseket a tisztességgel kapcsolatos problémák megismerésére és a tisztességes értékelésre a gépi tanulási modellek létrehozásakor, létrehozhat olyan modelleket, amelyek tisztességtelen eredményeket hoznak létre.

A Fairlearn nyílt forráskódú csomagra vonatkozó [felhasználói útmutató](https://fairlearn.github.io/main/user_guide/index.html) következő összefoglalása leírja, hogyan használhatja azt a kiépített AI-rendszerek tisztaságának értékeléséhez.  A nyílt forráskódú Fairlearn-csomag olyan lehetőségeket is kínál, amelyek segítenek enyhíteni vagy csökkenteni a megfigyelt tisztességgel kapcsolatos problémákat.  Tekintse meg az útmutató és a [minta jegyzetfüzeteket](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) , amelyek lehetővé teszik a mesterséges intelligencia [-](how-to-machine-learning-fairness-aml.md) rendszerek értékelését az Azure Machine Learningon való képzés során.


## <a name="what-is-fairness-in-machine-learning-models"></a>Mi a tisztességes a gépi tanulási modellekben?

>[!NOTE]
> A méltányosság egy szocio-technikai kérdés. A méltányosság számos aspektusa, például az igazságszolgáltatás és a megfelelő folyamat nem rögzíti a mennyiségi korrektség mérőszámait. Emellett számos mennyiségi korrektség mérőszáma nem lehet egyszerre kielégítve. A Fairlearn nyílt forráskódú csomag célja, hogy lehetővé tegye az emberek számára a különböző hatás-és kockázatcsökkentő stratégiák értékelését. Végső soron a mesterséges intelligenciát és a gépi tanulási modelleket kiépítő emberi felhasználók a forgatókönyvnek megfelelő kompromisszumokat hozhatnak.

A mesterséges intelligencia és a gépi tanulási rendszerek tisztességtelen viselkedést tudnak megjeleníteni. A tisztességtelen viselkedés meghatározásának egyik módja a sérülése, vagy a felhasználókra gyakorolt hatás. A mesterséges intelligencia-rendszerek számos fajta kárt okozhatnak. További információ: [Kate Crawford NeurIPS 2017 vitaindítója](https://www.youtube.com/watch?v=fMym_BKWQzk) .

A mesterséges intelligencia által okozott károk két gyakori típusa:

- Kiosztási kár: egy AI-rendszer bizonyos csoportok számára kiterjeszti vagy visszatartja a lehetőségeket, erőforrásokat vagy információkat. Ilyenek például a bérbeadás, az iskolai beléptetések és a hitelnyújtás, ahol a modell sokkal jobb lehet, ha a jó jelöltek kiválogatása egy adott csoportba tartozik, mint az egyéb csoportok között.

- A szolgáltatás minőségének károsodása: az AI-rendszerek nem működnek megfelelően egy csoport számára, mint egy másik személy. Előfordulhat például, hogy a hangfelismerő rendszer nem tud működni a nők számára, mint a férfiaknál.

Az AI-rendszerek tisztességtelen viselkedésének csökkentése érdekében fel kell mérnie és csökkentenie kell ezeket a károkat.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>A méltányosság felmérése és enyhítése a Fairlearn

A Fairlearn egy nyílt forráskódú Python-csomag, amely lehetővé teszi a gépi tanulási rendszerek fejlesztői számára, hogy felmérjék a rendszereik tisztességét, és enyhítsék a méltánytalanság mértékét.

A Fairlearn nyílt forráskódú csomag két összetevőből áll:

- Assessment irányítópult: egy Jupyter notebook widget annak értékeléséhez, hogy a modell előrejelzései hogyan befolyásolják a különböző csoportokat. Emellett lehetővé teszi több modell összehasonlítását a méltányosság és a teljesítmény mérőszámok használatával.
- Enyhítő algoritmusok: olyan algoritmusok összessége, amelyek a bináris besorolásban és a regresszióban csökkentik a méltánytalanság enyhítését.

Ezek az összetevők együtt lehetővé teszik az adatszakértők és az üzleti vezetők számára, hogy a méltányosság és a teljesítmény között bármilyen kompromisszumot keressenek, és kiválassza az igényeiknek leginkább megfelelő kockázatcsökkentő stratégiát.

## <a name="assess-fairness-in-machine-learning-models"></a>A tisztességesség értékelése a gépi tanulási modellekben

A Fairlearn nyílt forráskódú csomagjában a méltányosság fogalma a **csoport méltányosság** néven ismert megközelítése, amely megkérdezi, hogy mely személyeknek van kockázata az ártalmak felderítésére? A kapcsolódó csoportok, más néven alpopulációk, **bizalmas szolgáltatásokkal** vagy bizalmas attribútumokkal vannak meghatározva. Az érzékeny funkciók a Fairlearn nyílt forráskódú csomagjában, vektorként vagy nevű mátrixként lesznek átadva  `sensitive_features` . A kifejezés azt sugallja, hogy a rendszertervezőnek érzékenynek kell lennie ezekre a funkciókra a csoport tisztességének értékelésekor. 

Egy dolog, ami szem előtt tartja, hogy ezek a funkciók tartalmaznak-e adatvédelmi következményeket a magánjellegű adatok miatt. A "bizalmas" szó azonban nem jelenti azt, hogy ezek a funkciók nem használhatók előrejelzések készítésére.

>[!NOTE]
> A méltányosság értékelése nem pusztán technikai gyakorlat.  A Fairlearn nyílt forráskódú csomag segítségével kiértékelheti a modell tisztességesjét, de nem fogja elvégezni az értékelést.  A Fairlearn nyílt forráskódú csomag segíti a mennyiségi mérőszámok azonosítását a méltányosság kiértékeléséhez, de a fejlesztőknek minőségi elemzést is kell végezniük a saját modelljeik korrekt kiértékeléséhez.  A fentiekben ismertetett bizalmas funkciók példa erre a minőségi elemzésre.     

Az értékelési fázisban a méltányosság mértékét a különbségek mérőszámai határozzák meg. A különbségi **mérőszámok** kiértékelik és összehasonlítják a modell viselkedését a különböző csoportok között, akár arányként, akár különbségként. A Fairlearn nyílt forráskódú csomag a különbségi mérőszámok két osztályát támogatja:


- A modell teljesítményének eltérése: ezek a mérőszámok a kiválasztott teljesítménymutató értékeit határozzák meg a különböző alcsoportokon belül. Néhány példa:

  - eltérés a pontossági arányban
  - eltérés a hibák arányában
  - különbség a precizitásban
  - eltérés a visszahívás során
  - a különbség a MAE-ben
  - sokan mások

- Eltérés a kiválasztási arányban: Ez a metrika a különböző alcsoportok közötti különbségeket tartalmazza a kiválasztási arányban. Erre példa a hitelek jóváhagyási arányának eltérése. A kiválasztási arány azt jelenti, hogy az egyes osztályok datapoints frakciója 1 (bináris besorolás) vagy előrejelzési értékek eloszlása (regresszióban).

## <a name="mitigate-unfairness-in-machine-learning-models"></a>A gépi tanulási modellek méltánytalan használatának enyhítése

### <a name="parity-constraints"></a>Paritásos megkötések

A Fairlearn nyílt forráskódú csomag számos, a méltánytalanság enyhítésére szolgáló algoritmust tartalmaz. Ezek az algoritmusok több korlátozást is támogatnak a prediktív viselkedés **paritásos megkötések** vagy feltételek szerint. A paritásos megkötések esetében a prediktív viselkedés bizonyos aspektusait össze kell hasonlítani az érzékeny funkciók által definiált csoportok között (például különböző fajok). A nyílt forráskódú Fairlearn található enyhítő algoritmusok ilyen paritásos korlátozásokkal csökkentik a megfigyelt tisztességgel kapcsolatos problémákat.

>[!NOTE]
> A nem megfelelőség enyhítése a modellben azt jelenti, hogy csökkenti a méltánytalanság csökkentését, de ez a technikai megoldás nem tudja teljesen megszüntetni ezt a méltánytalanság.  A Fairlearn nyílt forráskódú csomagjának méltánytalan enyhítési algoritmusai olyan javasolt kockázatcsökkentő stratégiákat biztosítanak, amelyek segítenek csökkenteni a nem méltányosság használatát a gépi tanulási modellekben, de nem teszik lehetővé a tisztességtelenség teljes kiiktatását.  Előfordulhat, hogy más paritási korlátozásokat vagy feltételeket kell figyelembe venni az egyes fejlesztői gépi tanulási modellekhez. A Azure Machine Learningt használó fejlesztőknek saját maguknak kell meghatározniuk, ha a megoldás megfelelő módon kiküszöböli a nem méltányosság kihasználása és a gépi tanulási modellek üzembe helyezése terén.  

A Fairlearn nyílt forráskódú csomagja a következő típusú paritásos korlátozásokat támogatja: 

|Paritásos megkötés  | Cél  |Gépi tanulási feladat  |
|---------|---------|---------|
|Demográfiai paritás     |  A foglalási károsodások enyhítése | Bináris besorolás, regresszió |
|Equaled odds  | Kiosztási és szolgáltatásminőség-károsodások diagnosztizálása | Bináris besorolás        |
|Egyenlő lehetőség | Kiosztási és szolgáltatásminőség-károsodások diagnosztizálása | Bináris besorolás        |
|Kötött csoport elvesztése     |  A szolgáltatás minőségi károsodásának enyhítése | Regresszió |



### <a name="mitigation-algorithms"></a>Enyhítő algoritmusok

A Fairlearn nyílt forráskódú csomag utófeldolgozó biztosít, és csökkenti a méltánytalanság enyhítésének algoritmusait:

- Csökkentés: ezek az algoritmusok Standard fekete dobozos gépi tanulási kalkulátort (pl. LightGBM modellt) hoznak létre, és újraképzésen alapuló modelleket állítanak elő, amelyek újrasúlyozott betanítási adatkészletek sorozatával rendelkeznek. Előfordulhat például, hogy egy bizonyos nemhez tartozó pályázók súlyozása vagy csökkentése a modellek újratanítása és a különböző nemi csoportok közötti egyenlőtlenségek csökkentése érdekében. A felhasználók ezután olyan modellt választhatnak, amely a lehető legjobb kompromisszumot biztosítja a pontosság (vagy más teljesítmény mérőszáma) és a különbség között, ami általában üzleti szabályokon és költségszámításokon alapul.  
- Utómunka: ezek az algoritmusok egy meglévő besorolást és az érzékeny funkciót is felhasználják bemenetként. Ezután származtatják az osztályozó előrejelzését, hogy kikényszerítsék a megadott méltányos korlátozásokat. A küszöbérték-optimalizálás legnagyobb előnye az egyszerűség és a rugalmasság, mivel nem kell átképeznie a modellt. 

| Algoritmus | Description | Gépi tanulási feladat | Bizalmas funkciók | Támogatott paritásos megkötések | Algoritmus típusa |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | A Fair besorolásra vonatkozó, a [méltányos](https://arxiv.org/abs/1803.02453) besoroláshoz | Bináris besorolás | Kategorikus | [Demográfiai paritás](#parity-constraints), [equald odds](#parity-constraints) | Csökkentése |
| `GridSearch` | A [valós besorolás csökkentési megközelítésében](https://arxiv.org/abs/1803.02453) leírt fekete négyzetes megközelítés| Bináris besorolás | Bináris | [Demográfiai paritás](#parity-constraints), [equald odds](#parity-constraints) | Csökkentése |
| `GridSearch` | A fekete Box megközelítése, amely a valós regressziós teljesítmény-keresés változatát valósítja meg a [valós regresszió: mennyiségi definíciók és a redukáló algoritmusok](https://arxiv.org/abs/1905.12843) által leírt, kötött csoportok elvesztésének algoritmusával. | Regresszió | Bináris | [Kötött csoport elvesztése](#parity-constraints) | Csökkentése |
| `ThresholdOptimizer` | Utófeldolgozó algoritmus a [felügyelt tanulásban az esélyegyenlőségi lehetőség](https://arxiv.org/abs/1610.02413)alapján. Ez a módszer egy meglévő osztályozó és a bizalmas szolgáltatás bemenetét veszi figyelembe, és az osztályozó előrejelzésének monoton átalakítását származtatja a megadott paritásos megkötések érvénybe léptetéséhez. | Bináris besorolás | Kategorikus | [Demográfiai paritás](#parity-constraints), [equald odds](#parity-constraints) | Feldolgozás utáni |

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan használhatja a különböző összetevőket a Fairlearn [GitHub](https://github.com/fairlearn/fairlearn/), [felhasználói útmutató](https://fairlearn.github.io/main/user_guide/index.html), [példák](https://fairlearn.github.io/main/auto_examples/index.html)és [minta jegyzetfüzetek](https://github.com/fairlearn/fairlearn/tree/master/notebooks)használatával történő ellenőrzésével.
- Megtudhatja, [Hogyan](how-to-machine-learning-fairness-aml.md) engedélyezheti Azure Machine learning gépi tanulási modelljeinek méltányos értékelését.
- Tekintse meg a [minta-jegyzetfüzeteket](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) a Azure Machine learning a tisztesség értékelésére szolgáló forgatókönyvekben. 
