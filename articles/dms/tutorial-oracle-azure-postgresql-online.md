---
title: 'Oktatóanyag: Oracle online áttelepítése a Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan végezhet online migrálást helyszíni Oracle-ről vagy virtuális gépekről Azure Database for PostgreSQL virtuális gépekre a Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/24/2020
ms.openlocfilehash: 76ab2204a81d3cbfb559bfa4591f1f332b1707ee
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388074"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Oktatóanyag: Oracle online Azure Database for PostgreSQL A DMS (előzetes verzió) használatával

> [!IMPORTANT]
> **Az Oracle Azure Database for PostgreSQL** migrálási forgatókönyv (jelenleg előzetes verzióban érhető el) 2021. május 1. után már nem lesz elérhető. Továbbra is biztosítunk támogatást alternatív eszközök (például Ora2pg) segítségével, és a legjobb migrálási élményt biztosítjuk az Oracle-ről PostgreSQL-re történő migráláshoz. A migrálásra vonatkozó ajánlott eljárásokért lásd: [Oracle Azure Database for PostgreSQL áttelepítési útmutató.](https://aka.ms/OracletoPGguide) 

A helyszíni Azure Database Migration Service virtuális gépeken üzemeltetett Oracle-adatbázisokból minimális állásidővel átküldheti az adatbázisokat Azure Database for PostgreSQL virtuális gépekre. [](../postgresql/index.yml) Más szóval az alkalmazás minimális állásidejű migrálását is befejezheti. Ebben az oktatóanyagban a  HR-mintaadatbázist az Oracle 11g egy helyszíni vagy virtuálisgép-példányából a Azure Database for PostgreSQL-be migráljuk az Azure Database Migration Service.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Mérje fel a migrálási erőfeszítéseket az ora2pg eszközzel.
> * A mintasémát az ora2pg eszközzel miheti át.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy migrálási projektet a Azure Database Migration Service.
> * A migrálás futtatása.
> * Az áttelepítés monitorozása.

> [!NOTE]
> Ha Azure Database Migration Service online migrálást hajt végre, akkor a Prémium tarifacsomag alapján kell létrehoznia egy példányt.

> [!IMPORTANT]
> Az optimális migrálási élmény érdekében a Microsoft azt javasolja, hogy hozzon létre egy Azure Database Migration Service a céladatbázissal azonos Azure-régióban. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk bemutatja, hogyan végezhet online migrálást az Oracle-ről a Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse az [Oracle 11g Release 2 (Standard kiadás, Standard kiadás One vagy Enterprise kiadás) kiadást.](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)
* Töltse le a [](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002)MINTA **HR-adatbázist** innen: .
* Töltse le [és telepítse az ora2pg-t Windows vagy Linux rendszeren.](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Példány létrehozása Azure Database for PostgreSQL-ben](../postgresql/quickstart-create-server-database-portal.md).
* Csatlakozzon a példányhoz, és hozzon létre egy adatbázist az ebben a dokumentumban leírtak [szerint.](../postgresql/tutorial-design-database-using-azure-portal.md)
* Hozzon létre egy Microsoft Azure Virtual Network-Azure Database Migration Service a Azure Resource Manager üzembe helyezési modelljével, amely [expressRoute](../expressroute/expressroute-introduction.md) vagy VPN használatával biztosít hely–hely kapcsolatot a helyszíni [forráskiszolgálókkal.](../vpn-gateway/vpn-gateway-about-vpngateways.md) A virtuális hálózatok létrehozásával kapcsolatos további [](../virtual-network/index.yml)információkért tekintse meg a Virtual Network dokumentációját, és különösen a részletes információkat tartalmazó gyorsútmutató-cikkeket.

  > [!NOTE]
  > Ha a virtuális hálózat beállítása során az ExpressRoute-et a [](../virtual-network/virtual-network-service-endpoints-overview.md) Microsofttal való hálózati társviszony-létesítés során használja, adja hozzá a következő szolgáltatásvégpontokat ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
  >
  > * Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont és így tovább)
  > * Storage-végpont
  > * Service Bus-végpont
  >
  > Ez a konfiguráció azért szükséges, mert Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózati biztonsági csoport (NSG) szabályai nem blokkolják a ServiceBus, a Storage és az AzureMonitor ServiceTag 443-as kimenő portját. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részletekért tekintse meg a Hálózati forgalom szűrése hálózati [biztonsági csoportokkal cikket.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, hogy Azure Database Migration Service a forrás Oracle-kiszolgálóhoz, amely alapértelmezés szerint az 1521-es TCP-port.
* Ha tűzfalberendezést használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia, hogy a Azure Database Migration Service hozzáfér a forrásadatbázis(ok)hoz a migráláshoz.
* Hozzon létre egy kiszolgálószintű [tűzfalszabályt](../azure-sql/database/firewall-configure.md) a Azure Database for PostgreSQL, hogy Azure Database Migration Service hozzáférést a céladatbázishoz. Adja meg a virtuális hálózat alhálózati tartományát, Azure Database Migration Service.
* Engedélyezze a forrás Oracle-adatbázisokhoz való hozzáférést.

  > [!NOTE]
  > Ahhoz, hogy egy felhasználó csatlakozni tud az Oracle-forráshoz, az adatbázis-adatbázis szerepkör szükséges.

  * A növekményes szinkronizáláshoz archivált adatnaplók szükségesek a Azure Database Migration Service adatváltozások rögzítéséhez. Az Oracle-forrás konfiguráláshoz kövesse az alábbi lépéseket:
    * Jelentkezzen be SYSDBA-jogosultsággal a következő parancs futtatásával:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Állítsa le az adatbázispéldányt a következő parancs futtatásával.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Várja meg a `'ORACLE instance shut down'` megerősítést.

    * Indítsa el az új példányt, és csatlakoztassa (de ne nyissa meg) az adatbázist az archiválás engedélyezéséhez vagy letiltásához a következő parancs futtatásához:

      ```
      STARTUP MOUNT;
      ```

      Az adatbázist csatlakoztatni kell; várjon, amíg meg nem kezdődik az "Oracle-példány" megerősítése.

    * Módosítsa az adatbázis-archiválási módot a következő parancs futtatásával:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Nyissa meg az adatbázist a normál működéshez a következő parancs futtatásával:

      ```
      ALTER DATABASE OPEN;
      ```

      Előfordulhat, hogy újra kell indítania az ARC-fájlt, hogy az érvénybe lép.

    * Az ellenőrzéshez futtassa a következő parancsot:

      ```
      SELECT log_mode FROM v$database;
      ```

      A következő választ kell `'ARCHIVELOG'` kapnia: . Ha a válasz , akkor a követelmény `'NOARCHIVELOG'` nem teljesül.

  * Engedélyezze a kiegészítő naplózást a replikációhoz az alábbi lehetőségek egyikével.

    * **1. lehetőség.**
      Módosítsa az adatbázisszintű kiegészítő naplózást úgy, hogy lefedje az összes táblát PK-val és egyedi indexszel. Az észlelési lekérdezés a következőt adja `'IMPLICIT'` vissza: .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Módosítsa a táblázatszintű kiegészítő naplózást. Csak olyan táblákhoz fut, amelyeken adatmanipuláció van, és nem rendelkezik PK-okkal vagy egyedi indexekkel.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **2. lehetőség.**
      Módosítsa az adatbázisszintű kiegészítő naplózást úgy, hogy az összes táblát lefedje, és az észlelési lekérdezés a értéket adja `'YES'` vissza.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Módosítsa a táblaszint kiegészítő naplózását. Kövesse az alábbi logikát, ha minden táblához csak egy utasítást futtat.

      Ha a tábla elsődleges kulccsal rendelkezik:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Ha a tábla egyedi indexszel rendelkezik:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      Ellenkező esetben futtassa a következő parancsot:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Az ellenőrzéshez futtassa a következő parancsot:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    A következő választ kell `'YES'` kapnia: .

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Az Oracle migrálásra irányuló Azure Database for PostgreSQL felmérése

Javasoljuk, hogy az Ora2pg használatával mérje fel az Oracle-ről a Azure Database for PostgreSQL. A irányelv használatával hozzon létre egy jelentést, amely az összes Oracle-objektumot, a becsült migrálási költséget (fejlesztői napokban) és bizonyos olyan adatbázis-objektumokat sorolja fel, amelyek az átalakítás részeként különleges figyelmet `ora2pg -t SHOW_REPORT` igényelnek.

A legtöbb ügyfél jelentős mennyiségű időt tölt majd az értékelési jelentés áttekintésével, és figyelembe véve az automatikus és manuális konverziós erőfeszítéseket.

Az ora2pg felmérési jelentés létrehozásához való konfigurálás és futtatásához tekintse meg az Oracle **Premigration: Assessment (Migrálás:** Értékelés) szakaszát a [Cookbook Azure Database for PostgreSQL futtatásához.](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) Itt egy minta vagya2pg értékelési jelentés érhető [el referenciaként.](https://ora2pg.darold.net/report.html)

## <a name="export-the-oracle-schema"></a>Az Oracle-séma exportálása

Javasoljuk, hogy az Ora2pg használatával alakítsa át az Oracle-sémát és más Oracle-objektumokat (típusokat, eljárásokat, függvényeket stb.) egy, a Azure Database for PostgreSQL. Az ora2pg számos irányelvet tartalmaz bizonyos adattípusok előzetes definiálása érdekében. Az irányelv használatával például a `DATA_TYPE` NUMBER(*,0) egészét bigintre cserélheti a NUMERIC(38) helyett.

Az ora2pg futtatásával exportálhatja az egyes adatbázis-objektumokat .sql-fájlokba. Ezután áttekintheti az .sql-fájlokat, mielőtt importálja őket Azure Database for PostgreSQL psql használatával, vagy végrehajthatja a következőt: . SQL-szkript a PgAdminben.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Például:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Az ora2pg sémakonverzióra való konfigurálásával és futtatásával kapcsolatban tekintse meg az Oracle **Áttelepítés:** Séma és adatok szakaszát a [következőre: Azure Database for PostgreSQL cookbook.](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>A séma beállítása a Azure Database for PostgreSQL

Oracle-táblasémák, tárolt eljárások, csomagok és egyéb adatbázis-objektumok konvertálásával a Postgres kompatibilisé tehetők az ora2pg használatával a migrálási folyamat Azure Database Migration Service. Az ora2pg az alábbi hivatkozásokon keresztül működik:

* [Az ora2pg telepítése Windows rendszeren](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Oracle–Azure PostgreSQL migrálási kézikönyv](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service PostgreSQL-táblasémát is létrehozhat. A szolgáltatás hozzáfér a csatlakoztatott Oracle-forrás táblasémához, és egy kompatibilis táblasémát hoz létre a Azure Database for PostgreSQL. Ellenőrizze és ellenőrizze a sémaformátumot a Azure Database for PostgreSQL miután Azure Database Migration Service a séma létrehozását és az adatok áthelyezését.

> [!IMPORTANT]
> Azure Database Migration Service csak a táblasémát hozza létre; más adatbázis-objektumok, például tárolt eljárások, csomagok, indexek stb. nem jön létre.

A teljes betöltés futtatásához ne hagyja el a idegen kulcsot a céladatbázisban. A idegen kulcs elejtéshez használható [](./tutorial-postgresql-azure-postgresql-online.md) szkriptért tekintse meg a cikk **Migrate** the sample schema című szakaszát. Teljes Azure Database Migration Service és szinkronizáláshoz használja a Azure Database Migration Service futtatásához.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Ha a PostgreSQL-táblaséma már létezik

Ha a postgreSQL-sémát az ora2pg vagy hasonló eszközökkel hozza létre, mielőtt az Azure Database Migration Service-val elkezdi az adatátmozgatást, leképezi a forrástáblákat a Azure Database Migration Service.

1. Amikor létrehoz egy új Oracle-t a Azure Database for PostgreSQL migrálási projekthez, a rendszer a Séma kiválasztása lépésben felkéri, hogy válassza ki a céladatbázist és a célsémát. Töltse ki a céladatbázist és a célsémát.

   ![A Leképezés céladatbázisra képernyőképe.](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. Az **Áttelepítési beállítások** képernyőn megjelenik az Oracle-forrásban lévő táblák listája. Azure Database Migration Service a forrástáblák és a céltáblák táblái a tábla neve alapján próbálnak egyezni. Ha több egyező céltábla létezik különböző kis- és nagybani céltáblával, kiválaszthatja a leképezni kívánt céltáblát.

    ![Képernyőkép a migrálási beállításokról.](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Ha a forrástáblák neveit különböző nevekkel és e-mail-címmel és e-mail-címmel kell leképezni a táblákra, a folyamat automatizálásához pedig egy [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) szkriptet is biztosítunk.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Ha a PostgreSQL-táblaséma nem létezik

Ha a cél PostgreSQL-adatbázis nem tartalmaz táblasémával kapcsolatos információkat, a Azure Database Migration Service konvertálja a forrássémát, és újra létrehozza a céladatbázisban. Ne feledje, Azure Database Migration Service csak a táblasémát hozza létre, más adatbázis-objektumokat, például tárolt eljárásokat, csomagokat és indexeket nem.
Ahhoz, Azure Database Migration Service létre a sémát, győződjön meg arról, hogy a célkörnyezet olyan sémát tartalmaz, amely nem tartalmaz meglévő táblákat. Ha Azure Database Migration Service felderít egy táblát, a szolgáltatás feltételezi, hogy a sémát egy külső eszköz, például az ora2pg hozta létre.

> [!IMPORTANT]
> Azure Database Migration Service, hogy az összes táblát azonos módon kell létrehozni a Azure Database Migration Service vagy egy olyan eszközzel, mint az ora2pg, de nem mindkettővel.

Első lépések:

1. Hozzon létre egy sémát a céladatbázisban az alkalmazás követelményei alapján. Alapértelmezés szerint a PostgreSQL-táblasémák és -oszlopok neve kisbetűs. Az Oracle-táblasémák és -oszlopok viszont alapértelmezés szerint nagybetűsek.
2. A Sémák kiválasztása lépésben adja meg a céladatbázist és a célsémát.
3. A Azure Database for PostgreSQL létrehozott Azure Database Migration Service átalakítási szabályokat használja:

    Ha az Oracle-forrásban található sémanév megegyezik Azure Database for PostgreSQL- Azure Database Migration Service a táblasémát a célban használt eset használatával hozza *létre.*

    Például:

    | Forrás Oracle-séma | Cél: PostgreSQL Database.Schema | A DMS létrehozta a schema.table.column oszlopot |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR". COUNTRIES" (ORSZÁGOK) COUNTRY_ID" |
    | HR | targetHR.HR | "HR". COUNTRIES" (ORSZÁGOK) COUNTRY_ID" |
    | HR | targetHR.Hr | *Nem lehet vegyes eseteket leképezni |

    *Vegyeseseset-séma- és táblanevek a cél PostgreSQL-ben való létrehozásához lépjen kapcsolatba a következővel: [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com) . Biztosítunk egy szkriptet a vegyes esettábla sémájának beállításához a cél PostgreSQL-adatbázisban.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Keressen rá a migrálás kifejezésre, majd a **Microsoft.DataMigration** jobb oldalon válassza a **Regisztráció lehetőséget.**

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válasszon ki egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat hozzáférést Azure Database Migration Service a forrás Oracle-hez és a célként Azure Database for PostgreSQL példányhoz.

    További információ a virtuális hálózatok létrehozásáról a Azure Portal létrehozásáról: Virtuális hálózat létrehozása [a Azure Portal.](../virtual-network/quick-create-portal.md)

5. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.

    ![Az Azure Database Migration Service személyes példányának megkeresése](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az Új **migrálási projekt** képernyőn adja meg  a projekt nevét, majd a  Forráskiszolgáló típusa szövegmezőben válassza az **Oracle** lehetőséget, majd a Célkiszolgáló típusa szövegmezőben válassza a **Azure Database for PostgreSQL.**
5. A Tevékenység **típusának kiválasztása szakaszban válassza** az **Online adatáttelepítés lehetőséget.**

   ![Azure Database Migration Service-projekt létrehozása](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Másik lehetőségként választhatja a Csak projekt **létrehozása lehetőséget** a migrálási projekt létrehozásához, és később végrehajthatja a migrálást.

6. Válassza **a Mentés** lehetőséget, jegyezze fel a Azure Database Migration Service online migráláshoz való sikeres használatára vonatkozó követelményeket, majd válassza a Tevékenység létrehozása és futtatása **lehetőséget.**

## <a name="specify-source-details"></a>Forrás adatainak megadása

* A **Forrásadatok hozzáadása képernyőn** adja meg a forrás Oracle-példány kapcsolati adatait.

  ![A Forrás adatainak hozzáadása képernyő](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Oracle OCI-illesztő feltöltése

1. Válassza a **Mentés** lehetőséget, majd az **OCI-illesztőprogram** telepítése képernyőn jelentkezzen be Oracle-fiókjába, és töltse le az **illesztőtinstantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37 128 586 bájt/bájt) (SHA1 Ellenőrzőum: 865082268) [innen.](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst)
2. Töltse le az illesztőprogramot egy megosztott mappába.

   Győződjön meg arról, hogy a mappa meg van osztva a megadott felhasználónévvel, minimális írási hozzáféréssel. Azure Database Migration Service OCI-illesztőnek az Azure-ba való feltöltéshez a megadott felhasználónév megszemélyesítésével fér hozzá és olvas a megosztásból.

   A megadott felhasználónévnek Windows felhasználói fióknak kell lennie.

   ![OCI-illesztőprogram telepítése](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés** lehetőséget, majd a Cél részletei képernyőn adja meg a célként megadott Azure Database for PostgreSQL-kiszolgáló kapcsolati adatait,  amely a HR-sémát üzembe helyező Azure Database for PostgreSQL előre kiépített példánya. 

    ![A részleteket tartalmazó képernyő](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrásadatbázis, Azure Database Migration Service a céladatbázist választja ki alapértelmezés szerint.

    ![Leképezés céladatbázisokra](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

  Megjelenik a migrálási tevékenység ablaka, és a tevékenység **állapota** **inicializálás.**

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Fut** értékre.

     ![Tevékenység állapota – fut](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Az **Adatbázis neve alatt** válasszon ki egy adott  adatbázist a Teljes adatbetöltés és a Növekményes adatszinkronizálási műveletek migrálási **állapotának beállításához.**

    Az Adatok teljes betöltésénél az első betöltés migrálási állapota jelenik meg, a Növekményes adatszinkronizálásnál pedig az Adatváltozások rögzítése (CDC) állapot látható.

     ![Tevékenység állapota – Teljes betöltés kész](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Tevékenység állapota – Növekményes adatszinkronizálás](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.

   ![Átállás indítása](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis migrálási állapota **Befejezve,** csatlakoztassa az alkalmazásokat az új célként Azure Database for PostgreSQL példányhoz.

 > [!NOTE]
 > Mivel a PostgreSQL alapértelmezés szerint kisbetűs schema.table.column oszlopot tartalmaz, **a** cikk korábbi, A séma beállítása a Azure Database for PostgreSQL-ban című szakaszában található szkripttel kisbetűsre állíthat vissza.

## <a name="next-steps"></a>Következő lépések

* Az Azure Database for PostgreSQL-be történő online migrálás végrehajtásakor felmerülő ismert hibákhoz és korlátozásokhoz kapcsolódó információk: [Az Azure Database for PostgreSQL online migrálásával kapcsolatos ismert hibák és kerülő megoldások](known-issues-azure-postgresql-online.md).
* A cikkel kapcsolatos Azure Database Migration Service lásd: [Mi a Azure Database Migration Service?](./dms-overview.md).
* További információt a Azure Database for PostgreSQL a Mi a Azure Database for PostgreSQL? cikkben [talál.](../postgresql/overview.md)
