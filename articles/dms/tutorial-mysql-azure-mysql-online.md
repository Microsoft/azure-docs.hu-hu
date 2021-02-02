---
title: 'Oktatóanyag: a MySQL online migrálása a Azure Database for MySQLba'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan végezheti el a helyszíni MySQL online áttelepítését, hogy Azure Database for MySQL a Azure Database Migration Service használatával.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 1d3ab2df51e80b44dce6057b02975fe210ebaa24
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254325"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Oktatóanyag: MySQL online migrálása az Azure Database for MySQL-be a DMS használatával

A Azure Database Migration Service segítségével telepítheti át az adatbázisokat egy helyszíni MySQL-példányból, hogy [Azure Database for MySQL](../mysql/index.yml) minimális állásidővel. Ez azt jelenti, hogy a migrálás az alkalmazás minimális ideig tartó leállásával végezhető el. Ebben az oktatóanyagban áttelepíti az **alkalmazottak** minta-adatbázisát a MySQL 5,7 helyszíni példányáról, hogy Azure Database for MySQL a Azure Database Migration Service Online áttelepítési tevékenységével.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * A mintaséma migrálása a mysqldump segédprogrammal.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * Az áttelepítés monitorozása.

> [!NOTE]
> A Azure Database Migration Service használata az online áttelepítés végrehajtásához a prémium szintű díjszabás alapján kell létrehoznia egy példányt.

> [!IMPORTANT]
> Az optimális áttelepítési élmény érdekében a Microsoft azt javasolja, hogy Azure Database Migration Service-példányt hozzon létre ugyanabban az Azure-régióban, mint a célként megadott adatbázis. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

> [!NOTE]
> Elfogultság – ingyenes kommunikáció
>
> A Microsoft sokféle és befogadó környezetet támogat. Ez a cikk a _Slave_ kifejezésre mutató hivatkozásokat tartalmaz. Az [elfogultság nélküli kommunikációhoz használható Microsoft-stílus útmutatója](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) ezt a kizáró szót ismeri fel. A szó a jelen cikkben a konzisztencia miatt használatos, mert jelenleg a szoftverben megjelenő szó. Ha a szoftver frissítve lett a szó eltávolítására, a rendszer a cikket úgy frissíti, hogy az legyen az igazítás.
>


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse a [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6-os vagy 5.7-es verzióját. A helyszíni MySQL-verziónak egyeznie kell az Azure Database for MySQL verziójával. Például a MySQL 5.6 csak az Azure Database for MySQL 5.6-ba migrálható, az 5.7-es verzióra nem frissíthető. A MySQL 8,0-ből vagy-ból való Migrálás nem támogatott.
* [Példány létrehozása az Azure Database for MySQL-ben](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Az adatbázisok az Azure Portal használatával való csatlakoztatásának és létrehozásának részleteiért tekintse meg az [Azure Database for MySQL: Csatlakozás és adatlekérdezés a MySQL Workbench használatával](../mysql/connect-workbench.md) című cikket.  
* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Servicehoz Azure Resource Manager üzembe helyezési modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](../expressroute/expressroute-introduction.md) vagy a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával. A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](../virtual-network/index.yml), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.

    > [!NOTE]
    > Ha a virtuális networkNet telepítésekor a ExpressRoute-t használja a Microsofthoz, adja hozzá a következő szolgáltatási [végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
    >
    > * Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont stb.)
    > * Tárolási végpont
    > * Service Bus-végpont
    >
    > Erre a konfigurációra azért van szükség, mert Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport szabályai nem gátolják meg a következő kimenő kommunikációs portok Azure Database Migration Service: 443, 53, 9354, 445, 12000. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/virtual-network-vnet-plan-design-arm.md)című cikket.
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules).
* Nyissa meg a Windows tűzfalat, hogy a Azure Database Migration Service hozzáférhessen a forrás MySQL-kiszolgálóhoz, amely alapértelmezés szerint a 3306-es TCP-port.
* Ha a forrásadatbázis (ok) előtt tűzfal-berendezést használ, előfordulhat, hogy olyan tűzfalszabályok hozzáadására van szükség, amelyek lehetővé teszik a Azure Database Migration Service számára a forrás-adatbázis (ok) elérését az áttelepítéshez.
* Hozzon létre egy kiszolgálói szintű [Tűzfalszabály-szabályt](../azure-sql/database/firewall-configure.md) a Azure Database for MySQL számára, hogy lehetővé tegye Azure Database Migration Service hozzáférést a célként megadott adatbázisokhoz. Adja meg a Azure Database Migration Service használt virtuális hálózat alhálózati tartományát.
* A forrásként szolgáló MySQL-nek a MySQL közösségi kiadásának kell lennie. A MySQL-példány verziójának megállapításához futtassa a következő parancsot a MySQL segédprogramban vagy a MySQL Workbenchben:

    ```
    SELECT @@version;
    ```

* Az Azure Database for MySQL csak az InnoDB-táblákat támogatja. Ha a MyISAM-táblákat InnoDB-táblákká szeretné alakítani, tekintse meg a [táblák MyISAM-ból InnoDB-vé konvertálását ismertető](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) részt

* Engedélyezze a bináris naplózást a forrásadatbázisban szereplő my.ini (Windows) vagy a my.cnf (Unix) fájlban az alábbi konfiguráció használatával:

  * **server_id** = 1 vagy nagyobb (csak a MySQL 5.6-os verziójára vonatkozik)
  * **log-bin** = \<path> (csak a MySQL 5,6 esetén releváns)    Például: log-bin = E:\ MySQL_logs \BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (javasolt, hogy ne használjon nullát, ami csak a MySQL 5,6 esetén szükséges)
  * **Binlog_row_image** = full (csak a MySQL 5.6-os verziójára vonatkozik)
  * **log_slave_updates** = 1

* A felhasználónak a ReplicationAdmin szerepkörrel kell rendelkeznie az alábbi jogosultságokkal:

  * **REPLICATION CLIENT** – Csak változásfeldolgozási feladatokhoz szükséges. Vagyis a csak teljes betöltési feladatokhoz nincs szükség erre a jogosultságra.
  * **REPLICATION REPLICA** – Csak változásfeldolgozási feladatokhoz szükséges. Vagyis a csak teljes betöltési feladatokhoz nincs szükség erre a jogosultságra.
  * **SUPER** – Kizárólag a MySQL 5.6.6-os verziójánál korábbi verziókban szükséges.

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása

Ahhoz, hogy az összes adatbázis-objektumot táblasémaként, indexekként és tárolt eljárásokként egészíthessük ki, ki kell bontanunk a forrásadatbázis sémáját, és alkalmaznunk kell az adatbázisra. A séma kibontásához használhatja a mysqldump segédprogramot a `--no-data` paraméterrel.

Feltételezve, hogy rendelkezik a MySQL- **alkalmazottak** mintaadatbázis használatával a helyszíni rendszerben, a mysqldump-t használó séma-áttelepítési parancs a következő:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Például:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Ha sémát szeretne importálni az Azure Database for MySQL célba, futtassa a következő parancsot:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Például:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz.  Futtassa a következő szkriptet a MySQL Workbenchben a drop Foreign Key szkript kibontásához és a külső kulcsú parancsfájl hozzáadásához.

```sql
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

Az idegen kulcs elvetéséhez futtassa a drop foreign key (második oszlop) szkriptet a lekérdezési eredményekben.

> [!NOTE]
> Az Azure DMS nem támogatja a lépcsőzetes hivatkozási műveletet, amely segít automatikusan törölni vagy frissíteni a gyermektábla egyező sorát, amikor egy sort törölnek vagy frissítenek a fölérendelt táblában. További információ a MySQL dokumentációjában található a [FOREIGN Key megkötések](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)című cikk hivatkozási műveletek című szakasza.
> Az Azure DMS-nek szüksége van arra, hogy a kezdeti adatterhelés során dobja el a külső kulcsra vonatkozó korlátozásokat a célként megadott adatbázis-kiszolgálón, és nem használhat hivatkozási műveleteket. Ha a számítási feladat a kapcsolódó alárendelt tábla ezen hivatkozási művelettel való frissítésének függvénye, akkor azt javasoljuk, hogy ehelyett végezzen el egy [memóriaképet és egy visszaállítást](../mysql/concepts-migrate-dump-restore.md) . 


> [!IMPORTANT]
> Ha biztonsági másolat használatával importálja az adatimportálást, távolítsa el manuálisan vagy a--skip-depontosító parancs használatával a mysqldump végrehajtásakor. A definomabb jogosultságok létrehozásához és korlátozásához Azure Database for MySQL.

Ha van egy trigger az adatokban (INSERT vagy Update trigger), akkor a rendszer az adatok integritását a forrástól kezdve a replikált adatok előtt kikényszeríti. Javasoljuk, hogy migráláskor tiltsa le a triggereket a cél minden táblájában, majd a migrálás végeztével engedélyezze őket ismét.

A céladatbázis eseményindítóinak letiltásához használja a következő parancsot:

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók** lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd a **Microsoft. DataMigration** jobb oldalán válassza a **regisztráció** lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás-SQL Server és a célként megadott Azure SQL Database példányhoz.

    Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md)című cikket.

5. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Azure Database Migration Service összes példányának megkeresése](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service példány nevét, majd válassza ki a példányt.

     ![Azure Database Migration Service példányának megkeresése](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az **Új migrálási projekt** képernyőn nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza ki a **MySQL**, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **AzureDbForMySQL** elemet.
5. A **Tevékenység típusának kiválasztása** szakaszban válassza az **Online adatok migrálása** lehetőséget.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > Másik lehetőségként választhatja a **projekt létrehozása** lehetőséget az áttelepítési projekt létrehozásához, és később végrehajthatja az áttelepítést.

6. Kattintson a **Mentés** gombra, jegyezze fel a DMS-sel történő sikeres adatmigráláshoz szükséges követelményeket, majd válassza a **Tevékenység létrehozása és futtatása** lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Forrás adatainak hozzáadása** képernyőn adja meg a forrásként szolgáló MySQL-példány kapcsolati adatait.

    ![A Forrás adatainak hozzáadása képernyő](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés** lehetőséget, majd a **Cél részletei** képernyőn adja meg a célul szolgáló Azure Database for MySQL-kiszolgáló kapcsolati adatait. Ez a cél az Azure Database for MySQL azon példánya, amelyen üzembe helyezte az **Employees** sémát a mysqldump segédprogrammal.

    ![A részleteket tartalmazó képernyő](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrás-adatbázis, akkor a Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázis-adatbázist.

    ![Leképezés céladatbázisokra](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)
   > [!NOTE] 
   > Bár ebben a lépésben több adatbázist is kiválaszthat, a Azure Database Migration Service minden példánya legfeljebb 4 adatbázist támogat egyidejű áttelepítéshez. Az előfizetések régiónként legfeljebb 10 példányt Azure Database Migration Service. Ha például az áttelepíteni kívánt 80-es adatbázissal rendelkezik, a 40-et áttelepítheti ugyanahhoz a régióhoz, de csak akkor, ha a Azure Database Migration Service 10 példányát hozta létre.

3. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik az áttelepítési tevékenység ablak, és a tevékenység **állapota** **inicializálva** van.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Befejezve** értékre.

     ![Tevékenység állapota: Befejezve](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. Az **Adatbázis neve** mezőben válassza ki az adatbázist, az **Adatok teljes betöltése** és a **Növekményes adatszinkronizálás** műveletek migrálási állapotának megtekintéséhez.

    Az Adatok teljes betöltésénél az első betöltés migrálási állapota jelenik meg, a Növekményes adatszinkronizálásnál pedig az Adatváltozások rögzítése (CDC) állapot látható.

     ![Tevékenység állapota – Teljes betöltés kész](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![Tevékenység állapota – Növekményes adatszinkronizálás](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

    ![Átállás indítása](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.
3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis migrálási állapota **Befejezve** értékre vált, csatlakoztassa alkalmazásait az új Azure SQL-céladatbázishoz.

## <a name="next-steps"></a>Következő lépések

* Az Azure Database for MySQL-be történő online migrálás végrehajtásakor felmerülő ismert hibákhoz és korlátozásokhoz kapcsolódó információk: [Az Azure Database for MySQL online migrálásával kapcsolatos ismert hibák és kerülő megoldások](known-issues-azure-mysql-online.md).
* További információ a Azure Database Migration Serviceről: mi a [Azure Database Migration Service?](./dms-overview.md).
* További információ a Azure Database for MySQLről: mi a [Azure Database for MySQL?](../mysql/overview.md).
