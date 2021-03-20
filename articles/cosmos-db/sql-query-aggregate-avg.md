---
title: Azure Cosmos DB lekérdezés nyelvének ÁTLAGa
description: Ismerkedjen meg az átlagos (AVG) SQL System függvénnyel Azure Cosmos DBban.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2cfbca798a7b404e4ee12b93396b2a5b08d7d5bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553388"
---
# <a name="avg-azure-cosmos-db"></a>ÁTLAG (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez az összesítő függvény a kifejezésben szereplő értékek átlagát adja vissza.
  
## <a name="syntax"></a>Szintaxis
  
```sql
AVG(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
A következő példa az átlagos értéket adja vissza `propertyA` :
  
```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy). Ha bármely argumentum `AVG` karakterlánc, logikai vagy NULL értékű, a teljes aggregált rendszerfunkció vissza fog térni `undefined` . Ha bármelyik argumentum `undefined` értéke értékkel rendelkezik, akkor az nem befolyásolja a `AVG` számítást.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények a Azure Cosmos DBban](sql-query-mathematical-functions.md)
- [A Azure Cosmos DB rendszerfunkciói](sql-query-system-functions.md)
- [Összesítő függvények a Azure Cosmos DBban](sql-query-aggregate-functions.md)