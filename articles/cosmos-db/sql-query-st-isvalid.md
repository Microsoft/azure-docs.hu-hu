---
title: Azure Cosmos DB lekérdezési nyelv ST_ISVALID
description: Ismerkedjen meg a Azure Cosmos DB SQL System Function ST_ISVALIDával.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e7c124da91a947a0ac8426ce8c92347396236c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004432"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott GeoJSON pont, sokszög, többsokszögű vagy LineString kifejezés érvényes-e.  
  
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

## <a name="next-steps"></a>Következő lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
