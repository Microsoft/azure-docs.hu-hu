---
title: Azure Cosmos DB lekérdezési nyelv megszámlálása
description: Ismerkedjen meg a Count (DARABSZÁM) SQL System függvénnyel Azure Cosmos DBban.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 5228558f4bcb146ec08ee5fff45fb1bdf4d56f01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96552378"
---
# <a name="count-azure-cosmos-db"></a>DARABSZÁM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a rendszerfüggvény a kifejezésben szereplő értékek számát adja vissza.
  
## <a name="syntax"></a>Szintaxis
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*scalar_expr*  
   Bármely skaláris kifejezés
  
## <a name="return-types"></a>Visszatérési típusok
  
Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
A következő példa egy tárolóban lévő elemek teljes számát adja vissza:
  
```sql
SELECT COUNT(1)
FROM c
``` 
A COUNT bármilyen skaláris kifejezést felvehet bemenetként. Az alábbi lekérdezés egyenértékű eredményeket fog eredményezni:

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a lekérdezés szűrője bármely tulajdonságának [tartomány-indexét](index-policy.md#includeexclude-strategy) .

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények a Azure Cosmos DBban](sql-query-mathematical-functions.md)
- [A Azure Cosmos DB rendszerfunkciói](sql-query-system-functions.md)
- [Összesítő függvények a Azure Cosmos DBban](sql-query-aggregate-functions.md)