---
title: StringEquals Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg, hogy a Azure Cosmos DB StringEquals SQL System függvénye hogyan ad vissza egy logikai értéket, amely azt jelzi, hogy az első karakterlánc-kifejezés megfelel-e a második
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 47d899534de535b5cd6a7c3fb2df78cdadbe11f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338048"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy logikai értéket ad vissza, amely azt jelzi, hogy az első karakterlánc-kifejezés megfelel-e a másodpercnek.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   Az összehasonlítandó első karakterlánc-kifejezés.  
  
*str_expr2*  
   Az összehasonlítandó második karakterlánc-kifejezés.  

*bool_expr* Nem kötelező érték az eset figyelmen kívül hagyásával. Ha igaz értékre van állítva, a StringEquals kis-és nagybetűket nem megkülönböztető keresést hajt végre. Ha nincs megadva, ez az érték hamis.
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa ellenőrzi, hogy az "ABC" megegyezik-e az "ABC" értékkel, és ha az "ABC" megegyezik az "ABC" értékkel  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
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

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
