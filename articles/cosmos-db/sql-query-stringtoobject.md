---
title: StringToObject Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function StringToObject Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 369c86c8fc2ef4d125c3f13db4eb3af88b7e726e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337846"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy objektumra fordított kifejezést ad vissza. Ha a kifejezés nem fordítható le, a nem definiált értéket adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés, amelyet JSON-objektum kifejezésként kell elemezni. Vegye figyelembe, hogy a beágyazott karakterlánc-értékeket idézőjelek közé kell írni, hogy érvényesek legyenek. A JSON formátumával kapcsolatos részletekért lásd: [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy objektum kifejezését adja vissza, vagy nincs meghatározva.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan viselkedik a `StringToObject` különböző típusok között. 
  
 Az alábbi példák érvényes bemenettel rendelkeznek.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Itt látható az eredményhalmaz.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 A következő példák érvénytelen bemenettel rendelkeznek.
Annak ellenére, hogy egy lekérdezésen belül érvényesek, nem lesznek értelmezve érvényes objektumokra. Az objektumon belüli karakterláncokat "{ \\ " a \\ ":" Str "}" karakterrel kell megszökni, \\ \\ vagy a környező idézőjelnek egyetlen "{" a ":" Str "}" értékűnek kell lennie.

A környező tulajdonságok neveinek egyetlen idézőjele nem érvényes JSON.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Itt látható az eredményhalmaz.

```json
[{}]
```  

A környező idézőjelek nélküli tulajdonságnév nem érvényes JSON.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Itt látható az eredményhalmaz.

```json
[{}]
``` 

A következő példák érvénytelen bemenettel rendelkeznek.

 Az átadott kifejezés JSON-objektumként lesz értelmezve; Ezek a bemenetek nem értékelik ki az objektum típusát, így a nem definiált értéket adja vissza.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Itt látható az eredményhalmaz.

```json
[{}]
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
