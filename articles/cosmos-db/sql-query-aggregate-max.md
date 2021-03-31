---
title: MAX Azure Cosmos DB lekérdezési nyelven
description: További információ a Azure Cosmos DBban található Max (MAX) SQL System függvényről.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a3d8e3f2687a492461bdbbdd761e26cdb58f9e96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96552381"
---
# <a name="max-azure-cosmos-db"></a>MAX (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez az összesítő függvény a kifejezésben szereplő értékek maximális értékét adja vissza.
  
## <a name="syntax"></a>Szintaxis
  
```sql
MAX(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok

*scalar_expr*  
   Skaláris kifejezés. 
  
## <a name="return-types"></a>Visszatérési típusok
  
Skaláris kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
A következő példa a maximális értékét adja vissza `propertyA` :
  
```sql
SELECT MAX(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy). Az argumentumok `MAX` szám, karakterlánc, logikai vagy Null lehet. A nem definiált értékek figyelmen kívül lesznek hagyva.

A különböző típusú adattípusok összehasonlításakor a rendszer a következő prioritási sorrendet használja (csökkenő sorrendben):

- sztring
- szám
- boolean
- null

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények a Azure Cosmos DBban](sql-query-mathematical-functions.md)
- [A Azure Cosmos DB rendszerfunkciói](sql-query-system-functions.md)
- [Összesítő függvények a Azure Cosmos DBban](sql-query-aggregate-functions.md)