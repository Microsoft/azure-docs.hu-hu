---
title: Rendelje hozzá a változókat az Azure SQL Data Warehouse |} A Microsoft Docs
description: Tippek az Azure SQL Data Warehouse a T-SQL-változók hozzárendeléséhez használható megoldások fejlesztéséhez.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 29b86d430fb5d65e7f089c616651da5f488b675c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455983"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse változók hozzárendelése
Tippek az Azure SQL Data Warehouse a T-SQL-változók hozzárendeléséhez használható megoldások fejlesztéséhez.

## <a name="setting-variables-with-declare"></a>DECLARE a változók beállítása
Az SQL Data Warehouse változók vannak beállítva, használja a `DECLARE` utasítást vagy a `SET` utasítást. A DECLARE változók inicializálása az egyik változó értékének beállítása az SQL Data Warehouse a legrugalmasabb módszer.

```sql
DECLARE @v  int = 0
;
```

DECLARE használatával egyszerre több változó beállítása. Válassza ki vagy a frissítés nem használható a következőket:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nem lehet inicializálni a, és nem használhat egy változót az azonos DECLARE utasítást. A pont mutatja be, a következő példában van **nem** hajtható végre, mert @p1 van inicializálva és az azonos DECLARE utasítást használja. A következő példa olyan hibaüzenetet ad.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>A SET értékeinek beállítása
SET a módszert gyakran egy változó beállításához.

Az alábbi utasításokat az összes érvényes módon beállítva egy olyan változó megadásához:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Csak akkor állíthat egy változó egyszerre beállítva. Azonban az összetett kezelők megengedett is.

## <a name="limitations"></a>Korlátozások
Válassza ki vagy a frissítés nem használható változó-hozzárendelés.

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

