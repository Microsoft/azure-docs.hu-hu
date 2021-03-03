---
title: Kapcsolódás a szinapszis SQL-hez a Sqlcmd használatával
description: Használja a Sqlcmd parancssori segédprogramot a kiszolgáló nélküli SQL-készlet és a dedikált SQL-készlet lekérdezéséhez.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 7d6675f9584f90b67d8520091dcd4b04dd89e462
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667580"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Kapcsolódás a szinapszis SQL-hez a Sqlcmd használatával

> [!div class="op_single_selector"]
> * [Azure Data Studio)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

A [Sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true) parancssori segédprogrammal kapcsolódhat és lekérdezheti a kiszolgáló nélküli SQL-készletet és a dedikált SQL-készletet a szinapszis sqlon belül.  

## <a name="1-connect"></a>1. kapcsolat
A [Sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true)megkezdéséhez nyissa meg a parancssort, és írja be a **Sqlcmd** , majd a szinapszis SQL Database-hez tartozó kapcsolódási karakterláncot. A kapcsolati sztringben a következő paraméterekre van szükség:

* **Server (-S):** A kiszolgáló neve `<`kiszolgálónév`>`.database.windows.net formátumban.
* **Adatbázis (-d):** Adatbázis neve
* **Idézett azonosítók engedélyezése (-I):** Az idézőjelek közé tartozó azonosítókat engedélyezni kell a szinapszis SQL-példányhoz való kapcsolódáshoz

SQL Server hitelesítés használatához hozzá kell adnia a felhasználónevet és a jelszó paramétereit:

* **Felhasználó (-U):** A kiszolgáló felhasználója az űrlap `<` felhasználója`>`
* **Jelszó (-P):** A felhasználóhoz tartozó jelszó

A kapcsolatok karakterlánca a következő példához hasonló lehet:

**Kiszolgáló nélküli SQL-készlet**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**Dedikált SQL-készlet**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Az Azure Active Directory beépített hitelesítés használatához meg kell adnia az Azure Active Directory paramétereit:

* **Azure Active Directory Authentication (-G):** az Azure Active Directory használata a hitelesítéshez

A kapcsolatok karakterlánca a következő példákhoz hasonlóan jelenhet meg:

**Kiszolgáló nélküli SQL-készlet**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**Dedikált SQL-készlet**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Az Active Directory használatával történő hitelesítéshez [engedélyeznie kell az Azure Active Directory-hitelesítést](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="2-query"></a>2. lekérdezés

### <a name="use-dedicated-sql-pool"></a>Dedikált SQL-készlet használata

A csatlakoztatást követően bármilyen támogatott [Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) utasítást ki lehet állítani a példányon. Ebben a példában a lekérdezések interaktív módban lesznek elküldve:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

A dedikált SQL-készlet esetén a következő példák bemutatják, hogyan futtathat lekérdezéseket batch módban a-Q kapcsolóval vagy az SQL Sqlcmd:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-serverless-sql-pool"></a>Kiszolgáló nélküli SQL-készlet használata

A csatlakozás után bármely támogatott [Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) utasítást kiállíthat a példányon.  A következő példában a lekérdezések interaktív módban lesznek elküldve:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

Kiszolgáló nélküli SQL-készlet esetén az alábbi példák bemutatják, hogyan futtathat lekérdezéseket batch módban a-Q kapcsolóval vagy az SQL Sqlcmd:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Következő lépések

A Sqlcmd beállításaival kapcsolatos további információkért tekintse meg a [Sqlcmd dokumentációját](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true).
