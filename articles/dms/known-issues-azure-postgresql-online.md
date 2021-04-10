---
title: 'Ismert problémák: a PostgreSQL-ből való online áttelepítés Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Ismerje meg a PostgreSQL-ről online áttelepítéssel kapcsolatos ismert problémákat és áttelepítési korlátozásokat a Azure Database Migration Service használatával Azure Database for PostgreSQL.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: fdefcabdda64402610f115832976ec9f7af81b80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258829"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Ismert problémák/áttelepítési korlátozások a PostgreSQL-ből származó online áttelepítéssel a PostgreSQL-hez készült Azure DB-be

A PostgreSQL-ről Azure Database for PostgreSQLre való online áttelepítéssel kapcsolatos ismert problémák és korlátozások a következő szakaszokban olvashatók.

## <a name="online-migration-configuration"></a>Online áttelepítési konfiguráció

- A forrás PostgreSQL-kiszolgálónak a 9,4, 9,5, 9,6, 10 vagy 11 verziót kell futtatnia. További információkért lásd a [PostgreSQL-adatbázisok támogatott verzióit](../postgresql/concepts-supported-versions.md)ismertető cikket.
- Csak az azonos vagy újabb verzióra történő áttelepítések támogatottak. Ha például a PostgreSQL 9,5-et Azure Database for PostgreSQL 9,6-re vagy 10-re szeretné áttelepíteni, de a PostgreSQL 11-es verzióról a PostgreSQL 9,6-re való Migrálás nem támogatott.
- A logikai replikáció engedélyezéséhez a **forrás PostgreSQL PostgreSQL. conf** fájlban adja meg a következő paramétereket:
  - **wal_level** = logikai
  - **max_replication_slots** = [legalább az adatbázisok maximális száma az áttelepítéshez]; Ha négy adatbázist szeretne áttelepíteni, állítsa az értéket legalább 4 értékre.
  - **max_wal_senders** = [egyidejűleg futó adatbázisok száma]; a javasolt érték 10
- DMS-ügynök IP-címének hozzáadása a forrás PostgreSQL pg_hba. conf
  1. A Azure Database Migration Service-példány üzembe helyezésének befejezése után jegyezze fel a DMS IP-címét.
  2. Adja hozzá az IP-címet a pg_hba. conf fájlhoz az alábbiak szerint:

      ```
          host    all    172.16.136.18/10    md5
          host    replication postgres    172.16.136.18/10     md5
      ```

- A felhasználónak rendelkeznie kell a replikációs szerepkörrel a forrás-adatbázist üzemeltető kiszolgálón.
- A forrás-és a célként megadott adatbázis-sémáknak egyezniük kell.
- A PostgreSQL-Single-kiszolgáló cél Azure-adatbázisában lévő séma nem rendelkezhet idegen kulcsokkal. A külső kulcsok eldobásához használja az alábbi lekérdezést:

    ```
                  SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Futtassa a ’drop foreign key’-t (ez a második oszlop) a lekérdezési eredményben.

- A cél Azure-adatbázis PostgreSQL-Single-kiszolgáló sémája nem rendelkezhet eseményindítókkal. A következő paranccsal tilthatja le az eseményindítókat a célként megadott adatbázisban:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="size-limitations"></a>Méretre vonatkozó korlátozások
- A PostgreSQL-ből akár 2 TB-nyi adat is áttelepíthető egyetlen DMS-szolgáltatás használatával.
## <a name="datatype-limitations"></a>Adattípus-korlátozások

  **Korlátozás**: Ha a táblák nem rendelkeznek elsődleges kulccsal, akkor előfordulhat, hogy a módosítások nem szinkronizálhatók a céladatbázis használatával.

  **Megkerülő megoldás**: átmenetileg állítsa be a tábla elsődleges kulcsát az áttelepítéshez a folytatáshoz. Az elsődleges kulcsot az adatáttelepítés befejeződése után is eltávolíthatja.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Az AWS RDS PostgreSQL-ről történő online áttelepítés korlátozásai

Ha az AWS RDS PostgreSQL-ről a Azure Database for PostgreSQLra próbál online áttelepítést végezni, a következő hibák merülhetnek fel.

- **Hiba**: A(z) „{database}” adatbázisban található „{table}” tábla „{column}” oszlopának alapértelmezett értéke eltérő a forrás- és a célkiszolgálón. Az érték „{value on source}” a forráson és „{value on target}” a célon.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha egy oszlop sémájának alapértelmezett értéke eltér a forrás-és a cél-adatbázisok között.
  **Áthidaló megoldás**: Győződjön meg arról, hogy a célként megadott séma megfelel a forrás sémájának. A séma áttelepítésével kapcsolatos részletekért tekintse meg az [Azure PostgreSQL Online áttelepítési dokumentációját](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Hiba**: A(z) „{database}” céladatbázis „{number of tables}” táblával rendelkezik, a(z) „{database}” forrásadatbázisban azonban „{number of tables}” tábla található. A forrás- és a céladatbázisok táblái számának azonosnak kell lennie.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha a táblák száma eltér a forrás-és a cél-adatbázis között.

  **Áthidaló megoldás**: Győződjön meg arról, hogy a célként megadott séma megfelel a forrás sémájának. A séma áttelepítésével kapcsolatos részletekért tekintse meg az [Azure PostgreSQL Online áttelepítési dokumentációját](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Hiba:** A forrás-adatbázis ({Database}) üres.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha a forrásadatbázis üres. Valószínűleg azért, mert a megfelelő adatbázist választotta forrásként.

  **Áthidaló megoldás**: Ellenőrizze az áttelepítéshez kiválasztott forrásadatbázis-adatbázist, majd próbálkozzon újra.

- **Hiba:** A célként megadott adatbázis ({Database}) üres. Migrálja a sémát.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha a céladatbázis nem rendelkezik sémával. Győződjön meg arról, hogy a cél sémája egyezik a forrásban található sémával.
  **Áthidaló megoldás**: Győződjön meg arról, hogy a célként megadott séma megfelel a forrás sémájának. A séma áttelepítésével kapcsolatos részletekért tekintse meg az [Azure PostgreSQL Online áttelepítési dokumentációját](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

## <a name="other-limitations"></a>Egyéb korlátozások

- Az adatbázis neve nem tartalmazhatja a pontosvesszőt (;).
- A rögzített táblának rendelkeznie kell egy elsődleges kulccsal. Ha egy tábla nem rendelkezik elsődleges kulccsal, a törlés és a rekord frissítése művelet eredménye kiszámíthatatlan lesz.
- Az elsődleges kulcs szegmensének frissítése figyelmen kívül lesz hagyva. Ilyen esetekben az ilyen frissítés alkalmazását a cél olyan frissítésként azonosítja, amely nem módosította a sorokat, és egy, a kivételek táblába írt rekordot fog eredményezni.
- Több tábla ugyanazzal a névvel való áttelepítése, de egy másik eset (például Tábla1, TÁBLA1 és tábla1) kiszámíthatatlan működést eredményezhet, ezért nem támogatott.
- A (z) [CREATE | feldolgozásának módosítása ALTER | DROP | CSONKOLT] a Table DDLs nem támogatott.
- Azure Database Migration Service egyetlen áttelepítési tevékenység legfeljebb négy adatbázist tud fogadni.
- A pg_largeobject tábla migrálása nem támogatott. 
