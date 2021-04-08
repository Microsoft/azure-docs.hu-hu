---
title: Bejelentkezés Azure Cosmos DB lekérdezés nyelve
description: További információ a Azure Cosmos DB LOG SQL System függvényéről a megadott numerikus kifejezés természetes alapú logaritmusának visszaadásához
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 44a9d5b273e13886b0674b3b2e9f5f7a75e72fcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338577"
---
# <a name="log-azure-cosmos-db"></a>NAPLÓ (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 A megadott numerikus kifejezés természetes alapú logaritmusát adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
*base*  
   Nem kötelező numerikus argumentum, amely a logaritmus alapját állítja be.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="remarks"></a>Megjegyzések
  
  Alapértelmezés szerint a LOG () függvény a természetes alapú logaritmust adja vissza. A logaritmus alapjait másik értékre módosíthatja a választható alap paraméter használatával.  
  
  A természetes logaritmus az **e**-Base logaritmusa, ahol az **e** egy, körülbelül 2,718281828-as irracionális konstans.  
  
  Egy szám exponenciális értékének természetes alapú logaritmusa maga a szám: LOG (EXP (n)) = n. Egy szám természetes alapú logaritmusának exponenciális értéke maga a szám: EXP (LOG (n)) = n.

  Ez a rendszerfüggvény nem fogja használni az indexet.
  
## <a name="examples"></a>Példák
  
  A következő példa deklarál egy változót, és visszaadja a megadott változó (10) logaritmus értékét.  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 A következő példa `LOG` egy szám kitevőjét számítja ki.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
