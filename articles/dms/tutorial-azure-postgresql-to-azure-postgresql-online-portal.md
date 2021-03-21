---
title: 'Oktatóanyag: az Azure DB for PostgreSQL átmigrálása az Azure Database for PostgreSQL online-ba a Azure Portal használatával'
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan hajthat végre egy online áttelepítést az egyik Azure-ADATBÁZISból a PostgreSQL-hez egy másik Azure Database for PostgreSQLra Azure Database Migration Service használatával a Azure Portalon keresztül
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 07/21/2020
ms.openlocfilehash: 996523d507f0a4f2850e936df39a38769bc47cde
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101091292"
---
# <a name="tutorial-migrateupgrade-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server--online-using-dms-via-the-azure-portal"></a>Oktatóanyag: Azure Database for PostgreSQL – egyetlen kiszolgáló az Azure DB for PostgreSQL-hez – egyetlen kiszolgáló online a DMS használatával a Azure Portal

A Azure Database Migration Service segítségével áttelepítheti az adatbázisokat egy [Azure Database for PostgreSQL-egykiszolgálós](../postgresql/overview.md#azure-database-for-postgresql---single-server) példányról az Azure Database for PostgreSQL-egykiszolgálós példány vagy a Azure Database for PostgreSQL rugalmas kiszolgáló különböző verziójára minimális állásidővel. Ebben az oktatóanyagban áttelepíti a **DVD-kölcsönzési** minta adatbázisát egy Azure Database for PostgreSQL v10-ből Azure Database for PostgreSQL egyetlen kiszolgálóra a Azure Database Migration Service Online áttelepítési tevékenységének használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Telepítse át a minta sémát a pg_dump segédprogram használatával.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet a Azure Database Migration Service.
> * A migrálás futtatása.
> * Az áttelepítés monitorozása.
> * Hajtsa végre az áttelepítési átváltás.

> [!NOTE]
> A Azure Database Migration Service használata az online áttelepítés végrehajtásához a prémium szintű díjszabás alapján kell létrehoznia egy példányt. A lemez titkosításával megakadályozható az adatlopás az áttelepítés során

> [!IMPORTANT]
> Az optimális áttelepítési élmény érdekében a Microsoft azt javasolja, hogy Azure Database Migration Service-példányt hozzon létre ugyanabban az Azure-régióban, mint a célként megadott adatbázis. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

> [!IMPORTANT]
> A Azure Database for PostgreSQL rendszerről történő áttelepítés a PostgreSQL 10-es és újabb verzióiban támogatott. Ezt az oktatóanyagot arra is használhatja, hogy áttelepítsen egy Azure Database for PostgreSQL példányról egy másik Azure Database for PostgreSQL példány-vagy nagy kapacitású (Citus) példányra.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* A [Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapotának](./resource-scenario-status.md) ellenőrzéséhez a támogatott áttelepítési és verzió-kombinációk esetében. 
* Egy meglévő [Azure Database for PostgreSQL](../postgresql/index.yml) 10-es vagy újabb verzió a DVD- **kölcsönzési** adatbázissal. 

    Azt is vegye figyelembe, hogy a cél Azure Database for PostgreSQL verziószámának vagy annál későbbinek kell lennie, mint a helyszíni PostgreSQL-verziónak. Például a PostgreSQL 10 áttelepíthető Azure Database for PostgreSQL 10 vagy 11 rendszerre, de nem Azure Database for PostgreSQL 9,6.

* [Hozzon létre egy Azure Database for PostgreSQL-kiszolgálót](../postgresql/quickstart-create-server-database-portal.md) , vagy [hozzon létre egy Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgálót](../postgresql/quickstart-create-hyperscale-portal.md) célként szolgáló adatbázis-kiszolgálóként, amelybe áttelepíti az adatátvitelt.
* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Servicehoz a Azure Resource Manager üzembe helyezési modell használatával. A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](../virtual-network/index.yml), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.

* Győződjön meg arról, hogy a virtuális hálózat hálózati biztonsági csoport (NSG) szabályai nem blokkolják a ServiceBus, a Storage és a AzureMonitor ServiceTag 443-es kimenő portját. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/virtual-network-vnet-plan-design-arm.md)című cikket.
* Hozzon létre egy kiszolgálói szintű [Tűzfalszabály-szabályt](../azure-sql/database/firewall-configure.md) a Azure Database for PostgreSQL forráshoz, hogy a Azure Database Migration Service hozzáférjen a forrás-adatbázisokhoz. Adja meg a Azure Database Migration Service használt virtuális hálózat alhálózati tartományát.
* Hozzon létre egy kiszolgálói szintű [Tűzfalszabály](../azure-sql/database/firewall-configure.md) a Azure Database for PostgreSQL céljához, hogy engedélyezze a Azure Database Migration Service számára a célként megadott adatbázisokhoz való hozzáférést. Adja meg a Azure Database Migration Service használt virtuális hálózat alhálózati tartományát.
* [Engedélyezze a logikai replikációt](../postgresql/concepts-logical.md) az Azure-adatbázis PostgreSQL-forrásához. 
* Állítsa be a következő kiszolgálói paramétereket a forrásként használt Azure Database for PostgreSQL példányban:

  * max_replication_slots = [bővítőhelyek száma], javasolt beállítás **10 bővítőhelyre**
  * max_wal_senders = [párhuzamos feladatok száma] – a max_wal_senders paraméter megadja a párhuzamosan futtatható feladatok számát, az ajánlott beállítás **10 feladat**

> [!NOTE]
> A fenti kiszolgálói paraméterek statikusak, és a Azure Database for PostgreSQL-példány újraindítását igénylik érvénybe léptetéséhez. A kiszolgálói paraméterekkel kapcsolatos további információkért lásd: [Azure Database for PostgreSQL kiszolgáló paramétereinek konfigurálása](../postgresql/howto-configure-server-parameters-using-portal.md).

> [!IMPORTANT]
> A meglévő adatbázisban lévő összes táblának elsődleges kulcsra van szüksége annak biztosításához, hogy a módosítások szinkronizálva legyenek a céladatbázis használatával.

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása

Ahhoz, hogy az összes adatbázis-objektumot táblasémaként, indexekként és tárolt eljárásokként egészíthessük ki, ki kell bontanunk a forrásadatbázis sémáját, és alkalmaznunk kell az adatbázisra.

1. Pg_dump -s parancs segítségével hozzon létre egy séma-memóriaképfájlt az adatbázishoz.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Például a **dvdrental** -adatbázishoz tartozó séma-memóriakép létrehozásához:

    ```
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    A pg_dump segédprogram használatával kapcsolatos további információkért lásd a [pg-memóriakép](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) oktatóanyagban szereplő példákat.

2. Hozzon létre egy üres adatbázist a célkörnyezetben, amely az Azure Database for PostgreSQL.

    Az adatbázisok összekapcsolásával és létrehozásával kapcsolatos további információkért tekintse meg a [Azure Database for PostgreSQL kiszolgáló létrehozása a Azure Portal](../postgresql/quickstart-create-server-database-portal.md) vagy [a Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgáló létrehozása a Azure Portalben](../postgresql/quickstart-create-hyperscale-portal.md)című cikket.

    > [!NOTE]
    > Azure Database for PostgreSQL-nagy kapacitású (Citus) egy példánya csak egyetlen adatbázist tartalmaz: **Citus**.

3. Importálja a sémát a létrehozott céladatbázisba a séma-memóriaképfájl visszaállításával.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Például:

    ```
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. A drop Foreign Key parancsfájl kibontásához és a célhelyhez (Azure Database for PostgreSQL) való hozzáadásához futtassa a következő szkriptet a PgAdmin vagy a psql alkalmazásban.

   > [!IMPORTANT]
   > A séma külső kulcsai az áttelepítés kezdeti terhelését és folyamatos szinkronizálását okozzák.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name ,'"', STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ','"', foreignkey,'"'), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name,'"', STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ','"', foreignkey,'"', ' FOREIGN KEY (','"', column_name,'"', ')', ' REFERENCES ','"', foreign_table_schema,'"', '.','"', foreign_table_name,'"', '(','"', foreign_column_name,'"', ')',' ON UPDATE ',update_rule,' ON DELETE ',delete_rule), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name,
        S.update_rule,
        S.delete_rule
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name,
        rc.update_rule AS update_rule,
        rc.delete_rule AS delete_rule
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
        JOIN information_schema.referential_constraints as rc ON rc.constraint_name = tc.constraint_name AND rc.constraint_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name,S.update_rule,S.delete_rule
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Futtassa a ’drop foreign key’-t (ez a második oszlop) a lekérdezési eredményben.

6. A céladatbázis eseményindítóinak letiltásához futtassa az alábbi szkriptet.

   > [!IMPORTANT]
   > A forrásból replikált adatok előtt eseményindítók (INSERT vagy Update), amelyek az adatok integritását érvényesítik a célhelyen. Ennek eredményeképpen ajánlott letiltani az eseményindítókat **a célhelyen** lévő összes táblában az áttelepítés során, majd újból engedélyezni az eseményindítókat az áttelepítés befejeződése után.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók** lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd a **Microsoft. DataMigration** jobb oldalán válassza a **regisztráció** lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Az **áttelepítési szolgáltatás létrehozása** képernyőn adjon meg egy nevet, egy előfizetést, egy új vagy egy meglévő erőforráscsoportot, valamint a szolgáltatás helyét.

4. Válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás PostgreSQL-kiszolgálóhoz és a cél Azure Database for PostgreSQL példányhoz.

    Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md)című cikket.

5. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Válassza a **felülvizsgálat + létrehozás** lehetőséget a szolgáltatás létrehozásához.

   A szolgáltatás létrehozása körülbelül 10 – 15 percen belül elvégezhető.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Azure Database Migration Service összes példányának megkeresése](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service példány nevét, válassza ki a példányt, majd válassza az + **új áttelepítési projekt** lehetőséget.

3. Az **új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **forráskiszolgáló típusa** szövegmezőben válassza a **PostgreSQL** lehetőséget a **célkiszolgáló típusa** szövegmezőben, majd válassza a **Azure Database for PostgreSQL** lehetőséget.
    > [!NOTE]
    > A **forráskiszolgáló típusában** válassza a **PostgreSQL** lehetőséget, annak ellenére, hogy a forráskiszolgáló egy **Azure Database for PostgreSQL** példány.  

4. A **tevékenység típusának** kiválasztása szakaszban válassza az **online adatáttelepítés** lehetőséget.

    ![Azure Database Migration Service projekt létrehozása](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Másik lehetőségként választhatja a **projekt létrehozása** lehetőséget az áttelepítési projekt létrehozásához, és később végrehajthatja az áttelepítést.

5. Válassza a **Save (Mentés**) lehetőséget, és jegyezze fel az adatáttelepítés Azure Database Migration Service sikeres használatát, majd válassza a **Létrehozás és Futtatás tevékenység** lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **forrás hozzáadása részletek** képernyőn adja meg a forrás PostgreSQL-példány kapcsolati adatait.

    ![A Forrás adatainak hozzáadása képernyő](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > A **Azure Database for PostgreSQL** -portálon megtalálhatja a "kiszolgálónév", a "kiszolgáló portja", az "adatbázis neve" stb. részleteit.

2. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. A **cél részletei** képernyőn válassza ki a cél nagy kapacitású (Citus) kiszolgáló kapcsolati adatait, amely a nagy kapacitású (Citus) előre kiépített példánya, amelyre a **DVD-kölcsönzési** sémát a pg_dump használatával telepítették.

    ![A részleteket tartalmazó képernyő](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > Áttelepíthet egy Azure Database for PostgreSQL-példányról egy másik Azure Database for PostgreSQL egykiszolgálós példányra vagy egy nagy kapacitású-kiszolgálóra (Citus).

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrás-adatbázis, akkor a Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázis-adatbázist.

    ![Leképezés a céladatbázis-adatbázisokra képernyő](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Válassza a **Mentés** lehetőséget, majd az **áttelepítési beállítások** képernyőn fogadja el az alapértelmezett értékeket.

    ![Áttelepítési beállítások képernyő](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![Áttelepítési összesítő képernyő](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

Megjelenik az áttelepítési tevékenység ablak, és a tevékenység **állapota** úgy frissül, hogy a **biztonsági mentés folyamatban van**. A következő hibaüzenet jelenhet meg, amikor az Azure DB for PostgreSQL 9,5 vagy 9,6 verzióról frissít:

**Egy forgatókönyv ismeretlen hibát jelzett. 28000: nincs pg_hba. conf bejegyzés a replikációs kapcsolatban a (z) "40.121.141.121" gazdagépről, "SR" felhasználótól**

Ennek az az oka, hogy a PostgreSQL nem rendelkezik megfelelő jogosultsággal a szükséges logikai replikációs összetevők létrehozásához. A szükséges jogosultságok engedélyezéséhez a következőket teheti:

1. Nyissa meg a "kapcsolatbiztonsági" beállításokat a PostgreSQL-kiszolgálóhoz tartozó Azure-ADATBÁZIShoz, amelyet át szeretne telepíteni/frissíteni kíván.
2. Adjon hozzá egy "_replrule" végződésű új tűzfalszabály nevét, és adja hozzá az IP-címet a hibaüzenetből a kezdő IP-cím és a záró IP-mezőhöz. A fenti példában szereplő hiba esetén –
> Tűzfalszabály neve = sr_replrule; Start IP = 40.121.141.121; Záró IP = 40.121.141.121

3. Kattintson a Mentés gombra, és hagyja, hogy a módosítás befejeződjön. 
4. Próbálja megismételni a DMS-tevékenységet. 

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Befejezve** értékre.

     ![Az áttelepítési folyamat figyelése](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Ha az áttelepítés befejeződött, az **adatbázis neve** területen válasszon ki egy adott adatbázist, amely a **teljes adatterheléshez** és a **növekményes adatszinkronizálási** műveletekhez szükséges áttelepítési állapotba kerül.

   > [!NOTE]
   > A **teljes adatterhelés** megjeleníti a kezdeti betöltési áttelepítés állapotát, míg a **növekményes adatszinkronizálás** megjeleníti az adatváltozások rögzítésére (CDC) vonatkozó állapotot.

     ![Teljes adatterhelés részletei](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Növekményes adatok szinkronizálásának részletei](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

2. Várjon, amíg a **függőben lévő változások** számlálója **0-ra** mutat, hogy a forrásadatbázis összes bejövő tranzakciója le legyen állítva, jelölje be a Confirm ( **megerősítés** ) jelölőnégyzetet, majd kattintson az **alkalmaz** gombra.

    ![Teljes átváltás képernyő](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Ha az adatbázis-áttelepítési **állapot megjelenik,** [hozza létre](https://wiki.postgresql.org/wiki/Fixing_Sequences) újra a sorozatot (ha van ilyen), és az alkalmazásait az Azure Database for PostgreSQL új célként megadott példányához kapcsolja.
 
> [!NOTE]
> A Azure Database Migration Service a Azure Database for PostgreSQL-egyetlen kiszolgálón a csökkentett állásidővel rendelkező főverziók frissítésére használható. Először konfigurálnia kell egy céladatbázis-t a kívánt magasabb PostgreSQL-verzióval, a hálózati beállításokkal és paraméterekkel. Ezután kezdeményezheti az áttelepítést a célként megadott adatbázisokra a fent ismertetett eljárás használatával. A cél adatbázis-kiszolgálóra való átváltás követően frissítheti az alkalmazás-kapcsolódási karakterláncot, hogy az a cél adatbázis-kiszolgálóra mutasson. 

## <a name="next-steps"></a>Következő lépések

* Az Azure Database for PostgreSQL-be történő online migrálás végrehajtásakor felmerülő ismert hibákhoz és korlátozásokhoz kapcsolódó információk: [Az Azure Database for PostgreSQL online migrálásával kapcsolatos ismert hibák és kerülő megoldások](known-issues-azure-postgresql-online.md).
* Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](./dms-overview.md).
* További információ a Azure Database for PostgreSQLről: mi a [Azure Database for PostgreSQL?](../postgresql/overview.md).
