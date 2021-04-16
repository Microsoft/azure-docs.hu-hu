---
title: Entitástípusok – LUIS
description: Az entitások adatokatnyernek ki egy felhasználói kimondott szövegből az előrejelzési futásidőben. Egy _választható_, másodlagos cél a szándék vagy más entitások előrejelzésének növelése az entitás funkcióként való használatával.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 44cffecd653ec2ec748e73d01dc86a87cfcd7de9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500328"
---
# <a name="entities-in-luis"></a>Entitások a LUIS-ban

Az entitások olyan elemek vagy elemek, amelyek a felhasználó szándéka szempontjából relevánsak. Az entitások a kimondott szövegből kinyerni kívánt adatokat határozzák meg, és elengedhetetlenek a felhasználó szükséges műveletének befejezéséhez. Például:

|Beszédelem|Szándék előrejelezni|Kinyert entitások|Magyarázat|
|--|--|--|--|
|helló, hogy vagy?|Üdvözlés|-|Semmit nem kell kinyerni.|
|Rendelek egy kis pizzát|orderPizza| "kicsi" | A "Méret" entitás "kicsiként" lesz kinyerve.|
|A hálószobás világítás kikapcsolása|Kanyart| "hálószobá" | A "Szoba" entitás kinyerve "hálószobá" lesz.|
|A megtakarítási fiók egyenlegének ellenőrzése 4406-ra végződően|checkBalance (Egyensúly ellenőrzése)| "megtakarítás", "4406" | Az "accountType" entitást a "savings" (megtakarítás) és az "accountNumber" entitást a "4406" entitással lehet kinyerni.|
|3 jegy vásárlása New Yorkba|buyTickets| "3", "New York" | A "ticketsCount" entitást a rendszer "3"-ként, a "Destination" (Cél) entitást pedig "New York" (New York) entitásként bontja ki.|

Az entitások használata nem kötelező, de ajánlott. Nem kell entitásokat létrehoznia az alkalmazás minden fogalmához, csak azok számára, ahol:

* Az ügyfélalkalmazásnak szüksége van az adatokra, vagy 
* Az entitás tippként vagy jelként működik egy másik entitás vagy szándék számára. Az entitásokkal kapcsolatos további információkért a Funkciókként az [Entitások mint funkciók között olvashat.](#entities-as-features)

## <a name="entity-types"></a>Entitástípusok

Entitás létrehozásához meg kell adni neki egy nevet és egy típust. A LUIS-ban számos entitástípus létezik. 

### <a name="list-entity"></a>Listaentitás

A listaentitás a kapcsolódó szavak rögzített, lezárt halmazát jelöli a szinonimák mellett. Listaentitások használatával több szinonimát vagy változatot is felismerhet, és normalizált kimenetet kaphat hozzájuk. Az ajánlott *lehetőséggel* az aktuális lista alapján javaslatokat láthat az új szavakra. 

A listaentitások nem gépi tanulással edznek, ami azt jelenti, hogy a LUIS nem derít fel további értékeket a listaentitásokhoz. A LUIS a válaszban entitásként jelöli meg bármely lista egyik elemének egyezését.

A listaentitások egyeztetése megkülönbözteti a kis- és nagybetűket, és pontos egyezést kell kinyerni. A normalizált értékek a listaentitás egyeztetésekor is használatosak. Például:

|Normalizált érték|Szinonimák|
|--|--|
|Kicsi|sm, sml, tiny, smallest|
|Közepes|md, mdm, regular, average, middle|
|Nagy|lg, lrg, big|

További információt [a listaentitások referenciacikkében](reference-entity-list.md) talál.

### <a name="regex-entity"></a>Regex Entity

A reguláriskifejezés-entitások az Ön által adott reguláriskifejezés-minta alapján kinyernek egy entitást. Figyelmen kívül hagyja az esetet, és figyelmen kívül hagyja a kulturális változatot. A reguláris kifejezés strukturált szöveghez vagy bizonyos formátumban várt alfanumerikus értékek előre meghatározott sorozata esetén a legjobb. Például:

|Entitás|Reguláris kifejezés|Példa|
|--|--|--|
|Járatszám|flight [A-Z] {2} [0-9]{4}| FLIGHT AS 1234|
|Bankkártyaszám|[0-9]{16}|5478789865437632|

További információt [a regex entitások referenciacikkében](reference-entity-regular-expression.md) talál.

### <a name="prebuilt-entity"></a>Előre összeállított entitás

A LUIS előre összeállított entitások készletét kínálja az olyan gyakori adattípusok felismeréséhez, mint a név, a dátum, a szám és a pénznem.  Az előre összeállított entitások viselkedése rögzített. Az előre összeállított entitások támogatása a LUIS-alkalmazás kulturális környezetének megfelelően változik. Például:

|Előre összeállított entitás|Példaérték|
|--|--|
|PersonName (Személynév)|James, Bill, Tom|
|DatetimeV2|2019-05-02, 2019. május 2. 08.|

További információt [az előre összeállított entitások referenciacikkében](./luis-reference-prebuilt-entities.md) talál.

### <a name="patternany-entity"></a>Pattern.Any entitás

Egy minta. Minden entitás egy változó hosszúságú helyőrző, amely csak a minta kimondott sablonjának kimondott szövegében használatos annak megjelöléséhez, hogy hol kezdődik és végződik az entitás. Egy adott szabályt vagy mintát követ, és a legjobban rögzített lexikális szerkezetű mondatokhoz használható. Például:

|Példa kimondott szöveg|Mintázat|Entitás|
|--|--|--|
|Kaphatok egy kicsit?|Kaphatok {étel} [kérjük][?]| Burger
|Kaphatok pizzát?|Kaphatok {étel} [kérjük][?]| Pizza
|Hol található a The Great Gatsby?|Hol található a következő: {bookName}?| A Nagy Gatsby|

További információt [a Pattern.Any entitások referenciacikkében](./reference-entity-pattern-any.md) talál.

### <a name="machine-learned-ml-entity"></a>Gépi tanulású (ML) entitás

A gépi tanulással megtanult entitás környezetek segítségével bont ki entitásokat címkével jelölt példák alapján. Ez a LUIS-alkalmazások előnyben részesített entitása. Gépi tanulási algoritmusokat használ, és a címkézést az alkalmazáshoz kell igazítani. Gépi tanulási entitással azonosíthatja azokat az adatokat, amelyek nem mindig jól formázottak, de azonos jelentéssel bírnak. 

|Példa kimondott szöveg|*Kinyert termékentitás*|
|--|--|
|Szeretnék vásárolni egy könyvet.|"book"|
|Be tudom szerezni ezeket a cipőket?|"shoes"|
|Adja hozzá ezeket a rövideket a kosárhoz.|"shorts"|

A gépi tanuláson át áteső entitásokkal kapcsolatban itt olvashat [bővebben.](./reference-entity-machine-learned-entity.md)

További információt [a gépi tanulással kapcsolatos entitások referenciacikkében](./reference-entity-pattern-any.md) talál.

#### <a name="ml-entity-with-structure"></a>Ml-entitás struktúrával

Az ML-entitások kisebb alentitásokból is alkothatóak, amelyek mindegyikének saját tulajdonságai is vannak. A Cím *struktúrája* például a következő lehet:

* Cím: 4567 Main Street, NY, 98052, USA
    * Épületszám: 4567
    * Utca neve: Main Street
    * Állam: NY
    * Irányítószám: 98052
    * Ország: USA


### <a name="building-effective-ml-entities"></a>Hatékony ML-entitások kiépítése

A gépi tanuláson át áteső entitások hatékony felépítéséhez kövesse az alábbi ajánlott eljárásokat:

* Ha rendelkezik gépi tanulással és alentitásokkal, győződjön meg arról, hogy az entitás és az alentitások különböző rendelései és változatai a címkével címkével jelölt kimondott szövegben jelennek meg. A címkével címkével jelölt kimondott szövegnek tartalmaznia kell az összes érvényes űrlapot, valamint a hiányzó és a kimondott szövegben átrendezett entitásokat.

* Kerülje az entitások túlilledését egy nagyon rögzített készlethez. A túlilledés akkor fordul elő, ha a modell nem általánosít jól, és ez gyakori probléma a gépi tanulási modellekben. Ez azt jelenti, hogy az alkalmazás nem működne megfelelően az új típusú példákon. A címkével címkével jelölt kimondott szövegeket is át kell adnia, hogy az alkalmazás a korlátozott példákon túl általánosítson.

* A címkézésnek konzisztensnek kell lennie a szándékok között. Ide tartoznak még az entitást tartalmazó *None* szándékban adott kimondott szöveg is. Ellenkező esetben a modell nem tudja hatékonyan meghatározni a sorozatokat.

## <a name="entities-as-features"></a>Entitások mint funkciók

Az entitások egy másik fontos funkciója, hogy jellemzőkként vagy más szándékok vagy entitások jellemzőinek megkülönböztetésére használják őket, hogy a rendszer megfigyelje és tanulja őket.

### <a name="entities-as-features-for-intents"></a>Entitások mint a szándékok funkciói

Az entitások használhatók a szándékok jelként való jelzéseként. Például egy adott entitás jelenléte a kimondott szövegben meg tudja különböztetni, hogy melyik szándék alá tartozik.

|Példa kimondott szöveg|Entitás|Szándék|
|--|--|--|
|Foglalj nekem *egy meccst New Yorkba.*|City|Book Flight|
|Foglaljon nekem egy *konferenciatermet.*|Szoba|Foglalási helyiség|

### <a name="entities-as-feature-for-entities"></a>Entitások mint funkció az entitásokhoz

Az entitásokat más entitások jelenlétének jelzéseként is használhatja. Erre gyakori példa egy előre összeállított entitás használata funkcióként egy másik ML-entitáshoz.
Ha repülőjárat-foglalási rendszert hoz létre, és a kimondott szöveg így néz  ki:  "Foglalj le egy repülőjegyet Tolból Seattle-be", akkor gépi tanulási entitásként a Kiindulási város és a Célváros lesz a gépi tanulási entitás. Az előre összeállított entitást mindkét entitáshoz jó funkcióként `GeographyV2` használni.

További információt [a GeographyV2 entitások referenciáját](./luis-reference-prebuilt-geographyv2.md) bemutató cikkben talál.

Az entitásokat más entitások kötelező funkcióiként is használhatja. Ez segít a kinyert entitások megoldásában. Ha például egy pizzarendelési alkalmazást hoz létre, és gépi tanulási entitással rendelkezik, létrehozhat listaentitást, és használhatja az entitáshoz szükséges `Size` `SizeList` `Size` funkcióként. Az alkalmazás a normalizált értéket adja vissza a kimondott szövegből kinyert entitásként. 

További [információért](luis-concept-feature.md) tekintse [](./luis-reference-prebuilt-entities.md) meg a funkciókat és az előre összeállított entitásokat, ha többet szeretne megtudni a kulturális környezetben elérhető előre összeállított entitások megoldásról. 


## <a name="entity-prediction-status-and-errors"></a>Entitás-előrejelzés állapota és hibái

A LUIS-portál a következőket jeleníti meg, ha az entitás más entitás-előrejelzéssel rendelkezik, mint a kimondott példaként címkével jelölt entitás. Ez a pontszám az aktuális betanított modellen alapul. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="A LUIS-portál megjeleníti, ha az entitás más entitás-előrejelzéssel rendelkezik, mint a kimondott példaként megadott entitás":::

A hibát okozó szöveg ki van emelve a példaszövegben, és a kimondott példaszöveg vonala egy piros háromszögként megjelenő hibajelzőt tartalmaz a jobb oldalon. 

Az entitáshibák elhárításához próbáljon ki egyet vagy többet az alábbiak közül:

* A kiemelt szöveg címkéje helytelen. A javításhoz tekintse át a címkét, javítsa ki, majd képezje újra az alkalmazást. 
* Hozzon [létre egy](luis-concept-feature.md) funkciót az entitáshoz, amely segít az entitás fogalmának azonosításában.
* Adjon hozzá [további kimondott szövegeket és](luis-concept-utterance.md) címkét az entitással.
* [Tekintse át az előrejelzési](luis-concept-review-endpoint-utterances.md) végponton fogadott bármely kimondott szöveg aktív tanulási javaslatait, amelyek segíthetnek az entitás fogalmának azonosításában.


## <a name="next-steps"></a>Következő lépések

* Ismerje meg a jó példa [kimondott szövegeket.](luis-concept-utterance.md)
* Az [entitások](luis-how-to-add-entities.md) LUIS-alkalmazáshoz való hozzáadásáról az Entitások hozzáadása cikkből olvashat bővebben.
* További információ a LUIS-alkalmazások [korlátairól.](./luis-limits.md) 
* Az [oktatóanyag segítségével](tutorial-machine-learned-entity.md) megtudhatja, hogyan lehet strukturált adatokat kinyerni egy kimondott szövegből a gépi tanulási entitás használatával.
