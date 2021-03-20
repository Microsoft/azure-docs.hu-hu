---
title: 'ML Studio (klasszikus): adatelemzés kezdőknek – Azure'
description: Az adatelemzés kezdőknek 5 rövid videóban alapfogalmakat tanítunk, és az adatelemzés 5 kérdésével kezdődik. Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: e9bbadc95be3479da82d91e9a4a4f712fbc32c57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520391"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Adatelemzés kezdőknek – 1. videó: 5 kérdés, amelyre az adatelemzés választ ad

Gyors bevezetést nyerhet az adatelemzésből az adatelemzésből a *kezdők számára* öt rövid videóban egy legfontosabb adattudóstól. Ezek a videók alapszintű, de hasznosak, függetlenül attól, hogy az adatelemzést vagy az adatszakértőkkel való munkát érdekli.

Ez az első videó arról szól, hogy milyen típusú kérdésekre tud válaszolni az adatelemzés. Ha ki szeretné próbálni a sorozatot, tekintse meg őket. [Ugrás a videók listájára](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>További videók ebben a sorozatban
Az *adatelemzés kezdőknek* gyors bevezetést biztosítanak az adatelemzésből, így összesen 25 percet vesz igénybe. Tekintse meg az öt videót:

* 1. videó: az adatelemzés 5 kérdésre adott válaszok
* 2. videó: [készen áll](data-science-for-beginners-is-your-data-ready-for-data-science.md) az adatelemzésre? *(4 perc 56 mp)*
* 3. videó: [Kérdezzen rá az adatválaszra](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 perc 17 mp)*
* 4. videó: [Válasz Megjósolása egy egyszerű modellel](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 perc 42 mp)*
* 5. videó: [a többi személy munkájának másolása az adatelemzéshez](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 mp)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Átirat: az adatelemzés 5 kérdésre adott válasz
Hello! Köszönti a *kezdőknek szóló* Video Series-adatelemzés.

Az adatelemzés megfélemlítő lehet, ezért az alapismereteket itt mutatjuk be egyenletek vagy számítógépes programozási zsargon nélkül.

Ebben az első videóban az "5 kérdés adatelemzési válasz" kifejezésre fogunk beszélni.

Az adatelemzés számokat és neveket (más néven kategóriákat vagy címkéket) használ a kérdésekre adott válaszok előrejelzéséhez.

Lehet, hogy meglepő, de az *adattudományi válaszok közül csak öt kérdés*:

* Ez A vagy B?
* Ez fura?
* Mennyit – vagy – hány?
* Hogyan történik ez a rendszerezés?
* Mi a teendő ezután?

A kérdések mindegyikét egy különálló gépi tanulási módszer, az algoritmusok nevezik.

Hasznos lehet olyan algoritmust meggondolni, amely egy Receptként és az Ön által használt összetevőkből áll. Egy algoritmus azt mutatja be, hogyan egyesítheti és keverheti össze az adattípusokat, hogy választ kapjon. A számítógépek olyanok, mint a turmixgép. Az algoritmus nagy része az Ön számára, és nagyon gyorsan elvégezhető.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>1. kérdés: ez A vagy B? besorolási algoritmusokat használ
Kezdjük a kérdést: ez A vagy B?

![Besorolási algoritmusok: ez A vagy B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Az algoritmusok ezt a családját kétosztályos besorolásnak nevezzük.

Minden olyan kérdés esetében hasznos, amely csak két lehetséges választ tartalmaz.

Például:

* A gumiabroncs sikertelen lesz a következő 1 000 mérföldben: Igen vagy nem?
* Mely több ügyfelet tartalmaz: egy $5 kupont vagy 25%-os kedvezményt?

Ezt a kérdést úgy is megteheti, hogy több mint két lehetőséget is tartalmaz: ez A vagy B vagy C vagy D, stb.?  Ezt nevezzük többosztályos besorolásnak, és akkor hasznos, ha több – vagy több ezer – lehetséges válasz van. A többosztályos besorolás a legvalószínűbbet választja.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>2. kérdés: ez fura? anomália-észlelési algoritmusokat használ
A következő kérdés adattudománya választ kaphat: ez fura? Ezt a kérdést a rendellenesség észlelése nevű algoritmusok egyike válaszolja meg.

![Rendellenesség-észlelési algoritmusok: ez fura?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Ha rendelkezik bankkártyával, már előnyben részesült a anomáliák észlelése során. A bankkártya vállalata elemzi a vásárlási mintákat, így riasztást kaphat a lehetséges csalásról. A "fura" díjat olyan áruházban lehet vásárolni, amely nem általában a szokásos áron vásárolja meg és nem vásárolja meg.

Ez a kérdés számos módon hasznos lehet. Ilyenek például a következők:

* Ha van egy, a nyomásmérővel rendelkező autó, érdemes tudnia: Ez a nyomásmérő a normál módon olvas?
* Ha az internetet figyeli, érdemes tudnia, hogy ez az üzenet jellemző az internetről?

Nem várt vagy szokatlan eseményeket vagy viselkedést jelző anomáliák észlelése. Megadja a problémák keresésének helyét.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>3. kérdés: mennyit? vagy hány? regressziós algoritmusokat használ
A gépi tanulás is megjósolhatja a választ, hogy mennyit? vagy hány? Az ezt a kérdést megválaszoló algoritmus családjának neve regresszió.

![Regressziós algoritmusok: mennyit? vagy hány?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

A regressziós algoritmusok numerikus előrejelzéseket tesznek elérhetővé, például:

* Milyen hőmérséklet lesz a jövő kedden?  
* Mi lesz a negyedik negyedéves értékesítés?

Segítenek bármilyen olyan kérdés megválaszolásában, amely egy számot kér.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>4. kérdés: Hogyan történik ez a rendszerezés? fürtözési algoritmusokat használ
Most az utolsó két kérdés egy kicsit fejlettebb.

Néha szeretné megismerni az adathalmazok szerkezetét – hogyan történik ez a rendszerezés? Ebben a kérdésben nem rendelkezik olyan példákkal, amelyekkel már ismeri a eredményeit.

Az adatszerkezetnek számos módja van. Az egyik módszer a fürtözés. A könnyebb értelmezés érdekében elkülöníti az adathalmazokat természetes "csoportokba". A fürtözéssel kapcsolatban nincs egyetlen helyes válasz.

![Csoportosítási algoritmusok: Hogyan történik ez a rendezés?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

A fürtözési kérdések gyakori példái a következők:

* Mely megjelenítők hasonlóan azonos típusú filmek?
* Mely nyomtatási modellek nem egyeznek meg a módszerrel?

Az adatszervezés módjának megismerésével jobban megismerheti és megjósolhatja a viselkedést és az eseményeket.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>5. kérdés: mit tegyek most? megerősítő tanulási algoritmusokat használ
Az utolsó kérdés – mi a teendő? – a megerősítő tanulás nevű családot használja.

A megerősítő tanulást azzal inspirálta, hogy a patkányok és az emberek agyai hogyan reagálnak a büntetésre és a juttatásokra. Ezek az algoritmusok az eredményekből tanulnak, és döntenek a következő műveletről.

A megerősítő tanulás általában jól illeszkedik az olyan automatizált rendszerekhez, amelyeknek emberi útmutatás nélkül kell meghozniuk a sok kis döntést.

![Megerősítő tanulási algoritmusok: mit tegyek tovább?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

A választ tartalmazó kérdések mindig arról szólnak, hogy milyen műveleteket kell végrehajtani – általában egy géppel vagy egy robottal. Példák:

* Ha hőmérséklet-vezérlési rendszer van egy ház számára: állítsa be a hőmérsékletet, vagy hagyja ott, ahol van?  
* Ha önkiszolgáló autót használok: sárga fénnyel, fék vagy felgyorsítva?  
* Robot vákuum esetén: tartsa a vákuumot, vagy térjen vissza a töltési állomáshoz?

A megerősítő tanulási algoritmusok adatokat gyűjtenek, a próbaverziótól és a hibáktól tanulva.

Tehát az az 5 kérdés, hogy az adatelemzés válaszolni tud.

## <a name="next-steps"></a>Következő lépések
* [Próbálja ki az első adatelemzési kísérletet Machine Learning Studio (klasszikus)](create-experiment.md)
* [Bevezetés a Machine Learning on Microsoft Azure](../overview-what-is-azure-ml.md)