---
title: SQL-allekérdezések Azure Cosmos DB
description: Ismerje meg az SQL allekérdezéseket és azok gyakori használati eseteit és a különböző típusú allekérdezéseket Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: f5f209229d17a2587258d21ee90e7560e629d082
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340855"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Példák az SQL allekérdezésre Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A segédlekérdezés egy másik lekérdezésbe ágyazott lekérdezés. A segédlekérdezés belső lekérdezésnek vagy belső kijelölésnek is nevezik. Az allekérdezést tartalmazó utasítást általában külső lekérdezésnek nevezzük.

Ez a cikk az SQL allekérdezéseket és azok gyakori használati eseteit ismerteti Azure Cosmos DBban. Az ebben a dokumentumban található összes lekérdezés futtatható egy olyan táplálkozási adatkészleten, amely előre be van töltve a [Azure Cosmos DB lekérdezési demókörnyezet](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Allekérdezések típusai

Az allekérdezéseknek két fő típusa van:

* **Korrelált**: olyan segédlekérdezés, amely a külső lekérdezés értékeit hivatkozik. A segédlekérdezés egyszer lesz kiértékelve minden olyan sorban, amelyet a külső lekérdezés feldolgoz.
* **Nem korrelált**: olyan segédlekérdezés, amely független a külső lekérdezéstől. A külső lekérdezésre való támaszkodás nélkül is futtatható.

> [!NOTE]
> A Azure Cosmos DB csak a korrelált allekérdezéseket támogatja.

Az allekérdezések tovább csoportosíthatók a visszaadott sorok és oszlopok száma alapján. Háromféle típus létezik:
* **Tábla**: több sort és több oszlopot ad vissza.
* **Többértékű**: több sort és egyetlen oszlopot ad vissza.
* **Skaláris**: egyetlen sort és egyetlen oszlopot ad vissza.

Azure Cosmos DB SQL-lekérdezések mindig egyetlen oszlopot adnak vissza (egyszerű vagy összetett dokumentum). Ezért a Azure Cosmos DBban csak a többértékű és a skaláris allekérdezések alkalmazhatók. A FROM záradékban csak a többértékű allekérdezés használható viszonyítási kifejezésként. Skaláris allekérdezést használhat skaláris kifejezésként a SELECT vagy WHERE záradékban, vagy egy viszonyítási kifejezésként a FROM záradékban.

## <a name="multi-value-subqueries"></a>Többértékű allekérdezések

A többértékű allekérdezések dokumentumok készletét adják vissza, és a FROM záradékban mindig használatosak. A következőket használják:

* ILLESZTÉSi kifejezések optimalizálása. 
* Költséges kifejezések kiértékelése egyszer és többszöri hivatkozással.

## <a name="optimize-join-expressions"></a>ILLESZTÉSi kifejezések optimalizálása

A többértékű allekérdezések optimalizálják az összekapcsolási kifejezéseket úgy, hogy az egyes Select-many kifejezéseket nem a WHERE záradékban lévő összes illesztés után küldi el.

Tekintse meg a következő lekérdezést:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Ebben a lekérdezésben az index minden olyan dokumentumnak megfelel, amelynek a neve "Infant formula" nevű címkével rendelkezik. Ez egy 0 és 10 közötti értéket tartalmazó tápanyag-elem, valamint egy kiszolgáló elem, amelynek értéke nagyobb, mint 1. A JOIN kifejezés itt hajtja végre a címkék és a tápanyagok összes elemének, valamint az összes egyező dokumentum tömbjét a szűrő alkalmazása előtt. 

A WHERE záradék Ezután alkalmazza a Filter predikátumot minden <c, t, n, s> rekordon. Ha például egy egyező dokumentum 10 elemet tartalmaz mindhárom tömbben, akkor az 1 x 10 x 10 x 10 (azaz 1 000) rekordok bővül. Az allekérdezések használatával a következő kifejezéssel való csatlakozás előtt segíthet a csatlakoztatott tömbök kiszűrésében.

Ez a lekérdezés megegyezik az előzővel, de allekérdezéseket használ:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Tegyük fel, hogy a címkék tömbben csak egy elem felel meg a szűrőnek, és öt elem van a tápanyagok számára, és tömböket is kiszolgál. Az ILLESZTÉSi kifejezések ezután 1 x 1 x 5 x 5 = 25 elemre lesznek kiterjesztve, az első lekérdezés 1 000 elemével szemben.

## <a name="evaluate-once-and-reference-many-times"></a>Egyszeri kiértékelés és hivatkozás többször

Az allekérdezések olyan költséges kifejezésekkel optimalizálják a lekérdezéseket, mint a felhasználó által definiált függvények (UDF), az összetett karakterláncok vagy a aritmetikai kifejezések. A kifejezést egy JOIN kifejezéssel együtt használva kiértékelheti a kifejezést egyszer, de többször is hivatkozhat rá.

A következő lekérdezés kétszer futtatja az UDF-t `GetMaxNutritionValue` :

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Az alábbi egy egyenértékű lekérdezés, amely csak egyszer futtatja az UDF-t:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Ne feledje, hogy az ILLESZTÉSi kifejezések több termékkel kapcsolatos viselkedését is figyelembe kell venni. Ha az UDF-kifejezés kiértékelése nem definiált, akkor ügyeljen arra, hogy az ILLESZTÉSi kifejezés mindig egyetlen sort hozzon létre egy objektumnak az allekérdezésből való visszaadásával közvetlenül az érték helyett.
>

Az alábbi példa egy objektumot ad vissza egy érték helyett:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

A megközelítés nem korlátozódik a UDF. Ez minden potenciálisan költséges kifejezésre vonatkozik. Tegyük fel, hogy ugyanezt a módszert használja a matematikai függvénnyel `avg` :

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Külső referenciák összekapcsolásának szimulálása

Előfordulhat, hogy gyakran olyan statikus adatmennyiségre kell hivatkoznia, amely ritkán változik, például mértékegység-vagy országkódok. Jobb, ha nem ismétli meg az egyes dokumentumok adattartalmait. Ennek az ismétlődésnek az elkerülése érdekében a rendszer megtakarítja a tárterületet, és javítja az írási teljesítményt a dokumentum méretének csökkentésével. Az allekérdezéssel belső illesztésű szemantikai és hivatkozási adatgyűjteményeket is használhat.

Vegyük például a következő hivatkozási adatkészletet:

| **Unit (Egység)** | **Név**            | **Szorzó** | **Alapegység** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogrammos            | 1.00 e-09       | Gram          |
| μg       | Mikrogramm           | 1.00 e-06       | Gram          |
| mg       | Milligramm           | 1.00 e-03       | Gram          |
| g        | Gram                | 1.00 e + 00       | Gram          |
| kg       | Kilogramm            | 1.00 e + 03       | Gram          |
| Mg       | Megagram            | 1.00 e + 06       | Gram          |
| Gg       | Gigagram            | 1.00 e + 09       | Gram          |
| Egyet       | Nanojoule           | 1.00 e-09       | Joule         |
| μJ       | Aljoule          | 1.00 e-06       | Joule         |
| mJ       | Millijoule          | 1.00 e-03       | Joule         |
| J        | Joule               | 1.00 e + 00       | Joule         |
| kJ       | Kalápocsonkénti ütési           | 1.00 e + 03       | Joule         |
| MJ       | Megajoule           | 1.00 e + 06       | Joule         |
| GJ       | Gigajoule           | 1.00 e + 09       | Joule         |
| Cal      | Kalória             | 1.00 e + 00       | kalória       |
| kcal     | Kalória             | 1.00 e + 03       | kalória       |
| NE       | Nemzetközi egységek |                |               |


A következő lekérdezés utánozza az adatokat, hogy hozzáadja az egység nevét a kimenethez:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Skaláris allekérdezések

A skaláris allekérdezési kifejezés egy olyan segédlekérdezés, amely egyetlen értékre értékel ki. A skaláris allekérdezési kifejezés értéke a segédlekérdezés kivetítésének (SELECT záradékának) értéke.  Skaláris segédlekérdezés kifejezést használhat számos olyan helyen, ahol egy skaláris kifejezés érvényes. Használhat például egy skaláris allekérdezést bármely kifejezésben a SELECT és a WHERE záradékban.

Skaláris allekérdezés használata nem mindig segít az optimalizálásban, bár. Például, ha egy skaláris allekérdezést egy rendszer vagy felhasználó által definiált függvény argumentumként ad át, az erőforrás-egység (RU) használatának vagy késésének nem kell haszna.

A skaláris allekérdezések továbbra is csoportosíthatók:
* Egyszerű kifejezésű skaláris allekérdezések
* Összesített skaláris allekérdezések

## <a name="simple-expression-scalar-subqueries"></a>Egyszerű kifejezésű skaláris allekérdezések

Egy egyszerű kifejezéssel rendelkező skaláris segédlekérdezés olyan korrelált segédlekérdezés, amelynek SELECT záradéka nem tartalmaz összesítő kifejezést. Ezek az allekérdezések nem biztosítanak optimalizálási előnyöket, mert a fordító egy nagyobb egyszerű kifejezésre konvertálja őket. A belső és a külső lekérdezések között nincs korrelációs környezet.

Íme néhány példa:

**1\. példa**

```sql
SELECT 1 AS a, 2 AS b
```

Ezt a lekérdezést egy egyszerű kifejezéssel rendelkező skaláris segédlekérdezés használatával írhatja át a következőre:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Mindkét lekérdezés ezt a kimenetet hozza létre:

```json
[
  { "a": 1, "b": 2 }
]
```

**2\. példa**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Ezt a lekérdezést egy egyszerű kifejezéssel rendelkező skaláris segédlekérdezés használatával írhatja át a következőre:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Lekérdezés kimenete:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**3\. példa**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Ezt a lekérdezést egy egyszerű kifejezéssel rendelkező skaláris segédlekérdezés használatával írhatja át a következőre:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Lekérdezés kimenete:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Összesített skaláris allekérdezések

Az összesített skaláris segédlekérdezés olyan allekérdezés, amely összesítő függvényt tartalmaz a kivetítésben vagy a szűrőben, amely egyetlen értékre van kiértékelve.

**1. példa:**

Az alábbi allekérdezés egyetlen összesítő függvény kifejezéssel rendelkezik a kivetítés során:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Lekérdezés kimenete:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**2\. példa**

Íme egy segédlekérdezés több összesítő függvény kifejezéssel:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Lekérdezés kimenete:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**3\. példa**

Az alábbiakban egy összesítő allekérdezéssel rendelkező lekérdezés szerepel a vetítésben és a szűrőben:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Lekérdezés kimenete:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

A lekérdezés megírásának optimális módja, ha az allekérdezéshez csatlakozik, és az allekérdezési aliasra hivatkozik mind a SELECT, mind a WHERE záradékban. Ez a lekérdezés hatékonyabb, mert az allekérdezést csak a JOIN utasításon belül kell végrehajtania, nem pedig a vetítés és a szűrő között.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>LÉTEZIK kifejezés

Azure Cosmos DB támogatja a létező kifejezéseket. Ez egy összesített skaláris segédlekérdezés a Azure Cosmos DB SQL API-ba építve. LÉTEZIK egy olyan logikai kifejezés, amely allekérdezési kifejezést vesz fel, és igaz értéket ad vissza, ha az allekérdezés bármilyen sort visszaad. Ellenkező esetben hamis értéket ad vissza.

Mivel a Azure Cosmos DB SQL API nem tesz különbséget a logikai kifejezések és az egyéb skaláris kifejezések között, a SELECT és a WHERE záradékban is használható. Ez a T-SQL-vel ellentétben, ahol a logikai kifejezés (például létezik, a és a között) a szűrőre korlátozódik.

Ha a létező segédlekérdezés egyetlen, nem definiált értéket ad vissza, akkor a rendszer a hamis értéket fogja kiértékelni. Vegyük például a következő lekérdezést, amely hamis értéket ad vissza:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Ha az előző allekérdezésben szereplő VALUE kulcsszó ki van hagyva, a lekérdezés az igaz értéket fogja kiértékelni:
```sql
SELECT EXISTS (SELECT undefined) 
```

A segédlekérdezés egy objektum kiválasztott listájában lévő értékek listáját fogja csatolni. Ha a kiválasztott lista nem tartalmaz értékeket, a segédlekérdezés az "" egyetlen értéket fogja visszaadni {} . Ez az érték definiálva van, ezért a függvény Igaz értéket ad vissza.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Példa: ARRAY_CONTAINS újraírása és csatlakozás LÉTEZŐként

ARRAY_CONTAINS gyakori felhasználási esete, ha egy tömbben lévő elem létezésével szűr egy dokumentumot. Ebben az esetben ellenőrzi, hogy a címkék tömb tartalmazza-e a "narancssárga" nevű elemeket.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Ugyanazzal a lekérdezéssel is újraírható, hogy létezik:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Emellett a ARRAY_CONTAINS csak azt vizsgálhatja, hogy egy érték egyenlő-e egy tömbben lévő bármelyik elemmel. Ha összetettebb szűrőkre van szüksége a tömb tulajdonságainál, használja a JOIN (csatlakozás) parancsot.

Vegye figyelembe a következő lekérdezést, amely a tömb egységei és tulajdonságai alapján szűr `nutritionValue` : 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

A gyűjtemény összes dokumentuma esetében a rendszer a tömb elemeivel együttesen végzi a termék összevetését. Ez az ILLESZTÉSi művelet lehetővé teszi a tömbön belüli tulajdonságok szűrését. A lekérdezés RU-fogyasztása azonban jelentős lesz. Ha például az 1 000-dokumentumok mindegyik tömbben 100 elemek voltak, akkor a rendszer a 1 000 x 100 (azaz 100 000) rekordok bővíti.

A meglévő használata segíthet elkerülni ezt a költséges termékeket:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

Ebben az esetben a tömb elemeit szűrheti a létező allekérdezésen belül. Ha egy tömb elem megfelel a szűrőnek, akkor a projekt, és a kiértékelése igaz értékre van állítva.

Alias is létezik, és hivatkozhat a vetítésre:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Lekérdezés kimenete:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>TÖMB kifejezése

A tömb kifejezés használatával a lekérdezés eredményeit tömbként is kioszthatja. Ez a kifejezés csak a lekérdezés SELECT záradékán belül használható.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Lekérdezés kimenete:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Más allekérdezésekhez hasonlóan lehetséges a ARRAY kifejezéssel rendelkező szűrők is.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Lekérdezés kimenete:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

A tömb kifejezései az allekérdezésekben a FROM záradék után is származhatnak.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Lekérdezés kimenete:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Következő lépések

- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dokumentumadatok modellezése](modeling-data.md)
