---
title: Az Adatmásolás növekményes másolása a Change Tracking használatával a PowerShell használatával
description: Ebben az oktatóanyagban egy Azure Data Factory folyamatot hoz létre, amely a különbözeti adatok növekményes másolását végzi egy SQL Server-adatbázis több táblájából Azure SQL Databasere.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019, devx-track-azurepowershell
ms.date: 02/18/2021
ms.openlocfilehash: a31f8ce227175e65f7119c25dcc575dc6fafdcd4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727736"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information-using-powershell"></a>Adatok növekményes betöltése a Azure SQL Databaseból az Azure-ba Blob Storage a Change Tracking Information használatával a PowerShell használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ebben az oktatóanyagban létrehoz egy Azure-beli adat-előállítót egy olyan folyamattal, amely az Azure Blob Storage-ban Azure SQL Database található forrásadatbázis **változás-követési** információi alapján tölti be a különbözeti adatokat.  

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A forrásadattár előkészítése
> * Adat-előállító létrehozása
> * Társított szolgáltatások létrehozása.
> * Forrás, fogadó és változáskövetési adatkészletek létrehozása.
> * A teljes másolási folyamat létrehozása, futtatása és figyelése
> * A forrástáblában szereplő adatok hozzáadása vagy frissítése
> * A növekményes másolási folyamat létrehozása, futtatása és figyelése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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
    2. A változásadatok betöltéséhez egyesítse a **sys.change_tracking_tables** táblázat módosított sorainak (a két SYS_CHANGE_VERSION érték közötti) elsődleges kulcsait és a **forrástábla** adatait, majd helyezze át a módosított adatokat a célhelyre.
    3. A következő alkalommal frissítse a SYS_CHANGE_VERSION értékét a változásadatok betöltéséhez.

## <a name="high-level-solution"></a>Összefoglaló jellegű megoldás
Ebben az oktatóanyagban két folyamatot hoz létre, amelyek az alábbi két műveletet hajtják végre:  

1. **Kezdeti betöltés:** egy másolási tevékenységgel rendelkező folyamatot fog létrehozni, amely a forrásadattár (Azure SQL Database) összes adatát átmásolja a céladattárba (Azure Blob Storage).

    ![Összes adat betöltése](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-load-flow-diagram.png)
1.  **Növekményes betöltés:** a következő tevékenységeket tartalmazó folyamatot fog létrehozni, majd időszakosan futtatni.
    1. Hozzon létre **két keresési tevékenységet** a SYS_CHANGE_VERSION régi és új értékének lekéréséhez az Azure SQL Database-ből, majd adja azt át a másolási tevékenységnek.
    2. Hozzon létre **egy másolási tevékenységet** a két SYS_CHANGE_VERSION érték közötti beillesztett/frissített/törölt adatok az Azure SQL Database-ből az Azure Blob Storage-be való másolásához.
    3. Hozzon létre **egy tárolt eljárási tevékenységet** a SYS_CHANGE_VERSION értékének a következő folyamatfuttatáshoz való frissítéséhez.

    ![Növekményes betöltés folyamatábrája](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-load-flow-diagram.png)


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

* Azure PowerShell. Telepítse a legújabb Azure PowerShell modulokat a [Azure PowerShell telepítésére és konfigurálására](/powershell/azure/install-Az-ps)vonatkozó utasításokat követve.
* **Azure SQL Database**. Ezt az adatbázist használjuk **forrásadattárként**. Ha nem rendelkezik Azure SQL Database-adatbázissal, a létrehozásához szükséges lépésekért tekintse meg az [adatbázis létrehozása Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) cikkben.
* **Azure Storage-fiók**. A blobtárolót használjuk majd **fogadóadattárként**. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../storage/common/storage-account-create.md) ismertető cikket. Hozzon létre egy tárolót **adftutorial** néven. 

### <a name="create-a-data-source-table-in-your-database"></a>Adatforrás-tábla létrehozása az adatbázisban

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
    > - Cserélje le az &lt; adatbázis nevét annak az &gt; adatbázisnak a nevére, amely a data_source_table rendelkezik.
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
Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-Az-ps) ismertető cikkben szereplő utasításokat a legújabb Azure PowerShell-modulok telepítéséhez.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása
1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) nevét idézőjelek között, majd futtassa a parancsot. Példa: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot
2. Adjon meg egy változót az adat-előállító helyéhez:

    ```powershell
    $location = "East US"
    ```
3. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához:

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ```
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot.
3. Adjon meg egy változót az adat-előállító nevéhez.

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét, hogy globálisan egyedi legyen.  

    ```powershell
    $dataFactoryName = "IncCopyChgTrackingDF";
    ```
5. Az adatelőállító létrehozásához futtassa az alábbi **set-AzDataFactoryV2** parancsmagot:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFIncCopyChangeTrackingTestFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie.
* Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.


## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a szakaszban társított szolgáltatásokat hoz létre az Azure Storage-fiókjához és az adatbázisához Azure SQL Databaseban.

### <a name="create-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása
Ebben a lépésben az Azure Storage-fiókot társítja az adat-előállítóval.

1. Hozzon létre egy **AzureStorageLinkedService.json** nevű JSON-fájlt a **C:\ADFTutorials\IncCopyChangeTrackingTutorial** mappában a következő tartalommal (ha még nem létezik a mappa, hozza létre). Mielőtt mentené a fájlt, az `<accountName>` és az `<accountKey>` helyőrzőt cserélje le az Azure Storage-fiók nevére és kulcsára.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
            }
        }
    }
    ```
2. A **Azure PowerShellban** váltson a **C:\ADFTutorials\IncCopyChangeTrackingTutorial** mappára.
3. Futtassa a **set-AzDataFactoryV2LinkedService** parancsmagot a társított szolgáltatás létrehozásához: **AzureStorageLinkedService**. A következő példában a **ResourceGroupName** és a **DataFactoryName** paraméter értékeit fogja megadni.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Itt látható a minta kimenete:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása.
Ebben a lépésben összekapcsolja az adatbázist az adatelőállítóval.

1. Hozzon létre egy **AzureSQLDatabaseLinkedService.js** nevű JSON-fájlt a **C:\ADFTutorials\IncCopyChangeTrackingTutorial** mappában a következő tartalommal: a fájl mentése előtt cserélje le a **&lt; kiszolgáló- &gt; &lt; adatbázis nevét, a &gt; &lt; felhasználói azonosítót &gt; és a &lt; jelszót &gt;** a kiszolgáló nevére, az adatbázis nevére, a felhasználói azonosítóra és a jelszóra.

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;"
            }
        }
    }
    ```
2. A **Azure PowerShell** a **set-AzDataFactoryV2LinkedService** parancsmag futtatásával hozza létre a társított szolgáltatást: **AzureSQLDatabaseLinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Itt látható a minta kimenete:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre, amelyek az adatforrást, az adatcél helyét és a SYS_CHANGE_VERSION változó tárolási helyét jelölik.

### <a name="create-a-source-dataset"></a>Forrásadatkészlet létrehozása
Ebben a lépésben egy adatkészletet hoz létre, amely a forrásadatokat jelöli.

1. Hozzon létre egy SourceDataset.json nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal:

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

2.  Futtassa a Set-AzDataFactoryV2Dataset parancsmagot az adatkészlet létrehozásához: SourceDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:

    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Fogadó adatkészlet létrehozása
Ebben a lépésben egy adatkészletet hoz létre, amely a forrásadattárból másolt adatokat jelöli.

1. Hozzon létre egy SinkDataset.json nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal:

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incchgtracking",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    Előfeltételként hozzon létre egy adftutorial nevű tárolót az Azure Blob Storage-ben. Ha még nem létezik, hozza létre a tárolót, vagy állítsa be egy meglévő tároló nevét. Ebben az oktatóanyagban a kimeneti fájl nevét dinamikusan hozzuk létre a következő kifejezéssel: @CONCAT('Incremental-', pipeline().RunId, '.txt').
2.  Futtassa a Set-AzDataFactoryV2Dataset parancsmagot az adatkészlet létrehozásához: SinkDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:

    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

### <a name="create-a-change-tracking-dataset"></a>Változáskövetési adatkészlet létrehozása
Ebben a lépésben egy adatkészletet hozunk létre a változáskövetés verziószámának tárolásához.  

1. Hozzon létre egy ChangeTrackingDataset.json nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal:

    ```json
    {
        "name": " ChangeTrackingDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "table_store_ChangeTracking_version"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    Előfeltételként hozzon létre egy table_store_ChangeTracking_version nevű táblát.
2.  Futtassa a Set-AzDataFactoryV2Dataset parancsmagot az adatkészlet létrehozásához: ChangeTrackingDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "ChangeTrackingDataset" -File ".\ChangeTrackingDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:

    ```json
    DatasetName       : ChangeTrackingDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

## <a name="create-a-pipeline-for-the-full-copy"></a>Folyamat létrehozása teljes másolat készítéséhez
Ebben a lépésben egy másolási tevékenységgel rendelkező folyamatot fog létrehozni, amely a forrásadattár (Azure SQL Database) összes adatát átmásolja a céladattárba (Azure Blob Storage).

1. Hozzon létre egy FullCopyPipeline.json nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal:

    ```json
    {
        "name": "FullCopyPipeline",
        "properties": {
            "activities": [{
                "name": "FullCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },

                "inputs": [{
                    "referenceName": "SourceDataset",
                    "type": "DatasetReference"
                }],
                "outputs": [{
                    "referenceName": "SinkDataset",
                    "type": "DatasetReference"
                }]
            }]
        }
    }
    ```
2. Futtassa a Set-AzDataFactoryV2Pipeline parancsmagot a következő folyamat létrehozásához: FullCopyPipeline.

   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "FullCopyPipeline" -File ".\FullCopyPipeline.json"
   ```

   Itt látható a minta kimenete:

   ```console
    PipelineName      : FullCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {FullCopyActivity}
    Parameters        :
   ```

### <a name="run-the-full-copy-pipeline"></a>A teljes másolási folyamat futtatása
Futtassa a folyamatot: **FullCopyPipeline** a **Meghívási-AzDataFactoryV2Pipeline** parancsmag használatával.

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "FullCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName        
```

### <a name="monitor-the-full-copy-pipeline"></a>A teljes másolási folyamat megfigyelése

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com).
2. Kattintson a **Minden szolgáltatás** elemre, végezzen keresést a `data factories` kulcsszóval, és válassza az **Adat-előállítók** lehetőséget.

    ![Adat-előállítók menü](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-data-factories-menu-1.png)
3. Keresse meg az **adat-előállítóját** az adat-előállítók listájában, és kattintson rá az Adat-előállító lap megnyitásához.

    ![Az adat-előállító keresése](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-search-data-factory-2.png)
4. Az Adat-előállító lapon kattintson a **Figyelés és felügyelet** csempére.

    ![Monitor & Manage csempe](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-monitor-manage-tile-3.png)    
5. Az **adatintegrációs alkalmazás** egy külön lapon indul el. Láthatja az összes **folyamat futtatását** és állapotát. A következő példában a folyamatfuttatás állapota **Sikeres**. A **Paraméterek** oszlopban található hivatkozásra kattintva megtekintheti a folyamatnak átadott paramétereket. Hiba esetén egy hivatkozás jelenik meg a **Hiba** oszlopban. Kattintson a **Műveletek** oszlopban található hivatkozásra.

    ![Képernyőfelvétel: az adatfeldolgozó folyamatait bemutató képernyőkép.](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-4.png)    
6. Ha a **Műveletek** oszlopban található hivatkozásra kattint, megjelenik a következő lap, amely megmutatja a folyamat összes **tevékenységfuttatását**.

    ![Képernyőfelvétel: az adatelőállítók tevékenység-futtatási funkciói, a folyamatokra mutató hivatkozás.](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-5.png)
7. A **Folyamatfuttatások** nézetre való visszaváltáshoz kattintson a **Folyamatok** lehetőségre a képen látható módon.


### <a name="review-the-results"></a>Az eredmények áttekintése
Egy `incremental-<GUID>.txt` nevű fájl található az `adftutorial` nevű tároló `incchgtracking` mappájában.

![Kimeneti fájl teljes másolásból](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-copy-output-file.png)

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

1. Hozzon létre egy IncrementalCopyPipeline.json nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal:

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupLastChangeTrackingVersionActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from table_store_ChangeTracking_version"
                        },
                        "dataset": {
                            "referenceName": "ChangeTrackingDataset",
                            "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupCurrentChangeTrackingVersionActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion"
                        },
                        "dataset": {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupLastChangeTrackingVersionActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupCurrentChangeTrackingVersionActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],
                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },
                {
                    "name": "StoredProceduretoUpdateChangeTrackingActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "Update_ChangeTracking_Version",
                        "storedProcedureParameters": {
                            "CurrentTrackingVersion": {
                                "value": "@{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}",
                                "type": "INT64"
                            },
                            "TableName": {
                                "value": "@{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName}",
                                "type": "String"
                            }
                        }
                    },
                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Futtassa a Set-AzDataFactoryV2Pipeline parancsmagot a következő folyamat létrehozásához: FullCopyPipeline.

   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ```

   Itt látható a minta kimenete:

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {LookupLastChangeTrackingVersionActivity, LookupCurrentChangeTrackingVersionActivity, IncrementalCopyActivity, StoredProceduretoUpdateChangeTrackingActivity}
    Parameters        :
   ```

### <a name="run-the-incremental-copy-pipeline"></a>A növekményes másolási folyamat futtatása
Futtassa a folyamatot: **IncrementalCopyPipeline** a **Meghívási-AzDataFactoryV2Pipeline** parancsmag használatával.

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName     
```


### <a name="monitor-the-incremental-copy-pipeline"></a>A növekményes másolási folyamat figyelése
1. Az **adatintegrációs alkalmazásban** frissítse a **Folyamatfuttatások** nézetet. Ellenőrizze, hogy az IncrementalCopyPipeline szerepel-e a listában. Kattintson a **Műveletek** oszlopban található hivatkozásra.  

    ![Képernyőfelvétel: a folyamat futtatására szolgáló adatfeldolgozó.](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-6.png)    
2. Ha a **Műveletek** oszlopban található hivatkozásra kattint, megjelenik a következő lap, amely megmutatja a folyamat összes **tevékenységfuttatását**.

    ![Képernyőfelvétel: a folyamat futása egy olyan adatfeldolgozó esetében, amelynek több jelöltje sikeres.](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-7.png)
3. A **Folyamatfuttatások** nézetre való visszaváltáshoz kattintson a **Folyamatok** lehetőségre a képen látható módon.

### <a name="review-the-results"></a>Az eredmények áttekintése
A második fájl az `adftutorial` nevű tároló `incchgtracking` mappájában található.

![Kimeneti fájl növekményes másolásból](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-copy-output-file.png)

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
>[Új fájlok másolása a lastmodifieddate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
