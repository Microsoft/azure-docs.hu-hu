---
title: StringToNumber Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function StringToNumber Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f2622958a2315458ccd01da4aea7c6b75941e755
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339563"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy számra fordított kifejezést ad vissza. Ha a kifejezés nem fordítható le, a nem definiált értéket adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés, amelyet JSON-szám kifejezésként kell elemezni. A JSON-számnak egész számnak vagy lebegőpontos pontnak kell lennie. A JSON formátumával kapcsolatos részletekért lásd: [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy szám kifejezést vagy nem definiált értéket ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan viselkedik a `StringToNumber` különböző típusok között. 

A szóköz csak a szám előtt vagy után engedélyezett.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Itt látható az eredményhalmaz.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

A JSON-ban érvényes számnak kell lennie vagy egész számnak vagy lebegőpontos számnak kell lennie.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Itt látható az eredményhalmaz.  
  
```json
{{}}
```  

Az átadott kifejezést szám típusú kifejezésként értelmezi a rendszer. Ezek a bemenetek nem értékelik ki a számot, és így nem definiált értéket adnak vissza. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Itt látható az eredményhalmaz.  
  
```json
{{}}
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
