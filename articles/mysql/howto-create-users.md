---
title: Adatbázisok és felhasználók létrehozása – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan hozhatók létre új felhasználói fiókok Azure Database for MySQL-kiszolgálóval való kommunikációhoz.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: 9a6346a2b62c81dd74cf0ebe9a85df12d3488679
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251250"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>Adatbázisok és felhasználók létrehozása a Azure Database for MySQLban

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Ez a cikk azt ismerteti, hogyan hozhatók létre felhasználók a Azure Database for MySQLban.

> [!NOTE]
> Ez a cikk a _Slave_ kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.
>

Amikor először hozta létre a Azure Database for MySQL-kiszolgálót, a kiszolgálói rendszergazda felhasználónevet és jelszót adott meg. További [információt ebben a](quickstart-create-mysql-server-database-using-azure-portal.md)rövid útmutatóban talál. A kiszolgáló rendszergazdájának felhasználónevét a Azure Portalban határozhatja meg.

A kiszolgáló-rendszergazda felhasználó rendelkezik a következő jogosultságokkal:

   KIJELÖLÉS, BESZÚRÁS, FRISSÍTÉS, TÖRLÉS, LÉTREHOZÁS, ELDOBÁS, ÚJRATÖLTÉS, FELDOLGOZÁS, HIVATKOZÁSOK, INDEX, ALTER, ADATBÁZISOK MEGJELENÍTÉSE, IDEIGLENES TÁBLÁK LÉTREHOZÁSA, TÁBLÁK ZÁROLÁSA, VÉGREHAJTÁS, REPLIKÁLÁSI SLAVE, REPLIKÁCIÓS ÜGYFÉL, NÉZET LÉTREHOZÁSA, NÉZET MEGJELENÍTÉSE, RUTIN LÉTREHOZÁSA, MÓDOSÍTÁS RUTIN, FELHASZNÁLÓ LÉTREHOZÁSA, ESEMÉNY, ESEMÉNYINDÍTÓ

Azure Database for MySQL-kiszolgáló létrehozása után az első kiszolgálói rendszergazdai fiók használatával több felhasználót is létrehozhat, és rendszergazdai hozzáférést biztosíthat számukra. A kiszolgálói rendszergazdai fiókkal kevesebb jogosultsággal rendelkező felhasználó hozható létre, akik egyéni adatbázis-sémákkal rendelkeznek hozzáféréssel.

> [!NOTE]
> A SUPER Privilege és a DBA szerepkör nem támogatott. Tekintse át a korlátozások című cikkben szereplő [jogosultságokat](concepts-limits.md#privileges--data-manipulation-support) , hogy megtudja, mi nem támogatott a szolgáltatásban.
>
> `validate_password` `caching_sha2_password` A szolgáltatás nem támogatja a jelszóhoz tartozó beépülő modulokat.

## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>Adatbázis létrehozása nem rendszergazda felhasználóval Azure Database for MySQL

1. Kérje le a kapcsolatfelvételi adatokat és a rendszergazda felhasználónevét.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. A kiszolgáló nevét és bejelentkezési adatait a kiszolgáló **Áttekintés** lapján vagy a Azure Portal **Tulajdonságok** lapján találhatja meg.

2. Az adatbázis-kiszolgálóhoz való kapcsolódáshoz használja a rendszergazdai fiókot és a jelszót. Használja az előnyben részesített ügyfélprogramot, például a MySQL Workbench, a mysql.exe vagy a HeidiSQL.

   Ha nem tudja, hogyan csatlakozhat, tekintse meg [az egykiszolgálós kapcsolat és lekérdezési információk összekapcsolását](./connect-workbench.md) és [a rugalmas kiszolgálóval való kapcsolódást és adatlekérdezést](./flexible-server/connect-workbench.md)ismertető témakört.

3. Szerkessze és futtassa a következő SQL-kódot. Cserélje le a helyőrző értékét a `db_user` kívánt új felhasználónévre. Cserélje le a helyőrző értékét az `testdb` adatbázis nevére.

   Ez az SQL-kód egy új, testdb nevű adatbázist hoz létre. Ezután létrehoz egy új felhasználót a MySQL szolgáltatásban, és minden jogosultságot biztosít az új adatbázis-sémához (testdb. \* ) az adott felhasználó számára.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Ellenőrizze a támogatást az adatbázisban:

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Jelentkezzen be a kiszolgálóra, adja meg a kijelölt adatbázist, és használja az új felhasználónevet és jelszót. Ez a példa a MySQL parancssort jeleníti meg. Ha ezt a parancsot használja, a rendszer kérni fogja a felhasználó jelszavát. Használja a saját kiszolgáló nevét, az adatbázis nevét és a felhasználónevet.

   ### <a name="single-server"></a>[Önálló kiszolgáló](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

   ### <a name="flexible-server"></a>[Rugalmas kiszolgáló](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-more-admin-users-in-azure-database-for-mysql"></a>Több rendszergazda felhasználó létrehozása Azure Database for MySQL

1. Kérje le a kapcsolatfelvételi adatokat és a rendszergazda felhasználónevét.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. A kiszolgáló nevét és bejelentkezési adatait a kiszolgáló **Áttekintés** lapján vagy a Azure Portal **Tulajdonságok** lapján találhatja meg.

2. Az adatbázis-kiszolgálóhoz való kapcsolódáshoz használja a rendszergazdai fiókot és a jelszót. Használja az előnyben részesített ügyfélprogramot, például a MySQL Workbench, a mysql.exe vagy a HeidiSQL.

   Ha nem tudja, hogyan csatlakozhat, tekintse meg a következőt: [a MySQL Workbench használata a kapcsolódáshoz és az adatlekérdezéshez](./connect-workbench.md).

3. Szerkessze és futtassa a következő SQL-kódot. Cserélje le a helyőrző értékét az `new_master_user` új felhasználónevére. Ez a szintaxis megadja a felsorolt jogosultságokat az összes adatbázis-sémán (*.*) a felhasználó számára ( `new_master_user` ebben a példában).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. A támogatások ellenőrzése:

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

Az összes Azure Database for MySQL-kiszolgáló egy "azure_superuser" nevű felhasználóval jön létre. Ez egy, a Microsoft által létrehozott rendszerfiók, amely a kiszolgáló felügyeletét, biztonsági mentését és egyéb rendszeres karbantartását végzi. A hívási mérnökök ezt a fiókot is használhatják a kiszolgáló elérésére a tanúsítványalapú hitelesítéssel rendelkező incidensek során, és az igény szerinti (JIT) folyamatokat kell használniuk.

## <a name="next-steps"></a>Következő lépések

Nyissa meg a tűzfalat az új felhasználói gépek IP-címei számára, hogy a kapcsolódásuk lehetővé váljon:

* [Tűzfalszabályok létrehozása és kezelése egyetlen kiszolgálón](howto-manage-firewall-using-portal.md)
* [Tűzfalszabályok létrehozása és kezelése rugalmas kiszolgálón](flexible-server/how-to-connect-tls-ssl.md)

A felhasználói fiókok kezelésével kapcsolatos további információkért tekintse meg a MySQL termékdokumentációt a [felhasználói fiókok felügyeletéhez](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), a [jogosultságok](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html) [megadásához](https://dev.mysql.com/doc/refman/5.7/en/grant.html)és a jogosultságokhoz.
