---
title: Memóriakép és visszaállítás az Azure Database for PostgreSQL - kiszolgáló egyetlen
description: Bontsa ki a PostgreSQL-adatbázis egy memóriakép fájlba, és a visszaállítás által pg_dump az Azure Database for PostgreSQL - kiszolgáló egyetlen létrehozott fájlt a módját ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: aa9485ec8fcabdc0276e0598bd3e19f04d70dfa1
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65066989"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Memóriakép és visszaállítás használatával a PostgreSQL-adatbázis migrálása
Használhatja [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) be memóriakép-fájl egy PostgreSQL-adatbázis kibontásához és [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) pg_dump által létrehozott archív fájl visszaállítása a PostgreSQL-adatbázishoz.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- Egy [, Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md) , hogy a hozzáférés és az alatta database tűzfalszabályokkal.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) parancssori segédprogramok telepítése

Kövesse az alábbi lépéseket a dump és a PostgreSQL-adatbázis visszaállítása:

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Hozzon létre egy memóriakép pg_dump kell betölteni az adatokat tartalmazó fájlt
Készítsen biztonsági másolatot egy meglévő PostgreSQL adatbázis helyi vagy egy virtuális Gépre, futtassa a következő parancsot:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Ha például van egy helyi kiszolgálón és a egy nevű adatbázist **testdb** benne
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Állítsa vissza az adatokat a cél Azure Database-be a PostrgeSQL pg_restore használatával
Miután létrehozta a céladatbázis, a pg_restore parancsot és a -d,--dbname paraméter használatával állítsa vissza az adatokat a memóriakép-fájl a cél-adatbázisba.
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Beleértve a--no-owner paraméter okok tulajdonosa a felhasználó megadja a--username és a visszaállítás során létrehozott összes objektumot. További információkért tekintse meg a hivatalos PostgreSQL dokumentációja [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Ha a PostgreSQL-kiszolgáló SSL-kapcsolatot igényel (az Azure Database for PostgreSQL-kiszolgálók alapértelmezés szerint), egy környezeti változót `PGSSLMODE=require` úgy, hogy SSL használatával kapcsolódik a pg_restore eszköz. SSL-t, anélkül a hiba lehetséges, hogy olvasása  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> A Windows-parancssorban futtassa a parancsot `SET PGSSLMODE=require` a pg_restore parancs futtatása előtt. Linux vagy a Bash futtassa a parancsot `export PGSSLMODE=require` a pg_restore parancs futtatása előtt.
>

Ebben a példában visszaállíthatja az adatokat a memóriakép-fájl **testdb.dump** az adatbázisba **mypgsqldb** célkiszolgálón **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Az áttelepítési folyamat optimalizálása

A meglévő PostgreSQL-adatbázis migrálása az Azure Database for PostgreSQL szolgáltatás egyik módja, hogy az adatbázis biztonsági mentése a forráson, és állítsa vissza az Azure-ban. Az áttelepítés végrehajtásához szükséges idő minimalizálása érdekében fontolja meg az alábbi paramétereket a biztonsági mentést, és állítsa vissza a parancsokat.

> [!NOTE]
> Szintaxis részletes információkért tekintse meg a cikkeket [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>A biztonsági mentés
- A biztonsági mentés a szálcsatorna - kapcsoló igénybe, így a visszaállítást hajthat végre párhuzamosan, felgyorsítása érdekében. Példa:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName > Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>A visszaállításhoz
- Javasoljuk, hogy a biztonságimásolat-fájl áthelyezése egy Azure virtuális Gépen az Azure Database for PostgreSQL-kiszolgáló végzi az áttelepítést, és hajtsa végre a pg_restore a hálózati késés csökkentése érdekében a virtuális Gépeket ugyanabban a régióban. Azt javasoljuk, hogy a virtuális gép létrejött-e a [gyorsított hálózatkezelést](../virtual-network/create-vm-accelerated-networking-powershell.md) engedélyezve van.

- Alapértelmezés szerint már végezhető, de a memóriakép-fájl, és ellenőrizze, hogy a create index utasításokat az adatok a Beszúrás után nyissa meg. Ha nem, akkor a helyzet, a create index utasításokat áthelyezése után az adatok.

- Állítsa vissza a kapcsolók a -Fc és -j *#* való párhuzamosíthatja a visszaállítást. *#* van a célkiszolgálón magok számát. Az is megpróbálhatja *#* kétszer a célkiszolgáló magok számát értékre van állítva,-azonosítókra gyakorolt hatást. Példa:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- A parancs hozzáadásával a memóriakép-fájl is szerkesztheti *: synchronous_commit állítsa le; =* elején és a parancs *beállítása: synchronous_commit = on;* végén. Nem bekapcsolását, a végén az alkalmazásokat módosítani az adatokat, mielőtt az adatok későbbi adatvesztést eredményezhet.

- A cél Azure Database for PostgreSQL-kiszolgáló vegye figyelembe az alábbiakat, mielőtt a visszaállítás során:
    - Kapcsolja ki a lekérdezési teljesítmény nyomon követése, mivel ezek a statisztikák nem szükségesek a migrálás során. Ez pg_stat_statements.track pg_qs.query_capture_mode és pgms_wait_sampling.query_capture_mode NONE értékre állításával teheti meg.

    - A magas számítási és a magas memóriahasználat sku, például az optimalizált memóriájú, 32 virtuális mag használatával felgyorsíthatja az áttelepítés. Azt is könnyen pedig vissza az előnyben részesített termékváltozat a visszaállítás befejezése után. A magasabb a termékváltozat, a további párhuzamosság érhet el a megfelelő növelésével `-j` paramétert a pg_restore parancsba. 

    - A célkiszolgálón több IOPS javíthatja a visszaállítási teljesítménye. A kiszolgáló tárolómérete növelésével további IOPS építhető ki. Ez a beállítás nem vonható vissza, de vegye figyelembe, hogy egy magasabb iops-t a jövőben kiaknázhatják a valós számítási feladat.

Ne felejtse el tesztelés és ellenőrzés ezeket a parancsokat egy tesztkörnyezetben, mielőtt éles környezetben használni.

## <a name="next-steps"></a>További lépések
- Az exportálás és importálás segítségével a PostgreSQL-adatbázis áttelepítéséhez lásd: [exportálási PostgreSQL adatbázis Migrálása és importálása](howto-migrate-using-export-and-import.md).
- További információ az adatbázisok az Azure Database for postgresql-hez, lásd: a [adatbázis-Migrálási útmutató](https://aka.ms/datamigration).
