---
title: Importálás segítségével adatbázis áttelepítése és az Azure Database for PostgreSQL - kiszolgáló egyetlen exportálása
description: Ismerteti, hogyan bontsa ki a PostgreSQL-adatbázis egy szkript fájlba, és a céladatbázis fájlból, hogy az adatok importálása.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 785e9ec77dea749546e3f1d59007706eac14f2ea
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "65067026"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Exportálás a PostgreSQL-adatbázis migrálása és importálása
Használható [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) kibontani a PostgreSQL-adatbázis egy szkript fájlba és [psql](https://www.postgresql.org/docs/current/static/app-psql.html) a céladatbázis fájlból, hogy az adatok importálásához.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- Egy [, Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md) , hogy a hozzáférés és az alatta database tűzfalszabályokkal.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) telepített parancssori segédprogram
- [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) telepített parancssori segédprogram

Kövesse az alábbi lépéseket exportálásáról és importálásáról a PostgreSQL-adatbázishoz.

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Használatával pg_dump kell betölteni az adatokat tartalmazó parancsfájl létrehozása
A meglévő PostgreSQL adatbázis helyi exportálhatók, és a egy sql-parancsfájlt a virtuális gépen, futtassa a következő parancsot a meglévő környezetben:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Ha például van egy helyi kiszolgálón és a egy nevű adatbázist **testdb** benne:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importálhatja az adatokat, a célon, Azure Database for postgresql-hez
A psql parancssor és a--dbname paramétert (-d), az adatok importálása az Azure Database for PostgreSQL kiszolgáló és az adatok betöltése az sql-fájlból.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Ez a példa psql segédprogram és a egy parancsfájl-fájlt használ **testdb.sql** adatokat importálni az adatbázis előző lépésben **mypgsqldb** a célkiszolgálón  **mydemoserver.postgres.Database.Azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>További lépések
- Memóriakép és visszaállítás használatával a PostgreSQL-adatbázis áttelepítéséhez lásd: [memóriakép és visszaállítás használatával a PostgreSQL-adatbázis áttelepítése](howto-migrate-using-dump-and-restore.md).
- További információ az adatbázisok az Azure Database for postgresql-hez, lásd: a [adatbázis-Migrálási útmutató](https://aka.ms/datamigration). 
