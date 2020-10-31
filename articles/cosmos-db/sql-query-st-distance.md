---
title: Azure Cosmos DB lekérdezési nyelv ST_DISTANCE
description: Ismerkedjen meg a Azure Cosmos DB SQL System Function ST_DISTANCEával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0796ec0b69f7a6a1c3ab6beb2b74f04c8fe94af7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082334"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 A két GeoJSON pont, a sokszög, a többsokszögű vagy a LineString kifejezés közötti távolságot adja vissza. További információért lásd a [térinformatikai és a GeoJSON vonatkozó információkat](sql-query-geospatial-intro.md) ismertető cikket.
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   Bármely érvényes GeoJSON pont, sokszög vagy LineString objektum kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A távolságot tartalmazó numerikus kifejezést adja vissza. Ez az alapértelmezett hivatkozási rendszer mérőszámában van kifejezve.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan lehet visszaadni az összes olyan családi dokumentumot, amely a megadott hely 30 km-n belül található a `ST_DISTANCE` beépített függvénnyel. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [térinformatikai index](index-policy.md#spatial-indexes)előnyeit.

## <a name="next-steps"></a>Következő lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
