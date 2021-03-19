---
title: EXP Azure Cosmos DB lekérdezési nyelven
description: Tudnivalók a kitevő (EXP) SQL System függvényről Azure Cosmos DB a megadott numerikus kifejezés exponenciális értékének visszaadásához
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: adf80d36edbe8f9a5535e8fa04501918a62ea7ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93335622"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 A megadott numerikus kifejezés exponenciális értékét adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="remarks"></a>Megjegyzések
  
  A konstans **e** (2,718281...), a természetes logaritmus alapja.  
  
  Egy szám kitevője a **szám hatványára emelt** konstans. Például: EXP (1.0) = e ^ 1.0 = 2.71828182845905 és EXP (10) = e ^ 10 = 22026.4657948067.  
  
  Egy szám természetes alapú logaritmusának exponenciális értéke maga a szám: EXP (LOG (n)) = n. Egy szám exponenciális értékének természetes alapú logaritmusa maga a szám: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Példák
  
  A következő példa deklarál egy változót, és visszaadja a megadott változó exponenciális értékét (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 A következő példa a 20. számú természetes logaritmus exponenciális értékét adja vissza, valamint a 20 exponenciális érték természetes alapú logaritmusát. Mivel ezek a függvények egymástól eltérő inverz függvények, a visszatérési érték a lebegőpontos matematika kerekítési értékeként mindkét esetben 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
