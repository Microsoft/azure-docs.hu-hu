---
title: SSIS-csomag futtatása tárolt eljárási tevékenységgel – Azure
description: Ez a cikk azt ismerteti, hogyan futtathat egy SQL Server Integration Services-(SSIS-) csomagot egy Azure Data Factory-folyamatban a tárolt eljárási tevékenység használatával.
author: swinarko
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6260606fe56d4dfc6bac93e04e726b5fd3298777
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100391507"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>SSIS-csomag futtatása a Tárolt eljárás tevékenységgel az Azure Data Factoryben

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogyan futtathat SSIS-csomagokat egy Azure Data Factory-folyamatban egy tárolt eljárási tevékenység használatával. 

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-sql-database"></a>Azure SQL Database 
A jelen cikkben található útmutató a SSIS-katalógus futtatásához Azure SQL Database használ. Használhatja az Azure SQL felügyelt példányát is.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása
Hozzon létre egy Azure-SSIS integrációs modult, ha még nem rendelkezik az oktatóanyag részletes utasításával [: SSIS-csomagok telepítése](./tutorial-deploy-ssis-packages-azure.md).

## <a name="data-factory-ui-azure-portal"></a>Data Factory felhasználói felület (Azure Portal)
Ebben a szakaszban Data Factory felhasználói felületet használ egy SSIS-csomagot meghívó tárolt eljárási tevékenységgel rendelkező Data Factory folyamat létrehozásához.

### <a name="create-a-data-factory"></a>Adat-előállító létrehozása
Első lépésként hozzon létre egy adatgyárat a Azure Portal használatával. 

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. Navigáljon a [Azure Portal](https://portal.azure.com). 
3. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**. 
      
     ![Új adat-előállító lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Az Azure-beli adatgyár nevének **globálisan egyedinek** kell lennie. Ha a Név mezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
     ![A név nem érhető el – hiba](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoport** esetében hajtsa végre az alábbi lépések egyikét:
     
   - Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából. 
   - Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét.   
         
     Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. A **Verzió** résznél válassza a **V2** értéket.
5. Válassza ki a Data Factory **helyét**. A legördülő listában csak a Data Factory által támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más helyeken is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Létrehozás** lehetőségre.
8. Az irányítópulton a következő csempe jelenik meg, amelynek állapota: az **adatgyár üzembe helyezése**. 

     ![adat-előállító üzembe helyezése csempe](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
     ![Data factory kezdőlap](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felület (UI) alkalmazás külön lapon történő elindításához kattintson az **Létrehozás és monitorozás** csempére. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Folyamat létrehozása tárolt eljárási tevékenységgel
Ebben a lépésben a Data Factory felhasználói felületét használja egy folyamat létrehozásához. Egy tárolt eljárási tevékenységet ad hozzá a folyamathoz, és úgy konfigurálja, hogy a SSIS-csomagot a sp_executesql tárolt eljárással futtassa. 

1. Az első lépések lapon kattintson a **folyamat létrehozása** elemre: 

    ![Első lépések lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. A **tevékenységek** eszközkészletben bontsa ki az **általános** elemet, és húzza a **tárolt eljárás** tevékenységet a folyamat tervező felületére. 

    ![Tárolt eljárás húzása tevékenység](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. A tárolt eljárási tevékenység tulajdonságok ablakában váltson az **SQL-fiók** lapra, és kattintson az **+ új** elemre. Létre kell hoznia egy, a SSIS-katalógust (SSIDB-adatbázist) futtató Azure SQL Database-adatbázishoz való kapcsolódást. 
   
    ![Új társított szolgáltatás gomb](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. A **New Linked Service** (Új társított szolgáltatás) ablakban végezze el az alábbi lépéseket: 

    1. A **típushoz** válassza a **Azure SQL Database** lehetőséget.
    2. Válassza ki az **alapértelmezett** Azure Integration Runtime az adatbázist futtató Azure SQL Databasehoz való kapcsolódáshoz `SSISDB` .
    3. Válassza ki a SSISDB-adatbázist futtató Azure SQL Database a **kiszolgálónév** mezőhöz.
    4. Válassza ki a **SSISDB** az **adatbázis neveként**.
    5. A **Felhasználónév** mezőbe írja be annak a felhasználónak a nevét, aki hozzáfér az adatbázishoz.
    6. A **Password (jelszó**) mezőben adja meg a felhasználó jelszavát. 
    7. Az adatbázishoz való kapcsolódás teszteléséhez kattintson a **Kapcsolódás tesztelése** gombra.
    8. Mentse a társított szolgáltatást a Save ( **Mentés** ) gombra kattintva. 

        ![Képernyőkép, amely egy új társított szolgáltatás hozzáadásának folyamatát mutatja be.](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. A Tulajdonságok ablakban váltson az **SQL-fiók** lap **tárolt eljárás** fülére, és hajtsa végre a következő lépéseket: 

    1. Válassza a **Szerkesztés** elemet. 
    2. A **tárolt eljárás neve** mezőbe írja be a következőt: `sp_executesql` . 
    3. Kattintson az **+ új** elemre a **tárolt eljárás paramétereinek** szakaszában. 
    4. A paraméter **neve** mezőbe írja be a következőt: **stmt**. 
    5. A paraméter **típusához** írja be a **karakterlánc** értéket. 
    6. A paraméter **értékeként** adja meg a következő SQL-lekérdezést:

        Az SQL-lekérdezésben határozza meg a **folder_name**, **project_name** és **package_name** paraméterek megfelelő értékeit. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Azure SQL Database társított szolgáltatás](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. A folyamat konfigurációjának ellenőrzéséhez kattintson az **Érvényesítés** elemre az eszköztáron. A **Folyamatérvényesítési jelentés** bezárásához kattintson a **>>** jelre.

    ![Folyamat érvényesítése](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Tegye közzé a folyamatot Data Factory az **összes közzététele** gombra kattintva. 

    ![Közzététel](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>A folyamat futtatása és figyelése
Ebben a szakaszban egy folyamat futtatását indítja el, majd figyeli. 

1. A folyamat futtatásának elindításához kattintson az **aktiválás** elemre az eszköztáron, majd kattintson az **aktiválás most** elemre. 

    ![Aktiválás most](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. A **Folyamatfuttatás** ablakban kattintson a **Befejezés** gombra. 
3. Váltson a bal oldali **Monitorozás** lapra. Megtekintheti a folyamat futását és állapotát, valamint egyéb információkat (például a Futtatás kezdési idejét). A nézet frissítéséhez kattintson a **Frissítés** parancsra.

    ![Folyamatfuttatások](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Kattintson az **Actions** (Műveletek) oszlopban található **View Activity Runs** (Tevékenységfuttatások megtekintése) hivatkozásra. Csak egy tevékenység fut, mivel a folyamatnak csak egy tevékenysége van (tárolt eljárási tevékenység).

    ![Tevékenységfuttatások](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. A következő **lekérdezést** futtathatja SQL Database SSISDB-adatbázisán a csomag végrehajtásának ellenőrzéséhez. 

    ```sql
    select * from catalog.executions
    ```

    ![Csomagok végrehajtásának ellenőrzése](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Létrehozhat egy ütemezett triggert is a folyamathoz, hogy a folyamat ütemezésen (óránként, naponta stb.) fusson. Példaként tekintse meg a következőt: [create a Factory-Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ebben a szakaszban a Azure PowerShell használatával hozzon létre egy Data Factory folyamatot egy tárolt eljárási tevékenységgel, amely egy SSIS-csomagot hív meg. 

Telepítse a legújabb Azure PowerShell modulokat a [Azure PowerShell telepítésére és konfigurálására](/powershell/azure/install-az-ps)vonatkozó utasításokat követve. 

### <a name="create-a-data-factory"></a>Adat-előállító létrehozása
Használhatja ugyanazt az adatelőállítót, amely rendelkezik a Azure-SSIS IR vagy létrehoz egy különálló adatelőállítót. Az alábbi eljárás egy adatelőállító létrehozásának lépéseit ismerteti. A folyamat egy tárolt eljárási tevékenységgel rendelkező folyamatot hoz létre ebben az adatgyárban. A tárolt eljárási tevékenység végrehajt egy tárolt eljárást a SSISDB-adatbázisban a SSIS-csomag futtatásához. 

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) nevét idézőjelek között, majd futtassa a parancsot. Példa: `"adfrg"`. 
   
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

5. Az adatok előállítójának létrehozásához futtassa az alábbi **set-AzDataFactoryV2** parancsmagot a $ResGrp változó hely és ResourceGroupName tulajdonságának használatával: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie.
* Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása
Hozzon létre egy társított szolgáltatást, amely összekapcsolja az adatbázist, amely a SSIS-katalógust az adatgyárba tárolja. Data Factory a társított szolgáltatás információit használja a SSISDB-adatbázishoz való kapcsolódáshoz, és egy tárolt eljárást hajt végre egy SSIS-csomag futtatásához. 

1. Hozzon létre egy **AzureSqlDatabaseLinkedService.js** nevű JSON-fájlt a **C:\ADF\RunSSISPackage** mappában a következő tartalommal: 

    > [!IMPORTANT]
    > &lt; &gt; A fájl mentése előtt cserélje le a servername, &lt; &gt; a username és a &lt; Password értéket a &gt; Azure SQL Database értékeire.

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

3. Futtassa a **set-AzDataFactoryV2LinkedService** parancsmagot a társított szolgáltatás létrehozásához: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Folyamat létrehozása tárolt eljárási tevékenységgel 
Ebben a lépésben létrehoz egy folyamatot egy tárolt eljárási tevékenységgel. A tevékenység meghívja a sp_executesql tárolt eljárást a SSIS-csomag futtatásához. 

1. Hozzon létre egy **RunSSISPackagePipeline.js** nevű JSON-fájlt a **C:\ADF\RunSSISPackage** mappában a következő tartalommal:

    > [!IMPORTANT]
    > &lt;A fájl mentése előtt cserélje le a mappa nevét, a &gt; &lt; projekt nevét &gt; , a &lt; csomag nevét, valamint a &gt; mappa, a projekt és a csomag nevét a SSIS-katalógusba. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. A folyamat létrehozása: **RunSSISPackagePipeline**, futtassa a **set-AzDataFactoryV2Pipeline** parancsmagot.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Itt látható a minta kimenete:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása
A folyamat futtatásához használja a **meghívó-AzDataFactoryV2Pipeline** parancsmagot. A parancsmag visszaadja a folyamat futásának azonosítóját a későbbi monitorozás céljából.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi PowerShell-szkriptet. Másolja/illessze be az alábbi szkriptet a PowerShell-ablakba, majd nyomja le az Enter billentyűt. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>Trigger létrehozása
Az előző lépésben meghívotta a folyamat igény szerinti meghívását. Létrehozhat egy ütemezett triggert is a folyamat ütemezett futtatásához (óránként, naponta stb.).

1. Hozzon létre egy **MyTrigger.js** nevű JSON-fájlt a **C:\ADF\RunSSISPackage** mappában a következő tartalommal: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. A **Azure PowerShellban** váltson a **C:\ADF\RunSSISPackage** mappára.
3. Futtassa a **set-AzDataFactoryV2Trigger** parancsmagot, amely létrehozza az triggert. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Alapértelmezés szerint az trigger leállított állapotban van. Indítsa el a triggert a **Start-AzDataFactoryV2Trigger** parancsmag futtatásával. 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. A **Get-AzDataFactoryV2Trigger** parancsmag futtatásával ellenőrizze, hogy elindult-e az trigger. 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Futtassa a következő parancsot a következő óra után. Ha például az aktuális időpont 3:25 PM UTC, futtassa a parancsot 4 órakor UTC-kor. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    A következő lekérdezést futtathatja SQL Database SSISDB-adatbázisán a csomag végrehajtásának ellenőrzéséhez. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Következő lépések
A folyamatot a Azure Portal használatával is nyomon követheti. Részletes útmutatásért lásd: [a folyamat figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).