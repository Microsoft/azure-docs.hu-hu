---
title: Felhasználók létrehozása – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan hozhat létre új felhasználói fiókokat egy Azure Database for PostgreSQL – egyetlen kiszolgálóval való kommunikációhoz.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2019
ms.openlocfilehash: c2d0cfc15457d45701f129ae329295064d773a09
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604107"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Felhasználók létrehozása Azure Database for PostgreSQL – egyetlen kiszolgálón

Ez a cikk azt ismerteti, hogyan hozhat létre felhasználókat egy Azure Database for PostgreSQL-kiszolgálón belül.

Ha szeretné megtudni, hogyan hozhat létre és kezelhet Azure-előfizetést használó felhasználókat és azok jogosultságait, látogasson el az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) című cikkre](../role-based-access-control/built-in-roles.md) , vagy tekintse át a [szerepkörök testreszabását](../role-based-access-control/custom-roles.md)ismertető cikket.

## <a name="the-server-admin-account"></a>A kiszolgálói rendszergazdafiók

Amikor először hozta létre a Azure Database for PostgreSQL, a kiszolgálói rendszergazda felhasználónevet és jelszót adott meg. További információkért kövesse a rövid útmutató lépéseit [, ahol](quickstart-create-server-database-portal.md) megtekintheti a lépésenkénti megközelítést. Mivel a kiszolgáló-rendszergazda felhasználóneve egy egyéni név, a kiválasztott kiszolgálói rendszergazdai felhasználónevet a Azure Portalban találja.

A Azure Database for PostgreSQL-kiszolgáló a megadott 3 alapértelmezett szerepkörrel jön létre. Ezeket a szerepköröket a következő parancs futtatásával tekintheti meg: `SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- a kiszolgáló-rendszergazda felhasználó

A kiszolgáló-rendszergazda felhasználó a azure_pg_admin szerepkör tagja. A kiszolgálói rendszergazdai fiók azonban nem része a azure_superuser szerepkörnek. Mivel ez a szolgáltatás felügyelt Pásti szolgáltatás, csak a Microsoft tagja a felügyelői szerepkörnek.

A PostgreSQL-motor jogosultságokat használ az adatbázis-objektumokhoz való hozzáférés vezérlésére, ahogy azt a [PostgreSQL termékdokumentációja](https://www.postgresql.org/docs/current/static/sql-createrole.html)tárgyalja. Azure Database for PostgreSQL a kiszolgáló-rendszergazda felhasználó megkapja ezeket a jogosultságokat: bejelentkezés, nem rendszergazda, öröklés, CREATEDB, CREATEROLE, nincs REPLIKÁCIÓ

A kiszolgáló-rendszergazdai felhasználói fiókkal további felhasználókat hozhat létre, és megadhatja a felhasználókat a azure_pg_admin szerepkörnek. A kiszolgálói rendszergazdai fiókkal kevesebb jogosultsággal rendelkező felhasználót és szerepkört hozhat létre, amelyek az egyes adatbázisokhoz és sémához is hozzáférnek.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>További rendszergazda felhasználók létrehozása a Azure Database for PostgreSQLban

1. Kérje le a kapcsolatfelvételi adatokat és a rendszergazda felhasználónevét.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. A kiszolgáló és a bejelentkezési adatok könnyen megtalálhatók a kiszolgáló **Áttekintés** lapján vagy a Azure Portal **Tulajdonságok** lapján.

2. Az adatbázis-kiszolgálóhoz való kapcsolódáshoz használja a rendszergazdai fiókot és a jelszót. Használja az előnyben részesített ügyfélprogramot, például pgAdmin vagy psql.
   Ha nem tudja, hogyan csatlakozhat, tekintse meg [a](./quickstart-create-server-database-portal.md) rövid útmutatót.

3. Szerkessze és futtassa a következő SQL-kódot. Cserélje le az új felhasználónevet a helyőrző értékre <new_user>, és cserélje le a helyőrző jelszavát a saját erős jelszavára. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Adatbázis-felhasználók létrehozása a Azure Database for PostgreSQLban

1. Kérje le a kapcsolatfelvételi adatokat és a rendszergazda felhasználónevét.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. A kiszolgáló és a bejelentkezési adatok könnyen megtalálhatók a kiszolgáló **Áttekintés** lapján vagy a Azure Portal **Tulajdonságok** lapján.

2. Az adatbázis-kiszolgálóhoz való kapcsolódáshoz használja a rendszergazdai fiókot és a jelszót. Használja az előnyben részesített ügyfélprogramot, például pgAdmin vagy psql.

3. Szerkessze és futtassa a következő SQL-kódot. Cserélje le a helyőrző értékét a `<db_user>` kívánt új felhasználónévre, és adja meg a helyőrző értékét a `<newdb>` saját adatbázisának nevével. Cserélje le a helyőrző jelszavát a saját erős jelszavára.

   Ez az SQL Code-szintaxis egy új, testdb nevű adatbázist hoz létre, például a következő célokra:. Ezután létrehoz egy új felhasználót a PostgreSQL szolgáltatásban, és csatlakozási jogosultságokat biztosít az új adatbázishoz az adott felhasználó számára.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Rendszergazdai fiók használata esetén előfordulhat, hogy további jogosultságokat kell megadnia az objektumok biztonságossá tételéhez az adatbázisban. Az adatbázis-szerepkörökkel és a jogosultságokkal kapcsolatos további részletekért tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/current/static/ddl-priv.html) . Például:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Jelentkezzen be a kiszolgálóra, és adja meg a kijelölt adatbázist az új Felhasználónév és jelszó használatával. Ebben a példában a psql parancssor látható. Ezzel a paranccsal a rendszer a felhasználónévhez tartozó jelszót kéri. Cserélje le a saját kiszolgáló nevét, az adatbázis nevét és a felhasználónevet.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Következő lépések

Nyissa meg a tűzfalat az új felhasználói gépek IP-címei számára a kapcsolódáshoz: [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése a Azure Portal vagy az](howto-manage-firewall-using-portal.md) [Azure CLI](howto-manage-firewall-using-cli.md)használatával.

A felhasználói fiókok kezelésével kapcsolatos további információkért lásd: PostgreSQL termékdokumentációja [adatbázis-szerepkörökhöz és-jogosultságokhoz](https://www.postgresql.org/docs/current/static/user-manag.html), [szintaxis megadása](https://www.postgresql.org/docs/current/static/sql-grant.html)és [jogosultságok](https://www.postgresql.org/docs/current/static/ddl-priv.html).
