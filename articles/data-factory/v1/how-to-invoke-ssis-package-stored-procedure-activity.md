---
title: SSIS-csomag meghívása Azure Data Factory tárolt eljárási tevékenység használatával
description: Ez a cikk azt ismerteti, hogyan hívhat meg egy SQL Server Integration Services (SSIS) csomagot egy Azure Data Factory folyamatból a tárolt eljárási tevékenység használatával.
author: linda33wj
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 6c831fa1c6351840693f2b10a22f59c5cc424d0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392884"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>SSIS-csomag meghívása tárolt eljárási tevékenység használatával Azure Data Factory
Ez a cikk azt ismerteti, hogyan hívhat meg egy SSIS-csomagot egy Azure Data Factory folyamatból egy tárolt eljárási tevékenység használatával. 

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a SSIS-csomagok meghívása tárolt eljárással tevékenység a](../how-to-invoke-ssis-package-stored-procedure-activity.md)alkalmazásban című témakört.

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-sql-database"></a>Azure SQL Database 
A jelen cikkben található útmutató Azure SQL Databaset használ. Használhatja a felügyelt Azure SQL-példányokat is.

### <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása
Hozzon létre egy Azure-SSIS integrációs modult, ha még nem rendelkezik az oktatóanyag részletes utasításával [: SSIS-csomagok telepítése](../tutorial-deploy-ssis-packages-azure.md). Azure-SSIS integrációs modul létrehozásához nem használható az 1. Data Factory-es verzió. 

## <a name="azure-powershell"></a>Azure PowerShell
Ebben a szakaszban a Azure PowerShell használatával hozzon létre egy Data Factory folyamatot egy tárolt eljárási tevékenységgel, amely egy SSIS-csomagot hív meg.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Telepítse a legújabb Azure PowerShell modulokat a [Azure PowerShell telepítésére és konfigurálására](/powershell/azure/install-az-ps)vonatkozó utasításokat követve.

### <a name="create-a-data-factory"></a>Adat-előállító létrehozása
Az alábbi eljárás egy adatelőállító létrehozásának lépéseit ismerteti. A folyamat egy tárolt eljárási tevékenységgel rendelkező folyamatot hoz létre ebben az adatgyárban. A tárolt eljárási tevékenység végrehajt egy tárolt eljárást a SSISDB-adatbázisban a SSIS-csomag futtatásához.

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../../azure-resource-manager/management/overview.md) nevét idézőjelek között, majd futtassa a parancsot. Példa: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$ResourceGroupName` változóhoz, majd futtassa újra a parancsot
2. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$ResourceGroupName` változóhoz, majd futtassa újra a parancsot. 
3. Adjon meg egy változót az adat-előállító nevéhez. 

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét, hogy globálisan egyedi legyen. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Az adatok előállítójának létrehozásához futtassa a következő **New-AzDataFactory** parancsmagot a $ResGrp változó hely és ResourceGroupName tulajdonságának használatával: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie.

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása
Hozzon létre egy társított szolgáltatást, amely összekapcsolja az adatbázist a SSIS-katalógust tároló Azure SQL Databaseban. Data Factory a társított szolgáltatás információit használja a SSISDB-adatbázishoz való kapcsolódáshoz, és egy tárolt eljárást hajt végre egy SSIS-csomag futtatásához. 

1. Hozzon létre egy **AzureSqlDatabaseLinkedService.js** nevű JSON-fájlt a **C:\ADF\RunSSISPackage** mappában a következő tartalommal: 

    > [!IMPORTANT]
    > &lt; &gt; A fájl mentése előtt cserélje le a servername, &lt; &gt; @ &lt; a username servername &gt; és a &lt; Password értéket a &gt; Azure SQL Database értékeire.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. A **Azure PowerShellban** váltson a **C:\ADF\RunSSISPackage** mappára.
3. Futtassa a **New-AzDataFactoryLinkedService** parancsmagot a társított szolgáltatás létrehozásához: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
Ez a kimeneti adatkészlet egy olyan próbabábu-adatkészlet, amely a folyamat ütemtervét vezeti. Figyelje meg, hogy a gyakoriság értéke óra, és az intervallum értéke 1. Ezért a folyamat óránként egyszer fut a folyamat kezdési és befejezési időpontjain belül. 

1. Hozzon létre egy OutputDataset.jsfájlt a következő tartalommal: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. A **New-AzDataFactoryDataset** parancsmag futtatásával hozzon létre egy adatkészletet. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Folyamat létrehozása tárolt eljárási tevékenységgel 
Ebben a lépésben létrehoz egy folyamatot egy tárolt eljárási tevékenységgel. A tevékenység meghívja a sp_executesql tárolt eljárást a SSIS-csomag futtatásához. 

1. Hozzon létre egy **MyPipeline.js** nevű JSON-fájlt a **C:\ADF\RunSSISPackage** mappában a következő tartalommal:

    > [!IMPORTANT]
    > &lt;A fájl mentése előtt cserélje le a mappa nevét, a &gt; &lt; projekt nevét &gt; , a &lt; csomag nevét, valamint a &gt; mappa, a projekt és a csomag nevét a SSIS-katalógusba.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. A folyamat létrehozásához: **RunSSISPackagePipeline**, futtassa a **New-AzDataFactoryPipeline** parancsmagot.

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. A **Get-AzDataFactorySlice** futtatásával részletes információkat kaphat a kimeneti adatkészlet * * összes szeletéről, amely a folyamat kimeneti táblázata.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Megfigyelheti, hogy a StartDateTime itt megadott értéke megegyezik a folyamat JSON-fájljában megadott kezdési idővel. 
1. A **Get-AzDataFactoryRun** futtatásával lekérheti egy adott szelet tevékenység-futtatásának részleteit.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Futtassa a parancsmagot, amíg a szelet **Ready** (Kész) vagy **Failed** (Sikertelen) állapotú nem lesz. 

    A következő lekérdezést futtathatja a kiszolgáló SSISDB adatbázisán annak ellenőrzéséhez, hogy a csomag végre lett hajtva. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Következő lépések
A tárolt eljárási tevékenységgel kapcsolatos részletekért tekintse meg a [tárolt eljárási tevékenységről](data-factory-stored-proc-activity.md) szóló cikket.