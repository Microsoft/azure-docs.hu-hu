---
title: 'ML Studio (klasszikus): válaszok előrejelzése regressziós modellekkel – Azure'
description: Egyszerű regressziós modell létrehozása az adatelemzési díj előrejelzéséhez a kezdők számára készült 4. videóban. Lineáris regressziót tartalmaz a célként megadott adattal.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: c6d78b10b98abe33744673459b3d31d8941aac89
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520408"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Válasz előrejelzése egy egyszerű modell segítségével


## <a name="video-4-data-science-for-beginners-series"></a>4. videó: az adatelemzés kezdőknek sorozata
Megtudhatja, hogyan hozhat létre egy egyszerű regressziós modellt, amely a kezdőknek szóló 4. videóban megjósolhatja a Diamond adatelemzési díját. Egy regressziós modellt rajzolunk a célként megadott adattal.

Ha ki szeretné próbálni a sorozatot, tekintse meg őket. [Ugrás a videók listájára](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>További videók ebben a sorozatban
Az *adatelemzés kezdőknek* az adatelemzés öt rövid videóban való bevezetésének első lépései.

* 1. videó: [az 5 kérdés adatelemzési válasz](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 perc 14 mp)*
* 2. videó: [készen áll](data-science-for-beginners-is-your-data-ready-for-data-science.md) az adatelemzésre? *(4 perc 56 mp)*
* 3. videó: [Kérdezzen rá az adatválaszra](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 perc 17 mp)*
* 4. videó: válasz megjósolása egy egyszerű modellel
* 5. videó: [a többi személy munkájának másolása az adatelemzéshez](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 mp)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Átirat: válasz megjósolása egy egyszerű modellel
Üdvözöljük az "adatelemzés kezdőknek" sorozat negyedik videójában. Ebben az esetben egy egyszerű modellt hozunk létre, és előrejelzést készítünk.

Az *adatmodell* egy egyszerűsített történet az adatkezeléshez. Megmutatom, mire gondolok.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Releváns, pontos, csatlakoztatott és elegendő adatok gyűjtése
Tegyük fel, hogy egy gyémántot szeretnék vásárolni. Van egy olyan gyűrűm, amely egy 1,35 karátos Diamond-ra vonatkozó beállítással rendelkezett a nagymamám számára, és szeretnék megmutatni, hogy Mennyibe kerül a díj. Egy jegyzettömb és egy toll az ékszer-áruházban, és leírom az összes gyémánt árát az adott esetben, és mennyit mérjük az összes káró. Az első rombusztól kezdődően 1,01 karátos és $7 366.

Most megyek át, és ezt az összes többi, az áruházban található gyémánt esetében elvégezem.

![Rombusz típusú oszlopok](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Figyelje meg, hogy a lista két oszloppal rendelkezik. Minden oszlophoz eltérő attribútum tartozik, és minden egyes sor egyetlen gyémántot jelképező adatpont.

Egy kis adathalmazt hoztunk létre itt – egy táblát. Figyelje meg, hogy megfelel a minőségi követelményeknek:

* Az adat **releváns** – a súlyozás egyértelműen a díjszabáshoz kapcsolódik
* **Pontos** – a rendszer duplán ellenőrizte, hogy milyen árakat írunk le
* **Csatlakoztatva** van – nincsenek üres szóközök az oszlopok egyikében sem
* És amint láthatjuk, **elég** az információ a kérdés megválaszolásához

## <a name="ask-a-sharp-question"></a>Tegyen fel egy éles kérdést
Most a kérdést éles módon mutatjuk be: "Mennyibe kerül a 1,35 Carat Diamond megvásárlása?"

A lista nem tartalmaz 1,35 karátos gyémántot, ezért a többi adattal kell használnia, hogy választ kapjon a kérdésre.

## <a name="plot-the-existing-data"></a>A meglévő adatterületek ábrázolása
Az első dolog, hogy egy vízszintes, egy tengely nevű sort rajzolunk a súlyok ábrázolásához. A súlyok tartománya 0 és 2 közé esik, ezért rajzolunk egy sort, amely az adott tartományt lefedi, és minden fél Carat-re helyezi a kullancsot.

A következő lépésben egy függőleges tengelyt rajzolunk az ár rögzítéséhez és a vízszintes súlyozási tengelyhez való kapcsolódáshoz. Ez lesz a dollár egységben. Most már van egy koordináta-tengelyünk.

![Súlyozási és díjszabási tengelyek](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Most ezeket az adatsorokat fogjuk kialakítani, és egy *Scatter-területre* fogjuk kapcsolni. Ez nagyszerű módja a numerikus adatkészletek megjelenítésének.

Az első adatpontnál a szemgolyó egy függőleges vonal, 1,01 karátos. Ezt követően a $7 366-kor egy vízszintes vonalat. Ahol megfelelnek, egy pontot rajzolunk. Ez az első gyémántot jelöli.

Most haladunk végig az egyes gyémántokon ezen a listán, és ugyanezt a dolgot is megteheti. Amikor áthaladunk, ez az, amit kapunk: egy csomó pont, egy az egyes gyémántokhoz.

![Pontdiagram](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>A modell rajzolása az adatpontokon keresztül
Most, ha megtekinti a pontokat és a kancsal, a gyűjtemény úgy néz ki, mint egy FAT, fuzzy line. Megtehetjük a jelölőt, és rajzolunk egy egyenes vonalat.

Egy sor rajzolásával létrehozunk egy *modellt*. Képzelje el ezt úgy, hogy a valós világba kerül, és egy egyszerű rajzfilm-verziót használ. A Cartoon nem stimmel – a sor nem halad végig az összes adatponton. Ez azonban hasznos egyszerűsítés.

![Lineáris regressziós vonal](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Az a tény, hogy az összes pont nem pontosan a sorban halad, rendben van. Az adatszakértők ezt a modellt ismertetik, amely azt jelzi, hogy létezik a modell – Ez a vonal, majd minden egyes ponthoz tartozik valamilyen *zaj* vagy *variancia* . Ott van a mögöttes tökéletes kapcsolat, és ott van a kavicsos, valós világ, amely zaj-és bizonytalanságot ad.

Mivel a kérdés megválaszolása mennyit jelent *?* ezt *regressziónak* nevezzük. És mivel egyenes vonalat használunk, *lineáris regresszió*.

## <a name="use-the-model-to-find-the-answer"></a>A modell használata a válasz megkereséséhez
Most már van egy modellünk, és feltesszük a kérdést: Mennyibe kerül a 1,35 Carat Diamond díja?

A kérdés megválaszolásához a szemgolyó 1,35 karátos, és rajzoljon egy függőleges vonalat. Ha átlépi a modell sorát, a rendszer egy vízszintes vonalat helyez el a dollár tengelyen. Közvetlenül a 10 000-kor. Bumm! Ez a válasz: A 1,35 Carat Diamond költségei a $10 000-es kiadással kapcsolatban.

![Válasz keresése a modellen](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Megbízhatósági intervallum létrehozása
Ez természetes, hogy meggondolja, mennyire pontos ez az előrejelzés. Hasznos tudni, hogy a 1,35 Carat Diamond nagyon közel van-e a $10 000-hez, vagy jóval magasabb vagy alacsonyabb. Ennek megállapításához rajzoljon egy keretet a regressziós vonal körül, amely a pontok többségét tartalmazza. Ezt a borítékot a *megbízhatósági intervallumnak* nevezzük: biztosak vagyunk abban, hogy az árak a borítékon belül esnek, mert a legkorábbi többségük. Két további vízszintes vonalat is megrajzolhat, amelyből a 1,35 karátos vonal a boríték tetején és alján halad.

![Megbízhatósági intervallum](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Most mondhatjuk, hogy mi a megbízhatósági intervallumunk: nyugodtan mondhatjuk, hogy a 1,35 Carat Diamond ára körülbelül $10 000 – de a $8 000-as, de akár a $12 000-as szint is lehet.

## <a name="were-done-with-no-math-or-computers"></a>Elkészült, matematikai vagy számítógép nélküli
Megtettük, hogy milyen adatszakértők fizettek a teendőknek, és a következő lépésekkel rendelkezünk:

* Feltettük azt a kérdést, hogy az adatválaszok
* A *lineáris regressziót* használó *modellt* készítettünk
* *Előrejelzést* készítettünk, amely *megbízhatósági intervallummal* fejeződött be

A matematikai vagy a számítógépeket nem használjuk a művelet elvégzésére.

Most, ha további információra volt szükségünk, például:...

* a rombusz kivágása
* színvariációk (Hogyan zárjuk be a gyémántot fehérre)
* a gyémántba való felvételek száma

... Ezután több oszlop lett volna. Ebben az esetben a matematika hasznos lesz. Ha több mint két oszlopa van, nehéz rajzolni a pontokat a papíron. A Math lehetővé teszi, hogy szépen illeszkedjen a vonalhoz vagy az adataihoz.

Emellett, ha csupán néhány rombusz helyett a 2000-es vagy a 2 000 000-es verziót használta, sokkal gyorsabban dolgozhat a számítógéppel.

Ma már beszéltünk a lineáris regresszióval kapcsolatban, és az adatelemzés során készítettünk egy előrejelzést.

Tekintse meg a többi videót a "kezdő adatelemzés" Microsoft Azure Machine Learning Studio (klasszikus).

## <a name="next-steps"></a>Következő lépések
* [Próbálja ki az első adatelemzési kísérletet Machine Learning Studio (klasszikus)](create-experiment.md)
* [Bevezetés a Machine Learning on Microsoft Azure](../overview-what-is-azure-ml.md)