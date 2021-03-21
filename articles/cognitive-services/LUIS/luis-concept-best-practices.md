---
title: Ajánlott eljárások a LUIS-alkalmazás létrehozásához
description: Ismerje meg az ajánlott eljárásokat a LUIS-alkalmazás modelljéből származó legjobb eredmények eléréséhez.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 2f6ed85416cc5d7c3c2baba2b2cfe489e301d7e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788485"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Ajánlott eljárások a Language Understanding (LUIS) alkalmazások létrehozásához
A LUIS-alkalmazás létrehozásához használja az alkalmazás-létrehozási folyamatot:

* Nyelvi modellek (szándékok és entitások) létrehozása
* Vegyen fel néhány betanítási példát a hosszúságú kimondott szöveg (15-30/szándék)
* Közzététel a végponton
* Tesztelés végpontról

Miután [közzétette](luis-how-to-publish-app.md)az alkalmazást, a fejlesztési életciklus használatával hozzáadhat szolgáltatásokat, közzétehet és tesztelheti a végpontról. Ne kezdje el a következő szerzői ciklust további példa hosszúságú kimondott szöveg hozzáadásával, mivel ez nem teszi lehetővé, hogy a LUIS a valós felhasználói hosszúságú kimondott szöveg segítségével Ismerje meg a modellt.

Ne bontsa ki a hosszúságú kimondott szöveg, amíg a példa és a végpontok hosszúságú kimondott szöveg aktuális készlete nem ad vissza magabiztosan, nagy előrejelzési pontszámokat. Az [aktív tanulással](luis-concept-review-endpoint-utterances.md)javíthatja a pontszámokat.




## <a name="do-and-dont"></a>Ne
A következő lista a LUIS-alkalmazásokra vonatkozó ajánlott eljárásokat tartalmazza:

|Ajánlott|Nem ajánlott|
|--|--|
|[A séma megtervezése](#do-plan-your-schema)|[Létrehozás és közzététel terv nélkül](#dont-publish-too-quickly)|
|[Különböző leképezések definiálása](#do-define-distinct-intents)<br>[Funkciók hozzáadása a szándékokhoz](#do-add-features-to-intents)<br>
[Gépi megtanult entitások használata](#do-use-machine-learned-entities) |[Számos példa hosszúságú kimondott szöveg hozzáadása a leképezésekhez](#dont-add-many-example-utterances-to-intents)<br>[Néhány vagy egyszerű entitás használata](#dont-use-few-or-simple-entities) |
|[Keressen egy édes helyet a túl általános és az egyes szándékok között](#do-find-sweet-spot-for-intents)|[A LUIS használata képzési platformként](#dont-use-luis-as-a-training-platform)|
|[Az alkalmazás iteratív létrehozása verziókkal](#do-build-your-app-iteratively-with-versions)<br>[Entitások létrehozása a modell elbomlásához](#do-build-for-model-decomposition)|[Több, azonos formátumú hosszúságú kimondott szöveg hozzáadása, figyelmen kívül hagyva más formátumok](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Mintázatok hozzáadása a későbbi ismétlésekben](#do-add-patterns-in-later-iterations)|[A leképezések és entitások definíciójának összekeverése](#dont-mix-the-definition-of-intents-and-entities)|
|A [hosszúságú kimondott szöveg egyensúlyt kell kiosztania az összes](#balance-your-utterances-across-all-intents) szándékon, kivéve a none szándékot.<br>[Példa hosszúságú kimondott szöveg hozzáadása nincs szándékhoz](#do-add-example-utterances-to-none-intent)|[A kifejezések listáját minden lehetséges értékkel létrehozhatja](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Az aktív tanulásra vonatkozó javaslati funkció kihasználása](#do-leverage-the-suggest-feature-for-active-learning)|[Túl sok mintázat hozzáadása](#dont-add-many-patterns)|
|[Az alkalmazás teljesítményének figyelése batch-teszteléssel](#do-monitor-the-performance-of-your-app)|[Betanítás és közzététel minden egyes példa Kimondás esetén](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-plan-your-schema"></a>Tervezze meg a sémát

Az alkalmazás sémájának létrehozása előtt meg kell határoznia, hogy mit és hol kívánja használni az alkalmazást. Minél alaposabban és pontosabban megtervezi az alkalmazását, annál jobb lesz az alkalmazása.

* Kutatási megcélzó felhasználók
* Végpontok közötti personák meghatározása az alkalmazás hangjának, avatárjának, a probléma kezelése (proaktív, reaktív)
* Azonosítsa a felhasználói interakciókat (szöveg, beszéd), amelyek segítségével a csatornákon keresztül átadhatja a meglévő megoldásokat, vagy létrehozhat egy új megoldást ehhez az alkalmazáshoz
* Végpontok közötti felhasználói út
    * Mire számíthat az alkalmazás, és nem? * Milyen prioritásai vannak?
    * Mik a fő felhasználási esetek?
* Adatgyűjtés – az adatok gyűjtésével és előkészítésével [kapcsolatos tudnivalók](data-collection.md)

## <a name="do-define-distinct-intents"></a>Eltérő leképezések definiálása
Győződjön meg arról, hogy az egyes szándékok szókincse csak erre a célra szolgál, és ne legyen átfedésben más szándékkal. Ha például olyan alkalmazást szeretne használni, amely az utazási szabályokat, például a légitársasági repülőjáratokat és a szállodákat kezeli, dönthet úgy, hogy ezeket a témaköröket külön szándékként vagy azonos szándékkal látja el a részletekben lévő konkrét adategységekhez.

Ha a két cél közötti szókincs ugyanaz, kombinálja a szándékot, és használja az entitásokat.

Vegye figyelembe a következő példa hosszúságú kimondott szöveg:

|Példák kimondott szövegekre|
|--|
|Repülőjáratok lefoglalása|
|Szállodai foglalás|

`Book a flight` és `Book a hotel` ugyanazt a szókincset használja `book a ` . Ez a formátum ugyanaz, mint a különböző szavakat `flight` és a `hotel` kinyert entitásokat.

## <a name="do-add-features-to-intents"></a>Szolgáltatások hozzáadása a szándékokhoz

A szolgáltatások a szándék fogalmait írják le. A szolgáltatás lehet olyan szavak listája, amelyek jelentősek az adott szándék szempontjából, vagy olyan entitások, amelyek jelentősek erre a célra.

## <a name="do-find-sweet-spot-for-intents"></a>A cél az édes hely keresése
A LUIS előrejelzési adatai alapján állapítsa meg, hogy a szándékok átfedésben vannak-e. Az átfedésben lévő leképezések megzavarják a LUIS-t. Ennek az az oka, hogy a legfelső pontozási szándék túl van közelebb egy másik szándékhoz. Mivel a LUIS nem ugyanazt az elérési utat használja, mint az egyes időpontokban az adatképzések, az átfedésben lévő szándék a képzés első vagy második lépésének esélye. Azt szeretné, hogy a teljes pontszám az egyes céloknál távolabb legyen, így ez a flip/flop nem fog történni. A szándékok megfelelő megkülönböztetésének minden alkalommal a várt felső szándékot kell eredményeznie.

## <a name="do-use-machine-learned-entities"></a>Gépi megtanult entitások használata

A gépi megtanult entitások az alkalmazásra vannak szabva, és a címkézést kötelezővé kell tenniük. Ha nem használ gépi megtanult entitásokat, lehetséges, hogy nem megfelelő eszközt használ.

A gépi megtanult entitások más entitásokat is használhatnak szolgáltatásként. Ezek az entitások lehetnek olyan egyéni entitások, mint például a reguláris kifejezési entitások vagy a lista entitások, vagy az előre elkészített entitások szolgáltatásként is használhatók.

Ismerkedjen meg a [hatékony gépi megtanult entitásokkal](luis-concept-entity-types.md#effective-machine-learned-entities).

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Készítse el az alkalmazás iteratív a verziókkal

Minden authoring ciklusnak egy meglévő verzióról klónozott új [verzióba](./luis-concept-app-iteration.md)kell esnie.

## <a name="do-build-for-model-decomposition"></a>Build a Model dekompozícióhoz

A modell kibontása a következőket jellemző folyamattal rendelkezik:

* **szándék** létrehozása ügyfél-alkalmazás felhasználói szándékai alapján
* 15-30-es példa hosszúságú kimondott szöveg hozzáadása a valós felhasználói bevitel alapján
* a legfelső szintű adatelemzési koncepció megjelölése példaként
* az adatkoncepció felosztása alentitásokra
* szolgáltatások hozzáadása alentitásokhoz
* funkciók hozzáadása a szándékokhoz

Miután létrehozta a szándékot, és hozzáadta például a hosszúságú kimondott szöveg-t, az alábbi példa az entitások lebomlását ismerteti.

Első lépésként azonosítsa a kinyerni kívánt teljes adatfogalmakat. Ez a gépi tanulási entitás. Ezután bontsa ki a kifejezést a részébe. Ide tartozik az alentitások és a funkciók azonosítása is.

Ha például szeretne kinyerni egy-egy címeket, a legfontosabb gépi tanulási entitás hívható `Address` . A cím létrehozásakor azonosítsa az alentitások egy részét, például a házszámot, a várost, az állapotot és az irányítószámot.

Az elemek kibontásának folytatásához a következőket kell tennie:
* A postai kód kötelező funkciójának hozzáadása reguláris kifejezési entitásként.
* Az utca címe részekbe való bontása:
    * Egy előre összeépített számú entitás kötelező funkciójával rendelkező **utca száma** .
    * Az **utca neve**.
    * Egy **utcanév-típus** , amely egy lista entitás kötelező funkciójával rendelkezik, beleértve az Avenue, a Circle, a Road és a Lane kifejezéseket.

A v3 authoring API lehetővé teszi a modellek lebomlását.

## <a name="do-add-patterns-in-later-iterations"></a>Minták hozzáadása a későbbi iterációkban

Érdemes tisztában lennie azzal, hogy az alkalmazás hogyan viselkedik a [mintázatok](luis-concept-patterns.md) hozzáadása előtt, mivel a mintákat sokkal nagyobb mértékben súlyozzák, mint például a hosszúságú kimondott szöveg, és a megbízhatóságot is rontja.

Miután megértette, hogyan viselkedik az alkalmazás, vegyen fel mintákat, ahogyan azok az alkalmazásra vonatkoznak. Az egyes [iterációk](luis-concept-app-iteration.md)nem szükségesek hozzájuk.

A modell kialakításának elején nem árt felvenni a problémát, de könnyebben láthatja, hogy az egyes minták Hogyan változnak a modell hosszúságú kimondott szöveg való tesztelése után.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>A hosszúságú kimondott szöveg egyensúlyba vétele az összes cél között

Ahhoz, hogy a LUIS-előrejelzések pontosak legyenek, az egyes szándékok (kivéve a none szándékot) hosszúságú kimondott szöveg mennyiségének viszonylag egyenlőnek kell lennie.

Ha az 100-es hosszúságú kimondott szöveg és egy 20 példás hosszúságú kimondott szöveg, akkor a 100-Kimondás szándéka magasabb előrejelzési aránnyal fog rendelkezni.

## <a name="do-add-example-utterances-to-none-intent"></a>Ne adjon hozzá példa hosszúságú kimondott szöveg a nincs szándékhoz

Ez a szándék a tartalék szándék, amely az alkalmazáson kívül mindent jelez. Vegyen fel egy példát a nincs szándékra a LUIS-alkalmazás többi hosszúságú kimondott szöveg 10 példa esetén.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Használja ki a javasolt funkciót az aktív tanuláshoz

Az [aktív tanulás](luis-how-to-review-endpoint-utterances.md) **felülvizsgálati végpontjának hosszúságú kimondott szöveg** rendszeresen használhatja, ahelyett, hogy további példákat hosszúságú kimondott szöveg hozzá. Mivel az alkalmazás folyamatosan kap végponti hosszúságú kimondott szöveg, ez a lista növekszik és változik.

## <a name="do-monitor-the-performance-of-your-app"></a>Az alkalmazás teljesítményének figyelése

Figyelje meg az előrejelzés pontosságát egy [Batch-tesztkörnyezet](./luis-how-to-batch-test.md) használatával.

Tartsa meg a [hosszúságú kimondott szöveg](luis-concept-utterance.md) vagy a végpont hosszúságú kimondott szöveg nem használt különálló hosszúságú kimondott szöveg. Tartsa meg az alkalmazás fejlesztését a tesztkörnyezet számára. Állítsa be a tesztet úgy, hogy az tükrözze a valós felhasználói hosszúságú kimondott szöveg. Ezzel a tesztelési készlettel értékelheti ki az alkalmazás minden egyes iterációját vagy verzióját.

## <a name="dont-publish-too-quickly"></a>Túl gyorsan nem tesz közzé

Ha túl gyorsan teszi közzé az alkalmazást, a [megfelelő tervezés](#do-plan-your-schema)nélkül több problémát is okozhat, például:

* Az alkalmazás nem fog működni a tényleges helyzetben a teljesítmény elfogadható szintjén.
* A séma (szándékok és entitások) nem megfelelő, és ha a sémát követően fejlesztett ki ügyfélalkalmazás-logikát, előfordulhat, hogy újra kell írnia az előzményekből. Ez váratlan késéseket és a projektre vonatkozó további költségeket okozhatja.
* A modellhez hozzáadott hosszúságú kimondott szöveg torzítást okozhat a példaként szolgáló, nehezen hibakeresésre és azonosításra alkalmas értékhez. Emellett a kétértelműség megnehezíti az egyes sémák véglegesítését követően.

## <a name="dont-add-many-example-utterances-to-intents"></a>Ne adjon hozzá számos példa hosszúságú kimondott szöveg a leképezésekhez

Az alkalmazás közzététele után csak a fejlesztési életciklus folyamatában hosszúságú kimondott szöveg adhat hozzá az aktív tanulásból. Ha a hosszúságú kimondott szöveg túl hasonlóak, vegyen fel egy mintát.

## <a name="dont-use-few-or-simple-entities"></a>Ne használjon néhány vagy egyszerű entitást

Az entitások az kinyeréshez és előrejelzéshez készültek. Fontos, hogy az egyes szándékok olyan gépi tanulási entitásokkal rendelkezzenek, amelyek a szándékban lévő adatelemzést írják le. Ez segíti a LUIS előrejelzését abban az esetben is, ha az ügyfélalkalmazás nem kell a kibontott entitást használnia.

## <a name="dont-use-luis-as-a-training-platform"></a>Ne használja a LUIS-t képzési platformként

A LUIS a nyelvi modell tartományára vonatkozik. Nem az általános természetes nyelvi képzési platformként működik.

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Ne vegyen fel több példa hosszúságú kimondott szöveg ugyanazzal a formátummal, figyelmen kívül hagyva a többi formátumot

A LUIS a hosszúságú kimondott szöveg-k variációit várja. A hosszúságú kimondott szöveg változhat, miközben ugyanazt az általános jelentést kell használnia. A variációk magukban foglalhatják a teljes hosszúságot, a Word-választást és a szó elhelyezését.

|Ne használja ugyanazt a formátumot|Eltérő formátum használata|
|--|--|
|Jegy vásárlása a Seattle-be<br>Jegy vásárlása Párizsba<br>Jegy vásárlása az Orlando-ba|1 jegy vásárlása Seattle-be<br>Foglaljon le két helyet a Red Eye-on a következő hétfőn Párizsba<br>3 jegyet szeretnék foglalni az Orlando-ba Spring breakre|

A második oszlop különböző műveleteket (vásárlás, foglalás, könyv), különböző mennyiségeket (1, 2, 3) és különböző kifejezéseket használ, de az is ugyanaz, mint a repülőjegyek utazáshoz való megvásárlásának szándéka.

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Ne keverje össze a leképezések és az entitások definícióját

Hozzon létre egy szándékot bármilyen művelethez, amelyet a robotja végrehajt. Az entitásokat olyan paraméterekként használhatja, amelyek ezt a műveletet teszik elérhetővé.

Egy olyan robot esetében, amely légitársasági repülőjáratokat fog foglalni, hozzon létre egy **BookFlight** szándékot. Ne hozzon létre szándékot minden egyes légitársasághoz vagy célhoz. Ezeket az [adategységeket entitásként](luis-concept-entity-types.md) használhatja, és megjelölheti őket a példában szereplő hosszúságú kimondott szöveg.

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Ne hozzon létre a kifejezések listáját az összes lehetséges értékkel

Adjon meg néhány példát a [kifejezések listájában](luis-concept-feature.md) , de ne minden szót vagy kifejezést. LUIS általánosítja és figyelembe veszi a kontextust.

## <a name="dont-add-many-patterns"></a>Ne adjon hozzá sok mintázatot

Ne adjon hozzá túl sok [mintázatot](luis-concept-patterns.md). LUIS célja, hogy gyorsan tudjon tanulni kevesebb példát. Ne terhelje feleslegesen a rendszerterhelést.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Ne tanítson és tegyen közzé minden egyes példa Kimondás esetén

Vegyen fel 10 vagy 15 hosszúságú kimondott szöveg a képzés és a közzététel előtt. Ez lehetővé teszi, hogy megtekintse az előrejelzés pontosságának hatását. Egyetlen Kimondás hozzáadásával nem lehet látható hatással a pontszámra.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [tervezze meg alkalmazását](luis-how-plan-your-app.md) a Luis-alkalmazásban.