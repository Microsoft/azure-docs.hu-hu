---
title: Az Adatmásolás növekményes másolása a Change Tracking használatával Azure Portal
description: Ebben az oktatóanyagban egy Azure Data Factory hoz létre egy olyan folyamattal, amely az Azure Blob Storage-ban lévő forrás-adatbázis változás-követési információi alapján tölti be a különbözeti adatokat a Azure SQL Database.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: c79d96e016459732ce71019511fa429d62d91f9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740137"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information-using-the-azure-portal"></a>Adatok növekményes betöltése Azure SQL Databaseból az Azure-ba Blob Storage a Change Tracking Information használatával a Azure Portal

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ebben az oktatóanyagban egy Azure Data Factory hoz létre egy olyan folyamattal, amely az Azure Blob Storage-ban lévő forrás-adatbázis **változás-követési** információi alapján tölti be a különbözeti adatokat a Azure SQL Database.  

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A forrásadattár előkészítése
> * Adat-előállító létrehozása
> * Társított szolgáltatások létrehozása.
> * Forrás, fogadó és változáskövetési adatkészletek létrehozása.
> * A teljes másolási folyamat létrehozása, futtatása és figyelése
> * A forrástáblában szereplő adatok hozzáadása vagy frissítése
> * A növekményes másolási folyamat létrehozása, futtatása és figyelése

## <a name="overview"></a>Áttekintés
Adatintegrációs megoldások esetében gyakran használt forgatókönyv az adatok növekményes betöltése egy kezdeti, teljes adatbetöltést követően. Bizonyos esetekben a forrásadattárban lévő adott időszakbeli módosított adatok egyszerűen felszeletelhetők (például: LastModifyTime, CreationTime). Bizonyos esetekben nincs kifejezett módja a legutóbbi adatfeldolgozásból származó változásadatok azonosításának. A változásadatok a változáskövetési technológiával azonosíthatóak, amelyeket egyes adattárak támogatnak, például az Azure SQL Database vagy az SQL Server.  Ez az oktatóanyag bemutatja, hogy az Azure Data Factory és az SQL változáskövetési technológia segítségével hogyan tölthetők be növekményesen az Azure SQL Database változásadatai az Azure Blob Storage-ba.  Az SQL változáskövetési technológiával kapcsolatos részletesebb információért lásd: [Változáskövetés az SQL Serveren](/sql/relational-databases/track-changes/about-change-tracking-sql-server).

## <a name="end-to-end-workflow"></a>Teljes körű munkafolyamat
Íme az adatok változáskövetési technológiával történő növekményes betöltési munkafolyamatának részletes lépései.

> [!NOTE]
> Az Azure SQL Database és az SQL Server is támogatja a változáskövetési technológiát. Ez az oktatóanyag az Azure SQL Database-t használja forrásadattárként. Használhat SQL Server példányt is.

1. **Előzményadatok kezdeti betöltése** (egyszeri futtatás):
    1. Change Tracking technológia engedélyezése a forrás-adatbázisban Azure SQL Databaseban.
    2. Az adatbázis SYS_CHANGE_VERSION kezdeti értékének beolvasása a módosított értékek rögzítésének alapterve alapján.
    3. Teljes körű adatok betöltése a forrás-adatbázisból egy Azure Blob Storage-ba.
2. **Változásadatok ütemezett növekményes betöltése** (időszakos futtatás az első adatbetöltést követően):
    1. Kérje le a SYS_CHANGE_VERSION régi és új értékét.
    3. A változásadatok betöltéséhez egyesítse a **sys.change_tracking_tables** táblázat módosított sorainak (a két SYS_CHANGE_VERSION érték közötti) elsődleges kulcsait és a **forrástábla** adatait, majd helyezze át a módosított adatokat a célhelyre.
    4. A következő alkalommal frissítse a SYS_CHANGE_VERSION értékét a változásadatok betöltéséhez.

## <a name="high-level-solution"></a>Összefoglaló jellegű megoldás
Ebben az oktatóanyagban két folyamatot hoz létre, amelyek az alábbi két műveletet hajtják végre:  

1. **Kezdeti betöltés:** egy másolási tevékenységgel rendelkező folyamatot fog létrehozni, amely a forrásadattár (Azure SQL Database) összes adatát átmásolja a céladattárba (Azure Blob Storage).

    ![Összes adat betöltése](media/tutorial-incremental-copy-change-tracking-feature-portal/full-load-flow-diagram.png)
1.  **Növekményes betöltés:** a következő tevékenységeket tartalmazó folyamatot fog létrehozni, majd időszakosan futtatni.
    1. Hozzon létre **két keresési tevékenységet** a SYS_CHANGE_VERSION régi és új értékének lekéréséhez az Azure SQL Database-ből, majd adja azt át a másolási tevékenységnek.
    2. Hozzon létre **egy másolási tevékenységet** a két SYS_CHANGE_VERSION érték közötti beillesztett/frissített/törölt adatok az Azure SQL Database-ből az Azure Blob Storage-be való másolásához.
    3. Hozzon létre **egy tárolt eljárási tevékenységet** a SYS_CHANGE_VERSION értékének a következő folyamatfuttatáshoz való frissítéséhez.

    ![Növekményes betöltés folyamatábrája](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-load-flow-diagram.png)


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek
* **Azure SQL Database**. Ezt az adatbázist használjuk **forrásadattárként**. Ha nem rendelkezik Azure SQL Database-adatbázissal, a létrehozásához szükséges lépésekért tekintse meg az [adatbázis létrehozása Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) cikkben.
* **Azure Storage-fiók**. A blobtárolót használjuk majd **fogadóadattárként**. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../storage/common/storage-account-create.md) ismertető cikket. Hozzon létre egy tárolót **adftutorial** néven. 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Adatforrás-tábla létrehozása Azure SQL Databaseban

1. Indítsa el **SQL Server Management Studio**, és kapcsolódjon a SQL Databasehoz.
2. A **Kiszolgálókezelőben** kattintson a jobb gombbal az **adatbázisra**, és válassza az **Új lekérdezés** elemet.
3. Futtassa a következő SQL-parancsot az adatbázison, és hozzon létre egy nevű táblát az `data_source_table` adatforrás-tárolóban.  

    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```

4. Engedélyezze a **változáskövetési** mechanizmust az adatbázisban és a forrástáblában (data_source_table) az alábbi SQL-lekérdezés futtatásával:

    > [!NOTE]
    > - Cserélje le az adatbázisnevet az adatbázis nevére, &lt; &gt; Azure SQL Database a data_source_table.
    > - Ebben a példában a rendszer két napig tárolja a módosított adatokat. Ha a módosított adatokat három naponta vagy annál ritkábban tölti be, nem minden módosított adat jelenik meg.  Növelje a CHANGE_RETENTION változó értékét, vagy ügyeljen arra, hogy a módosított adatok betöltései között ne teljen el két napnál több idő. További információk: [Változáskövetés engedélyezése adatbázishoz](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database).

    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  

    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. Hozzon létre egy új táblát, és az alábbi lekérdezés futtatásával tárolja alapértelmezett értékkel a ChangeTracking_version változót:

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```

    > [!NOTE]
    > Ha az adatok nem módosultak, miután engedélyezte a változáskövetést az SQL Database-ben, a változáskövetés verziójának értéke 0.
6. A következő lekérdezés futtatásával hozzon létre egy tárolt eljárást az adatbázisban. A folyamat ezt a tárolt eljárást hívja meg az előző lépésben létrehozott tábla változáskövetési verziójának frissítéséhez.

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS

    BEGIN

    UPDATE table_store_ChangeTracking_version
    SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName

    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-Az-ps) ismertető cikkben szereplő utasításokat a legújabb Azure PowerShell-modulok telepítéséhez.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **adatok és Analitika**  >  **Data Factory**:

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**.

     ![Új adat-előállító lap](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory.png)

   A Azure Data Factory nevének **globálisan egyedinek** kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a data factory nevét (például sajátnévADFTutorialDataFactory-ra), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

   *A "ADFTutorialDataFactory" nevű adatgyár nem érhető el*
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
4. Az **erőforráscsoport** esetében hajtsa végre az alábbi lépések egyikét:

      - Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.
      - Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét.   
         
        Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. Válassza a **V2 (előzetes verzió)** értéket a **verzió** esetén.
5. Válassza ki a Data Factory **helyét**. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Létrehozás** lehetőségre.      
8. Az irányítópulton a következő csempe jelenik meg, amelynek állapota: az **adatgyár üzembe helyezése**.

    ![adat-előállító üzembe helyezése csempe](media/tutorial-incremental-copy-change-tracking-feature-portal/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.

   ![Data factory kezdőlap](./media/tutorial-incremental-copy-change-tracking-feature-portal/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.
11. Az **első lépéseket bemutató** lapon váltson a **Szerkesztés** lapra a bal oldali panelen, ahogy az az alábbi képen látható:

    ![Folyamat létrehozása gomb](./media/tutorial-incremental-copy-change-tracking-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a szakaszban társított szolgáltatásokat hoz létre az Azure Storage-fiókjához és az adatbázisához Azure SQL Databaseban.

### <a name="create-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása
Ebben a lépésben az Azure Storage-fiókot társítja az adat-előállítóval.

1. Kattintson a **Kapcsolatok**, majd az **+ Új** elemre.

   ![Új kapcsolat gomb](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-connection-button-storage.png)
2. A **New Linked Service** (Új társított szolgáltatás) ablakban válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Continue** (Folytatás) elemre.

   ![Select Azure Blob Storage](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-storage.png)
3. A **New Linked Service** (Új társított szolgáltatás) ablakban végezze el az alábbi lépéseket:

    1. A **Name** (Név) mezőbe írja az **AzureStorageLinkedService** nevet.
    2. A **Storage-fiók neve** elemnél válassza ki saját Azure Storage-fiókját.
    3. Kattintson a **Mentés** gombra.

   ![Azure Storage-fiók beállításai](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása.
Ebben a lépésben összekapcsolja az adatbázist az adatelőállítóval.

1. Kattintson a **Kapcsolatok**, majd az **+ Új** elemre.
2. Az **Új társított szolgáltatás** ablakban válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Folytatás** elemre.
3. A **New Linked Service** (Új társított szolgáltatás) ablakban végezze el az alábbi lépéseket:

    1. A **Név** mezőben adja meg az **AzureSqlDatabaseLinkedService** értéket.
    2. Válassza ki a kiszolgáló **nevét a kiszolgálónév** mezőhöz.
    3. Válassza ki az adatbázist az **adatbázis neve** mezőhöz.
    4. A **Felhasználónév** mezőben adja meg a felhasználó nevét.
    5. A **Jelszó** mezőben adja meg a felhasználóhoz tartozó jelszót.
    6. A kapcsolat teszteléséhez kattintson a **Kapcsolat tesztelése** elemre.
    7. A társított szolgáltatás mentéséhez kattintson a **Mentés** gombra.

       ![Azure SQL Database társított szolgáltatás beállításai](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-sql-database-linked-service-settings.png)

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre, amelyek az adatforrást, az adatcél helyét és a SYS_CHANGE_VERSION változó tárolási helyét jelölik.

### <a name="create-a-dataset-to-represent-source-data"></a>Forrásadatokat képviselő adatkészlet létrehozása
Ebben a lépésben egy adatkészletet hoz létre, amely a forrásadatokat jelöli.

1. A fanézetben kattintson a **+ (plusz)** jelre, majd az **Adatkészlet** elemre.

   ![Új adatkészlet menü](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Befejezés** gombra.

   ![Forrásadatkészlet típusa – Azure SQL Database](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-sql-database.png)
3. Megjelenik egy új, az adatkészlet konfigurálására szolgáló lap. Az adatkészlet fanézetben is megjelenik. A **Tulajdonságok** ablakban módosítsa az adatkészlet nevét a következőre: **SourceDataset**.

   ![Forrásadatkészlet neve](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-name.png)    
4. Váltson a **Kapcsolat** lapra, és végezze el az alábbi lépéseket:

    1. A **Társított szolgáltatás** elemnél válassza az **AzureSqlDatabaseLinkedService** lehetőséget.
    2. A **Tábla** elemnél válassza a **[dbo].[data_source_table]** lehetőséget.

   ![Forráskapcsolat](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-connection.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Fogadóadattárba másolt adatokat képviselő adatkészlet létrehozása
Ebben a lépésben egy adatkészletet hoz létre, amely a forrásadattárból másolt adatokat jelöli. Előfeltételként létrehozta az adftutorial nevű tárolót az Azure Blob Storage-ban. Ha még nem létezik, hozza létre a tárolót, vagy állítsa be egy meglévő tároló nevét. Ebben az oktatóanyagban a kimeneti fájl nevét dinamikusan hozzuk létre a következő kifejezés használatával: `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

1. A fanézetben kattintson a **+ (plusz)** jelre, majd az **Adatkészlet** elemre.

   ![Új adatkészlet menü](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Befejezés** gombra.

   ![Fogadó adatkészlet típusa – Azure Blob Storage](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-type.png)
3. Megjelenik egy új, az adatkészlet konfigurálására szolgáló lap. Az adatkészlet fanézetben is megjelenik. A **Tulajdonságok** ablakban módosítsa az adatkészlet nevét a következőre: **SinkDataset**.

   ![Fogadó adatkészlet – név](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-name.png)
4. Váltson a **Kapcsolat** lapra a tulajdonságok ablakában, és végezze el az alábbi lépéseket:

    1. A **Társított szolgáltatás** mezőben válassza az **AzureStorageLinkedService** értéket.
    2. A **filePath****mappa** részéhez írja be az **adftutorial/incchgtracking** kifejezést.
    3. Adja meg **\@ a concat (növekményes, folyamat () értéket. RunId, '. txt ')** a **filepath** **fájljának** részeként.  

       ![Fogadó adatkészlet – kapcsolat](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-connection.png)

### <a name="create-a-dataset-to-represent-change-tracking-data"></a>Változáskövetési adatokat képviselő adatkészlet létrehozása
Ebben a lépésben egy adatkészletet hozunk létre a változáskövetés verziószámának tárolásához.  Előfeltételként létrehozta a table_store_ChangeTracking_version nevű táblát.

1. A fanézetben kattintson a **+ (plusz)** jelre, majd az **Adatkészlet** elemre.
2. Válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Befejezés** gombra.
3. Megjelenik egy új, az adatkészlet konfigurálására szolgáló lap. Az adatkészlet fanézetben is megjelenik. A **Tulajdonságok** ablakban módosítsa az adatkészlet nevét a következőre: **ChangeTrackingDataset**.
4. Váltson a **Kapcsolat** lapra, és végezze el az alábbi lépéseket:

    1. A **Társított szolgáltatás** elemnél válassza az **AzureSqlDatabaseLinkedService** lehetőséget.
    2. Válassza a **[dbo].[table_store_ChangeTracking_version]** értéket a **Tábla** mezőben.

## <a name="create-a-pipeline-for-the-full-copy"></a>Folyamat létrehozása teljes másolat készítéséhez
Ebben a lépésben egy másolási tevékenységgel rendelkező folyamatot fog létrehozni, amely a forrásadattár (Azure SQL Database) összes adatát átmásolja a céladattárba (Azure Blob Storage).

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd a **Folyamat** elemre.

    ![Képernyőfelvétel: az adatfeldolgozó folyamatának beállítása.](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu.png)
2. Megjelenik egy új, a folyamat konfigurálására szolgáló lap. A folyamat fanézetben is megjelenik. A **Tulajdonságok** ablakban módosítsa a folyamat nevét a következőre: **FullCopyPipeline**.

    ![A képernyőképen egy megadott nevű folyamat látható.](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-name.png)
3. A **Tevékenységek** eszközkészletben bontsa ki az **Adatfolyam** elemet, húzza át a **Másolás** tevékenységet a folyamat tervezőfelületére, és állítsa be a **FullCopyActivity** nevet.

    ![Teljes másolási tevékenység neve](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-activity-name.png)
4. Váltson a **Forrás** lapra, és válassza a **SourceDataset** elemet a **Forrásadatkészlet** mezőnél.

    ![Másolási tevékenység – forrás](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-source.png)
5. Váltson a **Fogadó** lapra, és válassza a **SinkDataset** lehetőséget a **Fogadó adatkészlet** mezőnél.

    ![Másolási tevékenység – fogadó](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-sink.png)
6. A folyamat meghatározásának érvényesítéséhez kattintson az **Érvényesítés** gombra az eszköztáron. Ellenőrizze, hogy nincs-e érvényesítési hiba. A **>>** gombra kattintva zárja be a **folyamatérvényesítési jelentést**.

    ![A folyamat érvényesítése](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-validate.png)
7. Az entitások (társított szolgáltatások, adatkészletek és folyamatok) közzétételéhez kattintson a **Közzététel** elemre. Várja meg, amíg befejeződik a közzétételi folyamat.

    ![A képernyőképen az összes közzététel gomb jelenik meg.](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button.png)
8. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet.

    ![Sikeres közzététel](./media/tutorial-incremental-copy-change-tracking-feature-portal/publishing-succeeded.png)
9. Az értesítéseket a bal oldalon található **Értesítések megjelenítése** gombra kattintva tekintheti meg. Az értesítések ablakának bezárásához kattintson az **X** gombra.

    ![Értesítések megjelenítése](./media/tutorial-incremental-copy-change-tracking-feature-portal/show-notifications.png)


### <a name="run-the-full-copy-pipeline"></a>A teljes másolási folyamat futtatása
Kattintson az **Aktiválás** gombra a folyamat eszköztárán, majd az **Aktiválás most** elemre.

![A képernyőképen a trigger (trigger) lehetőség van kiválasztva az indítás menüből.](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu.png)

### <a name="monitor-the-full-copy-pipeline"></a>A teljes másolási folyamat megfigyelése

1. Kattintson a bal oldali **Monitorozás** lapra. Ekkor a folyamat futtatása és állapota megjelenik a listában. A lista frissítéséhez kattintson a **Refresh** (Frissítés) elemre. A Műveletek oszlop hivatkozásai lehetővé teszik, hogy megtekintse a folyamat futtatásához társított tevékenységfuttatásokat, és hogy újra futtassa a folyamatot.

    ![Képernyőfelvétel: az adatfeldolgozó folyamatait bemutató képernyőkép.](./media/tutorial-incremental-copy-change-tracking-feature-portal/monitor-full-copy-pipeline-run.png)
2. A folyamat futtatásához társított tevékenységfuttatások megtekintéséhez kattintson a **Tevékenységfuttatások megtekintése** hivatkozásra a **Műveletek** oszlopban. Csak egy tevékenység található a folyamatban, ezért csak egy bejegyzés fog szerepelni a listában. A Folyamatfuttatások nézetre való visszaváltáshoz kattintson a fent található **Folyamatok** hivatkozásra.

    ![Képernyőfelvétel: az adatelőállítók tevékenység-futtatási funkciói, a folyamatokra mutató hivatkozás.](./media/tutorial-incremental-copy-change-tracking-feature-portal/activity-runs-full-copy.png)

### <a name="review-the-results"></a>Az eredmények áttekintése
Egy `incremental-<GUID>.txt` nevű fájl található az `adftutorial` nevű tároló `incchgtracking` mappájában.

![Kimeneti fájl teljes másolásból](media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-output-file.png)

A fájlnak az adatbázisból származó adatokkal kell rendelkeznie:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>További adatok hozzáadása a forrástáblához

Futtassa az alábbi lekérdezést az adatbázison egy sor hozzáadásához és egy sor frissítéséhez.

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

```

## <a name="create-a-pipeline-for-the-delta-copy"></a>Folyamat létrehozása a változásadatok másolásához
Ebben a lépésben a következő tevékenységeket tartalmazó folyamatot fog létrehozni, majd időszakosan futtatni. A **keresési tevékenységek** lekérik a SYS_CHANGE_VERSION régi és új értékét az Azure SQL Database-ből, majd átadják azt a másolási tevékenységnek. A **másolási tevékenység** a két SYS_CHANGE_VERSION érték közötti beillesztett/frissített/törölt adatokat az Azure SQL Database-ből az Azure Blob Storage-be másolja. A **tárolt eljárási tevékenység** frissíti a SYS_CHANGE_VERSION értékét a következő folyamatfuttatáshoz.

1. A Data Factory felhasználói felületen váltson a **Szerkesztés** lapra. Kattintson a **+ (plusz)** gombra a bal oldali ablaktáblán, majd kattintson a **folyamat** elemre.

    ![A képernyőfelvétel azt mutatja be, hogyan lehet folyamatokat létrehozni egy adatelőállítóban.](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu-2.png)
2. Megjelenik egy új, a folyamat konfigurálására szolgáló lap. A folyamat fanézetben is megjelenik. A **Tulajdonságok** ablakban módosítsa a folyamat nevét a következőre: **IncrementalCopyPipeline**.

    ![Folyamat neve](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-pipeline-name.png)
3. A **Tevékenységek** eszközkészletben bontsa ki az **Általános** elemet, és húzza a **Keresés** tevékenységet a folyamat tervezőfelületére. Állítsa a tevékenység nevét a következőre: **LookupLastChangeTrackingVersionActivity**. Ez a tevékenység a **table_store_ChangeTracking_version** táblában tárolt utolsó másolási művelet során használt változáskövetési verziót kapja meg.

    ![A képernyőképen egy keresési tevékenységgel rendelkező folyamat látható.](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-name.png)
4. Váltson a **Beállítások** lapra a **tulajdonságok** ablakában, és válassza a **ChangeTrackingDataset** elemet a **Forrásadatkészlet** mezőnél.

    ![A képernyőképen a Tulajdonságok ablak Beállítások lapja látható.](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-settings.png)
5. Húzza a **keresési** tevékenységet a **Tevékenységek** eszközkészletből a folyamat tervezőfelületére. Állítsa a tevékenység nevét a következőre: **LookupCurrentChangeTrackingVersionActivity**. Ez a tevékenység az aktuális változáskövetési verziót kapja meg.

    ![A képernyőfelvételen két keresési tevékenységgel rendelkező folyamat látható.](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-name.png)
6. Váltson a **Beállítások** lapra a **tulajdonságok** ablakában, és hajtsa végre a következő lépéseket:

   1. Válassza ki a **SourceDataset** elemet a **Forrásadatkészlet** mezőnél.
   2. A **Lekérdezés használata** elemnél válassza a **Lekérdezés** lehetőséget.
   3. A **Lekérdezés** elemhez adja meg az alábbi SQL-lekérdezést.

       ```sql
       SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion
       ```

      ![A képernyőképen egy, a Tulajdonságok ablak Beállítások lapjára felvett lekérdezés jelenik meg.](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-settings.png)
7. A **Tevékenységek** eszközkészletben bontsa ki az **Adatfolyam** elemet, és húzza át a **Másolás** tevékenységet a folyamat tervezőfelületére. Állítsa a tevékenység nevét a következőre: **IncrementalCopyActivity**. Ez a tevékenység átmásolja az utolsó és az aktuális változáskövetési verzió közötti adatokat a céladattárba.

    ![Másolási tevékenység – név](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-activity-name.png)
8. Váltson a **Forrás** lapra a **tulajdonságok** ablakában, és hajtsa végre a következő lépéseket:

   1. Válassza ki a **SourceDataset** elemet a **Forrásadatkészlet** mezőnél.
   2. A **Lekérdezés használata** elemnél válassza a **Lekérdezés** lehetőséget.
   3. A **Lekérdezés** elemhez adja meg az alábbi SQL-lekérdezést.

       ```sql
       select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}
       ```

      ![Másolási tevékenység – forrás beállításai](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-source-settings.png)
9. Váltson a **Fogadó** lapra, és válassza a **SinkDataset** lehetőséget a **Fogadó adatkészlet** mezőnél.

    ![Másolási tevékenység – fogadóbeállítások](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-sink-settings.png)
10. **Mindkét keresési tevékenységet kapcsolja a másolási tevékenységhez**, egymás után. Húzza a **keresési** tevékenységhez tartozó **zöld** gombot a **másolási** tevékenységhez.

    ![A keresési és másolási tevékenységek összekapcsolása](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-lookup-and-copy.png)
11. Húzza át a **Tárolt eljárás** tevékenységet a **tevékenységek** eszközkészletéből a folyamat tervezőfelületére. Állítsa a tevékenység nevét a következőre: **StoredProceduretoUpdateChangeTrackingActivity**. Ez a tevékenység frissíti a **table_store_ChangeTracking_version** táblában található változáskövetési verziót.

    ![Tárolt eljárási tevékenység – név](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-activity-name.png)
12. Váltson az *SQL-fiók** lapra, majd a **Társított szolgáltatás** elemnél válassza az **AzureSqlDatabaseLinkedService** lehetőséget.

    ![Tárolt eljárási tevékenység – SQL-fiók](./media/tutorial-incremental-copy-change-tracking-feature-portal/sql-account-tab.png)
13. Váltson a **Tárolt eljárás** lapra, és végezze el az alábbi lépéseket:

    1. A **tárolt eljárás neveként** válassza az **Update_ChangeTracking_Version** lehetőséget.  
    2. Válassza az **Importálási paraméter** lehetőséget.
    3. A **tárolt eljárás paramétereinél** adja meg az alábbi értékeket a paraméterekhez:

        | Név | Típus | Érték |
        | ---- | ---- | ----- |
        | CurrentTrackingVersion | Int64 | @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion} |
        | TableName | Sztring | @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName} |

        ![Tárolt eljárási tevékenység – Paraméterek](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-parameters.png)
14. **A másolási tevékenység összekapcsolása a tárolt eljárási tevékenységgel**. Húzza a másolási tevékenységhez tartozó **zöld** gombot a tárolt eljárási tevékenységhez.

    ![Másolási és tárolt eljárási tevékenységek összekapcsolása](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-copy-stored-procedure.png)
15. Kattintson az **Érvényesítés** elemre az eszköztáron. Ellenőrizze, hogy nincs-e érvényesítési hiba. A **>>** gombra kattintva zárja be a **folyamatérvényesítési jelentés** ablakát.

    ![Érvényesítés gomb](./media/tutorial-incremental-copy-change-tracking-feature-portal/validate-button.png)
16. Az entitásokat (társított szolgáltatásokat, adatkészleteket és folyamatokat) az **Összes közzététele** gombra kattintva teheti közzé a Data Factory szolgáltatásban. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet.

       ![Képernyőfelvétel: az adatelőállító összes közzétételének gombja.](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button-2.png)    

### <a name="run-the-incremental-copy-pipeline"></a>A növekményes másolási folyamat futtatása
1. Kattintson az **Aktiválás** gombra a folyamat eszköztárán, majd az **Aktiválás most** elemre.

    ![A képernyőképen egy tevékenységgel rendelkező folyamat látható, amely a trigger menüből kiválasztott trigger most lehetőséggel jelenik meg.](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu-2.png)
2. A **Folyamatfuttatás** ablakban kattintson a **Befejezés** gombra.

### <a name="monitor-the-incremental-copy-pipeline"></a>A növekményes másolási folyamat figyelése
1. Kattintson a bal oldali **Monitorozás** lapra. Ekkor a folyamat futtatása és állapota megjelenik a listában. A lista frissítéséhez kattintson a **Refresh** (Frissítés) elemre. A **Műveletek** oszlop hivatkozásai lehetővé teszik, hogy megtekintse a folyamat futtatásához társított tevékenységfuttatásokat, és hogy újra futtassa a folyamatot.

    ![Képernyőfelvétel: a folyamat futtatására szolgáló adatfeldolgozó.](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-pipeline-runs.png)
2. A folyamat futtatásához társított tevékenységfuttatások megtekintéséhez kattintson a **Tevékenységfuttatások megtekintése** hivatkozásra a **Műveletek** oszlopban. Csak egy tevékenység található a folyamatban, ezért csak egy bejegyzés fog szerepelni a listában. A Folyamatfuttatások nézetre való visszaváltáshoz kattintson a fent található **Folyamatok** hivatkozásra.

    ![Képernyőfelvétel: a folyamat futása egy olyan adatfeldolgozó esetében, amelynek több jelöltje sikeres.](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-activity-runs.png)


### <a name="review-the-results"></a>Az eredmények áttekintése
A második fájl az `adftutorial` nevű tároló `incchgtracking` mappájában található.

![Kimeneti fájl növekményes másolásból](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-output-file.png)

A fájlnak csak az adatbázisból származó különbözeti adatokkal kell rendelkeznie. Az `U` karaktert tartalmazó rekord a frissített sor az adatbázisban, az `I` karaktert tartalmazó rekord pedig a hozzáadott sor.

```
1,update,10,2,U
6,new,50,1,I
```
Az első három oszlop a data_source_table táblából származó módosított adatokat tartalmazza. Az utolsó két oszlop a változáskövetési rendszer táblájából származó metaadatokat tartalmazza. A negyedik oszlop az egyes módosított sorokra vonatkozó SYS_CHANGE_VERSION értéket tartalmazza. Az ötödik oszlop a műveletet tartalmazza: U = frissítés, I = beszúrás.  A változáskövetési adatokkal kapcsolatos információért lásd: [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql).

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10            2                                 U
6        new     50            1                                 I
```

## <a name="next-steps"></a>Következő lépések
Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan másolhat új és módosított fájlokat csak a LastModifiedDate alapján:

> [!div class="nextstepaction"]
> [Új fájlok másolása a lastmodifieddate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
