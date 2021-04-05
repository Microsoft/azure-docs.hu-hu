---
title: Dinamikus SQL használata
description: Tippek a dinamikus SQL-készleteket használó fejlesztői megoldásokhoz az Azure szinapszis Analyticsben.
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
ms.openlocfilehash: 18dcdbf5a4840fda3c2689cc3c8c003470bf1c7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98679601"
---
# <a name="dynamic-sql-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Dinamikus SQL a dedikált SQL-készletek számára az Azure szinapszis Analyticsben

Ez a cikk a dinamikus SQL-t használó, dedikált SQL-készletekben található fejlesztési megoldásokhoz nyújt tippeket.

## <a name="dynamic-sql-example"></a>Dinamikus SQL-példa

A dedikált SQL-készletek alkalmazási kódjának fejlesztésekor előfordulhat, hogy a dinamikus SQL-t kell használnia a rugalmas, általános és moduláris megoldások biztosításához. A dedikált SQL-készletek jelenleg nem támogatják a blob-adattípusokat.

A blob-adattípusok nem támogatják a karakterláncok méretét, mivel a blob-adattípusok a varchar (max) és a nvarchar (max) típusokat is tartalmazzák.

Ha már használta ezeket a típusokat az alkalmazás kódjában nagyméretű karakterláncok létrehozásához, akkor a kódot adattömbökbe kell bontania, és ehelyett az EXEC utasítást kell használnia.

Egy egyszerű példa:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Ha a karakterlánc rövid, akkor a [Sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) a megszokott módon használhatja.

> [!NOTE]
> A dinamikus SQLként végrehajtott utasítások továbbra is az összes T-SQL érvényesítési szabály hatálya alá esnek.

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).
