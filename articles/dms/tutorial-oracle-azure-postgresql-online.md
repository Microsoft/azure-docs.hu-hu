---
title: 'Oktatóanyag: Oracle online migrálása a Azure Database for PostgreSQLba'
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan hajthat végre egy online áttelepítést a helyszíni Oracle-ből vagy a virtuális gépekről, hogy Azure Database Migration Service használatával Azure Database for PostgreSQL.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.subservice: migration-guide
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/24/2020
ms.openlocfilehash: 2be824c89ac1e6b900dc9b67e046e2be2c7f8919
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103562824"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Oktatóanyag: Oracle migrálása Azure Database for PostgreSQL online-ba a DMS használatával (előzetes verzió)

> [!IMPORTANT]
> **Az Oracle – Azure Database for PostgreSQL** áttelepítési forgatókönyv (jelenleg előzetes verzióban) már nem lesz elérhető a 2021. május 1. után. Továbbra is biztosítunk támogatást az alternatív eszközök (például a Ora2pg) segítségével, és a lehető legjobb migrációs élményt nyújtják az Oracle és a PostgreSQL közötti áttelepítéshez. Az áttelepítéssel kapcsolatos ajánlott eljárásokért tekintse [meg az Oracle Azure Database for PostgreSQL áttelepítési útmutatót](https://aka.ms/OracletoPGguide). 

A Azure Database Migration Service segítségével áttelepítheti az adatbázisokat a helyszíni vagy virtuális gépeken üzemeltetett Oracle-adatbázisokból, hogy minimális állásidővel [Azure Database for PostgreSQL](../postgresql/index.yml) . Más szóval az áttelepítés minimális állásidővel is elvégezhető az alkalmazáshoz. Ebben az oktatóanyagban a **HR** mintaadatbázis helyszíni vagy virtuálisgép-példányáról telepíti át a Azure Database for PostgreSQL a Azure Database Migration Service Online áttelepítési tevékenységének használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Mérje fel az áttelepítési erőfeszítést a ora2pg eszközzel.
> * Telepítse át a minta sémát a ora2pg eszköz használatával.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * Az áttelepítés monitorozása.

> [!NOTE]
> A Azure Database Migration Service használata az online áttelepítés végrehajtásához a prémium szintű díjszabás alapján kell létrehoznia egy példányt.

> [!IMPORTANT]
> Az optimális áttelepítési élmény érdekében a Microsoft azt javasolja, hogy Azure Database Migration Service-példányt hozzon létre ugyanabban az Azure-régióban, mint a célként megadott adatbázis. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk azt ismerteti, hogyan hajtható végre az Oracle-ből a Azure Database for PostgreSQLba való online áttelepítés.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse az [Oracle 11g 2. kiadását (Standard Edition, Standard Edition One vagy Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Töltse le [innen](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002)a minta **HR** -adatbázist.
* Töltse le és [telepítse a ora2pg-t Windows vagy Linux rendszeren](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Példány létrehozása Azure Database for PostgreSQL-ben](../postgresql/quickstart-create-server-database-portal.md).
* Kapcsolódjon a példányhoz, és hozzon létre egy adatbázist a jelen [dokumentum](../postgresql/tutorial-design-database-using-azure-portal.md)utasításai alapján.
* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Service számára a Azure Resource Manager üzemi modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](../expressroute/expressroute-introduction.md) vagy a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával. A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](../virtual-network/index.yml), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.

  > [!NOTE]
  > Ha a virtuális hálózat beállítása során ExpressRoute használ a Microsoft számára, adja hozzá a következő szolgáltatási [végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
  >
  > * Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont stb.)
  > * Tárolási végpont
  > * Service Bus-végpont
  >
  > Erre a konfigurációra azért van szükség, mert Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport (NSG) szabályai nem gátolják meg a ServiceTag 443 kimenő portját a ServiceBus, a Storage és a AzureMonitor számára. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/virtual-network-vnet-plan-design-arm.md)című cikket.
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, hogy a Azure Database Migration Service hozzáférhessen a forrás Oracle-kiszolgálóhoz, amely alapértelmezés szerint a 1521-es TCP-port.
* Ha a forrásadatbázis (ok) előtt tűzfal-berendezést használ, előfordulhat, hogy olyan tűzfalszabályok hozzáadására van szükség, amelyek lehetővé teszik a Azure Database Migration Service számára a forrás-adatbázis (ok) elérését az áttelepítéshez.
* Hozzon létre egy kiszolgálói szintű [Tűzfalszabály-szabályt](../azure-sql/database/firewall-configure.md) a Azure Database for PostgreSQL számára, hogy lehetővé tegye Azure Database Migration Service hozzáférést a célként megadott adatbázisokhoz. Adja meg a Azure Database Migration Service használt virtuális hálózat alhálózati tartományát.
* Engedélyezze a hozzáférést a forrás Oracle-adatbázisokhoz.

  > [!NOTE]
  > A DBA szerepkör szükséges ahhoz, hogy egy felhasználó csatlakozhasson az Oracle-forráshoz.

  * A növekményes szinkronizáláshoz szükséges archiválási naplók a Azure Database Migration Service adatváltozások rögzítéséhez szükségesek. Az Oracle-forrás konfigurálásához kövesse az alábbi lépéseket:
    * A következő parancs futtatásával jelentkezzen be a SYSDBA-jogosultság használatával:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Az alábbi parancs futtatásával állítsa le az adatbázis-példányt.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Várja meg a megerősítést `'ORACLE instance shut down'` .

    * Indítsa el az új példányt, és csatlakoztassa (de ne nyissa meg) az adatbázist az alábbi parancsot futtató Bu-archiválás engedélyezéséhez vagy letiltásához:

      ```
      STARTUP MOUNT;
      ```

      Az adatbázist csatlakoztatni kell; várja meg az "Oracle-példány elindítva" jóváhagyását.

    * Módosítsa az adatbázis-archiválási módot a következő parancs futtatásával:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * A következő parancs futtatásával nyissa meg a normál működéshez tartozó adatbázist:

      ```
      ALTER DATABASE OPEN;
      ```

      Előfordulhat, hogy újra kell indítania az ív-fájl megjelenítését.

    * A következő parancs futtatásával ellenőrizheti:

      ```
      SELECT log_mode FROM v$database;
      ```

      Meg kell kapnia egy választ `'ARCHIVELOG'` . Ha a válasz `'NOARCHIVELOG'` , akkor a követelmény nem teljesül.

  * A következő lehetőségek egyikének használatával engedélyezheti a kiegészítő naplózást a replikáláshoz.

    * **1. lehetőség**.
      Módosítsa az adatbázis-szint kiegészítő naplózását úgy, hogy az az összes táblát lefedje a PK és az egyedi index használatával. A rendszer visszaküldi az észlelési lekérdezést `'IMPLICIT'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      A tábla szintjének kiegészítő naplózásának módosítása. Csak olyan táblák esetében fusson, amelyek adatkezeléssel rendelkeznek, és nincsenek PKs vagy egyedi indexek.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **2. lehetőség**.
      Módosítsa az adatbázis szintjének kiegészítő naplózását az összes táblára, az észlelési lekérdezés pedig visszaadja `'YES'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      A tábla szintjének kiegészítő naplózásának módosítása. Kövesse az alábbi logikát, hogy csak egy utasítást futtasson minden táblához.

      Ha a táblának van elsődleges kulcsa:

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

    A következő parancs futtatásával ellenőrizheti:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Meg kell kapnia egy választ `'YES'` .

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Az Oracle Azure Database for PostgreSQL áttelepítésre vonatkozó erőfeszítésének felmérése

Javasoljuk, hogy a ora2pg használatával mérje fel az Oracle-ből a Azure Database for PostgreSQLba való Migrálás szükséges erőfeszítéseit. A `ora2pg -t SHOW_REPORT` direktívával létrehozhat egy jelentést, amely felsorolja az összes Oracle-objektumot, a becsült áttelepítési költségeket (a fejlesztői napokon) és bizonyos adatbázis-objektumokat, amelyek különleges figyelmet igényelhetnek az átalakítás részeként.

A legtöbb ügyfelünk jelentős időt tölt az értékelési jelentés áttekintésével, és figyelembe veszi az automatikus és a manuális átalakítási erőfeszítést.

A ora2pg konfigurálásához és futtatásához értékelési jelentés létrehozásához tekintse meg az Oracle előzetes **áttelepítését: Assessment** című szakaszát, [hogy Azure Database for PostgreSQL a Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). [Itt](https://ora2pg.darold.net/report.html)a minta ora2pg-értékelő jelentés is elérhető.

## <a name="export-the-oracle-schema"></a>Az Oracle-séma exportálása

Javasoljuk, hogy a ora2pg használatával alakítsa át az Oracle-sémát és más Oracle-objektumokat (típusokat, eljárásokat, függvényeket stb.) olyan sémára, amely kompatibilis a Azure Database for PostgreSQLokkal. a ora2pg számos irányelvet tartalmaz, amelyek segítségével előre definiálhatja bizonyos adattípusokat. Az `DATA_TYPE` direktívával például lecserélheti az összes számot (*, 0) a bigint és nem numerikus (38) értékre.

A ora2pg futtatásával exportálhatja az egyes adatbázis-objektumokat. SQL-fájlokba. Ezután áttekintheti az. SQL-fájlokat, mielőtt az psql használatával Azure Database for PostgreSQL importálni őket, vagy végrehajthatja a következőt:. SQL-szkript a PgAdmin-ben.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Például:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

A ora2pg konfigurálásához és futtatásához tekintse meg az Oracle **áttelepítési: séma és információ** szakaszát, [hogy Azure Database for PostgreSQL a szakácskönyvet](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Séma beállítása Azure Database for PostgreSQL

Megadhatja, hogy az Oracle Table-sémákat, tárolt eljárásokat, csomagokat és más adatbázis-objektumokat úgy alakítsa ki, hogy a ora2pg használatával kompatibilisek legyenek a postgres, mielőtt elkezdené a Azure Database Migration Service áttelepítési folyamatát. Tekintse meg az alábbi hivatkozásokat a ora2pg való használathoz:

* [A ora2pg telepítése Windows rendszeren](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Oracle – Azure PostgreSQL Migration Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service is létrehozhatja a PostgreSQL Table sémát. A szolgáltatás hozzáfér a tábla sémához a csatlakoztatott Oracle-forrásban, és létrehoz egy kompatibilis Table sémát Azure Database for PostgreSQL. Győződjön meg arról, hogy a séma formátumának érvényesítése és ellenőrzése Azure Database for PostgreSQL azt követően, hogy Azure Database Migration Service befejezte a séma létrehozását és az adatáthelyezést.

> [!IMPORTANT]
> Azure Database Migration Service csak a Table sémát hozza létre; nem jönnek létre más adatbázis-objektumok, például a tárolt eljárások, csomagok, indexek stb..

Ne feledje, hogy a teljes terhelés futtatásához el kell dobnia a külső kulcsot a célként megadott adatbázisban. Tekintse át a cikk **minta-séma** átirányítása című szakaszát [egy olyan](./tutorial-postgresql-azure-postgresql-online.md) parancsfájlhoz, amelyet a külső kulcs eldobásához használhat. A teljes betöltéshez és a szinkronizáláshoz használja a Azure Database Migration Service.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Ha a PostgreSQL-tábla sémája már létezik

Ha a PostgreSQL-sémát olyan eszközökkel hozza létre, mint például a ora2pg, mielőtt megkezdené az adatáthelyezést a Azure Database Migration Service használatával, képezze le a táblákat a célként megadott táblákra Azure Database Migration Service.

1. Amikor új Oracle-t hoz létre Azure Database for PostgreSQL áttelepítési projekthez, a sémák kiválasztása lépésben meg kell adnia a céladatbázis és a célként megadott séma elemet. Töltse ki a célként megadott adatbázist és a célként megadott sémát.

   ![A képernyőképen látható a cél-adatbázisok leképezése.](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. Az **áttelepítési beállítások** képernyő az Oracle-forrás tábláinak listáját jeleníti meg. Azure Database Migration Service a tábla neve alapján a forrásban és a célként megadott táblákban lévő táblákat próbálja meg egyeztetni. Ha a különböző burkolattal rendelkező több egyező céltábla létezik, kiválaszthatja, hogy melyik cél tábla legyen leképezve.

    ![Képernyőfelvétel: áttelepítési beállítások.](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Ha különböző névvel rendelkező táblákhoz szeretné leképezni a forrástábla nevét, e-mail-címet, [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) és a folyamat automatizálására szolgáló parancsfájlt is megadhat.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Ha a PostgreSQL-tábla sémája nem létezik

Ha a cél PostgreSQL-adatbázis nem tartalmaz Table Schema-információkat, Azure Database Migration Service átalakítja a forrás sémát, és újból létrehozza a céladatbázis-adatbázisban. Ne feledje, Azure Database Migration Service csak a tábla sémáját hozza létre, nem más adatbázis-objektumokat, például tárolt eljárásokat, csomagokat és indexeket.
Ahhoz, hogy Azure Database Migration Service hozza létre a sémát, győződjön meg arról, hogy a célként megadott környezet meglévő táblák nélküli sémát tartalmaz. Ha Azure Database Migration Service felfedi a táblákat, a szolgáltatás feltételezi, hogy a sémát egy külső eszköz, például a ora2pg hozta létre.

> [!IMPORTANT]
> Azure Database Migration Service megköveteli, hogy az összes tábla ugyanúgy legyen létrehozva, Azure Database Migration Service vagy egy olyan eszköz használatával, mint a ora2pg, de mindkettőt nem.

Első lépések:

1. Hozzon létre egy sémát a céladatbázis alkalmazásának követelményei alapján. Alapértelmezés szerint a PostgreSQL-tábla sémája és az oszlopainak neve alacsonyabb tokozású. Az Oracle Table séma és az oszlopok viszont alapértelmezés szerint az összes nagybetűs esetben vannak.
2. A sémák kiválasztása lépésben adja meg a cél-adatbázist és a célként megadott sémát.
3. A Azure Database for PostgreSQLban létrehozott séma alapján Azure Database Migration Service a következő átalakítási szabályokat használja:

    Ha a séma neve az Oracle-forrásban található, és megfelel a Azure Database for PostgreSQLban szereplőnek, akkor Azure Database Migration Service a *tábla sémáját a célként megadott módon hozza létre*.

    Például:

    | Forrás Oracle-séma | Cél PostgreSQL-adatbázis. séma | DMS létrehozva Schema. table. Column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR. Public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"." ORSZÁGOK "." COUNTRY_ID " |
    | HR | targetHR.HR | "HR". " ORSZÁGOK "." COUNTRY_ID " |
    | HR | targetHR.Hr | * Nem sikerült leképezni a vegyes eseteket |

    * Ha vegyes eseti sémát és táblanév nevet szeretne létrehozni a cél PostgreSQL-ben, forduljon a következőhöz: [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com) . Megadhatunk egy parancsfájlt a vegyes Case Table séma beállításához a cél PostgreSQL-adatbázisban.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd a **Microsoft. DataMigration** jobb oldalán válassza a **regisztráció** lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás Oracle és a TARGET Azure Database for PostgreSQL példányhoz.

    Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md)című cikket.

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
4. Az **új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **forráskiszolgáló típusa** szövegmezőben válassza az **Oracle** lehetőséget a **célkiszolgáló típusa** szövegmezőben, majd válassza a **Azure Database for PostgreSQL** lehetőséget.
5. A **tevékenység típusának** kiválasztása szakaszban válassza az **online adatáttelepítés** lehetőséget.

   ![Azure Database Migration Service-projekt létrehozása](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Másik lehetőségként választhatja a **projekt létrehozása** lehetőséget az áttelepítési projekt létrehozásához, és később végrehajthatja az áttelepítést.

6. Válassza a **Mentés** lehetőséget, és jegyezze fel, hogy az online áttelepítés végrehajtásához a Azure Database Migration Service sikeres használatát, majd válassza a **Létrehozás és Futtatás tevékenység** lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása

* A **forrás hozzáadása részletek** képernyőn adja meg a forrás Oracle-példány kapcsolati adatait.

  ![A Forrás adatainak hozzáadása képernyő](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Oracle OCI-illesztőprogram feltöltése

1. Válassza a **Mentés** lehetőséget, majd a **OCI-illesztőprogram telepítése** képernyőn jelentkezzen be az Oracle-fiókjába, és töltse le az illesztőprogramot **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37 128 586 BÁJT (s)) (SHA1 ellenőrzőösszeg: 865082268) [innen.](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst)
2. Töltse le az illesztőprogramot egy megosztott mappába.

   Győződjön meg arról, hogy a mappa meg van osztva a minimális írásvédett hozzáféréssel megadott felhasználónévvel. Azure Database Migration Service hozzáfér a megosztáshoz, és beolvassa a OCI-illesztőprogramot az Azure-ba, ha megszemélyesíti a megadott felhasználónevet.

   A megadott felhasználónévnek Windows felhasználói fióknak kell lennie.

   ![OCI-illesztőprogram telepítése](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés** lehetőséget, majd a **cél részletei** képernyőn adja meg a cél Azure Database for PostgreSQL-kiszolgáló kapcsolati adatait, amely a **HR** -séma üzembe helyezésének Azure Database for PostgreSQL előre kiépített példánya.

    ![A részleteket tartalmazó képernyő](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrás-adatbázis, akkor a Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázis-adatbázist.

    ![Leképezés céladatbázisokra](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

  Megjelenik az áttelepítési tevékenység ablak, és a tevékenység **állapota** **inicializálva** van.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Fut** értékre.

     ![Tevékenység állapota – fut](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Az **adatbázis neve** területen válasszon ki egy adott adatbázist, amely a **teljes adatterhelés** és a **növekményes adatszinkronizálási** műveletek áttelepítési állapotát adja meg.

    Az Adatok teljes betöltésénél az első betöltés migrálási állapota jelenik meg, a Növekményes adatszinkronizálásnál pedig az Adatváltozások rögzítése (CDC) állapot látható.

     ![Tevékenység állapota – Teljes betöltés kész](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Tevékenység állapota – Növekményes adatszinkronizálás](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.

   ![Átállás indítása](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis-áttelepítési **állapot megjelenik,** az alkalmazásokat az új cél Azure Database for PostgreSQL-példányhoz kell kötni.

 > [!NOTE]
 > Mivel a PostgreSQL alapértelmezés szerint a Schema. table. Column altípust használja, a jelen cikk korábbi részében a **Azure Database for PostgreSQL séma beállítása a sémában** című részében található parancsfájl használatával visszaállíthatja a nagybetűs esetről a kisbetűsre.

## <a name="next-steps"></a>Következő lépések

* Az Azure Database for PostgreSQL-be történő online migrálás végrehajtásakor felmerülő ismert hibákhoz és korlátozásokhoz kapcsolódó információk: [Az Azure Database for PostgreSQL online migrálásával kapcsolatos ismert hibák és kerülő megoldások](known-issues-azure-postgresql-online.md).
* A Azure Database Migration Service kapcsolatos információkért tekintse meg a [Azure Database Migration Service?](./dms-overview.md)című cikket.
* További információ a Azure Database for PostgreSQLről: mi a [Azure Database for PostgreSQL?](../postgresql/overview.md).
