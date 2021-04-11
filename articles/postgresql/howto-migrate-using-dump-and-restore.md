---
title: Memóriakép és visszaállítás – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: A PostgreSQL-adatbázist kinyerheti egy memóriaképfájl-fájlba. Ezután visszaállíthat egy pg_dump által létrehozott fájlból Azure Database for PostgreSQL egyetlen kiszolgálóról.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 809ff06fe460a06a689d7bbc11cdbd5ee247f585
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450055"
---
# <a name="migrate-your-postgresql-database-by-using-dump-and-restore"></a>A PostgreSQL-adatbázis migrálása a dump és a Restore használatával
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

A [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) használatával kinyerheti a PostgreSQL-adatbázist egy memóriaképfájl-fájlba. Ezután a [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) használatával állítsa vissza a PostgreSQL-adatbázist egy által létrehozott archív fájlból `pg_dump` .

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md), beleértve a hozzáférés engedélyezésére vonatkozó tűzfalszabályok beállításait is.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) parancssori segédprogramok telepítve.

## <a name="create-a-dump-file-that-contains-the-data-to-be-loaded"></a>Hozzon létre egy memóriaképfájl-fájlt, amely tartalmazza a betölteni kívánt adatfájlt.

Ha egy meglévő PostgreSQL-adatbázist szeretne biztonsági másolatot készíteni a helyszínen vagy egy virtuális gépen, futtassa a következő parancsot:

```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Ha például egy helyi kiszolgálóval és egy **testdb** nevű adatbázissal rendelkezik, futtassa a következőt:

```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```

## <a name="restore-the-data-into-the-target-database"></a>Az adatbázis visszaállítása a céladatbázisbe

A céladatbázis létrehozása után a `pg_restore` parancs és a  `--dbname` paraméter használatával visszaállíthatja az adatait a célként megadott adatbázisba a memóriakép fájlból.

```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

A `--no-owner` paraméterrel együtt a visszaállítás során létrehozott összes objektum tulajdonosa lesz a által megadott felhasználó tulajdona `--username` . További információt a [PostgreSQL dokumentációjában](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html)talál.

> [!NOTE]
> Azure Database for PostgreSQL kiszolgálókon a TLS/SSL-kapcsolatok alapértelmezés szerint be vannak kapcsolva. Ha a PostgreSQL-kiszolgáló TLS/SSL-kapcsolatokat igényel, de nem rendelkezik velük, állítson be egy környezeti változót, `PGSSLMODE=require` hogy az pg_restore eszköz csatlakozzon a TLS-hez. A TLS nélkül a hiba valószínűleg olvasható: "végzetes: SSL-kapcsolat szükséges. Adja meg az SSL-beállításokat, majd próbálkozzon újra. " A Windows-parancssorban futtassa a parancsot a `SET PGSSLMODE=require` parancs futtatása előtt `pg_restore` . Linux vagy bash esetén futtassa a parancsot a `export PGSSLMODE=require` parancs futtatása előtt `pg_restore` . 
>

Ebben a példában a **testdb. dump** fájlból származó adatok visszaállíthatók az adatbázis **mypgsqldb** a célkiszolgáló **mydemoserver.postgres.database.Azure.com**.

Íme egy példa arra, hogyan használhatja ezt `pg_restore` egyetlen kiszolgálóhoz:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

Példa erre a `pg_restore` rugalmas kiszolgáló használatára:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```

## <a name="optimize-the-migration-process"></a>Az áttelepítési folyamat optimalizálása

A meglévő PostgreSQL-adatbázis Azure Database for PostgreSQLre való áttelepítése egyik módja, hogy biztonsági másolatot készít az adatbázisról a forrásról, és visszaállítja azt az Azure-ban. Az áttelepítés befejezéséhez szükséges idő minimalizálásához érdemes a következő paramétereket használni a biztonsági mentési és visszaállítási parancsokkal.

> [!NOTE]
> A szintaxissal kapcsolatos részletes információkat a [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)című témakörben talál.
>

### <a name="for-the-backup"></a>A biztonsági mentéshez

Végezze el a biztonsági mentést a `-Fc` kapcsolóval, hogy a visszaállítás párhuzamosan legyen felgyorsítva. Például:

```bash
pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
```

### <a name="for-the-restore"></a>A visszaállításhoz

- Helyezze át a biztonságimásolat-fájlt egy olyan Azure-beli virtuális gépre, amely ugyanabban a régióban található, mint az áttelepíteni kívánt Azure Database for PostgreSQL-kiszolgáló. A hálózati késés csökkentése érdekében hajtsa végre az alkalmazást a `pg_restore` virtuális gépről. Hozza létre a virtuális gépet a [gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-powershell.md) engedélyezésével.

- Nyissa meg a memóriaképet annak ellenőrzéséhez, hogy a Create index utasítások az adatbeszúrás után szerepelnek-e. Ha nem ez a helyzet, helyezze át a Create index utasítást az adatbeszúrás után. Ezt már alapértelmezés szerint el kell végezni, de érdemes megerősíteni.

- Állítsa vissza a kapcsolókat `-Fc` és `-j` (egy számmal) a visszaállítás integrálással. A megadott szám a célkiszolgálón lévő magok száma. Azt is megteheti, hogy kétszer is megadhatja a célkiszolgáló magok számát a hatás megjelenítéséhez.

    Íme egy példa arra, hogyan használhatja ezt `pg_restore` egyetlen kiszolgálóhoz:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

    Példa erre a `pg_restore` rugalmas kiszolgáló használatára:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

- A memóriaképet a parancs `set synchronous_commit = off;` elején, a végén pedig a parancs hozzáadásával is szerkesztheti `set synchronous_commit = on;` . Ha nem kapcsolja be a végét, az alkalmazások megváltoznak az adatvesztés következtében.

- A cél Azure Database for PostgreSQL kiszolgálón a visszaállítás előtt vegye figyelembe a következőket:
    
  - A lekérdezési teljesítmény nyomon követésének kikapcsolása. Ezek a statisztikák nem szükségesek az áttelepítés során. Ezt a, a és a () beállítással végezheti el `pg_stat_statements.track` `pg_qs.query_capture_mode` `pgms_wait_sampling.query_capture_mode` `NONE` .

  - A Migrálás felgyorsításához használjon nagy számítási és magas memória-SKU-t, például 32 virtuális mag memóriát. A visszaállítás befejezése után egyszerűen méretezheti vissza az előnyben részesített SKU-ra. Minél nagyobb a SKU, annál több párhuzamosságot érhet el a `-j` parancs megfelelő paraméterének növelésével `pg_restore` .

  - A célkiszolgáló további IOPS javíthatják a visszaállítási teljesítményt. A kiszolgáló tárolási méretének növelésével több IOPS is kiépítheti. Ez a beállítás nem vonható vissza, azonban érdemes megfontolni, hogy egy magasabb IOPS a jövőben is hasznát veheti-e a tényleges munkaterhelésnek.

Az éles környezetben való használat előtt ne felejtse el tesztelni és érvényesíteni ezeket a parancsokat tesztkörnyezetben.

## <a name="next-steps"></a>Következő lépések

- A PostgreSQL-adatbázis exportálással és importálással történő áttelepítésével kapcsolatban lásd: [a PostgreSQL-adatbázis migrálása exportálással és importálással](howto-migrate-using-export-and-import.md).
- Az adatbázisok Azure Database for PostgreSQLre való áttelepítésével kapcsolatos további információkért tekintse meg az [adatbázis-áttelepítési útmutatót](https://aka.ms/datamigration).


