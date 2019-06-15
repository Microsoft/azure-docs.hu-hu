---
title: Algoritmusok kiválasztása
titleSuffix: Azure Machine Learning Studio
description: Hogyan lehet felügyelt és nem felügyelt tanítás Learning az Azure Machine Learning Studio algoritmusok kiválasztása a fürtözés, a besorolási vagy regressziós kísérletekben.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: 3bb88f2f9546ec25433061a0704bd144730bd34c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60752884"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio"></a>Az Azure Machine Learning Studióban az algoritmusok kiválasztása

A "Milyen gépi tanulási algoritmus kell használnom?" kérdésre adott válasz mindig kapcsolva "Attól függ." Ez a méret, minőségi és az adatok természetétől függ. Ez attól függ, hogy mit szeretne tenni a választ az. Attól függ, hogyan helyett a számításokat, az algoritmus használ a számítógép utasításokat fordítja volt. Attól függ, és hogy mennyi idő alatt az rendelkezésre áll. Akár a legnagyobb tapasztalt adatszakértők nejde zjistit algoritmus ajánlott hajtja végre őket kísérlet előtt.

A Machine Learning Studio a legkorszerűbb algoritmusokat biztosítja, többek között például a továbbfejlesztett méretezhető döntési fákat, a Bayes ajánlási rendszereket, a neurális hálózatokat és a Microsoft Research által fejlesztett Decision Jungle algoritmust. Az olyan méretezhető, nyílt forráskódú gépi tanulási csomagokat, mint a Vowpal Wabbit, szintén támogatja a Machine Learning Studio. A Machine Learning Studio támogatja a multiclass és bináris osztályzásra, a regresszióra és a fürtszolgáltatásra használt gépi tanulási algoritmusokat. A teljes listáját lásd: [Machine Learning-modulok](/azure/machine-learning/studio-module-reference/index).
A dokumentáció tartalmaz némi információt minden algoritmust és hangolása paramétereket az algoritmus optimalizált használata érdekében.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>A Machine Learning algoritmus – Adatlap

A **[a Microsoft Azure Machine Learning Studio algoritmus Adatlap lap](algorithm-cheat-sheet.md)** úgy dönt, hogy a jobb segít gépi tanulási algoritmus számára az Azure Machine Learning a prediktív elemzési megoldások Studio algoritmusok könyvtára.
Ez a cikk végigvezeti a Adatlap használata.

> [!NOTE]
> A Adatlap letöltése, és kövesse az ebben a cikkben együtt, keresse fel a [gépi tanulási algoritmus-adatlap a Microsoft Azure Machine Learning Studióban](algorithm-cheat-sheet.md).
> 
> 

Ez Adatlap rendelkezik egy nagyon adott célközönséget szem: elején értenie az adatokhoz az undergraduate szintű machine learning algoritmus a kezdéshez válasszon az Azure Machine Learning Studióban próbál. Ez azt jelenti, hogy lehetővé teszi bizonyos általánosítást és oversimplifications, de biztonságos irányban mutat be. Azt is jelenti, hogy nincsenek-e nagy mennyiségű algoritmusok nem szerepel itt.

Ezek az ajánlások számos az adatszakértők és a machine learning szakértői tippeket és lefordított visszajelzés. Minden nem fogadtuk el, de alakítottuk ki összehangolni a vélemények be egy durva konszenzus. Legtöbb olyan utasítás érjen véget kezdődik ", amelytől..."

### <a name="how-to-use-the-cheat-sheet"></a>Hogyan használható a – Adatlap

Olvassa el a diagram elérési útja és algoritmus címkéinek "a  *&lt;görbe felirata&gt;* , használjon  *&lt;algoritmus&gt;* ." Például "a *sebesség*, használjon *két osztály logisztikai regressziós*." Néha több ág vonatkozik.
Néha egyikük sem tökéletes választás. Arra szolgálnak, hogy a szabály az USB javaslatok, így nem kell aggódnia, hogy pontos lehet.
Beszéltünk az említett, amelyek több az adatszakértők a csak arról a legjobb algoritmus található módja kipróbálni az összes őket.

Íme egy példa a a [Azure AI-katalógusban](https://gallery.azure.ai/) kísérletet, amelyek az ugyanazon adatokra irányuló számos algoritmust megpróbál, és összehasonlítja az eredmények: [Hasonlítsa össze a többcsoportos besorolások: Elismerési betűs](https://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92).

> [!TIP]
> Machine learning alapjait kapcsolatos gyakori machine learning kérdésre népszerű algoritmusok könnyen érthető szemléltető ábra áttekintést tölthető le: [Machine learning alapjai algoritmuspéldákkal](basics-infographic-with-algorithm-examples.md).

## <a name="flavors-of-machine-learning"></a>A gépi tanulás változatban érhetők el

### <a name="supervised"></a>Csak felügyelt eszköz

Felügyelt tanítás algoritmusok előrejelzéseket példák egy készlete alapján. Például a tőzsdei árakat, hogy a későbbi díjak kapcsolatos Találgatások használható. Minden egyes képzéshez használt példa a lényeges értékkel feliratú – ebben az esetben a tőzsdei árfolyam. Felügyelt tanulási algoritmus úgy tűnik, az adott érték címkék mintákat. Képes használni minden olyan információ, amelyek hasznosak lehetnek a – a hét, az évszak, a vállalati pénzügyi adatok, iparági típusát, jelenlétét geopolitikai azokat a káros eseményeket, a nap – és minden egyes algoritmus úgy tűnik, a különböző típusú mintákat. Az algoritmus talált a legjobb minta után képes kezelni, a minta segítségével címke nélküli tesztelési adatok előrejelzéseket – feldolgozásaiba árak.

Felügyelt tanítás olyan népszerű és hasznos a gépi tanulás. Egy kivétellel az Azure Machine Learning Studióban modult felügyelete tanulási algoritmus. Több meghatározott típusú felügyelt tanítás, amelyek szerepelnek az Azure Machine Learning Studióban található: a besorolás, regressziós és rendellenességek észlelése.

* **Besorolási**. Az adatok előre jelezni egy kategóriát vannak használatban, amikor felügyelt tanítás rövidítése besorolást. Ez a helyzet kép "cat" vagy "kutya" képként való hozzárendelésekor. Ha csak két választási lehetőség, nevezzük **két osztályú** vagy **binomiális besorolási**. Ha további kategóriák amikor az NCAA megjósolása győztesét előrejelzésére, ez a probléma néven **többcsoportos besorolási**.
* **Regresszió**. Egy érték van folyamatban előre jelzett, mint a tőzsdei árfolyamok, felügyelt tanítás regressziós neve.
* **Anomáliadetektálás**. Néha a cél, hogy egyszerűen szokatlan adatokat pontok azonosításához. Az adathamisítások felderítése például bármilyen rendkívüli hitelkártya költségkeret minták gyanúsak. A lehetséges változata ezért számos és a képzési példák, ezért néhány, hogy nem található megtudhatja, milyen rosszindulatú tevékenység néznek ki. A módszer, amely a rendellenességek észlelése, hogy egyszerűen megtudhatja, milyen szokásos tevékenység néz ki (nem csalárd tranzakciókat előzményeit használva), és mindent, ami jelentősen eltérő azonosításához.

### <a name="unsupervised"></a>Felügyeletlen

Felügyeletlen learning, az adatpontok nem tartalmaz feliratokat társítva. Ehelyett egy felügyeletlen tanulási algoritmus célja valamilyen módon adatok rendezése, vagy a szerkezetét írja le. Ez azt létre, fürtök vagy megtalálni a különböző módszereket, az összetett megnézzük, így egyszerűbb vagy több szervezett jelenik.

### <a name="reinforcement-learning"></a>Megerősítő tanulás

A megerősítő tanulást az algoritmus lesz az egyes válaszul művelet kiválasztását. A tanulási algoritmus is fogad ellenszolgáltatás jel egy rövid ideig később, hogyan jó döntés lett megjelölve.
Ennek alapján, az algoritmus módosítása igazítani a stratégiáját a legmagasabb ellenszolgáltatás növelhető. Jelenleg nincsenek nincs megerősítő tanulási algoritmus modulok az Azure Machine Learning Studióban. Megerősítő tanulást szokás robotika, ahol egy adatpont érzékelőinek adott ponton időben lesz, és az algoritmus ki kell választania a robot a következő művelet. Emellett akkor is, természetes választás olyan Internet of Things alkalmazásokat.

## <a name="considerations-when-choosing-an-algorithm"></a>Egy algoritmus kiválasztásakor megfontolandó szempontok

### <a name="accuracy"></a>pontosság

Bevezetés a legpontosabb válasz lehetséges nem mindig szükséges.
Néha közelítés verziója megfelelő, attól függően szeretné használni. Ha ebben az esetben lehet kivágni a feldolgozási idő jelentősen több hozzávetőleges módszerekkel színerősséget. További hozzávetőleges módszerek egy másik előnye, hogy természetes módon általában overfitting elkerülése érdekében.

### <a name="training-time"></a>Képzési idő

Perc vagy a modell betanításához szükséges órák száma jelentős algoritmusok közé esik. Képzési idő gyakran szorosan vannak kötve, pontosságának – egy általában társul, a másik. Emellett egyes algoritmusok érzékenyebbek a adatpontok számát, mint mások.
Korlátozott idő esetén különösen akkor, ha a készlet mérete nagy, is kiterjedtebb algoritmus kiválasztása.

### <a name="linearity"></a>Lineáris

Győződjön meg arról, a gépi tanulási algoritmusok rengeteg lineáris használatát. Lineáris besorolás az algoritmusok azt feltételezik, hogy osztályokat lehet tagolni egyenes (vagy annak újabb dimenzionális analóg). Ezek közé tartozik a logisztikai regressziós, és támogatják a vektoros gépek (implementálva az Azure Machine Learning Studióban).
Lineáris regresszió algoritmus azt feltételezik, hogy az adattrendek kövesse egyenes. Ilyen Előfeltevések nem hibás bizonyos problémák, de a mások azok leállásához pontosságát.

![A határ nem lineáris osztályban](./media/algorithm-choice/image1.png)

***Az osztály nem lineáris határ*** *-egy lineáris osztályozó algoritmus támaszkodva eredményezne alacsony pontossága*

![Adatok nem lineáris trendek](./media/algorithm-choice/image2.png)

***Adatok nem lineáris trendek*** *– egy lineáris regressziós módszerrel szeretné készítése a szükségesnél sokkal nagyobb hibák*

Annak ellenére, hogy a veszélyek lineáris algoritmusok támadás első vonal rendkívül népszerű. Ezek általában algorithmically gyorsan és egyszerűen betanításához.

### <a name="number-of-parameters"></a>A paraméterek száma

Paraméterek a következők: adatszakértő lekérdezi állítson be egy olyan algoritmust kapcsolja be a belül Azok, amelyek befolyásolják az algoritmus viselkedését, például a hibatűrés vagy ismétlések vagy a lehetőségek között a algoritmus működését változatának száma számok. A képzési időt és az algoritmus pontosságát néha lehet teljesen bizalmas, csak a megfelelő beállítások beolvasása. Nagy számú paraméterek algoritmusok általában a legtöbb próbaverzió és hiba található egy jó kombináció szükséges.

Másik lehetőségként van egy [paraméter kezdik](algorithm-parameters-optimize.md) az Azure Machine Learning Studióban, amely automatikusan megpróbálja az összes paraméterkombinációk bármilyen granularitási választja, a modul letiltása. Bár ez kiválóan alkalmas, győződjön meg arról, hogy felölelt paraméter terület, a modell betanításához szükséges idő egyenes arányban növekszik exponenciálisan paraméterek száma.

A feje, hogy ha sok paraméter általában azt jelzi, hogy egy algoritmussal rendelkezik-e nagyobb rugalmasságot biztosít. Nagyon jó pontossága, gyakran érheti el a megadott paraméter-beállításainak kombinációjával talál.

### <a name="number-of-features"></a>Szolgáltatások száma

Az egyes adattípusok, szolgáltatások száma lehet nagyon nagy méretű adatpontok száma képest. Ez gyakran a helyzet a genetikai vagy a szöveges adatokat. A számos mintavételezésének le néhány tanulási algoritmusokat, így képzési unfeasibly hosszú idő. Támogatási vektor gépek kiválóan alkalmasak a ebben az esetben (lásd alább).

### <a name="special-cases"></a>Bizonyos esetekben

Néhány tanulási algoritmusok győződjön meg az adatok vagy a kívánt eredmények szerkezete adott feltételezéseket. Ha talál egyet, amely a legjobban az igényeinek, témakörébe több hasznos eredmények, a pontos előrejelzések vagy gyorsabb képzést.

| **Algoritmus** | **pontosság** | **Képzési idő** | **Lineáris** | **Paraméterek** | **Megjegyzések** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Két osztályú osztályozás** | | | | | |
| [Logisztikai regresszió](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) | |● |● |5 | |
| [döntési erdő](/azure/machine-learning/studio-module-reference/two-class-decision-forest) |● |○ | |6 | |
| [döntési Dzsungel](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) |● |○ | |6 |Kevés a memória által elfoglalt tárterület |
| [gyorsított döntési fa](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree) |● |○ | |6 |Nagy a memóriaigénye |
| [Neurális hálózat](/azure/machine-learning/studio-module-reference/two-class-neural-network) |● | | |9 |[Lehetőség további testreszabási](azure-ml-netsharp-reference-guide.md) |
| [átlagolt perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) |○ |○ |● |4 | |
| [tartóvektor-gép](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) | |○ |● |5 |Megfelelő választás a nagyméretű szolgáltatások |
| [helyileg részletes tartóvektor-gép](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) |○ | | |8 |Megfelelő választás a nagyméretű szolgáltatások |
| [Bayes' pontozó gépet](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine) | |○ |● |3 | |
| **Többcsoportos besorolás** | | | | | |
| [Logisztikai regresszió](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) | |● |● |5 | |
| [döntési erdő](/azure/machine-learning/studio-module-reference/multiclass-decision-forest) |● |○ | |6 | |
| [döntési Dzsungel](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle) |● |○ | |6 |Kevés a memória által elfoglalt tárterület |
| [Neurális hálózat](/azure/machine-learning/studio-module-reference/multiclass-neural-network) |● | | |9 |[Lehetőség további testreszabási](azure-ml-netsharp-reference-guide.md) |
| [one-v-all](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass) |- |- |- |- |Megtekintheti a tulajdonságait, a két osztályú módszer van kiválasztva |
| **Regression** | | | | | |
| [linear](/azure/machine-learning/studio-module-reference/linear-regression) | |● |● |4 | |
| [Lineáris Bayes](/azure/machine-learning/studio-module-reference/bayesian-linear-regression) | |○ |● |2 | |
| [döntési erdő](/azure/machine-learning/studio-module-reference/decision-forest-regression) |● |○ | |6 | |
| [gyorsított döntési fa](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) |● |○ | |5 |Nagy a memóriaigénye |
| [gyors erdő ki osztóérték](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) |● |○ | |9 |Pont előrejelzéseket helyett disztribúciók |
| [Neurális hálózat](/azure/machine-learning/studio-module-reference/neural-network-regression) |● | | |9 |[Lehetőség további testreszabási](azure-ml-netsharp-reference-guide.md) |
| [Poisson](/azure/machine-learning/studio-module-reference/poisson-regression) | | |● |5 |Technikailag log lineáris. A számok előrejelzésére |
| [Sorszám](/azure/machine-learning/studio-module-reference/ordinal-regression) | | | |0 |A rang rendezése |
| **Anomáliadetektálás** | | | | | |
| [tartóvektor-gép](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) |○ |○ | |2 |Nagyméretű szolgáltatások számára különösen hasznos |
| [PCA-alapú anomáliadetektálás](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) | |○ |● |3 | |
| [K-means](/azure/machine-learning/studio-module-reference/k-means-clustering) | |○ |● |4 |Egy csoportosítási algoritmus |

**Algoritmus tulajdonságai:**

**●** -kiváló pontosságot, gyors képzést idők és lineáris használatát mutatja

**○** -jeleníti meg a helyes pontosság és mérsékelt képzési alkalommal

## <a name="algorithm-notes"></a>Algoritmus megjegyzések

### <a name="linear-regression"></a>Lineáris regresszió

Ahogy korábban említettük [lineáris regressziós](/azure/machine-learning/studio-module-reference/linear-regression) vonal (vagy adatsík vagy hyperplane) megfelel az adatkészletben. Egy workhorse, egyszerű és gyors, de elképzelhető, hogy a túlságosan ellenkezőleg kapcsolatos problémákat.

![Lineáris trendek adatok](./media/algorithm-choice/image3.png)

***Lineáris trendek adatok***

### <a name="logistic-regression"></a>Logisztikai regresszió

Bár a neve "regressziós" tartalmazza, logisztikai regressziós ténylegesen a hatékony eszközzel [két osztályú](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) és [osztályú osztályozási műveletet](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) besorolás. Fontos, hogy gyors és egyszerű. A tény, hogy használ-a "-helyett egy egyenes vonallal átalakított görbe lehetővé teszi adatok megosztásának csoportokba természetesen illeszkednek. Logisztikai regressziós biztosít lineáris osztály határok, így használva, győződjön meg arról, lineáris közelítés valami, te is.

![Kétosztályos adatokhoz csak egy szolgáltatás logisztikai regresszió](./media/algorithm-choice/image4.png)

***Kétosztályos adatokhoz csak egy szolgáltatás egy logisztikai regressziós*** *-az osztály határ az a pont, amelyen logisztikai csak közelebb mindkét osztályok*

### <a name="trees-forests-and-jungles"></a>Jungle algoritmust, fák és erdők

Döntési erdő ([regressziós](/azure/machine-learning/studio-module-reference/decision-forest-regression), [két osztályú](/azure/machine-learning/studio-module-reference/two-class-decision-forest), és [osztályú](/azure/machine-learning/studio-module-reference/multiclass-decision-forest)), decision jungle algoritmust ([két osztályú](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) és [ osztályú](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle)), és a súlyozott döntési fák ([regressziós](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) és [két osztályú](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree)) alapulnak döntési fákat, a eligazodást machine learning-fogalom. Döntési fák számos változata, de mindegyikük járjon el ugyanígy – a szolgáltatás terület feloszthatja olyan régiókban, főleg a címkét. Ezek lehetnek régiók konzisztens kategória vagy állandó érték, attól függően, hogy osztályozási vagy regressziós végez.

![Döntési fa alterületét adható meg a szolgáltatás](./media/algorithm-choice/image5.png)

***A döntési fa funkció szóközzel bázisterületeit régiókba nagyjából egységes értékek***

Szolgáltatás szóközzel önkényesen kis régiók osztható meg, mert könnyedék tegyük fel, hogy elég osztani egy adatpontot kell régiónként. Ez a overfitting szélsőséges példát. Ennek elkerülése érdekében rengeteg fák kialakítani, matematikai gondosan biztosítják a fák algoritmus nem közötti kapcsolatot. A "döntési erdő" átlagát egy fa, amelyek kiküszöbölik az overfitting. Döntési erdő sok memóriát használhat. Decision jungle algoritmust, amely rovására képzési valamivel hosszabb ideig kevesebb memóriát használ fel variant.

Gyorsított döntési fák algoritmus overfitting tovább lehet futtatásainak számát, és hogyan néhány adatpontot az egyes régiókban engedélyezett korlátozásával elkerülése érdekében. Az algoritmus fák, sorozata, amelyeken a hiba előtt a fa által hátrahagyott kompenzálják megtanulja hoz létre. Ez egy nagyon pontos learner, amely általában sok memóriát használja. Teljes műszaki leírásáért tekintse meg [Friedman az eredeti tanulmány](https://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Erdő ki osztóérték regressziós gyors](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) a döntési fák az különleges esetet, ahol szeretné tudni, hogy nem csak a tipikus (közepes) értéket a Data-régió, hanem quantiles formájában a terjesztési egy változata.

### <a name="neural-networks-and-perceptrons"></a>Neurális hálózatokat és perceptrons

Neurális hálózatok vannak agy ihlette tanulási algoritmus kiterjedő [osztályú](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [két osztályú](/azure/machine-learning/studio-module-reference/two-class-neural-network), és [regressziós](/azure/machine-learning/studio-module-reference/neural-network-regression) problémákat. Egy végtelen számos különböző származnak, de a Neurális hálózatokat az Azure Machine Learning Studio belüli összes irányított aciklikus diagramjait formájában. Ez azt jelenti, hogy bemeneti funkciói továbbítja a rendszer előre (soha nem visszafelé) rétegek sorozatát előtt be kimenetek kapcsolva. Minden egyes rétegben bemenetei között meg különböző kombinációival súlyozott, összegzi, és a következő réteg pedig továbbítva. Ez a kombinációja egyszerű számításokat Magic Quadrant által bővítése lezárulhat ismerje meg a kifinomult osztály határokat és az adatok trendeket, lehetővé teszi eredményez. Az ilyen jellegű, több rétegből hálózatok hajtsa végre a "deep learning", amely ad lendületet az ennél jóval tech jelentéskészítési és sci-fi irodalmat.

A nagy teljesítményű nem adják ingyen, azonban. Neurális hálózatokkal betanítást, különösen a nagy méretű adatkészleteket a funkciók számos hosszú időt is igénybe vehet. Is rendelkeznek, mint a legtöbb algoritmusok, ami azt jelenti, hogy a paraméter kezdik jelentős bontja ki a betanítási idő további paramétereket.
És ezeket overachievers szeretnének [adja meg a saját hálózati struktúra](azure-ml-netsharp-reference-guide.md), az lehetőségeket inexhaustible.

![Határok megtanult a Neurális hálózatokkal](./media/algorithm-choice/image6.png)

***A határok Neurális hálózatok megtanult összetett és szabálytalan lehet.***

A [két osztályú átlagolt perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) Neurális hálózatokat választ skyrocketing képzési többször van. A hálózati struktúra, amely lineáris osztály határokat használja. Napjaink szabványok által szinte egyszerű, de már hosszú ideje hatékonyabban dolgozhatók használatát, és általában elég kis méretűek további gyorsan.

### <a name="svms"></a>SVMs

Támogatási vektor gépek (SVMs) található, amely elválasztja az osztályok által számos olyan margó, amennyire csak lehetséges, a határ. Ha nem tudja egyértelműen elkülönüljenek a két osztály, az algoritmusokat a legjobb határt, a következőkre találja meg. Ahogy az az Azure Machine Learning Studio szerepel a [két osztályú SVM](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) azért teszi ezt csak egy egyenes vonallal (SVM beszél, használ egy lineáris kernel).
Mivel az lehetővé teszi, a lineáris közelítés, viszonylag gyorsan futtatható. A szolgáltatás-intenzív adatok, például szöveg vagy részfeladatának adat van, akkor válik igazán egyértelművé. Ezekben az esetekben SVMs tudnak külön osztályok, gyorsabban és kevesebb, mint a legtöbb más algoritmusok mellett csak egy szerény mennyiségű memóriát igénylő overfitting.

![Támogatási vector machine osztály határ](./media/algorithm-choice/image7.png)

***Egy tipikus támogatási vector machine osztály határ a lehető legnagyobbra növeli a két osztály szétválasztása margó***

A Microsoft Research, egy másik szorzatát a [két osztályú helyileg részletes SVM](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) egy nem lineáris változata, amely megtartja a lineáris verzió sebessége és a memória hatékonyságát a legtöbb SVM az. Ideális esetben, ahol a lineáris módszert elég pontos válaszok nem ad. A fejlesztők számára gyors bontásához, a problémát, és kis lineáris SVM problémák által vezetett azt. Olvassa el a [teljes leírás](http://proceedings.mlr.press/v28/jose13.html) hogyan azok lekért ki a kört részleteiért.

Egy nem lineáris SVMs ötletes bővítményének használatával a [egy szintű SVM](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) megrajzolja egy határt tartalmazó szorosan a teljes adathalmazon. Hasznos lehet a rendellenességek észlelése. Minden olyan új adatokat sokkal adott határon kívül eső pontok szokatlan kell megjegyezni.

### <a name="bayesian-methods"></a>Bayes módszerek

Bayes módszerek rendelkezik, célszerű lenne minőségi: azok a overfitting elkerülése érdekében. Erre azáltal, hogy néhány feltételezéseket előre valószínűleg elosztása a választ. Egy másik byproduct erre a megközelítésre arról, hogy rendelkezik-e nagyon kevés paramétert. Az Azure Machine Learning Studio tartalmaz Bayes algoritmus mindkét besorolási ([két osztályú Bayes pontozó gépet](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine)) és regressziós ([Bayes lineáris regressziós](/azure/machine-learning/studio-module-reference/bayesian-linear-regression)).
Vegye figyelembe, hogy ezek feltételezik, hogy az adatokat fel vagy egy egyenes vonallal illeszkednek.

Egy korábbi megjegyzés, a Microsoft Research Bayes' pont gépek fejlesztett. Van néhány kivételesen, gyönyörű elméleti munkahelyi mögöttük. Az érdekelt tanulói van irányítva az [JMLR eredeti cikkében](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) és a egy [által Chris Bishop szemléletes blog](https://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Speciális algoritmusok
Ha lehet, vonulással kapcsolatos adatokat tartalmazó nagyon adott cél. Az Azure Machine Learning Studio-gyűjteményekben vannak specializáltak algoritmusokat használnak:

- előrejelzési rangsor ([regressziós](/azure/machine-learning/studio-module-reference/ordinal-regression)),
- előrejelzési száma ([Poisson regressziós](/azure/machine-learning/studio-module-reference/poisson-regression)),
- rendellenességek észlelése (egy alapján [fő összetevőit elemzési](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) és a egy alapján [vektor gépek támogatására](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine))
- Fürtszolgáltatás ([K-közép](/azure/machine-learning/studio-module-reference/k-means-clustering))

![PCA-alapú anomáliadetektálás](./media/algorithm-choice/image8.png)

***PCA-alapú anomáliadetektálás*** *– az adatok túlnyomó többsége lép át egy stereotypical terjesztési; méretektől révén rendkívül gyorsan megjelenhet a terjesztési pontok olyan gyanús*

![Adatkészlet csoportosítva K-közép használatával](./media/algorithm-choice/image9.png)

***Egy adatkészlet öt tárolófürtöknek a K-közép van csoportosítva.***

Emellett van egy ensemble [egy – v – minden multiclass osztályozó](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass), az N szintű osztályozási problémához amely bontja N-1 két osztályú osztályozási problémák. A pontosság, a képzési időt és a lineáris tulajdonságok a használt két osztályú osztályozás határozza meg.

![Harmadik szintű besorolás alkot két osztályú osztályozás](./media/algorithm-choice/image10.png)

***Két osztályú osztályozás összevonás kialakításához három szintű besorolás párjai***

Az Azure Machine Learning Studióban is biztosít hozzáférést. a cím alatt egy hatékony gépi tanulási keretrendszert [Vowpal Wabbit](/azure/machine-learning/studio-module-reference/train-vowpal-wabbit-version-7-4-model).
VW szakmai kategorizálási itt, mert további besorolási és regressziós is problémákat, és még további részlegesen címke nélküli adatokból. Konfigurálhatja úgy, hogy több tanulási algoritmusokat, adatveszteség-funkciók és algoritmusok közül bármelyik használja. Arra készült egészen az alapoktól be hatékony, párhuzamos és rendkívül gyors. Kezelési túlzóan nagy szolgáltatáskészleteket kis kétségtelenül fennáll-e a munkát.
Elindult, és a Microsoft Research saját John Langford által vezetett, VW egy képlet egy bejegyzést a készlet autó algoritmusok mezőben. Nem minden probléma VW megfelel, de az Öné használható, ha lehet, érdemes eléri a maximumot, gyorsan elsajátítható illesztőjén. Akkor is elérhető [önálló nyílt forráskóddal](https://github.com/JohnLangford/vowpal_wabbit) több nyelven is.

## <a name="next-steps"></a>További lépések

* Machine learning alapjait kapcsolatos gyakori machine learning kérdésre népszerű algoritmusok könnyen érthető szemléltető ábra áttekintést tölthető le: [Machine learning alapjai algoritmuspéldákkal](basics-infographic-with-algorithm-examples.md).

* A Machine Learning Studióban elérhető összes gépi tanulási algoritmusok, kategória szerint, lásd: [modell inicializálása](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) a Machine Learning Studio algoritmus-és modul segítségével.

* Betűrend szerinti rendezés listájáért algoritmusok és a Machine Learning Studióban modulok, lásd: [betűrend szerinti lista Machine Learning Studio-modulok](/azure/machine-learning/studio-module-reference/a-z-module-list) a Machine Learning Studio algoritmus- és modul segítségével.
