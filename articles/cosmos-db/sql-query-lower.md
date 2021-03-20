---
title: Azure Cosmos DB lekérdezési nyelv alacsonyabb
description: Ismerkedjen meg az Azure Cosmos DB alsó SQL System függvényével, amely egy karakterlánc-kifejezést ad vissza a nagybetűs karakterek kisbetűsre konvertálása után.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: dd1d9ddefd67cadb92632fd6db7a1fbd5a34f35a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338433"
---
# <a name="lower-azure-cosmos-db"></a>ALSÓ (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy karakterlánc-kifejezést ad vissza, miután a nagybetűs karaktereket a kisbetűs értékre konvertálta.  

Az alsó rendszerfüggvény nem használja az indexet. Ha gyakori kis-és nagybetűket nem megkülönböztető összehasonlításokat tervez, az alacsonyabb rendszerfunkció nagy mennyiségű RU-t is felhasználhat. Ha ez az eset áll fenn, ahelyett, hogy az alacsonyabb rendszerfüggvényt használja az összehasonlítások minden egyes időpontjában, a borítást a beszúráskor normalizálhatja. Ezután egy olyan lekérdezés, mint például a SELECT * FROM c, ahol az alacsonyabb (c. Name) = ' Bob ' egyszerűen kiválaszthatja a * c betűt, ahol a c.name = ' Bob '.

## <a name="syntax"></a>Szintaxis
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan használható `LOWER` egy lekérdezésben.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
