---
title: ÖSSZEG Azure Cosmos DB lekérdezési nyelven
description: A Azure Cosmos DB Sum (SZUM) SQL System függvény megismerése.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: d5a86cd5af504072480e0cd749caa6c0532d0bc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96552390"
---
# <a name="sum-azure-cosmos-db"></a>SUM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez az összesítő függvény a kifejezésben szereplő értékek összegét adja vissza.
  
## <a name="syntax"></a>Szintaxis
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
Az alábbi példa a következő összegét adja vissza `propertyA` :
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy). Ha bármely argumentum `SUM` karakterlánc, logikai vagy NULL értékű, a teljes aggregált rendszerfunkció vissza fog térni `undefined` . Ha bármelyik argumentum `undefined` értéke értékkel rendelkezik, akkor nem befolyásolja a `SUM` számítást.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények a Azure Cosmos DBban](sql-query-mathematical-functions.md)
- [A Azure Cosmos DB rendszerfunkciói](sql-query-system-functions.md)
- [Összesítő függvények a Azure Cosmos DBban](sql-query-aggregate-functions.md)