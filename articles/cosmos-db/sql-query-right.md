---
title: JOBB Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System függvénysel közvetlenül a Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: be950fed563d8e80339dc80181e7fe5a78092734
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341576"
---
# <a name="right-azure-cosmos-db"></a>JOBB (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 A megadott számú karakterből álló karakterlánc jobb oldali részét adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   A karakterlánc-kifejezés kinyerni a karaktereket a következőből:.  
  
*num_expr*  
   Egy numerikus kifejezés, amely a karakterek számát határozza meg.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa az "ABC" jobb részét adja vissza a különböző hosszúságú értékekhez.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
