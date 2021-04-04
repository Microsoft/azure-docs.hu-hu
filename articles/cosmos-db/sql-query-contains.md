---
title: Azure Cosmos DB lekérdezési nyelvet tartalmaz
description: Ismerje meg, hogy az Azure Cosmos DB SQL System függvénye hogyan ad vissza egy logikai értéket, amely azt jelzi, hogy az első karakterlánc-kifejezés tartalmazza-e a másodpercet
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4b6835b22e5cfa4ca703b95d70e20112b8723def
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339172"
---
# <a name="contains-azure-cosmos-db"></a>TARTALMAZZA (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Egy logikai értéket ad vissza, amely azt jelzi, hogy az első karakterlánc-kifejezés tartalmazza-e a másodpercet.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   A keresendő karakterlánc-kifejezés.  
  
*str_expr2*  
   A keresendő karakterlánc-kifejezés.  

*bool_expr* Nem kötelező érték az eset figyelmen kívül hagyásával. Ha True értékre van állítva, akkor a tartalmaz egy kis-és nagybetűket megkülönböztető keresést. Ha nincs megadva, ez az érték hamis.
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa ellenőrzi, hogy az "ABC" tartalmazza-e az "AB" kifejezést, és ha az "ABC" tartalmazza az "A" kifejezést.  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

A-ben a tartalmazott RU-használat növekedni fog, mivel a System függvényben megnövekszik a tulajdonsága. Más szóval, ha azt ellenőrzi, hogy egy tulajdonságérték tartalmaz-e egy bizonyos karakterláncot, az RU-lekérdezés díja az adott tulajdonság lehetséges értékeinek számától függ.

Vegyük például a következő két tulajdonságot: város és ország. A város Bíborosa 5 000, az ország pedig 200. Íme két példa a lekérdezésekre:

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

Az első lekérdezés valószínűleg több RUs-t fog használni a második lekérdezésnél, mert a város számos országa magasabb.

Ha a tartalmazott tulajdonság mérete nagyobb, mint 1 KB egyes dokumentumokhoz, a lekérdezési motornak be kell töltenie ezeket a dokumentumokat. Ebben az esetben a lekérdezési motor nem tudja teljes mértékben kiértékelni a tartalmaz indexet. Ha nagy számú, 1 KB-nál nagyobb méretű dokumentumot tartalmaz, akkor a rendszer nem számít fel díjat.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
