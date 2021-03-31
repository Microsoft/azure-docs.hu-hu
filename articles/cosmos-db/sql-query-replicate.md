---
title: REPLIKÁLÁS Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System függvény REPLIKÁLÁSával Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7c0f1673c620ceadeb5ccca2a15cc9b7ce8d7685
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341603"
---
# <a name="replicate-azure-cosmos-db"></a>REPLIKÁLÁS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Megadott számú alkalommal ismétli meg a karakterláncot.
  
## <a name="syntax"></a>Szintaxis
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.
  
*num_expr*  
   Egy numerikus kifejezés. Ha *num_expr* negatív vagy nem véges, az eredmény nincs meghatározva.
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezést ad vissza.
  
## <a name="remarks"></a>Megjegyzések

  Az eredmény maximális hossza 10 000 karakter, azaz (hossz (*str_expr*) *  *num_expr*) <= 10 000. Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan használható `REPLICATE` egy lekérdezésben.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Itt látható az eredményhalmaz.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
