---
title: Kérdések megválaszolása – ML Studio (klasszikus) – Azure
description: Ismerje meg, hogyan alakíthat ki egy éles adatelemzési kérdést az adatelemzési szolgáltatásban a kezdőknek szóló 3. videóban. A besorolási és a regressziós kérdések összehasonlítását foglalja magában.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 2ff440801db7ba8c5bfc1785fc7a399a1d773b73
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517025"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Adatokkal megválaszolható kérdések megfogalmazása
## <a name="video-3-data-science-for-beginners-series"></a>3. videó: az adatelemzés kezdőknek sorozata
Megtudhatja, hogyan alakíthat ki adatelemzési problémát a kezdőknek szóló 3. videóban található adatelemzési kérdésben. Ez a videó a besorolási és regressziós algoritmusokkal kapcsolatos kérdések összehasonlítását tartalmazza.

Ha ki szeretné próbálni a sorozatot, tekintse meg őket. [Ugrás a videók listájára](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>További videók ebben a sorozatban
Az *adatelemzés kezdőknek* az adatelemzés öt rövid videóban való bevezetésének első lépései.

* 1. videó: [az 5 kérdés adatelemzési válasz](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 perc 14 mp)*
* 2. videó: [készen áll](data-science-for-beginners-is-your-data-ready-for-data-science.md) az adatelemzésre? *(4 perc 56 mp)*
* 3. videó: Kérdezzen rá, hogy válaszol-e az adatvédelemre
* 4. videó: [Válasz Megjósolása egy egyszerű modellel](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 perc 42 mp)*
* 5. videó: [a többi személy munkájának másolása az adatelemzéshez](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 mp)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Átirat: Kérdezzen rá, hogy válaszoljon az adatvédelemre
Üdvözöljük az adatelemzés kezdőknek című sorozat harmadik videójában.  

Ebben az esetben néhány tippet talál, amelyekkel megadhatja az adatválasztó kérdéseket.

Ebből a videóból többet is megnézhet, ha először tekinti meg a két korábbi videót ebben a sorozatban: "az adatelemzési 5 kérdés válaszol", és "az adatai készen állnak az adatelemzésre?"

## <a name="ask-a-sharp-question"></a>Tegyen fel egy éles kérdést
Beszéltünk arról, hogy az adattudomány hogyan használja fel a neveket (más néven kategóriákat vagy címkéket) és számokat a kérdéses válasz előrejelzésére. De nem lehet csak bármilyen kérdés; *éles kérdésnek* kell lennie.

Egy homályos kérdést nem kell megválaszolni egy névvel vagy számmal. Egy éles kérdésnek kell lennie.

Képzelje el, hogy talált egy mágikus lámpát egy Dzsinn-val, aki őszintén választ ad bármilyen kérdésre. De ez egy Csintalan Dzsinn, amely a válaszát olyan homályosnak és zavarosnak fogja kitenni, amennyire csak lehet. Azt szeretné, hogy egy olyan kérdéssel rögzítse őket, amely nem tud segíteni, de azt, hogy mit szeretne tudni.

Ha egy homályos kérdést szeretne feltenni, például: "mi fog történni a saját készlettel?", a Dzsinn választ kaphat, "az ár megváltozik". Ez igaz válasz, de nem nagyon hasznos.

Ha azonban egy éles kérdésre lenne szüksége, például: "mi lesz a készletem eladási ára a jövő héten?", a Dzsinn nem tud segíteni, de egy adott választ ad meg, és előrejelzést készít az eladási díjakról.

## <a name="examples-of-your-answer-target-data"></a>Példák a válaszra: célobjektum
Ha megfogalmazta a kérdését, ellenőrizze, hogy van-e példa az adataiban található válaszra.

Ha a kérdés a következő: mi lesz a készletem eladási ára a jövő héten? Ezután meg kell győződnie arról, hogy az adatkészletek tartalmazzák a tőzsdei árfolyam-előzményeket.

Ha a kérdés az, hogy a flotta melyik autója lesz az első sikertelen? Ezután meg kell győződni arról, hogy az adatok tartalmazzák a korábbi hibákra vonatkozó információkat.

![Cél adatai – példák a válaszra. Dolgozzon ki egy adatelemzési kérdést.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Ezeket a válaszokat nevezzük célként. A cél az, hogy mit próbálunk megjósolni a jövőbeli adatpontokkal kapcsolatban, legyen szó egy kategóriáról vagy egy számról.

Ha nem rendelkezik célként megadott adattal, néhányat meg kell kapnia. Nem fogja tudni megválaszolni a kérdését.

## <a name="reformulate-your-question"></a>A kérdés újrafogalmazása
Előfordulhat, hogy a kérdését újra felhasználva hasznos választ kaphat.

A kérdés az, hogy ez az adatpont vagy B? egy adott kategória (vagy név vagy címke) előrejelzése. A válaszhoz egy *besorolási algoritmust* használunk.

A kérdés "mennyit?" vagy "hány?" egy összeg előrejelzése. Válaszként *regressziós algoritmust* használunk.

Ha szeretné megtudni, hogyan alakíthatja át ezeket, nézzük meg a kérdést: "melyik Hírek a legérdekesebb az olvasó számára?" Több lehetőség közül egyetlen választási lehetőséget kér – más szóval: "ez A vagy B vagy C vagy D?" – és egy besorolási algoritmust használ.

Ez a kérdés azonban könnyebben megválaszolható, ha a következőre hivatkozik: "milyen érdekes az egyes történetek ezen a listán erre az olvasóra?" Most megadhat minden cikket numerikus pontszámként, és könnyen azonosíthatja a legmagasabb pontszámot tartalmazó cikket. Ez a besorolási kérdés újraszövegezése egy regressziós kérdésben vagy mennyit?

![Alakítsa át a kérdést. Besorolási kérdés vs. regressziós kérdés.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Az a kérdés, hogy milyen algoritmussal lehet választ adni.

Megtudhatja, hogy az algoritmusok bizonyos családja – mint például a hírek történetének példája – szorosan összefügg egymással. Átalakíthatja a kérdést, hogy a leghasznosabb választ biztosító algoritmust használja.

De a legfontosabb, hogy az éles kérdés – az a kérdés, amelyről választ kaphat az adatvédelemre. Győződjön meg arról, hogy rendelkezik a megfelelő adattal, hogy válaszoljon.

Beszéltünk az adatokkal kapcsolatos kérdések megválaszolásával kapcsolatos alapvető elvekről.

Tekintse meg a többi videót a "kezdő adatelemzés" Microsoft Azure Machine Learning Studio (klasszikus).

## <a name="next-steps"></a>Következő lépések
* [Próbálja ki az első adatelemzési kísérletet Machine Learning Studio (klasszikus)](create-experiment.md)
* [Bevezetés a Machine Learning on Microsoft Azure](../overview-what-is-azure-ml.md)