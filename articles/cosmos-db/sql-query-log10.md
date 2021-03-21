---
title: LOG10 Azure Cosmos DB lekérdezési nyelven
description: További információ a Azure Cosmos DB LOG10 SQL System függvényéről a megadott numerikus kifejezés 10-es alapú logaritmusának visszaadásához
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27f7d916c1a3f84f26674fea6b04597fd9e546bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338431"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 A megadott numerikus kifejezés 10-es alapú logaritmusát adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expression*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="remarks"></a>Megjegyzések
  
  A LOG10 és a POWER függvények fordítottan kapcsolódnak egymáshoz. Például: 10 ^ LOG10 (n) = n. Ez a rendszerfüggvény nem fogja használni az indexet.
  
## <a name="examples"></a>Példák
  
  A következő példa deklarál egy változót, és visszaadja a megadott változó LOG10 értékét (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
