---
title: Adatvezérelt stílusú kifejezések a Azure Maps web SDK-ban | Microsoft Azure térképek
description: További információ az adatvezérelt stílusú kifejezésekről. Tekintse meg, hogyan használhatja ezeket a kifejezéseket a Azure Maps web SDK-ban a stílusok a Maps-ben való beállításához.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 41a117c9ea8b47afcedaa1714abc2031d3be6c21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680052"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Adatvezérelt stílusú kifejezések (web SDK)

A kifejezések lehetővé teszik, hogy üzleti logikát alkalmazzon az adatforrásban lévő egyes alakzatokban definiált tulajdonságokat betekintő formázási lehetőségekre. A kifejezések adatforrásban vagy rétegben szűrhetik az adatszűrést. A kifejezések tartalmazhatnak feltételes logikát, például if-utasítások. A és a segítségével a következő műveleteket végezheti el az adatkezeléshez: karakterlánc-operátorok, logikai operátorok és matematikai operátorok.

Az adatvezérelt stílusok csökkentik az üzleti logika stíluson belüli megvalósításához szükséges kód mennyiségét. A rétegekkel való használatkor a kifejezések kiértékelése a renderelés időpontjában történik egy külön szálon. Ez a funkció nagyobb teljesítményt nyújt, mint az üzleti logika kiértékelése a felhasználói felületi szálon.

Ez a videó áttekintést nyújt az adatvezérelt stílusról a Azure Maps web SDK-ban.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

A kifejezések JSON-tömbökként jelennek meg. A tömb egyik kifejezésének első eleme egy olyan karakterlánc, amely megadja a kifejezés operátor nevét. Például: "+" vagy "Case". A következő elemek (ha vannak ilyenek) a kifejezés argumentumai. Minden argumentum vagy egy literális érték (karakterlánc, szám, logikai vagy `null` ), vagy egy másik Expression tömb. A következő pseudocode határozzák meg egy kifejezés alapszintű szerkezetét. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

A Azure Maps web SDK számos típusú kifejezést támogat. A kifejezések saját vagy más kifejezésekkel kombinálva is használhatók.

| Kifejezések típusa | Leírás |
|---------------------|-------------|
| [Összesítő kifejezés](#aggregate-expression) | Egy olyan kifejezés, amely egy adathalmazon feldolgozott számítást határoz meg, és a használatával használható `clusterProperties` `DataSource` . |
| [Logikai kifejezések](#boolean-expressions) | A logikai kifejezések logikai operátorok egy készletét biztosítják a logikai összehasonlítások kiértékeléséhez. |
| [Színkifejezések](#color-expressions) | A színkifejezések egyszerűbbé teszik a színértékek létrehozását és kezelését. |
| [Feltételes kifejezések](#conditional-expressions) | A feltételes kifejezések olyan logikai műveleteket biztosítanak, amelyek például if-utasítások. |
| [Adatkifejezések](#data-expressions) | Hozzáférést biztosít a szolgáltatásban található tulajdonság-információhoz. |
| [Interpolációs és Step kifejezések](#interpolate-and-step-expressions) | Az interpolációs és a Step kifejezésekkel az értékek kiszámíthatók az interpolált görbe vagy a Step függvény használatával. |
| [Réteg-specifikus kifejezések](#layer-specific-expressions) | Olyan speciális kifejezések, amelyek csak egyetlen rétegre érvényesek. |
| [Matematikai kifejezések](#math-expressions) | Matematikai operátorokat biztosít az adatvezérelt számítások végrehajtásához a kifejezés-keretrendszeren belül. |
| [Karakterlánc-operátor kifejezései](#string-operator-expressions) | A karakterlánc-operátor kifejezései olyan karakterlánc-átalakítási műveleteket hajtanak végre, mint például az Összefűzés és az átalakítás. |
| [Típuskifejezések](#type-expressions) | A Type kifejezések a különböző adattípusok (például karakterláncok, számok és logikai értékek) tesztelésére és átalakítására szolgáló eszközöket biztosítanak. |
| [Változó kötési kifejezések](#variable-binding-expressions) | A változó kötési kifejezések egy változóban lévő számítás eredményét tárolják, és többször is hivatkoznak egy kifejezésben, anélkül, hogy újra kellene számítani a tárolt értéket. |
| [Nagyítás kifejezése](#zoom-expression) | Lekéri a Térkép jelenlegi nagyítási szintjét renderelési időben. |

A jelen dokumentumban szereplő összes példa a következő funkcióval mutatja be a különböző típusú kifejezések használatának különböző módszereit. 

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

## <a name="data-expressions"></a>Adatkifejezések

Az adatkifejezések hozzáférést biztosítanak a szolgáltatásban található tulajdonság-értékekhez. 

| Expression | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['at', number, array]` | érték | Egy elem lekérése egy tömbből. |
| `['geometry-type']` | sztring | A szolgáltatás geometriai típusának beolvasása: pont, multipoint, LineString, MultiLineString, sokszög és többsokszög. |
| `['get', string]` | érték | A tulajdonság értékének beolvasása az aktuális funkció tulajdonságaiból. Null értéket ad vissza, ha a kért tulajdonság hiányzik. |
| `['get', string, object]` | érték | A tulajdonság értékének beolvasása a megadott objektum tulajdonságaiból. Null értéket ad vissza, ha a kért tulajdonság hiányzik. |
| `['has', string]` | boolean | Meghatározza, hogy a szolgáltatás tulajdonságai rendelkeznek-e a megadott tulajdonsággal. |
| `['has', string, object]` | boolean | Meghatározza, hogy az objektum tulajdonságai rendelkeznek-e a megadott tulajdonsággal. |
| `['id']` | érték | A szolgáltatás AZONOSÍTÓjának beolvasása, ha rendelkezik ilyennel. |
| `['in', boolean | string | number, array]` | boolean | Meghatározza, hogy egy elem létezik-e tömbben |
| `['in', substring, string]` | boolean | Meghatározza, hogy létezik-e egy alsztring egy karakterláncban. |
| `['index-of', boolean | string | number, array | string]`<br/><br/>`['index-of', boolean | string | number, array | string, number]` | szám | Azt az első pozíciót adja vissza, amikor egy tömbben vagy egy alsztringben található elem található egy karakterláncban, vagy `-1` Ha a bemenet nem található. Elfogad egy nem kötelező indexet a keresés megkezdéséhez. |
| `['length', string | array]` | szám | Egy karakterlánc vagy tömb hosszának beolvasása. |
| `['slice', array | string, number]`<br/><br/>`['slice', array | string, number, number]` | karakterlánc- \| tömb | Egy tömbből vagy egy karakterláncból származó elemet ad vissza egy megadott kezdő indexből, vagy egy kezdő indexből és egy záró indexből, ha be van állítva. A visszatérési érték tartalmazza a kezdő indexet, de nem a záró indexet. |

**Példák**

Egy szolgáltatás tulajdonságai közvetlenül egy kifejezés használatával érhetők el egy kifejezésben `get` . Ez a példa a `zoneColor` szolgáltatás értékét használja a buborékdiagram szín tulajdonságának megadásához. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

A fenti példa megfelelően fog működni, ha az összes funkció rendelkezik a `zoneColor` tulajdonsággal. Ha nem, a szín valószínűleg vissza fog térni a "fekete" értékre. A tartalék szín módosításához használjon `case` kifejezést a `has` kifejezéssel együtt annak ellenőrzéséhez, hogy a tulajdonság létezik-e. Ha a tulajdonság nem létezik, egy tartalék színt ad vissza.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

A buborék és a szimbólum rétegek alapértelmezés szerint az adatforrás összes alakzatának koordinátáit jelenítik meg. Ez a viselkedés kiemelheti a sokszög vagy a vonal csúcspontját. A `filter` réteg beállításával korlátozhatja az általa megjelenített szolgáltatások geometriájának típusát egy `['geometry-type']` logikai kifejezésen belüli kifejezés használatával. Az alábbi példa egy buborék réteget korlátozza, hogy csak a `Point` funkciók legyenek megjelenítve.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

A következő példa lehetővé teszi a `Point` és a `MultiPoint` szolgáltatások megjelenítését. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Hasonlóképpen a sokszögek körvonalai is megjelennek a sorokban. Ha le szeretné tiltani ezt a viselkedést egy vonal rétegben, adjon hozzá egy olyan szűrőt, amely csak a `LineString` és a `MultiLineString` funkciókat engedélyezi.  

Íme néhány további példa az adatkifejezések használatára:

```javascript
//Get item [2] from an array "properties.abcArray[1]" = "c"
['at', 2, ['get', 'abcArray']]

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
['at', 1, ['at', 0, ['get', 'array2d']]]

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
['in', 'a', ['get', 'abcArray']]

//Gets the index of the value 'b' in an array "properties.abcArray.indexOf('b')" = 1
['index-of', 'b', ['get', 'abcArray']]

//Get the length of an array "properties.abcArray.length" = 3
['length', ['get', 'abcArray']]

//Get the value of a subproperty "properties._style.fillColor" = "red"
['get', 'fillColor', ['get', '_style']]

//Check that "fillColor" exists as a subproperty of "_style".
['has', 'fillColor', ['get', '_style']]

//Slice an array starting at index 2 "properties.abcArray.slice(2)" = ['c']
['slice', ['get', 'abcArray'], 2]

//Slice a string from index 0 to index 4 "properties.entityType.slice(0, 4)" = 'rest'
['slice', ['get', 'entityType'], 0, 4]
```

## <a name="math-expressions"></a>Matematikai kifejezések

A matematikai kifejezések matematikai operátorokat biztosítanak az adatvezérelt számítások végrehajtásához a kifejezés keretrendszerében.

| Expression | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['+', number, number, …]` | szám | Kiszámítja a megadott számok összegét. |
| `['-', number]` | szám | A megadott szám szerinti 0 kivonása. |
| `['-', number, number]` | szám | Kivonja az első számokat a második szám alapján. |
| `['*', number, number, …]` | szám | A megadott számok összeszorzása. |
| `['/', number, number]` | szám | Az első szám felosztása a második szám alapján. |
| `['%', number, number]` | szám | Kiszámítja a maradékot az első szám második számmal való osztásakor. |
| `['^', number, number]` | szám | Kiszámítja a második szám hatványára emelt első érték értékét. |
| `['abs', number]` | szám | Kiszámítja a megadott szám abszolút értékét. |
| `['acos', number]` | szám | Kiszámítja a megadott szám szám arkusz koszinuszát. |
| `['asin', number]` | szám | Kiszámítja a megadott szám arkusz szinuszát. |
| `['atan', number]` | szám | Kiszámítja a megadott szám arkusz tangensét. |
| `['ceil', number]` | szám | Felfelé kerekíti a számot a következő egész számra. |
| `['cos', number]` | szám | Kiszámítja a megadott szám cos-számát. |
| `['e']` | szám | A matematikai állandót adja vissza `e` . |
| `['floor', number]` | szám | Lefelé kerekíti a számot az előző egész egész számra. |
| `['ln', number]` | szám | A megadott szám természetes alapú logaritmusát számítja ki. |
| `['ln2']` | szám | A matematikai állandót adja vissza `ln(2)` . |
| `['log10', number]` | szám | Kiszámítja a megadott szám 10-es alapú logaritmusát. |
| `['log2', number]` | szám | Kiszámítja a megadott szám alap-két logaritmusát. |
| `['max', number, number, …]` | szám | Kiszámítja a megadott Számsorozatok maximális számát. |
| `['min', number, number, …]` | szám | Kiszámítja a minimális számot a megadott számú készletben. |
| `['pi']` | szám | A matematikai állandót adja vissza `PI` . |
| `['round', number]` | szám | A számot a legközelebbi egész számra kerekíti. A félúton lévő értékek a nullától távolabbi távolságra vannak kerekítve. Például `['round', -1.5]` kiértékeli a következőt: `-2` . |
| `['sin', number]` | szám | Kiszámítja a megadott szám szinuszát. |
| `['sqrt', number]` | szám | Kiszámítja a megadott szám négyzet gyökerét. |
| `['tan', number]` | szám | Kiszámítja a megadott szám tangensét. |

## <a name="aggregate-expression"></a>Összesítő kifejezés

Az összesítő kifejezés egy adathalmazon feldolgozott számítást határoz meg, amely a használatával használható `clusterProperties` `DataSource` . A kifejezések kimenetének számnak vagy logikai értéknek kell lennie. 

Az összesítő kifejezés három értéket vesz igénybe: egy operátor értékét és a kezdeti értéket, valamint egy olyan kifejezést, amely az adatok egyes funkcióinak egy tulajdonságát kéri le az összesített művelet alkalmazásához. A kifejezés formátuma a következő:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operátor: egy Expression függvény, amelyet a `mapExpression` rendszer a fürt minden pontja által kiszámított összes értékre alkalmaz. Támogatott operátorok: 
    - Számok: `+` ,, `*` `max` , `min`
    - Logikai értékek esetén: `all` , `any`
- initialValue: a kezdeti érték, amelyben az első számított érték összesítve lesz.
- mapExpression: az adathalmaz minden pontján alkalmazott kifejezés.

**Példák**

Ha az adathalmaz összes funkciója rendelkezik egy `revenue` tulajdonsággal, amely egy szám. Ezt követően az adatkészletből létrehozott összes pont teljes bevételét kiszámíthatja a fürtben. Ezt a számítást a következő összesítő kifejezéssel hajtja végre: `['+', 0, ['get', 'revenue']]`

### <a name="accumulated-expression"></a>Halmozott kifejezés

A kifejezés beolvassa `accumulated` az eddig felhalmozott fürtözött tulajdonság értékét. Ez csak `clusterProperties` fürtözött forrás esetén használható `DataSource` .

**Használat**

```javascript
["accumulated"]
```

## <a name="boolean-expressions"></a>Logikai kifejezések

A logikai kifejezések logikai operátorok egy készletét biztosítják a logikai összehasonlítások kiértékeléséhez.

Az értékek összehasonlításakor az összehasonlítás szigorúan be van írva. A különböző típusú értékek mindig egyenlőtlennek számítanak. Azok az esetek, amelyekben a típusok ismertek, hogy az elemzési idő eltérő, és elemzési hibát eredményez. 

| Expression | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['!', boolean]` | boolean | Logikai tagadás. Visszaadja `true` , ha a bemenet `false` , és `false` Ha a bemenet szerepel `true` . |
| `['!=', value, value]` | boolean | Visszaadja `true` , ha a bemeneti értékek nem egyenlőek, `false` ellenkező esetben. |
| `['<', value, value]` | boolean | Akkor adja vissza `true` , ha az első bemenet szigorúan kisebb a másodiknál, `false` ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['<=', value, value]` | boolean | Azt adja vissza `true` , hogy az első bemenet kisebb-e vagy egyenlő-e a másodikval, `false` ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['==', value, value]` | boolean | Visszaadja `true` , ha a bemeneti értékek egyenlőek, `false` ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['>', value, value]` | boolean | Azt adja vissza `true` , hogy az első bemenet szigorúan nagyobb-e, mint a második, `false` ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['>=' value, value]` | boolean | Akkor adja vissza `true` , ha az első bemenet nagyobb vagy egyenlő, mint a második, `false` ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['all', boolean, boolean, …]` | boolean | Visszaadja `true` , ha az összes bemenet `true` , `false` ellenkező esetben. |
| `['any', boolean, boolean, …]` | boolean | Visszaadja `true` , ha a bemenetek bármelyike `true` , `false` ellenkező esetben. |
| `['within', Polygon | MultiPolygon | Feature<Polygon | MultiPolygon>]` | boolean | Azt adja vissza `true` , hogy a kiértékelt szolgáltatás teljes mértékben szerepel-e a bemeneti geometria határán belül, máskülönben hamis értéket ad. A bemeneti érték lehet a következő típusú érvényes GeoJSON:,, `Polygon` `MultiPolygon` `Feature` , vagy `FeatureCollection` . A próbaverzió támogatott funkciói:<br/><br/>-Point: azt adja vissza `false` , hogy egy pont a határon van-e, vagy a határon kívül esik.<br/>-LineString: akkor adja vissza `false` , ha egy vonal valamely része a határon kívül esik, a vonal metszi a határt, vagy egy vonal végpontja a határon van. |

## <a name="conditional-expressions"></a>Feltételes kifejezések

A feltételes kifejezések olyan logikai műveleteket biztosítanak, amelyek például if-utasítások.

A következő kifejezések feltételes logikai műveleteket hajtanak végre a bemeneti adatokon. A kifejezés például " `case` IF/then/Else" logikát biztosít, miközben a `match` kifejezés olyan, mint a "Switch-utasítás". 

### <a name="case-expression"></a>Case kifejezés

A `case` kifejezés olyan feltételes kifejezés típusa, amely "If/then/Else" logikát biztosít. Az ilyen típusú kifejezés lépései a logikai feltételek listáján keresztül jelennek meg. Az első logikai feltétel kimeneti értékét adja vissza az igaz érték kiértékeléséhez.

A következő pseudocode határozzák meg a kifejezés szerkezetét `case` . 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Példa**

A következő példa különböző logikai feltételeken halad végig, amíg meg nem találja a kiértékelését `true` , majd visszaadja a hozzá tartozó értéket. Ha egyetlen logikai feltétel sincs kiértékelve `true` , a rendszer visszaadja a tartalék értéket. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Egyezés kifejezése

A `match` kifejezés olyan feltételes kifejezés, amely switch-utasítást, például logikát biztosít. A bemenet bármely kifejezés lehet, például `['get', 'entityType']` egy sztringet vagy egy számot ad vissza. Minden címkének egy literális vagy literális értékből álló tömbnek kell lennie, amelynek értékének minden sztringnek vagy számnak kell lennie. A bemenet megegyezik, ha a tömb bármelyik értéke megegyezik. Minden címkének egyedinek kell lennie. Ha a bemeneti típus nem egyezik a címkék típusával, az eredmény a tartalék érték lesz.

A következő pseudocode határozzák meg a kifejezés szerkezetét `match` . 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Példák**

A következő példa egy `entityType` buborék rétegben lévő pont funkció tulajdonságát keresi meg. Ha egyezést talál, a rendszer a megadott értéket adja vissza, vagy visszaadja a tartalék értéket.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

Az alábbi példa egy tömböt használ a címkék egy halmazának listázásához, amelynek minden esetben ugyanazt az értéket kell visszaadnia. Ez a megközelítés sokkal hatékonyabb, mint a címkék egyenkénti listázása. Ebben az esetben, ha a `entityType` tulajdonság "étterem" vagy "grocery_store", a "piros" színt adja vissza.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>Egyesítő kifejezés

Egy `coalesce` kifejezés a kifejezések egy halmazán halad át, amíg az első nem null értéket nem szerzi be, és az értéket adja vissza. 

A következő pseudocode határozzák meg a kifejezés szerkezetét `coalesce` . 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Példa**

Az alábbi példa egy `coalesce` kifejezést használ `textField` egy szimbólum réteg beállításának beállításához. Ha a `title` tulajdonság hiányzik a szolgáltatásból, vagy a értékre van állítva `null` , a kifejezés ezután megpróbálja megkeresni a `subTitle` tulajdonságot, ha a hiányzó, vagy pedig `null` visszaesik egy üres karakterláncra. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subTitle. 
            ['get', 'subTitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

A következő példa egy kifejezést használ a `coalesce` Térkép sprite-ban elérhető első elérhető képikon beolvasására a megadott képnevekből származó listából.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Típuskifejezések

A Type kifejezések a különböző adattípusok (például karakterláncok, számok és logikai értékek) tesztelésére és átalakítására szolgáló eszközöket biztosítanak.

| Expression | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['array', value]` \| `['array', type: "string" | "number" | "boolean", value]` | Objektum [] | Azt állítja be, hogy a bemenet egy tömb. |
| `['boolean', value]` \| `["boolean", value, fallback: value, fallback: value, ...]` | boolean | Azt állítja be, hogy a bemeneti érték logikai. Ha több érték is meg van határozva, mindegyiket a rendszer a logikai érték beszerzése érdekében kiértékeli. Ha a bemenetek egyike sem logikai érték, a kifejezés hibát jelez. |
| `['collator', { 'case-sensitive': boolean, 'diacritic-sensitive': boolean, 'locale': string }]` | válogató | A területi beállításoktól függő összehasonlító műveletekben használandó rendezést adja vissza. A kis-és nagybetűket megkülönböztető beállítások alapértelmezett értéke false (hamis). A területi beállítás argumentum a használni kívánt területi beállítás IETF nyelvi címkéjét adja meg. Ha nincs megadva, az alapértelmezett területi beállítás lesz érvényben. Ha a kért területi beállítás nem érhető el, a leválogató rendszer által definiált tartalék területi beállítást fog használni. A helyi visszaállítási viselkedés eredményének teszteléséhez használja a megoldott területi beállítást. |
| `['literal', array]`<br/><br/>`['literal', object]` | tömb \| objektum | Egy konstans tömb vagy objektum értékét adja vissza. Ezzel a kifejezéssel megakadályozható, hogy egy tömb vagy objektum kifejezésként legyen kiértékelve. Erre akkor van szükség, ha egy tömböt vagy objektumot egy kifejezésnek kell visszaadnia. |
| `['image', string]` | sztring | Ellenőrzi, hogy a megadott rendszerkép-azonosító be van-e töltve a Maps-rendszerkép sprite-ba. Ha igen, a rendszer visszaadja az azonosítót, ellenkező esetben null értéket ad vissza. |
| `['number', value]` \| `["number", value, fallback: value, fallback: value, ...]` | szám | Azt állítja be, hogy a bemeneti érték egy szám. Ha több értéket ad meg, a rendszer mindegyiket kiértékeli, amíg meg nem kapja a számot. Ha a bemenetek egyike sem számok, a kifejezés hibát jelez. |
| `['object', value]`  \| `["object", value, fallback: value, fallback: value, ...]` | Objektum | Azt állítja be, hogy a bemeneti érték egy objektum.  Ha több érték is meg van határozva, a rendszer mindegyiket kiértékeli, amíg meg nem kapja az adott objektumot. Ha a bemenetek egyike sem objektum, a kifejezés hibát jelez. |
| `['string', value]` \| `["string", value, fallback: value, fallback: value, ...]` | sztring | Azt állítja be, hogy a bemeneti érték egy karakterlánc. Ha több értéket ad meg, a rendszer mindegyiket kiértékeli, amíg meg nem kapja a karakterláncot. Ha a bemenetek egyike sem karakterlánc, a kifejezés hibát jelez. |
| `['to-boolean', value]` | boolean | A bemeneti értéket logikai értékre alakítja. Az eredmény az, `false` Ha a bemenet egy üres karakterlánc,,, `0` `false` `null` vagy `NaN` ;, ellenkező esetben a `true` . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | szín | Átalakítja a bemeneti értéket egy színre. Ha több érték van megadva, a rendszer mindegyiket kiértékeli, amíg meg nem történik az első sikeres konverzió. Ha a bemenetek egyike sem alakítható át, akkor a kifejezés hibát jelez. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | szám | Ha lehetséges, átalakítja a bemeneti értéket egy számra. Ha a bemenet `null` vagy a `false` , az eredmény 0. Ha a bemenet értéke `true` , az eredmény 1. Ha a bemenet egy karakterlánc, akkor a rendszer a ECMAScript nyelvi specifikáció [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) string függvényével egy számra konvertálja. Ha több érték van megadva, a rendszer mindegyiket kiértékeli, amíg meg nem történik az első sikeres konverzió. Ha a bemenetek egyike sem alakítható át, akkor a kifejezés hibát jelez. |
| `['to-string', value]` | sztring | A bemeneti értéket karakterlánccá alakítja. Ha a bemenet értéke `null` , az eredmény: `""` . Ha a bemenet logikai érték, az eredmény a `"true"` vagy a `"false"` . Ha a bemenet egy szám, a rendszer a ECMAScript nyelv specifikációjának [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) Number függvényét használva karakterlánccá alakítja át. Ha a bemenet szín, a rendszer átalakítja a CSS RGBA színkarakterlánccá `"rgba(r,g,b,a)"` . Ellenkező esetben a rendszer a bemenetet egy karakterlánccá alakítja át a ECMAScript nyelvi specifikációjának [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) funkciója segítségével. |
| `['typeof', value]` | sztring | A megadott érték típusát leíró karakterláncot ad vissza. |

> [!TIP]
> Ha egy hasonló hibaüzenet `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` jelenik meg a böngésző konzolján, akkor az azt jelenti, hogy a kódban egy olyan kifejezés található, amely rendelkezik egy olyan tömbvel, amely nem rendelkezik sztringtel az első értékhez. Ha azt szeretné, hogy a kifejezés egy tömböt ad vissza, zárja be a tömböt a `literal` kifejezéssel. Az alábbi példa egy szimbólum réteg ikonját állítja be `offset` , amelynek két számot tartalmazó tömbnek kell lennie, egy `match` kifejezéssel, amely két eltolási érték közül választhat a  `entityType` pont szolgáltatás tulajdonságának értéke alapján.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Színkifejezések

A színkifejezések egyszerűbbé teszik a színértékek létrehozását és kezelését.

| Expression | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | szín | Egy színértéket hoz létre a *vörös*, *zöld* és *kék* összetevőkből a és a között `0` `255` , és egy alfa-összetevőt `1` . Ha bármelyik összetevő tartományon kívül esik, a kifejezés hibát jelez. |
| `['rgba', number, number, number, number]` | szín | Egy színértéket hoz létre a *vörös*, *zöld* és *kék* összetevők között, amelyeknek a és a közötti tartományba kell `0` `255` esnie, valamint egy alfa-összetevőt a és a tartományban `0` `1` . Ha bármelyik összetevő tartományon kívül esik, a kifejezés hibát jelez. |
| `['to-rgba']` | \[szám, szám, szám, szám\] | Egy négy elemből álló tömböt ad vissza, amely a bemeneti szín *vörös*, *zöld*, *kék* és *alfa* összetevőit tartalmazza ebben a sorrendben. |

**Példa**

Az alábbi példa egy olyan RGB színértéket hoz létre, amely *vörös* értékkel rendelkezik `255` , valamint *zöld* és *kék* értékeket tartalmaz, amelyek kiszámítása a `2.5` tulajdonság értékének szorzatával történik `temperature` . Ahogy a hőmérséklet változik, a szín különböző árnyalatú *vörös* színűre változik.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Karakterlánc-operátor kifejezései

A karakterlánc-operátor kifejezései olyan karakterlánc-átalakítási műveleteket hajtanak végre, mint például az Összefűzés és az átalakítás. 

| Expression | Visszatérési típus | Description |
|------------|-------------|-------------|
| `['concat', string, string, …]` | sztring | Több karakterlánc összefűzése egymással. Minden értéknek sztringnek kell lennie. Ha szükséges, használja a `to-string` Type kifejezést más típusú értékek karakterlánccá alakításához. |
| `['downcase', string]` | sztring | A megadott karakterláncot kisbetűsre alakítja. |
| `['is-supported-script', string]` \| `['is-supported-script', Expression]`| boolean | Meghatározza, hogy a bemeneti karakterlánc az aktuális betűkészlet-verem által támogatott karakterkészletet használ-e. Például: `['is-supported-script', 'ಗೌರವಾರ್ಥವಾಗಿ']` |
| `['resolved-locale', string]` | sztring | A megadott rendezés által használt területi beállítás IETF nyelvi címkéjét adja vissza. Ezzel meghatározhatja az alapértelmezett rendszer területi beállítását, vagy meghatározhatja, hogy a kért területi beállítás sikeresen be van-e töltve. |
| `['upcase', string]` | sztring | A megadott karakterláncot nagybetűssé alakítja. |

**Példa**

Az alábbi példa átalakítja a `temperature` pont funkció tulajdonságát egy sztringre, majd a végéhez összefűzi a "°f" karakterláncot.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

A fenti kifejezés egy PIN-kódot jelenít meg a térképen a "64 °F" szöveggel, az alábbi képen látható módon.

<center>

![Karakterlánc-operátor kifejezése – példa ](media/how-to-expressions/string-operator-expression.png)</center>

## <a name="interpolate-and-step-expressions"></a>Interpolációs és Step kifejezések

Az interpolációs és a Step kifejezésekkel az értékek kiszámíthatók az interpolált görbe vagy a Step függvény használatával. Ezek a kifejezések olyan kifejezéssel rendelkeznek, amely egy numerikus értéket ad vissza bemenetként, például: `['get',  'temperature']` . A bemeneti érték kiértékelése a bemeneti és a kimeneti érték párokkal történik, hogy meghatározza az interpolált görbe vagy a Step függvénynek legjobban illeszkedő értéket. A kimeneti értékeket "leáll" értéknek nevezzük. Az egyes leállítás bemeneti értékének számnak kell lennie, és növekvő sorrendben kell lennie. A kimeneti értékeknek számnak, számok tömbének vagy színnek kell lenniük.

### <a name="interpolate-expression"></a>Interpolációs kifejezés

Egy `interpolate` kifejezéssel folytonos és simított értékeket lehet kiszámítani a leállítási értékek közötti interpolációval. A `interpolate` színértékeket visszaadó kifejezés olyan színátmenetet hoz létre, amelyben a rendszer kijelöli az eredmények értékét.

A kifejezésekben három típusú interpolációs módszer használható `interpolate` :
 
* `['linear']` – A leállások párosítása között lineárisan interpolált.
* `['exponential', base]` – A leállás exponenciálisan megszakítható. Az `base` érték határozza meg, hogy a kimenet milyen sebességgel növekszik. A nagyobb értékek miatt a kimenet nagyobb mértékben növekszik a tartomány magas végén. Az `base` 1 értékhez közeledő értékek olyan kimenetet eredményeznek, amely lineárisan növekszik.
* `['cubic-bezier', x1, y1, x2, y2]` – A megadott vezérlési pontok által meghatározott [köbméter Bezier-görbe](https://developer.mozilla.org/docs/Web/CSS/timing-function) használatával Interpolációk.

Íme egy példa arra, hogy a különböző típusú Interpolációk hogyan néznek ki. 

| Lineáris  | Exponenciális | Köbméter Bezier |
|---------|-------------|--------------|
| ![Lineáris interpolációs gráf](media/how-to-expressions/linear-interpolation.png) | ![Exponenciális interpolációs gráf](media/how-to-expressions/exponential-interpolation.png) | ![Köbméter Bezier-interpolációs gráf](media/how-to-expressions/bezier-curve-interpolation.png) |

A következő pseudocode határozzák meg a kifejezés szerkezetét `interpolate` . 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Példa**

Az alábbi példa egy kifejezést használ egy `linear interpolate` `color` buborékdiagram tulajdonságának beállítására a `temperature` pont funkció tulajdonsága alapján. Ha az `temperature` érték kisebb, mint 60, a rendszer a "Blue" értéket adja vissza. Ha 60-es és 70-nál kisebb, akkor a sárga értéket adja vissza. Ha 70 és kevesebb, mint 80, a rendszer a "narancssárga" értéket adja vissza. Ha 80 vagy nagyobb, akkor a rendszer a "vörös" értéket adja vissza.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50, 'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

Az alábbi képen látható, hogyan választják ki a színeket a fenti kifejezéshez.
 
<center>

![Példa ](media/how-to-expressions/interpolate-expression-example.png) interpolált kifejezésre </center>

### <a name="step-expression"></a>Lépés kifejezése

Egy `step` kifejezéssel kiszámíthatja a különálló, lépcsőzetes eredmények értékét a leállások által definiált [piecewise függvény](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) kiértékelésével. 

A következő pseudocode határozzák meg a kifejezés szerkezetét `step` . 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

A Step kifejezésekkel közvetlenül a bemeneti érték előtt, vagy az első leállítási értéknél kisebb értéket kell megadni a Leállítás eredményében. 

**Példa**

Az alábbi példa egy kifejezést használ egy `step` `color` buborékdiagram tulajdonságának beállítására a `temperature` pont funkció tulajdonsága alapján. Ha az `temperature` érték kisebb, mint 60, a rendszer a "Blue" értéket adja vissza. Ha 60 és kevesebb, mint 70, a rendszer a "sárga" értéket adja vissza. Ha 70 és kevesebb, mint 80, a rendszer a "narancssárga" értéket adja vissza. Ha 80 vagy nagyobb, akkor a rendszer a "vörös" értéket adja vissza.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

Az alábbi képen látható, hogyan választják ki a színeket a fenti kifejezéshez.
 
<center>

![Példa a Step kifejezésre](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Réteg-specifikus kifejezések

Speciális kifejezések, amelyek csak bizonyos rétegekre érvényesek.

### <a name="heat-map-density-expression"></a>Hő-Térkép sűrűségének kifejezése

A Heat Map sűrűség kifejezés lekérdezi a Heat Térkép sűrűségének értékét egy hő-Térkép réteg minden egyes képpontjához, és definiálva van `['heatmap-density']` . Ez az érték a és a közötti szám `0` `1` . Egy vagy kifejezéssel együtt használatos, amely `interpolation` `step` meghatározza a Heat Térkép színezéséhez használt színátmenetet. Ez a kifejezés csak a Heat Map réteg [szín beállításában](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions#color) használható.

> [!TIP]
> A 0 indexű szín egy interpolációs kifejezésben vagy egy lépés színének alapértelmezett színe határozza meg annak a területnek a színét, ahol nincs adatmennyiség. A 0. indexben szereplő szín használható a háttér színének meghatározására. Számos előnyben részesítette ezt az értéket áttetszőre vagy félig átlátszó feketére állítani.

**Példa**

Ez a példa egy vonalhajózási interpolációs kifejezést használ a hő-Térkép megjelenítésére szolgáló sima színátmenet létrehozásához. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Amellett, hogy a zökkenőmentes átmenetet egy hő-Térkép színezésére használja, a színeket kifejezés használatával is megadhatja egy tartományon belül `step` . `step`A Heat Térkép színezésére szolgáló kifejezés használatával a rendszer vizuálisan megtöri a sűrűséget olyan tartományokra, amelyek egy kontúr vagy egy radar stílusú térképhez hasonlítanak.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

További információkért lásd a [Heat Map-réteg hozzáadása](map-add-heat-map-layer.md) című dokumentumot.

### <a name="line-progress-expression"></a>Vonal állapota kifejezés

Egy vonal-folyamatjelző kifejezés lekérdezi az előrehaladást egy vonal rétegében lévő átmenetes vonal mentén, és a következőképpen van definiálva: `['line-progress']` . Ez az érték 0 és 1 közötti szám. Egy `interpolation` vagy `step` kifejezéssel együtt használható. Ez a kifejezés csak a vonal rétegének [strokeGradient kapcsolóval]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions#strokegradient) használható. 

> [!NOTE]
> A `strokeGradient` vonal rétegének beállításához `lineMetrics` meg kell adni az adatforrás beállítását `true` .

**Példa**

Ez a példa a `['line-progress']` kifejezés használatával egy színátmenetet alkalmaz egy vonal körvonalára.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Lásd az élő példát](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Szöveg mező formázása kifejezés

A Text mező formázása kifejezés a `textField` Symbol Layers `textOptions` tulajdonsággal együtt használható vegyes szövegformázás biztosításához. Ez a kifejezés lehetővé teszi a bemeneti karakterláncok és a formázási beállítások megadását. Ebben a kifejezésben a következő beállítások adhatók meg minden bemeneti karakterlánchoz.

 * `'font-scale'` -Megadja a betűméret méretezési tényezőjét. Ha meg van adva, ez az érték felülbírálja az `size` `textOptions` Egyéni sztringhez tartozó tulajdonságot.
 * `'text-font'` -Egy vagy több olyan betűkészlet-családot határoz meg, amelyet ehhez a karakterlánchoz kell használni. Ha meg van adva, ez az érték felülbírálja az `font` `textOptions` Egyéni sztringhez tartozó tulajdonságot.

A következő pseudocode határozzák meg a szöveg mező formázása kifejezés szerkezetét. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[]
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[]
    },
    …
]
```

**Példa**

A következő példa egy félkövér betűkészlet hozzáadásával formázza a szövegmezőt, és a `title` szolgáltatás tulajdonságának betűméretét. Ez a példa a `subTitle` funkció tulajdonságát egy sortörésen is hozzáadja, és a méretezési betűméretet.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subTitle property. 
            ['get', 'subTitle'],
            { 
                'font-scale': 0.75
            }
        ]
    }
});
```

Ez a réteg az alábbi képen látható módon fogja megjeleníteni a pont funkciót:
 
<center>

![A pont funkció képe formázott szöveg mezővel ](media/how-to-expressions/text-field-format-expression.png)</center>

### <a name="number-format-expression"></a>Számformátum kifejezése

A `number-format` kifejezés csak `textField` szimbólum réteg beállításával használható. Ez a kifejezés egy formázott karakterlánccá alakítja át a megadott számot. Ez a kifejezés a JavaScript [Number. toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) függvényt csomagolja be, és a következő beállításokat támogatja.

 * `locale` -Adja meg ezt a lehetőséget, ha a számokat karakterlánccá szeretné konvertálni úgy, hogy az a megadott nyelven legyen igazítva. Adja át a [BCP 47 Language címkét](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) ebbe a beállításba.
 * `currency` – A szám konvertálása pénznemet jelölő sztringre. A lehetséges értékek az [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217)-es pénznem-kódok, például az USA-dollár "USD", az "EUR" az euro esetében, vagy a kínai RMB "CNY".
 * `'min-fraction-digits'` -A szám karakterlánc-verziójában szerepeltetni kívánt tizedesjegyek minimális számát határozza meg.
 * `'max-fraction-digits'` -A szám karakterlánc-verziójában szerepeltetni kívánt tizedesjegyek maximális számát adja meg.

A következő pseudocode határozzák meg a szöveg mező formázása kifejezés szerkezetét. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Példa**

Az alábbi példa egy kifejezést használ egy kifejezés használatával, amely azt módosítja, hogyan jelenik meg a `number-format` `revenue` pont funkció tulajdonsága `textField` egy szimbólum rétegben, hogy megjelenjen az amerikai dollár értéke.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { 'currency': 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Ez a réteg az alábbi képen látható módon fogja megjeleníteni a pont funkciót:

<center>

![Példa ](media/how-to-expressions/number-format-expression.png) a számformátum kifejezésére </center>

### <a name="image-expression"></a>Rendszerkép kifejezése

A képkifejezések használhatók a `image` `textField` szimbólum réteg és a beállításokkal, valamint a `fillPattern` sokszög rétegének beállításával. Ez a kifejezés ellenőrzi, hogy a kért rendszerkép létezik-e a stílusban, és visszaadja-e a megoldott rendszerkép nevét `null` , vagy attól függően, hogy a rendszerkép jelenleg a stílusban van-e kiválasztva. Ez az ellenőrzési folyamat szinkronban van, és megköveteli, hogy a rendszerkép hozzá legyen adva a stílushoz, mielőtt a rendszerkép argumentumban bekéri azt.

**Példa**

Az alábbi példa egy kifejezést használ egy olyan `image` ikon hozzáadásához, amely egy szimbólum rétegben lévő szöveggel jelenik meg. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Ez a réteg megjeleníti a szövegmezőt a szimbólum rétegben, ahogy az alábbi képen is látható:

<center>

![Példa ](media/how-to-expressions/image-expression.png) képkifejezésre </center>

## <a name="zoom-expression"></a>Nagyítás kifejezése

A `zoom` kifejezés használatával lekérdezhető a Térkép jelenlegi nagyítási szintje a renderelési időben, és a következőként van definiálva: `['zoom']` . Ez a kifejezés a Térkép minimális és maximális nagyítási szintje közötti számot adja vissza. Azure Maps a web és Android rendszerhez készült interaktív térkép-vezérlőelemek 25 nagyítási szintet (0 – 24) támogatnak. A `zoom` kifejezés használata lehetővé teszi, hogy a stílusok dinamikusan legyenek módosítva, mivel a Térkép nagyítási szintje módosul. A `zoom` kifejezés csak `interpolate` és `step` kifejezésekkel használható.

**Példa**

Alapértelmezés szerint a Heat Map rétegben megjelenített adatpontok sugara rögzített képpont-sugárral rendelkezik az összes nagyítási szinthez. Ahogy a Térkép nagyítva van, az adatösszesítések együtt, a Heat Map-réteg pedig eltérőnek tűnik. Egy `zoom` kifejezés használható a sugár méretezésére az egyes nagyítási szintekhez úgy, hogy az egyes adatpontok a Térkép fizikai területére is kiterjednek. Így a Heat Térkép rétegének statikus és konzisztensnek kell lennie. A Térkép minden nagyítási szintje kétszer annyi képpontot tartalmaz függőlegesen és vízszintesen, mint az előző nagyítási szint. A sugár skálázása úgy, hogy az minden nagyítási szinten megduplázódik, egy olyan hő-térképet hoz létre, amely minden nagyítási szinten konzisztensnek tűnik. A kifejezéssel `zoom` egy kifejezéssel elvégezhető `base 2 exponential interpolation` , a minimális nagyítási szinthez beállított képpont-sugárral, a maximális nagyítási szinthez pedig a lent látható módon kiszámított mérettel `2 * Math.pow(2, minZoom - maxZoom)` .

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        1, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[Lásd az élő példát](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Változó kötési kifejezések

Változó kötési kifejezések a számítások eredményeit tárolják egy változóban. Így a számítási eredmények több alkalommal is hivatkozhatnak egy kifejezésben máshol. Hasznos optimalizálás olyan kifejezések esetében, amelyek sok számítást tartalmaznak.

| Expression | Visszatérési típus | Leírás |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Let",<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: karakterlánc,<br/>&nbsp;&nbsp;&nbsp;&nbsp;érték1: bármely,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: karakterlánc,<br/>&nbsp;&nbsp;&nbsp;&nbsp;érték2: bármely,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Egy vagy több értéket tárol változóként az `var` eredményt visszaadó gyermek kifejezésben szereplő kifejezés használatával. |
| `['var', name: string]` | bármelyik | A kifejezés használatával létrehozott változóra hivatkozik `let` . |

**Példa**

Ez a példa egy olyan kifejezést használ, amely a bevételt a hőmérsékleti arányhoz viszonyítva kiszámítja, majd egy `case` kifejezés használatával kiértékeli a különböző logikai műveleteket ezen az értéken. A `let` kifejezés a bevétel hőmérsékleti arányhoz viszonyított tárolására szolgál, így csak egyszer kell kiszámítani. A `var` kifejezés a szükségesnél gyakrabban hivatkozik erre a változóra anélkül, hogy újra kellene számítania.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekben további kódokat talál a kifejezések megvalósításához:

> [!div class="nextstepaction"] 
> [Szimbólumréteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Buborékréteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Hőtérképréteg hozzáadása](map-add-heat-map-layer.md)

További információ a kifejezéseket támogató rétegbeli lehetőségekről:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

> [!div class="nextstepaction"] 
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)