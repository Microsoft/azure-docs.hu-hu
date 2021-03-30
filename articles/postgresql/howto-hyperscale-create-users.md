---
title: Felhasználók létrehozása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Ez a cikk azt ismerteti, hogyan hozhat létre új felhasználói fiókokat Azure Database for PostgreSQL-nagy kapacitású (Citus) való interakcióhoz.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/8/2019
ms.openlocfilehash: 3d23ee6119b625e11ce44bb9ad11ce4b3ee0280d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91295736"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Felhasználók létrehozása a Azure Database for PostgreSQL-nagy kapacitású (Citus)

> [!NOTE]
> A "felhasználók" kifejezés egy nagy kapacitású-(Citus-) kiszolgálócsoport felhasználóira hivatkozik. Az Azure-előfizetések felhasználóinak és azok jogosultságának megismeréséhez látogasson el az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) című cikkre](../role-based-access-control/built-in-roles.md) , vagy tekintse át a [szerepkörök testreszabását](../role-based-access-control/custom-roles.md)ismertető cikket.

## <a name="the-server-admin-account"></a>A kiszolgálói rendszergazdafiók

A PostgreSQL-motor [szerepköröket](https://www.postgresql.org/docs/current/sql-createrole.html) használ az adatbázis-objektumokhoz való hozzáférés szabályozására, és az újonnan létrehozott nagy kapacitású-(Citus-) kiszolgálócsoport több szerepkört is tartalmaz előre definiált:

* Az [alapértelmezett PostgreSQL-szerepkörök](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Mivel a nagy kapacitású (Citus) egy felügyelt Pásti szolgáltatás, csak a Microsoft tud bejelentkezni a `postgres` felügyelői szerepkörbe. A korlátozott rendszergazdai hozzáféréshez a nagy kapacitású (Citus) biztosítja a `citus` szerepkört.

A szerepkör engedélyei `citus` :

* Olvassa el az összes konfigurációs változót, még akkor is, ha a változók általában csak a főfelhasználók számára láthatók.
* Olvassa el az összes PG \_ stat- \_ \* nézetet, és használja a statisztikával kapcsolatos különböző bővítményeket – akár nézeteket vagy bővítményeket is, amelyek általában csak a főfelhasználók számára láthatók.
* Olyan figyelési funkciókat hajthat végre, amelyek hozzáférést kaphatnak a táblákban lévő MEGOSZTÁSi zárolásokhoz, akár hosszú ideig is.
* [Hozzon létre PostgreSQL-bővítményeket](concepts-hyperscale-extensions.md) (mivel a szerepkör a tagja `azure_pg_admin` ).

A `citus` szerepkörnek például vannak korlátai:

* Nem hozhatók létre szerepkörök
* Nem hozhatók létre adatbázisok

## <a name="how-to-create-additional-user-roles"></a>További felhasználói szerepkörök létrehozása

Ahogy említettük, a `citus` rendszergazdai fióknak nincs engedélye további felhasználók létrehozására. Felhasználó hozzáadásához használja a Azure Portal felületet.

1. Lépjen a nagy kapacitású (Citus) csoport **szerepkörök** lapjára, majd kattintson a **+ Hozzáadás** gombra:

   :::image type="content" source="media/howto-hyperscale-create-users/1-role-page.png" alt-text="A szerepkörök lap":::

2. Adja meg a szerepkör nevét és jelszavát. Kattintson a **Mentés** gombra.

   :::image type="content" source="media/howto-hyperscale-create-users/2-add-user-fields.png" alt-text="Szerepkör hozzáadása":::

A felhasználó a kiszolgálócsoport koordinátor csomópontján jön létre, és az összes munkavégző csomópontra propagálva lesz. A Azure Portal által létrehozott szerepkörök rendelkeznek az `LOGIN` attribútummal, ami azt jelenti, hogy azok az igaz felhasználók, akik bejelentkezhetnek az adatbázisba.

## <a name="how-to-modify-privileges-for-user-role"></a>Felhasználói szerepkör engedélyeinek módosítása

Az új felhasználói szerepköröket általában a korlátozott jogosultságokkal rendelkező adatbázis-hozzáférés biztosítására használják. A felhasználói jogosultságok módosításához használja a standard PostgreSQL-parancsokat egy olyan eszköz használatával, mint például a PgAdmin vagy a psql. (Lásd: [Csatlakozás a psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) -hez a nagy kapacitású (Citus) rövid útmutatójában.)

Ha például engedélyezni `db_user` szeretné az olvasást `mytable` , adja meg az engedélyt:

```sql
GRANT SELECT ON mytable TO db_user;
```

A nagy kapacitású (Citus) a teljes fürtön keresztül propagálja az egytáblás engedélyezési utasításokat, és alkalmazza azokat az összes munkavégző csomóponton. Emellett a rendszerszintű (például a sémában lévő összes táblázathoz tartozó) támogatásokat is propagálja:

```sql
-- applies to the coordinator node and propagates to workers
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Felhasználói szerepkör törlése vagy jelszavuk módosítása

Egy felhasználó frissítéséhez keresse fel a nagy kapacitású (Citus) kiszolgálócsoport **szerepkörök** lapját, és kattintson a felhasználó melletti három pontra. **..** Az ellipszisek menüt nyitnak meg a felhasználó törléséhez vagy a jelszavuk alaphelyzetbe állításához.

   :::image type="content" source="media/howto-hyperscale-create-users/edit-role.png" alt-text="Szerepkör szerkesztése":::

A `citus` szerepkör jogosultsággal rendelkezik, és nem törölhető.

## <a name="next-steps"></a>Következő lépések

Nyissa meg a tűzfalat az új felhasználói gépek IP-címei számára, hogy csatlakozhasson a kapcsolódáshoz: [nagy kapacitású-(Citus-) tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](howto-hyperscale-manage-firewall-using-portal.md).

Az adatbázis-felhasználói fiókok kezelésével kapcsolatos további információkért lásd a PostgreSQL termékdokumentációját:

* [Adatbázis-szerepkörök és-jogosultságok](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Szintaxis megadása](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Jogosultságok](https://www.postgresql.org/docs/current/static/ddl-priv.html)
