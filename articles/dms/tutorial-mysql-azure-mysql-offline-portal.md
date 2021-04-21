---
title: 'Oktatóanyag: MySQL offline Azure Database for MySQL A DMS használatával'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan végezhet offline migrálást a mySQL-ről a Azure Database for MySQL a Azure Database Migration Service.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 71363771359ffef0ff165bd4a6744d864ea1856c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819648"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-offline-using-dms"></a>Oktatóanyag: MySQL offline Azure Database for MySQL A DMS használatával

A Azure Database Migration Service egy alkalommal teljes adatbázis-migrálást hajthat végre a helyszíni MySQL-példányon, [Azure Database for MySQL](../mysql/index.yml) nagy sebességű adatáttelepítési képességgel. Ebben az oktatóanyagban egy mintaadatbázist migrálunk a MySQL 5.7 egy helyszíni példányából az Azure Database for MySQL -be (5.7-es) egy offline migrálási tevékenység használatával a Azure Database Migration Service. Bár a cikkek feltételezik, hogy a forrás egy MySQL-adatbázispéldány, és Azure Database for MySQL célként szolgál, használható az egyik Azure Database for MySQL-ból egy másikba való áttelepítéshez csupán a forráskiszolgáló nevének és hitelesítő adatainak módosításával. Emellett az alacsonyabb verziójú MySQL-kiszolgálókról (5.6-os és újabb verziók) magasabb verziókra való migrálás is támogatott.

> [!IMPORTANT]
> Online migrálások esetén használhatja ezt az új ajánlatot és [a beható adatreplikációt.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) Másik lehetőségként használjon nyílt forráskódú eszközöket, például a [MyDumpert vagy a MyLoadert](https://centminmod.com/mydumper.html) az online migrálások során a beható adatreplikációval. 

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Adatbázissémát telepít át a mysqldump segédprogrammal.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy migrálási projektet a Azure Database Migration Service.
> * A migrálás futtatása.
> * Az áttelepítés monitorozása.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Aktív előfizetéssel rendelkezik egy Azure-fiókkal. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free)
* Helyszíni MySQL-adatbázis 5.7-es verzióval. Ha nem, töltse le és telepítse a [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.7-es kiadását.
* [Példány létrehozása az Azure Database for MySQL-ben](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Az adatbázisok Workbench alkalmazással való csatlakoztatásával és létrehozásával kapcsolatos részletekért tekintse meg a Csatlakozás és adatok lekérdezése a [MySQL Workbench](../mysql/connect-workbench.md) használatával cikket. A Azure Database for MySQL verziónak a helyszíni MySQL-verzióval egyenlőnek vagy annál magasabbnak kell lennie. A MySQL 5.7-es verzió például az 5.7-es Azure Database for MySQL-re vagy a 8-as verzióra frissíthető. 
* Hozzon létre Microsoft Azure Virtual Network-Azure Database Migration Service-kiszolgálóhoz egy üzembe helyezési modellel, amely [expressRoute](../expressroute/expressroute-introduction.md) vagy VPN használatával biztosítja Azure Resource Manager helyszíni forráskiszolgálókhoz való [hely–hely kapcsolatot.](../vpn-gateway/vpn-gateway-about-vpngateways.md) A virtuális hálózatok létrehozásával kapcsolatos további [](../virtual-network/index.yml)információkért tekintse meg a Virtual Network dokumentációját, és különösen a részletes információkat tartalmazó gyorsútmutató-cikkeket.

    > [!NOTE]
    > A virtuális hálózat beállítása során, ha az ExpressRoute-et a [](../virtual-network/virtual-network-service-endpoints-overview.md) Microsofttal való hálózati társviszony-létesítés során használja, adja hozzá a következő szolgáltatásvégpontokat ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
    >
    > * Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont és így tovább)
    > * Storage-végpont
    > * Service Bus-végpont
    >
    > Ez a konfiguráció azért szükséges, mert Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport szabályai nem blokkolják a ServiceBus, a Storage és az AzureMonitor serviceTag 443-as kimenő portját. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részletekért tekintse meg a Hálózati forgalom szűrése hálózati [biztonsági csoportokkal cikket.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* Nyissa meg a Windows tűzfalat, hogy engedélyezze a Virtual Network for Azure Database Migration Service számára a forrás MySQL-kiszolgálóhoz való hozzáférést, amely alapértelmezés szerint a 3306-os TCP-port.
* Ha tűzfalberendezést használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia, hogy engedélyezze a Virtual Network-ból származó kapcsolatokat, hogy a Azure Database Migration Service hozzáfér a forrásadatbázis(ok)hoz a migráláshoz.
* Hozzon létre [](../azure-sql/database/firewall-configure.md) egy kiszolgálószintű tűzfalszabályt, vagy konfigurálja a VNET-szolgáltatásvégpontokat a Azure Database for MySQL számára, hogy Virtual Network hozzáférést Azure Database Migration Service a céladatbázishoz. [](../mysql/howto-manage-vnet-using-portal.md)
* A forrásként szolgáló MySQL-nek a MySQL közösségi kiadásának kell lennie. A MySQL-példány verziójának megállapításához futtassa a következő parancsot a MySQL segédprogramban vagy a MySQL Workbenchben:

    ```
    SELECT @@version;
    ```

* Az Azure Database for MySQL csak az InnoDB-táblákat támogatja. Ha a MyISAM-táblákat InnoDB-táblákká szeretné alakítani, tekintse meg a [táblák MyISAM-ból InnoDB-vé konvertálását ismertető](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) részt
* A felhasználónak jogosultsággal kell rendelkezik a forrásadatbázis adatainak olvasása érdekében.

## <a name="migrate-database-schema"></a>Adatbázisséma áttelepítése

Az összes adatbázis-objektum, például a táblasémák, indexek és tárolt eljárások átviteléhez ki kell bontanunk a sémát a forrásadatbázisból, és alkalmaznunk kell a céladatbázisra. A séma kibontásához használhatja a mysqldump segédprogramot a `--no-data` paraméterrel. Ehhez olyan gépre van szükség, amely a forrás MySQL-adatbázishoz és a céladatbázishoz is Azure Database for MySQL.

A séma mysqldump használatával történő exportáláshoz futtassa a következő parancsot:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Például:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

A séma importálása célként Azure Database for MySQL futtassa a következő parancsot:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Például:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Ha a sémában vannak idegen kulcsok, a párhuzamos adatbetöltést a migrálási feladat fogja kezelni. A sémaáttelepítés során nem szükséges elejteni a idegen kulcsokat.

Ha az adatbázisban eseményindítók vannak, az kikényszeríteni fogja az adatok integritását a célban, még a forrásból való teljes adatáttelepítés előtt. Javasoljuk, hogy a migrálás során tiltsa le az eseményindítókat a célban lévő összes táblán, majd engedélyezze az eseményindítókat a migrálás után.

Hajtsa végre a következő szkriptet a MySQL Workbenchben a céladatbázison az eseményindító-szkript kibontása és az eseményindító-szkript hozzáadása érdekében.

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

Futtassa a létrehozott drop triggerlekérdezés (DropQuery oszlop) az eredményben, hogy eldobja az eseményindítókat a céladatbázisban. Az eseményindító hozzáadása lekérdezés menthető, és az adatáttelepítés befejezése után használható.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

Az erőforrás-szolgáltató regisztrációját minden Azure-előfizetésben csak egyszer kell regisztrálni. A regisztráció nélkül nem hozhat létre példányokat a **Azure Database Migration Service.**

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-mysql-to-azure-mysql-offline-portal/01-dms-portal-select-subscription.png)

2. Válassza ki azt az előfizetést, amelyben létre szeretné hozni a Azure Database Migration Service, majd válassza az **Erőforrás-szolgáltatók lehetőséget.**

3. Keressen rá a migrálás kifejezésre, majd a **Microsoft.DataMigration** jobb oldalon válassza a **Regisztráció lehetőséget.**

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-mysql-to-azure-mysql-offline-portal/02-dms-portal-register-rp.png)

## <a name="create-a-database-migration-service-instance"></a>Új Database Migration Service létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-mysql-to-azure-mysql-offline-portal/03-dms-portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-mysql-to-azure-mysql-offline-portal/04-dms-portal-marketplace-create.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válasszon ki egy tarifacsomagot, és lépjen a hálózati képernyőre. Az offline migrálás a Standard és a Prémium tarifacsomagban is elérhető.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Alapszintű Azure Database Migration Service konfigurálása](media/tutorial-mysql-to-azure-mysql-offline-portal/05-dms-portal-create-basic.png)

5. Válasszon ki egy meglévő virtuális hálózatot a listából, vagy adja meg a létrehozni kívánt új virtuális hálózat nevét. Lépjen a felülvizsgálat + létrehozás képernyőre. Ha szeretne, címkéket is hozzáadhat a szolgáltatáshoz a címkék képernyőjén.

    A virtuális hálózat hozzáférést Azure Database Migration Service forrás- és SQL Server a Azure SQL Database számára.

    ![Hálózati Azure Database Migration Service konfigurálása](media/tutorial-mysql-to-azure-mysql-offline-portal/06-dms-portal-create-networking.png)

    További információ a virtuális hálózatok létrehozásáról a Azure Portal létrehozásáról: Virtuális hálózat létrehozása [a Azure Portal.](../virtual-network/quick-create-portal.md)

6. Tekintse át a konfigurációkat, és **válassza a Létrehozás lehetőséget** a szolgáltatás létrehozásához.
    
    ![Azure Database Migration Service létrehozása](media/tutorial-mysql-to-azure-mysql-offline-portal/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.  

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![A példányok összes példányának Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Válassza ki a migrálási szolgáltatás példányát a keresési eredmények közül, majd válassza az + **Új migrálási projekt lehetőséget.**
    
    ![Új migrálási projekt létrehozása](media/tutorial-mysql-to-azure-mysql-offline-portal/08-02-dms-portal-new-project.png)

3. Az  Új migrálási projekt képernyőn adja meg  a projekt nevét, majd a Forráskiszolgáló típusa kiválasztási mezőben válassza a **MySQL** lehetőséget, a Célkiszolgáló típusa választómezőben válassza az **Azure Database for MySQL**   **\[ \]** lehetőséget, majd a Migrálási tevékenység típusa választómezőben válassza az Adatáttelepítés előnézete lehetőséget. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-mysql-to-azure-mysql-offline-portal/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Másik lehetőségként választhatja a Csak projekt **létrehozása lehetőséget** a migrálási projekt létrehozásához, és később végrehajthatja a migrálást.

## <a name="configure-migration-project"></a>Migrálási projekt konfigurálása

1. A Forrás **kiválasztása képernyőn** adja meg a forrás MySQL-példány kapcsolati adatait, majd válassza a **Tovább:** Cél kiválasztása lehetőséget>>

    ![Forrás részleteinek hozzáadása képernyő](media/tutorial-mysql-to-azure-mysql-offline-portal/10-dms-portal-project-mysql-source.png)

2. A Cél **kiválasztása képernyőn** adja meg a célpéldány kapcsolati Azure Database for MySQL, majd válassza a **Tovább:** Adatbázisok kiválasztása>>

    ![Céladatok hozzáadása képernyő](media/tutorial-mysql-to-azure-mysql-offline-portal/11-dms-portal-project-mysql-target.png)

3. Az Adatbázisok **kiválasztása képernyőn** leképezheti a forrás- és a céladatbázist az áttelepítéshez, majd válassza a **Tovább: Áttelepítési** beállítások konfigurálása>>. A Forráskiszolgáló  csak olvashatóként beállítása lehetőséggel a forrás csak olvashatóként is olvasható, de ügyeljen rá, hogy ez egy kiszolgálószintű beállítás. Ha be van jelölve, a teljes kiszolgálót írásra állítja, nem csak a kiválasztott adatbázisokat.
    
    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrásadatbázis, Azure Database Migration Service a céladatbázist választja ki.
    ![Adatbázis részleteinek kiválasztása képernyő](media/tutorial-mysql-to-azure-mysql-offline-portal/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
    > Bár ebben a lépésben több adatbázist is kiválaszthat, de a adatbázisok migrálható adatbázisoknak a migrálható adatbázisok számára és sebességre vonatkozó korlátaik vannak, mivel mindegyik adatbázis osztozik a számításon. A prémium szintű termékváltozat alapértelmezett konfigurációja esetén minden migrálási feladat két tábla párhuzamos migrálását kíséreli meg. Ezek a táblák a kiválasztott adatbázisok bármelyikét tartalmazni lehet. Ha ez nem elég gyors, feloszthatja az adatbázis-migrálási tevékenységeket különböző migrálási feladatokra, és több szolgáltatás között skálázhatja őket. Emellett előfizetésenként legfeljebb 10 példány Azure Database Migration Service régiónként.
    > A migrálás átviteli sebességének és párhuzamosításának részletesebb szabályozásához tekintse meg a PowerShell: Offline migrálás futtatása MySQL-adatbázisból Azure Database for MySQL [DMS](./migrate-mysql-to-azure-mysql-powershell.md) használatával

4. A Configure **migration settings (Áttelepítési beállítások konfigurálása)** képernyőn válassza ki a migrálás részétni kívánt táblákat, majd válassza a **Next : Summary (Tovább: Összegzés)>>.** Ha a céltáblák tartalmaznak adatokat, alapértelmezés szerint nincsenek kiválasztva, de explicit módon kiválaszthatja őket, és a migrálás megkezdése előtt csonkolva lesznek.

    ![Táblák kiválasztása képernyő](media/tutorial-mysql-to-azure-mysql-offline-portal/13-dms-portal-project-mysql-select-tbl.png)

5. Az Összefoglalás **képernyőn** a  Tevékenység neve szövegmezőben adja meg a migrálási tevékenység nevét, és tekintse át az összegzést, hogy a forrás és a cél adatai megegyeznek-e a korábban megadott adatokval.

    ![Migrálási projekt áttekintése](media/tutorial-mysql-to-azure-mysql-offline-portal/14-dms-portal-project-mysql-activity-summary.png)

6. Válassza a **Migrálás kezdete lehetőséget.** Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**. A **tábla migrálásának** **kezdetekor** az Állapot Fut állapotra változik.
 
     ![A migrálás futtatása](media/tutorial-mysql-to-azure-mysql-offline-portal/15-dms-portal-project-mysql-running.png)

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálási tevékenység képernyőjén válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez, és tekintse meg a befejezett táblák számának előrehaladását. 

2. A tevékenység képernyőjén az adatbázis nevére kattintva láthatja az egyes migrált tábla állapotát. A **megjelenítés frissítéséhez** válassza a Frissítés lehetőséget. 

     ![Áttelepítés figyelése](media/tutorial-mysql-to-azure-mysql-offline-portal/16-dms-portal-project-mysql-monitor.png)

## <a name="complete-the-migration"></a>Az áttelepítés befejezése

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Befejezve** értékre.

    ![Az áttelepítés befejezése](media/tutorial-mysql-to-azure-mysql-offline-portal/17-dms-portal-project-mysql-complete.png)

## <a name="post-migration-activities"></a>Migrálás utáni tevékenységek

Az offline migrálás átállása egy alkalmazásfüggő folyamat, amely nem része ennek a dokumentumnak, de a következő áttelepítés utáni tevékenységeket írja elő:

1. Hozza létre a bejelentkezéseket, szerepköröket és engedélyeket az alkalmazás követelményeinek megfelelően.
2. Hozza létre újra az összes eseményindítót a céladatbázison a migrálás előtti lépés során kinyert adatok alapján.
3. A migrálás tanúsításához végezzen sanitási teszteket az alkalmazáson a céladatbázison. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a Database Migration Service, akkor a következő lépésekkel törölheti a szolgáltatást:

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![A DMS összes példányának megkeresés](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Válassza ki a migrálási szolgáltatás példányát a keresési eredmények közül, majd válassza a **Szolgáltatás törlése lehetőséget.**
    
    ![A migrálási szolgáltatás törlése](media/tutorial-mysql-to-azure-mysql-offline-portal/18-dms-portal-delete.png)

3. A megerősítő párbeszédpanelen írja be a szolgáltatás nevét a **TYPE THE DATABASE MIGRATION SERVICE NAME (ADATBÁZIS-MIGRÁLÁSI** SZOLGÁLTATÁS NEVE) szövegmezőbe, és válassza a Delete (Törlés) **lehetőséget.**

    ![A migrálási szolgáltatás törlésének megerősítése](media/tutorial-mysql-to-azure-mysql-offline-portal/19-dms-portal-deleteconfirm.png)

## <a name="next-steps"></a>Következő lépések

* A DMS-sel való migrálás során az ismert problémákkal és korlátozásokkal kapcsolatos további információkért lásd: Gyakori problémák [– Azure Database Migration Service.](./known-issues-troubleshooting-dms.md)
* A forrásadatbázis DMS használata közbeni csatlakozási problémáinak elhárításához tekintse meg a forrásadatbázisok csatlakoztatásával [kapcsolatos problémákat bemutató cikket.](./known-issues-troubleshooting-dms-source-connectivity.md)
* További információt a Azure Database Migration Service a Mi a [Azure Database Migration Service? cikkben talál.](./dms-overview.md)
* További információt a Azure Database for MySQL a Mi a [Azure Database for MySQL? cikkben talál.](../mysql/overview.md)
* A DMS PowerShellen keresztüli használatával kapcsolatos útmutatásért tekintse meg a PowerShell: Offline migrálás futtatása MySQL-adatbázisból Azure Database for MySQL [DMS használatával](./migrate-mysql-to-azure-mysql-powershell.md)