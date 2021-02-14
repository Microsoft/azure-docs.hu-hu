---
title: Nagy mennyiségű Adatmásolás a Azure Portal használatával
description: A Azure Data Factory és a másolási tevékenységgel az adatok tömegesen másolhatók a forrás adattárolóból a cél adattárakba.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/29/2021
ms.openlocfilehash: 4807d1d5e97f7f95521df054d1a71f8925ac4ebb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367605"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory-in-the-azure-portal"></a>Több táblázat másolása ömlesztve Azure Data Factory használatával a Azure Portal

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez az oktatóanyag azt mutatja be, hogyan **másolhat több táblázatot Azure SQL Databaseról az Azure szinapszis Analytics szolgáltatásba**. A minta egyéb másolási forgatókönyvek esetén is alkalmazható. Ha például SQL Server/Oracle-ből másol táblákat a Azure SQL Database/Azure szinapszis Analytics/Azure Blobba, a különböző elérési utakat a Blobból a Azure SQL Database táblákba másolja.

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory szolgáltatással: [Bevezetés az Azure Data Factory használatába](introduction.md).

Az oktatóanyag a következő főbb lépésekből áll:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre Azure SQL Database, az Azure szinapszis Analytics és az Azure Storage társított szolgáltatásait.
> * Hozzon létre Azure SQL Database és az Azure szinapszis Analytics-adatkészleteket.
> * Hozzon létre egy folyamatot, amellyel megkeresheti a másolandó táblákat és egy másik folyamatot a tényleges másolási művelet végrehajtásához. 
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Ez az oktatóanyag az Azure Portalt használja. Az adat-előállítók egyéb eszközökkel/SDK-kkal való létrehozásával kapcsolatban lásd [rövid útmutatóinkat](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Teljes körű munkafolyamat
Ebben az esetben az Azure szinapszis Analyticsbe másolni kívánt Azure SQL Databaseban számos táblázat található. A következők a folyamatokban végbemenő munkafolyamat lépései logikai sorrendben:

![Munkafolyamat](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* Az első folyamat megkeresi azoknak a tábláknak a listáját, amelyeket át kell másolni a fogadó adattárakba.  Másik megoldásként fenntarthat egy metaadattáblát, amely felsorolja az összes, a fogadó adattárba másolandó táblát. A folyamat ezután elindít egy másik folyamatot, amely végigiterál az adatbázis összes tábláján, és elvégzi az adatmásolási műveletet.
* A második folyamat hajtja végre a tényleges másolást. A táblák listáját használja paraméterként. A listában szereplő minden táblázat esetében másolja az adott táblázatot Azure SQL Database az Azure szinapszis Analytics megfelelő táblájába a [szakaszos másolással a blob Storage-on keresztül,](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics) a legjobb teljesítmény érdekében. Ebben a példában az első folyamat a táblák listáját adja át a paraméter értékeként. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek
* **Azure Storage-fiók**. Az Azure Storage-fiók a tömeges másolási műveletben átmeneti blobtárolóként működik. 
* **Azure SQL Database**. Ez az adatbázis tartalmazza a forrásadatokat. Hozzon létre egy adatbázist SQL Database az Adventure Works LT mintaadatok alapján, és [hozzon létre egy adatbázist a Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) cikkben. Ez az oktatóanyag a mintaadatbázis összes tábláját egy Azure szinapszis Analyticsre másolja.
* Az **Azure szinapszis Analytics**. Ez az adattárház tárolja az SQL Database-ből átmásolt adatokat. Ha nem rendelkezik Azure szinapszis Analytics-munkaterülettel, tekintse meg az első [lépések az Azure szinapszis Analytics](..\synapse-analytics\get-started.md) használatával című cikket a létrehozás lépéseihez.

## <a name="azure-services-to-access-sql-server"></a>Az SQL Server elérésének engedélyezése az Azure-szolgáltatások számára

A SQL Database és az Azure szinapszis Analytics esetében is lehetővé teszi az Azure-szolgáltatások számára az SQL Server elérését. Győződjön **meg** arról, hogy az **Azure-szolgáltatások és-erőforrások elérésének engedélyezése ehhez a kiszolgálóhoz** beállítás be van kapcsolva a kiszolgálón. Ez a beállítás lehetővé teszi, hogy az Data Factory szolgáltatás beolvassa az adatait a Azure SQL Databaseból, és adatokkal írja az Azure szinapszis Analyticsbe. 

A beállítás ellenőrzéséhez és bekapcsolásához nyissa meg a kiszolgálót > biztonsági > tűzfalak és virtuális hálózatok > állítsa be az **Azure-szolgáltatások és-erőforrások engedélyezése a kiszolgálóhoz való hozzáféréshez** **beállítást.**

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. Nyissa meg az [Azure Portal](https://portal.azure.com). 
1. A Azure Portal menü bal oldalán válassza az **erőforrás létrehozása**  >  **integráció**  >  **Data Factory** lehetőséget. 

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Az **új adatgyár** lapon adja meg a **ADFTutorialBulkCopyDF** **nevet**. 
 
   Az Azure-beli adatgyár nevének **globálisan egyedinek** kell lennie. Ha a névmezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialBulkCopyDF). A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
    ```text
    Data factory name "ADFTutorialBulkCopyDF" is not available
    ```
1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
1. Az **erőforráscsoport** esetében hajtsa végre az alábbi lépések egyikét:
     
   - Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából. 
   - Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét.   
         
     Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
1. A **Verzió** résznél válassza a **V2** értéket.
1. Válassza ki a Data Factory **helyét**. Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
1. Kattintson a **Létrehozás** lehetőségre.
1. A létrehozás befejezése után válassza az **erőforrás keresése** lehetőséget, és lépjen a **Data Factory** lapra. 
   
1. A Data Factory felhasználóifelület-alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.


## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre az adattárak és a számítási erőforrások adat-előállítóval történő társításához. A társított szolgáltatás rendelkezik azon kapcsolatadatokkal, amelyeket a Data Factory szolgáltatás futtatáskor az adattárhoz való kapcsolódáshoz használ. 

Ebben az oktatóanyagban összekapcsolja az Azure SQL Database, az Azure szinapszis Analytics és az Azure Blob Storage adattárakat a saját adataival. Az Azure SQL Database a forrásadattár. Az Azure szinapszis Analytics a fogadó/rendeltetési adattár. Az Azure Blob Storage az adatok előkészítését, mielőtt az adatok betöltődik az Azure szinapszis Analyticsbe a Base használatával. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>A forrás Azure SQL Database-beli társított szolgáltatás létrehozása
Ebben a lépésben létrehoz egy társított szolgáltatást, amely összekapcsolja az adatbázist Azure SQL Databaseban az adatgyárban. 

1. Nyissa meg a [kezelés fület](./author-management-hub.md) a bal oldali ablaktáblán.

1. A társított szolgáltatások lapon válassza az **+ új** lehetőséget egy új társított szolgáltatás létrehozásához.

   ![Új társított szolgáltatás](./media/doc-common-process/new-linked-service.png)
1. Az **Új társított szolgáltatás** ablakban válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Folytatás** elemre. 
1. Az **új társított szolgáltatás (Azure SQL Database)** ablakban végezze el a következő lépéseket: 

    a. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet.

    b. **Kiszolgáló kiválasztása** a kiszolgálónévhez
    
    c. Válassza ki az adatbázis **nevét**. 
    
    d. Adja meg **a felhasználó nevét** az adatbázishoz való kapcsolódáshoz. 
    
    e. Adja meg a felhasználóhoz tartozó **jelszót**. 

    f. Ha tesztelni szeretné az adatbázishoz való kapcsolódást a megadott információval, kattintson a **Kapcsolódás tesztelése** gombra.
  
    : A társított szolgáltatás mentéséhez kattintson a **Létrehozás** gombra.


### <a name="create-the-sink-azure-synapse-analytics-linked-service"></a>A fogadó Azure szinapszis Analytics társított szolgáltatás létrehozása

1. A **Kapcsolatok** lapon kattintson ismét az **+ Új** elemre az eszköztáron. 
1. Az **új társított szolgáltatás** ablakban válassza az **Azure szinapszis Analytics** elemet, majd kattintson a **Folytatás** gombra. 
1. Az **új társított szolgáltatás (Azure szinapszis Analytics)** ablakban végezze el a következő lépéseket: 
   
    a. A **Név** mezőbe írja be az **AzureSqlDWLinkedService** nevet.
     
    b. **Kiszolgáló kiválasztása** a kiszolgálónévhez
     
    c. Válassza ki az adatbázis **nevét**. 
     
    d. Adja meg a **felhasználónevet** az adatbázishoz való kapcsolódáshoz. 
     
    e. Adja meg a felhasználó **jelszavát** . 
     
    f. Ha tesztelni szeretné az adatbázishoz való kapcsolódást a megadott információval, kattintson a **Kapcsolódás tesztelése** gombra.
     
    : Kattintson a **Létrehozás** lehetőségre.

### <a name="create-the-staging-azure-storage-linked-service"></a>Az átmeneti Azure Storage-beli társított szolgáltatás létrehozása
Ebben az oktatóanyagban a jobb másolási teljesítmény érdekében az Azure Blob Storage-ot átmeneti területként használja a PolyBase engedélyezéséhez.

1. A **Kapcsolatok** lapon kattintson ismét az **+ Új** elemre az eszköztáron. 
1. A **New Linked Service** (Új társított szolgáltatás) ablakban válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Continue** (Folytatás) elemre. 
1. Az **új társított szolgáltatás (Azure Blob Storage)** ablakban végezze el a következő lépéseket: 

    a. A **Name** (Név) mezőbe írja az **AzureStorageLinkedService** nevet.                                                 
    b. A **Storage-fiók neve** elemnél válassza ki saját **Azure Storage-fiókját**.
    
    c. Kattintson a **Létrehozás** lehetőségre.

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben az oktatóanyagban létrehozza a forrás- és fogadó-adatkészletet, amelyek meghatározzák az adatok tárolásának helyét. 

Az **AzureSqlDatabaseDataset** bemeneti adatkészlet az **AzureSqlDatabaseLinkedService** szolgáltatásra hivatkozik. A társított szolgáltatás megadja a kapcsolati sztringet az adatbázishoz való csatlakozáshoz. Az adatkészlet megadja a forrásadatokat tartalmazó adatbázis és tábla nevét. 

Az **AzureSqlDWDataset** kimeneti adatkészlet az **AzureSqlDWLinkedService** szolgáltatásra hivatkozik. A társított szolgáltatás megadja a kapcsolati karakterláncot az Azure szinapszis Analyticshez való kapcsolódáshoz. Az adatkészlet meghatározza az adatbázist és táblát, ahova a rendszer az adatokat másolja. 

Ebben az oktatóanyagban a forrás és cél SQL-táblái nincsenek fixen rögzítve az adatkészlet-definíciókban. Ehelyett a ForEach tevékenység továbbítja futtatáskor a tábla nevét a másolási tevékenységnek. 

### <a name="create-a-dataset-for-source-sql-database"></a>Adatkészlet létrehozása a forrás SQL-adatbázishoz

1. Válassza a **Szerző** fület a bal oldali ablaktáblán.

1. Válassza ki a **+** (plusz) elemet a bal oldali ablaktáblán, majd válassza az **adatkészlet** lehetőséget. 

    ![Új adatkészlet menü](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. Az **új adatkészlet** ablakban válassza a **Azure SQL Database** lehetőséget, majd kattintson a **Folytatás** gombra. 
    
1. A **készlet tulajdonságai** ablakban, a név mezőben adja meg a **AzureSqlDatabaseDataset** **nevet**. A **társított szolgáltatás** területen válassza a **AzureSqlDatabaseLinkedService** lehetőséget. Ezután kattintson az **OK** gombra.

1. Váltson a **kapcsolatok** lapra, és válassza **a tábla bármelyik tábláját.** Ez a tábla egy helyőrző tábla. Egy folyamat létrehozásakor meghatározhat egy lekérdezést a forrásadatkészlethez. A lekérdezés az adatok adatbázisból való kinyerésére szolgál. Azt is megteheti, hogy a **Szerkesztés** jelölőnégyzetre kattint, majd a **dbo. dummyName** nevet adja meg a táblázat neveként. 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics"></a>Adatkészlet létrehozása a fogadó Azure szinapszis Analyticshez 

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd kattintson az **Adatkészlet** elemre. 
1. Az **új adatkészlet** ablakban válassza az **Azure szinapszis Analytics** elemet, majd kattintson a **Folytatás** gombra.
1. A **készlet tulajdonságai** ablakban, a név mezőben adja meg a **AzureSqlDWDataset** **nevet**. A **társított szolgáltatás** területen válassza a **AzureSqlDWLinkedService** lehetőséget. Ezután kattintson az **OK** gombra.
1. Váltson a **Paraméterek** lapra, és kattintson az **+ Új** elemre, majd adja meg a **DWTableName** értéket a paraméter neveként. Kattintson ismét az **+ új** elemre, majd írja be a **DWSchema** nevet a paraméter neveként. Ha ezt a nevet másolja/illeszti be az oldalról, ügyeljen arra, hogy a *DWTableName* és a *DWSchema* végén ne legyen **szóköz karakter** . 
1. Lépjen a **Kapcsolat** lapra. 

    1. A **tábla** mezőben keresse meg a **Szerkesztés** lehetőséget. Válassza ki az első beviteli mezőt, majd kattintson a **dinamikus tartalom hozzáadása** hivatkozásra. A **dinamikus tartalom hozzáadása** lapon kattintson a **DWSchema** elemre a **Paraméterek** területen, amely automatikusan kitölti a felső kifejezés szövegmezőjét `@dataset().DWSchema` , majd kattintson a **Befejezés** gombra.  
    
        ![Adatkészlet-kapcsolatok táblanév](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    1. Válassza ki a második beviteli mezőt, majd kattintson a **dinamikus tartalom hozzáadása** hivatkozásra. A **dinamikus tartalom hozzáadása** lapon kattintson a **DWTAbleName** elemre a **Paraméterek** területen, amely automatikusan kitölti a felső kifejezés szövegmezőjét `@dataset().DWTableName` , majd kattintson a **Befejezés** gombra. 
    
    1. Az adatkészlet **Táblanév** tulajdonsága a **DWSchema** és a **DWTableName** paraméterek argumentumként átadott értékekre van beállítva. A ForEach tevékenység végighalad egy táblalistán, és egyesével továbbítja azokat a másolási tevékenységnek. 
    

## <a name="create-pipelines"></a>Folyamatok létrehozása
Ebben az oktatóanyagban a következő két folyamatot hozza létre: **IterateAndCopySQLTables** és **GetTableListAndTriggerCopyData**. 

A **GetTableListAndTriggerCopyData** folyamat két műveletet hajt végre:

* Megkeresi az Azure SQL Database rendszertábláját, és lekéri a másolni kívánt táblák listáját.
* Elindítja a folyamat **IterateAndCopySQLTables** , hogy elvégezze a tényleges Adatmásolást.

A  **IterateAndCopySQLTables** folyamat a táblák listáját veszi fel paraméterként. A lista minden táblázatában az adatok átmásolása az Azure SQL Database lévő táblából az Azure szinapszis Analytics szakaszos másolás és a kiinduló használatával.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Az IterateAndCopySQLTables folyamat létrehozása

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd kattintson a **Folyamat** elemre.

    ![Új folyamat menü](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
 
1. A **Tulajdonságok** terület általános paneljén adja meg  a IterateAndCopySQLTables **nevet**. Ezután csukja össze a panelt a jobb felső sarokban található tulajdonságok ikonra kattintva.

1. Váltson a **Paraméterek** lapra, és hajtsa végre a következőket: 

    a. Kattintson az **+ Új** elemre. 
    
    b. Adja meg a **tableList** a paraméter **neveként**.
    
    c. A **Típus** részben válassza a **Tömb** lehetőséget.

1. A **Tevékenységek** eszközkészletben bontsa ki az **Ismétlés és feltételek** elemet, és húzza a **ForEach** tevékenységet a folyamat tervezőfelületére. A **Tevékenységek** eszközkészletben kereshet is az egyes tevékenységek között. 

    a. Az alul található **Általános** lapon a **Név** mezőbe írja be az **IterateSQLTables** nevet. 

    b. Váltson a **Beállítások** lapra, kattintson az **elemek** beviteli mezőjére, majd kattintson a **dinamikus tartalom hozzáadása** hivatkozásra. 

    c. A **dinamikus tartalom hozzáadása** lapon bontsa ki a **rendszerváltozók** és **függvények** szakaszokat, kattintson a **tableList** elemre a **Parameters (paraméterek**) területen, amely automatikusan feltölti a felső kifejezés szövegmezőjét `@pipeline().parameter.tableList` . Ezután kattintson a **Befejezés** gombra. 

    ![Foreach paramétereinek szerkesztője](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Váltson a **tevékenységek** lapra, kattintson a **ceruza ikonra** egy alárendelt tevékenység a **foreach** tevékenységhez való hozzáadásához.
    
    ![Foreach Activity-szerkesztő](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. A **tevékenységek** eszközkészletben bontsa ki az **Áthelyezés & az átvitel** elemet, majd húzza az **adatok másolása** tevékenységet a folyamat tervező felületére. Figyelje meg felül a navigációs menüt. A **IterateAndCopySQLTable** a folyamat neve, a **IterateSQLTables** pedig a foreach-tevékenység neve. A tervező a tevékenység hatókörében van. Ha vissza szeretne váltani a ForEach-szerkesztőben lévő folyamat-szerkesztőre, kattintson a navigációs menüben található hivatkozásra. 

    ![Másolás a ForEach tevékenységbe](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Váltson a **Forrás** lapra, és végezze el az alábbi lépéseket:

    1. **Forrásadatkészletnek** válassza a következőt: **AzureSqlDatabaseDataset**. 
    1. A lekérdezéshez válassza a **lekérdezés** lehetőséget. 
    1. Kattintson a **Lekérdezés** beviteli mezőre, válassza alul a **Dinamikus tartalom hozzáadása** lehetőséget, adja meg a következő kifejezést a **Lekérdezés** értékeként, majd kattintson a **Befejezés** gombra.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

1. Váltson a **Fogadó** lapra, és végezze el az alábbi lépéseket: 

    1. **Fogadó-adatkészletnek** válassza a következőt: **AzureSqlDWDataset**.
    1. Kattintson a beviteli mezőre a DWTableName paraméter ÉRTÉKEként – > válassza a **dinamikus tartalom hozzáadása** lehetőséget, írja be a `@item().TABLE_NAME` kifejezést parancsfájlként, > válassza a **Befejezés** gombot.
    1. Kattintson a beviteli mezőre a DWSchema paraméter ÉRTÉKEként – > válassza a **dinamikus tartalom hozzáadása** lehetőséget, írja be a `@item().TABLE_SCHEMA` kifejezést parancsfájlként, > válassza a **Befejezés** gombot.
    1. A másolási módszer beállításnál válassza a **alapszintű** lehetőséget. 
    1. Törölje az **alapértelmezett típus használata** beállítást. 
    1. A Table (tábla) beállításnál az alapértelmezett beállítás a "None". Ha nem rendelkezik előre létrehozott táblákkal a fogadó Azure szinapszis-elemzésben, engedélyezze a **tábla automatikus létrehozása** lehetőséget, a másolási tevékenység automatikusan létrehozza a táblákat a forrásadatok alapján. További részletekért tekintse meg a fogadó [tábla automatikus létrehozása](copy-activity-overview.md#auto-create-sink-tables)című témakört. 
    1. Kattintson a **Másolás előtti szkript** beviteli mezőre, válassza alul a **Dinamikus tartalom hozzáadása** lehetőséget, adja meg szkriptként a következő kifejezést, majd kattintson a **Befejezés** gombra. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Fogadóbeállítások másolása](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)

1. Váltson a **Beállítások** lapra, és végezze el az alábbi lépéseket: 

    1. Jelölje be az **előkészítés engedélyezése** jelölőnégyzetet.
    1. A **Tárfiók társított szolgáltatásánál** válassza az **AzureStorageLinkedService** elemet.

1. A folyamat beállításainak érvényesítéséhez kattintson az **Érvényesítés** gombra a felső folyamateszköztáron. Ellenőrizze, hogy nincs-e érvényesítési hiba. A folyamat- **ellenőrzési jelentés** bezárásához kattintson a dupla szögű zárójelekre **>>** .

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>A GetTableListAndTriggerCopyData folyamat létrehozása

Ez a folyamat két műveletet végez:

* Megkeresi az Azure SQL Database rendszertábláját, és lekéri a másolni kívánt táblák listáját.
* Elindítja az „IterateAndCopySQLTables” folyamatot, amely elvégezi a tényleges adatmásolást.

A folyamat létrehozásának lépései a következők:

1. Kattintson a bal oldali ablaktáblán a **+ (plusz)** jelre, majd kattintson a **Folyamat** elemre.
1. A **Tulajdonságok** területen található általános panelen módosítsa a folyamat nevét a **GetTableListAndTriggerCopyData** értékre. 

1. A **tevékenységek** eszközkészletben bontsa ki az **általános** elemet, majd húzza a **keresési** tevékenységet a folyamat-tervező felületre, és hajtsa végre a következő lépéseket:

    1. A **Név** mezőbe írja be a **LookupTableList** nevet. 
    1. Adja meg **az adatbázisból a tábla listájának lekérése** **leírást**.

1. Váltson a **Beállítások** lapra, és végezze el az alábbi lépéseket:

    1. **Forrásadatkészletnek** válassza a következőt: **AzureSqlDatabaseDataset**. 
    1. Válassza a **lekérdezés** lehetőséget a **lekérdezés használatához**. 
    1. A **Lekérdezés** elemhez adja meg az alábbi SQL-lekérdezést.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Törölje a **Csak az első sor** mező jelölőnégyzetének jelölését.

        ![Keresési tevékenység – beállítások lap](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Húzza a **folyamat végrehajtása** tevékenységet a tevékenységek eszközkészletből a folyamat tervezői felületére, és állítsa a nevet **TriggerCopy** értékre.

1. Ha a **keresési** tevékenységet a **folyamat végrehajtása** tevékenységhez szeretné **csatlakoztatni** , húzza a keresési tevékenységhez csatolt **zöld mezőt** a folyamat végrehajtása tevékenység bal oldalán.

    ![A keresési és a Folyamat végrehajtása tevékenység csatlakoztatása](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Váltson a **Beállítások** lapra a **folyamat végrehajtása** tevékenységnél, és hajtsa végre a következő lépéseket: 

    1. A **Meghívott folyamat** elemnél válassza az **IterateAndCopySQLTables** lehetőséget. 
    1. Törölje a jelet a **várakozás befejezéséhez** jelölőnégyzetből.
    1. A **Parameters (paraméterek** ) szakaszban kattintson a beviteli mezőre a VALUE-> alatt, válassza a **dinamikus tartalom hozzáadása** a következőhöz – > adja meg a `@activity('LookupTableList').output.value` tábla neve értéket – > válassza a **Befejezés** lehetőséget. A keresési tevékenység eredményeinek listáját a második folyamat bemenetének állítja be. Az eredménylista tartalmazza azokat a táblákat, amelyeknek az adatait a célhelyre kell másolni. 

        ![Folyamat végrehajtása tevékenység – beállítások lap](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. A folyamat érvényesítéséhez kattintson az **Érvényesítés** elemre az eszköztáron. Ellenőrizze, hogy nincs-e érvényesítési hiba. A **Folyamatérvényesítési jelentés** bezárásához kattintson a **>>** jelre.

1. Ha az entitásokat (adatkészleteket, folyamatokat stb.) közzé szeretné tenni a Data Factory szolgáltatásban, kattintson az **összes közzététele** elemre az ablak tetején. Várja meg, amíg befejeződik a közzétételi folyamat. 

## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása

1. Nyissa meg a folyamat **GetTableListAndTriggerCopyData**, kattintson a felső folyamat eszköztárán található **trigger hozzáadása** elemre, majd az **aktiválás most** elemre. 

1. Erősítse meg a futtatást a **folyamat futtatása** lapon, majd válassza a **Befejezés** gombot.

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Váltson a **figyelés** lapra. Kattintson a **frissítés** gombra, amíg meg nem jelenik a Futtatás a megoldásban lévő folyamatokhoz. Folytassa mindaddig a lista frissítését, amíg meg nem jelenik a **Sikeres** állapot. 

1. A **GetTableListAndTriggerCopyData** folyamathoz társított tevékenység-futtatások megtekintéséhez kattintson a folyamat neve hivatkozásra. Ehhez a folyamatfuttatáshoz két tevékenységfuttatást kell látnia. 
    ![Folyamat futtatásának figyelése](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. A **keresési** tevékenység kimenetének megtekintéséhez kattintson a tevékenység **neve** oszlopban a tevékenység melletti **kimenet** hivatkozásra. A **Kimenet** ablakot teljes méretre állíthatja és visszaállíthatja. Áttekintés után kattintson az **X** elemre a **Kimenet** ablak bezárásához.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Ha vissza szeretne váltani a **folyamat-futtatások** nézetre, kattintson a navigációs menü tetején található **minden folyamat futtatása** hivatkozásra. Kattintson a **IterateAndCopySQLTables** hivatkozás (a **folyamat neve** oszlop alatt) elemre a folyamat tevékenység-futtatásának megtekintéséhez. Figyelje meg, hogy a **keresési** tevékenység kimenetében minden táblához egy **másolási** tevékenység fut. 

1. Győződjön meg róla, hogy az adatgyűjtés az oktatóanyagban használt Azure szinapszis-elemzésre lett másolva. 

## <a name="next-steps"></a>Következő lépések
Az oktatóanyagban az alábbi lépéseket hajtotta végre: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre Azure SQL Database, az Azure szinapszis Analytics és az Azure Storage társított szolgáltatásait.
> * Hozzon létre Azure SQL Database és az Azure szinapszis Analytics-adatkészleteket.
> * Egy folyamat létrehozása a másolni kívánt táblák megkeresésére, és egy másik folyamat létrehozása a tényleges másolási művelet elvégzésére 
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Folytassa a következő oktatóanyaggal, amelyben az adatok egy forrásból egy célhelyre történő fokozatos másolását ismerheti meg:
> [!div class="nextstepaction"]
>[Adatok fokozatos másolása](tutorial-incremental-copy-portal.md)
