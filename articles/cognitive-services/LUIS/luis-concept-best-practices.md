---
title: Ajánlott eljárások a LUIS-alkalmazás építéséhez
description: Ismerje meg az ajánlott eljárásokat, hogy a legjobb eredményeket tudja kihozni a LUIS-alkalmazás modelljéből.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: d5fa2a1e865a4f54de268e7ad756d1d4363f3b78
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500209"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Ajánlott eljárások a language understanding (LUIS) alkalmazások építéséhez
Az alkalmazás-szerzői folyamattal hozza létre a LUIS-alkalmazást:

* Nyelvi modellek (szándékok és entitások) összeállítása
* Néhány példaként betanított kimondott szöveg hozzáadása (szándékonként 15–30)
* Közzététel végponton
* Tesztelés végpontból

Az alkalmazás közzététele [után](luis-how-to-publish-app.md)a fejlesztési életciklussal funkciókat adhat hozzá, tehet közzé és tesztelheti a végpontról. Ne kezdje a következő szerzői ciklust további példa kimondott szövegekkel, mert így a LUIS nem tanulja meg a modellt valós felhasználói kimondott szövegekkel.

Csak akkor bontsa ki a kimondott szövegeket, ha a példa- és a végponti kimondott szöveg aktuális készlete is magabiztos, magas előrejelzési pontszámokat ad vissza. Pontszámok javítása aktív [tanulással.](luis-concept-review-endpoint-utterances.md)




## <a name="do-and-dont"></a>Do and Don't
Az alábbi lista a LUIS-alkalmazások ajánlott eljárásait tartalmazza:

|Ajánlott|Nem ajánlott|
|--|--|
|[A séma megterve](#do-plan-your-schema)|[Build és közzététel csomag nélkül](#dont-publish-too-quickly)|
|[Eltérő szándékok meghatározása](#do-define-distinct-intents)<br>[Funkciók hozzáadása szándékhoz](#do-add-features-to-intents)<br>
[Gépi tanuláson át áteső entitások használata](#do-use-machine-learned-entities) |[Számos kimondott példa kimondott szöveg hozzáadása szándékhoz](#dont-add-many-example-utterances-to-intents)<br>[Kevés vagy egyszerű entitás használata](#dont-use-few-or-simple-entities) |
|[Egy kis hely megkereshető a túl általános és az egyes szándékok számára túl specifikus között](#do-find-sweet-spot-for-intents)|[A LUIS használata betanító platformként](#dont-use-luis-as-a-training-platform)|
|[Az alkalmazás iteratív összeállítása verziók használatával](#do-build-your-app-iteratively-with-versions)<br>[Entitások összeállítása modell felbontáshoz](#do-build-for-model-decomposition)|[Számos, azonos formátumú kimondott szöveg hozzáadása más formátumok figyelmen kívül hagyása nélkül](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Minták hozzáadása későbbi iterációkban](#do-add-patterns-in-later-iterations)|[A szándékok és entitások definíciójának vegyesen](#dont-mix-the-definition-of-intents-and-entities)|
|[Egyensúlyba kell találnia a kimondott szövegeket az összes szándék között,](#balance-your-utterances-across-all-intents) kivéve a None szándékot.<br>[Kimondott példa kimondott szöveg hozzáadása a None szándékhoz](#do-add-example-utterances-to-none-intent)|[Kifejezéslisták létrehozása az összes lehetséges értékkel](#dont-create-phrase-lists-with-all-the-possible-values)|
|[A javaslat funkció használata aktív tanuláshoz](#do-leverage-the-suggest-feature-for-active-learning)|[Túl sok minta hozzáadása](#dont-add-many-patterns)|
|[Az alkalmazás teljesítményének figyelése kötegelt teszteléssel](#do-monitor-the-performance-of-your-app)|[Betanítás és közzététel minden hozzáadott kimondott példa szerint](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-plan-your-schema"></a>Tervezze meg a sémát

Az alkalmazás sémája kiépítése előtt meg kell határoznia, hogy mit és hol tervez használni az alkalmazást. Minél alaposabban és pontosabban tervezi meg a tervezést, annál jobb lesz az alkalmazása.

* Megcélzott felhasználók kutatása
* Az alkalmazásnak megfelelő teljes személyek definiálása – hang, avatar, problémakezelés (proaktív, reaktív)
* A felhasználói interakciók (szöveg, beszéd) azonosítása, amelyeken keresztül a csatornákat, a meglévő megoldásoknak való osztást vagy az alkalmazás új megoldásának létrehozását
* Végfelhasználói út végpontok között
    * Mire számíthat az alkalmazástól, és mit nem? * Mik a prioritások annak, hogy mit kell tennie?
    * Melyek a fő esetek?
* Adatgyűjtés – [tudnivalók](data-collection.md) az adatok gyűjtéséről és előkészítéséről

## <a name="do-define-distinct-intents"></a>Eltérő szándékok meghatározása
Győződjön meg arról, hogy az egyes szándékok szókészlete csak erre a szándékra vonatkozik, és nem fedi át egymást más szándékkal. Ha például olyan alkalmazást szeretne, amely utazási adatokat kezel , például repülőjáratokat és szállodákat, dönthet úgy, hogy ezeket a tárgyterületeket külön szándékként vagy azonos szándékként használja a kimondott szövegben lévő adott adatokhoz tartozó entitásokkal.

Ha két szándék szókincse azonos, kombinálja a szándékot, és használjon entitásokat.

Vegyük példaként a következő kimondott szövegeket:

|Példák kimondott szövegekre|
|--|
|Repülőjárat foglalása|
|Szálloda foglalása|

`Book a flight` A `Book a hotel` és a ugyanazt a szókészletet `book a ` használja. Ez a formátum megegyezik, ezért azonos szándéknak kell lennie a és a különböző szavaival, mint a `flight` `hotel` kinyert entitások.

## <a name="do-add-features-to-intents"></a>Funkciók hozzáadása szándékhoz

A funkciók a szándékok fogalmait írják le. A funkció lehet egy kifejezéslista, amely az adott szándéknak megfelelő szavakat tartalmazza, vagy egy olyan entitás, amely jelentős az adott szándék számára.

## <a name="do-find-sweet-spot-for-intents"></a>Nem talál jó pontot a szándékok számára
A LUIS előrejelzési adataival állapítsa meg, hogy a szándékok átfedésben vannak-e. Az átfedésben lévő szándékok összezavarják a LUIS-t. Az eredmény az, hogy a legmagasabb pontszámú szándék túl közel van egy másik szándékhoz. Mivel a LUIS nem ugyanazt az útvonalat használja az adatokon keresztül minden alkalommal a betanítás során, az átfedésben lévő szándéknak megvan az esélye, hogy a betanítás első vagy második része legyen. Azt szeretné, hogy a kimondott szöveg pontszáma az egyes szándékok között távolabb legyen egymástól, hogy ez a fel-/ledobás ne történjen meg. A szándékok megfelelő megkülönböztetése minden alkalommal a várt legfelső szándékot eredményezi.

## <a name="do-use-machine-learned-entities"></a>Gépi tanuláson át áteső entitások használata

A gépi tanulású entitások az alkalmazáshoz vannak szabva, és a sikeres címkézést igénylik. Ha nem gépi tanuláson át áteső entitásokat használ, előfordulhat, hogy nem a megfelelő eszközt használja.

A gépi tanuláson át áteső entitások más entitásokat is használhatnak funkciókként. Ezek az egyéb entitások egyéni entitások, például reguláriskifejezés-entitások vagy listaentitások, vagy előre összeállított entitások is használhatók funkciókként.

Ismerje meg a [hatékony gépi tanuláson át áteső entitásokat.](luis-concept-entity-types.md#machine-learned-ml-entity)

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Az alkalmazás iteratív összeállítása verziók használatával

Minden szerzői ciklusnak egy új verzióban kell [lennie,](./luis-concept-app-iteration.md)amely egy meglévő verzióból van klónozva.

## <a name="do-build-for-model-decomposition"></a>Buildelhet a modell felbontására

A modell felbontásának tipikus folyamata a következő:

* szándék **létrehozása** az ügyfélalkalmazás felhasználói szándékai alapján
* 15–30 példa kimondott szöveg hozzáadása valós felhasználói bevitel alapján
* legfelső szintű adatfogalmak címkézése kimondott példaként
* adatfogalmak alentitásokbe való lebontása
* funkciók hozzáadása az alentitásokhoz
* funkciók hozzáadása szándékhoz

Miután létrehozta a szándékot és hozzáadta a példaként megadott kimondott szövegeket, az alábbi példa az entitás felbontását ismerteti.

Először azonosítsa a kimondott szövegben kinyerni kívánt teljes adatfogalmakat. Ez az Ön gépi tanulási entitása. Ezután a kifejezés részekre bontása. Ebbe beletartozik az alentitások és a funkciók azonosítása is.

Ha például egy címet szeretne kinyerni, a legfelső gépi tanulási entitás neve `Address` lehet. A cím létrehozásakor azonosítsa néhány alentitását, például a címet, a várost, az államot és az irányítószámot.

Az elemek felbontása a következővel folytatódik:
* Az irányítószám kötelező tulajdonságának hozzáadása reguláriskifejezés-entitásként.
* A cím felbontása részekre:
    * Egy **előre összeállított** szám entitás kötelező jellemzővel rendelkezik egy utca számmal.
    * Egy **utcanév.**
    * Egy **utcatípus** egy listaentitás kötelező tulajdonságával, beleértve az utakat, köröket, utakat és sávokat.

A V3 szerzői API lehetővé teszi a modell felbontását.

## <a name="do-add-patterns-in-later-iterations"></a>Minták hozzáadása későbbi iterációkban

A minták hozzáadása előtt tudnia [](luis-concept-patterns.md) kell, hogyan viselkedik az alkalmazás, mivel a minták nagyobb súlyozással vannak felfogva, mint a kimondott példaként való kimondott szöveg, és el fognak tolni a megbízhatóságot.

Ha már tudja, hogyan viselkedik az alkalmazás, adjon hozzá mintákat, amint azok az alkalmazásra vonatkoznak. Nem kell minden iterációval [hozzáadnia őket.](luis-concept-app-iteration.md)

A modelltervezés elején nem okoz kárt a hozzáadásuk, de könnyebb látni, hogyan módosítják az egyes minták a modellt a kimondott szövegekkel való tesztelés után.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Egyensúlyba kell találnia a kimondott szövegeket az összes szándék között

Ahhoz, hogy a LUIS-előrejelzések pontosak legyenek, az egyes szándékok kimondott példa kimondott szövegének mennyisége (a None szándék kivételével) viszonylag egyenlőnek kell lennie.

Ha 100 kimondott példa kimondott szövegből és 20 példa kimondott szövegből áll szándék, a 100 kimondott szöveg szándéka magasabb előrejelzési sebességgel fog fog esni.

## <a name="do-add-example-utterances-to-none-intent"></a>Kimondott példa kimondott szövegeket adhat hozzá a None szándékhoz

Ez a szándék a tartalék szándék, amely mindent jelez az alkalmazáson kívül. Adjon hozzá egy példa kimondott szövegeket a None szándékhoz a LUIS-alkalmazás többi részében minden 10 példa kimondott szöveghez.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Használja a javaslat funkciót az aktív tanuláshoz

Használja az aktív  [tanulás](luis-how-to-review-endpoint-utterances.md)végponti kimondott szövegének rendszeres áttekintését, ahelyett, hogy további kimondott példa kimondott szövegeket ad hozzá a szándékhoz. Mivel az alkalmazás folyamatosan fogad végponti kimondott szövegeket, ez a lista egyre nő és változik.

## <a name="do-monitor-the-performance-of-your-app"></a>Az alkalmazás teljesítményének figyelése

Monitorja az előrejelzés pontosságát egy [kötegelt tesztelési készlet](./luis-how-to-batch-test.md) használatával.

Tartsa meg a kimondott szövegeket, amelyek [](luis-concept-utterance.md) nem kimondott példaként vagy végponti kimondott szövegként vannak használva. Tovább fejleszti az alkalmazást a tesztelési készlethez. A tesztkészletet igaz állítsa be úgy, hogy valós felhasználói beszédeket tükrözze. Ezzel a tesztkészletkel értékelheti ki az alkalmazás egyes iterációját vagy verzióját.

## <a name="dont-publish-too-quickly"></a>Ne tegye közzé túl gyorsan

Az alkalmazás túl gyorsan, megfelelő tervezés nélkül való [közzététele](#do-plan-your-schema)számos problémát okozhat, például:

* Az alkalmazás nem fog elfogadható teljesítményszinten működni a tényleges forgatókönyvben.
* A séma (szándékok és entitások) nem lenne megfelelő, és ha az ügyfélalkalmazás logikáját a séma alapján fejlesztette ki, előfordulhat, hogy az új sémát újra kell írnia. Ez váratlan késéseket és többletköltséget okozna a projektben, amelyn dolgozik.
* A modellhez adott kimondott szövegekkel torzítást okozhat a példaként beállított kimondott szöveg, amely nehezen lehet hibakeresést végezni és azonosítani. Ez megnehezíti a kétértelműség eltávolítását is, miután egy adott sémába kötelezettséget vállalt.

## <a name="dont-add-many-example-utterances-to-intents"></a>Ne adjon hozzá sok kimondott példa kimondott szövegeket a szándékhoz

Az alkalmazás közzététele után csak az aktív tanulásból származó kimondott szövegeket adja hozzá a fejlesztési életciklus folyamatához. Ha a kimondott szöveg túl hasonló, adjon hozzá egy mintát.

## <a name="dont-use-few-or-simple-entities"></a>Ne használjon kevés vagy egyszerű entitást

Az entitások az adatok kinyerése és előrejelzése érdekében vannak felépítve. Fontos, hogy minden szándék gépi tanulási entitásokkal legyen, amelyek leírják a szándékban található adatokat. Ez segít a LUIS-nak a szándék előrejelzésében, még akkor is, ha az ügyfélalkalmazásnak nincs szüksége a kinyert entitás használatára.

## <a name="dont-use-luis-as-a-training-platform"></a>Ne használja a LUIS-t betanító platformként

A LUIS egy nyelvi modell tartományának megfelelő. Nem általános természetes nyelvi képzési platformként működik.

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Ne adjon hozzá sok azonos formátumú kimondott példa kimondott szövegeket, figyelmen kívül hagyva a többi formátumot

A LUIS a szándék kimondott szövegének változatait várja. A kimondott szöveg eltérő lehet, miközben a jelentése megegyezik. A változatok magukban foglalhatják a kimondott szöveg hosszát, a szóválasztást és a szóelhelyezést.

|Ne használja ugyanazt a formátumot|Használjon változó formátumot|
|--|--|
|Jegy vásárlása Seattle-be<br>Jegy vásárlása Párizsba<br>Jegy vásárlása Orlandóhoz|1 jegy vásárlása Seattle-be<br>Foglalj le két helyet jövő hétfőtől Párizsig<br>3 jegyet szeretnék lefoglalni Orlandóba a tavaszi szünetre|

A második oszlop különböző műveleteket (vásárlás, foglalás, könyv), különböző mennyiségeket (1, kettő, 3) és különböző szavakat használ, de mind ugyanaz a szándékuk, hogy repülőjegyet vásárolnak az utazáshoz.

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Ne keverje a szándékok és entitások definícióját

Hozzon létre egy szándékot minden olyan művelethez, amit a robot fog. Használjon entitásokat paraméterekként, amelyek lehetővé teszik ezt a műveletet.

Egy repülőjáratokat lefoglaló robothoz hozzon létre egy **BookFlight szándékot.** Ne hozzon létre szándékot minden légitársaság vagy cél számára. Ezeket az adatokat entitásként [használhatja,](luis-concept-entity-types.md) és megjelölheti őket a kimondott példaként használt kimondott szövegben.

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Ne hozzon létre kifejezéslistákat az összes lehetséges értékkel

Adjon meg néhány példát a [kifejezéslistákban,](luis-concept-feature.md) de ne minden szót vagy kifejezést. A LUIS általánosítja és figyelembe veszi a kontextust.

## <a name="dont-add-many-patterns"></a>Ne adjon hozzá sok mintát

Ne adjon hozzá túl sok [mintát.](luis-concept-patterns.md) A LUIS-t arra szántuk, hogy gyorsan, kevesebb példával tanuljon. Ne terhelje feleslegesen túl a rendszert.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Ne betanítsa és tegye közzé minden kimondott példaként

10 vagy 15 kimondott szöveg hozzáadása a betanítás és a közzététel előtt. Így láthatja az előrejelzés pontosságára gyakorolt hatást. Előfordulhat, hogy egyetlen kimondott szöveg hozzáadása nincs látható hatással a pontszámra.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [tervezze meg alkalmazását a](luis-how-plan-your-app.md) LUIS-alkalmazásban.