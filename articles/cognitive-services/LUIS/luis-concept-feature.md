---
title: A LUIS-vel rendelkező gépi tanulási funkciók
description: Adjon hozzá funkciókat a nyelvi modellhez, hogy javaslatokat nyújtson a címkével vagy osztályozással ellátott bemenetek felismeréséhez.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: da85abdff3d1022659f2d4e83fd14c5ae6003fc9
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546057"
---
# <a name="machine-learning-features"></a>Gépi tanulási funkciók

A gépi tanulásban a *funkció*   a rendszer által megfigyelt és megszerzett adattípusok megkülönböztető tulajdonságai vagy adatattribútuma.

A gépi tanulási funkciók a LUIS fontos mutatóit adják meg, ahol megkereshetik azokat a dolgokat, amelyek megkülönböztetik a koncepciót. Arra utalnak, hogy LUIS használható, de nem rögzített szabályok. A LUIS ezeket a mutatókat a címkékkel együtt használja az adatkereséshez.

Egy funkció a következő függvényként használható: f (x) = y. A példában a Kimondás során a szolgáltatás megkeresi a megkülönböztető tulajdonságokat. Ez az információ segít a séma létrehozásában.

## <a name="types-of-features"></a>A szolgáltatások típusai

A szolgáltatások a séma kialakításának szükséges részei. A LUIS a kifejezések listáját és modelljét is támogatja a szolgáltatásként:

* Kifejezés-lista funkció
* Modell (szándék vagy entitás) szolgáltatásként

## <a name="find-features-in-your-example-utterances"></a>A példa hosszúságú kimondott szöveg funkcióinak megkeresése

Mivel a LUIS egy Language-alapú alkalmazás, a funkciók szöveges alapúak. Válassza ki a megkülönböztetni kívánt tulajdonságot jelző szöveget. LUIS esetében a legkisebb egység a *token*. Az angol nyelv esetében a token olyan levelek és számok folytonos tartománya, amelyek nem rendelkeznek szóközökkel vagy írásjelekkel.

Mivel a szóközök és az írásjelek nem jogkivonatok, a funkciókként használható szöveges jelekre koncentrálhat. Ne feledje, hogy a szavak változatait is tartalmazza, például:

* többes számú űrlap
* műveleti idő
* rövidítéseket
* helyesírások és helyesírási hibák

Annak megállapítása, hogy a szöveg, mert megkülönbözteti a tulajdonságokat, a következőnek kell lennie:

* Pontos szót vagy kifejezést adjon meg: vegyen fel egy reguláris kifejezés entitást vagy egy lista entitást az entitás vagy szándék szolgáltatásként.
* Olyan jól ismert fogalmakkal, mint például a dátumok, az időpontok vagy a személyek nevei: egy előre összeépített entitást használhat az entitás vagy szándék szolgáltatásként.
* Ismerkedjen meg az új példákkal az idő múlásával: használjon egy kifejezést a fogalomhoz, amely az entitás vagy a szándék szolgáltatásának egyik példáját mutatja be.

## <a name="create-a-phrase-list-for-a-concept"></a>Kifejezések listájának létrehozása egy fogalomhoz

A kifejezések listája a fogalmakat leíró szavak vagy kifejezések listája. A kifejezések listája kis-és nagybetűket nem megkülönböztető egyezésként lesz alkalmazva a jogkivonat szintjén.

A kifejezések listájának hozzáadásakor **[globálisként](#global-features)** állíthatja be a szolgáltatást. A globális funkció a teljes alkalmazásra vonatkozik.

### <a name="when-to-use-a-phrase-list"></a>Mikor kell használni a kifejezések listáját

Ha a LUIS-alkalmazás általánosított és a koncepció új elemeinek azonosítására van szüksége, használja a mondatok listáját. A kifejezések listája hasonló a tartományhoz tartozó szószedetekhez. Javítják a szándékok és az entitások megismerésének minőségét.

### <a name="how-to-use-a-phrase-list"></a>Kifejezések listájának használata

A kifejezést tartalmazó listával LUIS a kontextust és az általánosításokat úgy tekinti meg, hogy azonosítsa a hasonló elemeket, de nem egy pontos szöveges egyezés. Az alábbi lépéseket követve használhatja a kifejezések listáját:

1. Kezdje egy gépi tanulási entitással:
    1. Adja hozzá például a hosszúságú kimondott szöveg.
    1. Címke egy gépi tanulási entitással.
1. Kifejezések listájának hozzáadása:
    1. Szavak hozzáadása hasonló jelentéssel. Ne adjon hozzá minden lehetséges szót vagy kifejezést. Ehelyett adjon hozzá néhány szót vagy kifejezést egyszerre. Ezután végezze el az újratanítást és a közzétételt.
    1. Tekintse át és adja hozzá a javasolt szavakat.

### <a name="a-typical-scenario-for-a-phrase-list"></a>Egy kifejezés listára jellemző forgatókönyv

A kifejezések listájának egy tipikus forgatókönyve az adott ötlethez kapcsolódó szavak kiemelése.

Az orvosi feltételek jó példát mutatnak az olyan szavakra, amelyeknek szükségük lehet a kifejezésekre, hogy növeljék a jelentőségét. Ezek a kifejezések konkrét fizikai, kémiai, terápiás vagy absztrakt jelentésekkel rendelkezhetnek. LUIS nem fogja tudni, hogy a feltételek nem fontosak a tárgy tartománya számára kifejezési lista nélkül.

Az orvosi feltételek kinyerése:

1. Hozzon létre például hosszúságú kimondott szöveg és címkézze fel orvosi kifejezéseket a hosszúságú kimondott szöveg belül.
2. Hozzon létre egy kifejezéseket tartalmazó listát a tárgy tartományon belüli kifejezésekre vonatkozó példákkal. A kifejezések listájának tartalmaznia kell a ténylegesen felcímkézett kifejezést és más, ugyanezen fogalmakat leíró kifejezéseket.
3. Adja hozzá a kifejezés listát ahhoz a entitáshoz vagy alentitáshoz, amely kibontja a kifejezés listában használt fogalmat. A leggyakoribb forgatókönyv a gépi tanulási entitások egyik összetevője (gyermeke). Ha a kifejezések listáját az összes leképezésben vagy entitásban alkalmazni kell, a kifejezések listáját globális kifejezésként kell megjelölnie. A **enabledForAllModels** jelző a modell hatókörét SZABÁLYOZZA az API-ban.

### <a name="token-matches-for-a-phrase-list"></a>Egy kifejezés listához tartozó token egyezése

A kifejezések listája mindig a jogkivonat szintjén érvényes. A következő táblázat azt mutatja be, hogy az **Ann** szót tartalmazó kifejezések listája az adott sorrendben azonos karakterek variációit érinti.


| **Anna** jogkivonat-variációja | A kifejezések listája megegyezik a jogkivonat megtalálása után |
|--------------------------|---------------------------------------|
| **ANN**<br>**aNN**<br>           | Igen – a token **Ann**                  |
| **Ann 's**                    | Igen – a token **Ann**                  |
| **Anne**                     | Nem – token **Anne**                  |

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>A modell mint szolgáltatás segít egy másik modellben

Hozzáadhat egy modellt (szándék vagy entitás) egy másik modell (cél vagy entitás) szolgáltatásként. Egy meglévő szándék vagy entitás szolgáltatásként való hozzáadásával olyan jól definiált koncepciót ad hozzá, amely tartalmaz címkével ellátott példákat.

A modell szolgáltatásként való hozzáadásakor a következőképpen állíthatja be a szolgáltatást:
* **[Kötelező megadni](#required-features)**. Meg kell találni egy szükséges szolgáltatást ahhoz, hogy a modellt vissza lehessen adni az előrejelzési végpontból.
* **[Globális](#global-features)**. A globális funkció a teljes alkalmazásra vonatkozik.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Ha egy entitást szolgáltatásként kíván használni egy szándékhoz

Vegyen fel egy entitást szolgáltatásként, ha az adott entitás észlelése jelentős a szándék szempontjából.

Ha például a cél egy olyan járat foglalása, mint a **BookFlight**, és az entitás a jegyekkel kapcsolatos információk (például az ülések száma, a forrás és a célhely), akkor a Ticket-Information entitásnak jelentős súlyt kell adnia a **BookFlight** szándék előrejelzéséhez.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Az entitás szolgáltatásként való használata egy másik entitáshoz

Egy entitást (A) a szolgáltatásként egy másik entitáshoz (B) kell hozzáadni, ha az adott entitás (A) észlelése jelentős az entitás (B) előrejelzéséhez.

Ha például egy szállítási címet tartalmazó entitás egy utcanév-alentitásban található, akkor az utcai címek alentitásának megállapítása jelentős súlyt biztosít a szállítási címek entitásának előrejelzéséhez.

* Szállítási címe (gépi tanulási entitás):

    * Utca száma (alentitás)
    * Utca címe (alentitás)
    * Város (alentitás)
    * Állam vagy megye (alentitás)
    * Ország/régió (alentitás)
    * Irányítószám (alentitás)

## <a name="nested-subentities-with-features"></a>Beágyazott alentitások funkciókkal

A gépi tanulási alentitások azt jelzik, hogy egy fogalom van jelen a szülő entitás számára. A szülő lehet egy másik alentitás vagy a felső entitás. Az alentitások értéke szolgáltatásként viselkedik a szülője számára.

Az alentitások tartalmazhatnak egy kifejezési listát és egy modellt (egy másik entitást) is.

Ha az alentitásban szerepel egy kifejezés lista, az növeli a koncepció szókincsét, de nem ad hozzá semmilyen információt az előrejelzés JSON-válaszához.

Ha az alentitás egy másik entitás szolgáltatásával rendelkezik, a JSON-válasz tartalmazza a másik entitás kinyert értékeit.


## <a name="required-features"></a>Szükséges szolgáltatások

Meg kell találni egy szükséges szolgáltatást ahhoz, hogy a modellt vissza lehessen adni az előrejelzési végpontból. Használjon kötelező szolgáltatást, ha tudja, hogy a bejövő adatainak meg kell egyezniük a szolgáltatással.

Ha a teljes szöveg nem egyezik meg a szükséges szolgáltatással, akkor nem lesz kibontva.

Egy kötelező funkció nem gépi tanulási entitást használ:

* Reguláris kifejezésű entitás
* Listaentitás
* Előre elkészített entitás

Ha biztos abban, hogy a modell az adataiban található, állítsa be a szolgáltatást igény szerint. Egy kötelező funkció nem ad vissza semmit, ha nem található.

Folytassa a szállítási címek példáját:

Szállítási címe (gépi megtanult entitás)

 * Utca száma (alentitás)
 * Utca címe (alentitás)
 * Utca neve (alentitás)
 * Város (alentitás)
 * Állam vagy megye (alentitás)
 * Ország/régió (alentitás)
 * Irányítószám (alentitás)

### <a name="required-feature-using-prebuilt-entities"></a>Kötelező funkció előre elkészített entitások használatával

Az előre elkészített entitások, például a város, az állam, az ország/régió általában egy lezárt készlet, ami azt jelenti, hogy az idő múlásával nem változnak. Ezek az entitások rendelkezhetnek a megfelelő ajánlott funkciókkal, és ezek a funkciók kötelezőként jelölhetők meg. A jelző azonban `isRequired` csak a hozzá tartozó entitáshoz van társítva, és nincs hatással a hierarchiára. Ha az előre elkészített alentitások szolgáltatás nem található, akkor ez nem befolyásolja a szülő entitás észlelését és visszaküldését.

Példa egy szükséges szolgáltatásra, érdemes lehet felderíteni a címeket. Érdemes lehet megfontolni az utca számának kötelezővé tételét. Ez lehetővé tenné a felhasználóknak "1 Microsoft Way" vagy "egy Microsoft Way" megadását, és mindkettő feloldja az "1" számot az utca száma alentitás számára. További információért tekintse meg az [előre felépített entitást ](luis-reference-prebuilt-entities.md) ismertető cikket.

### <a name="required-feature-using-list-entities"></a>Kötelező szolgáltatás a lista entitások használatával

A [lista entitások](reference-entity-list.md) a kanonikus nevek listáját használják a szinonimákkal együtt. Szükség esetén, ha a kihagyás nem tartalmazza a kanonikus nevet vagy a szinonimát, az entitás nem kerül vissza az előrejelzési végpont részeként.

Tegyük fel, hogy a vállalata csak korlátozott számú országhoz/régióhoz tartozó hajót tartalmaz. Létrehozhat egy list entitást, amely számos módszert tartalmaz az ügyfél számára az országra/régióra való hivatkozáshoz. Ha a LUIS nem talál pontos egyezést a kiírás szövegében, akkor az adott entitás (amely a lista entitás szükséges funkciójával rendelkezik) nem jelenik meg az előrejelzésben.

|Kanonikus név|Szinonimák|
|--|--|
|Egyesült Államok|Egyesült Államok<br>U. S. A<br>USA<br>USA<br>0|

Egy ügyfélalkalmazás, például egy csevegési robot, segítségre teheti a következő kérdéseket: Ez segít az ügyfélnek megérteni, hogy az ország/régió kiválasztása korlátozott és *kötelező*.

### <a name="required-feature-using-regular-expression-entities"></a>Reguláris kifejezési entitásokat használó kötelező funkció

A kötelező szolgáltatásként használt [reguláris kifejezési entitások](reference-entity-regular-expression.md) gazdag szöveg-egyeztetési képességeket biztosítanak.

A szállítási cím példában létrehozhat egy reguláris kifejezést, amely rögzíti az ország/régió postai irányítószámok szintaxisának szabályait.

## <a name="global-features"></a>Globális funkciók

Habár a leggyakoribb a funkció alkalmazása egy adott modellre, a szolgáltatás **globális szolgáltatásként** is konfigurálható a teljes alkalmazásra való alkalmazáshoz.

A globális szolgáltatás leggyakoribb felhasználási célja, hogy egy további szókincset adjon hozzá az alkalmazáshoz. Ha például az ügyfelek az elsődleges nyelvet használják, de az is előfordulhat, hogy egy adott kifejezésen belül más nyelvet kíván használni, hozzáadhat egy olyan funkciót, amely a másodlagos nyelvből származó szavakat tartalmaz.

Mivel a felhasználó a másodlagos nyelvet szeretné használni bármely szándék vagy entitás között, a másodlagos nyelv szavait adja hozzá a kifejezés listához. A kifejezések listáját globális szolgáltatásként konfigurálja.

## <a name="combine-features-for-added-benefit"></a>Funkciók összevonása a hozzáadott előnyökkel

Egy tulajdonság vagy fogalom leírásához több funkciót is használhat. Gyakori párosítást kell használni:

* Egy kifejezés-lista funkció: több kifejezést is használhat ugyanazon modell szolgáltatásként.
* Szolgáltatásként szolgáló modell: [előre összeépített entitás](luis-reference-prebuilt-entities.md), [reguláris kifejezés entitás](reference-entity-regular-expression.md), [entitás listázása](reference-entity-list.md). 

### <a name="example-ticket-booking-entity-features-for-a-travel-app"></a>Példa: egy utazási alkalmazás Ticket-foglalási entitásának funkciói  

Alapszintű példaként vegye fontolóra egy olyan alkalmazás foglalását, amely egy repülési foglalási _szándékot_ és egy Ticket-foglalási _entitást_ foglal le. A Ticket-Booking entitás rögzíti az információkat, hogy egy repülőjegyet foglaljon le egy foglalási rendszeren. 

A Ticket-Book gépi tanulási entitása két alentitással rendelkezik a forrás és a cél rögzítéséhez. A funkciókat hozzá kell adni az egyes alentitásokhoz, nem a legfelső szintű entitáshoz.

:::image type="content" source="media/luis-concept-features/ticket-booking-entity.png" alt-text="Ticketbooking-entitás sémája":::

A Ticket-Booking entitás egy gépi tanulásra szolgáló entitás, amelynek alentitásai, például a _forrás_ és a _cél_. Ezek az alentitások mind földrajzi helyet jeleznek. A hely kinyeréséhez, valamint a _forrás_ és a _cél_ közötti különbségtételhez minden alentitásnak rendelkeznie kell funkciókkal.

|Típus|Forrás alentitás |Cél alentitás|
|--|--|--|
|Modell szolgáltatásként|[geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3) előre elkészített entitás|[geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3) előre elkészített entitás|
|Kifejezések listája|**Forrás szavai**: `start at` , `begin from` , `leave`|**Cél szavai**: `to` ,,, `arrive` `land at` `go` , `going` , `stay` , `heading`|
|Kifejezések listája|Repülőtéri kódok – azonos lista a forrás és a cél számára|Repülőtéri kódok – azonos lista a forrás és a cél számára|
|Kifejezések listája|Repülőterek nevei – azonos lista a forrás és a cél számára|Repülőtéri kódok – azonos lista a forrás és a cél számára|

Ha azt tervezi, hogy a felhasználók a repülőtér-kódokat és a repülőterek nevét használják, mint a LUIS, akkor a kifejezések listáját mindkét típusú kifejezést használja. A repülőtéri kódok gyakoribbak lehetnek a Csevegőrobot beírt szövegekkel, míg a repülőtéri nevek gyakoribbak lehetnek a szóbeli beszélgetésekkel, például a beszédfelismerést támogató Csevegőrobot.

A funkciók megfeleltetési részleteit csak a modellekhez adja vissza, nem pedig a kifejezések listájához, mert a rendszer csak a modelleket adja vissza az előrejelzési JSON-ben.

#### <a name="ticket-booking-labeling-in-the-intent"></a>Jegy – a foglalás címkézése a szándékban

Miután létrehozta a gépi tanulási entitást, hozzá kell adnia egy példa hosszúságú kimondott szöveg egy szándékhoz, és fel kell címkéznie a szülő entitást és az összes alentitást.

A Ticket foglalási példához címkézze fel a példában szereplő hosszúságú kimondott szöveg az `TicketBooking` entitással és a szövegben található alentitásokkal.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity.png" alt-text="Címke – példa hosszúságú kimondott szöveg":::

### <a name="example-pizza-ordering-app"></a>Példa: Pizza rendezési alkalmazás

Egy másik Példaként vegyünk egy alkalmazást egy pizzéria-étterem számára, amely pizzás rendeléseket fogad, beleértve a Megrendeléses pizza típusának részleteit. Ha lehetséges, a pizza minden részletét ki kell vonni a megrendelés feldolgozásának befejezéséhez.

Ebben a példában a gépi tanulási entitás a beágyazott alentitások, a kifejezések listája, az előre elkészített entitások és az egyéni entitások esetében összetettebb.

:::image type="content" source="media/luis-concept-features/pizza-order-entity.png" alt-text="Pizza Order entitás sémája":::

Ez a példa az alentitások szintjén és az alentitások szintjének alárendelt funkciókat használja. Milyen szinten kapja meg, hogy milyen típusú kifejezéseket vagy modelleket tartalmaz a funkció az entitások kialakításának fontos részeként.

Habár az alentitások számos olyan kifejezést tartalmazhatnak, amelyek segítenek az entitás észlelésében, az egyes alentitások csak egy modellel rendelkeznek szolgáltatásként. Ebben a [pizza-alkalmazásban](https://github.com/Azure/pizza_luis_bot/blob/master/CognitiveModels/MicrosoftPizza.json)ezek a modellek elsősorban a listában szerepelnek.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity-pizza.png" alt-text="Pizza Order szándék a címkével ellátott példa hosszúságú kimondott szöveg":::

A helyesen címkézett példa hosszúságú kimondott szöveg úgy jelenik meg, hogy megmutassa az entitások beágyazásának módját. 


## <a name="best-practices"></a>Ajánlott eljárások

Ismerje meg az [ajánlott eljárásokat](luis-concept-best-practices.md).

## <a name="next-steps"></a>Következő lépések

* [Kiterjesztheti](schema-change-prediction-runtime.md) az alkalmazás modelljeit az előrejelzési futtatókörnyezetben.
* További információ a szolgáltatások hozzáadása a LUIS-alkalmazáshoz [című témakörben](luis-how-to-add-features.md) talál további információt.
