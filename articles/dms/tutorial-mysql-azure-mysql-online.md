---
title: 'Oktatóanyag: MySQL online mi áttelepítése Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan végezhet online migrálást a mySQL-ről Azure Database for MySQL mysql-be a Azure Database Migration Service.
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
ms.openlocfilehash: 754d8cc9e79bc100e87f56c6fc33102963e53e8d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818075"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Oktatóanyag: MySQL online migrálása az Azure Database for MySQL-be a DMS használatával

Az adatbázisokat Azure Database Migration Service mySQL-példányról minimális állásidővel át Azure Database for MySQL [](../mysql/index.yml) át a Azure Database for MySQL használatával. Ez azt jelenti, hogy a migrálás az alkalmazás minimális ideig tartó leállásával végezhető el. Ebben az oktatóanyagban az **Employees** mintaadatbázist a MySQL 5.7 egy helyszíni példányából a Azure Database for MySQL-be migráljuk egy online migrálási tevékenységgel a Azure Database Migration Service.

> [!IMPORTANT]
> A "MySQL to Azure Database for MySQL" online migrálási forgatókönyv 2021. június 1-én párhuzamos, nagy mértékben teljesítő offline migrálási forgatókönyvre lesz lecserélve. Online migrálások esetén használhatja ezt az új ajánlatot és [a beható adatreplikációt.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) Alternatív megoldásként használjon olyan nyílt forráskódú eszközöket, mint a [MyDumper/MyLoader,](https://centminmod.com/mydumper.html) és használjon adatreplikációt az online migráláshoz. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * A mintaséma migrálása a mysqldump segédprogrammal.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy migrálási projektet a Azure Database Migration Service.
> * A migrálás futtatása.
> * Az áttelepítés monitorozása.

> [!NOTE]
> Ha Azure Database Migration Service online migrálást hajt végre, akkor a Prémium tarifacsomag alapján kell létrehoznia egy példányt.

> [!IMPORTANT]
> Az optimális migrálási élmény érdekében a Microsoft azt javasolja, hogy hozzon létre egy Azure Database Migration Service a céladatbázissal azonos Azure-régióban. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

> [!NOTE]
> Torzításmentes kommunikáció
>
> A Microsoft sokféle és befogadó környezetet támogat. Ez a cikk a slave szóra mutató _hivatkozásokat tartalmaz._ A Microsoft [torzításmentes kommunikációra](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) vonatkozó stíluskalauza ezt kizárási szóként ismeri fel. Ebben a cikkben a konzisztencia szót használjuk, mert jelenleg ez jelenik meg a szoftverben. Ha a szoftver frissítve lett, hogy eltávolítsa a szót, a cikk a megfelelőnek megfelelően frissül.
>


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse a [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6-os vagy 5.7-es verzióját. A helyszíni MySQL-verziónak egyeznie kell az Azure Database for MySQL verziójával. Például a MySQL 5.6 csak az Azure Database for MySQL 5.6-ba migrálható, az 5.7-es verzióra nem frissíthető. A MySQL 8.0-ra vagy onnan más verzióra való migrálás nem támogatott.
* [Példány létrehozása az Azure Database for MySQL-ben](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Az adatbázisok Workbench alkalmazással való csatlakoztatásával és létrehozásával kapcsolatos részletekért tekintse meg a Csatlakozás és adatok lekérdezése a [MySQL Workbench](../mysql/connect-workbench.md) használatával cikket.  
* Hozzon létre egy Microsoft Azure Virtual Network Azure Database Migration Service-kiszolgálóhoz egy üzembe helyezési modellel, amely [expressRoute](../expressroute/expressroute-introduction.md) vagy VPN használatával biztosítja Azure Resource Manager helyszíni forráskiszolgálókhoz való hely–hely [kapcsolatot.](../vpn-gateway/vpn-gateway-about-vpngateways.md) A virtuális hálózatok létrehozásával kapcsolatos további [](../virtual-network/index.yml)információkért tekintse meg a Virtual Network dokumentációját, és különösen a részletes információkat tartalmazó gyorsútmutató-cikkeket.

    > [!NOTE]
    > A virtuális hálózat beállítása során, ha az ExpressRoute-et a [](../virtual-network/virtual-network-service-endpoints-overview.md) Microsofttal való hálózati társviszony-létesítés során használja, adja hozzá a következő szolgáltatásvégpontokat ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
    >
    > * Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont és így tovább)
    > * Tárolási végpont
    > * Service Bus-végpont
    >
    > Erre a konfigurációra azért van szükség, mert Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózat hálózati biztonsági csoport szabályai nem blokkolják a ServiceTag 443-as kimenő portját a ServiceBus, a Storage és az AzureMonitor számára. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részletekért tekintse meg a Hálózati forgalom szűrése hálózati [biztonsági csoportokkal cikket.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, hogy Azure Database Migration Service a MySQL Server forráshoz, amely alapértelmezés szerint a 3306-os TCP-port.
* Ha tűzfalberendezést használ a forrásadatbázis(ak) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia ahhoz, hogy a Azure Database Migration Service hozzáférjenek a forrásadatbázis(okkal) a migráláshoz.
* Hozzon létre egy kiszolgálószintű [tűzfalszabályt](../azure-sql/database/firewall-configure.md) a Azure Database for MySQL, hogy Azure Database Migration Service a céladatbázishoz. Adja meg a virtuális hálózat alhálózati tartományát, Azure Database Migration Service.
* A forrásként szolgáló MySQL-nek a MySQL közösségi kiadásának kell lennie. A MySQL-példány verziójának megállapításához futtassa a következő parancsot a MySQL segédprogramban vagy a MySQL Workbenchben:

    ```
    SELECT @@version;
    ```

* Az Azure Database for MySQL csak az InnoDB-táblákat támogatja. Ha a MyISAM-táblákat InnoDB-táblákká szeretné alakítani, tekintse meg a [táblák MyISAM-ból InnoDB-vé konvertálását ismertető](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) részt

* Engedélyezze a bináris naplózást a forrásadatbázisban szereplő my.ini (Windows) vagy a my.cnf (Unix) fájlban az alábbi konfiguráció használatával:

  * **server_id** = 1 vagy nagyobb (csak a MySQL 5.6-os verziójára vonatkozik)
  * **naplógyűjtő** = \<path> (csak a MySQL 5.6 esetén releváns)    Például: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (javasoljuk, hogy ne használja a nullát; csak a MySQL 5.6 esetén releváns)
  * **Binlog_row_image** = full (csak a MySQL 5.6-os verziójára vonatkozik)
  * **log_slave_updates** = 1

* A felhasználónak a ReplicationAdmin szerepkörrel kell rendelkeznie az alábbi jogosultságokkal:

  * **REPLICATION CLIENT** – Csak változásfeldolgozási feladatokhoz szükséges. Vagyis a csak teljes betöltési feladatokhoz nincs szükség erre a jogosultságra.
  * **REPLICATION REPLICA** – Csak változásfeldolgozási feladatokhoz szükséges. Vagyis a csak teljes betöltési feladatokhoz nincs szükség erre a jogosultságra.
  * **SUPER** – Kizárólag a MySQL 5.6.6-os verziójánál korábbi verziókban szükséges.

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása

Ahhoz, hogy az összes adatbázis-objektumot táblasémaként, indexekként és tárolt eljárásokként egészíthessük ki, ki kell bontanunk a forrásadatbázis sémáját, és alkalmaznunk kell az adatbázisra. A séma kibontásához használhatja a mysqldump segédprogramot a `--no-data` paraméterrel.

Feltételezve, hogy a mySQL **Employees** mintaadatbázis a helyszíni rendszerben található, a séma migrálásának parancsa a mysqldump használatával a következő:

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

Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz.  Hajtsa végre a következő szkriptet a MySQL Workbenchben a drop foreign key szkript kinyerés és a idegenkulcs-hozzáadási szkript kinyerésében.

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
> Az Azure DMS nem támogatja a CASCADE hivatkozási műveletet, amely segít automatikusan törölni vagy frissíteni egy megfelelő sort a gyermektáblában egy sor szülőtáblában való törlésekor vagy frissítésekor. További információt a MySQL dokumentációjának FOREIGN KEY Constraints (IDEGEN KULCSkorlátozások) című cikk Referential Actions (Hivatkozási műveletek) [szakaszában talál.](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)
> Az Azure DMS megköveteli, hogy a kezdeti adatbetöltés során elejtse a céladatbázis-kiszolgálóra vonatkozó idegenkulcs-korlátozásokat, és nem használhat hivatkozási műveleteket. Ha a számítási feladat egy kapcsolódó gyermektábla ezen a hivatkozási műveleten keresztüli frissítésétől függ, javasoljuk, hogy inkább memóriaképet és visszaállítást [hajtson](../mysql/concepts-migrate-dump-restore.md) végre. 


> [!IMPORTANT]
> Ha biztonsági mentéssel importál adatokat, manuálisan vagy a --skip-definer paranccsal távolítsa el a CREATE DEFINER parancsokat a mysqldump végrehajtásakor. A DEFINER-nek felügyelői jogosultságra van szüksége a létrehozásához, és korlátozott a Azure Database for MySQL.

Ha az adatbázisban eseményindítók vannak, az kikényszeríteni fogja az adatok integritását a célban a forrásból való teljes adatáttelepítés előtt. Javasoljuk, hogy a migrálás során tiltsa le az eseményindítókat a célban lévő összes táblán, majd engedélyezze az eseményindítókat a migrálás után.

Hajtsa végre az alábbi szkriptet a MySQL Workbenchben a céladatbázison az eseményindító-szkript kibontása és az eseményindító-szkript hozzáadása érdekében.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Futtassa a létrehozott drop trigger lekérdezést (DropQuery oszlop) az eredményben az eseményindítók céladatbázisban való eldobása érdekében. Az eseményindító hozzáadása lekérdezés menthető, és az adatáttelepítés befejezése után használható.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-mysql-to-azure-mysql-online/01-portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyben létre szeretné hozni a Azure Database Migration Service, majd válassza az **Erőforrás-szolgáltatók lehetőséget.**

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-mysql-to-azure-mysql-online/02-01-portal-select-resource-provider.png)

3. Keressen rá a migrálás kifejezésre, majd a **Microsoft.DataMigration** jobb oldalon válassza a **Regisztráció lehetőséget.**

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-mysql-to-azure-mysql-online/02-02-portal-register-resource-provider.png)

## <a name="create-a-database-migration-service-instance"></a>Új Database Migration Service létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-mysql-to-azure-mysql-online/03-dms-portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-mysql-to-azure-mysql-online/04-dms-portal-marketplace-create.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válasszon ki egy tarifacsomagot, és lépjen a hálózati képernyőre. Az offline migrálás a Standard és a Prémium tarifacsomagban is elérhető.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Alapszintű Azure Database Migration Service konfigurálása](media/tutorial-mysql-to-azure-mysql-online/05-dms-portal-create-basic.png)

5. Válasszon ki egy meglévő virtuális hálózatot a listából, vagy adja meg a létrehozni kívánt új virtuális hálózat nevét. Lépjen az áttekintés + létrehozás képernyőre. A címkék képernyő használatával címkéket is hozzáadhat a szolgáltatáshoz.

    A virtuális hálózat hozzáférést Azure Database Migration Service a forrás- SQL Server a célként Azure SQL Database példányhoz.

    ![Hálózati Azure Database Migration Service konfigurálása](media/tutorial-mysql-to-azure-mysql-online/06-dms-portal-create-networking.png)

    További információ a virtuális hálózatok létrehozásáról a Azure Portal: Virtuális hálózat létrehozása [a](../virtual-network/quick-create-portal.md)Azure Portal.

6. Tekintse át a konfigurációkat, és **válassza a Létrehozás lehetőséget** a szolgáltatás létrehozásához.
    
    ![Azure Database Migration Service létrehozása](media/tutorial-mysql-to-azure-mysql-online/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.  

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![A példányok összes példányának Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/08-01-dms-portal-search-service.png)

2. Válassza ki a migrálási szolgáltatás példányát a keresési eredmények közül, majd válassza az + **Új migrálási projekt lehetőséget.**
    
    ![Új migrálási projekt létrehozása](media/tutorial-mysql-to-azure-mysql-online/08-02-dms-portal-new-project.png)

3. Az  Új migrálási projekt képernyőn adja meg  a projekt nevét, majd a Forráskiszolgáló típusa választómezőben válassza a **MySQL**  lehetőséget, majd a Célkiszolgáló típusa választómezőben válassza az Azure Database **for MySQL** lehetőséget, majd a Migrálási tevékenység típusa választómezőben válassza az **Online** adatáttelepítés lehetőséget.  Válassza a **Tevékenység létrehozása és futtatása** lehetőséget.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-mysql-to-azure-mysql-online/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Azt is választhatja, hogy a Csak projekt létrehozása **lehetőséget** választja a migrálási projekt létrehozásához, és később végrehajtja a migrálást.

## <a name="configure-migration-project"></a>Migrálási projekt konfigurálása

1. A Forrás **kiválasztása képernyőn** adja meg a forrás MySQL-példány kapcsolati adatait, majd válassza a **Tovább: Cél** kiválasztása lehetőséget>>

    ![Forrásadatok hozzáadása képernyő](media/tutorial-mysql-to-azure-mysql-online/10-dms-portal-project-mysql-source.png)

2. A Cél **kiválasztása képernyőn** adja meg a célpéldány kapcsolati Azure Database for MySQL, majd válassza a **Tovább: Válassza** ki a>>

    ![Céladatok hozzáadása képernyő](media/tutorial-mysql-to-azure-mysql-online/11-dms-portal-project-mysql-target.png)

3. Az Adatbázisok **kiválasztása képernyőn** leképezheti a forrás- és a céladatbázist az áttelepítéshez, majd válassza a **Tovább: Áttelepítési** beállítások konfigurálása>>. Ha a  Forráskiszolgáló csak olvashatóként beállítást választja, a forrás csak olvasható lesz, de ügyeljen rá, hogy ez egy kiszolgálószintű beállítás. Ha be van jelölve, a teljes kiszolgálót írásra állítja, nem csak a kiválasztott adatbázisokat.
    
    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrásadatbázis, Azure Database Migration Service a céladatbázist választja ki alapértelmezés szerint.
    ![Adatbázis részleteinek kiválasztása képernyő](media/tutorial-mysql-to-azure-mysql-online/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
   > Bár ebben a lépésben több adatbázist is kiválaszthat, a Azure Database Migration Service minden példánya legfeljebb 4 adatbázist támogat az egyidejű migráláshoz. Emellett előfizetésenként legfeljebb 10 példány Azure Database Migration Service régiónként. Ha például 80 adatbázist kell átemelni, akkor 40 adatbázist mi egyiket egyidejűleg is át lehet milegálni ugyanoda a régióba, de csak akkor, ha 10 példányt hozott létre a Azure Database Migration Service.

4. A Configure **migration settings (Áttelepítési beállítások konfigurálása)** képernyőn válassza ki a migrálás részétni kívánt táblákat, majd válassza a **Next : Summary (Tovább: Összegzés)>>.** Ha a céltáblák tartalmaznak adatokat, alapértelmezés szerint nincsenek kiválasztva, de explicit módon kijelölheti őket, és a migrálás megkezdése előtt csonkolva lesznek.

    ![Táblák kiválasztása képernyő](media/tutorial-mysql-to-azure-mysql-online/13-dms-portal-project-mysql-select-tbl.png)

5. Az Összefoglalás **képernyő** Tevékenység  neve szövegmezőjében adja meg a migrálási tevékenység nevét, és tekintse át az összegzést, hogy a forrás és a cél adatai megegyeznek-e a korábban megadott adatokval.

    ![Migrálási projekt áttekintése](media/tutorial-mysql-to-azure-mysql-online/14-dms-portal-project-mysql-activity-summary.png)

6. Válassza a **Migrálás kezdete lehetőséget.** Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**. A **tábla migrálásának** **kezdetekor** az Állapot Fut állapotra változik.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Befejezve** értékre.

     ![Tevékenység állapota: Befejezve](media/tutorial-mysql-to-azure-mysql-online/15-dms-activity-completed.png)

2. Az **Adatbázis neve** mezőben válassza ki az adatbázist, az **Adatok teljes betöltése** és a **Növekményes adatszinkronizálás** műveletek migrálási állapotának megtekintéséhez.

    Az Adatok teljes betöltésénél az első betöltés migrálási állapota jelenik meg, a Növekményes adatszinkronizálásnál pedig az Adatváltozások rögzítése (CDC) állapot látható.

     ![Tevékenység állapota – Teljes betöltés kész](media/tutorial-mysql-to-azure-mysql-online/16-dms-activity-full-load-completed.png)

     ![Tevékenység állapota – Növekményes adatszinkronizálás](media/tutorial-mysql-to-azure-mysql-online/17-dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

    ![Átállás indítása](media/tutorial-mysql-to-azure-mysql-online/18-dms-start-cutover.png)

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.
3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis migrálási állapota **Befejezve** értékre vált, csatlakoztassa alkalmazásait az új Azure SQL-céladatbázishoz.

## <a name="next-steps"></a>Következő lépések

* Az Azure Database for MySQL-be történő online migrálás végrehajtásakor felmerülő ismert hibákhoz és korlátozásokhoz kapcsolódó információk: [Az Azure Database for MySQL online migrálásával kapcsolatos ismert hibák és kerülő megoldások](known-issues-azure-mysql-online.md).
* További információt a Azure Database Migration Service a Mi a [Azure Database Migration Service? cikkben talál.](./dms-overview.md)
* További információt a Azure Database for MySQL a Mi a [Azure Database for MySQL? cikkben talál.](../mysql/overview.md)
