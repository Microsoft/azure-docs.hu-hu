---
title: fájl belefoglalása
description: fájl belefoglalása
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 04/07/2021
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: 09eb6d9483268314febd9478a551595a059d973b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024076"
---
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).


Kövesse az alábbi lépéseket az Azure-adatbázis PostgreSQL-kiszolgálóhoz létrehozásához:
1. Kattintson az **erőforrás létrehozása**  elemre a Azure Portal bal felső sarkában.
2. Az **Új** panelen válassza az **Adatbázisok** lehetőséget, majd az **Adatbázisok** panelen válassza az **Azure-adatbázis PostgreSQL-kiszolgálóhoz** lehetőséget.
3. A központi telepítés beállításnál kattintson a **Létrehozás** gombra a **nagy kapacitású (Citus) kiszolgáló csoportban.**
4. Adja meg az alábbi adatokat az új kiszolgálóűrlapon:
   - Erőforráscsoport: kattintson az **új létrehozása** hivatkozásra a mező szövegmezője alatt. Adjon meg egy nevet, például **myresourcegroup**.
   - Kiszolgálócsoport neve: adjon meg egy egyedi nevet az új kiszolgálócsoport számára, amely egy kiszolgálói altartományhoz is használható.
   - Aktiválja a jelölőnégyzetet, és **engedélyezze az előzetes verziójú funkciókat**.
   - Rendszergazdai Felhasználónév: a **citus** értéknek jelenleg kötelezőnek kell lennie, és nem módosítható.
   - Jelszó: legalább nyolc karakter hosszúnak kell lennie, és tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból – angol nagybetűs karakterek, angol kisbetűs karakterek, számok (0-9) és nem alfanumerikus karakterek (!, $, #,% stb.)
   - Hely: használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adataihoz.

   > [!IMPORTANT]
   > Az itt megadott kiszolgálói rendszergazdai jelszó szükséges a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

5. Kattintson a **kiszolgálócsoport konfigurálása** elemre.
   - Válassza ki a **szinthez** tartozó **alapszintű** választógombot.
   - Kattintson a **Mentés** gombra.
6. Kattintson a **Tovább gombra: hálózatkezelés >** a képernyő alján.

7. A **hálózatkezelés** lapon kattintson a **nyilvános végpont** választógombra.
   ![Nyilvános végpont kiválasztva](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Kattintson a hivatkozásra **+ az aktuális ügyfél IP-címének hozzáadása** elemre.
   ![Ügyfél IP-címe hozzáadva](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > Azure PostgreSQL-kiszolgáló az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Ha igen, nem tud csatlakozni a nagy kapacitású-fürthöz (Citus), kivéve, ha az informatikai részleg megnyitja a 5432-es portot.
   >

9. Kattintson a **felülvizsgálat + létrehozás** , majd a **Létrehozás** elemre a kiszolgáló kiépítéséhez. A kiépítés eltarthat néhány percig.
10. A rendszer átirányítja a lapot az üzemelő példány figyelésére. Ha a telepítés állapotának változása **folyamatban van** az üzemelő **példányról, kattintson** a lap bal oldalán található **outputs (kimenet** ) elemre.
11. A outputs (kimenetek) lapon egy, a mellette található gomb is szerepel, amely az értéket a vágólapra másolja. Jegyezze fel ezt az információt későbbi használatra.

## <a name="connect-to-the-database-using-psql"></a>Kapcsolódás az adatbázishoz a psql használatával

A Azure Database for PostgreSQL-kiszolgáló létrehozásakor létrejön egy **citus** nevű alapértelmezett adatbázis. Az adatbázis-kiszolgálóhoz való kapcsolódáshoz kapcsolati sztringre és rendszergazdai jelszóra van szükség.

1. Szerezze be a kapcsolatok karakterláncát. A kiszolgálócsoport lapon kattintson a **kapcsolatok karakterláncok** menüelemre. (A **Beállítások** alatt van.) Keresse meg a **psql** jelölésű karakterláncot. A formátum a következőket eredményezi:

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   Másolja a karakterláncot. A korábban kiválasztott rendszergazdai jelszóval kell lecserélnie a (z) {Your \_ Password} kifejezést. A rendszer nem tárolja az egyszerű szöveges jelszót, ezért nem jelenítheti meg a kapcsolatok karakterláncában.

2. Nyisson meg egy terminál-ablakot a helyi számítógépen.

3. A parancssorban kapcsolódjon a Azure Database for PostgreSQL-kiszolgálóhoz a [psql](https://www.postgresql.org/docs/current/app-psql.html) segédprogrammal. Adja át a kapcsolatok karakterláncát idézőjelek között, hogy az tartalmazza a jelszavát:
   ```bash
   psql "host=..."
   ```

   Például a következő parancs csatlakozik a **mydemoserver**-kiszolgálócsoport koordinátori csomópontjára:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
