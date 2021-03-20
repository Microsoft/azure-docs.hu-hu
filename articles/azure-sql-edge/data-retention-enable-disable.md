---
title: Adatmegőrzési házirendek engedélyezése és letiltása – Azure SQL Edge
description: Megtudhatja, hogyan engedélyezheti és tilthatja le az adatmegőrzési szabályzatokat az Azure SQL Edge-ben
keywords: SQL Edge, adatmegőrzés
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 5b8dd911952a63ba8775f27a6128ff61e849e823
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861425"
---
# <a name="enable-and-disable-data-retention-policies"></a>Adatmegőrzési szabályzatok engedélyezése és letiltása

Ez a témakör azt ismerteti, hogyan lehet engedélyezni és letiltani az adatmegőrzési házirendeket egy adatbázis és egy tábla számára. 

## <a name="enable-data-retention-for-a-database"></a>Adatmegőrzés engedélyezése egy adatbázis számára

Az alábbi példa bemutatja, hogyan engedélyezheti az adatmegőrzést az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql-set-options)használatával.

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>Ellenőrizze, hogy engedélyezve van-e az adatmegőrzés az adatbázishoz

A következő parancs használatával ellenőrizhető, hogy engedélyezve van-e az adatmegőrzés az adatbázishoz
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>Adatmegőrzés engedélyezése egy táblánál

Minden olyan táblánál engedélyezve kell lennie az adatmegőrzésnek, amelyhez az adatok automatikus kiürítését szeretné. Ha az adatmegőrzés engedélyezve van az adatbázison és a táblán, a háttérben futó rendszerfeladat rendszeresen megvizsgálja a táblázatot az elavult (idős) sorok azonosítására és törlésére. Az adatmegőrzés engedélyezhető egy táblán a [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) vagy az [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql)paranccsal a tábla létrehozása során.

Az alábbi példa bemutatja, hogyan engedélyezhető az adatmegőrzés egy táblához a [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql)használatával. 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

A `WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )` CREATE TABLE parancs része a tábla adatmegőrzését állítja be. A parancs a következő kötelező paramétereket használja 

- DATA_DELETION – azt jelzi, hogy be van-e kapcsolva az adatok megőrzése.
- FILTER_COLUMN – a tábla azon oszlopának neve, amely annak megállapítására szolgál, hogy a sorok elavultak-e vagy sem. A szűrő oszlop csak a következő adattípusú oszlop lehet. 
    - Date
    - Idő adattípusúra
    - DateTime
    - DateTime2
    - DateTimeOffset
- RETENTION_PERIOD – egész érték, amelyet egy egység leírója követ. Az engedélyezett egységek: nap, nap, hét, hét, hónap, hónap, év és év.

Az alábbi példa bemutatja, hogyan engedélyezheti az adatmegőrzést a tábla számára az [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql)paranccsal.  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>Ellenőrizze, hogy engedélyezve van-e az adatmegőrzés a táblához

A következő parancs segítségével ellenőrizhetők azok a táblák, amelyeken engedélyezve van az adatok megőrzése

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

Data_retention_period =-1 érték értéke, és data_retention_period_unit VÉGTELENként, azt jelzi, hogy az adatmegőrzés nincs beállítva a táblán.

A következő lekérdezéssel azonosítható az adatmegőrzési filter_columnként használt oszlop. 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="correlating-db-and-table-data-retention-settings"></a>Az adatbázis és a tábla adatmegőrzési beállításainak korrelációja

Az adatbázis és a tábla adatmegőrzési beállítása a (z) együtt használható annak megállapításához, hogy az elavult sorokhoz tartozó autorazzia a táblákon fut-e. 

|Adatbázis-beállítás | Tábla lehetőség | Működés |
|----------------|--------------|----------|
| KI | KI | Az adatmegőrzési házirend le van tiltva, és az elavult rekordok automatikus és manuális tisztítása le van tiltva.|
| KI | ON  | Az adatmegőrzési szabályzat engedélyezve van a táblához. Az elavult rekordok automatikus tisztítása le van tiltva, azonban az elavult rekordok törlésére a manuális törlési módszer használható. |
| ON | KI | Az adatmegőrzési szabályzat az adatbázis szintjén van engedélyezve. Mivel azonban a beállítás le van tiltva a tábla szintjén, a rendszer nem őrzi meg az elavult sorok megőrzésen alapuló törlését.|
| ON | ON | Az adatmegőrzési házirend az adatbázishoz és a táblákhoz is engedélyezve van. Az elavult rekordok automatikus törlése engedélyezve van. |

## <a name="disable-data-retention-on-a-table"></a>Adatok megőrzésének letiltása egy táblában 

Az adatmegőrzés le lehet tiltani egy táblában az [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql)paranccsal. A következő parancs használatával letilthatja az adatmegőrzést egy táblán.

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>Adatok megőrzésének letiltása egy adatbázison

Az adatmegőrzés letiltható egy táblán az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql-set-options)utasítás használatával. Az alábbi parancs használatával letilthatja az adatmegőrzést egy adatbázison.

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>Következő lépések
- [Adatmegőrzés és az automatikus Adattisztítás](data-retention-overview.md)
- [Korábbi adatok kezelése adatmegőrzési házirenddel](data-retention-cleanup.md)
