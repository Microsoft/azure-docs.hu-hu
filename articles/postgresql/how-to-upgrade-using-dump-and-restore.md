---
title: Frissítés a dump és a Restore-Azure Database for PostgreSQL-Single Server használatával
description: Leírja az offline frissítési módszereket a dump és Restore adatbázisok használatával, hogy áttelepítsen egy újabb verzióra Azure Database for PostgreSQL – egyetlen kiszolgálóra.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 42bbe1c9f4056ae0dae0ccd59b452db90a7c63c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96493661"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>A PostgreSQL-adatbázis frissítése a dump és a Restore használatával

A következő módszerekkel frissítheti a PostgreSQL-kiszolgálót Azure Database for PostgreSQL – egyetlen kiszolgálón is, ha az adatbázisait egy magasabb verziószámú kiszolgálóra telepíti át.
* **Offline** metódus a PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) használatával és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) , amely az adatáttelepítés során felmerülő állásidőt okoz. Ez a dokumentum a frissítési/áttelepítési módszert tárgyalja.
* **Online** metódus [Database Migration Service](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) (DMS) használatával. Ez a módszer csökkenti a leállások áttelepítését, és a célként megadott adatbázist szinkronban tartja a forrással, és kiválaszthatja, hogy mikor kell kivágni. A DMS használata azonban néhány előfeltételt és korlátozást is igénybe vehet. Részletekért lásd a [DMS dokumentációját](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md). 

 Az alábbi táblázat az adatbázisok méretétől és forgatókönyveken alapuló javaslatokat tartalmaz.

| **Adatbázis/forgatókönyv** | **Memóriakép/visszaállítás (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| Van egy kis adatbázisa, amely leállást biztosít a frissítéshez  | X | |
| Kisméretű adatbázisok (< 10 GB)  | X | X | 
| Kis adathordozós adatbázisok (10 GB – 100 GB) | X | X |
| Nagyméretű adatbázisok (> 100 GB) |  | X |
| Leállást biztosíthat a verziófrissítéshez (az adatbázis méretétől függetlenül) | X |  |
| Megadhatja a DMS [előfeltételeit](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md#prerequisites), beleértve az újraindítást is? |  | X |
| Elkerülheti a frissítési folyamat során a DDLs és a nem naplózott táblákat? | |  X |

Ez az útmutató néhány offline áttelepítési módszert és példát tartalmaz, amelyek bemutatják, hogyan telepíthet át a forráskiszolgálóról a PostgreSQL újabb verzióját futtató célkiszolgálóra.

> [!NOTE]
> A PostgreSQL-memóriakép és a visszaállítás többféleképpen is elvégezhető. Dönthet úgy, hogy az útmutatóban ismertetett módszerek egyikét használja, vagy az igényeinek megfelelő alternatív módszereket választ. További paraméterekkel rendelkező részletes memóriakép-és visszaállítási szintaxist a következő cikkekben talál: [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>A dump és a Restore parancs használatának előfeltételei Azure Database for PostgreSQL
 
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- 9,5, 9,6 vagy 10 rendszert futtató **forrás** PostgreSQL-adatbázis, amelyet frissíteni kíván
- **Cél** PostgreSQL-adatbázis-kiszolgáló a kívánt főverzióval [Azure Database for PostgreSQL-kiszolgálóval](quickstart-create-server-database-portal.md). 
- PostgreSQL-ügyfélrendszer a memóriakép és a visszaállítási parancsok futtatásához.
  - Ez lehet egy Linux vagy Windows rendszerű ügyfél, amelyen telepítve van a PostgreSQL, és [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) parancssori segédprogramok vannak telepítve. 
  - Azt is megteheti, hogy [Azure Cloud Shell](https://shell.azure.com) vagy a [Azure Portal](https://portal.azure.com)jobb felső sarkában lévő menüsorban található Azure Cloud Shellra kattint. `az login`A memóriakép és a visszaállítási parancsok futtatása előtt be kell jelentkeznie a fiókjába.
- A PostgreSQL-ügyfél lehetőleg ugyanabban a régióban fut, mint a forrás-és a célkiszolgáló. 


## <a name="additional-details-and-considerations"></a>További részletek és szempontok
- A kapcsolódási karakterláncot a forrás-és a célként megadott adatbázisokhoz a portálon a "kapcsolódási karakterláncok" gombra kattintva érheti el. 
- Előfordulhat, hogy a kiszolgálón egynél több adatbázist futtat. Az adatbázisok listáját a forráskiszolgáló csatlakoztatásával és a futtatásával érheti el `\l` .
- Hozza létre a megfelelő adatbázisokat a cél adatbázis-kiszolgálón.
- Kihagyhatja a frissítés `azure_maintenance` vagy a sablon adatbázisait.
- A fenti táblázatokból megállapíthatja, hogy az adatbázis alkalmas-e az ilyen áttelepítési módra.
- Ha Azure Cloud Shell szeretné használni, vegye figyelembe, hogy a munkamenet 20 perc elteltével időtúllépést mutat. Ha az adatbázis mérete < 10 GB, lehetséges, hogy a munkamenet időtúllépése nélkül tudja befejezni a frissítést. Ellenkező esetben előfordulhat, hogy a munkamenetet más módon kell megnyitnia, például a kulcs megnyomása <Enter> 10-15 percen belül. 


## <a name="example-database-used-in-this-guide"></a>Példa az útmutatóban használt adatbázisra

Ebben az útmutatóban a következő forrás-és célkiszolgáló és adatbázis-nevek szerepelnek példákkal.

 | **Leírás** | **Érték** |
 | ------- | ------- |
 | Forráskiszolgáló (v 9.5) | pg-95.postgres.database.azure.com |
 | Forrásadatbázis | bench5gb |
 | Forrásadatbázis mérete | 5 GB |
 | Forrás felhasználóneve | pg@pg-95 |
 | Célkiszolgáló (v11) | pg-11.postgres.database.azure.com |
 | Céladatbázis | bench5gb |
 | Cél felhasználóneve | pg@pg-11 |

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>Az adatbázisok frissítése offline áttelepítési módszerekkel
Dönthet úgy, hogy az ebben a szakaszban ismertetett módszerek egyikét használja a frissítésekhez. A feladatok elvégzése közben a következő tippeket is használhatja.

- Ha a forráshoz és a céladatbázishez ugyanazt a jelszót használja, beállíthatja a `PGPASSWORD=yourPassword` környezeti változót.  Ezután nem kell minden alkalommal megadnia a jelszót, amikor olyan parancsokat futtat, mint például a psql, a pg_dump és a pg_restore.  Ehhez hasonlóan további változókat is beállíthat `PGUSER` , például `PGSSLMODE` a következőt: [PostgreSQL környezeti változók](https://www.postgresql.org/docs/11/libpq-envars.html).
  
- Ha a PostgreSQL-kiszolgáló TLS/SSL-kapcsolatokat igényel (alapértelmezés szerint Azure Database for PostgreSQL-kiszolgálókon), állítson be egy környezeti változót, `PGSSLMODE=require` hogy a pg_restore eszköz csatlakozzon a TLS-hez. A TLS nélkül a hiba is olvasható  `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- A Windows-parancssorban futtassa a parancsot a `SET PGSSLMODE=require` pg_restore parancs futtatása előtt. A Linux vagy a bash futtatása előtt futtassa a parancsot a `export PGSSLMODE=require` pg_restore parancs futtatása előtt.

### <a name="method-1-migrate-using-dump-file"></a>1. módszer: az áttelepítés memóriaképfájl használatával

Ez a módszer két lépést is magában foglal. Először hozzon létre egy memóriaképet a forráskiszolgálóról. A második lépés a memóriakép fájljának visszaállítása a célkiszolgálóra. További részletekért tekintse meg a [Migrálás a dump és a Restore dokumentáció használatával](howto-migrate-using-dump-and-restore.md) című témakört. Ez az ajánlott módszer, ha nagyméretű adatbázisokkal rendelkezik, és az ügyfélrendszer elegendő tárterülettel rendelkezik a memóriakép fájljának tárolásához.

### <a name="method-2-migrate-using-streaming-the-dump-data-to-the-target-database"></a>2. módszer: áttelepítés a memóriakép adatainak továbbítása a célként megadott adatbázisba

Ha nem rendelkezik PostgreSQL-ügyféllel, vagy a Azure Cloud Shell szeretné használni, ezt a metódust használhatja. Az adatbázis-memóriakép továbbítása közvetlenül a célként megadott adatbázis-kiszolgálóra történik, és nem tárolja a memóriaképet az ügyfélen. Ezért ezt a korlátozott tárterülettel rendelkező ügyféllel lehet használni, és a Azure Cloud Shell is futtathatók. 

1. Győződjön meg arról, hogy az adatbázis létezik a célkiszolgálón a `\l` parancs használatával. Ha az adatbázis nem létezik, hozza létre az adatbázist.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Futtassa a memóriaképet, és állítsa vissza egyetlen parancssorból egy pipe használatával. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Példa:

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Miután a verziófrissítési (áttelepítési) folyamat befejeződik, tesztelheti az alkalmazást a célkiszolgálóra. 
4. Ismételje meg a folyamatot a kiszolgálón található összes adatbázisra vonatkozóan.

 Az alábbi táblázat szemlélteti, hogy az adatfolyam-továbbítási módszer használatával történt Migrálás ideje. A mintaadatok a [pgbench](https://www.postgresql.org/docs/10/pgbench.html)használatával lesznek feltöltve. Mivel az adatbázis különböző méretű objektumokkal rendelkezhet, amelyek mérete eltérő, mint a pgbench létrehozott táblák és indexek, erősen ajánlott tesztelni a memóriaképet és visszaállítani az adatbázist, hogy megértse az adatbázis frissítéséhez szükséges időt. 

| **Adatbázis mérete** | **Kb. szükséges idő** | 
| ----- | ------ |
| 1 GB  | 1-2 perc |
| 5 GB | 8-10 perc |
| 10 GB | 15-20 perc |
| 50 GB | 1 – 1,5 óra |
| 100 GB | 2,5 – 3 óra|
   
### <a name="method-3-migrate-using-parallel-dump-and-restore"></a>3. módszer: áttelepítés párhuzamos memóriakép és visszaállítás használatával 

Ezt a metódust akkor érdemes figyelembe venni, ha az adatbázis néhány nagyobb táblájával rendelkezik, és szeretné integrálással az adott adatbázis memóriaképét és visszaállítási folyamatát. Emellett elegendő tárterületre van szüksége az ügyfélrendszer számára a biztonsági mentési memóriaképek fogadásához. Ez a párhuzamos memóriakép és visszaállítási folyamat csökkenti az idő felhasználását a teljes áttelepítés befejezéséhez. Például a 50 GB-os pgbench-adatbázis, amely 1 – 1,5 órányi átállást vett igénybe, az 1. és 2. módszer használatával fejeződött be, a metódus használatával kevesebb, mint 30 percet vett igénybe.

1. A forráskiszolgálón található minden adatbázishoz hozzon létre egy megfelelő adatbázist a célkiszolgálón.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Példa:
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Futtassa az pg_dump parancsot egy olyan könyvtár-formátumban, amelyben a feladatok száma = 4 (a táblák száma az adatbázisban). Nagyobb számítási szinten és több táblázattal nagyobb számra növelheti. A pg_dump létre fog hozni egy könyvtárat, amely az egyes feladatokhoz tartozó tömörített fájlokat tárolja.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Példa:
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Ezután állítsa vissza a biztonsági mentést a célkiszolgálón.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Példa:
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> Az ebben a dokumentumban említett folyamat az előzetes verzióban elérhető Azure Database for PostgreSQL rugalmas kiszolgáló frissítésére is használható. A fő különbség a rugalmas kiszolgáló céljának a () nélküli kapcsolatok karakterlánca `@dbName` .  Ha például a Felhasználónév `pg` , akkor az egyetlen kiszolgáló felhasználónevét a kapcsolódási karakterláncban `pg@pg-95` , míg a rugalmas kiszolgálóval, egyszerűen használhatja `pg` .

## <a name="next-steps"></a>Következő lépések

- Miután elégedett a céladatbázis-függvénnyel, elhúzhatja a régi adatbázis-kiszolgálót. 
- Ha ugyanazt az adatbázis-végpontot szeretné használni, mint a forráskiszolgáló, akkor a régi forrás-adatbázis-kiszolgáló törlése után létrehozhat egy olvasási replikát a régi adatbázis-kiszolgáló nevével. Ha az állandó állapot létrejött, leállíthatja a replikát, amely a replika-kiszolgálót egy független kiszolgálónak fogja előléptetni. További részletekért lásd: [replikáció](./concepts-read-replicas.md) .
- Az éles környezetben való használat előtt ne felejtse el tesztelni és érvényesíteni ezeket a parancsokat tesztkörnyezetben.