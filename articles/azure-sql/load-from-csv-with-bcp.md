---
title: Adatok betöltése CSV-fájlból egy adatbázisba (BCP)
description: Kisebb adatméret esetén a bcp segítségével importálhatja az adatokat az Azure SQL Database-be.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 216410d423a70037588a6fb596771a702a13c003
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472235"
---
# <a name="load-data-from-csv-into-azure-sql-database-or-sql-managed-instance-flat-files"></a>Adatok betöltése CSV-fájlból Azure SQL Database vagy SQL felügyelt példányba (egyszerű fájlok)
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

A BCP parancssori segédprogrammal adatok importálhatók CSV-fájlokból Azure SQL Database vagy Azure SQL felügyelt példányba.

## <a name="before-you-begin"></a>Előkészületek

### <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához a következőkre lesz szüksége:

* Egy adatbázis a Azure SQL Database
* Telepített bcp parancssori segédprogram
* Telepített sqlcmd parancssori segédprogram

A BCP és a Sqlcmd segédprogramot a [Microsoft Sqlcmd dokumentációjában] [] töltheti le https://docs.microsoft.com/sql/tools/sqlcmd-utility?view=sql-server-ver15 .

### <a name="data-in-ascii-or-utf-16-format"></a>Adatok ASCII vagy UTF-16 formátumban

Ha a saját adataival próbálja használni ezt az oktatóanyagot, az adatoknak ASCII vagy UTF-16 kódolást kell használniuk, mert a bcp nem támogatja az UTF-8 formátumot.

## <a name="1-create-a-destination-table"></a>1. cél tábla létrehozása

Adjon meg egy táblát az SQL Database-ben céltáblaként. A tábla oszlopainak meg kell felelnie az adatfájl egyes soraiban szereplő adatoknak.

Tábla létrehozásához nyisson meg egy parancssort, és az sqlcmd.exe segítségével futtassa a következő parancsot:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```

## <a name="2-create-a-source-data-file"></a>2. forrás adatfájl létrehozása

Nyissa meg a Jegyzettömböt, és másolja az alábbi adatsorokat egy új szöveges fájlba, majd mentse ezt a fájlt a helyi ideiglenes könyvtárba (C:\Temp\DimDate2.txt). Ezek az adatok ASCII formátumban vannak.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Opcionális) A saját adatok SQL Server-adatbázisból való exportálásához nyisson meg egy parancssort, és futtassa az alábbi parancsot. Cserélje le a TableName, ServerName, DatabaseName, Username és Password adatokat a saját adataira.

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ,
```

## <a name="3-load-the-data"></a>3. töltse be az adattárolót

Az adatok betöltéséhez nyisson meg egy parancssort, és futtassa az alábbi, a kiszolgáló nevét, az adatbázis nevét, a felhasználónevet és a jelszót a saját értékeire lecserélő parancsot.

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

A parancs segítségével ellenőrizze, hogy megfelelő volt-e az adatok betöltése

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Az eredménynek így kell kinéznie:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>Következő lépések

Az SQL Server-adatbázisok áttelepítésével kapcsolatban tekintse meg az [SQL Server-adatbázisok áttelepítésével](database/migrate-to-database-from-sql-server.md) foglalkozó cikket.

<!--MSDN references-->
[bcp]: /sql/tools/bcp-utility
[CREATE TABLE syntax]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
