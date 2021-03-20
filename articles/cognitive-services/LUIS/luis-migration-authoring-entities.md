---
title: Migrálás v3 gépi tanulási entitásba
description: A v3 authoring egy új entitást biztosít, a gépi tanulási entitást, valamint lehetővé teszi a kapcsolatok hozzáadását a gépi tanulási entitáshoz, valamint az alkalmazás egyéb entitásait vagy funkcióit.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 667226770d25ef1687420b1c13bc71863f987e33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91324688"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrálás v3 authoring entitásba

A v3 authoring egy új entitást biztosít, a gépi tanulási entitást, valamint lehetővé teszi a kapcsolatok hozzáadását a gépi tanulási entitáshoz, valamint az alkalmazás egyéb entitásait vagy funkcióit.

## <a name="entities-are-decomposable-in-v3"></a>Az entitások kiállíthatók a v3-as verzióban

A v3 authoring API-kkal létrehozott entitások az [API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) -k vagy a portál használatával hozhatók létre, és a szülővel és gyermekekkel rendelkező rétegzett entitásokat is létrehozhatnak. A szülő ismert, hogy a gépi **tanulási entitás** , a gyermekeket pedig a gép megtanult entitásának **alentitásának** nevezzük.

Mindegyik alentitás egy gépi tanulási entitás is, de a funkciók további konfigurációs beállításai is elérhetők.

* A **szükséges szolgáltatások** olyan szabályok, amelyek garantálják, hogy az entitások kinyerve legyenek, amikor egy szolgáltatás megfelel. Ezt a szabályt a kötelező funkció határozza meg a modellhez:
    * [Előre elkészített entitás](luis-reference-prebuilt-entities.md)
    * [Reguláriskifejezés-entitás](reference-entity-regular-expression.md)
    * [Entitás listázása](reference-entity-list.md).

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Hogyan hasonlítják össze ezeket az új kapcsolatokat a v2 authoring használatával

V2 a szerzői műveletek hierarchikus és összetett entitásokat, valamint szerepköröket és szolgáltatásokat biztosítanak ugyanezen feladat végrehajtásához. Mivel az entitások, szolgáltatások és szerepkörök nem voltak explicit módon egymással összekapcsolva, nehéz volt megérteni, hogy a LUIS milyen módon utalt a kapcsolatokra az előrejelzés során.

A v3-as verzióban a kapcsolat explicit módon és az alkalmazás szerzője által lett kialakítva. Ez lehetővé teszi, hogy az alkalmazás szerzője:

* Vizuálisan megtudhatja, hogyan Jósolja meg a LUIS ezeket a kapcsolatokat, a példában hosszúságú kimondott szöveg
* Tesztelje ezeket a kapcsolatokat az [interaktív teszt ablaktáblával](luis-interactive-test.md) vagy a végponton
* Használja ezeket a kapcsolatokat az ügyfélalkalmazás egy jól strukturált, elnevezett, beágyazott [. JSON-objektumon keresztül.](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Tervezés

Az áttelepítés során vegye figyelembe a következőket az áttelepítési tervben:

* Készítsen biztonsági másolatot a LUIS-alkalmazásról, és végezze el az áttelepítést egy külön alkalmazáson. Ha egy v2 és v3 verziójú alkalmazás is elérhető egyszerre, akkor ellenőrizheti a szükséges módosításokat, és hatással lehet az előrejelzés eredményeire.
* Aktuális előrejelzési sikerességi mérőszámok rögzítése
* Aktuális irányítópult-információk rögzítése az alkalmazás állapotának pillanatképe
* Meglévő leképezések, entitások, kifejezések listája, mintázatok és batch-tesztek áttekintése
* A következő elemek migrálása **változtatás nélkül** történhet:
    * Leképezések
    * Entitások
        * Reguláriskifejezés-entitás
        * Listaentitás
    * Funkciók
        * Kifejezések listája
* A következő elemeket kell áttelepíteni a **változásokkal**:
    * Entitások
        * Hierarchikus entitás
        * Összetett entitás
    * Szerepkörök – a szerepkörök csak gépi tanulási (szülő) entitásra alkalmazhatók. A szerepkörök nem alkalmazhatók alentitásokra
    * A hierarchikus és összetett entitásokat használó batch-tesztek és-minták

Az áttelepítési terv tervezésekor hagyjon időt az utolsó gépi tanulási entitások áttekintésére, miután az összes hierarchikus és összetett entitás át lett telepítve. Míg az egyenes áttelepítés működni fog, miután elvégezte a módosítást, és áttekinti a Batch-teszt eredményeit, és előrejelzési JSON-t, a több egységesített JSON-t úgy is megteheti, hogy az ügyféloldali alkalmazáshoz tartozó végső adatokat különbözőképpen rendezi. Ez hasonló a kód újrabontásához, és ugyanazzal a felülvizsgálati folyamattal kell kezelni, mint a szervezet.

Ha nem rendelkezik batch-tesztekkel a v2-modellhez, és az áttelepítés részeként áttelepíti a Batch-teszteket a v3-modellre, nem fogja tudni ellenőrizni, hogy az áttelepítés milyen hatással lesz a végpont-előrejelzés eredményeire.

## <a name="migrating-from-v2-entities"></a>Áttelepítés v2 entitásokból

Ahogy elkezdi a v3 authoring modelre való áttérést, érdemes megfontolnia, hogyan váltson át a gépi tanulási entitásra és az alentitásokra és szolgáltatásokra.

A következő táblázat azt ismerteti, hogy mely entitásoknak kell áttérniük a v2-ből egy v3-es entitás kialakítására.

|V2 szerzői entitás típusa|V3 szerzői entitás típusa|Példa|
|--|--|--|
|Összetett entitás|Gépi megtanult entitás|[tudj meg többet](#migrate-v2-composite-entity)|
|Hierarchikus entitás|gépi tanulási entitás szerepköre|[tudj meg többet](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>V2 összetett entitás migrálása

A v2 kompozit minden gyermekének szerepelnie kell a v3 gépi tanulási entitás alentitásával. Ha az összetett gyermek egy előre elkészített, reguláris kifejezés vagy egy lista entitás, ezt kötelező funkcióként kell alkalmazni az alentitáson.

Összetett entitás gépi tanulási entitásba való áttelepítésének tervezésekor megfontolandó szempontok:
* A gyermek entitások nem használhatók mintázatban
* A gyermek entitások már nincsenek megosztva
* A gyermek entitásokat címkével kell ellátni, ha nem gépi megismertnek használták

### <a name="existing-features"></a>Meglévő szolgáltatások

Az összetett entitásban található szavak növelésére használt kifejezések listáját a Machine learning (szülő) entitás, az alentitás (gyermek) entitás vagy a szándék (ha a kifejezés lista csak egy szándékra vonatkozik) szolgáltatásként kell alkalmazni. Tervezze meg a funkció hozzáadását az entitáshoz, ahol a legjelentősebb lendületet kell adnia. Ne adja hozzá a szolgáltatást általános módon a gépi tanulási (szülő) entitáshoz, ha az nagyobb mértékben növeli az alentitások (gyermek) előrejelzését.

### <a name="new-features"></a>Új funkciók

A v3 authoring szolgáltatásban vegyen fel egy tervezési lépést, amely az entitások és a szándékok lehetséges funkcióinak kiértékelését teszi elérhetővé.

### <a name="example-entity"></a>Példa entitás

Ez az entitás csak példaként szolgál. A saját entitások áttelepítése más szempontokat is igényelhet.

Vegye fontolóra a v2 összetett verzióját a következőt használó pizzák módosítására `order` :
* előre elkészített datetimeV2 a szállítási időhöz
* kifejezések listája az egyes szavak, például a pizza, a pite, a kéreg és a feltöltések növeléséhez
* entitások listázása az olyan feltöltések észleléséhez, mint például a gombák, az olajbogyók, a pepperoni.

Erre az entitásra például a következőt kell kimondani:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

Az alábbi táblázat az áttelepítést mutatja be:

|V2 modellek|V3 modellek|
|--|--|
|Szülő-összetevő nevű entitás `Order`|Szülő-gépi tanulás nevű entitás `Order`|
|Gyermek előre elkészített datetimeV2|* Az előre elkészített entitás migrálása új alkalmazásba.<br>* A kötelező funkció hozzáadása a szülőhöz előre elkészített datetimeV2.|
|Gyermek-lista entitás a feltöltésekhez|* A List entitás migrálása új alkalmazásba.<br>* Ezt követően adjon hozzá egy kötelező szolgáltatást a szülőhöz a lista entitáshoz.|


## <a name="migrate-v2-hierarchical-entity"></a>A v2 hierarchikus entitás migrálása

A v2-authoring szolgáltatásban egy hierarchikus entitás lett megadva a LUIS-ben meglévő szerepkörök előtt. Mindkettő ugyanazt a célt szolgálja, hogy a környezet használata alapján kinyerje az entitásokat. Ha hierarchikus entitásokkal rendelkezik, a szerepköröket egyszerű entitásként tekintheti meg.

A v3 létrehozásakor:
* A szerepkör alkalmazható a gépi tanulás (szülő) entitásra.
* Egy szerepkör nem alkalmazható alentitásokra.

Ez az entitás csak példaként szolgál. A saját entitások áttelepítése más szempontokat is igényelhet.

Vegye fontolóra a v2 hierarchikus entitást a pizza módosításához `order` :
* ahol az egyes gyermekek az eredeti vagy a végső feltöltést határozzák meg

Erre az entitásra például a következőt kell kimondani:

`Change the topping from mushrooms to olives`

Az alábbi táblázat az áttelepítést mutatja be:

|V2 modellek|V3 modellek|
|--|--|
|Szülő-összetevő nevű entitás `Order`|Szülő-gépi tanulás nevű entitás `Order`|
|Gyermek-hierarchikus entitás eredeti és utolsó pizza-feltöltéssel|* Adja hozzá a szerepkört `Order` az egyes feltöltésekhez.|

## <a name="api-change-constraint-replaced-with-required-feature"></a>Az API-módosítási megkötés lecserélve a szükséges szolgáltatással

Ez a módosítás a Build konferencián 2020 májusában történt, és csak a v3 authoring API-kra vonatkozik, ahol az alkalmazások korlátozott funkciót használnak. Ha v2-ből a v3 authoring-re végez áttelepítést, vagy nem használta a v3 korlátozott funkciókat, ugorja át ezt a szakaszt.

**Funkció** – a meglévő entitások szolgáltatásként való megkövetelésének lehetősége egy másik modellre, és csak akkor kell kinyerni a modellt, ha az entitás észlelve van. A funkció nem módosult, de az API és a terminológia megváltozott.

|Előző terminológia|Új terminológia|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>Automatikus áttelepítés

A **19 2020. júniustól** kezdődően nem lehet megkötéseket létrehozni programozott módon az előző authoring API használatával, amely ezt a funkciót feltette.

A rendszer minden meglévő korlátozási funkciót automatikusan áttelepít a szükséges szolgáltatás-jelzőbe. Az előrejelzési API-hoz nem szükségesek programozási változások, és az előrejelzési pontosság minőségének változása nem változik.

#### <a name="luis-portal-changes"></a>A LUIS-portál változásai

A LUIS betekintő portál ezt a funkciót a **korlátozásként** hivatkozott. A jelenlegi LUIS-portál ezt a funkciót **kötelező szolgáltatásként** jelöli meg.

#### <a name="previous-authoring-api"></a>Korábbi szerzői API

Ezt a funkciót a rendszer az entitás definíciójának előnézetében az entitások **[létrehozása ALÁRENDELT API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** előzetes verziójának létrehozásával, az `instanceOf` entitások gyermekének a tulajdonságának használatával alkalmazta:

```json
{
    "name" : "dayOfWeek",
    "instanceOf": "datetimeV2",
    "children": [
        {
           "name": "dayNumber",
           "instanceOf": "number",
           "children": []
        }
    ]
}
```

#### <a name="new-authoring-api"></a>Új szerzői API

Ez a funkció a és a tulajdonságok használatával lett alkalmazva az **[entitás hozzáadása szolgáltatásbeli relációs API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** `featureName` -val `isRequired` . A tulajdonság értéke a `featureName` modell neve.

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>Következő lépések

* [Fejlesztői erőforrások](developer-reference-resource.md)
