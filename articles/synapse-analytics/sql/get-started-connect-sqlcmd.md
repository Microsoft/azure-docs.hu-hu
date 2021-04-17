---
title: Csatlakozás Synapse SQL sqlcmd használatával
description: Használja az sqlcmd parancssori segédprogramot a kiszolgáló nélküli SQL-készlethez és a dedikált SQL-készlethez való csatlakozáshoz és lekérdezéséhez.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 3abdd44f0684282e92da147dff996ff54f0ef23f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565474"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Csatlakozás Synapse SQL sqlcmd használatával

> [!div class="op_single_selector"]
> * [Azure Data Studio)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Az [sqlcmd parancssori](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true) segédprogrammal csatlakozhat kiszolgáló nélküli SQL-készlethez és dedikált SQL-készlethez a Synapse SQL.  

## <a name="1-connect"></a>1. Csatlakozás
Az [sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true)első lépésekhez nyissa meg a parancssort, és írja be az **sqlcmd** parancsot, majd a kapcsolati sztringet a Synapse SQL adatbázisához. A kapcsolati sztringben a következő paraméterekre van szükség:

* **Server (-S):** A kiszolgáló neve `<`kiszolgálónév`>`.database.windows.net formátumban.
* **Adatbázis (-d):** Adatbázis neve
* **Enable Quoted Identifiers (-I):** Az idézett azonosítókat engedélyezni kell a Synapse SQL-példányhoz való csatlakozáshoz

Az SQL Server hitelesítéshez hozzá kell adni a felhasználónév és a jelszó paramétereit:

* **Felhasználó (-U):** Kiszolgálófelhasználó Felhasználó `<` formában`>`
* **Jelszó (-P):** A felhasználóhoz társított jelszó

A kapcsolati sztring az alábbi példához hasonló lehet:

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

A kapcsolati sztring a következő példákhoz hasonló lehet:

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

## <a name="2-query"></a>2. Lekérdezés

### <a name="use-dedicated-sql-pool"></a>Dedikált SQL-készlet használata

A csatlakozás után bármilyen támogatott [Transact-SQL-](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL-) utasítás ki van használhatja a példányon. Ebben a példában a lekérdezések interaktív módban vannak elküldve:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Dedikált SQL-készlet esetén a következő példák bemutatják, hogyan futtathat lekérdezéseket kötegelt módban a -Q lehetőséggel, vagy hogyan küldheti át az SQL-t az sqlcmd-be:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-serverless-sql-pool"></a>Kiszolgáló nélküli SQL-készlet használata

A csatlakozás után bármilyen támogatott [Transact-SQL-](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL-) utasítás ki lesz használhatja a példányon.  A következő példában a lekérdezések interaktív módban vannak elküldve:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

Kiszolgáló nélküli SQL-készlet esetén a következő példák bemutatják, hogyan futtathat lekérdezéseket kötegelt módban a -Q lehetőséggel, vagy hogyan küldheti át az SQL-t az sqlcmd-be:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Következő lépések

Az sqlcmd-beállításokkal kapcsolatos további információkért tekintse meg az [sqlcmd dokumentációját.](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true)
