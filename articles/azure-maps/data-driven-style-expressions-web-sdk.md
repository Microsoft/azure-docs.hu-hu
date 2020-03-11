---
title: Adatvezérelt stílusú kifejezések a Azure Maps web SDK-ban | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan használhatja az adatvezérelt stílusú kifejezéseket a Microsoft Azure Maps web SDK-ban.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: c3f5fb2a387db6e672290fcf03d46c476b6211b6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78368923"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Adatvezérelt stílusú kifejezések (web SDK)

A kifejezések lehetővé teszik, hogy üzleti logikát alkalmazzon az adatforrásban lévő egyes alakzatokban definiált tulajdonságokat betekintő formázási lehetőségekre. A kifejezések adatforrásban vagy rétegben szűrhetik az adatszűrést. A kifejezések tartalmazhatnak feltételes logikát, például if-utasítások. A és a segítségével a következő műveleteket végezheti el az adatkezeléshez: karakterlánc-operátorok, logikai operátorok és matematikai operátorok.

Az adatvezérelt stílusok csökkentik az üzleti logika stíluson belüli megvalósításához szükséges kód mennyiségét. A rétegekkel való használatkor a kifejezések kiértékelése a renderelés időpontjában történik egy külön szálon. Ez a funkció nagyobb teljesítményt nyújt, mint az üzleti logika kiértékelése a felhasználói felületi szálon.

Ez a videó áttekintést nyújt az adatvezérelt stílusról a Azure Maps web SDK-ban.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

A kifejezések JSON-tömbökként jelennek meg. A tömb egyik kifejezésének első eleme egy olyan karakterlánc, amely megadja a kifejezés operátor nevét. Például: "+" vagy "Case". A következő elemek (ha vannak ilyenek) a kifejezés argumentumai. Minden argumentum vagy egy literális érték (karakterlánc, szám, logikai vagy `null`) vagy egy másik Expression tömb. A következő pseudocode határozzák meg egy kifejezés alapszintű szerkezetét. 

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
| [Összesítő kifejezés](#aggregate-expression) | Egy olyan kifejezés, amely egy adathalmazon feldolgozott számítást határoz meg, és a `DataSource``clusterProperties` lehetőséggel használható. |
| [Logikai kifejezések](#boolean-expressions) | A logikai kifejezések logikai operátorok egy készletét biztosítják a logikai összehasonlítások kiértékeléséhez. |
| [Színkifejezések](#color-expressions) | A színkifejezések egyszerűbbé teszik a színértékek létrehozását és kezelését. |
| [Feltételes kifejezések](#conditional-expressions) | A feltételes kifejezések olyan logikai műveleteket biztosítanak, amelyek például if-utasítások. |
| [Adatkifejezések](#data-expressions) | Hozzáférést biztosít a szolgáltatásban található tulajdonság-információhoz. |
| [Interpolációs és Step kifejezések](#interpolate-and-step-expressions) | Az interpolációs és a Step kifejezésekkel az értékek kiszámíthatók az interpolált görbe vagy a Step függvény használatával. |
| [Réteg-specifikus kifejezések](#layer-specific-expressions) | Olyan speciális kifejezések, amelyek csak egyetlen rétegre érvényesek. |
| [Matematikai kifejezések](#math-expressions) | Matematikai operátorokat biztosít az adatvezérelt számítások végrehajtásához a kifejezés-keretrendszeren belül. |
| [Karakterlánc-operátor kifejezései](#string-operator-expressions) | A karakterlánc-operátor kifejezései olyan karakterlánc-átalakítási műveleteket hajtanak végre, mint például az Összefűzés és az átalakítás. |
| [Típus kifejezések](#type-expressions) | A Type kifejezések a különböző adattípusok (például karakterláncok, számok és logikai értékek) tesztelésére és átalakítására szolgáló eszközöket biztosítanak. |
| [Változó kötési kifejezések](#variable-binding-expressions) | A változó kötési kifejezések egy változóban lévő számítás eredményét tárolják, és többször is hivatkoznak egy kifejezésben, anélkül, hogy újra kellene számítani a tárolt értéket. |
| [Nagyítás kifejezése](#zoom-expression) | Lekéri a Térkép jelenlegi nagyítási szintjét renderelési időben. |

A jelen dokumentumban szereplő összes példa a következő funkcióval mutatja be a különböző típusú kifejezések használatának különböző módszereit. 

```javascript
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
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Adatkifejezések

Az adatkifejezések hozzáférést biztosítanak a szolgáltatásban található tulajdonság-értékekhez. 

| Kifejezés | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['at', number, array]` | objektum | Egy elem lekérése egy tömbből. |
| `['geometry-type']` | sztring | A szolgáltatás geometriai típusának beolvasása: pont, multipoint, LineString, MultiLineString, sokszög és többsokszög. |
| `['get', string]` | érték | A tulajdonság értékének beolvasása az aktuális funkció tulajdonságaiból. Null értéket ad vissza, ha a kért tulajdonság hiányzik. |
| `['get', string, object]` | érték | A tulajdonság értékének beolvasása a megadott objektum tulajdonságaiból. Null értéket ad vissza, ha a kért tulajdonság hiányzik. |
| `['has', string]` | logikai | Meghatározza, hogy a szolgáltatás tulajdonságai rendelkeznek-e a megadott tulajdonsággal. |
| `['has', string, object]` | logikai | Meghatározza, hogy az objektum tulajdonságai rendelkeznek-e a megadott tulajdonsággal. |
| `['id']` | érték | A szolgáltatás AZONOSÍTÓjának beolvasása, ha rendelkezik ilyennel. |
| `['length', string | array]` | szám | Egy karakterlánc vagy tömb hosszának beolvasása. |
| `['in', boolean | string | number, array]` | logikai | Meghatározza, hogy egy elem létezik-e tömbben |
| `['in', substring, string]` | logikai | Meghatározza, hogy létezik-e egy alsztring egy karakterláncban. |

**Példák**

A szolgáltatások tulajdonságai közvetlenül egy kifejezésben érhetők el egy `get` kifejezés használatával. Ez a példa a szolgáltatás "zoneColor" értékét használja a buborékdiagram szín tulajdonságának megadásához. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

A fenti példa megfelelően fog működni, ha az összes funkció rendelkezik a `zoneColor` tulajdonsággal. Ha nem, a szín valószínűleg "fekete" lesz. A tartalék szín módosításához használjon `case` kifejezést a `has` kifejezéssel együtt annak ellenőrzéséhez, hogy a tulajdonság létezik-e. Ha a tulajdonság nem létezik, egy tartalék színt ad vissza.

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

A buborék és a szimbólum rétegek alapértelmezés szerint az adatforrás összes alakzatának koordinátáit jelenítik meg. Ez a viselkedés kiemelheti a sokszög vagy a vonal csúcspontját. A réteg `filter` beállításával korlátozhatja az általa megjelenített szolgáltatások geometriájának típusát egy logikai kifejezésen belüli `['geometry-type']` kifejezés használatával. Az alábbi példa egy buborék réteget korlátozza, hogy csak `Point` szolgáltatások legyenek megjelenítve.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

A következő példa lehetővé teszi a `Point` és `MultiPoint` szolgáltatások megjelenítését. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Hasonlóképpen a sokszögek körvonalai is megjelennek a sorokban. Ha le szeretné tiltani ezt a viselkedést egy sorban, adjon hozzá egy szűrőt, amely csak `LineString` és `MultiLineString` funkciókat engedélyez.  

## <a name="math-expressions"></a>Matematikai kifejezések

A matematikai kifejezések matematikai operátorokat biztosítanak az adatvezérelt számítások végrehajtásához a kifejezés keretrendszerében.

| Kifejezés | Visszatérési típus | Leírás |
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
| `['e']` | szám | A matematikai konstans `e`adja vissza. |
| `['floor', number]` | szám | Lefelé kerekíti a számot az előző egész egész számra. |
| `['ln', number]` | szám | A megadott szám természetes alapú logaritmusát számítja ki. |
| `['ln2']` | szám | A matematikai konstans `ln(2)`adja vissza. |
| `['log10', number]` | szám | Kiszámítja a megadott szám 10-es alapú logaritmusát. |
| `['log2', number]` | szám | Kiszámítja a megadott szám alap-két logaritmusát. |
| `['max', number, number, …]` | szám | Kiszámítja a megadott Számsorozatok maximális számát. |
| `['min', number, number, …]` | szám | Kiszámítja a minimális számot a megadott számú készletben. |
| `['pi']` | szám | A matematikai konstans `PI`adja vissza. |
| `['round', number]` | szám | A számot a legközelebbi egész számra kerekíti. A félúton lévő értékek a nullától távolabbi távolságra vannak kerekítve. `['round', -1.5]` például a következőt értékeli:-2. |
| `['sin', number]` | szám | Kiszámítja a megadott szám szinuszát. |
| `['sqrt', number]` | szám | Kiszámítja a megadott szám négyzet gyökerét. |
| `['tan', number]` | szám | Kiszámítja a megadott szám tangensét. |

## <a name="aggregate-expression"></a>Összesítő kifejezés

Az összesítő kifejezés egy adathalmazon feldolgozott számítást határoz meg, amely egy `DataSource``clusterProperties` lehetőséggel használható. A kifejezések kimenetének számnak vagy logikai értéknek kell lennie. 

Az összesítő kifejezés három értéket vesz igénybe: egy operátor értékét és a kezdeti értéket, valamint egy olyan kifejezést, amely az adatok egyes funkcióinak egy tulajdonságát kéri le az összesített művelet alkalmazásához. A kifejezés formátuma a következő:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operátor: egy Expression függvény, amely a fürt minden pontjára vonatkozóan a `mapExpression` által kiszámított összes értékre vonatkozik. Támogatott operátorok: 
    - Számok: `+`, `*`, `max`, `min`
    - Logikai értékek esetén: `all`, `any`
- initialValue: a kezdeti érték, amelyben az első számított érték összesítve lesz.
- mapExpression: az adathalmaz minden pontján alkalmazott kifejezés.

**Példák**

Ha az adathalmaz összes funkciója rendelkezik egy `revenue` tulajdonsággal, amely egy szám. Ezt követően az adatkészletből létrehozott összes pont teljes bevételét kiszámíthatja a fürtben. Ezt a számítást a következő összesítő kifejezéssel hajtja végre: `['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Logikai kifejezések

A logikai kifejezések logikai operátorok egy készletét biztosítják a logikai összehasonlítások kiértékeléséhez.

Az értékek összehasonlításakor az összehasonlítás szigorúan be van írva. A különböző típusú értékek mindig egyenlőtlennek számítanak. Azok az esetek, amelyekben a típusok ismertek, hogy az elemzési idő eltérő, és elemzési hibát eredményez. 

| Kifejezés | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['! ', boolean]` | logikai | Logikai tagadás. `true` értéket ad vissza, ha a bemenet `false`, és `false`, ha a bemenet `true`. |
| `['!= ', value, value]` | logikai | `true` visszaadása, ha a bemeneti értékek nem egyenlőek, `false` más módon. |
| `['<', value, value]` | logikai | `true` visszaadása, ha az első bemenet szigorúan kisebb a másodiknál, `false` másként. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['<=', value, value]` | logikai | `true` visszaadása, ha az első bemenet kisebb vagy egyenlő, mint a második, `false` egyébként. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['==', value, value]` | logikai | `true` értéket ad vissza, ha a bemeneti értékek egyenlőek, `false` másként. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['>', value, value]` | logikai | `true` visszaadása, ha az első bemenet szigorúan nagyobb a másodiknál, `false` másként. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['>=' value, value]` | logikai | `true` visszaadása, ha az első bemenet nagyobb vagy egyenlő, mint a második, `false` egyébként. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['all', boolean, boolean, …]` | logikai | `true` adja vissza, ha az összes bemenet `true`, `false` egyéb módon. |
| `['any', boolean, boolean, …]` | logikai | `true` adja vissza, ha a bemenetek bármelyike `true`, `false` egyéb módon. |

## <a name="conditional-expressions"></a>Feltételes kifejezések

A feltételes kifejezések olyan logikai műveleteket biztosítanak, amelyek például if-utasítások.

A következő kifejezések feltételes logikai műveleteket hajtanak végre a bemeneti adatokon. A `case` kifejezés például "If/then/Else" logikát biztosít, míg a `match` kifejezés "Switch-utasítás". 

### <a name="case-expression"></a>Case kifejezés

A `case` kifejezés olyan feltételes kifejezés típusa, amely "If/then/Else" logikát biztosít. Az ilyen típusú kifejezés lépései a logikai feltételek listáján keresztül jelennek meg. Az első logikai feltétel kimeneti értékét adja vissza az igaz érték kiértékeléséhez.

A következő pseudocode határozzák meg a `case` kifejezés szerkezetét. 

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

A következő példa különböző logikai feltételeken halad végig, amíg meg nem találja az egyiket, amely kiértékeli a `true`, majd visszaadja a hozzá tartozó értéket. Ha nincs logikai feltétel kiértékelése `true`re, a rendszer visszaadja a tartalék értéket. 

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

A `match` kifejezés olyan feltételes kifejezés, amely switch-utasítást, például logikát biztosít. A bemenet tetszőleges kifejezés lehet, például `['get', 'entityType']`, amely egy karakterláncot vagy egy számot ad vissza. Minden címkének egy literális vagy literális értékből álló tömbnek kell lennie, amelynek értékének minden sztringnek vagy számnak kell lennie. A bemenet megegyezik, ha a tömb bármelyik értéke megegyezik. Minden címkének egyedinek kell lennie. Ha a bemeneti típus nem egyezik a címkék típusával, az eredmény a tartalék érték lesz.

A következő pseudocode határozzák meg a `match` kifejezés szerkezetét. 

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

A következő példa egy buborék rétegben lévő pont funkció `entityType` tulajdonságát keresi meg. Ha egyezést talál, a rendszer a megadott értéket adja vissza, vagy visszaadja a tartalék értéket.

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

Az alábbi példa egy tömböt használ a címkék egy halmazának listázásához, amelynek minden esetben ugyanazt az értéket kell visszaadnia. Ez a megközelítés sokkal hatékonyabb, mint a címkék egyenkénti listázása. Ebben az esetben, ha a `entityType` tulajdonság "Restaurant" vagy "grocery_store", a "piros" színt adja vissza.

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

A következő példa egy egyezési kifejezést használ egy "in Array" vagy "Array" típusú szűrő végrehajtásához. Ebben az esetben a kifejezés a megengedett azonosítók listáján szereplő azonosító értékkel rendelkező értékeket szűri. Ha szűrőket használó kifejezéseket használ, az eredménynek logikai értéknek kell lennie.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>Egyesítő kifejezés

Egy `coalesce` kifejezés a kifejezések egy halmazán halad át, amíg az első nem null értéket nem sikerült beolvasni, és ezt az értéket adja vissza. 

A következő pseudocode határozzák meg a `coalesce` kifejezés szerkezetét. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Példa**

Az alábbi példa egy `coalesce` kifejezést használ egy szimbólum réteg `textField` beállításának beállításához. Ha a `title` tulajdonság hiányzik a szolgáltatásból, vagy a `null`értékre van állítva, a kifejezés ezután megpróbálja a `subtitle` tulajdonságot használni, ha a hiányzó vagy a `null`, akkor a rendszer visszaállítja egy üres karakterláncra. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

A következő példa egy `coalesce` kifejezést használ a Térkép sprite-ban elérhető első elérhető rendszerkép lekérésére a megadott képnevekből származó listából.

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

## <a name="type-expressions"></a>Típus kifejezések

A Type kifejezések a különböző adattípusok (például karakterláncok, számok és logikai értékek) tesztelésére és átalakítására szolgáló eszközöket biztosítanak.

| Kifejezés | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | tömb \| objektum | Egy konstans tömb vagy objektum értékét adja vissza. Ezzel a kifejezéssel megakadályozható, hogy egy tömb vagy objektum kifejezésként legyen kiértékelve. Erre akkor van szükség, ha egy tömböt vagy objektumot egy kifejezésnek kell visszaadnia. |
| `['image', string]` | sztring | Ellenőrzi, hogy a megadott rendszerkép-azonosító be van-e töltve a Maps-rendszerkép sprite-ba. Ha igen, a rendszer visszaadja az azonosítót, ellenkező esetben null értéket ad vissza. |
| `['to-boolean', value]` | logikai | A bemeneti értéket logikai értékre alakítja. Az eredmény `false`, ha a bemenet üres karakterlánc, `0`, `false`, `null`vagy `NaN`; Ellenkező esetben a `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | szín | Átalakítja a bemeneti értéket egy színre. Ha több érték van megadva, a rendszer mindegyiket kiértékeli, amíg meg nem történik az első sikeres konverzió. Ha a bemenetek egyike sem alakítható át, akkor a kifejezés hibát jelez. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | szám | Ha lehetséges, átalakítja a bemeneti értéket egy számra. Ha a bemenet `null` vagy `false`, az eredmény 0. Ha a bemenet `true`, az eredmény 1. Ha a bemenet egy karakterlánc, akkor a rendszer a ECMAScript nyelvi specifikáció [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) string függvényével egy számra konvertálja. Ha több érték van megadva, a rendszer mindegyiket kiértékeli, amíg meg nem történik az első sikeres konverzió. Ha a bemenetek egyike sem alakítható át, akkor a kifejezés hibát jelez. |
| `['to-string', value]` | sztring | A bemeneti értéket karakterlánccá alakítja. Ha a bemenet `null`, az eredmény `""`. Ha a bemenet logikai érték, az eredmény `"true"` vagy `"false"`. Ha a bemenet egy szám, a rendszer a ECMAScript nyelv specifikációjának [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) Number függvényét használva karakterlánccá alakítja át. Ha a bemenet színes, a rendszer átalakítja a CSS RGBA `"rgba(r,g,b,a)"`. Ellenkező esetben a rendszer a bemenetet egy karakterlánccá alakítja át a ECMAScript nyelvi specifikációjának [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) funkciója segítségével. |
| `['typeof', value]` | sztring | A megadott érték típusát leíró karakterláncot ad vissza. |

> [!TIP]
> Ha az `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].`hoz hasonló hibaüzenet jelenik meg a böngésző konzolján, akkor az azt jelenti, hogy a kódban van egy olyan kifejezés, amelynek tömbje nem tartalmaz karakterláncot az első értékhez. Ha azt szeretné, hogy a kifejezés egy tömböt ad vissza, zárja be a tömböt a `literal` kifejezéssel. Az alábbi példa egy szimbólum réteg ikon `offset` beállítását állítja be, amelynek két számot tartalmazó tömbnek kell lennie, egy `match` kifejezéssel, amely két eltolási érték közül választhat a pont funkció `entityType` tulajdonságának értéke alapján.
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

| Kifejezés | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | szín | Egy színértéket hoz létre a *vörös*, *zöld*és *kék* összetevőkből, amelyeknek `0` és `255`között kell lenniük, valamint `1`alfa-összetevőjének. Ha bármelyik összetevő tartományon kívül esik, a kifejezés hibát jelez. |
| `['rgba', number, number, number, number]` | szín | Egy színértéket hoz létre a *vörös*, *zöld*és *kék* összetevők között, amelyeknek `0` és `255`között kell lenniük, és egy alfa-összetevőnek `0` és `1`tartományon belül kell lennie. Ha bármelyik összetevő tartományon kívül esik, a kifejezés hibát jelez. |
| `['to-rgba']` | \[szám, szám, szám, szám\] | Egy négy elemből álló tömböt ad vissza, amely a bemeneti szín *vörös*, *zöld*, *kék*és *alfa* összetevőit tartalmazza ebben a sorrendben. |

**Példa**

Az alábbi példa egy olyan RGB színértéket hoz létre, amely a `255`*vörös* értékkel rendelkezik, és a kiszámított *zöld* és *kék* értékeket a `temperature` tulajdonság értéke alapján `2.5` szorozva. Ahogy a hőmérséklet változik, a szín különböző árnyalatú *vörös*színűre változik.

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

| Kifejezés | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['concat', string, string, …]` | sztring | Több karakterlánc összefűzése egymással. Minden értéknek sztringnek kell lennie. Ha szükséges, használja a `to-string` Type kifejezést más típusú értékek karakterlánccá alakításához. |
| `['downcase', string]` | sztring | A megadott karakterláncot kisbetűsre alakítja. |
| `['upcase', string]` | sztring | A megadott karakterláncot nagybetűssé alakítja. |

**Példa**

Az alábbi példa átalakítja a pont szolgáltatás `temperature` tulajdonságát egy karakterláncra, majd a végéhez összefűzi a "°F" karakterláncot.

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

![karakterlánc-kezelő kifejezés például](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Interpolációs és Step kifejezések

Az interpolációs és a Step kifejezésekkel az értékek kiszámíthatók az interpolált görbe vagy a Step függvény használatával. Ezek a kifejezések olyan kifejezéssel rendelkeznek, amely egy numerikus értéket ad vissza bemenetként, például `['get',  'temperature']`. A bemeneti érték kiértékelése a bemeneti és a kimeneti érték párokkal történik, hogy meghatározza az interpolált görbe vagy a Step függvénynek legjobban illeszkedő értéket. A kimeneti értékeket "leáll" értéknek nevezzük. Az egyes leállítás bemeneti értékének számnak kell lennie, és növekvő sorrendben kell lennie. A kimeneti értékeknek számnak, számok tömbének vagy színnek kell lenniük.

### <a name="interpolate-expression"></a>Interpolációs kifejezés

Egy `interpolate` kifejezéssel folytonos és simított értékeket lehet kiszámítani a leállítási értékek közötti interpolációval. A színértékeket visszaadó `interpolate` kifejezés olyan színátmenetet eredményez, amelyben a rendszer kijelöli az eredmények értékét.

A `interpolate`-kifejezésekben háromféle interpolációs módszert használhat:
 
* `['linear']` – lineárisan interpolált a leállások párosítása között.
* `['exponential', base]` – a leállítások között exponenciálisan interpolált. A `base` érték határozza meg, hogy a kimenet milyen sebességgel növekszik. A nagyobb értékek miatt a kimenet nagyobb mértékben növekszik a tartomány magas végén. Az 1. `base` érték olyan kimenetet eredményez, amely lineárisan növekszik.
* `['cubic-bezier', x1, y1, x2, y2]` – a megadott vezérlési pontok által definiált [köbös Bezier-görbe](https://developer.mozilla.org/docs/Web/CSS/timing-function) használatával Interpolációk.

Íme egy példa arra, hogy a különböző típusú Interpolációk hogyan néznek ki. 

| Lineáris  | Exponenciális | Köbméter Bezier |
|---------|-------------|--------------|
| ![Lineáris interpolációs gráf](media/how-to-expressions/linear-interpolation.png) | ![Exponenciális interpolációs gráf](media/how-to-expressions/exponential-interpolation.png) | ![Köbméter Bezier-interpolációs gráf](media/how-to-expressions/bezier-curve-interpolation.png) |

A következő pseudocode határozzák meg a `interpolate` kifejezés szerkezetét. 

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

Az alábbi példa egy `linear interpolate` kifejezést használ a buborék réteg `color` tulajdonságának beállításához a pont funkció `temperature` tulajdonsága alapján. Ha a `temperature` értéke kisebb, mint 60, akkor a "Blue" értéket adja vissza. Ha 60-es és 70-nál kisebb, akkor a sárga értéket adja vissza. Ha 70 és kevesebb, mint 80, a rendszer a "narancssárga" értéket adja vissza. Ha 80 vagy nagyobb, akkor a rendszer a "vörös" értéket adja vissza.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

Az alábbi képen látható, hogyan választják ki a színeket a fenti kifejezéshez.
 
<center>

![interpolációs kifejezés példája](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Lépés kifejezése

`step` kifejezéssel kiszámíthatja a különálló, lépcsőzetes eredmények értékét a leállások által definiált [piecewise függvény](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) kiértékelésével. 

A következő pseudocode határozzák meg a `step` kifejezés szerkezetét. 

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

Az alábbi példa egy `step` kifejezést használ a buborék réteg `color` tulajdonságának beállításához a pont funkció `temperature` tulajdonsága alapján. Ha a `temperature` értéke kisebb, mint 60, akkor a "Blue" értéket adja vissza. Ha 60 és kevesebb, mint 70, a rendszer a "sárga" értéket adja vissza. Ha 70 és kevesebb, mint 80, a rendszer a "narancssárga" értéket adja vissza. Ha 80 vagy nagyobb, akkor a rendszer a "vörös" értéket adja vissza.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

Az alábbi képen látható, hogyan választják ki a színeket a fenti kifejezéshez.
 
<center>

![lépés kifejezése például](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Réteg-specifikus kifejezések

Speciális kifejezések, amelyek csak bizonyos rétegekre érvényesek.

### <a name="heat-map-density-expression"></a>Hő-Térkép sűrűségének kifejezése

A Heat Map sűrűség kifejezés lekérdezi a Heat Térkép sűrűségének értékét a Heat Térkép réteg minden egyes képpontjához, és `['heatmap-density']`ként van definiálva. Ez az érték `0` és `1`közötti szám. A rendszer `interpolation` vagy `step` kifejezéssel együtt használja, hogy meghatározza a Heat Térkép színezéséhez használt színátmenetet. Ez a kifejezés csak a Heat Map réteg [szín beállításában](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) használható.

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

Amellett, hogy a zökkenőmentes átmenetet egy hő-Térkép színezésére használja, a színek egy `step` kifejezés használatával adhatók meg a tartományokon belül. Ha `step` kifejezést használ a Heat Térkép színezésére, a rendszer vizuálisan lebontja a sűrűséget olyan tartományokra, amelyek egy kontúr vagy egy radar stílusú térképhez hasonlítanak.  

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

Egy sor folyamatjelző kifejezés lekérdezi az előrehaladást egy vonal rétegében lévő átmenetes vonal mentén, és `['line-progress']`ként van definiálva. Ez az érték 0 és 1 közötti szám. `interpolation` vagy `step` kifejezéssel együtt használható. Ez a kifejezés csak a vonal rétegének [strokeGradient kapcsolóval]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) használható. 

> [!NOTE]
> A vonal rétegének `strokeGradient` beállításához az adatforrás `lineMetrics` beállításának `true`értékre kell állítani.

**Példa**

Ez a példa a `['line-progress']` kifejezést használja a színátmenetek egy vonal körvonalára való alkalmazásához.

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

A Text mező formázása kifejezés a Symbol Layers `textOptions` tulajdonság `textField` beállításával használható vegyes szövegformázás biztosításához. Ez a kifejezés lehetővé teszi a bemeneti karakterláncok és a formázási beállítások megadását. Ebben a kifejezésben a következő beállítások adhatók meg minden bemeneti karakterlánchoz.

 * `'font-scale'` – a betűméret méretezési tényezőjét határozza meg. Ha meg van adva, ez az érték felülbírálja az egyes karakterláncok `textOptions` `size` tulajdonságát.
 * `'text-font'` – egy vagy több olyan betűkészlet-családot határoz meg, amelyet ehhez a karakterlánchoz kell használni. Ha meg van adva, ez az érték felülbírálja az egyes karakterláncok `textOptions` `font` tulajdonságát.
 * `'text-color'` – a szövegre alkalmazandó színt adja meg megjelenítéskor. 

A következő pseudocode határozzák meg a szöveg mező formázása kifejezés szerkezetét. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**Példa**

A következő példa egy félkövér betűkészlet hozzáadásával formázza a szövegmezőt, és a funkció `title` tulajdonságának betűméretét. A példában a funkció `subtitle` tulajdonsága is megjelenik egy sortörésen, és a méretezési betűméret és a piros színű.

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

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

Ez a réteg az alábbi képen látható módon fogja megjeleníteni a pont funkciót:
 
<center>

![](media/how-to-expressions/text-field-format-expression.png) formázott szöveggel rendelkező pont funkciójának ábrázolása </center>

### <a name="number-format-expression"></a>Számformátum kifejezése

A `number-format` kifejezés csak szimbólum réteg `textField` kapcsolóval használható. Ez a kifejezés egy formázott karakterlánccá alakítja át a megadott számot. Ez a kifejezés a JavaScript [Number. toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) függvényt csomagolja be, és a következő beállításokat támogatja.

 * `locale` – Itt adhatja meg, hogy a rendszer hogyan konvertálja a számokat karakterláncokra úgy, hogy az a megadott nyelven legyen igazítva. Adja át a [BCP 47 Language címkét](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) ebbe a beállításba.
 * `currency` – a számot egy pénznemet jelölő sztringre alakíthatja. A lehetséges értékek az [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217)-es pénznem-kódok, például az USA-dollár "USD", az "EUR" az euro esetében, vagy a kínai RMB "CNY".
 * `'min-fraction-digits'` – a szám karakterlánc-verziójában szerepeltetni kívánt tizedesjegyek minimális számát határozza meg.
 * `'max-fraction-digits'` – a szám karakterlánc-verziójában szerepeltetni kívánt tizedesjegyek maximális számát adja meg.

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

Az alábbi példa egy `number-format` kifejezéssel módosítja, hogy a pont funkció `revenue` tulajdonsága hogyan legyen megjelenítve egy szimbólum-réteg `textField` beállításában úgy, hogy az az USA-dollár értékét jelenítse meg.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Ez a réteg az alábbi képen látható módon fogja megjeleníteni a pont funkciót:

<center>

![a számformátum-kifejezés példája](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Nagyítás kifejezése

`zoom` kifejezéssel lehet lekérdezni a Térkép jelenlegi nagyítási szintjét a leképezés időpontjában, és `['zoom']`ként van definiálva. Ez a kifejezés a Térkép minimális és maximális nagyítási szintje közötti számot adja vissza. Azure Maps a web és Android rendszerhez készült interaktív térkép-vezérlőelemek 25 nagyítási szintet (0 – 24) támogatnak. A `zoom` kifejezés használata lehetővé teszi a stílusok dinamikus módosítását, mivel a Térkép nagyítási szintje megváltozik. A `zoom` kifejezés csak `interpolate` és `step` kifejezésekkel használható.

**Példa**

Alapértelmezés szerint a Heat Map rétegben megjelenített adatpontok sugara rögzített képpont-sugárral rendelkezik az összes nagyítási szinthez. Ahogy a Térkép nagyítva van, az adatösszesítések együtt, a Heat Map-réteg pedig eltérőnek tűnik. Egy `zoom` kifejezés használható a sugár méretezésére az egyes nagyítási szintekhez úgy, hogy az egyes adatpontok a Térkép ugyanazon fizikai területére terjednek ki. Így a Heat Térkép rétegének statikus és konzisztensnek kell lennie. A Térkép minden nagyítási szintje kétszer annyi képpontot tartalmaz függőlegesen és vízszintesen, mint az előző nagyítási szint. A sugár skálázása úgy, hogy az minden nagyítási szinten megduplázódik, egy olyan hő-térképet hoz létre, amely minden nagyítási szinten konzisztensnek tűnik. A `zoom` kifejezéssel egy `base 2 exponential interpolation` kifejezéssel is elvégezhető az alább látható módon. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[Lásd az élő példát](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Változó kötési kifejezések

Változó kötési kifejezések a számítások eredményeit tárolják egy változóban. Így a számítási eredmények több alkalommal is hivatkozhatnak egy kifejezésben máshol. Hasznos optimalizálás olyan kifejezések esetében, amelyek sok számítást tartalmaznak.

| Kifejezés | Visszatérési típus | Leírás |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Let",<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;érték1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;érték2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;...<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Egy vagy több értéket tárol változóként, amelyet az eredményt visszaadó gyermek kifejezés `var` kifejezése használ. |
| `['var', name: string]` | bármely | Egy olyan változóra hivatkozik, amely a `let` kifejezés használatával lett létrehozva. |

**Példa**

Ez a példa egy olyan kifejezést használ, amely a bevételt a hőmérséklet arányhoz viszonyítva kiszámítja, majd egy `case` kifejezés használatával értékeli ki a különböző logikai műveleteket ezen az értéken. A `let` kifejezés a bevétel hőmérsékleti arányhoz viszonyított tárolására szolgál, így csak egyszer kell kiszámítani. A `var` kifejezés a szükségesnél gyakrabban hivatkozik erre a változóra anélkül, hogy újra kellene számítania.

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
> [Szimbólum réteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Buborék réteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Vonal rétegének hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszög réteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Heat Map-réteg hozzáadása](map-add-heat-map-layer.md)

További információ a kifejezéseket támogató rétegbeli lehetőségekről:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
