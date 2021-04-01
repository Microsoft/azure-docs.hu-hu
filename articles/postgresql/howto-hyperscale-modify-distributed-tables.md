---
title: Elosztott táblák módosítása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Elosztott táblák létrehozásához és módosításához szükséges SQL-parancsok – nagy kapacitású (Citus) a Azure Portal használatával
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: cf9f9ca5b8690a38c6e5aa6f519378c0a2e3a4f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026437"
---
# <a name="distribute-and-modify-tables"></a>Táblák elosztása és módosítása

## <a name="distributing-tables"></a>Táblák terjesztése

Elosztott tábla létrehozásához először meg kell határoznia a tábla sémáját. Ehhez megadhat egy táblázatot a [CREATE TABLE](http://www.postgresql.org/docs/current/static/sql-createtable.html) utasítással ugyanúgy, mint egy normál PostgreSQL-táblával.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

Ezután az \_ elosztott \_ tábla létrehozása () függvény használatával megadhatja a tábla terjesztési oszlopát, és létrehozhatja a feldolgozó szegmenseket.

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

A függvény meghívja a nagy kapacitású (Citus), hogy a GitHub \_ -események táblázatát el kell osztani a tárház \_ Azonosító oszlopában (az oszlop értékének kivonatolásával). A függvény létrehozza a munkavégző csomópontok szegmenseit is a citus. szegmensek \_ száma és a citus. szilánk \_ replikációs \_ faktor konfigurációs értékeinek használatával.

Létrehoz egy teljes citus. a szegmensek \_ számát, amelyben az egyes szegmensek egy kivonatoló terület egy részét birtokolják, és az alapértelmezett citus. szegmens \_ replikációs \_ faktor konfigurációs érték alapján replikálva lesznek. A feldolgozón létrehozott szegmens replikák ugyanazzal a tábla-sémával, indextel és megkötés-definíciókkal rendelkeznek, mint a koordinátor táblázata. A replikák létrehozása után a függvény az összes elosztott metaadatot menti a koordinátoron.

Minden létrehozott szegmenshez egyedi szegmens-azonosító van rendelve, és az összes replikája ugyanazzal a szegmens AZONOSÍTÓval rendelkezik. A szegmensek a Worker csomóponton, mint a táblanév shardid nevű normál PostgreSQL \' \_ -táblázatok \' , ahol a táblanév az elosztott tábla neve, a szegmens azonosítója pedig a hozzárendelt egyedi azonosító. A Worker postgres-példányokhoz kapcsolódhat, és megtekintheti és futtathatja az egyes szegmenseken található parancsokat.

Most már készen áll az elosztott táblába való beszúrásra és a lekérdezések futtatására. További információt a [táblázat és](reference-hyperscale-functions.md#table-and-shard-ddl) a szegmens DDL-hivatkozás ebben a szakaszban használt UDF-ről is talál.

### <a name="reference-tables"></a>Hivatkozási táblák

A fenti módszer több vízszintes szegmensbe osztja el a táblákat.  Egy másik lehetőség a táblák egyetlen szegmensbe terjesztése és a szegmens replikálása minden munkavégző csomópontra. Az ily módon elosztott táblákat *hivatkozási tábláknak nevezzük.* Ezeket a fájlokat a fürt több csomópontja által gyakran elérhetővé tett adattárolásra használják.

A hivatkozási táblákhoz tartozó általános jelöltek a következők:

-   Kisebb táblák, amelyeknek nagyobb elosztott táblákkal kell csatlakozniuk.
-   Olyan több-bérlős alkalmazásokban lévő táblák, amelyek nem rendelkeznek bérlői azonosító oszloppal, vagy amelyek nem a \' bérlőhöz vannak társítva. (Vagy az áttelepítés során még a bérlőhöz társított egyes táblák esetében is.)
-   A több oszlopra kiterjedő egyedi korlátozásokat igénylő táblák, amelyek elég kis méretűek.

Tegyük fel például, hogy egy több-bérlős e-kereskedelmi webhelynek eladási adót kell kiszámítania a tranzakciók bármelyik tárolóban való kiszámításához. A bérlőre vonatkozó adózási információk nem megfelelőek \' . Logikus, ha egy megosztott táblába helyezi. Az USA-központú hivatkozási táblázat a következőképpen néz ki:

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

Az olyan lekérdezések, mint például egy bevásárlókocsi kiszámítási adója `states` , hálózati terhelés nélkül csatlakozhatnak a táblához, és a jobb ellenőrzés érdekében hozzáadhat egy külső kulcsot az állapot kódjához is.

Egy tábla egyetlen replikált szegmensként való terjesztése mellett az `create_reference_table` UDF hivatkozási táblázatként jelöli meg a nagy kapacitású (Citus) metaadat-táblázatokban. A nagy kapacitású (Citus) automatikusan végrehajtja a kétfázisú végrehajtást ([2PC](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)) az így megjelenő táblázatok módosításaihoz, ami erős konzisztencia-garanciákat biztosít.

Ha van egy elosztott tábla, amelynek a szegmensei száma egy, akkor frissítheti, hogy az a következőhöz hasonló felismert hivatkozási táblázat legyen:

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

További példa a hivatkozási táblázatok használatára: [több-bérlős adatbázis-oktatóanyag](tutorial-design-database-hyperscale-multi-tenant.md).

### <a name="distributing-coordinator-data"></a>Koordinátori adat terjesztése

Ha egy meglévő PostgreSQL-adatbázist egy nagy kapacitású-(Citus-) fürt koordinátori csomópontjára alakít át, a tábláiban lévő adat hatékonyan terjeszthető, és az alkalmazás minimális megszakításával is elvégezhető.

A `create_distributed_table` korábban leírt függvény üres és nem üres táblákon is működik, és az utóbbi automatikusan elosztja a táblázat sorait a fürtben. Tudni fogja, hogy az üzenet jelenléte alapján másolja-e az adatait, és figyelje meg, hogy az \" adatok másolása a helyi táblából történik \. . \" Például:

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

A táblázatba írt írások le vannak tiltva az adatáttelepítés során, és a függőben lévő írásokat elosztott lekérdezésként kezeli a függvény véglegesítve. (Ha a függvény meghibásodik, akkor a lekérdezések ismét helyivé válnak.) Az olvasások a szokásos módon folytatódnak, és a függvény elkövetése után elosztott lekérdezésként lesznek elérhetők.

Az A és B tábla terjesztésekor, ahol A "B" külső kulccsal rendelkezik, a fő célként megadott "B" táblázatot kell kiosztania. Ha nem megfelelő sorrendben hajtja végre, a következő hibaüzenetet eredményezi:

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

Ha nem lehet a megfelelő sorrendben terjeszteni, dobja el a külső kulcsokat, Ossza szét a táblákat, majd hozza létre újra a külső kulcsokat.

Az adatok külső adatbázisból történő áttelepítésekor, például az Amazon RDS-ről a nagy kapacitású (Citus) felhőbe való áttelepítéskor először hozza létre a nagy kapacitású (Citus) elosztott táblákat a-n keresztül `create_distributed_table` , majd másolja az adatait a táblába.
Az elosztott táblákba való másolás elkerüli a kifogyott területet a koordinátor csomóponton.

## <a name="colocating-tables"></a>Táblázatok elhelyezése

A közös elhelyezés azt jelenti, hogy a kapcsolódó információkat ugyanarra a gépre helyezi. Hatékony lekérdezéseket tesz lehetővé, miközben kihasználja a teljes adatkészlet horizontális méretezhetőségét. További információ: az [egyhelyes elhelyezés](concepts-hyperscale-colocation.md).

A táblák a csoportokban találhatók. A tábla együttes elhelyezési csoportjának hozzárendelésének manuális vezérléséhez használja a választható `colocate_with` paraméterét `create_distributed_table` . Ha nem szeretné, hogy \' egy tábla egy adott helyére kerüljön, \' akkor hagyja ki ezt a paramétert. Alapértelmezés szerint a (z `'default'` ) érték, amely a tábla bármely más alapértelmezett, egymást megosztó oszlopával, a szegmensek számával és a replikációs tényezővel együtt csoportosítja a táblát. Ha meg szeretné szüntetni vagy frissíteni kívánja ezt az implicit helyet, használhatja a következőt: `update_distributed_table_colocation()` .

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

Ha egy új tábla nem kapcsolódik másokhoz, akkor a következőt kell megadnia: `colocated_with => 'none'` .

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

A nem kapcsolódó tábláknak a saját közös elhelyezésű csoportjaiba való felosztása növeli a szegmensek közötti [újrakiegyensúlyozási](howto-hyperscale-scale-rebalance.md) teljesítményt, mert az ugyanabban a csoportban lévő szegmenseket együtt kell áthelyezni.

Ha a táblák valóban kapcsolódnak egymáshoz (például amikor csatlakozni fognak), érdemes lehet explicit módon megkeresni őket. A megfelelő elhelyezési költségek sokkal fontosabbak, mint bármely más terheléselosztás.

Ha explicit módon több táblát szeretne elhelyezni, terjesszen egyet, majd helyezze el a többiet a közös elhelyezési csoportba. Például:

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

Az együttes elhelyezési csoportokra vonatkozó információkat a [pg_dist_colocation](reference-hyperscale-metadata.md#colocation-group-table) táblában tárolja a rendszer, míg [pg_dist_partition](reference-hyperscale-metadata.md#partition-table) felfedi, hogy mely táblák vannak hozzárendelve a csoporthoz.

## <a name="dropping-tables"></a>Táblák eldobása

A standard PostgreSQL DROP TABLE parancs használatával eltávolíthatja az elosztott táblákat. A normál táblákhoz hasonlóan a DROP TABLE eltávolítja a céltábla összes indexét, szabályát, eseményindítóját és megkötését. Emellett elveszi a szegmenseket a munkavégző csomópontokon is, és megtisztítja a metaadatokat.

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>Táblák módosítása

A nagy kapacitású (Citus) automatikusan számos típusú DDL-utasítást propagál.
Egy elosztott tábla a koordinátor csomóponton való módosítása a munkaterheléseket is frissíti. Más DDL-utasításokhoz manuális propagálásra van szükség, és bizonyos mások nem engedélyezettek, például olyanok, amelyek egy terjesztési oszlopot módosítanak.
Ha olyan DDL-t próbál futtatni, amely nem jogosult az automatikus propagálásra, a rendszer hibát jelez, és a koordinátor csomóponton lévő táblákat változatlanul hagyja.

A következő hivatkozás a propagált DDL-utasítások kategóriáit sorolja fel.
Az automatikus propagálás engedélyezhető vagy letiltható egy [konfigurációs paraméterrel](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean)

### <a name="addingmodifying-columns"></a>Oszlopok hozzáadása/módosítása

A nagy kapacitású (Citus) a legtöbb [ALTER TABLE](https://www.postgresql.org/docs/current/static/ddl-alter.html) parancsot automatikusan propagálja. Oszlopok hozzáadása vagy az alapértelmezett értékek módosítása ugyanúgy működnek, mint egy gép PostgreSQL-adatbázisban:

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

Egy meglévő oszlop jelentős módosításai, például az átnevezése vagy az adattípus módosítása is megfelelő. A [terjesztési oszlop](concepts-hyperscale-nodes.md#distribution-column) adattípusa azonban nem módosítható.
Ez az oszlop határozza meg, hogyan osztja el a tábla az nagy kapacitású (Citus) fürtjét, és hogy az adattípusának módosítása szükségessé teszi az adatáthelyezést.

A művelet a következő hiba miatt próbálkozik:

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>Megkötések hozzáadása/eltávolítása

A nagy kapacitású (Citus) használatával továbbra is használhatja a kapcsolódó adatbázisok biztonságát, beleértve az adatbázisra vonatkozó korlátozásokat is (lásd a PostgreSQL- [dokumentumokat](https://www.postgresql.org/docs/current/static/ddl-constraints.html)).
Az elosztott rendszerek jellegéből adódóan a nagy kapacitású (Citus) nem hivatkozik a munkavégző csomópontok közötti egyediségi korlátozásokra vagy hivatkozási integritásra.

Ha a külső kulcsot a közös elhelyezésű elosztott táblák között szeretné beállítani, mindig adja meg a terjesztési oszlopot a kulcsban. A terjesztési oszlop belefoglalása magában foglalhatja a kulcs összetett kiosztását.

A külső kulcsok a következő helyzetekben hozhatók létre:

-   két helyi (nem elosztott) tábla között
-   két hivatkozási táblázat között
-   két közösen [elhelyezett](concepts-hyperscale-colocation.md) elosztott tábla között, ha a kulcs tartalmazza a terjesztési oszlopot, vagy
-   egy [hivatkozási táblára](concepts-hyperscale-nodes.md#type-2-reference-tables) hivatkozó elosztott tábla

Nem támogatottak a külső kulcsok a hivatkozási táblákból az elosztott táblákba.

> [!NOTE]
>
> Az elsődleges kulcsoknak és az egyediségi megkötéseknek tartalmazniuk kell a terjesztési oszlopot. Ha nem terjesztési oszlophoz adja őket, hibaüzenetet fog eredményezni.

Ebből a példából megtudhatja, hogyan hozhat létre elsődleges és külső kulcsokat az elosztott táblákon:

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

Hasonlóképpen adja meg a terjesztési oszlopot az egyediségi megkötésekben:

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

A nem null értékű megkötések bármely oszlopra (eloszlásra vagy nem) alkalmazhatók, mert nem igénylik a feldolgozók közötti keresést.

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>NEM érvényes megkötések használata

Bizonyos helyzetekben hasznos lehet kényszeríteni az új sorokra vonatkozó korlátozásokat, miközben a meglévő nem megfelelő sorok változatlanok maradnak. A nagy kapacitású (Citus) támogatja ezt a funkciót az ellenőrzési megkötések és a külső kulcsok esetében, a PostgreSQL- \' t \" nem érvényes \" korlátozási megjelöléssel.

Vegyünk például egy olyan alkalmazást, amely egy [hivatkozási táblában](concepts-hyperscale-nodes.md#type-2-reference-tables)tárolja a felhasználói profilokat.

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

Az idő során Képzelje el, hogy néhány nem cím kerül bele a táblázatba.

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

Szeretnénk érvényesíteni a címeket, de a PostgreSQL általában nem teszi lehetővé, hogy olyan ellenőrző korlátozást adjon hozzá, amely nem felel meg a meglévő soroknak. Ez azonban *lehetővé teszi, hogy a* korlátozás érvénytelenként legyen megjelölve:

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

Az új sorok védelme már megtörtént.

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

Később, a nem csúcsidőben töltött órákban az adatbázis rendszergazdája megpróbálhatja kijavítani a hibás sorokat, és újraérvényesíteni a megkötést.

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

A PostgreSQL dokumentációja további információkat tartalmaz a nem érvényes és ÉRVÉNYESÍTett KORLÁTOZÁSokról az [ALTER TABLE](https://www.postgresql.org/docs/current/sql-altertable.html) szakaszban.

### <a name="addingremoving-indices"></a>Indexek hozzáadása/eltávolítása

A nagy kapacitású (Citus) támogatja az [indexek](https://www.postgresql.org/docs/current/static/sql-createindex.html)hozzáadását és eltávolítását:

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

Az index hozzáadása írási zárolást tesz lehetővé, amely a több-bérlős \" rendszer rekordjában nemkívánatos lehet. \" Az alkalmazások leállásának csökkentése érdekében az indexet [egyidejűleg](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY) hozza létre. Ennél a módszernél több teljes munka szükséges, mint a szabványos indexek létrehozása, és a folyamat hosszabb időt vesz igénybe. Mivel azonban lehetővé teszi a normál műveletek folytatását az index létrehozásakor, ez a módszer hasznos az új indexek éles környezetben való hozzáadásához.

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```
