---
title: Az Adatmásolás ömlesztve a PowerShell használatával
description: A másolási tevékenységgel rendelkező Azure Data Factory a forrás adattárban lévő adatok tömeges másolására használható.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/22/2018
ms.openlocfilehash: bbd0a98ad14aeef31232aa2557860989e39273aa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375119"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory-using-powershell"></a>Több táblázat másolása ömlesztve Azure Data Factory használatával a PowerShell használatával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez az oktatóanyag azt mutatja be, hogyan **másolhat több táblázatot Azure SQL Databaseról az Azure szinapszis Analytics szolgáltatásba**. A minta egyéb másolási forgatókönyvek esetén is alkalmazható. Például táblák másolására az SQL Serverről/Oracle-ből az Azure SQL Database-be/Data Warehouse-ba/Azure Blobba, vagy különböző elérési utak másolására a Blobból Azure SQL Database-táblákba.

Az oktatóanyag a következő főbb lépésekből áll:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre Azure SQL Database, az Azure szinapszis Analytics és az Azure Storage társított szolgáltatásait.
> * Hozzon létre Azure SQL Database és az Azure szinapszis Analytics-adatkészleteket.
> * Egy folyamat létrehozása a másolni kívánt táblák megkeresésére, és egy másik folyamat létrehozása a tényleges másolási művelet elvégzésére 
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Az oktatóanyag az Azure PowerShellt használja. Az adat-előállítók egyéb eszközökkel/SDK-kkal való létrehozásával kapcsolatban lásd [rövid útmutatóinkat](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Teljes körű munkafolyamat
Ebben a forgatókönyvben számos táblázat található Azure SQL Databaseban, amelyeket az Azure szinapszis Analytics szolgáltatásba szeretne másolni. A következők a folyamatokban végbemenő munkafolyamat lépései logikai sorrendben:

![Munkafolyamat](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* Az első folyamat megkeresi azoknak a tábláknak a listáját, amelyeket át kell másolni a fogadó adattárakba.  Másik megoldásként fenntarthat egy metaadattáblát, amely felsorolja az összes, a fogadó adattárba másolandó táblát. A folyamat ezután elindít egy másik folyamatot, amely végigiterál az adatbázis összes tábláján, és elvégzi az adatmásolási műveletet.
* A második folyamat hajtja végre a tényleges másolást. A táblák listáját használja paraméterként. A listában szereplő minden táblázat esetében másolja az adott táblázatot Azure SQL Database az Azure szinapszis Analytics megfelelő táblájába a [szakaszos másolással a blob Storage-on keresztül,](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics) a legjobb teljesítmény érdekében. Ebben a példában az első folyamat a táblák listáját adja át a paraméter értékeként. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure PowerShell**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-Az-ps) ismertető cikkben szereplő utasításokat.
* **Azure Storage-fiók**. Az Azure Storage-fiók a tömeges másolási műveletben átmeneti blobtárolóként működik. 
* **Azure SQL Database**. Ez az adatbázis tartalmazza a forrásadatokat. 
* Az **Azure szinapszis Analytics**. Ez az adattárház tárolja az SQL Database-ből átmásolt adatokat. 

### <a name="prepare-sql-database-and-azure-synapse-analytics"></a>Az SQL Database és az Azure szinapszis Analytics előkészítése

**A forrás Azure SQL Database előkészítése**:

Hozzon létre egy adatbázist az Adventure Works LT mintaadatok használatával SQL Database az [adatbázis létrehozása Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) cikkben. Ez az oktatóanyag a mintaadatbázis összes tábláját átmásolja az Azure szinapszis Analyticsbe.

**A fogadó Azure szinapszis Analytics előkészítése**:

1. Ha nem rendelkezik Azure szinapszis Analytics-munkaterülettel, tekintse meg az első [lépések az Azure szinapszis Analytics](..\synapse-analytics\get-started.md) használatával című cikket a létrehozás lépéseihez.

2. Hozza létre a megfelelő Table-sémákat az Azure szinapszis Analyticsben. A későbbiekben az Azure Data Factory segítségével fogja áttelepíteni/másolni az adatokat.

## <a name="azure-services-to-access-sql-server"></a>Az SQL Server elérésének engedélyezése az Azure-szolgáltatások számára

A SQL Database és az Azure szinapszis Analytics esetében is lehetővé teszi az Azure-szolgáltatások számára az SQL Server elérését. Győződjön **meg** arról, hogy az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás be van kapcsolva a kiszolgálóhoz. Ez a beállítás lehetővé teszi, hogy az Data Factory szolgáltatás beolvassa az adatait a Azure SQL Databaseból, és adatokba írjon az Azure szinapszis Analytics szolgáltatásba. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

1. Kattintson a **Minden szolgáltatás** elemre a bal oldalon, majd az **SQL-kiszolgálók** elemre.
2. Válassza ki a kiszolgálót, és kattintson a **BEÁLLÍTÁSOK** területen a **Tűzfal** elemre.
3. A **Tűzfalbeállítások** lapon kattintson a **BE** kapcsolóra az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás mellett.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. Indítsa el a **PowerShellt**. Az Azure PowerShellt hagyja megnyitva az oktatóanyag végéig. Ha bezárja és újra megnyitja a programot, akkor újra le kell futtatnia a parancsokat.

    Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.
        
    ```powershell
    Connect-AzAccount
    ```
    Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.

    ```powershell
    Get-AzSubscription
    ```
    Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Cserélje le az **SubscriptionId** -t az Azure-előfizetés azonosítójával:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. A **set-AzDataFactoryV2** parancsmag futtatásával hozzon létre egy adatelőállítót. A parancs végrehajtása előtt cserélje le a helyőrzőket a saját értékeire. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Vegye figyelembe a következő szempontokat:

    * Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének vagy rendszergazdájának kell lennie.
    * Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ebben az oktatóanyagban létrehoz három társított szolgáltatást a forrás, a fogadó és az átmeneti blobhoz, beleértve az adattárakkal való kapcsolatot:

### <a name="create-the-source-azure-sql-database-linked-service"></a>A forrás Azure SQL Database-beli társított szolgáltatás létrehozása

1. Hozzon létre egy **AzureSqlDatabaseLinkedService.json** nevű JSON-fájlt a **C:\ADFv2TutorialBulkCopy** mappában a következő tartalommal: (Ha még nem létezik, hozza létre az ADFv2TutorialBulkCopy nevű mappát.)

    > [!IMPORTANT]
    > A fájl mentése előtt a &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; és &lt;password&gt; értékeket cserélje le az Azure SQL-adatbázis értékeire.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. Az **Azure PowerShellben** váltson az **ADFv2TutorialBulkCopy** mappára.

3. Futtassa a **set-AzDataFactoryV2LinkedService** parancsmagot a társított szolgáltatás létrehozásához: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    Itt látható a minta kimenete:

    ```console
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-synapse-analytics-linked-service"></a>A fogadó Azure szinapszis Analytics társított szolgáltatás létrehozása

1. Hozzon létre egy **AzureSqlDWLinkedService.json** nevű JSON-fájlt a **C:\ADFv2TutorialBulkCopy** mappában a következő tartalommal:

    > [!IMPORTANT]
    > A fájl mentése előtt a &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; és &lt;password&gt; értékeket cserélje le az Azure SQL-adatbázis értékeire.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. A társított szolgáltatás létrehozásához: **AzureSqlDWLinkedService** futtassa a **set-AzDataFactoryV2LinkedService** parancsmagot.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Itt látható a minta kimenete:

    ```console
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>Az átmeneti Azure Storage-beli társított szolgáltatás létrehozása

Ebben az oktatóanyagban a jobb másolási teljesítmény érdekében az Azure Blob Storage-ot átmeneti területként használja a PolyBase engedélyezéséhez.

1. Hozzon létre egy **AzureStorageLinkedService.json** nevű JSON-fájlt a **C:\ADFv2TutorialBulkCopy** mappában a következő tartalommal:

    > [!IMPORTANT]
    > A fájl mentése előtt az &lt;accountName&gt; és az &lt;accountKey&gt; kifejezést cserélje le az Azure Storage-fiók nevére és kulcsára.

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

2. A társított szolgáltatás létrehozásához: **AzureStorageLinkedService** futtassa a **set-AzDataFactoryV2LinkedService** parancsmagot.

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

## <a name="create-datasets"></a>Adatkészletek létrehozása

Ebben az oktatóanyagban létrehozza a forrás és a fogadó adatkészletet, amelyek megadják az adatok tárolásának helyét:

### <a name="create-a-dataset-for-source-sql-database"></a>Adatkészlet létrehozása a forrás SQL-adatbázishoz

1. Hozzon létre egy **AzureSqlDatabaseDataset.json** nevű JSON-fájlt a **C:\ADFv2TutorialBulkCopy** mappában a következő tartalommal. A „tableName” egy helyőrző, mivel később, a másolási tevékenység során az SQL-lekérdezést fogja használni az adatok lekéréséhez.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. A következő adatkészlet létrehozásához futtassa a **set-AzDataFactoryV2Dataset** parancsmagot: **AzureSqlDatabaseDataset**.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    Itt látható a minta kimenete:

    ```console
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-synapse-analytics"></a>Adatkészlet létrehozása a fogadó Azure szinapszis Analyticshez

1. Hozzon létre egy **AzureSqlDWDataset.json** nevű JSON-fájlt a **C:\ADFv2TutorialBulkCopy** mappában a következő tartalommal: A „tableName” paraméterként van beállítva. A másolási tevékenység, amely erre az adatkészletre hivatkozik, később át fogja adni a tényleges értéket az adatkészletnek.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. A következő adatkészlet létrehozásához futtassa a **set-AzDataFactoryV2Dataset** parancsmagot: **AzureSqlDWDataset**.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    Itt látható a minta kimenete:

    ```console
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>Folyamatok létrehozása

Ebben az oktatóanyagban két folyamatot hoz létre:

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Az „IterateAndCopySQLTables” folyamat létrehozása

Ez a folyamat a táblák listáját használja paraméterként. A lista minden táblázatában az adatok átmásolása az Azure SQL Database lévő táblából az Azure szinapszis Analytics szakaszos másolás és a kiinduló használatával.

1. Hozzon létre egy **IterateAndCopySQLTables.json** nevű JSON-fájlt a **C:\ADFv2TutorialBulkCopy** mappában a következő tartalommal:

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from Azure SQL Database to Azure Synapse Analytics",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. A folyamat létrehozása: **IterateAndCopySQLTables**, futtassa a **set-AzDataFactoryV2Pipeline** parancsmagot.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    Itt látható a minta kimenete:

    ```console
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>A „GetTableListAndTriggerCopyData” folyamat létrehozása

Ez a folyamat két lépést hajt végre:

* Megkeresi az Azure SQL Database rendszertábláját, és lekéri a másolni kívánt táblák listáját.
* Elindítja az „IterateAndCopySQLTables” folyamatot, amely elvégezi a tényleges adatmásolást.

1. Hozzon létre egy **GetTableListAndTriggerCopyData.json** nevű JSON-fájlt a **C:\ADFv2TutorialBulkCopy** mappában a következő tartalommal:

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
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

2. A folyamat létrehozása: **GetTableListAndTriggerCopyData**, futtassa a **set-AzDataFactoryV2Pipeline** parancsmagot.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    Itt látható a minta kimenete:

    ```console
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>Folyamat futásának indítása és monitorozása

1. Indítsa el a fő „GetTableListAndTriggerCopyData” folyamat futását, és őrizze meg a folyamat futtatási azonosítóját későbbi monitorozás céljából. Eközben az ExecutePipeline tevékenységben megadott módon elindítja az „IterateAndCopySQLTables” folyamat futását.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  Futtassa a következő szkriptet a **GetTableListAndTriggerCopyData** folyamat futási állapotának folyamatos ellenőrzéséhez, majd nyomtassa ki az utolsó folyamatfuttatás és tevékenységfuttatás eredményét.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    Itt látható a példa futtatás kimenete:

    ```console
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Az „**IterateAndCopySQLTables**” folyamat futtatási azonosítójának lekéréséhez és a tevékenységfuttatás részletes eredményének áttekintéséhez tegye a következőket.

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    Itt látható a példa futtatás kimenete:

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. Kapcsolódjon a fogadó Azure szinapszis Analytics szolgáltatáshoz, és győződjön meg róla, hogy az adatok Azure SQL Database megfelelően lettek átmásolva.

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
>[Adatok fokozatos másolása](tutorial-incremental-copy-powershell.md)
