---
title: 'Oktatóanyag: az RDS MySQL online migrálása a Azure Database for MySQLba'
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan hajthat végre egy online áttelepítést az RDS MySQL-ről Azure Database for MySQLre a Azure Database Migration Service használatával.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 06/09/2020
ms.openlocfilehash: 3f2efd4051b427a4d7cef0e609f733095c6b020f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259185"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Oktatóanyag: RDS MySQL online migrálása az Azure Database for MySQL-be a DMS használatával

A Azure Database Migration Service segítségével telepíthet át adatbázisokat egy RDS MySQL-példányból, hogy [Azure Database for MySQL](../mysql/index.yml) , amíg a forrásadatbázis online állapotban marad az áttelepítés során. Más szóval a Migrálás az alkalmazás minimális állásidővel is elérhető. Ebben az oktatóanyagban áttelepíti az **alkalmazottak** minta adatbázisát az RDS MySQL egy példányáról, hogy Azure Database for MySQL a Azure Database Migration Service Online áttelepítési tevékenységének használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Telepítse át a minta sémát a mysqldump és a MySQL segédprogram használatával.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * Az áttelepítés monitorozása.

> [!NOTE]
> A Azure Database Migration Service használata az online áttelepítés végrehajtásához a prémium szintű díjszabás alapján kell létrehoznia egy példányt. További információkért tekintse meg a Azure Database Migration Service [díjszabását](https://azure.microsoft.com/pricing/details/database-migration/) ismertető oldalt.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk azt ismerteti, hogyan hajtható végre az online áttelepítés az RDS MySQL-példányból a Azure Database for MySQLba.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Győződjön meg arról, hogy a forrás MySQL-kiszolgáló támogatott MySQL Community Edition verziót futtat. A MySQL-példány verziójának meghatározásához a MySQL segédprogramban vagy a MySQL Workbenchben futtassa a következő parancsot:

    ```
    SELECT @@version;
    ```

    További információ: [támogatott Azure Database for MySQL verziók](../mysql/concepts-supported-versions.md).

* Töltse le és telepítse a [MySQL **Employees** mintaadatbázis-adatbázist](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* [Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)-példány létrehozása.
* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Service számára a Azure Resource Manager üzemi modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](../expressroute/expressroute-introduction.md) vagy a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával. A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](../virtual-network/index.yml), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.
* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport szabályai nem gátolják meg a következő kimenő kommunikációs portok Azure Database Migration Service: 443, 53, 9354, 445 és 12000. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/virtual-network-vnet-plan-design-arm.md)című cikket.
* Konfigurálja a [Windows tűzfalat](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) (vagy a Linux tűzfalat) az adatbázismotor-hozzáférés engedélyezéséhez. A MySQL-kiszolgáló esetében engedélyezze a 3306-es portot a kapcsolathoz.

> [!NOTE]
> Azure Database for MySQL csak a InnoDB táblákat támogatja. A MyISAM-táblák InnoDB való átalakításához tekintse meg a [táblák konvertálása a MyISAM-](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) ből a InnoDB-be című cikket.

### <a name="set-up-aws-rds-mysql-for-replication"></a>Az AWS RDS MySQL beállítása replikáláshoz

1. Új paraméterérték létrehozásához kövesse az AWS-ben a [MySQL-adatbázis naplófájljai](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)című cikkben szereplő utasításokat a **bináris naplózási formátum** szakaszban.
2. Hozzon létre egy új paraméter-csoportot a következő konfigurációval:
    * log_bin = bekapcsolva
    * binlog_format = row
    * binlog_checksum = nincs
3. Mentse az új paraméter csoportot.
4. Társítsa az új paraméter csoportot az RDS MySQL-példánnyal. Lehetséges, hogy újraindítás szükséges.
5. Ha a paraméterérték be van állítva, kapcsolódjon a MySQL-példányhoz, és [állítsa be a BinLog megőrzését](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_set_configuration.html#mysql_rds_set_configuration-usage-notes.binlog-retention-hours) legalább 5 napra.
```
call mysql.rds_set_configuration('binlog retention hours', 120);
```

## <a name="migrate-the-schema"></a>A séma migrálása

1. Bontsa ki a sémát a forrás-adatbázisból, és alkalmazza a célként megadott adatbázisra az összes adatbázis-objektum, például a táblázat sémái, indexek és tárolt eljárások áttelepítésének befejezéséhez.

    Csak a séma legegyszerűbb áttelepíteni a mysqldump és a--No-adatparamétert használja. A séma áttelepíthető parancsa a következő:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Például az **Employees** adatbázishoz tartozó sémafájl kiírásához használja az alábbi parancsot:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importálja a sémát a cél szolgáltatásba, amely Azure Database for MySQL. A séma memóriaképfájl-fájljának visszaállításához futtassa a következő parancsot:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Például az **Employees** adatbázis sémájának importálásához:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz. A drop Foreign Key szkript kibontásához és a külső kulcsú parancsfájl hozzáadásához a célhelyen (Azure Database for MySQL) futtassa a következő szkriptet a MySQL Workbenchben:

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
                    KCU.TABLE_NAME,
                    KCU.COLUMN_NAME,
                    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
                    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
                    WHERE
                      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
                      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. A külső kulcs eldobásához futtassa a legördülő idegen kulcsot (amely a második oszlop) a lekérdezési eredményben.

> [!NOTE]
> Az Azure DMS nem támogatja a lépcsőzetes hivatkozási műveletet, amely segít automatikusan törölni vagy frissíteni a gyermektábla egyező sorát, amikor egy sort törölnek vagy frissítenek a fölérendelt táblában. További információ a MySQL dokumentációjában található a [FOREIGN Key megkötések](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)című cikk hivatkozási műveletek című szakasza.
> Az Azure DMS-nek szüksége van arra, hogy a kezdeti adatterhelés során dobja el a külső kulcsra vonatkozó korlátozásokat a célként megadott adatbázis-kiszolgálón, és nem használhat hivatkozási műveleteket. Ha a számítási feladat a kapcsolódó alárendelt tábla ezen hivatkozási művelettel való frissítésének függvénye, akkor azt javasoljuk, hogy ehelyett végezzen el egy [memóriaképet és egy visszaállítást](../mysql/concepts-migrate-dump-restore.md) . 

5. Ha az adatokban eseményindítók (INSERT vagy Update trigger) szerepelnek, akkor az adatok a forrásból való replikálása előtt kényszerítve lesznek az adatok integritására a célhelyen. Javasoljuk, hogy az áttelepítés során tiltsa le az eseményindítókat a *célhelyen* lévő összes táblában, majd engedélyezze az eseményindítókat az áttelepítés befejeződése után.

    Eseményindítók letiltása a céladatbázisben:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Ha bármely táblában szerepelnek az ENUMERÁLÁS adattípus példányai, javasoljuk, hogy ideiglenesen frissítsen a "Character változó" adattípusra a cél táblában. Amikor az adatreplikálás befejeződött, állítsa az adattípust az ENUM értékre.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók** lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd a **Microsoft. DataMigration** jobb oldalán válassza a **regisztráció** lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Egy Azure Database Migration Service-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, amelyben létre szeretné hozni a Azure Database Migration Service példányát.

5. Válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás MySQL-példányhoz és a cél Azure Database for MySQL példányhoz.

    Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md)című cikket.

6. Válasszon árképzési szintet; az online áttelepítés esetében ügyeljen arra, hogy a prémium: 4vCores díjszabási szintet válassza.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.

     ![Az Azure Database Migration Service személyes példányának megkeresése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az **új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **forráskiszolgáló típusa** szövegmezőben válassza a **MySQL** lehetőséget, majd a **célkiszolgáló típusa** szövegmezőben válassza a **AzureDbForMySQL** lehetőséget.
5. A **tevékenység típusának** kiválasztása szakaszban válassza az **online adatáttelepítés** lehetőséget.

    > [!IMPORTANT]
    > Ügyeljen arra, hogy **online adatáttelepítést** válasszon; az offline áttelepítések nem támogatottak ebben a forgatókönyvben.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Másik lehetőségként választhatja a **projekt létrehozása** lehetőséget az áttelepítési projekt létrehozásához, és később végrehajthatja az áttelepítést.

6. Kattintson a **Mentés** gombra.

7. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

    > [!NOTE]
    > Jegyezze fel az online áttelepítés beállításához szükséges előfeltételeket a projekt létrehozása panelen.

## <a name="specify-source-details"></a>Forrás adatainak megadása

* Az **áttelepítési forrás részletei** képernyőn válassza ki a forrás MySQL-példány kapcsolati adatait.

   ![Forrás részletei](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés** lehetőséget, majd a **cél részletei** képernyőn adja meg a cél Azure Database for MySQL-kiszolgáló kapcsolati adatait, amely előre kiépített, és az **alkalmazottak** sémája a MySQLDump használatával van üzembe helyezve.

    ![Cél kiválasztása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrás-adatbázis, akkor a Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázis-adatbázist.

    ![Leképezés céladatbázisokra](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Fut** értékre.

    ![Tevékenység állapota – fut](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Az **adatbázis neve** területen válasszon ki egy adott adatbázist, amely a **teljes adatterhelés** és a **növekményes adatszinkronizálási** műveletek áttelepítési állapotát adja meg.

    A **teljes adatterhelés** megjeleníti a kezdeti betöltési áttelepítés állapotát, míg a **növekményes adatszinkronizálás** megjeleníti az adatváltozások rögzítésére (CDC) vonatkozó állapotot.

    ![Leltár képernyő – teljes adatterhelés](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Leltár képernyő – növekményes adatszinkronizálás](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

A kezdeti teljes terhelés befejezése után az adatbázisok **készen** állnak a átváltás.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

    ![Kivágás megkezdése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.
3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis-áttelepítési **állapot megjelenik,** az alkalmazásokat az új cél Azure Database for MySQL adatbázishoz kapcsolja.

A Azure Database for MySQL MySQL helyi példányának online áttelepítése most már befejeződött.

## <a name="next-steps"></a>Következő lépések

* Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](./dms-overview.md).
* További információ a Azure Database for MySQLről: mi a [Azure Database for MySQL?](../mysql/overview.md).
* Ha további kérdése van, küldjön e-mailt az [Azure Database áttelepítési](mailto:AskAzureDatabaseMigrations@service.microsoft.com) aliasra.
