---
title: Azure Cosmos DB lekérdezési nyelv ST_ISVALIDDETAILED
description: Ismerkedjen meg a Azure Cosmos DB SQL System Function ST_ISVALIDDETAILEDával.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6ccd3178f1126ce8fe8f10b126dc6eadaf72bf53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004415"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy logikai értéket tartalmazó JSON-értéket ad vissza, ha a megadott GeoJSON pont, sokszög vagy LineString kifejezés érvényes, és ha az érték érvénytelen, akkor a karakterlánc értékét is adja meg.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   Egy GeoJSON pont vagy sokszög kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai értéket tartalmazó JSON-értéket ad vissza, ha a megadott GeoJSON pont-vagy sokszög-kifejezés érvényes, és ha az érték érvénytelen, akkor a karakterlánc értékét is adja meg.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan ellenőrizhető az érvényesség (részletekkel) a használatával `ST_ISVALIDDETAILED` .  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Következő lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
