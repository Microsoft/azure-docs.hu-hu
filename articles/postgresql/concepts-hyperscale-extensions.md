---
title: Extensions – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Ismerteti az adatbázis funkcióinak kiterjesztését Azure Database for PostgreSQL-nagy kapacitású (Citus) bővítmények használatával
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 000f8a1457298901dcfc94bc5e0923e94ba35dc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620902"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL-bővítmények Azure Database for PostgreSQLban – nagy kapacitású (Citus)

A PostgreSQL lehetővé teszi az adatbázis funkcióinak kiterjesztését bővítmények használatával. A bővítmények lehetővé teszik több kapcsolódó SQL-objektum összefoglalását egyetlen csomagban, amelyet egyetlen paranccsal lehet betölteni vagy eltávolítani az adatbázisból. Az adatbázisba való betöltés után a bővítmények ugyanúgy működhetnek, mint a beépített funkciók. A PostgreSQL-bővítményekkel kapcsolatos további információkért lásd: [kapcsolódó objektumok kicsomagolása bővítménybe](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>PostgreSQL-bővítmények használata

A PostgreSQL-bővítményeket a használatuk előtt telepíteni kell az adatbázisba. Egy adott bővítmény telepítéséhez futtassa a [bővítmény létrehozása](https://www.postgresql.org/docs/current/static/sql-createextension.html) parancsot a psql eszközből a csomagolt objektumok adatbázisba való betöltéséhez.

A Azure Database for PostgreSQL-nagy kapacitású (Citus) jelenleg az itt felsorolt kulcs-kiterjesztések egy részhalmazát támogatja. A felsorolt bővítmények nem támogatottak. A Azure Database for PostgreSQL nem hozhat létre saját bővítményt.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>A Azure Database for PostgreSQL által támogatott bővítmények

A következő táblázat a Azure Database for PostgreSQL által jelenleg támogatott szabványos PostgreSQL-bővítményeket sorolja fel. Ezek az információk a futtatásával is elérhetők `SELECT * FROM pg_available_extensions;` .

### <a name="data-types-extensions"></a>Adattípusok bővítményei

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Kis-és nagybetűket nem megkülönböztető karakterlánc-típust biztosít. |
> | [adatkocka](https://www.postgresql.org/docs/current/static/cube.html) | Adattípust biztosít a többdimenziós kockákhoz. |
> | [HLL](https://github.com/citusdata/postgresql-hll) | HyperLogLog adatstruktúrát biztosít. |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Adattípust biztosít a kulcs-érték párok készletének tárolására. |
> | [helytelen átvitt](https://www.postgresql.org/docs/current/static/isn.html) | Adattípusokat biztosít a nemzetközi termékek számozási szabványainak. |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | Nagyméretű objektumok karbantartása. |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Adattípust biztosít a hierarchikus fastruktúrához hasonló struktúrákhoz. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | A vonalszakasz vagy a lebegőpontos intervallumok ábrázolására szolgáló adattípus. |
> | [tdigest](https://github.com/tvondra/tdigest) | Adattípus a rangsorolt statisztikai adatok, például a quantiles és a nyírt állapotok online összegyűjtéséhez. |
> | [legjobb n](https://github.com/citusdata/postgresql-topn/) | Adja meg a Top-n JSONB. |

### <a name="full-text-search-extensions"></a>Teljes szöveges keresési bővítmények

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [dict \_ int](https://www.postgresql.org/docs/current/static/dict-int.html) | Szöveges keresési szótári sablont biztosít az egész számokhoz. |
> | [dict \_ xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Szöveges keresési szótár sablon a kiterjesztett szinonimák feldolgozásához. |
> | [nem ékezetes](https://www.postgresql.org/docs/current/static/unaccent.html) | Szöveges keresési szótár, amely eltávolítja az ékezeteket (mellékjelek jeleit) a lexemes. |

### <a name="functions-extensions"></a>Functions-bővítmények

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Függvények az automatikusan növekvő mezőkhöz. |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Lehetővé teszi a nagy kör alakú távolságok kiszámítását a Föld felszínén. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Számos függvényt biztosít a karakterláncok közötti hasonlóságok és távolságok meghatározásához. |
> | [\_Felhasználónév beszúrása](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | A táblákat módosító függvények. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Egész számú gyűjtő és enumerálás (elavult). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Függvényeket és operátorokat biztosít az egész számok null nélküli tömbbe való manipulálására. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Függvények a legutóbbi módosítási idő nyomon követéséhez. |
> | [PG \_ parti](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | A particionált táblákat idő vagy azonosító alapján kezeli. |
> | [PG \_ trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | A függvényeket és operátorokat biztosít az alfanumerikus szöveg hasonlóságának meghatározásához a trigram megfeleltetése alapján. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Titkosítási funkciókat biztosít. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | A hivatkozási integritás megvalósítására szolgáló függvények (elavult). |
> | munkamenet- \_ elemzés | Függvények a hstore-tömbök lekérdezéséhez. |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Olyan függvényeket biztosít, amelyek a teljes táblákat, például a kereszttáblás funkciókat kezelik. |
> | [TCN](https://www.postgresql.org/docs/current/tcn.html) | Aktivált változási értesítések. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Az időutazás megvalósításához szükséges függvények. |
> | [UUID – ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Univerzálisan egyedi azonosítókat (UUID-ket) generál. |

### <a name="hyperscale-citus-extensions"></a>Nagy kapacitású (Citus) bővítmények

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus elosztott adatbázis. |

### <a name="index-types-extensions"></a>Index típusú bővítmények

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [Bloom](https://www.postgresql.org/docs/current/bloom.html) | A Bloom hozzáférési módszere – aláírási fájl alapú index. |
> | [fa \_ gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Olyan minta GIN operátori osztályokat biztosít, amelyek bizonyos adattípusok esetén B-fa viselkedést implementálnak. |
> | [fa \_ lényege](https://www.postgresql.org/docs/current/static/btree-gist.html) | A "B" fát implementáló lényegi index operátori osztályokat biztosít. |

### <a name="language-extensions"></a>Nyelvi bővítmények

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL betölthető eljárási nyelv. |

### <a name="miscellaneous-extensions"></a>Egyéb bővítmények

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [AdminPack](https://www.postgresql.org/docs/current/adminpack.html) | A PostgreSQL-hez készült felügyeleti függvények. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Függvények a kapcsolatok integritásának ellenőrzéséhez. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Egy modul, amely támogatja a más PostgreSQL-adatbázisokhoz való kapcsolódást egy adatbázis-munkameneten belülről. A bővítménysel kapcsolatos információkért tekintse meg a "dblink és postgres_fdw" szakaszt. |
> | [fájl \_ FDW](https://www.postgresql.org/docs/current/file-fdw.html) | Idegen adatburkoló a lapos fájlokhoz való hozzáféréshez. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Vizsgálja meg az adatbázis-lapok tartalmát alacsony szinten. |
> | [PG \_ buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | A lehetővé teszi a megosztott puffer gyorsítótárában zajló események valós idejű vizsgálatát. |
> | [PG \_ cron](https://github.com/citusdata/pg_cron) | Feladatütemező a PostgreSQL-hez. |
> | [PG \_ freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Vizsgálja meg a szabad terület leképezését (MSZÁ). |
> | [PG \_ előmelegítve](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Lehetővé teszi a kapcsolatok betöltését a puffer gyorsítótárába. |
> | [PG \_ stat- \_ utasítások](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | A kiszolgáló által végrehajtott összes SQL-utasítás végrehajtási statisztikáinak nyomon követését teszi lehetővé. A bővítménysel kapcsolatos információkért tekintse meg a "pg_stat_statements" szakaszt. |
> | [PG \_ láthatósága](https://www.postgresql.org/docs/current/pgvisibility.html) | Vizsgálja meg a láthatósági térképet (VM) és az oldal szintű láthatósági információkat. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Lehetővé teszi a sor szintű zárolási információk megjelenítését. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | A rekord szintű statisztikák megjelenítését teszi lehetővé. |
> | [postgres \_ FDW](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | A külső PostgreSQL-kiszolgálókon tárolt adatforrásokhoz való hozzáféréshez használt idegen adatburkoló. A bővítménysel kapcsolatos információkért tekintse meg a "dblink és postgres_fdw" szakaszt.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | A TLS/SSL-tanúsítványokkal kapcsolatos információk. |
> | [TSM \_ \_ rendszersorai](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE metódus, amely a sorok számát korlátként fogadja el. |
> | [TSM \_ - \_ rendszeridő](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE metódus, amely a korlátnak megfelelő időt fogad el ezredmásodpercben. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath-lekérdezés és XSLT. |


### <a name="postgis-extensions"></a>PostGIS-bővítmények

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), PostGIS \_ topológia, PostGIS \_ Tiger \_ geocoder, PostGIS \_ sfcgal | A PostgreSQL térbeli és földrajzi objektumai. |
> | \_a címek szabványosítása, a \_ szabványosítási adatkezelési \_ szolgáltatás \_ | A címek összetevőire való elemzéséhez használatos. A helymeghatározáshoz-címek normalizálása lépésének támogatásához használatos. |
> | PostGIS \_ sfcgal | PostGIS SFCGAL függvények. |
> | PostGIS \_ Tiger \_ geocoder | PostGIS Tiger geocoder és fordított geocoder. |
> | PostGIS- \_ topológia | PostGIS-topológia térbeli típusai és funkciói |


## <a name="pg_stat_statements"></a>pg_stat_statements
A [PG \_ stat \_ utasítások bővítmény](https://www.postgresql.org/docs/current/pgstatstatements.html) minden Azure Database for PostgreSQL kiszolgálón előre be van töltve, így biztosítva az SQL-utasítások végrehajtási statisztikáinak nyomon követését.

A beállítás határozza meg, hogy `pg_stat_statements.track` a bővítmény milyen utasításokat számoljon. Alapértelmezés szerint az `top` , ami azt jelenti, hogy az ügyfelek által közvetlenül kiadott összes utasítás nyomon van követve. A két másik követési szint a `none` és a `all` . Ez a beállítás kiszolgálói paraméterként konfigurálható a [Azure Portalon](./howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI](./howto-configure-server-parameters-using-cli.md)-n keresztül.

A lekérdezés végrehajtási információi pg_stat_statements biztosít, valamint a kiszolgáló teljesítményére gyakorolt hatás, ahogy az egyes SQL-utasítások bejelentkezik. Ha nem használja aktívan a pg_stat_statements bővítményt, javasoljuk, hogy állítsa a következőre: `pg_stat_statements.track` `none` . Előfordulhat, hogy egyes harmadik féltől származó figyelési szolgáltatások a lekérdezési teljesítmény megállapítására támaszkodnak pg_stat_statements, ezért ellenőrizze, hogy ez a helyzet-e az Ön számára.

## <a name="dblink-and-postgres_fdw"></a>dblink és postgres_fdw

A dblink és \_ a postgres FDW használatával csatlakozhat egy PostgreSQL-kiszolgálóról egy másikra, vagy egy kiszolgálón található másik adatbázisba.  A fogadó kiszolgálónak engedélyeznie kell a kapcsolódást a küldő kiszolgálóról a tűzfalon keresztül.  Ha ezeket a bővítményeket Azure Database for PostgreSQL kiszolgálók vagy nagy kapacitású (Citus) kiszolgálócsoportok közötti kapcsolathoz szeretné használni, állítsa be az **Azure-szolgáltatások és-erőforrások engedélyezése lehetőséget a kiszolgálócsoport (vagy kiszolgáló) számára való hozzáféréshez** .  Ezt a beállítást akkor is be kell kapcsolni, ha a bővítményekkel vissza kíván térni ugyanarra a kiszolgálóra.
Az **Azure-szolgáltatások és-erőforrások hozzáférésének engedélyezése ehhez a kiszolgálócsoport** -beállításhoz a Azure Portal oldalon található a nagy kapacitású (Citus) kiszolgálócsoport a **hálózat** területen.  Jelenleg Azure Database for PostgreSQL önálló kiszolgálóról és nagy kapacitású (Citus) származó kimenő kapcsolatok nem támogatottak, kivéve a más Azure Database for PostgreSQL kiszolgálókhoz és nagy kapacitású (Citus) kiszolgálói csoportokhoz való kapcsolódást.