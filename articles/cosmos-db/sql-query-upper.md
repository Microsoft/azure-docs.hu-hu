---
title: FELSŐ Azure Cosmos DB lekérdezési nyelv
description: Ismerkedjen meg az SQL System Function felső Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 06f85d97266e78b343ad8da233b77e369da5ee65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334905"
---
# <a name="upper-azure-cosmos-db"></a>FELSŐ (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy karakterlánc-kifejezést ad vissza a kisbetűs karakterek nagybetűssé alakítását követően.  

A felső rendszerfüggvény nem használja az indexet. Ha gyakori kis-és nagybetűket nem megkülönböztető összehasonlításokat tervez, a felső rendszerfunkció nagy mennyiségű RU-t használhat fel. Ha ez az eset áll fenn, ahelyett, hogy a felső rendszerfüggvényt használja az összehasonlítások minden egyes időpontjában az adatnormalizálás során, a borítást a Beszúrás után normalizálhatja. Ezután egy olyan lekérdezés, mint például a SELECT * FROM c, ahol az UPPER (c. Name) = ' BOB ' egyszerűen KIVÁLASZTJA * a c-ből, ahol a c.name = ' BOB ' lesz.

## <a name="syntax"></a>Szintaxis
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan használható a `UPPER` lekérdezésekben  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
