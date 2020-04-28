---
title: 'Oktatóanyag: RDS SQL Server online migrálása SQL Database'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan végezhet el egy online áttelepítést az RDS SQL Serverról Azure SQL Database önálló adatbázisra vagy felügyelt példányra az Azure Database Migration Service használatával.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 8d538deca610fd9981d401d28b6bea1c31c6d4c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80298872"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-database-managed-instance-online-using-dms"></a>Oktatóanyag: RDS SQL Server migrálása Azure SQL Database vagy Azure SQL Database felügyelt példányra online a DMS használatával
A Azure Database Migration Service segítségével áttelepítheti az adatbázisokat egy RDS SQL Server-példányról a [Azure SQL Databasere](https://docs.microsoft.com/azure/sql-database/) vagy egy [Azure SQL Database felügyelt példányra](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) , minimális állásidővel. Ebben az oktatóanyagban a **Adventureworks2012** -adatbázist a Azure Database Migration Service SQL Server 2012 (vagy újabb) egy RDS SQL Server példányára állítja át, hogy Azure SQL Database vagy Azure SQL Database felügyelt példányra telepítse.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Hozzon létre egy Azure SQL Database vagy egy Azure SQL Database felügyelt példány példányát. 
> * A mintaséma migrálása a Data Migration Assistant szolgáltatás használatával.
> * Egy Azure Database Migration Service-példány létrehozása.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.
> * Migrálási jelentés letöltése.

> [!NOTE]
> Az online áttelepítés végrehajtásához a Azure Database Migration Service használatával egy példányt kell létrehozni a prémium szintű díjszabás alapján. További információkért tekintse meg a Azure Database Migration Service [díjszabását](https://azure.microsoft.com/pricing/details/database-migration/) ismertető oldalt.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk az RDS SQL Serverról Azure SQL Databasere vagy Azure SQL Database felügyelt példányra történő online áttelepítést ismerteti.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Hozzon létre egy [RDS SQL Server-adatbázist](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* Hozzon létre egy Azure SQL Database egy példányát, amelyet a [Azure Portal Azure SQL Database-adatbázis létrehozása](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)című cikkben részletesen ismertet.

    > [!NOTE]
    > Ha Azure SQL Database felügyelt példányra végez áttelepítést, kövesse a [Azure SQL Database felügyelt példány létrehozása](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)című cikk részletes ismertetését, majd hozzon létre egy **AdventureWorks2012**nevű üres adatbázist. 
 
* Töltse le a [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) 3.3-as vagy újabb verzióját.
* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Servicehoz a Azure Resource Manager üzembe helyezési modell használatával. Ha Azure SQL Database felügyelt példányra végez áttelepítést, mindenképpen hozza létre a DMS-példányt ugyanabban a virtuális hálózatban, amelyet a Azure SQL Database felügyelt példányhoz használ, de egy másik alhálózaton.  Ha másik virtuális hálózatot használ a DMS-hez, létre kell hoznia egy virtuális hálózatot a két virtuális hálózat között. A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](https://docs.microsoft.com/azure/virtual-network/), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.

    > [!NOTE]
    > Ha a virtuális hálózat beállítása során ExpressRoute használ a Microsoft számára, adja hozzá a következő szolgáltatási [végpontokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
    >
    > * Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont stb.)
    > * Tárolási végpont
    > * Service Bus-végpont
    >
    > Erre a konfigurációra azért van szükség, mert a Azure Database Migration Service nem rendelkezik internetkapcsolattal. 

* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport szabályai nem gátolják meg a következő bejövő kommunikációs portok Azure Database Migration Service: 443, 53, 9354, 445, 12000. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)című cikket.
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse az SQL-kiszolgáló forrását, amely alapértelmezés szerint az 1433-as TCP-port.
* Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure SQL-adatbáziskiszolgáló számára, hogy az Azure SQL Database Migration Service hozzáférhessen a céladatbázisokhoz. Adja meg a Azure Database Migration Servicehoz használt virtuális hálózat alhálózati tartományát.
* Győződjön meg arról, hogy az RDS SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok egy olyan fiókhoz vannak társítva, amely a "processadmin" kiszolgálói szerepkör tagja, valamint az összes áttelepítendő adatbázis "db_owner" adatbázis-szerepkörének tagja.
* Győződjön meg arról, hogy a TARGET Azure SQL Database-példányhoz való kapcsolódáshoz használt hitelesítő adatok SZABÁLYOZzák az adatbázis engedélyeit a cél Azure SQL-adatbázisokon, valamint a sysadmin (rendszergazda) szerepkör tagját, ha Azure SQL Database felügyelt példányra végez áttelepítést
* A forrásként szolgáló RDS SQL Server verziójának a 2012-es vagy újabb SQL Server kell lennie. A futó SQL Server-példány verziójának megállapításához lásd [az SQL Server és összetevői verziójának, kiadásának és frissítési szintjének megállapításával](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an) foglalkozó cikket.
* Engedélyezze az adatváltozások rögzítését (CDC) az RDS SQL Server adatbázison és az összes áttelepítésre kiválasztott felhasználói táblán.
    > [!NOTE]
    > Az alábbi szkripttel engedélyezheti a CDC használatát egy RDS SQL Server-adatbázison.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Az alábbi szkripttel engedélyezheti a CDC használatát az összes táblán.
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
* Tiltsa le az adatbázistriggereket az Azure SQL-céladatbázisban.
    > [!NOTE]
    > Az Azure SQL-céladatbázis triggereit az alábbi lekérdezéssel keresheti meg:
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    További információk: [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása
A DMA használatával telepítse át a sémát Azure SQL Databaseba.

> [!NOTE]
> Mielőtt létrehozna egy migrálási projektet a DMA-ban, győződjön meg arról, hogy az előfeltételekben meghatározottak alapján már létrehozott egy Azure SQL-adatbázist. Ebben az oktatóanyagban a Azure SQL Database neve **AdventureWorks2012**, de megadhatja a kívánt nevet.

Az **AdventureWorks2012** séma Azure SQL Database-be való migrálásához végezze el a következő lépéseket:

1. A Data Migration Assistant programban válassza az Új (+) ikont, majd a **Projekt típusa** alatt válassza a **Migrálás** lehetőséget.
2. Nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza az **SQL Server** elemet, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database** lehetőséget.
3. A **Migrálási hatókör** alatt válassza a **Csak a séma** lehetőséget.

    A fent leírt lépések elvégzése után megjelenik a DMA felhasználói felülete, ahogy az a következő képen látható:

    ![Data Migration Assistant-projekt létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.
5. A DMA-ban adja meg az SQL Server forráskapcsolati adatait, válassza a **Csatlakozás** lehetőséget, majd válassza ki az **AdventureWorks2012** adatbázist.

    ![Data Migration Assistant forráskapcsolati adatok](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Kattintson a **tovább**gombra, a **Csatlakozás a célkiszolgálóra**lehetőségnél adja meg az Azure SQL Database cél kapcsolati adatait, válassza a **Csatlakozás**lehetőséget, majd válassza ki azt a **AdventureWorksAzure** -adatbázist, amelyet előre kiépített Azure SQL Databaseban.

    ![Data Migration Assistant célkapcsolati adatok](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. A **Tovább** lehetőséggel lépjen tovább az **Objektumok kiválasztása** képernyőre, ahol megadhatja azokat a sémaobjektumokat az **AdventureWorks2012** adatbázisban, amelyeket üzembe kell helyezni az Azure SQL Database-ben.

    Alapértelmezés szerint az összes objektum ki van jelölve.

    ![SQL-szkriptek létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Az **SQL-szkript létrehozása** lehetőséggel hozza létre az SQL-szkripteket, majd tekintse át azokat, hogy észrevehesse az esetleges hibákat.

    ![Sémaszkript](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Válassza a **Séma üzembe helyezése** lehetőséget a séma üzembe helyezéséhez az Azure SQL Database-ben. Az üzembe helyezés után ellenőrizze a célt az esetleges hibákért.

    ![Séma üzembe helyezése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd a **Microsoft. DataMigration**jobb oldalán válassza a **regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, amelyen az Azure Database Migration Service példányát létre szeretné hozni. 

5. Válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás-SQL Server és a célként megadott Azure SQL Database példányhoz.

    Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](https://aka.ms/DMSVnet)című cikket.

6. Válasszon árképzési szintet; az online áttelepítés esetében ügyeljen arra, hogy a prémium szintű díjszabást válassza.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

     ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.

     ![Az Azure Database Migration Service személyes példányának megkeresése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az **új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **forráskiszolgáló típusa** szövegmezőben válassza a **SQL Server AWS RDS**lehetőséget, a **célkiszolgáló típusa** szövegmezőben válassza a **Azure SQL Database**lehetőséget.

    > [!NOTE]
    > A célkiszolgáló típusa beállításnál válassza a **Azure SQL Database** lehetőséget az áttelepítéshez Azure SQL Database egypéldányos adatbázisra, valamint egy Azure SQL Database felügyelt példányra.

5. A **tevékenység típusának** kiválasztása szakaszban válassza az **online adatáttelepítés**lehetőséget.

    > [!IMPORTANT]
    > Ügyeljen arra, hogy **online adatáttelepítést**válasszon; az offline áttelepítések nem támogatottak ebben a forgatókönyvben.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Másik lehetőségként választhatja a **projekt létrehozása** lehetőséget az áttelepítési projekt létrehozásához, és később végrehajthatja az áttelepítést.

6. Kattintson a **Mentés** gombra.

7. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

    ![Database Migration Service-tevékenység létrehozása és futtatása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server-példány kapcsolati adatait.

    Gondoskodjon róla, hogy az SQL Server-példány neveként teljes tartománynevet (FQDN) használjon.

2. Ha nem telepített megbízható tanúsítványt a forráskiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítvánnyal titkosított TLS-kapcsolatok nem biztosítanak erős biztonságot. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Az önaláírt tanúsítványokat nem szabad a TLS-t használni éles környezetben vagy az internethez csatlakozó kiszolgálókon.

   ![Forrás részletei](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés** lehetőséget, majd a **Migrálási cél részletei** képernyőn adja meg a célul szolgáló Azure SQL-adatbáziskiszolgáló kapcsolati adatait. Ez a cél az az Azure SQL Database, amelyen üzembe helyezte az **AdventureWorks2012** sémát a DMA-val.

    ![Cél kiválasztása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis neve megegyezik a forrásadatbázis nevével, az Azure Database Migration Service alapértelmezés szerint a céladatbázist választja ki.

    ![Leképezés céladatbázisokra](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Válassza a **Mentés**, lehetőséget, majd a **Táblák kiválasztása** képernyőn bontsa ki a táblák listáját, és tekintse át az érintett mezőket.

    Az Azure Database Migration Service automatikusan kiválasztja az összes üres forrástáblát, amely a célként szolgáló Azure SQL Database-példányon megtalálható. Ha olyan táblákat szeretne újratelepíteni, amelyek már tartalmaznak adatfájlokat, explicit módon ki kell választania a táblákat ezen a képernyőn.

    ![Select tables (Táblák kiválasztása)](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. A következő **speciális Online áttelepítési beállítások**megadása után válassza a **Mentés**lehetőséget.

    | Beállítás | Leírás |
    | ------------- | ------------- |
    | **A párhuzamosan betölteni kívánt táblázatok maximális száma** | Meghatározza, hogy a DMS hány táblát hajt végre párhuzamosan az áttelepítés során. Az alapértelmezett érték 5, de beállítható úgy, hogy az optimális érték legyen az adott áttelepítési igényeknek megfelelő POC-Migrálás alapján. |
    | **A forrástábla csonkítása esetén** | Meghatározza, hogy a DMS csonkolja-e a cél táblát az áttelepítés során. Ez a beállítás akkor lehet hasznos, ha egy vagy több tábla az áttelepítési folyamat részeként van csonkítva. |
    | **A nagyméretű objektumok (LOB) beállításainak konfigurálása** | Megadja, hogy a DMS nem telepít-e le korlátlan LOB-adatmennyiséget, vagy korlátozza az áttelepített LOB-adatmennyiséget.  Ha a LOB-adatáttelepítés korlátja korlátozott, a rendszer csonkolja a korláton túli LOB-adatkereteket. Éles áttelepítés esetén javasolt a **korlátlan LOB-méret engedélyezése** az adatvesztés elkerülése érdekében. Ha a korlátlan LOB-méret engedélyezését adja meg, jelölje be a **LOB-adat áttelepítése egyetlen blokkban, ha az LOB-méret kisebb, mint (kb) a** teljesítmény javítása érdekében jelölőnégyzetet. |

    ![A speciális Online áttelepítési beállítások megadása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**.

    ![Tevékenység állapota: Inicializálás](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Fut** értékre.

2. Kattintson egy adatbázisra az **Adatok teljes betöltése** és a **Növekményes adatszinkronizálás** műveletek migrálási állapotának megtekintéséhez.

    ![Tevékenység állapota: Folyamatban](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

    ![Átállás indítása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.
3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis migrálási állapota **Befejezve** értékre vált, csatlakoztassa alkalmazásait az új Azure SQL-céladatbázishoz.

    ![Tevékenység állapota: Befejezve](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>További lépések

* Az Azure SQL Database-be történő online áttelepítéssel kapcsolatos ismert problémákról és korlátozásokról a [Azure SQL Database online áttelepítéssel kapcsolatos ismert problémák és megkerülő megoldások](known-issues-azure-sql-online.md)című cikkben olvashat.
* Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* További információ a Azure SQL Databaseről: [Mi a Azure SQL Database szolgáltatás?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
* Azure SQL Database felügyelt példányokkal kapcsolatos információkért tekintse meg [Azure SQL Database felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)lapon.
