---
title: A JSON használata az Azure Cosmos DB-ben
description: További információ a beágyazott JSON-tulajdonságok lekérdezéséről és eléréséről, valamint speciális karakterek használata Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/19/2020
ms.author: tisande
ms.openlocfilehash: 9a9300db1adc3ff238c44887012400702690b0e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337829"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>A JSON használata az Azure Cosmos DB-ben
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az Azure Cosmos DB SQL (Core) API-ja JSON formátumban tárolja az elemeket. A típusrendszer és a kifejezések kizárólag JSON-típusokkal működnek. További információ: [JSON-specifikáció](https://www.json.org/).

Összefoglaljuk a JSON használatának néhány fontos aspektusát:

- A JSON-objektumok mindig a `{` bal oldali kapcsos zárójelmel kezdődnek, és a `}` jobb oldali kapcsos zárójelekkel végződik
- A JSON-tulajdonságok egymásba [ágyazhatók](#nested-properties) .
- A JSON-tulajdonság értékei tömbök lehetnek
- A JSON-tulajdonságnév megkülönbözteti a kis-és nagybetűket
- A JSON-tulajdonságnév bármilyen karakterlánc-érték lehet (beleértve a szóközöket vagy a nem betűket tartalmazó karaktereket is).

## <a name="nested-properties"></a>Beágyazott tulajdonságok

A beágyazott JSON-t egy dot-elérő használatával érheti el. A lekérdezésekben a beágyazott JSON-tulajdonságokat ugyanúgy használhatja, mint bármely más tulajdonságot.

Beágyazott JSON-val rendelkező dokumentum:

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Ebben az esetben a `state` , a `country` és a `city` Tulajdonságok a tulajdonságon belül vannak beágyazva `address` .

A következő példa két beágyazott tulajdonságot tervez: `f.address.state` és `f.address.city` .

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmény a következő:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="working-with-arrays"></a>Tömbök használata

A beágyazott tulajdonságok mellett a JSON is támogatja a tömböket.

Az alábbi példa egy tömböt tartalmazó dokumentumot mutat be:

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

A tömbök használatakor a tömbön belül egy adott elemre lehet hozzáférni a pozícióra hivatkozva:

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

A legtöbb esetben azonban [allekérdezést](sql-query-subquery.md) vagy [önillesztést](sql-query-join.md) használ a tömbök használatakor.

Például itt egy olyan dokumentum látható, amely az ügyfél bankszámlájának napi egyenlegét jeleníti meg.

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

Ha olyan lekérdezést szeretne futtatni, amely az összes olyan ügyfelet megmutatta, aki negatív egyenleggel rendelkezett egy bizonyos ponton, akkor már [létezik](sql-query-subquery.md#exists-expression) egy segédlekérdezés:

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="difference-between-null-and-undefined"></a>Null és nem definiált érték közötti különbség

Ha egy tulajdonság nincs definiálva egy elemben, akkor annak értéke `undefined` . Az értékkel rendelkező tulajdonságot `null` explicit módon meg kell határozni, és hozzá kell rendelni egy `null` értéket.

Vegyük például ezt a minta tételt:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": null
}
```

Ebben a példában a tulajdonság `isRegistered` értéke, `undefined` mert ki van hagyva az elemből. A tulajdonság `creationDate` `null` értéke.

Azure Cosmos DB a következő két hasznos típust támogatja: a és a tulajdonságok ellenőrzése a rendszerfunkciókkal `null` `undefined` :

* [IS_NULL](sql-query-is-null.md) – ellenőrzi, hogy a tulajdonság értéke `null`
* [IS_DEFINED](sql-query-is-defined.md) – ellenőrzi, hogy a tulajdonság értéke definiálva van-e

Megismerheti a [támogatott operátorokat](sql-query-operators.md) és azok viselkedését `null` és `undefined` értékeit.

## <a name="reserved-keywords-and-special-characters-in-json"></a>Fenntartott kulcsszavak és speciális karakterek a JSON-ban

A tulajdonságokat az idézett tulajdonság operátor használatával érheti el `[]` . Például a `SELECT c.grade` és `SELECT c["grade"]` a egyenértékű. Ez a szintaxis akkor hasznos, ha olyan tulajdonságot szeretne elmenekülni, amely szóközt, speciális karaktereket vagy egy SQL-kulcsszó vagy fenntartott szó nevét tartalmazza.

Tegyük fel például, hogy egy olyan dokumentum található, amely egy nevű tulajdonságot `order` és egy `price($)` speciális karaktereket tartalmazó tulajdonságot tartalmaz:

```json
{
  "id": "AndersenFamily",
  "order": {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
   },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Ha a `order` tulajdonságot vagy tulajdonságot tartalmazó lekérdezéseket futtat `price($)` , szintaktikai hiba jelenik meg.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

Az eredmény a következőket eredményezi:

`
Syntax error, incorrect syntax near 'order'
`

Az alábbihoz hasonló lekérdezéseket kell újraírnia:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>JSON-kifejezések

A vetítés a JSON-kifejezéseket is támogatja, ahogy az az alábbi példában is látható:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmény a következő:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

Az előző példában a `SELECT` záradéknak létre kell hoznia egy JSON-objektumot, és mivel a minta nem tartalmaz kulcsot, a záradék az implicit argumentum változó nevét használja `$1` . A következő lekérdezés két implicit argumentum változót ad vissza: `$1` és `$2` .

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmény a következő:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      },
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="aliasing"></a>Aliasok használata

Explicit módon alias értékeket a lekérdezésekben. Ha egy lekérdezésnek két azonos nevű tulajdonsága van, az alias használatával nevezze át az egyik vagy mindkét tulajdonságot, hogy disambiguated a tervezett eredményben.

### <a name="examples"></a>Példák

Az `AS` aliashoz használt kulcsszó nem kötelező, ahogy az alábbi példában is látható, amikor a második értéket a következőként tervezi meg `NameInfo` :

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmény a következő:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>Alias a fenntartott kulcsszavakkal vagy speciális karakterekkel

Az aliasok nem használhatók olyan tulajdonság neveként, amely szóközt, speciális karaktert vagy fenntartott szót használ. Ha például egy érték kivetítését szeretné módosítani egy szóközzel, használhat egy [JSON-kifejezést](#json-expressions).

Bemutatunk egy példát:

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>Következő lépések

- [Bevezetés](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [WHERE záradék](sql-query-where.md)
