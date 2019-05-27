---
title: Az adattípusok – Azure SQL Data Warehouse meghatározása |} A Microsoft Docs
description: Javaslatok az Azure SQL Data Warehouse tábla adattípusaival meghatározása.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 06a273d3bfd5d416039a992e36bd4b0f72a85f78
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851548"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse tábla adattípusaival
Javaslatok az Azure SQL Data Warehouse tábla adattípusaival meghatározása. 

## <a name="what-are-the-data-types"></a>Mik azok az adattípusokat?

Az SQL Data Warehouse által támogatott adattípusok a leggyakrabban használt. A támogatott adattípusokat listáját lásd: [adattípusok](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) a CREATE TABLE utasításban. 

## <a name="minimize-row-length"></a>Minimalizálja a sor hossza
Az adattípusok méretének minimalizálása lerövidíti a sor hossza, ami jobb lekérdezési teljesítményt. Az adatokhoz, amely egyaránt használható legkisebb adattípust használja. 

- Ne hozzon létre karakteroszlopot nagy alapértelmezett hosszúságértékkel együtt. Például ha a leghosszabb értéke 25 karakterből, majd határozza meg az oszlop VARCHAR(25) típusként. 
- Ne használja a [NVARCHAR] [NVARCHAR], amikor csak szüksége van VARCHAR.
- Amikor lehetséges, használjon NVARCHAR(4000) vagy VARCHAR(8000) NVARCHAR(MAX), VARCHAR(MAX) vagy helyett.

A táblák betöltése a PolyBase külső táblák használ, ha a táblázatsor meghatározott hossza legfeljebb 1 MB. Ha a változó hosszúságú adatokat tartalmazó sor meghaladja az 1 MB, betöltheti a sor a BCP használatával, de nem a polybase-zel.

## <a name="identify-unsupported-data-types"></a>Nem támogatott adattípusok azonosítása
Ha az adatbázis egy másik SQL database-ből telepít át, nem támogatott az SQL Data Warehouse adattípusok léphetnek fel. Ez a lekérdezés segítségével felderíteni a meglévő SQL-séma nem támogatott adattípusokat.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Nem támogatott adattípusok megoldásai

Az alábbi lista bemutatja a adattípust, hogy az SQL Data Warehouse nem támogatja, és lehetővé teszi a nem támogatott adattípusokat helyett használható alternatív megoldások.

| Típusa nem támogatott | Áthidaló megoldás |
| --- | --- |
| [geometriai](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Földrajzi hely](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Oszlop felosztása több típusos oszlopot. |
| [Tábla](/sql/t-sql/data-types/table-transact-sql) |Az ideiglenes táblák típusra konvertál. |
| [Időbélyeg](/sql/t-sql/data-types/date-and-time-types) |Kódot, hogy az újragyártási [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) és a [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) függvény. Csak állandó támogatott az alapértelmezett, ezért current_timestamp nem definiálható mint default megkötés. Ha verzió Sorértékek át típusos időbélyegző-oszlopa van szüksége, használja a [bináris](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) vagy [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) nem null értékű vagy üres sor verzió értékeket. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [felhasználó által meghatározott típus](/sql/relational-databases/native-client/features/using-user-defined-types) |Konvertálja a natív adattípus, amikor csak lehetséges. |
| alapértelmezett értékek | Alapértelmezett értékek literálok lehetnek, és csak állandók támogatja. |


## <a name="next-steps"></a>További lépések
Táblák fejlesztéséről további információkért lásd: [táblák áttekintésével](sql-data-warehouse-tables-overview.md).
