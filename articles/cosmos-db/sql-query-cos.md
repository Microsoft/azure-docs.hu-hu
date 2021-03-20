---
title: COS Azure Cosmos DB lekérdezési nyelv
description: Ismerje meg, hogy a Azure Cosmos DB koszinusz (COS) SQL System függvénye hogyan adja vissza a megadott szög (radián) trigonometriai koszinuszát a megadott kifejezésben.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5f11307d4f24bab06973e33b4640e26b1c445336
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339027"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Az adott kifejezésben megadott szög (radián) trigonometriai koszinuszát adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a `COS` megadott szöget számítja ki.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
