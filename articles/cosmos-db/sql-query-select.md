---
title: SELECT záradék Azure Cosmos DB
description: További információ az Azure Cosmos DB SQL SELECT záradékáról. Az SQL használata Azure Cosmos DB JSON-lekérdezési nyelvként.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 072e17b1c0ea312b4adfa1687e447fd2cadde233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335440"
---
# <a name="select-clause-in-azure-cosmos-db"></a>SELECT záradék Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Minden lekérdezés tartalmaz egy `SELECT` záradékot, és nem kötelező [a](sql-query-from.md) és [Where](sql-query-where.md) záradékok alapján, az ANSI SQL-szabványoknak megfelelően. A záradékban általában a forrás szerepel `FROM` , a `WHERE` záradék pedig egy szűrőt alkalmaz a forráson a JSON-elemek egy részhalmazának lekéréséhez. `SELECT`Ezután a záradék a kért JSON-értékeket a kiválasztási listán adja meg.

## <a name="syntax"></a>Szintaxis

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>Argumentumok
  
- `<select_specification>`  

  Az eredményhalmaz számára kijelölhető tulajdonságok vagy értékek.  
  
- `'*'`  

  Megadja, hogy az értéket a módosítások végrehajtása nélkül kell beolvasni. Különösen, ha a feldolgozott érték egy objektum, az összes tulajdonságot lekéri a rendszer.  
  
- `<object_property_list>`  
  
  Meghatározza a lekérdezni kívánt tulajdonságok listáját. Minden visszaadott érték egy objektum lesz, amely a megadott tulajdonságokkal rendelkezik.  
  
- `VALUE`  

  Megadja, hogy a JSON-értéket a teljes JSON-objektum helyett le kell olvasni. Ezzel ellentétben `<property_list>` a nem zárja be a tervezett értéket egy objektumban.  

- `DISTINCT`
  
  Megadja, hogy el kell távolítani a kijelzett tulajdonságok másodpéldányait.  

- `<scalar_expression>`  

  A kiszámítani kívánt értéket jelölő kifejezés. Részletekért lásd a [skaláris kifejezések](sql-query-scalar-expressions.md) szakaszt.  

## <a name="remarks"></a>Megjegyzések

A `SELECT *` szintaxis csak akkor érvényes, ha a from záradék pontosan egy aliast deklarált. `SELECT *` identitás-kivetítést biztosít, amely akkor lehet hasznos, ha nincs szükség leképezésre. A SELECT * utasítás csak akkor érvényes, ha a FROM záradék meg van adva, és csak egyetlen bemeneti forrást mutat be.  
  
Mindkettő `SELECT <select_list>` `SELECT *` "szintaktikai cukor", és az alábbiakban látható egyszerű Select utasításokkal is megadható.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   egyenértékű a következővel:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   egyenértékű a következővel:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Példák

A következő SELECT lekérdezési példa azt adja vissza `address` , hogy `Families` mely `id` egyezések közül választhat `AndersenFamily` :

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmény a következő:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a name="next-steps"></a>Következő lépések

- [Bevezetés](sql-query-getting-started.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE záradék](sql-query-where.md)
