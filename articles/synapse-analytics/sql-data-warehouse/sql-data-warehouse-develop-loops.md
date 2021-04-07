---
title: T-SQL-hurkok használata
description: Tippek a megoldások fejlesztéséhez T-SQL-hurkok használatával, valamint a dedikált SQL-készletek mutatóinak cseréje az Azure szinapszis Analyticsben.
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
ms.openlocfilehash: a6c4eb98d77ece6e6ae130fd57cc263ee7e5ca64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683222"
---
# <a name="using-t-sql-loops-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>A T-SQL-hurkok használata dedikált SQL-készletekhez az Azure szinapszis Analyticsben

Ebben a cikkben a T-SQL-hurkok használatával és a kurzorok cseréjével kapcsolatos tippek találhatók a dedikált SQL Pool-megoldások fejlesztéséhez.

## <a name="purpose-of-while-loops"></a>Hurkok célja

Az Azure szinapszis dedikált SQL-készletei támogatják [a ciklusok](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ismételt végrehajtásához szükséges hurkokat. Ez a ciklus addig folytatódik, amíg a megadott feltételek teljesülnek, vagy amíg a kód kifejezetten leállítja a hurkot a BREAK kulcsszó használatával.

A hurkok hasznosak az SQL-kódban definiált kurzorok cseréjéhez. Szerencsére az SQL Code-ban írt összes kurzor a gyors továbbítás, csak olvasható fajta. Szóval, míg a hurkok nagyszerű alternatíva a kurzorok cseréjéhez.

## <a name="replacing-cursors-in-dedicated-sql-pool"></a>Kurzorok cseréje dedikált SQL-készletben

Mielőtt azonban először is felmerülnek a merülés előtt, a következő kérdéssel kell megkérdezni: "lehetséges, hogy a kurzor újraírható a set-based Operations használatára?"

Sok esetben a válasz igen, és gyakran a legjobb megoldás. A készleten alapuló művelet gyakran gyorsabb, mint egy iterációs, soron belüli módszer.

A gyors továbbítást csak olvasható kurzorok egyszerűen lecserélhetik hurkos szerkezettel. A következő példa egy egyszerű. Ez a kódrészlet frissíti az adatbázis minden táblájának statisztikáit. Ha megismétli a hurokban lévő táblákat, az egyes parancsok végrehajtása sorban történik.

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

További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).
