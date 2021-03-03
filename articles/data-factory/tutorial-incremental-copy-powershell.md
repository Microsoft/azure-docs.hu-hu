---
title: Tábla növekményes másolása a PowerShell-lel
description: Ebben az oktatóanyagban egy Azure Data Factory folyamatot hoz létre, amely Növekményesen másol egy Azure SQL Database-ből származó adatait az Azure Blob Storage-ba.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: f4cb4807e6f2620bb76649fc7c7dcce7363cf4a4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740987"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-powershell"></a>Adatok növekményes betöltése Azure SQL Databaseról az Azure Blob Storage-ba a PowerShell használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ebben az oktatóanyagban a Azure Data Factory használatával hozzon létre egy folyamatot, amely egy Azure SQL Database egyik táblájából az Azure Blob Storage-ba tölti be a különbözeti adatok betöltését.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az adatraktár előkészítése a küszöbértékek tárolására.
> * Adat-előállító létrehozása
> * Társított szolgáltatások létrehozása.
> * Forrás-, fogadó- és küszöbadatkészletek létrehozása.
> * Folyamat létrehozása.
> * A folyamat futtatása.
> * A folyamat futásának monitorozása.

## <a name="overview"></a>Áttekintés
Itt látható a megoldás összefoglaló jellegű ábrája:

![Adatok növekményes betöltése](media/tutorial-Incrementally-copy-powershell/incrementally-load.png)

Az alábbiak a megoldás kialakításának leglényegesebb lépései:

1. **A küszöb oszlopának kiválasztása**.
    Jelölje ki az adatforrás egyik oszlopát, amelynek alapján az új és a frissített rekordok minden egyes futtatáskor leválogathatók. Normális esetben az ebben a kiválasztott oszlopban (például: last_modify_time vagy ID) lévő adatok a sorok létrehozásával vagy frissítésével folyamatosan növekednek. Az ebben az oszlopban lévő legnagyobb érték szolgál a küszöbként.

2. **Egy adatraktár előkészítése a küszöbértékek tárolására**.   
    Ebben az oktatóanyagban a küszöbértékeket egy SQL-adatbázisban tároljuk.

3. **Hozzon létre egy folyamatot a következő munkafolyamattal**:

    Ebben a megoldásban a folyamat a következő tevékenységeket tartalmazza:

    * Két keresési tevékenység létrehozása. Az első keresési tevékenység az utolsó küszöbértéket kéri le. A második keresési tevékenység az új küszöbértéket kéri le. Ezeket a küszöbértékeket a rendszer átadja a másolási tevékenységnek.
    * Egy másolási tevékenység létrehozása, amely a küszöbértéket tartalmazó oszlopban a régi küszöbértéknél magasabb, az új küszöbértéknél alacsonyabb értékkel rendelkező sorokat másolja át a forrásadattárból. Ezután a módosított adatokat a forrásadattárból új fájlként egy Blob Storage-tárolóba másolja.
    * Egy StoredProcedure tevékenység létrehozása, amely frissíti a küszöbértékeket a folyamat következő futtatásához.


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure SQL Database**. Ezt az adatbázist használjuk forrásadattárként. Ha nem rendelkezik Azure SQL Database-adatbázissal, tekintse meg a következő témakört: [adatbázis létrehozása Azure SQL Databaseben](../azure-sql/database/single-database-create-quickstart.md) a létrehozásához szükséges lépések.
* **Azure Storage**. A blobtárolót használjuk majd fogadóadattárként. Ha még nem rendelkezik tárfiókkal, tekintse meg a [tárfiók létrehozásának](../storage/common/storage-account-create.md) lépéseit ismertető cikket. Hozzon létre egy tárolót adftutorial néven. 
* **Azure PowerShell**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-Az-ps) ismertető cikkben szereplő utasításokat.

### <a name="create-a-data-source-table-in-your-sql-database"></a>Adatforrástábla létrehozása az SQL-adatbázisban
1. Nyissa meg az SQL Server Management Studiót. A **Server Explorerben** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.

2. Futtassa a következő SQL-parancsot az SQL-adatbázison egy tábla `data_source_table` néven, adatforrástárként történő létrehozásához:

    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    Az oktatóanyagban a LastModifytime oszlopot használjuk küszöboszlopként. Az adatforrástár adatai az alábbi táblázatban láthatók:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Egy másik tábla létrehozása az SQL-adatbázisban a felső küszöbértékek tárolására
1. Futtassa a következő SQL-parancsot az SQL-adatbázison egy `watermarktable` nevű, a küszöbértékek tárolására szolgáló tábla létrehozásához:  

    ```sql
    create table watermarktable
    (

    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Állítsa be a felső küszöb alapértelmezett értékét a forrásadattár táblanevével. Ebben az oktatóanyagban a tábla neve a következő: data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Tekintse át a következő tábla adatait: `watermarktable`.

    ```sql
    Select * from watermarktable
    ```
    Kimenet:

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Tárolt eljárás létrehozása az SQL-adatbázisban

Az alábbi parancs futtatásával hozzon létre egy tárolt eljárást az SQL-adatbázisban:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime
WHERE [TableName] = @TableName

END
```

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása
1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) nevét dupla idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot.

2. Adjon meg egy változót az adat-előállító helyéhez.

    ```powershell
    $location = "East US"
    ```
3. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához:

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ```
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot.

4. Adjon meg egy változót az adat-előállító nevéhez.

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét, hogy globálisan egyedi legyen. Például: ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFIncCopyTutorialFactory";
    ```
5. Az adatelőállító létrehozásához futtassa az alábbi **set-AzDataFactoryV2** parancsmagot:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

Vegye figyelembe a következő szempontokat:

* Az adat-előállító nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra:

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Adatelőállító-példányok létrehozásához a felhasználói fióknak, amellyel bejelentkezik az Azure-ba, a közreműködő vagy tulajdonos szerepkörök tagjának, vagy az Azure-előfizetés rendszergazdájának kell lennie.
* Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adattárolók (Storage, SQL Database, Azure SQL felügyelt példány stb.) és számítási erőforrások (Azure HDInsight stb.) más régiókban is lehetnek.


## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a szakaszban társított szolgáltatásokat hoz létre a Storage-fiókjához, és SQL Database.

### <a name="create-a-storage-linked-service"></a>Storage-beli társított szolgáltatás létrehozása
1. Hozzon létre egy AzureStorageLinkedService.json nevű JSON-fájlt a C:\ADF mappában az alábbi tartalommal. (Ha még nem létezik, hozza létre az ADF mappát.) `<accountName>` A fájl mentése előtt cserélje le a és a `<accountKey>` nevet a Storage-fiók nevére és kulcsára.

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
2. A PowerShellben lépjen az ADF mappára.

3. Futtassa a **set-AzDataFactoryV2LinkedService** parancsmagot a társított szolgáltatás AzureStorageLinkedService létrehozásához. A következő példában a *ResourceGroupName* és a *DataFactoryName* paraméterek értékeit adja meg:

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Itt látható a minta kimenete:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-a-sql-database-linked-service"></a>SQL Database-beli társított szolgáltatás létrehozása
1. Hozzon létre egy AzureSQLDatabaseLinkedService.json nevű JSON-fájlt a C:\ADF mappában az alábbi tartalommal. (Ha még nem létezik, hozza létre az ADF mappát.) &lt;A fájl mentése előtt cserélje le a kiszolgálót, &gt; az adatbázist, a &lt; &gt; &lt; felhasználói azonosítót &gt; és &lt; a jelszót a &gt; kiszolgáló, az adatbázis, a felhasználói azonosító és a jelszó helyére.

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database>; Persist Security Info=False; User ID=<user> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;"
            }
        }
    }
    ```
2. A PowerShellben lépjen az ADF mappára.

3. Futtassa a **set-AzDataFactoryV2LinkedService** parancsmagot a társított szolgáltatás AzureSQLDatabaseLinkedService létrehozásához.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Itt látható a minta kimenete:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre, amelyek a forrás és a fogadó adatait jelölik.

### <a name="create-a-source-dataset"></a>Forrásadatkészlet létrehozása

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
    Ebben az oktatóanyagban a tábla neve a következő: data_source_table. Ha más nevű táblát használ, cserélje le a nevet.

2. Futtassa a **set-AzDataFactoryV2Dataset** parancsmagot az adatkészlet SourceDataset létrehozásához.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:

    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Fogadó adatkészlet létrehozása

1. Hozzon létre egy SinkDataset.json nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal:

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
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

    > [!IMPORTANT]
    > Ez a kódrészlet feltételezi, hogy rendelkezik egy blob-tárolóval a `adftutorial` blob Storage-ban. Ha még nem létezik, hozza létre a tárolót, vagy állítsa be egy meglévő tároló nevét. Az `incrementalcopy` kimeneti mappa automatikusan létrejön, ha még nem létezik a tárolóban. Ebben az oktatóanyagban a fájl nevét dinamikusan hozzuk létre a következő kifejezés használatával: `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

2. Futtassa a **set-AzDataFactoryV2Dataset** parancsmagot az adatkészlet SinkDataset létrehozásához.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:

    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-a-watermark"></a>Adatkészlet létrehozása a küszöbhöz
Ebben a lépésben egy adatkészletet hozunk létre a felső küszöbértékek tárolására.

1. Hozzon létre egy WatermarkDataset.json nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal:

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Futtassa a **set-AzDataFactoryV2Dataset** parancsmagot az adatkészlet WatermarkDataset létrehozásához.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:

    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Az oktatóanyag során egy olyan folyamatot fogunk létrehozni, amelyben két keresési, egy másolási és egy StoredProcedure (tárolt eljárás) tevékenység van összefűzve.


1. Hozzon létre egy IncrementalCopyPipeline.json nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal:

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },

                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
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
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
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
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {

                        "storedProcedureName": "usp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
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


2. Futtassa a **set-AzDataFactoryV2Pipeline** parancsmagot a folyamat IncrementalCopyPipeline létrehozásához.

   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ```

   Itt látható a minta kimenete:

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```

## <a name="run-the-pipeline"></a>A folyamat futtatása

1. Futtassa a folyamat IncrementalCopyPipeline a **hívás-AzDataFactoryV2Pipeline** parancsmag használatával. Cserélje le a helyőrzőket a saját erőforráscsoportja és adat-előállítója nevére.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
2. Ellenőrizze a folyamat állapotát a **Get-AzDataFactoryV2ActivityRun** parancsmag futtatásával egészen addig, amíg nem látja az összes sikeresen futó tevékenységet. Cserélje le a helyőrzőket a saját megfelelő időértékeire a *RunStartedAfter* és a *RunStartedBefore* paraméternél. Ebben az oktatóanyagban a *-RunStartedAfter "2017/09/14"* és a *-RunStartedBefore "2017/09/15"* paramétereket használja.

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Itt látható a minta kimenete:

    ```console
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>Az eredmények áttekintése

1. A blobtárolóban (fogadótároló) azt kell látnia, hogy az adatok át lettek másolva a SinkDataset által meghatározott fájlba. Ebben az oktatóanyagban a fájl neve: `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`. Nyissa meg a fájlt, és láthatja, hogy a benne lévő rekordok egyeznek az SQL-adatbázisban lévő adatokkal.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
2. Ellenőrizze a `watermarktable` legutolsó értékét. Láthatja, hogy a küszöbérték frissült.

    ```sql
    Select * from watermarktable
    ```

    Itt látható a minta kimenete:

    TableName | WatermarkValue
    --------- | --------------
    data_source_table | 2017-09-05 8:06:00.000

### <a name="insert-data-into-the-data-source-store-to-verify-delta-data-loading"></a>Adatok beszúrása a forrásadattárba a változásadatok betöltésének ellenőrzéséhez

1. Szúrjon be új adatokat az SQL-adatbázisba (forrásadattár).

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ```

    Az SQL-adatbázis a következő frissített adatokat tartalmazza:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. Futtassa újra a folyamat IncrementalCopyPipeline a **meghívó-AzDataFactoryV2Pipeline** parancsmag használatával. Cserélje le a helyőrzőket a saját erőforráscsoportja és adat-előállítója nevére.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
3. Ellenőrizze a folyamat állapotát a **Get-AzDataFactoryV2ActivityRun** parancsmag futtatásával egészen addig, amíg nem látja az összes sikeresen futó tevékenységet. Cserélje le a helyőrzőket a saját megfelelő időértékeire a *RunStartedAfter* és a *RunStartedBefore* paraméternél. Ebben az oktatóanyagban a *-RunStartedAfter "2017/09/14"* és a *-RunStartedBefore "2017/09/15"* paramétereket használja.

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Itt látható a minta kimenete:

    ```console
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4. A blobtárolóban azt láthatja, hogy egy újabb fájl jött létre. Ebben az oktatóanyagban az új fájl neve a következő: `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`. Nyissa meg ezt a fájlt, és láthatja, hogy két sornyi rekordot tartalmaz.

5. Ellenőrizze a `watermarktable` legutolsó értékét. Láthatja, hogy a küszöbérték ismét frissült.

    ```sql
    Select * from watermarktable
    ```
    Példa a kimenetre:

    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000


## <a name="next-steps"></a>Következő lépések
Az oktatóanyagban az alábbi lépéseket hajtotta végre:

> [!div class="checklist"]
> * Az adatraktár előkészítése a küszöbértékek tárolására.
> * Adat-előállító létrehozása
> * Társított szolgáltatások létrehozása.
> * Forrás-, fogadó- és küszöbadatkészletek létrehozása.
> * Folyamat létrehozása.
> * A folyamat futtatása.
> * A folyamat futásának monitorozása.

Ebben az oktatóanyagban a folyamat a blob Storage-ba Azure SQL Database egyetlen táblából másolta az adatait. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan másolhat egy SQL Server-adatbázis több táblájából származó adatok SQL Databaseba.

> [!div class="nextstepaction"]
>[Adatok növekményes betöltése az SQL Server több táblájából az Azure SQL-adatbázisba](tutorial-incremental-copy-multiple-tables-powershell.md)
