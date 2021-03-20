---
title: 'ML Studio (klasszikus): adatértékelés – Azure'
description: Négy feltételnek kell megfelelnie az adatelemzési igényeknek. Ez a videó olyan konkrét példákat tartalmaz, amelyek segítséget nyújtanak az alapszintű adatok kiértékeléséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 07db1f1c8021759fbd5702ad9353f74fcd1524c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516974"
---
# <a name="is-your-data-ready-for-data-science"></a>Készen állnak adatai az elemzésre?
## <a name="video-2-data-science-for-beginners-series"></a>2. videó: adatelemzés kezdőknek sorozat
Ismerje meg, hogyan értékelheti ki az adatait, és győződjön meg arról, hogy megfelel az alapvető feltételeknek, hogy készen álljon az adatelemzésre.

Ha ki szeretné próbálni a sorozatot, tekintse meg őket. [Ugrás a videók listájára](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>További videók ebben a sorozatban
Az *adatelemzés kezdőknek* az adatelemzés öt rövid videóban való bevezetésének első lépései.

* 1. videó: [az 5 kérdés adatelemzési válasz](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 perc 14 mp)*
* 2. videó: készen áll az adatelemzésre?
* 3. videó: [Kérdezzen rá az adatválaszra](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 perc 17 mp)*
* 4. videó: [Válasz Megjósolása egy egyszerű modellel](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 perc 42 mp)*
* 5. videó: [a többi személy munkájának másolása az adatelemzéshez](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 mp)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Átirat: készen áll az adatelemzésre?
Üdvözli a "készen áll az adatelemzésre?" az adatsorozat-adatelemzés második videója *kezdőknek*.  

Ahhoz, hogy az adatelemzés meg tudja adni a kívánt válaszokat, meg kell adnia néhány kiváló minőségű nyersanyagot a használatához. Ugyanúgy, mint egy pizzát, annál jobbnak kell lennie a kezdő összetevőknek, annál jobb a végtermék. 

## <a name="criteria-for-data"></a>Az adatértékek feltételei
Az adatelemzésben vannak olyan összetevők, amelyeket össze kell húzni, beleértve a következőket:

* Vonatkozó
* Csatlakozva
* Pontos
* Ahhoz, hogy működjön a

## <a name="is-your-data-relevant"></a>Relevánsak az adatai?
Az első összetevő tehát a megfelelő adatokat kell használnia.

![Releváns adatokat és irreleváns adatokat – kiértékelési adatokat](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

A bal oldalon a táblázat a bostoni sávon kívül tesztelt hét személy, az utolsó játék Red Sox szemrebbenés átlagát, a legközelebbi kényelmi tárolóban pedig a tej árát mutatja be.

Ez minden teljesen megbízható. Csak az a hiba, hogy nem releváns. A számok között nincs egyértelmű kapcsolat. Ha valaki a tej aktuális árát és a Red Sox szemrebbenés átlagát adta meg, akkor nem lehet kitalálni a véralkohol tartalmát.

Most tekintse meg a jobb oldalon található táblázatot. Ezúttal az egyes személyek testtömegének mérését, valamint az azokhoz tartozó italok számát kell megmérni.  Az egyes sorokban szereplő számok mostantól egymásra is érvényesek. Ha adtam a testem tömegét, és megadta a Margaritas számát, kitalálhatja a véralkoholom tartalmát.

## <a name="do-you-have-connected-data"></a>Van csatlakoztatva az adatkapcsolat?
A következő összetevő a csatlakoztatott adatkapcsolattal rendelkezik.

![Csatlakoztatott adatkapcsolatok és leválasztott adatkapcsolatok – adatfeltételek, kész adatfeldolgozás](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Itt láthatók a hamburgerek minőségével kapcsolatos fontos adatokat: a grill hőmérséklete, a Patty Weight és a minősítés a helyi élelmiszer-magazinban. De figyelje meg a bal oldali táblázatban lévő hézagokat.

Az adatkészletek többsége hiányzik néhány értékből. Gyakori, hogy a lyukak hasonlóak, és többféleképpen is megkerülheti őket. Ha azonban túl sok hiányzik, az adatai megkezdik a svájci sajtok megjelenését.

Ha a bal oldalon megtekinti a táblázatot, elég sok a hiányzó adatmennyiség, mert a grill hőmérséklete és a Patty súlyozása között bármilyen kapcsolat áll fenn. Ez a példa leválasztott adatkapcsolatot mutat be.

A jobb oldali tábla azonban teljes és teljes – példa a csatlakoztatott adatra.

## <a name="is-your-data-accurate"></a>Pontosak az adatai?
A következő összetevő pontossággal rendelkezik. Íme négy cél a találatok között.

![Pontos adatelemzési és pontatlan adatértékek](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

A jobb felső sarokban tekintse meg a célt. A bikák szeme szoros csoportosításban van. Ez természetesen pontos. Az adatelemzési nyelvben a cél közvetlenül az adattudományi nyelven is pontosnak számít.

Ha leképezte a nyilak középpontját, láthatja, hogy nagyon közel van a bikák szeméhez. A nyilak a cél körül oszlanak el, így pontatlannak számítanak, de a Bulls szem körül vannak, így pontosnak számítanak.

Most tekintse meg a bal felső célt. Itt a nyilak nagyon közel vannak egymáshoz, és egy szoros csoportosítás. Pontosak, de pontatlanok, mert a központ a bikák szemének kifelé mutat. A bal alsó cél nyilai pontatlanok és nem pontosak. Ennek az íjásznak további gyakorlatra van szüksége.

## <a name="do-you-have-enough-data-to-work-with"></a>Elegendő adattal rendelkezik a működéséhez?
Végül az összetevők #4 elegendő mennyiségű adattal rendelkeznek.

![Van elegendő adatai az elemzéshez? Az adatértékelés](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Úgy gondolja, hogy a tábla minden adatpontja ecseti ecsetvonásként szerepel a festészetben. Ha csak néhány közülük van, a festés zavaros lehet – nehéz megállapítani, hogy mi ez.

Ha több ecsetvonást ad hozzá, akkor a festés egy kicsit élesebben fog megjelenni.

Ha alig elég ecsetvonása van, csak annyit kell látnia, hogy elvégezze a szélesebb körű döntéseket. Valahol érdemes meglátogatni? Úgy néz ki, mint a tiszta víz – igen, ez az a hely, ahol nyaralni fogok.

További információk hozzáadásakor a kép világosabb lesz, és részletesebb döntéseket hozhat. Most megtekintheti a három hotelt a bal oldali banknál. Láthatja az előtérben található egyik építészeti funkcióját. Dönthet úgy is, hogy a nézet miatt a harmadik emeleten marad.

A releváns, a csatlakoztatott, a pontos és a megfelelő adatokat tartalmazó összetevők mindegyike rendelkezik a kiváló minőségű adatelemzéshez szükséges összes összetevővel.

Tekintse meg a további négy videót az *adatelemzésben* a Microsoft Azure Machine learning Studio (klasszikus) kezdőknek.

## <a name="next-steps"></a>Következő lépések
* [Próbálja ki az első adatelemzési kísérletet Machine Learning Studio (klasszikus)](create-experiment.md)
* [Bevezetés a Machine Learning on Microsoft Azure](../overview-what-is-azure-ml.md)