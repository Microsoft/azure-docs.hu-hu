---
title: Bővítmények – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: További tudnivalók a Azure Database for PostgreSQL-egyetlen kiszolgálón elérhető postgres-bővítményekről
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 78395873457f9fe53d45dfbfd94aa9ccdccd614d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92485460"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL-bővítmények az Azure Database for PostgreSQL-ben – Önálló kiszolgáló
A PostgreSQL lehetőséget nyújt az adatbázis funkcióinak kiterjesztésére a bővítmények használatával. A bővítmények több kapcsolódó SQL-objektumot kötegelnek egyetlen csomaggá, amely egyetlen paranccsal betölthető vagy eltávolítható az adatbázisból. Az adatbázisba való betöltés után a bővítmények ugyanúgy működnek, mint a beépített funkciók.

## <a name="how-to-use-postgresql-extensions"></a>A PostgreSQL-bővítmények használata
A PostgreSQL-bővítményeket a használatuk előtt telepíteni kell az adatbázisba. Egy adott bővítmény telepítéséhez futtassa a [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) parancsot a psql-eszközben a becsomagolt objektumok adatbázisba való betöltéséhez.

A Azure Database for PostgreSQL a következő listában szereplő kulcs-kiterjesztések részhalmazát támogatja. Ezek az információk a futtatásával is elérhetők `SELECT * FROM pg_available_extensions;` . A felsorolt bővítmények nem támogatottak. Nem hozhat létre saját bővítményt Azure Database for PostgreSQLban.

## <a name="postgres-11-extensions"></a>Postgres 11 bővítmény

A következő bővítmények érhetők el a postgres 11-es verzióját tartalmazó Azure Database for PostgreSQL-kiszolgálókon. 

> [!div class="mx-tableFixed"]
> | **Mellék**| **Bővítmény verziója** | **Leírás** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | A címek összetevőire való elemzéséhez használatos. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Példa az USA-beli szabványosító adathalmazra|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | gyakori adattípusok indexelésének támogatása a GIN-ben|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | gyakori adattípusok indexelésének támogatása a lényegeben|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | kis-és nagybetűket megkülönböztető karakterláncok adattípusa|
> |[adatkocka](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | többdimenziós kockák adattípusa|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | Kapcsolódás más PostgreSQL-adatbázisokhoz egy adatbázison belülről|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | szöveges keresési szótár sablonja egész számokhoz|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | nagy hatótávolságú távolságok kiszámítása a Föld felszínén|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | a karakterláncok közötti hasonlóságok és távolságok meghatározása|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | a (kulcs, érték) párok tárolására szolgáló adattípus|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Feltételezett indexek a PostgreSQL-hez|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | függvények, operátorok és indexek támogatása egész számok 1-D tömbhöz|
> |[helytelen átvitt](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | a nemzetközi termékek számozási szabványainak adattípusai|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | adattípus a hierarchikus fastruktúrához hasonló struktúrákhoz|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | A függvények és csomagok egy részhalmazát emuláló függvények és operátorok kereskedelmi RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | naplózási funkciókat biztosít|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | titkosítási függvények|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting-bővítmény|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | sor szintű zárolási információk megjelenítése|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | rekord szintű statisztikák megjelenítése|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | a megosztott puffer gyorsítótárának vizsgálata|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Bővítmény a particionált táblák kezeléséhez idő vagy azonosító alapján|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | előmelegítő kapcsolatok adatvédelme|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | az összes végrehajtott SQL-utasítás végrehajtási statisztikájának nyomon követése|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | a szöveg hasonlóságának mérése és indexelése Trigrams alapján|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL eljárási nyelv|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (V8) megbízható eljárási nyelv|
> |[PostGIS](https://www.postgis.net/)                      | 2.5.1           | Geometriai, földrajzi és raszteres térbeli típusok és függvények PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL függvények|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger geocoder és fordított geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS-topológia térbeli típusai és funkciói|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | idegen adatburkolók távoli PostgreSQL-kiszolgálókhoz|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | a teljes táblákat, például a kereszttáblás funkciókat kezelő függvények|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2             | Méretezhető lapkákat és összetett lekérdezéseket engedélyez az idősorozat-adatsorokhoz|
> |[nem ékezetes](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | ékezeteket eltávolító szöveges keresési szótár|
> |[UUID – ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | univerzálisan egyedi azonosítók (UUID-EK) előállítása|

## <a name="postgres-10-extensions"></a>Postgres 10 bővítmény 

A következő bővítmények érhetők el olyan Azure Database for PostgreSQL-kiszolgálókon, amelyeken a postgres 10-es verziója található.

> [!div class="mx-tableFixed"]
> | **Mellék**| **Bővítmény verziója** | **Leírás** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | A címek összetevőire való elemzéséhez használatos. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Példa az USA-beli szabványosító adathalmazra|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | gyakori adattípusok indexelésének támogatása a GIN-ben|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | gyakori adattípusok indexelésének támogatása a lényegeben|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | az automatikusan titkosított jelszavak adattípusa|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | kis-és nagybetűket megkülönböztető karakterláncok adattípusa|
> |[adatkocka](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | többdimenziós kockák adattípusa|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | Kapcsolódás más PostgreSQL-adatbázisokhoz egy adatbázison belülről|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | szöveges keresési szótár sablonja egész számokhoz|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | nagy hatótávolságú távolságok kiszámítása a Föld felszínén|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | a karakterláncok közötti hasonlóságok és távolságok meghatározása|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | a (kulcs, érték) párok tárolására szolgáló adattípus|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Feltételezett indexek a PostgreSQL-hez|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | függvények, operátorok és indexek támogatása egész számok 1-D tömbhöz|
> |[helytelen átvitt](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | a nemzetközi termékek számozási szabványainak adattípusai|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | adattípus a hierarchikus fastruktúrához hasonló struktúrákhoz|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | A függvények és csomagok egy részhalmazát emuláló függvények és operátorok kereskedelmi RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | naplózási funkciókat biztosít|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | titkosítási függvények|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting-bővítmény|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | sor szintű zárolási információk megjelenítése|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | rekord szintű statisztikák megjelenítése|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | a megosztott puffer gyorsítótárának vizsgálata|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Bővítmény a particionált táblák kezeléséhez idő vagy azonosító alapján|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | előmelegítő kapcsolatok adatvédelme|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | az összes végrehajtott SQL-utasítás végrehajtási statisztikájának nyomon követése|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | a szöveg hasonlóságának mérése és indexelése Trigrams alapján|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL eljárási nyelv|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (V8) megbízható eljárási nyelv|
> |[PostGIS](https://www.postgis.net/)                      | 2.4.3           | Geometriai, földrajzi és raszteres térbeli típusok és függvények PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL függvények|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS Tiger geocoder és fordított geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | PostGIS-topológia térbeli típusai és funkciói|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | idegen adatburkolók távoli PostgreSQL-kiszolgálókhoz|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | a teljes táblákat, például a kereszttáblás funkciókat kezelő függvények|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Méretezhető lapkákat és összetett lekérdezéseket engedélyez az idősorozat-adatsorokhoz|
> |[nem ékezetes](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | ékezeteket eltávolító szöveges keresési szótár|
> |[UUID – ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | univerzálisan egyedi azonosítók (UUID-EK) előállítása|

## <a name="postgres-96-extensions"></a>Postgres 9,6-bővítmények 

A következő bővítmények érhetők el a 9,6-es postgres-verzióval rendelkező Azure Database for PostgreSQL-kiszolgálókon.

> [!div class="mx-tableFixed"]
> | **Mellék**| **Bővítmény verziója** | **Leírás** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | A címek összetevőire való elemzéséhez használatos. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Példa az USA-beli szabványosító adathalmazra|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | gyakori adattípusok indexelésének támogatása a GIN-ben|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | gyakori adattípusok indexelésének támogatása a lényegeben|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | az automatikusan titkosított jelszavak adattípusa|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | kis-és nagybetűket megkülönböztető karakterláncok adattípusa|
> |[adatkocka](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | többdimenziós kockák adattípusa|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | Kapcsolódás más PostgreSQL-adatbázisokhoz egy adatbázison belülről|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | szöveges keresési szótár sablonja egész számokhoz|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | nagy hatótávolságú távolságok kiszámítása a Föld felszínén|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | a karakterláncok közötti hasonlóságok és távolságok meghatározása|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | a (kulcs, érték) párok tárolására szolgáló adattípus|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Feltételezett indexek a PostgreSQL-hez|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | függvények, operátorok és indexek támogatása egész számok 1-D tömbhöz|
> |[helytelen átvitt](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | a nemzetközi termékek számozási szabványainak adattípusai|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | adattípus a hierarchikus fastruktúrához hasonló struktúrákhoz|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | A függvények és csomagok egy részhalmazát emuláló függvények és operátorok kereskedelmi RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | naplózási funkciókat biztosít|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | titkosítási függvények|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting-bővítmény|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | sor szintű zárolási információk megjelenítése|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | rekord szintű statisztikák megjelenítése|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | a megosztott puffer gyorsítótárának vizsgálata|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Bővítmény a particionált táblák kezeléséhez idő vagy azonosító alapján|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | előmelegítő kapcsolatok adatvédelme|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | az összes végrehajtott SQL-utasítás végrehajtási statisztikájának nyomon követése|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | a szöveg hasonlóságának mérése és indexelése Trigrams alapján|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL eljárási nyelv|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (V8) megbízható eljárási nyelv|
> |[PostGIS](https://www.postgis.net/)                      | 2.3.2           | Geometriai, földrajzi és raszteres térbeli típusok és függvények PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL függvények|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS Tiger geocoder és fordított geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | PostGIS-topológia térbeli típusai és funkciói|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | idegen adatburkolók távoli PostgreSQL-kiszolgálókhoz|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | a teljes táblákat, például a kereszttáblás funkciókat kezelő függvények|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Méretezhető lapkákat és összetett lekérdezéseket engedélyez az idősorozat-adatsorokhoz|
> |[nem ékezetes](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | ékezeteket eltávolító szöveges keresési szótár|
> |[UUID – ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | univerzálisan egyedi azonosítók (UUID-EK) előállítása|

## <a name="postgres-95-extensions"></a>Postgres 9,5-bővítmények 

A következő bővítmények érhetők el a 9,5-es postgres-verzióval rendelkező Azure Database for PostgreSQL-kiszolgálókon.

> [!div class="mx-tableFixed"]
> | **Mellék**| **Bővítmény verziója** | **Leírás** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | A címek összetevőire való elemzéséhez használatos. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Példa az USA-beli szabványosító adathalmazra|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | gyakori adattípusok indexelésének támogatása a GIN-ben|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | gyakori adattípusok indexelésének támogatása a lényegeben|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | az automatikusan titkosított jelszavak adattípusa|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | kis-és nagybetűket megkülönböztető karakterláncok adattípusa|
> |[adatkocka](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | többdimenziós kockák adattípusa|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | Kapcsolódás más PostgreSQL-adatbázisokhoz egy adatbázison belülről|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | szöveges keresési szótár sablonja egész számokhoz|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | nagy hatótávolságú távolságok kiszámítása a Föld felszínén|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | a karakterláncok közötti hasonlóságok és távolságok meghatározása|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | a (kulcs, érték) párok tárolására szolgáló adattípus|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Feltételezett indexek a PostgreSQL-hez|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | függvények, operátorok és indexek támogatása egész számok 1-D tömbhöz|
> |[helytelen átvitt](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | a nemzetközi termékek számozási szabványainak adattípusai|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | adattípus a hierarchikus fastruktúrához hasonló struktúrákhoz|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | A függvények és csomagok egy részhalmazát emuláló függvények és operátorok kereskedelmi RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | naplózási funkciókat biztosít|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | titkosítási függvények|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting-bővítmény|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | sor szintű zárolási információk megjelenítése|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | rekord szintű statisztikák megjelenítése|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | a megosztott puffer gyorsítótárának vizsgálata|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Bővítmény a particionált táblák kezeléséhez idő vagy azonosító alapján|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | előmelegítő kapcsolatok adatvédelme|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | az összes végrehajtott SQL-utasítás végrehajtási statisztikájának nyomon követése|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | a szöveg hasonlóságának mérése és indexelése Trigrams alapján|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL eljárási nyelv|
> |[PostGIS](https://www.postgis.net/)                      | 2.3.0           | Geometriai, földrajzi és raszteres térbeli típusok és függvények PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL függvények|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS Tiger geocoder és fordított geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | PostGIS-topológia térbeli típusai és funkciói|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | idegen adatburkolók távoli PostgreSQL-kiszolgálókhoz|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | a teljes táblákat, például a kereszttáblás funkciókat kezelő függvények|
> |[nem ékezetes](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | ékezeteket eltávolító szöveges keresési szótár|
> |[UUID – ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | univerzálisan egyedi azonosítók (UUID-EK) előállítása|


## <a name="pg_stat_statements"></a>pg_stat_statements
A [pg_stat_statements bővítmény](https://www.postgresql.org/docs/current/pgstatstatements.html) minden Azure Database for PostgreSQL-kiszolgálón előre be van töltve, így biztosíthatja az SQL-utasítások végrehajtási statisztikáinak nyomon követését.
A beállítás `pg_stat_statements.track` , amely azt szabályozza, hogy a bővítmény mely utasításokat számítja ki, az alapértelmezett érték `top` , ami azt jelenti, hogy az ügyfelek által közvetlenül kiadott összes utasítás nyomon van követve. A két másik követési szint a `none` és a `all` . Ez a beállítás kiszolgálói paraméterként konfigurálható a [Azure Portalon](./howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI](./howto-configure-server-parameters-using-cli.md)-n keresztül.

Az egyes SQL-utasítások beolvasása során kompromisszumot pg_stat_statements a lekérdezés végrehajtási információi, valamint a kiszolgáló teljesítményére gyakorolt hatás. Ha nem használja aktívan a pg_stat_statements bővítményt, javasoljuk, hogy állítsa a következőre: `pg_stat_statements.track` `none` . Vegye figyelembe, hogy egyes harmadik féltől származó figyelési szolgáltatások felhasználhatják a lekérdezési teljesítménnyel kapcsolatos megállapítások kézbesítésének pg_stat_statementsét, így meggyőződhet arról, hogy ez a helyzet-e az Ön számára.

## <a name="dblink-and-postgres_fdw"></a>dblink és postgres_fdw
a [dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) és a [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) lehetővé teszik, hogy az egyik PostgreSQL-kiszolgálóról egy másikra, vagy ugyanabban a kiszolgálón található másik adatbázishoz kapcsolódjon. A fogadó kiszolgálónak engedélyeznie kell a kapcsolódást a küldő kiszolgálóról a tűzfalon keresztül. Ha ezekkel a bővítményekkel csatlakozik Azure Database for PostgreSQL kiszolgálók között, ezt az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítással teheti meg. Erre akkor is szükség van, ha a bővítmények használatával szeretne visszakapcsolni ugyanarra a kiszolgálóra. Az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás megtalálhatók a postgres-kiszolgáló Azure Portal lapján, a kapcsolat biztonsága lehetőségnél. Az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás bekapcsolásával az összes Azure-beli IP-címet az engedélyezési listán helyezheti el.

A Azure Database for PostgreSQL kimenő kapcsolatai jelenleg nem támogatottak, kivéve az ugyanabban a régióban lévő más Azure Database for PostgreSQL kiszolgálókhoz való kapcsolódást.

## <a name="uuid"></a>uuid
Ha `uuid_generate_v4()` az [UUID-ossp bővítményből](https://www.postgresql.org/docs/current/uuid-ossp.html)tervezi használni a használatát, érdemes lehet összehasonlítást alkalmazni `gen_random_uuid()` a [pgcrypto-bővítménnyel](https://www.postgresql.org/docs/current/pgcrypto.html) a teljesítménnyel kapcsolatos előnyökhöz.

## <a name="pgaudit"></a>pgAudit
Az [pgAudit bővítmény](https://github.com/pgaudit/pgaudit/blob/master/README.md) munkamenet-és objektum-naplózási naplózást biztosít. Ha meg szeretné tudni, hogyan használhatja ezt a bővítményt Azure Database for PostgreSQLban, tekintse meg a [naplózási fogalmakat ismertető cikket](concepts-audit.md). 

## <a name="pg_prewarm"></a>pg_prewarm
A pg_prewarm-bővítmény a kapcsolatot a gyorsítótárba tölti be. A gyorsítótárak előmelegítése azt jelenti, hogy a lekérdezések az újraindítás után az első futtatáskor jobb válaszidőt rendelkeznek. A 10. és az alatti postgres az előmelegítés manuálisan történik az [előmelegítő függvény](https://www.postgresql.org/docs/10/pgprewarm.html)használatával.

A postgres 11 vagy újabb verziójában beállíthatja, hogy az előmelegítő [automatikusan](https://www.postgresql.org/docs/current/pgprewarm.html)történjen. Meg kell adnia pg_prewarm a `shared_preload_libraries` paraméter listájában, majd újra kell indítania a kiszolgálót a módosítás alkalmazásához. A paraméterek a [Azure Portal](howto-configure-server-parameters-using-portal.md), a [CLI](howto-configure-server-parameters-using-cli.md), az REST API vagy az ARM sablonból állíthatók be. 

## <a name="timescaledb"></a>TimescaleDB
A TimescaleDB egy idősorozat-adatbázis, amely a PostgreSQL-bővítményként van csomagolva. A TimescaleDB időalapú analitikai funkciókat, optimalizálásokat és skálázási postgres biztosít az idősoros számítási feladatokhoz.

[További információ a TimescaleDB-ről](https://docs.timescale.com/latest): az [időskála, Inc.](https://www.timescale.com/)bejegyzett védjegye. A Azure Database for PostgreSQL a TimescaleDB [Apache-2 kiadását](https://www.timescale.com/legal/licenses)biztosítja.

### <a name="installing-timescaledb"></a>A TimescaleDB telepítése
A TimescaleDB telepítéséhez fel kell vennie azt a kiszolgáló megosztott előtelepítő könyvtáraiba. A postgres `shared_preload_libraries` paraméterének módosítása a **kiszolgáló újraindítását** igényli. A paramétereket a [Azure Portal](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md)használatával módosíthatja.

A [Azure Portal](https://portal.azure.com/)használata:

1. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.

2. Az oldalsávon válassza a **kiszolgálói paraméterek** lehetőséget.

3. Keresse meg a `shared_preload_libraries` paramétert.

4. Válassza a **TimescaleDB** lehetőséget.

5. A módosítások megőrzése érdekében válassza a **Mentés** lehetőséget. A módosítás mentése után értesítést kap. 

6. Az értesítés után **indítsa újra** a kiszolgálót a módosítások alkalmazásához. A kiszolgálók újraindításával kapcsolatos további információkért lásd: [Azure Database for PostgreSQL kiszolgáló](howto-restart-server-portal.md)újraindítása.


Mostantól engedélyezheti a TimescaleDB a postgres-adatbázisban. Kapcsolódjon az adatbázishoz, és adja ki a következő parancsot:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Ha hibaüzenet jelenik meg, ellenőrizze, hogy a shared_preload_libraries mentése után [újraindította-e a kiszolgálót](howto-restart-server-portal.md) . 

Most már létrehozhat egy TimescaleDB-hypertable [a semmiből](https://docs.timescale.com/getting-started/creating-hypertables) , vagy áttelepítheti a [meglévő idősoros adatok a PostgreSQL-ben](https://docs.timescale.com/getting-started/migrating-data).

### <a name="restoring-a-timescale-database"></a>Időskála-adatbázis visszaállítása
Ha pg_dump és pg_restore használatával kívánja visszaállítani az időskála-adatbázist, a céladatbázis két segítő eljárását kell futtatnia: `timescaledb_pre_restore()` és `timescaledb_post restore()` .

Először készítse elő a céladatbázis-adatbázist:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

Most már futtathatja az pg_dump az eredeti adatbázison, majd elvégezheti a pg_restore. A visszaállítás után mindenképp futtassa a következő parancsot a visszaállított adatbázisban:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Következő lépések
Ha nem látja a használni kívánt bővítményt, tudassa velünk. Szavazzon a meglévő kérelmekre, vagy hozzon létre új visszajelzési kéréseket a [visszajelzési fórumban](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).