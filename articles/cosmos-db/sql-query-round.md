---
title: Azure Cosmos DB lekérdezési nyelv KEREKÍTÉSe
description: Ismerkedjen meg az SQL System függvény Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7dc4d78f7af1086f9a4de9aa7392acb388df966e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590670"
---
# <a name="round-azure-cosmos-db"></a>KEREK (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy numerikus értéket ad vissza, amely a legközelebbi egész értékre van kerekítve.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="remarks"></a>Megjegyzések
  
Az elvégezhető kerekítési művelet az a középpontba kerül, amely nulláról van lekerekítve. Ha a bemenet egy numerikus kifejezés, amely pontosan két egész szám közé esik, akkor az eredmény a legközelebb eső egész érték lesz a nulláról. Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).
  
|<numeric_expr>|Lekerekített|
|-|-|
|– 6,5000|-7|
|– 0,5|-1|
|0,5|1|
|6,5000|7|
  
## <a name="examples"></a>Példák
  
A következő példa a következő pozitív és negatív számokat a legközelebbi egész számra kerekíti.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
Itt látható az eredményhalmaz.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
