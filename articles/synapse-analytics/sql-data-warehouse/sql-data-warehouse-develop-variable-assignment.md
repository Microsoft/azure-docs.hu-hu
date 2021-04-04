---
title: Változók kiosztása
description: Ebben a cikkben alapvető tippeket talál a T-SQL-változók a dedikált SQL-készletekhez való hozzárendeléséhez az Azure szinapszis Analyticsben.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 87448ea737c11af13a52632e5bf4f67dc54d9ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459219"
---
# <a name="assign-variables-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Változók kiosztása dedikált SQL-készletekhez az Azure szinapszis Analyticsben

Ebben a cikkben alapvető tippeket talál a T-SQL-változók dedikált SQL-készletben való hozzárendeléséhez.

## <a name="set-variables-with-declare"></a>Változók beállítása a DECLARE értékkel

A dedikált SQL-készletben lévő változók az `DECLARE` utasítással vagy az utasítással állíthatók be `SET` . A DEKLARÁLt változók inicializálása az egyik legrugalmasabb módszer az SQL-készletben lévő változó értékének megadására.

```sql
DECLARE @v  int = 0
;
```

A DECLARE paranccsal egyszerre több változót is beállíthat. A SELECT vagy a UPDATE művelettel nem végezheti el a következőket:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nem inicializálható és nem használható változó ugyanabban a DEKLARÁLt utasításban. A pont szemléltetése érdekében a következő példa **nem** engedélyezett, mivel @p1 mindkettő inicializálva van, és ugyanabban a deklarált utasításban van használatban. A következő példa hibát jelez:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Értékek beállítása a SET értékkel

A SET egy általános módszer egyetlen változó beállítására.

A következő utasítások minden érvényes módszert határoznak meg a KÉSZLETtel rendelkező változók beállításához:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Egyszerre csak egy változót lehet beállítani. Az összetett operátorok azonban megengedettek.

## <a name="limitations"></a>Korlátozások

A változó hozzárendeléshez nem használható frissítés.

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).
