---
title: PostgreSQL-bővítmények használata
description: PostgreSQL-bővítmények használata
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e247e372237572586e5a4647d24d9ed6067ea823
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104949787"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>PostgreSQL-bővítmények használata az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálói csoportban

A PostgreSQL a legjobb, ha bővítményekkel használja. Valójában a saját nagy kapacitású funkciójának kulcsfontosságú eleme a Microsoft által biztosított `citus` , alapértelmezés szerint telepített bővítmény, amely lehetővé teszi, hogy a postgres transzparens módon, több csomóponton keresztül lehessen átadni az adatmennyiséget.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>Támogatott bővítmények
A standard [`contrib`](https://www.postgresql.org/docs/12/contrib.html) kiterjesztések és a következő bővítmények már telepítve vannak az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport tárolójában:
- [`citus`](https://github.com/citusdata/citus), v: 9,4. A [Citus](https://www.citusdata.com/) -Citus-bővítmény alapértelmezés szerint be van töltve, mivel a nagy kapacitású-képességet a PostgreSQL-motorhoz viszi. A Citus-bővítmény az Azure arc PostgreSQL nagy kapacitású-kiszolgáló csoportból való eldobása nem támogatott.
- [`pg_cron`](https://github.com/citusdata/pg_cron), v: 1,2
- [`pgaudit`](https://www.pgaudit.org/), v: 1,4
- plpgsql, v: 1,0
- [`postgis`](https://postgis.net), v: 3.0.2
- [`plv8`](https://plv8.github.io/), v: 2.3.14

A lista frissítései közzé lesznek téve, ahogy az idővel fejlődik.

> [!IMPORTANT]
> Habár előfordulhat, hogy a kiszolgálói csoportot a fent felsoroltakon kívül más kiterjesztéssel is elvégezheti, ebben az előzetes verzióban nem fogja tudni megőrizni a rendszerét. Ez azt jelenti, hogy a rendszer újraindítása után nem lesz elérhető, és újra kell indítania.

Ez az útmutató a következő két bővítmény használatát teszi elérhetővé:
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>Mely bővítményeket kell hozzáadni a shared_preload_librarieshoz, és létre kell hozni?

|Bővítmények   |Az shared_preload_librarieshoz való hozzáadásához szükséges  |Létrehozásához szükséges |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |Nem       |Igen        |
|`pg_audit`     |Igen       |Igen        |
|`plpgsql`      |Igen       |Igen        |
|`postgis`      |Nem       |Igen        |
|`plv8`      |Nem       |Igen        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>Bővítmények hozzáadása a shared_preload_librarieshoz
További részletekért shared_preload_libraries kérjük, olvassa el a PostgreSQL-dokumentációt [itt](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES):
- Ez a lépés nem szükséges a következő részét képező bővítményekhez: `contrib`
- Ez a lépés nem szükséges olyan bővítményekhez, amelyek nem szükségesek shared_preload_librarieshoz való előzetes betöltéshez. Ezekben a bővítményekben a következő, [bővítmények létrehozása](#create-extensions)lehetőségre ugorhat.

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>Bővítmény hozzáadása egy kiszolgálócsoport létrehozási időpontjában
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>Bővítmény hozzáadása olyan példányhoz, amely már létezik
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>A shared_preload_librarieshoz hozzáadott bővítmények listájának megjelenítése
Futtassa az alábbi parancs egyikét.

### <a name="with-an-azdata-cli-command"></a>Azdata CLI-paranccsal
```console
azdata arc postgres server show -n <server group name>
```
Görgessen a kimenetben, és figyelje meg a engine\extensions fejezeteit. Például:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
### <a name="with-kubectl"></a>Kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Görgessen a kimenetben, és figyelje meg a engine\extensions fejezeteit. Például:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


## <a name="create-extensions"></a>Bővítmények létrehozása
Kapcsolódjon a kiszolgálói csoporthoz az Ön által választott ügyfél-eszközzel, és futtassa a standard PostgreSQL-lekérdezést:
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>A létrehozott bővítmények listájának megjelenítése
Kapcsolódjon a kiszolgálói csoporthoz az Ön által választott ügyfél-eszközzel, és futtassa a standard PostgreSQL-lekérdezést:
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>Bővítmény eldobása
Kapcsolódjon a kiszolgálói csoporthoz az Ön által választott ügyfél-eszközzel, és futtassa a standard PostgreSQL-lekérdezést:
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>A `PostGIS` bővítmény
A bővítményt nem kell hozzáadnia a következőhöz: `PostGIS` `shared_preload_libraries` .
[Mintaadatok](http://duspviz.mit.edu/tutorials/intro-postgis/) beszerzése az mit & a tervezésből származó városi tanulmányok Minisztériuma. Futtassa `apt-get install unzip` a parancsot a telepítéshez szükség szerint.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Kapcsolódjon az adatbázishoz, és hozzon létre egy `PostGIS` bővítményt:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Ha a csomag egyik bővítményét szeretné használni (például,, `postgis` `postgis_raster` `postgis_topology` `postgis_sfcgal` , `fuzzystrmatch` ...), először létre kell hoznia a PostGIS-bővítményt, majd létre kell hoznia a másik kiterjesztést. Például: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

És hozza létre a sémát:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

A `PostGIS` kibővíthető funkciókkal kombinálva a coffee_shops táblázatot terjesztettük el:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Töltsön be néhány adattal:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

És töltse ki a `geom` mezőt a megfelelő kódolású szélességgel és hosszúsággal az `PostGIS` `geometry` adattípusban:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Most már listázhatja az MIT legközelebb lévő kávéházi üzleteket (77 Massachusetts Ave, 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>A `pg_cron` bővítmény

Most engedélyezzük `pg_cron` a PostgreSQL-kiszolgálócsoport hozzáadását a shared_preload_libraries:

```console
azdata postgres server update -n pg2 -ns arc --extensions pg_cron
```

A kiszolgálócsoport a bővítmények telepítésének befejezése után újraindul. 2 – 3 percet is igénybe vehet.

Most már kapcsolódhat, és létrehozhatja a `pg_cron` bővítményt:

```sql
CREATE EXTENSION pg_cron;
```

Tesztelési célokra lehetővé teszi, hogy egy olyan táblát hozzon, `the_best_coffee_shop` amely véletlenszerű nevet vesz a korábbi `coffee_shops` táblázatból, és beszúrja a táblázat tartalmát:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

`cron.schedule`Több SQL-utasítást is használhatunk egy véletlenszerű tábla nevének beszerzéséhez (figyelje meg egy ideiglenes tábla használatát egy elosztott lekérdezési eredmény tárolásához), és tárolja azt a következő helyre `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

És most egy perc múlva egy másik nevet fogunk kapni:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

A szintaxissal kapcsolatos részletekért tekintse meg a [PG_CRON readme](https://github.com/citusdata/pg_cron) című témakört.


## <a name="next-steps"></a>Következő lépések
- Dokumentáció beolvasása [`plv8`](https://plv8.github.io/)
- Dokumentáció beolvasása [`PostGIS`](https://postgis.net/)
- Dokumentáció beolvasása [`pg_cron`](https://github.com/citusdata/pg_cron)