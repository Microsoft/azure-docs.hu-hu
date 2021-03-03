---
title: Adatmásolás növekményes másolásával az adatváltozások rögzítése használatával
description: Ebben az oktatóanyagban létrehoz egy Azure Data Factory folyamatot, amely a különbözeti adatok növekményes másolását végzi egy Azure SQL felügyelt példány-adatbázisban lévő táblából az Azure Storage-ba.
ms.author: nihurt
author: hurtn
ms.service: data-factory
ms.topic: tutorial
ms.date: 02/18/2021
ms.openlocfilehash: a00ec8698b188b8fa87935e498e8cfab3aeab5aa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724982"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>Adatok növekményes betöltése az Azure SQL felügyelt példányairól az Azure Storage-ba az adatváltozások rögzítése (CDC) használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ebben az oktatóanyagban egy Azure-beli adat-előállítót hoz létre egy olyan folyamattal, amely a forrásként szolgáló Azure SQL felügyelt példány-adatbázisában lévő **adatváltozások rögzítése (CDC)** adatain alapuló különbözeti adatokat tölt be Azure Blob Storage-ba  

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A forrásadattár előkészítése
> * Adat-előállító létrehozása
> * Társított szolgáltatások létrehozása.
> * Forrás- és fogadó-adatkészletek létrehozása.
> * A folyamat létrehozása, hibakeresése és futtatása a módosult adatértékek kereséséhez
> * Adatforrás módosítása
> * A teljes növekményes másolási folyamat befejezése, futtatása és figyelése

## <a name="overview"></a>Áttekintés
Az adattárak, például az Azure SQL felügyelt példányai (MI) és a SQL Server által támogatott adatváltozás-rögzítési technológia segítségével azonosíthatók a módosított adatmennyiségek.  Ez az oktatóanyag azt ismerteti, hogyan használható a Azure Data Factory az SQL Change adatrögzítési technológiával az Azure SQL felügyelt példányaiból származó Delta adatok növekményes betöltéséhez az Azure Blob Storageba.  Az SQL változási adatrögzítési technológiával kapcsolatos részletesebb információkért lásd: [adatváltozások rögzítése SQL Serverban](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

## <a name="end-to-end-workflow"></a>Teljes körű munkafolyamat
Itt láthatók a teljes körű munkafolyamat-lépések, amelyek az adatváltozások rögzítése technológia használatával fokozatosan töltődnek be az adatmennyiséggel.

> [!NOTE]
> Az Azure SQL MI és SQL Server is támogatja az adatváltozások rögzítésére szolgáló technológiát. Ez az oktatóanyag az Azure SQL felügyelt példányát használja forrásként szolgáló adattárként. Vagy egy helyszíni SQL Servert is használhat.

## <a name="high-level-solution"></a>Összefoglaló jellegű megoldás
Ebben az oktatóanyagban létrehoz egy folyamatot, amely a következő műveleteket hajtja végre:  

   1. Hozzon létre egy **keresési tevékenységet** a SQL Database CDC táblában szereplő módosított rekordok számának megszámlálásához, és adja át azt egy if Condition tevékenységnek.
   2. Hozzon létre egy **IF feltételt** , és ellenőrizze, hogy módosult-e a rekordok, és ha igen, hívja meg a másolási tevékenységet.
   3. Hozzon létre egy **másolási tevékenységet** a CDC tábla beszúrt/frissített/törölt értékének az Azure Blob Storageba való másolásához.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek
* **Azure SQL Database felügyelt példány**. Ezt az adatbázist használjuk **forrásadattárként**. Ha nem rendelkezik Azure SQL Database felügyelt példánnyal, tekintse meg az egy [Azure SQL Database felügyelt példány létrehozása](../azure-sql/managed-instance/instance-create-quickstart.md) című cikket a létrehozás lépéseihez.
* **Azure Storage-fiók**. A blobtárolót használjuk majd **fogadóadattárként**. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../storage/common/storage-account-create.md) ismertető cikket. Hozzon létre egy **RAW** nevű tárolót. 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Adatforrás-tábla létrehozása Azure SQL Databaseban

1. Indítsa el **SQL Server Management Studio**, és kapcsolódjon az Azure SQL felügyelt példányok kiszolgálójához.
2. A **Kiszolgálókezelőben** kattintson a jobb gombbal az **adatbázisra**, és válassza az **Új lekérdezés** elemet.
3. Futtassa a következő SQL-parancsot az Azure SQL felügyelt példányok adatbázisán, és hozzon létre egy nevű táblát az `customers` adatforrás-tárolóban.  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. A következő SQL-lekérdezés futtatásával engedélyezheti az adatbázis és a forrástábla (ügyfelek) **adatváltozás-rögzítési** mechanizmusát:

    > [!NOTE]
    > - Cserélje le &lt; a forrásoldali séma nevét a &gt; Customers (ügyfelek) táblát tartalmazó Azure SQL mi-sémára.
    > - Az adatváltozások rögzítése nem tesz semmit a nyomon követett táblát módosító tranzakciók részeként. Ehelyett az INSERT, az Update és a DELETE művelet íródik a tranzakciónaplóba. A módosítási táblákban elhelyezett adatmennyiség nem felügyelhető, ha nem rendszeres időközönként és szisztematikusan aszalt szilva az adatmennyiséget. További információ: az [adatváltozások rögzítésének engedélyezése adatbázishoz](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server#enable-change-data-capture-for-a-database)

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. A következő parancs futtatásával szúrja be az adatbevitelt a Customers (ügyfelek) táblába:

    ```sql
     insert into customers 
         (customer_id, first_name, last_name, email, city) 
     values 
         (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
         (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > Az adatváltozások rögzítése előtt a rendszer nem rögzíti a tábla korábbi módosításait.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **adatok és Analitika**  >  **Data Factory**:

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**.

     ![Új adat-előállító lap](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png)

   Az Azure-beli adatgyár nevének **globálisan egyedinek** kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a data factory nevét (például sajátnévADFTutorialDataFactory-ra), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

    *A "ADFTutorialDataFactory" nevű adatgyár nem érhető el.*
3. A **Verzió** résznél válassza a **V2** értéket.
4. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
5. Az **erőforráscsoport** esetében hajtsa végre az alábbi lépések egyikét:

   1. Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.
   2. Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét.   
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
5. Válassza ki a Data Factory **helyét**. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
6. Válassza ki a **git engedélyezése** lehetőséget.     
7. Kattintson a **Létrehozás** lehetőségre.
8. Miután az üzembe helyezés befejeződött, kattintson az **Ugrás erőforrásra** elemre.

   ![A képernyőképen egy üzenet jelenik meg, amely szerint a telepítés befejeződött, és az erőforráshoz való ugrás lehetősége.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.

   ![Képernyőfelvétel: a telepített adatfeldolgozó.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.
11. Az **első lépéseket bemutató** lapon váltson a **Szerkesztés** lapra a bal oldali panelen, ahogy az az alábbi képen látható:

    ![Folyamat létrehozása gomb](./media/tutorial-incremental-copy-change-data-capture-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a szakaszban társított szolgáltatásokat hoz létre az Azure Storage-fiókjához és az Azure SQL MI-hoz.

### <a name="create-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása
Ebben a lépésben az Azure Storage-fiókot társítja az adat-előállítóval.

1. Kattintson a **Kapcsolatok**, majd az **+ Új** elemre.

   ![Új kapcsolat gomb](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png)
2. A **New Linked Service** (Új társított szolgáltatás) ablakban válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Continue** (Folytatás) elemre.

   ![Select Azure Blob Storage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png)
3. A **New Linked Service** (Új társított szolgáltatás) ablakban végezze el az alábbi lépéseket:

   1. A **Name** (Név) mezőbe írja az **AzureStorageLinkedService** nevet.
   2. A **Storage-fiók neve** elemnél válassza ki saját Azure Storage-fiókját.
   3. Kattintson a **Mentés** gombra.

   ![Azure Storage-fiók beállításai](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-mi-database-linked-service"></a>Azure SQL MI Database társított szolgáltatás létrehozása.
Ebben a lépésben összekapcsolja az Azure SQL-adatbázisát az adatelőállítóval.

> [!NOTE]
> Az SQL MI használatával a nyilvános és magánhálózati végponton keresztüli hozzáférésre vonatkozó információkat [itt](./connector-azure-sql-managed-instance.md#prerequisites) tekintheti meg. Ha az egyik privát végpontot használja, a folyamatot saját üzemeltetésű integrációs modul használatával kell futtatnia. Ugyanez vonatkozik azokra a SQL Server helyszíni gépekre is, amelyek virtuális gépeken vagy VNet-forgatókönyvekben futnak.

1. Kattintson a **Kapcsolatok**, majd az **+ Új** elemre.
2. Az **új társított szolgáltatás** ablakban válassza **Azure SQL Database felügyelt példány** lehetőséget, majd kattintson a **Folytatás** gombra.
3. A **New Linked Service** (Új társított szolgáltatás) ablakban végezze el az alábbi lépéseket:

   1. Adja  meg a AzureSqlMI1 **nevet a név** mezőben.
   2. Válassza ki a **kiszolgáló neve** mezőhöz tartozó SQL Servert.
   4. Válassza ki az **adatbázis neve** mezőhöz tartozó SQL-adatbázist.
   5. A **Felhasználónév** mezőben adja meg a felhasználó nevét.
   6. A **Jelszó** mezőben adja meg a felhasználóhoz tartozó jelszót.
   7. A kapcsolat teszteléséhez kattintson a **Kapcsolat tesztelése** elemre.
   8. A társított szolgáltatás mentéséhez kattintson a **Mentés** gombra.

   ![Azure SQL MI Database társított szolgáltatás beállításai](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png)

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre, amelyek az adatforrást és az adat célját jelölik.

### <a name="create-a-dataset-to-represent-source-data"></a>Forrásadatokat képviselő adatkészlet létrehozása
Ebben a lépésben egy adatkészletet hoz létre, amely a forrásadatokat jelöli.

1. A fanézetben kattintson a **+ (plusz)** jelre, majd az **Adatkészlet** elemre.

   ![Új adatkészlet menü](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Válassza **Azure SQL Database felügyelt példány** lehetőséget, majd kattintson a **Folytatás** gombra.

   ![Forrásadatkészlet típusa – Azure SQL Database](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png)
   
3. A **készlet tulajdonságai** lapon állítsa be az adatkészlet nevét és a kapcsolatok adatait:
 
   1. Válassza a **AzureSqlMI1** elemet a **társított szolgáltatáshoz**.
   2. Válassza a **[dbo] lehetőséget. [** a **tábla neve** dbo_customers_CT].  Megjegyzés: Ez a tábla automatikusan jött létre, amikor a CDC engedélyezve lett az ügyfelek táblában. A módosított adatok soha nem jelennek meg közvetlenül ebből a táblából, hanem a [CDC funkcióival](/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql)kinyerve.

   ![Forráskapcsolat](./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Fogadóadattárba másolt adatokat képviselő adatkészlet létrehozása
Ebben a lépésben egy adatkészletet hoz létre, amely a forrásadattárból másolt adatokat jelöli. Az előfeltételek részeként létrehozta az Azure-Blob Storageban az adattó-tárolót. Ha még nem létezik, hozza létre a tárolót, vagy állítsa be egy meglévő tároló nevét. Ebben az oktatóanyagban a kimeneti fájl nevét dinamikusan generálja az indítás időpontjának használatával, amelyet később konfigurál a rendszer.

1. A fanézetben kattintson a **+ (plusz)** jelre, majd az **Adatkészlet** elemre.

   ![Új adatkészlet menü](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Folytatás** gombra.

   ![Fogadó adatkészlet típusa – Azure Blob Storage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png)
3. Válassza a **DelimitedText** lehetőséget, majd kattintson a **Folytatás** gombra.

   ![Fogadó adatkészlet formátuma – DelimitedText](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png)
4. A **készlet tulajdonságai** lapon állítsa be az adatkészlet nevét és a kapcsolatok adatait:

   1. A **Társított szolgáltatás** mezőben válassza az **AzureStorageLinkedService** értéket.
   2. Adja meg a **nyers** értéket a **filepath** **tároló** részénél.
   3. **Első sor engedélyezése fejlécként**
   4. Kattintson **az OK** gombra

   ![Fogadó adatkészlet – kapcsolat](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png)

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>Folyamat létrehozása a módosított adatmásoláshoz
Ebben a lépésben létrehoz egy folyamatot, amely először ellenőrzi a változási táblában található módosított rekordok számát egy **keresési tevékenység** használatával. Ha a feltétel típusú tevékenység ellenőrzi, hogy a módosult rekordok száma nagyobb-e nullánál, és **másolási tevékenységet** futtat a beszúrt/frissített/törölt adatoknak az Azure Blob Storageba való másolásához Azure SQL Database. Végül pedig egy kikapcsolt ablakos trigger van konfigurálva, és a kezdési és befejezési időpontokat a rendszer a kezdő és a záró ablak paraméterként adja át a tevékenységeknek. 

1. A Data Factory felhasználói felületen váltson a **Szerkesztés** lapra. Kattintson a **+ (plusz)** gombra a bal oldali ablaktáblán, majd kattintson a **folyamat** elemre.

    ![Új folyamat menü](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png)
2. Megjelenik egy új, a folyamat konfigurálására szolgáló lap. A folyamat fanézetben is megjelenik. A **Tulajdonságok** ablakban módosítsa a folyamat nevét a következőre: **IncrementalCopyPipeline**.

    ![Folyamat neve](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png)
3. A **Tevékenységek** eszközkészletben bontsa ki az **Általános** elemet, és húzza a **Keresés** tevékenységet a folyamat tervezőfelületére. Állítsa a tevékenység nevét **GetChangeCount** értékre. Ez a tevékenység beolvassa a rekordok számát a módosítási táblában egy adott időszakra vonatkozóan.

    ![Keresési tevékenység – név](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png)
4. Váltson a **beállításokra** a **Tulajdonságok** ablakban:
   1. Adja meg a **forrás adatkészlet** mezőhöz tartozó SQL mi adatkészlet nevét.
   2. Válassza ki a lekérdezési lehetőséget, és írja be a következőt a lekérdezés mezőbe:
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```
   3. **Csak az első sor** engedélyezése

    ![Keresési tevékenység – beállítások](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png)
5. Az **adatelőnézet** gombra kattintva ellenőrizheti, hogy a keresési tevékenység érvényes kimenetet kapott-e

    ![Keresési tevékenység – előzetes verzió](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png)
6. Bontsa ki az **iteráció &** a feltételes **műveletek** elemet a tevékenységek eszközkészletben, és húzza az **IF feltétel** tevékenységet a folyamat-tervező felületére. Állítsa a tevékenység nevét **HasChangedRows** értékre. 

    ![Ha feltétel – tevékenység neve](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png)
7. Váltson a **tevékenységekre** a **Tulajdonságok** ablakban:

   1. Adja meg a következő **kifejezést**
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. A True feltétel szerkesztéséhez kattintson a ceruza ikonra.

   ![Ha feltétel tevékenység – beállítások](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png)

   3. Bontsa ki az **általános** elemet a **tevékenységek** eszközkészletben, és húzza a **várakozási** tevékenységet a folyamat tervező felületére. Ez egy ideiglenes tevékenység az IF feltétel hibakereséséhez, és az oktatóanyag későbbi részében módosítva lesz. 

   ![Ha a feltétel igaz – várakozás](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png)

   4. A fő folyamathoz való visszatéréshez kattintson a IncrementalCopyPipeline navigációs elemre.

8. Futtassa a folyamatot **hibakeresési** módban a folyamat sikeres végrehajtásának ellenőrzéséhez. 

   ![Folyamat – hibakeresés](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png)
9. Ezután térjen vissza az igaz feltétel lépésre, és törölje a **várakozási** tevékenységet. A **tevékenységek** eszközkészletben bontsa ki az **Áthelyezés & átalakítás** elemet, majd húzza a **másolási** tevékenységet a folyamat tervező felületére. Állítsa a tevékenység nevét a következőre: **IncrementalCopyActivity**. 

   ![Másolási tevékenység – név](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png)
10. Váltson a **Forrás** lapra a **tulajdonságok** ablakában, és hajtsa végre a következő lépéseket:

   1. Adja meg a **forrás adatkészlet** mezőhöz tartozó SQL mi adatkészlet nevét. 
   2. A **Lekérdezés használata** elemnél válassza a **Lekérdezés** lehetőséget.
   3. Adja meg a következőt a **lekérdezéshez**.

      ```sql
      DECLARE @from_lsn binary(10), @to_lsn binary(10); 
      SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
      SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
      SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
      ```

   ![Másolási tevékenység – forrás beállításai](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png)

11. Az Előnézet elemre kattintva ellenőrizheti, hogy a lekérdezés helyesen adja-e vissza a módosított sorokat.

    ![A képernyőképen a lekérdezés ellenőrzésének ellenőrzése látható.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png)
12. Váltson a fogadó **lapra,** és válassza ki az Azure Storage-adatkészletet a fogadó **adatkészlet** mezőben.

    ![Képernyőfelvétel: a fogadó lap.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png)
13. Kattintson a vissza a fő folyamat vászonra lehetőségre, és kapcsolja össze a **keresési** tevékenységet az **IF Condition** tevékenységgel eggyel. Húzza a **keresési** tevékenységhez csatolt **zöld** gombot az **IF Condition** tevékenységhez.

    ![A keresési és másolási tevékenységek összekapcsolása](./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png)
14. Kattintson az **Érvényesítés** elemre az eszköztáron. Ellenőrizze, hogy nincs-e érvényesítési hiba. A **>>** gombra kattintva zárja be a **folyamatérvényesítési jelentés** ablakát.

    ![Érvényesítés gomb](./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png)
15. A folyamat teszteléséhez kattintson a hibakeresés elemre, és ellenőrizze, hogy a rendszer létrehozza-e a fájlt a tárolási helyen.

    ![Növekményes folyamat hibakeresése – 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png)
16. Az entitásokat (társított szolgáltatásokat, adatkészleteket és folyamatokat) az **összes közzététele** gombra kattintva teheti közzé a Data Factory szolgáltatásban. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet.

    ![Közzététel gomb](./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png)    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>A bukdácsoló ablak trigger és a CDC ablak paramétereinek konfigurálása 
Ebben a lépésben egy kieséses ablakos triggert hoz létre, amely rendszeres időközönként futtatja a feladatot. A WindowStart és a WindowEnd rendszerváltozóit fogja használni a bukdácsoló ablakos triggerhez, és paraméterként továbbítja őket a folyamathoz a CDC-lekérdezésben való használathoz.

1. Navigáljon a **IncrementalCopyPipeline** folyamat **Parameters (paraméterek** ) lapjára, és használja a folyamathoz a **+ New (+ új** ) gombot, és adja hozzá a két paramétert (**triggerStartTime** és **triggerEndTime**). Hibakeresési célokra adja hozzá az alapértelmezett értékeket az **éééé-hh-nn HH24: mi: SS. fff** formátumban, de győződjön meg arról, hogy a TRIGGERSTARTTIME a CDC előtt nem engedélyezett a táblán, ellenkező esetben hibaüzenetet fog eredményezni.

    ![Aktiválás most menü](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png)
2. Kattintson a **keresési** tevékenység beállítások fülére, és konfigurálja a lekérdezést a kezdő és a záró paraméterek használatára. Másolja a következőt a lekérdezésbe:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. Navigáljon a **másolási** tevékenységhez az **IF Condition (IF** ) tevékenység igaz esetére, és kattintson a **forrás** fülre. Másolja a következőt a lekérdezésbe:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. Kattintson a **másolási** tevékenység **mosogató** fülére, majd kattintson a **Megnyitás** gombra az adatkészlet tulajdonságainak szerkesztéséhez. Kattintson a **Parameters (paraméterek** ) fülre, és adjon hozzá egy új, **triggerStart** nevű paramétert.    

    ![Képernyőfelvétel: új paraméter hozzáadása a parameters (paraméterek) laphoz.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png)
5. Ezután konfigurálja az adatkészlet tulajdonságait úgy, hogy egy **ügyfél/növekményes** alkönyvtárban lévő adatokat dátum-alapú partíciókkal tárolja.
   1. Kattintson a **kapcsolatok** lapfülre az adatkészlet tulajdonságainál, és adja hozzá a dinamikus tartalmat a **címtárhoz** és a **fájlokhoz** . 
   2. Adja meg a következő kifejezést a **címtár** szakaszban a szövegmező alatt található dinamikus tartalom hivatkozásra kattintva:
    
    ```sql
    @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
    ```
   3. Adja meg a következő kifejezést a **fájl** szakaszban. Ekkor a rendszer az indítási dátum és idő alapján hozza létre a fájlneveket, a CSV-bővítménnyel ellátott utótaggal:
    
    ```sql
    @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
    ```
    ![Fogadó adatkészlet konfigurációja – 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png)

   4. A **IncrementalCopyPipeline** lapra kattintva térjen vissza a **másolási** tevékenység fogadói **beállításaihoz** . 
   5. Bontsa ki az adatkészlet tulajdonságait, és adja meg a dinamikus tartalmat a triggerStart paraméter értékeként a következő kifejezéssel:
     ```sql
     @pipeline().parameters.triggerStartTime
     ```
    ![Fogadó adatkészlet konfigurációja – 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png)

6. A folyamat teszteléséhez kattintson a hibakeresés elemre, és győződjön meg arról, hogy a mappa szerkezete és a kimeneti fájl a várt módon jön létre. Töltse le és nyissa meg a fájlt a tartalom ellenőrzéséhez. 

    ![Növekményes másolás hibakeresése – 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png)
7. Ellenőrizze, hogy a paraméterek bekerülnek-e a lekérdezésbe a folyamat futtatásának bemeneti paramétereinek áttekintésével.

    ![Növekményes másolás hibakeresése – 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png)
8. Az entitásokat (társított szolgáltatásokat, adatkészleteket és folyamatokat) az **összes közzététele** gombra kattintva teheti közzé a Data Factory szolgáltatásban. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet.
9. Végezetül állítson be egy késleltetésű ablakos triggert a folyamat rendszeres időközönkénti futtatásához és a kezdő és a befejező időpont paramétereinek megadásához. 
   1. Kattintson az **trigger hozzáadása** gombra, és válassza az **új/szerkesztés** lehetőséget.

   ![Új trigger hozzáadása](./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png)

   2. Adja meg az trigger nevét, és adjon meg egy kezdési időpontot, amely megegyezik a fenti hibakeresési időszak befejezési időpontjával.

   ![Ablak kiesése trigger](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png)

   3. A következő képernyőn a kezdő és a záró paraméterekhez a következő értékeket kell megadnia.
    ```sql
    @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
    @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
    ```

   ![Kiesési ablak triggere – 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png)

> [!NOTE]
> Megjegyzés: az trigger csak akkor fut le, ha közzé lett téve. Emellett a kiesési időszak várható viselkedése az, hogy a kezdési dátumtól kezdve minden korábbi intervallumot a mai napig futtasson. Az ablakos eseményindítókkal kapcsolatos további információkért tekintse meg [a következőt](./how-to-create-tumbling-window-trigger.md):. 
  
10. A **SQL Server Management Studio** használatával további módosításokat hajthat végre az ügyfél táblában a következő SQL futtatásával:
    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. Kattintson az **összes közzététele** gombra. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet.  
12. Néhány perc elteltével a folyamat elindul, és egy új fájl betöltődik az Azure Storage-ba


### <a name="monitor-the-incremental-copy-pipeline"></a>A növekményes másolási folyamat figyelése
1. Kattintson a bal oldali **Monitorozás** lapra. Ekkor a folyamat futtatása és állapota megjelenik a listában. A lista frissítéséhez kattintson a **Refresh** (Frissítés) elemre. Az újrafuttatási művelet és a felhasználás jelentés eléréséhez vigye az egérmutatót a folyamat neve közelében.

    ![Folyamatfuttatások](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png)
2. A folyamat futtatásához társított tevékenység-futtatások megtekintéséhez kattintson a folyamat nevére. Ha a rendszer módosult adatokat észlelt, akkor három tevékenység lesz, beleértve a másolási tevékenységet, ellenkező esetben csak két bejegyzés lesz a listában. Ha vissza szeretne váltani a folyamat-futtatások nézetre, kattintson a felül található **minden folyamat** hivatkozásra.

    ![Tevékenységfuttatások](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png)


### <a name="review-the-results"></a>Az eredmények áttekintése
A második fájl az `raw` nevű tároló `customers/incremental/YYYY/MM/DD` mappájában található.

![Kimeneti fájl növekményes másolásból](media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png)
 

## <a name="next-steps"></a>Következő lépések
Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan másolhat új és módosított fájlokat csak a LastModifiedDate alapján:

> [!div class="nextstepaction"]
>[Új fájlok másolása a lastmodifieddate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
