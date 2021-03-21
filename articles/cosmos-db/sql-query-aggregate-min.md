---
title: MINIMÁLIS érték Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg a minimális (MIN) SQL System függvénnyel Azure Cosmos DBban.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 679814822cca5a72bd261d3c8944863715e31f70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553382"
---
# <a name="min-azure-cosmos-db"></a>MIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez az összesítő függvény a kifejezésben szereplő értékek minimumát adja vissza.
  
## <a name="syntax"></a>Szintaxis
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*scalar_expr*  
   Skaláris kifejezés. 
  
## <a name="return-types"></a>Visszatérési típusok
  
Skaláris kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
A következő példa a minimális értéket adja vissza `propertyA` :
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy). Az argumentumok `MIN` szám, karakterlánc, logikai vagy Null lehet. A nem definiált értékek figyelmen kívül lesznek hagyva.

A különböző típusú adattípusok összehasonlításakor a rendszer a következő prioritási sorrendet használja (növekvő sorrendben):

- null
- boolean
- szám
- sztring

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények a Azure Cosmos DBban](sql-query-mathematical-functions.md)
- [A Azure Cosmos DB rendszerfunkciói](sql-query-system-functions.md)
- [Összesítő függvények a Azure Cosmos DBban](sql-query-aggregate-functions.md)