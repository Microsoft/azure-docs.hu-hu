---
title: Tárolt eljárások használata
description: Tippek a megoldások fejlesztéséhez a dedikált SQL-készletek tárolt eljárásainak megvalósításával az Azure szinapszis Analyticsben.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e28eeac131c737d673cac947a3fda30239180a62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673586"
---
# <a name="using-stored-procedures-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Tárolt eljárások használata dedikált SQL-készletekhez az Azure szinapszis Analyticsben

Ez a cikk tippekkel szolgál a dedikált SQL Pool-megoldások fejlesztéséhez tárolt eljárások megvalósításával.

## <a name="what-to-expect"></a>Amire számíthat

A dedikált SQL-készlet számos olyan T-SQL-funkciót támogat, amelyek a SQL Serverben használatosak. Még ennél is fontosabb, hogy a megoldás teljesítményének maximalizálása érdekében Felskálázási funkciók érhetők el.

Emellett a dedikált SQL-készlet méretezésének és teljesítményének fenntartása érdekében további funkciókat és funkciókat is tartalmaz, amelyek viselkedési különbségekkel rendelkeznek.

## <a name="introducing-stored-procedures"></a>Tárolt eljárások bevezetése

A tárolt eljárások nagyszerű lehetőséget biztosítanak az SQL-kód beágyazására, amely a dedikált SQL-készlet adataihoz közelebb van tárolva. A tárolt eljárások azt is segítik a fejlesztőket, hogy modularize a kódot a felügyelhető egységekbe ágyazva, így könnyebben használhatók a kód újrahasználhatósága. Az egyes tárolt eljárások is elfogadják a paramétereket, hogy azok még rugalmasabbak legyenek.

A dedikált SQL-készlet egyszerűsített és gördülékenyen tárolt eljárások megvalósítását teszi lehetővé. SQL Server képest a legnagyobb különbség az, hogy a tárolt eljárás nincs előre lefordított kód.

Az adatraktárak esetében általánosságban a fordítási idő a nagyméretű adatköteteken való lekérdezések futtatásához képest kicsi. Fontos, hogy a tárolt eljárás kódja megfelelően legyen optimalizálva a nagyméretű lekérdezéseknél.

> [!TIP]
> A cél az óra, perc és másodperc megtakarítása, nem ezredmásodperc. Így hasznos lehet a tárolt eljárások tárolóként való meggondolása az SQL Logic számára.

Ha egy dedikált SQL-készlet végrehajtja a tárolt eljárást, az SQL-utasítások elemzése, lefordítása és futási ideje optimalizált. A folyamat során minden utasítás elosztott lekérdezésekre lesz konvertálva. Az adatbázison végrehajtott SQL-kód eltér az elküldött lekérdezéstől.

## <a name="nesting-stored-procedures"></a>Tárolt eljárások beágyazása

Ha a tárolt eljárás más tárolt eljárásokat hív meg, vagy dinamikus SQL-t hajt végre, akkor a belső tárolt eljárás vagy a kód meghívása beágyazottnak mondható.

A dedikált SQL-készlet legfeljebb nyolc beágyazási szintet támogat. Ezzel szemben a SQL Server beágyazási szintje 32.

A legfelső szintű tárolt eljárás az 1. szintű beágyazásnak felel meg.

```sql
EXEC prc_nesting
```

Ha a tárolt eljárás egy másik EXEC hívást is végrehajt, a beágyazási szint két értékre nő.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Ha a második eljárás elvégez néhány dinamikus SQL-műveletet, a beágyazási szint háromra nő.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

A dedikált SQL-készlet jelenleg nem támogatja a [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)-t. Ezért nyomon kell követnie a beágyazási szintet. Nem valószínű, hogy túllépi a nyolc fészekre vonatkozó korlátot. Ha azonban ezt teszi, újra kell dolgoznia a kódját, hogy az illeszkedjen a korláton belüli beágyazási szintekhez.

## <a name="insertexecute"></a>INSERT..EXEARANYOS

A dedikált SQL-készlet nem teszi lehetővé, hogy egy tárolt eljárás eredmény-készletét egy INSERT utasítással használja. Van azonban egy alternatív megközelítés, amelyet használhat. Példaként tekintse meg az [ideiglenes táblákról](sql-data-warehouse-tables-temporary.md)szóló cikket.

## <a name="limitations"></a>Korlátozások

A Transact-SQL tárolt eljárásainak néhány aspektusa nem valósul meg a dedikált SQL-készletben, amelyek a következők:

* ideiglenes tárolt eljárások
* számozott tárolt eljárások
* kiterjesztett tárolt eljárások
* CLR tárolt eljárásai
* titkosítási beállítás
* replikálási beállítás
* tábla értékű paraméterek
* csak olvasható paraméterek
* alapértelmezett paraméterek
* végrehajtási környezetek
* visszatérési utasítás

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).
