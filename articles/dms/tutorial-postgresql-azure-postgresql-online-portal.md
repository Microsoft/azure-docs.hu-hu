---
title: 'Oktatóanyag: a PostgreSQL migrálása az Azure DB for PostgreSQL online-ba a Azure Portal használatával'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan végezheti el a helyszíni PostgreSQL-ről való online áttelepítést, hogy az a Azure Portal használatával Azure Database Migration Service segítségével Azure Database for PostgreSQL.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: 66a2bf17ac7cb94dc8e1429d662d7bfe14aa196a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096307"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Oktatóanyag: a PostgreSQL migrálása az Azure DB for PostgreSQL online-ba a DMS használatával a Azure Portalon keresztül

A Azure Database Migration Service segítségével telepítheti át az adatbázisokat egy helyszíni PostgreSQL-példányból, hogy [Azure Database for PostgreSQL](../postgresql/index.yml) az alkalmazás minimális állásidővel. Ebben az oktatóanyagban áttelepíti a **DVD-kölcsönzési** minta adatbázisát a PostgreSQL 9,6 helyszíni példányáról, hogy Azure Database for PostgreSQL a Azure Database Migration Service Online áttelepítési tevékenységének használatával.

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

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse a [PostgreSQL Community edition](https://www.postgresql.org/download/) 9,4, 9,5, 9,6 vagy 10 verziót. A forrás PostgreSQL-kiszolgáló verziószámának 9,4, 9,5, 9,6, 10 vagy 11 értéknek kell lennie. További információkért lásd a [PostgreSQL-adatbázisok támogatott verzióit](../postgresql/concepts-supported-versions.md)ismertető cikket.

    Azt is vegye figyelembe, hogy a cél Azure Database for PostgreSQL verziószámának vagy annál későbbinek kell lennie, mint a helyszíni PostgreSQL-verziónak. Például a PostgreSQL 9,6 Azure Database for PostgreSQL 9,6, 10 vagy 11 rendszerre telepíthető, de nem Azure Database for PostgreSQL 9,5.

* [Hozzon létre egy Azure Database for PostgreSQL kiszolgálót](../postgresql/quickstart-create-server-database-portal.md) , vagy [hozzon létre egy Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgálót](../postgresql/quickstart-create-hyperscale-portal.md).
* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Service számára a Azure Resource Manager üzemi modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](../expressroute/expressroute-introduction.md) vagy a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával. A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](../virtual-network/index.yml), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.

    > [!NOTE]
    > Ha a virtuális hálózat beállítása során ExpressRoute használ a Microsoft számára, adja hozzá a következő szolgáltatási [végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
    >
    > * Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont stb.)
    > * Tárolási végpont
    > * Service Bus-végpont
    >
    > Erre a konfigurációra azért van szükség, mert Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózat hálózati biztonsági csoport (NSG) szabályai nem blokkolják a ServiceBus, a Storage és a AzureMonitor ServiceTag 443-es kimenő portját. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/virtual-network-vnet-plan-design-arm.md)című cikket.
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, hogy a Azure Database Migration Service hozzáférhessen a forrás PostgreSQL-kiszolgálóhoz, amely alapértelmezés szerint a 5432-es TCP-port.
* Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
* Hozzon létre egy kiszolgálói szintű Azure Database for PostgreSQL [Tűzfalszabály](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules) , amely lehetővé teszi, hogy a Azure Database Migration Service hozzáférjen a célként megadott adatbázisokhoz. Adja meg a Azure Database Migration Service használt virtuális hálózat alhálózati tartományát.
* Engedélyezze a logikai replikálást a postgresql.config fájlban, és állítsa be a következő paramétereket:

  * wal_level = **logical**
  * max_replication_slots = [bővítőhelyek száma], javasolt beállítás **öt bővítőhelyre**
  * max_wal_senders = [párhuzamos feladatok száma] – a max_wal_senders paraméter megadja a párhuzamosan futtatható feladatok számát, az ajánlott beállítás **10 feladat**

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
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
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
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. A drop Foreign Key parancsfájl kibontásához és a célhelyhez (Azure Database for PostgreSQL) való hozzáadásához futtassa a következő szkriptet a PgAdmin vagy a psql alkalmazásban.

   > [!IMPORTANT]
   > A séma külső kulcsai az áttelepítés kezdeti terhelését és folyamatos szinkronizálását okozzák.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
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

   ![Portál-előfizetések megtekintése](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók** lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd a **Microsoft. DataMigration** jobb oldalán válassza a **regisztráció** lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Az **áttelepítési szolgáltatás létrehozása** képernyőn adjon meg egy nevet, egy előfizetést, egy új vagy egy meglévő erőforráscsoportot, valamint a szolgáltatás helyét.

4. Válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás PostgreSQL-kiszolgálóhoz és a cél Azure Database for PostgreSQL példányhoz.

    Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md)című cikket.

5. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Válassza a **felülvizsgálat + létrehozás** lehetőséget a szolgáltatás létrehozásához.

   A szolgáltatás létrehozása körülbelül 10 – 15 percen belül elvégezhető.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Azure Database Migration Service összes példányának megkeresése](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service példány nevét, válassza ki a példányt, majd válassza az + **új áttelepítési projekt** lehetőséget.

3. Az **új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **forráskiszolgáló típusa** szövegmezőben válassza a **PostgreSQL** lehetőséget a **célkiszolgáló típusa** szövegmezőben, majd válassza a **Azure Database for PostgreSQL** lehetőséget.

4. A **tevékenység típusának** kiválasztása szakaszban válassza az **online adatáttelepítés** lehetőséget.

    ![Azure Database Migration Service projekt létrehozása](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Másik lehetőségként választhatja a **projekt létrehozása** lehetőséget az áttelepítési projekt létrehozásához, és később végrehajthatja az áttelepítést.

5. Válassza a **Save (Mentés**) lehetőséget, és jegyezze fel az adatáttelepítés Azure Database Migration Service sikeres használatát, majd válassza a **Létrehozás és Futtatás tevékenység** lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **forrás hozzáadása részletek** képernyőn adja meg a forrás PostgreSQL-példány kapcsolati adatait.

    ![A Forrás adatainak hozzáadása képernyő](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. A **cél részletei** képernyőn válassza ki a cél nagy kapacitású (Citus) kiszolgáló kapcsolati adatait, amely a nagy kapacitású (Citus) előre kiépített példánya, amelyre a **DVD-kölcsönzési** sémát a pg_dump használatával telepítették.

    ![A részleteket tartalmazó képernyő](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrás-adatbázis, akkor a Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázis-adatbázist.

    ![Leképezés a céladatbázis-adatbázisokra képernyő](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Válassza a **Mentés** lehetőséget, majd az **áttelepítési beállítások** képernyőn fogadja el az alapértelmezett értékeket.

    ![Áttelepítési beállítások képernyő](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![Áttelepítési összesítő képernyő](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik az áttelepítési tevékenység ablak, és a tevékenység **állapota** úgy frissül, hogy a **biztonsági mentés folyamatban van**.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Befejezve** értékre.

     ![Az áttelepítési folyamat figyelése](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Ha az áttelepítés befejeződött, az **adatbázis neve** területen válasszon ki egy adott adatbázist, amely a **teljes adatterheléshez** és a **növekményes adatszinkronizálási** műveletekhez szükséges áttelepítési állapotba kerül.

   > [!NOTE]
   > A **teljes adatterhelés** megjeleníti a kezdeti betöltési áttelepítés állapotát, míg a **növekményes adatszinkronizálás** megjeleníti az adatváltozások rögzítésére (CDC) vonatkozó állapotot.

     ![Teljes adatterhelés részletei](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Növekményes adatok szinkronizálásának részletei](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

2. Várjon, amíg a **függőben lévő változások** számlálója **0-ra** mutat, hogy a forrásadatbázis összes bejövő tranzakciója le legyen állítva, jelölje be a Confirm ( **megerősítés** ) jelölőnégyzetet, majd kattintson az **alkalmaz** gombra.

    ![Teljes átváltás képernyő](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Ha az adatbázis-áttelepítési **állapot megjelenik,** [hozza létre](https://wiki.postgresql.org/wiki/Fixing_Sequences) újra a sorozatot (ha van ilyen), és az alkalmazásait az Azure Database for PostgreSQL új célként megadott példányához kapcsolja.

## <a name="next-steps"></a>Következő lépések

* Az Azure Database for PostgreSQL-be történő online migrálás végrehajtásakor felmerülő ismert hibákhoz és korlátozásokhoz kapcsolódó információk: [Az Azure Database for PostgreSQL online migrálásával kapcsolatos ismert hibák és kerülő megoldások](known-issues-azure-postgresql-online.md).
* Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](./dms-overview.md).
* További információ a Azure Database for PostgreSQLről: mi a [Azure Database for PostgreSQL?](../postgresql/overview.md).
