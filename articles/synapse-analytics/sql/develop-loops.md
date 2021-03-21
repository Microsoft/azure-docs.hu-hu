---
title: T-SQL-hurkok használata
description: Tippek a T-SQL-hurkok használatához, a kurzorok cseréjéhez és a kapcsolódó megoldások fejlesztéséhez a szinapszis SQL használatával az Azure szinapszis Analyticsben.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0d83e1305a851bf6bafb6c4c79f5caf73f8e44b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98120886"
---
# <a name="use-t-sql-loops-with-synapse-sql-in-azure-synapse-analytics"></a>T-SQL-hurkok használata a szinapszis SQL-sel az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan használhatja a T-SQL-hurkokat, a kurzorok cseréjét és a kapcsolódó megoldások fejlesztését a szinapszis SQL használatával.

## <a name="purpose-of-while-loops"></a>Hurkok célja

A szinapszis SQL a [while](/sql/t-sql/language-elements/while-transact-sql?preserve-view=true&view=sql-server-ver15) ciklust is támogatja az utasítások ismételt végrehajtásához. Ez a ciklus addig folytatódik, amíg a megadott feltételek teljesülnek, vagy amíg a kód kifejezetten leállítja a hurkot a BREAK kulcsszó használatával. 

A szinapszis SQL-hurkoi hasznosak az SQL-kódban definiált kurzorok cseréjéhez. Szerencsére az SQL Code-ban írt összes kurzor a gyors továbbítás, csak olvasható fajta. Szóval, míg a hurkok nagyszerű alternatíva a kurzorok cseréjéhez.

## <a name="replace-cursors-in-synapse-sql"></a>Kurzorok cseréje a szinapszis SQL-ben

A bekövetkezett merülés előtt a következő kérdést kell figyelembe venni: "lehetséges, hogy ez a kurzor a beállított műveletek használatára lett átírva?" Sok esetben a válasz igen, és gyakran a legjobb megoldás. A készleten alapuló művelet gyakran gyorsabb, mint egy iterációs, soron belüli módszer.

A gyors továbbítást csak olvasható kurzorok egyszerűen lecserélhető hurkos szerkezettel. A következő kód egy egyszerű példa. Ez a kódrészlet frissíti az adatbázis minden táblájának statisztikáit. Ha megismétli a hurokban lévő táblákat, az egyes parancsok végrehajtása sorban történik.

Először hozzon létre egy ideiglenes táblázatot, amely egy egyedi sorszámot tartalmaz, amely az egyes utasítások azonosítására szolgál:

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Másodszor, inicializálja a hurok végrehajtásához szükséges változókat:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Most a következő utasításokon keresztül hajtja végre az egyes utasításokat:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Végül dobja el az első lépésben létrehozott ideiglenes táblát

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [fejlesztés áttekintése](develop-overview.md).