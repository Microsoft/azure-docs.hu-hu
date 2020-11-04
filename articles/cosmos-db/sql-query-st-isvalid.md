---
title: Azure Cosmos DB lekérdezési nyelv ST_ISVALID
description: Ismerkedjen meg a Azure Cosmos DB SQL System Function ST_ISVALIDával.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9b20e57672e86c2b5a6a2a25151d779ea7bc92f3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335160"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott GeoJSON pont, sokszög vagy LineString kifejezés érvényes-e.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   Egy GeoJSON pont, sokszög vagy LineString kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan ellenőrizhető, hogy egy pont érvényes-e ST_VALID használatával.  
  
  Ez a pont például olyan szélességi értékkel rendelkezik, amely nem szerepel a [-90, 90] értékek érvényes tartományában, így a lekérdezés hamis értéket ad vissza.  
  
  A sokszögek esetében a GeoJSON-specifikáció megköveteli, hogy a megadott koordináta-pároknak meg kell egyezniük az elsővel, és létre kell hozni egy bezárt alakzatot. A sokszögben lévő pontokat a megfelelő sorrendben kell megadni. A megegyező sorrendben megadott sokszög a régió inverzét jelöli.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>További lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
