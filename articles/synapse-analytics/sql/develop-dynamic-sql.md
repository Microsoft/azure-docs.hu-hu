---
title: Dinamikus SQL használata a szinapszis SQL-ben
description: Tippek a dinamikus SQL használatához a szinapszis SQL-ben.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 4a4ce92278d5f6704fec4a5b9d30b44ddf89b0ad
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033438"
---
# <a name="dynamic-sql-in-synapse-sql"></a>Dinamikus SQL a szinapszis SQL-ben
Ebből a cikkből megtudhatja, hogyan használhatók a dinamikus SQL és a megoldások fejlesztése a szinapszis SQL használatával.

## <a name="dynamic-sql-example"></a>Dinamikus SQL-példa

Az alkalmazás kódjának fejlesztésekor előfordulhat, hogy dinamikus SQL-t kell használnia a rugalmas, általános és moduláris megoldások biztosításához.

> [!NOTE]
> Az SQL-készlet jelenleg nem támogatja a blob-adattípusokat. A blob-adattípusok nem támogatják a karakterláncok méretét, mivel a blob-adattípusok a varchar (max) és a nvarchar (max) típusokat is tartalmazzák. Ha már használta ezeket a típusokat az alkalmazás kódjában nagyméretű karakterláncok létrehozásához, akkor a kódot a darabokra kell bontania, és ehelyett az EXEC utasítást kell használnia.

Egy egyszerű példa:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Ha a karakterlánc rövid, akkor a [Sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a megszokott módon használhatja.

> [!NOTE]
> A dinamikus SQLként végrehajtott utasítások továbbra is az összes T-SQL érvényesítési szabály hatálya alá esnek.

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [fejlesztés áttekintése](develop-overview.md).
