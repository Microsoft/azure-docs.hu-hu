---
title: 'Ismert problémák: az online áttelepítés Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Ismerje meg az ismert problémákat és az áttelepítési korlátozásokat az online áttelepítéssel Azure Database for MySQL a Azure Database Migration Service használatakor.
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
ms.openlocfilehash: 11659bcbdf77d04c0f4e6f8bc7aca30c716fc924
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606889"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Az online áttelepítéssel kapcsolatos problémák & a MySQL-hez készült Azure-ADATBÁZISra vonatkozó korlátozásokat Azure Database Migration Service

A MySQL-ről Azure Database for MySQLre való online áttelepítéssel kapcsolatos ismert problémák és korlátozások a következő szakaszokban olvashatók.

## <a name="online-migration-configuration"></a>Online áttelepítési konfiguráció


- A forrás MySQL-kiszolgáló verziójának 5.6.35, 5.7.18 vagy újabb verziójúnak kell lennie
- Azure Database for MySQL a következőket támogatja:
  - MySQL Community Edition
  - InnoDB motor
- Azonos verziójú áttelepítés. A MySQL 5,6 áttelepítése Azure Database for MySQL 5,7-re nem támogatott. A MySQL 8,0-ből vagy-ból való Migrálás nem támogatott.
- Bináris naplózás engedélyezése my.ini (Windows) vagy My. cnf (UNIX) rendszeren
  - Server_id tetszőleges számú vagy egyenlő értékre állítja, például Server_id = 1 (csak MySQL 5,6 esetén)
  - Log-bin = \<path> (csak MySQL 5,6) beállítása
  - Binlog_format = sor beállítása
  - Expire_logs_days = 5 (csak a MySQL 5,6 esetén ajánlott)
- A felhasználónak rendelkeznie kell a ReplicationAdmin szerepkörrel.
- A forrás MySQL-adatbázishoz definiált rendezések ugyanazok, mint a TARGET Azure Database for MySQLban definiált beállítások.
- A sémának meg kell egyeznie a forrás MySQL-adatbázis és a célként megadott adatbázis között Azure Database for MySQL.
- A cél Azure Database for MySQL sémája nem rendelkezhet idegen kulcsokkal. A külső kulcsok eldobásához használja az alábbi lekérdezést:
    ```sql
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
      CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
        KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
        AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Futtassa a ’drop foreign key’-t (ez a második oszlop) a lekérdezési eredményben.
- A cél Azure Database for MySQL sémájában nem lehetnek eseményindítók. Eseményindítók eldobása a céladatbázisben:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Adattípus-korlátozások

- **Korlátozás**: Ha JSON-adattípust talál a forrás MySQL-adatbázisban, a Migrálás a folyamatos szinkronizálás során meghiúsul.

    **Megkerülő megoldás**: módosítsa a JSON-adattípust közepes szöveggé vagy LONGTEXT a forrás MySQL-adatbázisban.

- **Korlátozás**: Ha nincs elsődleges kulcs a táblákon, a folyamatos szinkronizálás sikertelen lesz.

    **Megkerülő megoldás**: átmenetileg állítsa be a tábla elsődleges kulcsát az áttelepítéshez a folytatáshoz. Az elsődleges kulcsot az adatáttelepítés befejeződése után is eltávolíthatja.

## <a name="lob-limitations"></a>LOB-korlátozások

A nagyméretű objektumok (LOB) oszlopai olyan oszlopok, amelyek mérete nagy méretekben nőhet. MySQL-hez, közepes szövegekhez, LONGTEXT, Blobhoz, mediumblob, dereglye stb., a LOB egyes adattípusai.

- **Korlátozás**: Ha a LOB-adattípusok elsődleges kulcsként használatosak, az áttelepítés sikertelen lesz.

    **Áthidaló megoldás**: cserélje le az elsődleges kulcsot más adattípusokra vagy nem LOB oszlopokra.

- **Korlátozás**: Ha a nagyméretű objektum (LOB) oszlopának hossza nagyobb, mint a "LOB-méret korlátozása" paraméter (nem lehet nagyobb, mint 64 kb), a rendszer az adatmennyiséget csonkolja a célhelyen. A LOB-oszlop hosszát a következő lekérdezéssel tekintheti meg:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Megkerülő megoldás**: Ha 64 KB-nál nagyobb LOB-objektummal rendelkezik, használja a "korlátlan LOB-méret engedélyezése" paramétert. Vegye figyelembe, hogy a "korlátlan LOB-méret engedélyezése" paraméterrel rendelkező áttelepítések lassabbak lesznek, mint a "LOB-méret korlátozása" paraméterrel.

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Korlátozások az AWS RDS MySQL-ről való online áttelepítés során

Ha az AWS RDS MySQL-ről a Azure Database for MySQLra próbál online áttelepítést végezni, akkor a következő hibák merülhetnek fel.

- **Hiba:** A ( {0} z) "" adatbázis külső kulcsa a cél. Javítsa a célt, és indítson új adatmigrálási tevékenységet. Futtassa az alábbi szkriptet a célhelyen a külső kulcs (ok) listázásához

  **Korlátozás**: Ha a sémában idegen kulcsok vannak, az áttelepítés kezdeti terhelése és folyamatos szinkronizálása sikertelen lesz.
  **Áthidaló megoldás**: hajtsa végre a következő szkriptet a MySQL workbenchben a drop Foreign Key parancsfájl kinyeréséhez és a külső kulcshoz tartozó parancsfájl hozzáadásához:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Hiba:** A (z {0} ) "" adatbázis nem létezik a kiszolgálón. A megadott MySQL-forráskiszolgáló esetében különbözőnek számítanak a kis- és nagybetűk. Ellenőrizze az adatbázis nevét.

  **Korlátozás**: Ha MySQL-adatbázist telepít át az Azure-ba a parancssori felületen (CLI), akkor a felhasználók ezt a hibát érhetik el. A szolgáltatás nem találta meg az adatbázist a forráskiszolgálón, mert lehetséges, hogy helytelen adatbázisnevet adott meg, vagy az adatbázis nem létezik a felsorolt kiszolgálón. Megjegyzés: az adatbázisok nevei megkülönböztetik a kis-és nagybetűket.

  **Megkerülő megoldás**: adja meg az adatbázis pontos nevét, majd próbálkozzon újra.

- **Hiba:** A (z) {Database} adatbázisban azonos nevű táblák találhatók. Az Azure Database for MySQL nem támogatja a kis- és nagybetűket megkülönböztető táblákat.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha két azonos nevű tábla található a forrás-adatbázisban. A Azure Database for MySQL nem támogatja a kis-és nagybetűket megkülönböztető táblákat.

  **Áthidaló megoldás**: frissítse a táblák nevét egyedire, majd próbálkozzon újra.

- **Hiba:** A célként megadott adatbázis ({Database}) üres. Migrálja a sémát.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha a cél Azure Database for MySQL-adatbázis nem rendelkezik a szükséges sémával. A séma áttelepítése szükséges ahhoz, hogy lehetővé váljon az adatáttelepítés a célhelyre.

  **Áthidaló megoldás**: [telepítse át a sémát](./tutorial-mysql-azure-mysql-online.md#migrate-the-sample-schema) a forrás adatbázisából a céladatbázisbe.

## <a name="other-limitations"></a>Egyéb korlátozások

- A jelszó karakterláncának elején és végén lévő kapcsos zárójelek {} nyitó és záró karakterlánca nem támogatott. Ez a korlátozás a Source MySQL-hez és a TARGET Azure Database for MySQLhoz való csatlakozásra egyaránt vonatkozik.
- A következő DDLs nem támogatottak:
  - Minden partíciós DDLs
  - Táblázat eldobása
  - Táblázat átnevezése
- Az *Alter table <table_name> oszlop hozzáadása <column_name>* utasítást, hogy oszlopokat adjon hozzá a tábla elejéhez vagy közepéhez, nem támogatott. Az *Alter table <table_name> oszlop hozzáadása <column_name>* hozzáadja a tábla végén található oszlopot.
- Az oszlopok adattípusának csak egy részén létrehozott indexek nem támogatottak. Az alábbi utasítás egy olyan példát mutat be, amely egy indexet hoz létre az oszlop adatai csak egy részének használatával:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- Azure Database Migration Service az egyetlen áttelepítési tevékenységben az áttelepítéshez szükséges adatbázisok korlátja négy.

- Az Azure DMS nem támogatja a lépcsőzetes hivatkozási műveletet, amely segít automatikusan törölni vagy frissíteni a gyermektábla egyező sorát, amikor egy sort törölnek vagy frissítenek a fölérendelt táblában. További információ a MySQL dokumentációjában található a [FOREIGN Key megkötések](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)című cikk hivatkozási műveletek című szakasza. Az Azure DMS-nek szüksége van arra, hogy a kezdeti adatterhelés során dobja el a külső kulcsra vonatkozó korlátozásokat a célként megadott adatbázis-kiszolgálón, és nem használhat hivatkozási műveleteket. Ha a számítási feladat a kapcsolódó alárendelt tábla ezen hivatkozási művelettel való frissítésének függvénye, akkor azt javasoljuk, hogy ehelyett végezzen el egy [memóriaképet és egy visszaállítást](../mysql/concepts-migrate-dump-restore.md) . 

- **Hiba:** A sor mérete túl nagy (> 8126). Egyes oszlopok SZÖVEGre vagy BLOBra való módosítása segíthet. Az aktuális sor formátumában 0 bájtos BLOB-előtag van tárolva.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha a InnoDB-tároló motor használatával Azure Database for MySQL migrálni, és a tábla sorainak mérete túl nagy (>8126 bájt).

  **Áthidaló megoldás**: frissítse a tábla azon sémáját, amelynek mérete meghaladja a 8126 bájtnál nagyobb méretű sort. Nem javasoljuk a szigorú mód módosítását, mert a rendszer csonkolja az adatvesztést. A page_size módosítása nem támogatott.