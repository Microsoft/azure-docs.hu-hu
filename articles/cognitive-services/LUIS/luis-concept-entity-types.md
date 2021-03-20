---
title: Entity types – LUIS
description: Egy entitás kinyeri az adatait a felhasználótól az előrejelzési futtatókörnyezetben. Egy _opcionális_, másodlagos cél a szándék vagy más entitások előrejelzésének növelése az entitás szolgáltatásként való használatával.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398d18642052726af4d4920443bad515ec0b5bef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91316562"
---
# <a name="extract-data-with-entities"></a>Adatok kinyerése entitásokkal

Egy entitás kinyeri az adatait a felhasználótól az előrejelzési futtatókörnyezetben. Egy _opcionális_, másodlagos cél a szándék vagy más entitások előrejelzésének növelése az entitás szolgáltatásként való használatával.

Többféle típusú entitás létezik:

* [gépi tanulási entitás](reference-entity-machine-learned-entity.md) – ez az elsődleges entitás. Más entitások használata előtt meg kell terveznie a sémát az entitás típusával.
* A nem gépi tanulás kötelező [funkcióként](luis-concept-feature.md) használható – a szöveges egyezések, a minták egyezései vagy az előre elkészített entitások észlelése
* [Minta. any](#patternany-entity) – a szabad formátumú szöveg kibontása, például a könyv címeinek [mintából](reference-entity-pattern-any.md) való kinyerése

a gépi tanulási entitások az kinyerési lehetőségek széles skáláját biztosítják. A nem gépi tanulást használó entitások szöveges egyeztetés szerint működnek, és a gépi tanulási entitások vagy szándékok [kötelező funkciójaként](#design-entities-for-decomposition) használatosak.

## <a name="entities-represent-data"></a>Az entitások az adathalmazokat jelölik

Az entitások olyan adatok, amelyeket le szeretne kérni a kiértékelésből, például a neveket, a dátumokat, a terméknévokat vagy a szavak jelentős csoportjait. A Kimondás számos entitást tartalmazhat, vagy egyetlen sem. Az ügyfélalkalmazás feladatának elvégzéséhez szükség _lehet_ az adatokra.

Az entitásokat következetesen kell megcímkézni a modell minden egyes szándékának összes képzési hosszúságú kimondott szöveg.

 Megadhatja saját entitásait, vagy az előre elkészített entitások használatával időt takaríthat meg a gyakori fogalmak, például a [datetimeV2](luis-reference-prebuilt-datetimev2.md), a [sorszám](luis-reference-prebuilt-ordinal.md), az [e-mail](luis-reference-prebuilt-email.md)és a [telefonszámok](luis-reference-prebuilt-phonenumber.md)számára.

|Beszédelem|Entitás|Adatok|
|--|--|--|
|3 jegy vásárlása a New Yorkba|Előre elkészített szám<br>Cél|3<br>New York|


### <a name="entities-are-optional-but-recommended"></a>Az entitások nem kötelezőek, de ajánlottak

A [szándékok](luis-concept-intent.md) megadásakor az entitások megadása nem kötelező. Nem kell entitásokat létrehoznia az alkalmazás minden fogalmához, de csak azokra az esetekre, amikor az ügyfélalkalmazás igényli az adatkezelést, vagy ha az entitás egy másik entitás vagy szándék felé irányuló célzásként vagy jelzésként működik.

Az alkalmazás fejlődése és az új adatkezelési igények azonosítása érdekében később is hozzáadhat megfelelő entitásokat a LUIS-modellhez.

<a name="entity-compared-to-intent"></a>

## <a name="entity-represents-data-extraction"></a>Az entitás kibontást jelöl

Az entitás egy adatkoncepciót képvisel _a teljes_ kifejezésen belül. A cél a _teljes Kimondás_.

Vegye figyelembe a következő négy hosszúságú kimondott szöveg:

|Beszédelem|Előre jelzett szándék|Kinyert entitások|Magyarázat|
|--|--|--|--|
|Súgó|segítség|-|Nincs kibontva.|
|Küldés valami|sendSomething|-|Nincs kibontva. A modell nem rendelkezik a kinyeréséhez szükséges funkcióval `something` , és nincs megadva címzett.|
|Bob a present küldése|sendSomething|`Bob`, `present`|A modell kibontása az `Bob` előre elkészített entitás kötelező funkciójának hozzáadásával `personName` . A rendszer gépi tanulási entitást használ a kinyeréséhez `present` .|
|Bob a csokoládét tartalmazó doboz elküldése|sendSomething|`Bob`, `box of chocolates`|A két fontos adatot, `Bob` a pedig a `box of chocolates` gépi tanulási entitások kinyerték.|

## <a name="label-entities-in-all-intents"></a>Entitások címkézése az összes leképezésben

Az entitások az előre jelzett szándéktól függetlenül kinyerik az adatmennyiséget. Ügyeljen arra, hogy az _összes példa hosszúságú kimondott szöveg az összes cél_ címkével. A `None` szándék hiányában az entitások címkézése zavart okoz, még akkor is, ha a többi cél esetében sokkal több betanítási hosszúságú kimondott szöveg van.

## <a name="design-entities-for-decomposition"></a>Entitások megtervezése a dekompozícióhoz

a gépi tanulással rendelkező entitások lehetővé teszik az alkalmazás sémájának megtervezését a dekompozícióhoz, és a nagyméretű koncepciók alentitásokra való feltörését.

A kiépítésének megtervezése lehetővé teszi a LUIS számára, hogy a nagymértékű entitás-feloldást adja vissza az ügyfélalkalmazás számára. Ez lehetővé teszi, hogy az ügyfélalkalmazás az üzleti szabályokra koncentráljon, és az adatfelbontást a LUIS-re bízza.

A gépi tanulási entitás eseményindítói a példa hosszúságú kimondott szöveg keresztül megszerzett kontextus alapján.

a [**gépi tanulási entitások**](tutorial-machine-learned-entity.md) a legfelső szintű kivonók. Az alentitások a gépi tanulási entitások alárendelt entitásai.

## <a name="effective-machine-learned-entities"></a>Hatékony gépi megtanult entitások

A gépi megtanult entitások hatékony felépítése:

* A címkézésnek konzisztensnek kell lennie a leképezések között. Ebbe beletartozik még a hosszúságú kimondott szöveg is, **amelyet az adott** entitást nem tartalmazó szándékban adhat meg. Ellenkező esetben a modell nem fogja tudni hatékonyan meghatározni a sorozatot.
* Ha van olyan gép, amely alentitásokkal rendelkezik, győződjön meg arról, hogy az entitás és az alentitások különböző megrendelései és változatai jelennek meg a címkézett hosszúságú kimondott szöveg. A címkével ellátott példa hosszúságú kimondott szöveg tartalmaznia kell az összes érvényes űrlapot, és tartalmaznia kell azokat az entitásokat, amelyek megjelentek és hiányoznak, és a megjelölésen belül is megtalálhatók.
* Kerülje az entitások egy nagyon rögzített készlethez való túlillesztését. Ha a modell nem jól általánosít, akkor a rendszer akkor is **megtörténik,** ha a gépi tanulási modellek gyakori problémát jelentenek. Ez azt jelenti, hogy az alkalmazás nem fog megfelelően működni az új adatmennyiségen. Viszont a címkével ellátott példa hosszúságú kimondott szöveg eltérőnek kell lennie, így az alkalmazás képes általánosítani az Ön által megadott példákon túl. A modell megváltozásakor a különböző alentitásoknak is változónak kell lennie, hogy csak a bemutatott példák helyett inkább a koncepciót gondolják.

## <a name="effective-prebuilt-entities"></a>Érvényes előre összeépített entitások

A gyakori adatok, például az [előre elkészített entitások](luis-reference-prebuilt-entities.md)által biztosított hatékony entitások létrehozásához a következő eljárást javasoljuk.

Javíthatja az adatgyűjtést úgy, hogy a saját adatait szolgáltatásként hozza ki. Így az adatokban szereplő összes további címke megtudhatja, hogy hol találhatók a Személynevek az alkalmazásban.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Az entitások típusai

A szülő alentitásának gépi tanulási entitásnak kell lennie. Az alentitások nem gépi tanulást használó entitásokat is használhatnak [szolgáltatásként](luis-concept-feature.md).

Válassza ki az entitást az Adatkivonatok és a kinyerés utáni megjelenítésük alapján.

|Entitástípus|Cél|
|--|--|
|[**Gépi megtanult**](tutorial-machine-learned-entity.md)|Beágyazott, összetett adatok kinyerése a címkével ellátott példákból. |
|[**Lista**](reference-entity-list.md)|A **pontos szöveges egyezéssel** kinyert elemek és szinonimáik listája.|
|[**Minta. any**](#patternany-entity)|Nehéz megállapítani, hogy az entitás véget ért-e, mert az entitás szabad formátumú. Csak [mintákban](luis-concept-patterns.md)érhető el.|
|[**Prebuilt**](luis-reference-prebuilt-entities.md)|Már betanítva bizonyos típusú adatok, például URL-cím vagy e-mailek kinyerésére. Ezen előre összeépített entitások némelyike a nyílt forráskódú [felismerők – Text](https://github.com/Microsoft/Recognizers-Text) projektben van meghatározva. Ha az adott kulturális környezet vagy entitás jelenleg nem támogatott, akkor hozzájárul a projekthez.|
|[**Reguláris kifejezés**](reference-entity-regular-expression.md)|Reguláris kifejezést használ a **pontos szöveges egyeztetéshez**.|


## <a name="extraction-versus-resolution"></a>Extrakció és feloldás

Az entitások adatok kinyerése, amikor az adatok megjelennek a teljes tartalomban. Az entitások nem módosítják vagy oldják fel az adatfeldolgozást. Az entitás nem biztosít semmilyen felbontást, ha a szöveg érvényes érték az entitás számára, vagy nem.

Lehetőség van a kivonás feloldására is, de tisztában kell lennie azzal, hogy az alkalmazás képes a variációk és a hibák elleni védekezésre.

Az entitások listázása és a reguláris kifejezés (szöveges megfeleltetésű) entitások [kötelező funkciókként](luis-concept-feature.md#required-features) használhatók egy alentitáshoz, és szűrőként viselkednek a kinyerésben. Ezt körültekintően kell használni, mivel nem akadályozza meg, hogy az alkalmazás előre megjósolja a képességét.

## <a name="extracting-contextually-related-data"></a>Kontextussal kapcsolatos adatok kinyerése

A Kimondás egy olyan entitás két vagy több előfordulását is tartalmazhatja, amelyben az információ jelentése a teljes kontextuson belüli kontextuson alapul. Ilyen például a két földrajzi hellyel, a forrással és a rendeltetéssel rendelkező repülés foglalásának kimondása.

`Book a flight from Seattle to Cairo`

A két helyet úgy kell kinyerni, hogy az ügyfél-alkalmazás tudja az egyes helyek típusát, hogy elvégezze a jegy megvásárlását.

A forrás és a cél kinyeréséhez hozzon létre két alentitást a Ticket Order Machine-learning entitás részeként. Mindegyik alentitáshoz hozzon létre egy szükséges szolgáltatást, amely geographyV2 használ.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>Az entitások korlátozásához szükséges szolgáltatások használata

További információ a [szükséges funkciókról](luis-concept-feature.md)

## <a name="patternany-entity"></a>Pattern.any entitás

A minta. any csak egy [mintában](luis-concept-patterns.md)érhető el.

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>Az entitásokra vonatkozó alkalmazások korlátainak túllépése

Ha a [korlátnál](luis-limits.md#model-limits)nagyobbra van szüksége, forduljon az ügyfélszolgálathoz. Ehhez gyűjtsön részletes információkat a rendszeréről, lépjen a [Luis](luis-reference-regions.md#luis-website) webhelyére, és válassza a **támogatás** lehetőséget. Ha az Azure-előfizetése támogatási szolgáltatásokat tartalmaz, forduljon az [Azure technikai támogatási](https://azure.microsoft.com/support/options/)szolgálatához.

## <a name="entity-prediction-status-and-errors"></a>Entitás-előrejelzési állapot és hibák

A LUIS-portál azt mutatja be, hogy az entitás eltérő egyed-előrejelzéssel rendelkezik, mint a kiválasztáshoz kiválasztott entitás. Ez a különböző pontszám az aktuálisan betanított modellen alapul. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="A LUIS-portál azt mutatja be, hogy az entitás eltérő egyed-előrejelzéssel rendelkezik, mint a kiválasztáshoz kiválasztott entitás.":::

A hibát jelző szöveg kiemelve jelenik meg a példán belül, és a példában szereplő kifejezés a jobbra mutató hibaüzenetet jeleníti meg, piros háromszögként megjelenítve. 

Ezekkel az információkkal az alábbi műveleteket hajthatja végre az entitások hibáinak elhárításához:
* A Kiemelt szöveg téves címkével van ellátva. A javításhoz, felülvizsgálathoz, javításhoz és újratanításhoz. 
* Hozzon létre egy [szolgáltatást](luis-concept-feature.md) az entitás számára az entitás koncepciójának azonosítása érdekében
* További [példa hosszúságú kimondott szöveg](luis-concept-utterance.md) és címke hozzáadása az entitáshoz
* [Tekintse át az aktív tanulási javaslatokat](luis-concept-review-endpoint-utterances.md) az előrejelzési végponton fogadott bármely hosszúságú kimondott szöveg, amely segíthet az entitás koncepciójának azonosításában.

## <a name="next-steps"></a>Következő lépések

Ismerje meg a jó [hosszúságú kimondott szöveg](luis-concept-utterance.md)kapcsolatos fogalmakat.

Az entitások LUIS-alkalmazáshoz való hozzáadásával kapcsolatos további tudnivalókért tekintse meg az [entitások hozzáadása](luis-how-to-add-entities.md) című témakört.

Tekintse meg az [oktatóanyag: strukturált adatok kinyerése a felhasználóktól a Language Understanding (Luis) gépi tanulási entitásokkal](tutorial-machine-learned-entity.md) című témakört, amelyből megtudhatja, hogyan lehet kinyerni a strukturált adatok kinyerését a gépi tanulási entitás használatával.

