---
title: Azure Cosmos DB lekérdezési nyelv ARRAY_CONTAINS
description: Ismerje meg, hogy a tömb hogyan tartalmazza az SQL System függvényt a Azure Cosmos DB egy logikai értéket ad vissza, amely jelzi, hogy a tömb tartalmazza-e a megadott értéket
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ff88d66ee6d1ceee4f1cf4e7b6501ab323dbc79e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93332661"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Egy olyan logikai érték beolvasása, amely azt jelzi, hogy a tömb tartalmazza-e a megadott értéket. Egy objektum részleges vagy teljes egyezését a parancsban található logikai kifejezés használatával tekintheti meg. 

## <a name="syntax"></a>Szintaxis
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*arr_expr*  
   A keresendő tömb kifejezése.  
  
*kifejezés*  
   A következő kifejezés található:.  

*bool_expr*  
   Egy logikai kifejezés. Ha a értéke "true" (igaz), és ha a megadott keresési érték egy objektum, a parancs részleges egyezést keres (a keresési objektum az egyik objektum részhalmaza). Ha "false" (hamis) értéket ad vissza, a parancs a tömbben lévő összes objektum teljes egyezését ellenőrzi. Ha nincs megadva, az alapértelmezett érték false (hamis). 
  
## <a name="return-types"></a>Visszatérési típusok
  
  Logikai értéket ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa egy tömb tagságának ellenőrzését mutatja be a használatával `ARRAY_CONTAINS` .  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"b1": true, "b2": false}]  
```  

A következő példa azt mutatja be, hogyan lehet egy tömbben lévő JSON részleges egyezését megkeresni ARRAY_CONTAINS használatával.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Array függvények Azure Cosmos DB](sql-query-array-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
