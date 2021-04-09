---
title: GetCurrentTimestamp Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function GetCurrentTimestamp Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: fa7d1ec2af12065fb7d761073cd982a561cf53c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99524261"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Azon ezredmásodpercek számát adja vissza, amelyek elteltek 00:00:00 csütörtök, 1970. január 1. között.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Visszatérési típusok
  
Egy aláírt numerikus értéket ad vissza, amely a UNIX-kor óta eltelt ezredmásodpercek száma, azaz a 00:00:00 csütörtök és 1970 január 1. között eltelt ezredmásodpercek száma.

## <a name="remarks"></a>Megjegyzések

A GetCurrentTimestamp () egy determinált-függvény. A visszaadott eredmény UTC (egyezményes világidő).

> [!NOTE]
> Ez a rendszerfüggvény nem fogja használni az indexet. Ha az aktuális időponthoz össze kell hasonlítani az értékeket, a lekérdezés végrehajtása előtt szerezze be a jelenlegi időt, és használja ezt az állandó sztring értéket a `WHERE` záradékban.

## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan kérheti le az aktuális időbélyeget a GetCurrentTimestamp () beépített függvény használatával.
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Íme egy példa az eredményhalmaz értékére.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Következő lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
