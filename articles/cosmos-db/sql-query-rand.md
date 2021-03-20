---
title: RAND Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg az SQL System Function RAND Azure Cosmos DB-ben való megismerését.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fb3e310970fcc2146ee0d4b790a9744dcd566bad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341654"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy véletlenszerűen generált numerikus értéket ad vissza [0, 1).
 
## <a name="syntax"></a>Syntax
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Visszatérési típusok

  Egy numerikus kifejezést ad vissza.

## <a name="remarks"></a>Megjegyzések

  `RAND` egy determinált függvény. Az ismétlődő hívások `RAND` nem adják vissza ugyanazt az eredményt. Ez a rendszerfüggvény nem fogja használni az indexet.


## <a name="examples"></a>Példák
  
  A következő példa egy véletlenszerűen generált numerikus értéket ad vissza.
  
```sql
SELECT RAND() AS rand 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
