---
title: StringToArray Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function StringToArray Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7ae1f69e92e890daae528eb1f4dfb95f76560043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337982"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy tömbre fordított kifejezést ad vissza. Ha a kifejezés nem fordítható le, a nem definiált értéket adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés, amely JSON-tömb kifejezésként lesz értelmezve. 
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy tömböt megadó kifejezést vagy nem definiált értéket ad vissza. 
  
## <a name="remarks"></a>Megjegyzések
  A beágyazott karakterlánc-értékeket idézőjelek közé kell írni, hogy érvényes JSON legyen. A JSON formátumával kapcsolatos részletekért lásd: [JSON.org](https://json.org/). Ez a rendszerfüggvény nem fogja használni az indexet.
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan viselkedik a `StringToArray` különböző típusok között. 
  
 Az alábbi példák érvényes bemenettel rendelkeznek.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Itt látható az eredményhalmaz.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Az alábbi példa érvénytelen bemenetet mutat be. 
   
 A tömbben lévő szimpla idézőjelek nem érvényesek a JSON-ban.
Annak ellenére, hogy egy lekérdezésen belül érvényesek, nem fogják értelmezni az érvényes tömböket. A tömb sztringjét tartalmazó karakterláncoknak Escape-"[" "]" karakternek kell lenniük \\ \\ , vagy a környező idézőjelnek egyetlen "[" "]" értékűnek kell lennie.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Itt látható az eredményhalmaz.

```json
[{}]
```

A következő példák érvényes bemenetre mutatnak.
   
 Az átadott kifejezés JSON-tömbként lesz értelmezve; a következő nem értékeli ki a tömböt, ezért a nem definiált értéket adja vissza.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Itt látható az eredményhalmaz.

```json
[{}]
```

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
