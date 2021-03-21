---
title: Adatvezérelt stílusú kifejezések az Android Maps szolgáltatásban | Microsoft Azure térképek
description: További információ az adatvezérelt stílusú kifejezésekről. Tekintse meg, hogyan használhatja ezeket a kifejezéseket a Azure Maps Android SDK-ban a stílusok a Maps-ben való beállításához.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 1babf1feb550109486089c45469ab4ce32f72cb3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097414"
---
# <a name="data-driven-style-expressions-android-sdk"></a>Adatvezérelt stílusú kifejezések (Android SDK)

A kifejezések lehetővé teszik, hogy üzleti logikát alkalmazzon az adatforrásban lévő egyes alakzatokban definiált tulajdonságokat betekintő formázási lehetőségekre. A kifejezések adatforrásban vagy rétegben szűrhetik az adatszűrést. A kifejezések tartalmazhatnak feltételes logikát, például if-utasítások. A és a segítségével a következő műveleteket végezheti el az adatkezeléshez: karakterlánc-operátorok, logikai operátorok és matematikai operátorok.

Az adatvezérelt stílusok csökkentik az üzleti logika stíluson belüli megvalósításához szükséges kód mennyiségét. A rétegekkel való használatkor a kifejezések kiértékelése a renderelés időpontjában történik egy külön szálon. Ez a funkció nagyobb teljesítményt nyújt, mint az üzleti logika kiértékelése a felhasználói felületi szálon.

A Azure Maps Android SDK szinte teljesen azonos stílusú kifejezéseket támogat, mint a Azure Maps web SDK, így az [adatvezérelt stílusú kifejezésekben (web SDK)](data-driven-style-expressions-web-sdk.md) megjelenő fogalmak az Android-alkalmazásokban is elvégezhetők. A Azure Maps Android SDK-ban található összes stílus kifejezés a névtér alatt érhető el `com.microsoft.azure.maps.mapcontrol.options.Expression` . Számos különböző típusú kifejezés létezik.

| Kifejezések típusa | Description |
|---------------------|-------------|
| [Logikai kifejezések](#boolean-expressions) | A logikai kifejezések logikai operátorok egy készletét biztosítják a logikai összehasonlítások kiértékeléséhez. |
| [Színkifejezések](#color-expressions) | A színkifejezések egyszerűbbé teszik a színértékek létrehozását és kezelését. |
| [Feltételes kifejezések](#conditional-expressions) | A feltételes kifejezések olyan logikai műveleteket biztosítanak, amelyek például if-utasítások. |
| [Adatkifejezések](#data-expressions) | Hozzáférést biztosít a szolgáltatásban található tulajdonság-információhoz. |
| [Interpolációs és Step kifejezések](#interpolate-and-step-expressions) | Az interpolációs és a Step kifejezésekkel az értékek kiszámíthatók az interpolált görbe vagy a Step függvény használatával. |
| [JSON-alapú kifejezések](#json-based-expressions) | Megkönnyíti az Android SDK-val a web SDK-hoz létrehozott stílusú nyers JSON-alapú kifejezések újrafelhasználását. |  
| [Réteg-specifikus kifejezések](#layer-specific-expressions) | Olyan speciális kifejezések, amelyek csak egyetlen rétegre érvényesek. |
| [Matematikai kifejezések](#math-expressions) | Matematikai operátorokat biztosít az adatvezérelt számítások végrehajtásához a kifejezés-keretrendszeren belül. |
| [Karakterlánc-operátor kifejezései](#string-operator-expressions) | A karakterlánc-operátor kifejezései olyan karakterlánc-átalakítási műveleteket hajtanak végre, mint például az Összefűzés és az átalakítás. |
| [Típuskifejezések](#type-expressions) | A Type kifejezések a különböző adattípusok (például karakterláncok, számok és logikai értékek) tesztelésére és átalakítására szolgáló eszközöket biztosítanak. |
| [Változó kötési kifejezések](#variable-binding-expressions) | A változó kötési kifejezések egy változóban lévő számítás eredményét tárolják, és többször is hivatkoznak egy kifejezésben, anélkül, hogy újra kellene számítani a tárolt értéket. |
| [Nagyítás kifejezése](#zoom-expression) | Lekéri a Térkép jelenlegi nagyítási szintjét renderelési időben. |

> [!NOTE]
> A kifejezések szintaxisa nagyjából azonos a Java és a Kotlin. Ha a dokumentáció a Kotlin értékre van állítva, de a Java-kód blokkolja, a kód mindkét nyelven megegyezik.

A dokumentum jelen szakaszának minden példája a következő funkcióval szemlélteti a kifejezések használatának különböző módszereit.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

A következő kód bemutatja, hogyan hozhatja létre manuálisan a GeoJSON szolgáltatást egy alkalmazásban.

::: zone pivot="programming-language-java-android"

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add properties to the feature.
feature.addNumberProperty("id", 123)
feature.addStringProperty("entityType", "restaurant")
feature.addNumberProperty("revenue", 12345)
feature.addStringProperty("subTitle", "Building 40")
feature.addNumberProperty("temperature", 64)
feature.addStringProperty("title", "Cafeteria")
feature.addStringProperty("zoneColor", "purple")

val abcArray = JsonArray()
abcArray.add("a")
abcArray.add("b")
abcArray.add("c")

feature.addProperty("abcArray", abcArray)

val array1 = JsonArray()
array1.add("a")
array1.add("b")

val array2 = JsonArray()
array1.add("x")
array1.add("y")

val array2d = JsonArray()
array2d.add(array1)
array2d.add(array2)

feature.addProperty("array2d", array2d)

val style = JsonObject()
style.addProperty("fillColor", "red")

feature.addProperty("_style", style)
```

::: zone-end

A következő kód bemutatja, hogyan deszerializálhatja a JSON-objektum sztringesített verzióját egy alkalmazás GeoJSON szolgáltatásában.

::: zone pivot="programming-language-java-android"

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}"

val feature = Feature.fromJson(featureString)
```

::: zone-end

## <a name="json-based-expressions"></a>JSON-alapú kifejezések

A Azure Maps web SDK olyan adatvezérelt stílusú kifejezéseket is támogat, amelyek JSON-tömb használatával vannak megjelenítve. Ugyanezek a kifejezések újra létrehozhatók az `Expression` Android SDK natív osztályának használatával. Alternatív megoldásként ezeket a JSON-alapú kifejezéseket egy, a `JSON.stringify` metódusba átadott webes függvénnyel is át lehet alakítani karakterlánccá `Expression.raw(String rawExpression)` . Tegyük fel például, hogy a következő JSON-kifejezést használja.

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

A fenti kifejezés sztringesített-verziója a következő lesz `"['get','title']"` , és az Android SDK-ba is beolvasható.

::: zone pivot="programming-language-java-android"

```java
Expression exp = Expression.raw("['get','title']")
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val exp = Expression.raw("['get','title']")
```

::: zone-end

Ennek a módszernek a használatával könnyedén újrahasznosíthatja a Azure Mapst használó mobil-és webalkalmazások közötti stílusú kifejezéseket.

Ez a videó áttekintést nyújt a Azure Maps adatvezérelt stílusáról.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>Adatkifejezések

Az adatkifejezések hozzáférést biztosítanak a szolgáltatásban található tulajdonság-értékekhez.

| Expression | Visszatérési típus | Description |
|------------|-------------|-------------|
| `accumulated()` | szám | Lekérdezi a fürt eddig felhalmozott tulajdonságának értékét. |
| `at(number | Expression, Expression)` | érték | Egy elem lekérése egy tömbből. |
| `geometryType()` | sztring | A szolgáltatás geometriai típusának beolvasása: pont, multipoint, LineString, MultiLineString, sokszög és többsokszög. |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | érték | A tulajdonság értékének beolvasása a megadott objektum tulajdonságaiból. Null értéket ad vissza, ha a kért tulajdonság hiányzik. |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | Meghatározza, hogy a szolgáltatás tulajdonságai rendelkeznek-e a megadott tulajdonsággal. |
| `id()` | érték | A szolgáltatás AZONOSÍTÓjának beolvasása, ha rendelkezik ilyennel. |
| `in(string | number | Expression, Expression)` | boolean | Meghatározza, hogy egy elem létezik-e tömbben |
| `length(string | Expression)` | szám | Egy karakterlánc vagy tömb hosszának beolvasása. |
| `properties()`| érték | Lekéri a szolgáltatás tulajdonságai objektumot. |

Az Android SDK nem támogatja a következő web SDK stílusú kifejezéseket:

- index-of
- szelet

**Példák**

Egy szolgáltatás tulajdonságai közvetlenül egy kifejezés használatával érhetők el egy kifejezésben `get` . Ez a példa a `zoneColor` szolgáltatás értékét használja a buborékdiagram szín tulajdonságának megadásához.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
)
```

::: zone-end

A fenti példa megfelelően fog működni, ha az összes funkció rendelkezik a `zoneColor` tulajdonsággal. Ha nem, a szín valószínűleg vissza fog térni a "fekete" értékre. A tartalék szín módosításához használjon `switchCase` kifejezést a `has` kifejezéssel együtt annak ellenőrzéséhez, hogy a tulajdonság létezik-e. Ha a tulajdonság nem létezik, egy tartalék színt ad vissza.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
)
```

::: zone-end

A buborék és a szimbólum rétegek alapértelmezés szerint az adatforrás összes alakzatának koordinátáit jelenítik meg. Ez a viselkedés kiemelheti a sokszög vagy a vonal csúcspontját. A `filter` réteg beállításával korlátozhatja az általa megjelenített szolgáltatások geometriájának típusát egy `geometryType` logikai kifejezésen belüli kifejezés használatával. Az alábbi példa egy buborék réteget korlátozza, hogy csak a `Point` funkciók legyenek megjelenítve.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
)
```

::: zone-end

A következő példa lehetővé teszi a `Point` és a `MultiPoint` szolgáltatások megjelenítését.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
)
```

::: zone-end

Hasonlóképpen a sokszögek körvonalai is megjelennek a sorokban. Ha le szeretné tiltani ezt a viselkedést egy vonal rétegben, adjon hozzá egy olyan szűrőt, amely csak a `LineString` és a `MultiLineString` funkciókat engedélyezi.  

Íme néhány további példa az adatkifejezések használatára:

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>Matematikai kifejezések

A matematikai kifejezések matematikai operátorokat biztosítanak az adatvezérelt számítások végrehajtásához a kifejezés keretrendszerében.

| Expression | Visszatérési típus | Description |
|------------|-------------|-------------|
| `abs(number | Expression)` | szám | Kiszámítja a megadott szám abszolút értékét. |
| `acos(number | Expression)` | szám | Kiszámítja a megadott szám szám arkusz koszinuszát. |
| `asin(number | Expression)` | szám | Kiszámítja a megadott szám arkusz szinuszát. |
| `atan(number | Expression)` | szám | Kiszámítja a megadott szám arkusz tangensét. |
| `ceil(number | Expression)` | szám | Felfelé kerekíti a számot a következő egész számra. |
| `cos(number | Expression)` | szám | Kiszámítja a megadott szám cos-számát. |
| `division(number, number)` \| `division(Expression, Expression)` | szám | Az első szám felosztása a második szám alapján. Web SDK-val egyenértékű kifejezés: `/` |
| `e()` | szám | A matematikai állandót adja vissza `e` . |
| `floor(number | Expression)` | szám | Lefelé kerekíti a számot az előző egész egész számra. |
| `log10(number | Expression)` | szám | Kiszámítja a megadott szám 10-es alapú logaritmusát. |
| `log2(number | Expression)` | szám | Kiszámítja a megadott szám alap-két logaritmusát. |
| `ln(number | Expression)` | szám | A megadott szám természetes alapú logaritmusát számítja ki. |
| `ln2()` | szám | A matematikai állandót adja vissza `ln(2)` . |
| `max(numbers... | expressions...)` | szám | Kiszámítja a megadott Számsorozatok maximális számát. |
| `min(numbers... | expressions...)` | szám | Kiszámítja a minimális számot a megadott számú készletben. |
| `mod(number, number)` \| `mod(Expression, Expression)` | szám | Kiszámítja a maradékot az első szám második számmal való osztásakor. Web SDK-val egyenértékű kifejezés: `%` |
| `pi()` | szám | A matematikai állandót adja vissza `PI` . |
| `pow(number, number)` \| `pow(Expression, Expression)` | szám | Kiszámítja a második szám hatványára emelt első érték értékét. |
| `product(numbers... | expressions...)` | szám | A megadott számok összeszorzása. Web SDK-val egyenértékű kifejezés: `*` |
| `round(number | Expression)` | szám | A számot a legközelebbi egész számra kerekíti. A félúton lévő értékek a nullától távolabbi távolságra vannak kerekítve. Például `round(-1.5)` kiértékeli a következőt: `-2` . |
| `sin(number | Expression)` | szám | Kiszámítja a megadott szám szinuszát. |
| `sqrt(number | Expression)` | szám | Kiszámítja a megadott szám négyzet gyökerét. |
| `subtract(number | Expression` | szám | A megadott szám szerinti 0 kivonása. |
| `subtract(number | Expression, number | Expression)` | szám | Kivonja az első számokat a második szám alapján. |
| `sum(numbers... | expressions...)` | szám | Kiszámítja a megadott számok összegét. |
| `tan(number | Expression)` | szám | Kiszámítja a megadott szám tangensét. |

## <a name="boolean-expressions"></a>Logikai kifejezések

A logikai kifejezések logikai operátorok egy készletét biztosítják a logikai összehasonlítások kiértékeléséhez.

Az értékek összehasonlításakor az összehasonlítás szigorúan be van írva. A különböző típusú értékek mindig egyenlőtlennek számítanak. Azok az esetek, amelyekben a típusok ismertek, hogy az elemzési idő eltérő, és elemzési hibát eredményez.

| Expression | Visszatérési típus | Description |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | Visszaadja `true` , ha az összes bemenet `true` , `false` ellenkező esetben. |
| `any(Expression...)` | boolean | Visszaadja `true` , ha a bemenetek bármelyike `true` , `false` ellenkező esetben. |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Visszaadja `true` , ha a bemeneti értékek egyenlőek, `false` ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Azt adja vissza `true` , hogy az első bemenet szigorúan nagyobb-e, mint a második, `false` ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Akkor adja vissza `true` , ha az első bemenet nagyobb vagy egyenlő, mint a második, `false` ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Akkor adja vissza `true` , ha az első bemenet szigorúan kisebb a másodiknál, `false` ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Azt adja vissza `true` , hogy az első bemenet kisebb-e vagy egyenlő-e a másodikval, `false` ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Visszaadja `true` , ha a bemeneti értékek nem egyenlőek, `false` ellenkező esetben. |
| `not(Expression | boolean)` | boolean | Logikai tagadás. Visszaadja `true` , ha a bemenet `false` , és `false` Ha a bemenet szerepel `true` . |

## <a name="conditional-expressions"></a>Feltételes kifejezések

A feltételes kifejezések olyan logikai műveleteket biztosítanak, amelyek például if-utasítások.

A következő kifejezések feltételes logikai műveleteket hajtanak végre a bemeneti adatokon. A kifejezés például " `switchCase` IF/then/Else" logikát biztosít, miközben a `match` kifejezés olyan, mint a "Switch-utasítás".

### <a name="switch-case-expression"></a>Váltás esetének kifejezése

A `switchCase` kifejezés olyan feltételes kifejezés típusa, amely "If/then/Else" logikát biztosít. Az ilyen típusú kifejezés lépései a logikai feltételek listáján keresztül jelennek meg. Az első logikai feltétel kimeneti értékét adja vissza az igaz érték kiértékeléséhez.

A következő pseudocode határozzák meg a kifejezés szerkezetét `switchCase` .

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**Példa**

A következő példa különböző logikai feltételeken halad végig, amíg meg nem találja a kiértékelését `true` , majd visszaadja a hozzá tartozó értéket. Ha egyetlen logikai feltétel sincs kiértékelve `true` , a rendszer visszaadja a tartalék értéket.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
)
```

::: zone-end

### <a name="match-expression"></a>Egyezés kifejezése

A `match` kifejezés olyan feltételes kifejezés, amely switch-utasítást, például logikát biztosít. A bemenet bármely kifejezés lehet, például `get( "entityType")` egy sztringet vagy egy számot ad vissza. Minden leállításhoz olyan címkének kell tartoznia, amely vagy egy literális érték, vagy egy literális értékből álló tömb, amelynek értékének minden sztringnek vagy számnak kell lennie. A bemenet megegyezik, ha a tömb bármelyik értéke megegyezik. Minden leállítási címkének egyedinek kell lennie. Ha a bemeneti típus nem egyezik a címkék típusával, az eredmény az alapértelmezett tartalék érték lesz.

A következő pseudocode határozzák meg a kifejezés szerkezetét `match` .

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**Példák**

A következő példa egy `entityType` buborék rétegben lévő pont funkció tulajdonságát keresi meg. Ha egyezést talál, a rendszer a megadott értéket adja vissza, vagy visszaadja a tartalék értéket.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

Az alábbi példa egy tömböt használ a címkék egy halmazának listázásához, amelynek minden esetben ugyanazt az értéket kell visszaadnia. Ez a megközelítés sokkal hatékonyabb, mint a címkék egyenkénti listázása. Ebben az esetben, ha a `entityType` tulajdonság "étterem" vagy "grocery_store", a "piros" színt adja vissza.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(arrayOf("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

### <a name="coalesce-expression"></a>Egyesítő kifejezés

Egy `coalesce` kifejezés a kifejezések egy halmazán halad át, amíg az első nem null értéket nem szerzi be, és az értéket adja vissza.

A következő pseudocode határozzák meg a kifejezés szerkezetét `coalesce` .

```java
coalesce(Expression... input)
```

**Példa**

Az alábbi példa egy `coalesce` kifejezést használ `textField` egy szimbólum réteg beállításának beállításához. Ha a `title` tulajdonság hiányzik a szolgáltatásból, vagy a értékre van állítva `null` , a kifejezés ezután megpróbálja megkeresni a `subTitle` tulajdonságot, ha a hiányzó, vagy pedig `null` visszaesik egy üres karakterláncra.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
)
```

::: zone-end

## <a name="type-expressions"></a>Típuskifejezések

A Type kifejezések a különböző adattípusok (például karakterláncok, számok és logikai értékek) tesztelésére és átalakítására szolgáló eszközöket biztosítanak.

| Expression | Visszatérési típus | Description |
|------------|-------------|-------------|
| `array(Expression)` | Objektum [] | Azt állítja be, hogy a bemenet egy tömb. |
| `bool(Expression)` | boolean | Azt állítja be, hogy a bemeneti érték logikai. |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | válogató | A területi beállításoktól függő összehasonlító műveletekben használandó rendezést adja vissza. A kis-és nagybetűket megkülönböztető beállítások alapértelmezett értéke false (hamis). A területi beállítás argumentum a használni kívánt területi beállítás IETF nyelvi címkéjét adja meg. Ha nincs megadva, az alapértelmezett területi beállítás lesz érvényben. Ha a kért területi beállítás nem érhető el, a leválogató rendszer által definiált tartalék területi beállítást fog használni. A helyi visszaállítási viselkedés eredményének teszteléséhez használja a megoldott területi beállítást.  |
| `literal(boolean \| number \| string \| Object \| Object[])` | logikai \| szám \| sztring \| objektum \| objektum [] | Egy konstans tömb vagy objektum értékét adja vissza. Ezzel a kifejezéssel megakadályozható, hogy egy tömb vagy objektum kifejezésként legyen kiértékelve. Erre akkor van szükség, ha egy tömböt vagy objektumot egy kifejezésnek kell visszaadnia. |
| `number(Expression)` | szám | Azt állítja be, hogy a bemeneti érték egy szám. |
| `object(Expression)` | Objektum | Azt állítja be, hogy a bemeneti érték egy objektum. |
| `string(Expression)` | sztring | Azt állítja be, hogy a bemeneti érték egy karakterlánc. |
| `toArray(Expression)` | Objektum [] | Átalakítja a kifejezést egy JSON-objektum tömbbe. |
| `toBool(Expression)` | boolean | A bemeneti értéket logikai értékre alakítja. |
| `toNumber(Expression)` | szám | Ha lehetséges, átalakítja a bemeneti értéket egy számra. |
| `toString(Expression)` | sztring | A bemeneti értéket karakterlánccá alakítja. |
| `typeoOf(Expression)` | sztring | A megadott érték típusát leíró karakterláncot ad vissza. |

## <a name="color-expressions"></a>Színkifejezések

A színkifejezések egyszerűbbé teszik a színértékek létrehozását és kezelését.

| Expression | Visszatérési típus | Description |
|------------|-------------|-------------|
| `color(int)` | szín | Egy szín egész értékének átalakítása színkifejezésre. |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | szín | Egy színértéket hoz létre a *vörös*, *zöld* és *kék* összetevőkből a és a között `0` `255` , és egy alfa-összetevőt `1` . Ha bármelyik összetevő tartományon kívül esik, a kifejezés hibát jelez. |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | szín | Egy színértéket hoz létre a *vörös*, *zöld* és *kék* összetevők között, amelyeknek a és a közötti tartományba kell `0` `255` esnie, valamint egy alfa-összetevőt a és a tartományban `0` `1` . Ha bármelyik összetevő tartományon kívül esik, a kifejezés hibát jelez. |
| `toColor(Expression)` | szín  | Átalakítja a bemeneti értéket egy színre. |
| `toRgba(Expression)` | szín | Egy négy elemből álló tömböt ad vissza, amely a bemeneti szín vörös, zöld, kék és alfa összetevőit tartalmazza ebben a sorrendben. |

**Példa**

Az alábbi példa egy olyan RGB színértéket hoz létre, amely *vörös* értékkel rendelkezik `255` , valamint *zöld* és *kék* értékeket tartalmaz, amelyek kiszámítása a `2.5` tulajdonság értékének szorzatával történik `temperature` . Ahogy a hőmérséklet változik, a szín különböző árnyalatú *vörös* színűre változik.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
)
```

::: zone-end

Ha az összes színparaméter értéke számok, nem szükséges becsomagolni őket a `literal` kifejezéssel. Például:

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
)
```

::: zone-end

> [!TIP]
> A karakterlánc színértékei a metódus használatával alakíthatók át színre `android.graphics.Color.parseColor` . A következő egy hexadecimális színkarakterláncot alakít át egy olyan színkifejezésre, amely rétegben használható.
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>Karakterlánc-operátor kifejezései

A karakterlánc-operátor kifejezései olyan karakterlánc-átalakítási műveleteket hajtanak végre, mint például az Összefűzés és az átalakítás.

| Expression | Visszatérési típus | Description |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | sztring | Több karakterlánc összefűzése egymással. Minden értéknek sztringnek kell lennie. Ha szükséges, használja a `toString` Type kifejezést más típusú értékek karakterlánccá alakításához. |
| `downcase(string)` \| `downcase(Expression)` | sztring | A megadott karakterláncot kisbetűsre alakítja. |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | Meghatározza, hogy a bemeneti karakterlánc az aktuális betűkészlet-verem által támogatott karakterkészletet használ-e. Például: `isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | sztring | A megadott rendezés által használt területi beállítás IETF nyelvi címkéjét adja vissza. Ezzel meghatározhatja az alapértelmezett rendszer területi beállítását, vagy meghatározhatja, hogy a kért területi beállítás sikeresen be van-e töltve. |
| `upcase(string)` \| `upcase(Expression)` | sztring | A megadott karakterláncot nagybetűssé alakítja. |

**Példa**

Az alábbi példa átalakítja a `temperature` pont funkció tulajdonságát egy sztringre, majd a végéhez összefűzi a "°f" karakterláncot.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
)
```

::: zone-end

A fenti kifejezés egy PIN-kódot jelenít meg a térképen a "64 °F" szöveggel, az alábbi képen látható módon.

![Karakterlánc-operátor kifejezése – példa](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>Interpolációs és Step kifejezések

Az interpolációs és a Step kifejezésekkel az értékek kiszámíthatók az interpolált görbe vagy a Step függvény használatával. Ezek a kifejezések olyan kifejezéssel rendelkeznek, amely egy numerikus értéket ad vissza bemenetként, például: `get("temperature")` . A bemeneti érték kiértékelése a bemeneti és a kimeneti érték párokkal történik, hogy meghatározza az interpolált görbe vagy a Step függvénynek legjobban illeszkedő értéket. A kimeneti értékeket "leáll" értéknek nevezzük. Az egyes leállítás bemeneti értékének számnak kell lennie, és növekvő sorrendben kell lennie. A kimeneti értékeknek számnak, számok tömbének vagy színnek kell lenniük.

### <a name="interpolate-expression"></a>Interpolációs kifejezés

Egy `interpolate` kifejezéssel folytonos és simított értékeket lehet kiszámítani a leállítási értékek közötti interpolációval. A `interpolate` színértékeket visszaadó kifejezés olyan színátmenetet hoz létre, amelyben a rendszer kijelöli az eredmények értékét. A `interpolate` kifejezés formátuma a következő:

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

A kifejezésekben három típusú interpolációs módszer használható `interpolate` :

| Név | Leírás |
|------|-------------|
| `linear()` | A leállások párosítása között lineárisan interpolált.  |
| `exponential(number)` \| `exponential(Expression)` | A leállások között exponenciálisan interpolált. A "Base" meg van adva, és szabályozza a kimenet növekedésének sebességét. A nagyobb értékek miatt a kimenet nagyobb mértékben növekszik a tartomány magas végén. Az 1 értékhez közeledő "Base" érték olyan kimenetet eredményez, amely lineárisan növekszik.|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | A megadott vezérlési pontok által definiált [köbös Bezier-görbe](https://developer.mozilla.org/docs/Web/CSS/timing-function) használatával Interpolációk. |

A `stop` kifejezés formátuma a ( `stop(stop, value)` ).

Íme egy példa arra, hogy a különböző típusú Interpolációk hogyan néznek ki.

| Lineáris  | Exponenciális | Köbméter Bezier |
|---------|-------------|--------------|
| ![Lineáris interpolációs gráf](media/how-to-expressions/linear-interpolation.png) | ![Exponenciális interpolációs gráf](media/how-to-expressions/exponential-interpolation.png) | ![Köbméter Bezier-interpolációs gráf](media/how-to-expressions/bezier-curve-interpolation.png) |

**Példa**

Az alábbi példa egy kifejezést használ egy `linear interpolate` `bubbleColor` buborékdiagram tulajdonságának beállítására a `temperature` pont funkció tulajdonsága alapján. Ha az `temperature` érték kisebb, mint 60, a rendszer a "Blue" értéket adja vissza. Ha 60-es és 70-nál kisebb, akkor a sárga értéket adja vissza. Ha 70 és kevesebb, mint 80, a rendszer a "narancssárga" ( `#FFA500` ) értéket adja vissza. Ha 80 vagy nagyobb, akkor a rendszer a "vörös" értéket adja vissza.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

Az alábbi képen látható, hogyan választják ki a színeket a fenti kifejezéshez.

![Példa interpolált kifejezésre](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Lépés kifejezése

Egy `step` kifejezéssel kiszámíthatja a különálló, lépcsőzetes eredmények értékét a leállások által definiált [piecewise függvény](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) kiértékelésével.

A `interpolate` kifejezés formátuma a következő:

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

A Step kifejezésekkel közvetlenül a bemeneti érték előtt, vagy az első leállítási értéknél kisebb értéket kell megadni a Leállítás eredményében.

**Példa**

Az alábbi példa egy kifejezést használ egy `step` `bubbleColor` buborékdiagram tulajdonságának beállítására a `temperature` pont funkció tulajdonsága alapján. Ha az `temperature` érték kisebb, mint 60, a rendszer a "Blue" értéket adja vissza. Ha 60 és kevesebb, mint 70, a rendszer a "sárga" értéket adja vissza. Ha 70 és kevesebb, mint 80, a rendszer a "narancssárga" értéket adja vissza. Ha 80 vagy nagyobb, akkor a rendszer a "vörös" értéket adja vissza.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

Az alábbi képen látható, hogyan választják ki a színeket a fenti kifejezéshez.

![Példa a Step kifejezésre](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>Réteg-specifikus kifejezések

Speciális kifejezések, amelyek csak bizonyos rétegekre érvényesek.

### <a name="heat-map-density-expression"></a>Hő-Térkép sűrűségének kifejezése

A Heat Map sűrűség kifejezés lekérdezi a Heat Térkép sűrűségének értékét egy hő-Térkép réteg minden egyes képpontjához, és definiálva van `heatmapDensity` . Ez az érték a és a közötti szám `0` `1` . Egy vagy kifejezéssel együtt használatos, amely `interpolation` `step` meghatározza a Heat Térkép színezéséhez használt színátmenetet. Ez a kifejezés csak a `heatmapColor` Heat Térkép rétegének beállításában használható.

> [!TIP]
> A 0 indexű szín egy interpolációs kifejezésben vagy egy lépés színének alapértelmezett színe határozza meg annak a területnek a színét, ahol nincs adatmennyiség. A 0. indexben szereplő szín használható a háttér színének meghatározására. Számos előnyben részesítette ezt az értéket áttetszőre vagy félig átlátszó feketére állítani.

**Példa**

Ez a példa egy vonalhajózási interpolációs kifejezést használ a hő-Térkép megjelenítésére szolgáló sima színátmenet létrehozásához.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
)
```

::: zone-end

Amellett, hogy a zökkenőmentes átmenetet egy hő-Térkép színezésére használja, a színeket kifejezés használatával is megadhatja egy tartományon belül `step` . `step`A Heat Térkép színezésére szolgáló kifejezés használatával a rendszer vizuálisan megtöri a sűrűséget olyan tartományokra, amelyek egy kontúr vagy egy radar stílusú térképhez hasonlítanak.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

További információkért lásd a [Heat Map-réteg hozzáadása](map-add-heat-map-layer-android.md) című dokumentumot.

### <a name="line-progress-expression"></a>Vonal állapota kifejezés

Egy vonal-folyamatjelző kifejezés lekérdezi az előrehaladást egy vonal rétegében lévő átmenetes vonal mentén, és a következőképpen van definiálva: `lineProgress()` . Ez az érték 0 és 1 közötti szám. Egy `interpolation` vagy `step` kifejezéssel együtt használható. Ez a kifejezés csak `strokeGradient` a vonal rétegének beállításával használható.

> [!NOTE]
> A `strokeGradient` vonal rétegének beállításához `lineMetrics` meg kell adni az adatforrás beállítását `true` .

**Példa**

Ez a példa a `lineProgress()` kifejezés használatával egy színátmenetet alkalmaz egy vonal körvonalára.

::: zone pivot="programming-language-java-android"

```java
LineLayer layer = new LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

[Lásd az élő példát](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Szöveg mező formázása kifejezés

A `format` kifejezés a `textField` szimbólum réteg beállításával használható vegyes szövegformázás biztosításához. Ez a kifejezés egy vagy több olyan kifejezést vesz igénybe, `formatEntry` amely egy karakterláncot és egy, `formatOptions` a Text mezőhöz való hozzáfűzést eredményez.

| Kifejezés | Leírás |
|------------|-------------|
| `format(Expression...)` | Olyan formázott szöveget ad vissza, amely megjegyzéseket tartalmaz a kevert formátumú szöveges mezőkben való használatra. |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | Egy formázott karakterlánc-bejegyzést ad vissza a `format` kifejezésben való használatra. |

A következő formátumú lehetőségek érhetők el:

| Kifejezés | Leírás |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | Megadja a betűméret méretének méretezési tényezőjét. Ha meg van adva, ez az érték felülbírálja az `textSize` egyes karakterláncok tulajdonságát. |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | Megadja azt a színt, amelyet egy szövegre alkalmazni kell a rendereléskor. |

**Példa**

A következő példa egy félkövér betűkészlet hozzáadásával formázza a szövegmezőt, és a `title` szolgáltatás tulajdonságának betűméretét. Ez a példa a `subTitle` funkció tulajdonságát egy sortörésen is hozzáadja, és a méretezési betűméretet.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(arrayOf("StandardFont-Bold")),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
)
```

::: zone-end

Ez a réteg az alábbi képen látható módon fogja megjeleníteni a pont funkciót:

![A pont funkció képe formázott szöveg mezővel](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Nagyítás kifejezése

A `zoom` kifejezés használatával lekérdezhető a Térkép jelenlegi nagyítási szintje a renderelési időben, és a következőként van definiálva: `zoom()` . Ez a kifejezés a Térkép minimális és maximális nagyítási szintje közötti számot adja vissza. Azure Maps a web és Android rendszerhez készült interaktív térkép-vezérlőelemek 25 nagyítási szintet (0 – 24) támogatnak. A `zoom` kifejezés használata lehetővé teszi, hogy a stílusok dinamikusan legyenek módosítva, mivel a Térkép nagyítási szintje módosul. A `zoom` kifejezés csak `interpolate` és `step` kifejezésekkel használható.

**Példa**

Alapértelmezés szerint a Heat Map rétegben megjelenített adatpontok sugara rögzített képpont-sugárral rendelkezik az összes nagyítási szinthez. Ahogy a Térkép nagyítva van, az adatösszesítések együtt, a Heat Map-réteg pedig eltérőnek tűnik. Egy `zoom` kifejezés használható a sugár méretezésére az egyes nagyítási szintekhez úgy, hogy az egyes adatpontok a Térkép fizikai területére is kiterjednek. Így a Heat Térkép rétegének statikus és konzisztensnek kell lennie. A Térkép minden nagyítási szintje kétszer annyi képpontot tartalmaz függőlegesen és vízszintesen, mint az előző nagyítási szint. A sugár skálázása úgy, hogy az minden nagyítási szinten megduplázódik, egy olyan hő-térképet hoz létre, amely minden nagyítási szinten konzisztensnek tűnik. A kifejezéssel `zoom` egy kifejezéssel elvégezhető `base 2 exponential interpolation` , a minimális nagyítási szinthez beállított képpont-sugárral, a maximális nagyítási szinthez pedig a lent látható módon kiszámított mérettel `2 * Math.pow(2, minZoom - maxZoom)` .

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
)
```

::: zone-end

## <a name="variable-binding-expressions"></a>Változó kötési kifejezések

Változó kötési kifejezések a számítások eredményeit tárolják egy változóban. Így a számítási eredmények több alkalommal is hivatkozhatnak egy kifejezésben máshol. Hasznos optimalizálás olyan kifejezések esetében, amelyek sok számítást tartalmaznak.

| Expression | Visszatérési típus | Description |
|--------------|---------------|--------------|
| `let(Expression... input)` | | Egy vagy több értéket tárol változóként az `var` eredményt visszaadó gyermek kifejezésben szereplő kifejezés használatával. |
| `var(Expression expression)` \| `var(string variableName)` | Objektum | A kifejezés használatával létrehozott változóra hivatkozik `let` . |

**Példa**

Ez a példa egy olyan kifejezést használ, amely a bevételt a hőmérsékleti arányhoz viszonyítva kiszámítja, majd egy `case` kifejezés használatával kiértékeli a különböző logikai műveleteket ezen az értéken. A `let` kifejezés a bevétel hőmérsékleti arányhoz viszonyított tárolására szolgál, így csak egyszer kell kiszámítani. A `var` kifejezés a szükségesnél gyakrabban hivatkozik erre a változóra anélkül, hogy újra kellene számítania.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
)
```

::: zone-end

## <a name="next-steps"></a>Következő lépések

További információ a kifejezéseket támogató rétegekről:

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Hő-Térkép hozzáadása](map-add-heat-map-layer-android.md)
