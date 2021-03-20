---
title: Logikai replikáció és logikai dekódolás – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Tudnivalók a logikai replikáció és a logikai dekódolás Azure Database for PostgreSQL rugalmas kiszolgálón való használatáról
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b6689220873aaeb65337ba480e346e5d2c8020ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91707863"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Logikai replikáció és a logikai dekódolás Azure Database for PostgreSQL rugalmas kiszolgálón

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

A PostgreSQL logikai replikálása és a logikai dekódolási funkciók Azure Database for PostgreSQL rugalmas kiszolgálókon, a postgres 11-es verziójában támogatottak.

## <a name="comparing-logical-replication-and-logical-decoding"></a>A logikai replikáció és a logikai dekódolás összehasonlítása
A logikai replikáció és a logikai dekódolás több hasonlóságot is tartalmaz. Mindkettő
* az postgres-ből való replikálás engedélyezése
* a [Write-Ahead log (Wal)](https://www.postgresql.org/docs/current/wal.html) használata a változások forrásaként
* az adatküldés [logikai replikációs tárolóhelyek](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) használatával. A tárolóhelyek a változások streamjét jelölik.
* a táblázat [REPLIKA identitás tulajdonságával](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY) meghatározhatja, hogy milyen módosításokat lehet elküldeni
* DDL-módosítások replikálásának mellőzése


A két technológia eltérései: logikai replikáció 
* lehetővé teszi a replikálni kívánt táblák vagy táblázatok megadását
* az adatreplikálása PostgreSQL-példányok között

Logikai dekódolás 
* a változások kibontása az adatbázisban lévő összes táblában 
* nem küldhető el közvetlenül a PostgreSQL-példányok között


## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>A logikai replikációhoz és a logikai dekódoláshoz szükséges előfeltételek

1. Állítsa a Server paramétert a következőre: `wal_level` `logical` .
2. A módosítás alkalmazásához indítsa újra a kiszolgálót `wal_level` .
3. Győződjön meg arról, hogy a PostgreSQL-példány engedélyezi a hálózati forgalmat a kapcsolódó erőforrásból.
4. Adja meg a rendszergazdai felhasználó replikációs engedélyeit.
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```


## <a name="using-logical-replication-and-logical-decoding"></a>Logikai replikáció és a logikai dekódolás használata

### <a name="logical-replication"></a>Logikai replikáció
A logikai replikáció a "kiadó" és az "előfizető" kifejezést használja. 
* A közzétevő a PostgreSQL **-** adatbázis, amelyről adatokat küld. 
* Az előfizető az a PostgreSQL **-** adatbázis, amelyhez adatokat küld.

Íme egy példa a logikai replikáció kipróbálására.

1. Kapcsolódjon a közzétevői adatbázishoz. Hozzon létre egy táblázatot, és adjon hozzá néhányat.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. Hozzon létre egy kiadványt a táblához.
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. Kapcsolódjon az előfizetői adatbázishoz. Hozzon létre egy táblázatot ugyanazzal a sémával, mint a közzétevőn.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. Hozzon létre egy előfizetést, amely csatlakozni fog a korábban létrehozott kiadványhoz.
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. Most már lekérdezheti a táblát az előfizetőn. Látni fogja, hogy a közzétevőtől kapott adatok.
   ```SQL
   SELECT * FROM basic;
   ```

Több sort is hozzáadhat a közzétevő táblához, és megtekintheti az előfizető módosításait.

A [logikai replikációval](https://www.postgresql.org/docs/current/logical-replication.html)kapcsolatos további információkért tekintse meg a PostgreSQL dokumentációját.

### <a name="logical-decoding"></a>Logikai dekódolás
A logikai dekódolást a Streaming Protocol vagy az SQL Interface használatával lehet használni. 

#### <a name="streaming-protocol"></a>Streaming Protocol
A változások a Streaming Protocol használatával való felhasználása gyakran előnyösebb. Létrehozhat saját fogyasztót vagy összekötőt, vagy használhat külső szolgáltatást (például [Debezium](https://debezium.io/)) is. 

Tekintse meg a wal2json dokumentációját, amely [a streaming protocol pg_recvlogical használatával történő használatát szemlélteti](https://github.com/eulerto/wal2json#pg_recvlogical).

#### <a name="sql-interface"></a>SQL-felület 
Az alábbi példában az SQL-felületet használjuk a wal2json beépülő modullal.
 
1. Hozzon létre egy tárolóhelyet.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL-parancsok kiadása. Például:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Használja fel a módosításokat.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   A kimenet a következőképpen fog kinézni:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Ha elkészült, dobja el a tárolóhelyet.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

Tekintse meg a PostgreSQL-dokumentációt, és Ismerje meg a [logikai dekódolást](https://www.postgresql.org/docs/current/logicaldecoding.html).


## <a name="monitoring"></a>Figyelés
Figyelnie kell a logikai dekódolást. A fel nem használt replikációs tárolóhelyeket el kell dobni. A bővítőhelyek betartva postgres a WAL-naplókat és a kapcsolódó rendszerkatalógusokat, amíg a módosítások nem lettek beolvasva. Ha az előfizető vagy a fogyasztó meghibásodik, vagy nem lett megfelelően konfigurálva, a nem felhasználható naplók felhalmoznak és feltöltik a tárhelyet. A nem felhasználatlan naplók is növelhetik a tranzakció-azonosító wraparound kockázatát. Mindkét esetben előfordulhat, hogy a kiszolgáló elérhetetlenné válik. Ezért fontos, hogy a logikai replikációs tárolóhelyek folyamatosan legyenek felhasználva. Ha már nincs használatban logikai replikációs tárolóhely, azonnal dobja el.

A pg_replication_slots nézet "aktív" oszlopa jelzi, hogy van-e egy fogyasztó csatlakoztatva egy tárolóhelyhez.
```SQL
SELECT * FROM pg_replication_slots;
```

[Állítsa be a riasztásokat](howto-alert-on-metrics.md) a **maximálisan használt tranzakciós azonosítók** és a **tárolók** számára, és használja a rugalmas kiszolgálói metrikákat, hogy értesítést kapjon, ha az értékek a korábbi normál küszöbértékeket 

## <a name="limitations"></a>Korlátozások
* **Olvasási replikák** – Azure Database for PostgreSQL az olvasási replikák jelenleg nem támogatottak a rugalmas kiszolgálók esetében.
* Az elsődleges kiszolgálón a **tárolóhelyek és a ha feladatátvételi** -logikai replikációs tárolóhelyek nem érhetők el a másodlagos az által biztosított készenléti kiszolgálón. Ez arra az esetre vonatkozik, ha a kiszolgáló a zóna – redundáns magas rendelkezésre állás lehetőséget használja. A készenléti kiszolgálóra történő feladatátvétel esetén a logikai replikációs tárolóhelyek nem lesznek elérhetők készenléti állapotban.

## <a name="next-steps"></a>Következő lépések
* További információ a [hálózatkezelési lehetőségekről](concepts-networking.md)
* A rugalmas kiszolgálókon elérhető [bővítmények](concepts-extensions.md) ismertetése
* További információ a [magas rendelkezésre állásról](concepts-high-availability.md)

