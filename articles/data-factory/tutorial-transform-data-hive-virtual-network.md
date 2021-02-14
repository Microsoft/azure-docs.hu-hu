---
title: Adatátalakítás a kaptár használatával az Azure-ban Virtual Network
description: A Azure PowerShell használatával létrehozhat egy Data Factory folyamatot, amely egy Azure-Virtual Network (VNet) található HDInsight-fürtön található kaptár-tevékenységgel alakítja át az Adatátalakítási tevékenységeket.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/22/2018
ms.openlocfilehash: 85f51fd52ce3224b37c27cea6c49a8a386fbea2c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377771"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Azure virtuális hálózaton lévő adatok átalakítása Hive-tevékenység segítségével az Azure Data Factoryben

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben az oktatóanyagban az Azure PowerShell-lel hoz létre egy Data Factory-folyamatot, amely egy Azure virtuális hálózaton (VNET) lévő HDInsight-fürtön futó Hive-tevékenységgel alakítja át az adatokat. Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása 
> * Saját üzemeltetésű integrációs modul készítése és beállítása
> * Társított szolgáltatások készítése és üzembe helyezése
> * Hive-tevékenységet tartalmazó folyamat készítése és üzembe helyezése
> * Folyamat futásának indítása
> * A folyamat futásának monitorozása 
> * A kimenet ellenőrzése 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure Storage-fiók**. Létrehoz egy Hive-szkriptet, és feltölti az Azure Storage-ba. A Hive-szkript kimenetét ebben a Storage-fiókban tárolja a rendszer. A példában a HDInsight-fürt ezt az Azure Storage-fiókot használja elsődleges tárolóként. 
- **Azure Virtual Network.** Ha nem rendelkezik Azure virtuális hálózattal, hozzon létre egyet [ezeket az utasításokat](../virtual-network/quick-create-portal.md) követve. Ebben a példában a HDInsight egy Azure virtuális hálózaton található. Itt látható az Azure virtuális hálózat egy példa konfigurációja. 

    ![Virtuális hálózat létrehozása](media/tutorial-transform-data-using-hive-in-vnet/create-virtual-network.png)
- **HDInsight-fürt.** Hozzon létre egy HDInsight-fürtöt, és csatolja az előző lépésben létrehozott virtuális hálózathoz az [Azure HDInsight Azure virtuális hálózat segítségével történő bővítésével](../hdinsight/hdinsight-plan-virtual-network-deployment.md) kapcsolatos cikk utasításait követve. Itt látható a virtuális hálózaton található HDInsight egy példa konfigurációja. 

    ![HDInsight virtuális hálózaton](media/tutorial-transform-data-using-hive-in-vnet/hdinsight-in-vnet-configuration.png)
- **Azure PowerShell**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-Az-ps) ismertető cikkben szereplő utasításokat.

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Hive-szkript feltöltése Blob Storage-fiókba

1. Hozzon létre egy **hivescript.hql** nevű Hive SQL-fájlt a következő tartalommal:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Az Azure Blob Storage-ban hozzon létre egy **adftutorial** nevű tárolót, ha még nem létezik.
3. Hozzon létre egy **hivescripts** nevű mappát.
4. Töltse fel a **hivescript.hql** fájlt a **hivescripts** almappába.

  

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása


1. Adja meg az erőforráscsoport nevét. Az oktatóanyag során létrehoz majd egy erőforráscsoportot. Ha szeretné, természetesen egy meglévő erőforráscsoportot is használhat. 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```
2. Adja meg az adat-előállító nevét. A névnek globálisan egyedinek kell lennie.

    ```powershell
    $dataFactoryName = "MyDataFactory09142017"
    ```
3. Adja meg a folyamat nevét. 

    ```powershell
    $pipelineName = "MyHivePipeline" # 
    ```
4. Adja meg a saját üzemeltetésű integrációs modul nevét. Egy saját üzemeltetésű integrációs modulra van szükség, amikor az adat-előállítónak egy virtuális hálózaton belüli erőforrásokhoz (pl. Azure SQL-adatbázis) kell hozzáférnie. 
    ```powershell
    $selfHostedIntegrationRuntimeName = "MySelfHostedIR09142017" 
    ```
2. Indítsa el a **PowerShellt**. Az Azure PowerShellt hagyja megnyitva a rövid útmutató végéig. Ha bezárja és újra megnyitja a programot, akkor újra le kell futtatnia a parancsokat. Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

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
3. Hozza létre az ADFTutorialResourceGroup erőforráscsoportot, ha még nincs ilyen az előfizetésben. 

    ```powershell
    New-AzResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Hozza létre az adat-előállítót. 

    ```powershell
     $df = Set-AzDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    A kimenet megtekintéséhez futtassa a következő parancsot: 

    ```powershell
    $df
    ```

## <a name="create-self-hosted-ir"></a>Saját üzemeltetésű integrációs modul létrehozása
Ebben a szakaszban egy saját üzemeltetésű integrációs modult hozhat létre, és társíthatja azt egy Azure-beli virtuális géphez ugyanazon az Azure virtuális hálózaton, amelyen a HDInsight-fürt található.

1. Saját üzemeltetésű integrációs modul létrehozása Használjon egy egyedi nevet, amennyiben már létezne egy integrációs modul ezen a néven.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted
   ```
    Ez a parancs létrehozza a saját üzemeltetésű integrációs modul logikai regisztrációját. 
2. A PowerShell-lel kérje le a hitelesítési kulcsokat a saját üzemeltetésű integrációs modul regisztrálásához. Másolja be az egyik kulcsot a saját üzemeltetésű integrációs modul regisztrálásához.

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName | ConvertTo-Json
   ```

   Itt látható a minta kimenete: 

   ```powershell
   {
       "AuthKey1":  "IR@0000000000000000000000000000000000000=",
       "AuthKey2":  "IR@0000000000000000000000000000000000000="
   }
   ```
    Jegyezze fel az **AuthKey1** értékét idézőjelek nélkül. 
3. Hozzon létre egy Azure-beli virtuális gépet, és csatlakoztassa ahhoz a virtuális hálózathoz, amelyen a HDInsight-fürt található. Részletekért tekintse meg a [virtuális gépek létrehozását](../virtual-network/quick-create-portal.md#create-virtual-machines) ismertető cikket. Csatlakoztassa ezeket egy Azure virtuális hálózathoz. 
4. Az Azure-beli virtuális gépen töltse le a [saját üzemeltetésű integrációs modult](https://www.microsoft.com/download/details.aspx?id=39717). Az előző lépésben megszerzett hitelesítési kulccsal regisztrálja manuálisan a saját üzemeltetésű integrációs modult. 

   ![Integrációs modul regisztrálása](media/tutorial-transform-data-using-hive-in-vnet/register-integration-runtime.png)

   Az integrációs modul sikeres regisztrációja esetén a következő üzenet jelenik meg: ![Sikeres regisztráció](media/tutorial-transform-data-using-hive-in-vnet/registered-successfully.png)

   A csomópont a felhőszolgáltatáshoz való csatlakozásakor a következő oldal jelenik meg: ![Csomópont csatlakoztatva](media/tutorial-transform-data-using-hive-in-vnet/node-is-connected.png)

## <a name="author-linked-services"></a>Társított szolgáltatások létrehozása

Ebben a részben két társított szolgáltatást hoz létre és helyez üzembe:
- Egy Azure Storage-beli társított szolgáltatást, amely egy Azure Storage-fiókot társít az adat-előállítóhoz. Ez a tároló a HDInsight-fürt által használt elsődleges tároló. Ebben az esetben ezt az Azure Storage-fiókot használjuk a Hive-szkript és a szkript kimenetének tárolására is.
- Egy HDInsight társított szolgáltatást. Az Azure Data Factory a Hive-szkriptet erre a HDInsight-fürtre küldi el végrehajtásra.

### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás

Hozzon létre egy JSON-fájlt az előnyben részesített szerkesztővel, másolja a fájlba egy Azure Storage társított szolgáltatás alábbi JSON-definícióját, majd mentse a fájlt **MyStorageLinkedService.json** néven.

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>"
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }  
    }
}
```

Cserélje le az **&lt; accountname &gt; és a &lt; accountkey &gt;** az Azure Storage-fiókjának nevére és kulcsára.

### <a name="hdinsight-linked-service"></a>HDInsight társított szolgáltatás

Hozzon létre egy JSON-fájlt a kívánt szerkesztőprogrammal, másolja a fájlba egy Azure HDInsight társított szolgáltatás következő JSON-definícióját, majd mentse a fájlt **MyHDInsightLinkedService.json** néven.

```
{
  "name": "MyHDInsightLinkedService",
  "properties": {     
      "type": "HDInsight",
      "typeProperties": {
          "clusterUri": "https://<clustername>.azurehdinsight.net",
          "userName": "<username>",
          "password": {
            "value": "<password>",
            "type": "SecureString"
          },
          "linkedServiceName": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }
  }
}
```

Frissítse a következő tulajdonságok értékeit a társított szolgáltatás definíciójában:

- **Felhasználónév**. A fürt bejelentkezési felhasználójának neve, amely a fürt létrehozásakor lett megadva. 
- **jelszó**. A felhasználó jelszava.
- **clusterUri**. Adja meg a HDInsight-fürt URL-címét a következő formátumban: `https://<clustername>.azurehdinsight.net` .  A cikk azt feltételezi, hogy a fürtöt az interneten éri el. A fürthöz például a `https://clustername.azurehdinsight.net` címen tud csatlakozni. Ez a cím a nyilvános átjárót használja, amely nem érhető el, ha az internetes hozzáférés korlátozva lett hálózati biztonsági csoportokkal (NSG-kkel) vagy felhasználó által definiált útvonalakkal (UDR-ekkel). Ahhoz, hogy a Data Factory feladatokat küldhessen az Azure-beli virtuális hálózaton található HDInsight-fürtre, az Azure-beli virtuális hálózatot úgy kell konfigurálni, hogy az URL-cím feloldható legyen a HDInsight által használt átjáró magánhálózati IP-címére.

  1. Az Azure Portalról nyissa meg a virtuális hálózatot, amelyen a HDInsight található. Nyissa meg a hálózati adaptert, amelynek a neve a következő karakterlánccal kezdődik: `nic-gateway-0`. Jegyezze fel a magánhálózati IP-címét. Például: 10.6.0.15. 
  2. Ha az Azure virtuális hálózaton van DNS-kiszolgáló, frissítse a DNS-rekordot, hogy a HDInsight-fürt URL-címe (`https://<clustername>.azurehdinsight.net`) feloldható legyen a `10.6.0.15` címre. Ez az ajánlott eljárás. Ha nincs DNS-kiszolgáló az Azure virtuális hálózaton, akkor átmeneti megoldást jelenthet, ha szerkeszti az összes saját üzemeltetésű integrációs modulként regisztrált virtuális gép listáját tartalmazó gazdafájlt (C:\Windows\System32\drivers\etc) egy, a következőhöz hasonló bejegyzés hozzáadásával: 
  
        `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
A PowerShell-ben váltson arra a mappára, ahol létrehozta a JSON-fájlokat, és futtassa a következő parancsot a társított szolgáltatások üzembe helyezéséhez: 

1. A PowerShell-ben váltson arra a mappára, ahol létrehozta a JSON-fájlokat.
2. Futtassa a következő parancsot egy Azure Storage társított szolgáltatás létrehozásához. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
3. Futtassa a következő parancsot egy Azure HDInsight társított szolgáltatás létrehozásához. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyHDInsightLinkedService" -File "MyHDInsightLinkedService.json"
    ```

## <a name="author-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy Hive-tevékenységgel rendelkező új folyamatot. A tevékenység egy Hive-szkript végrehajtásával ad vissza adatokat egy minta táblából, és menti azokat egy megadott elérési útra. Hozzon létre egy JSON-fájlt a kívánt szerkesztőprogrammal, másolja a fájlba egy folyamatdefiníció alábbi JSON-definícióját, majd mentse a fájlt **MyHivePipeline.json** néven.


```json
{
  "name": "MyHivePipeline",
  "properties": {
    "activities": [
      {
        "name": "MyHiveActivity",
        "type": "HDInsightHive",
        "linkedServiceName": {
            "referenceName": "MyHDILinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "scriptPath": "adftutorial\\hivescripts\\hivescript.hql",
          "getDebugInfo": "Failure",
          "defines": {           
            "Output": "wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/"
          },
          "scriptLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}

```

Vegye figyelembe a következő szempontokat:

- A **scriptPath** annak a Hive-szkriptnek az elérési útjára mutat az Azure Storage-fiókon, amelyet a MyStorageLinkedService szolgáltatáshoz használt. Az elérési út megkülönbözteti a kis- és nagybetűket.
- Az **Output** egy, a Hive-szkriptben használt argumentum. Használja a `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` formátumot, hogy egy létező mappára mutasson az Azure Storage-ban. Az elérési út megkülönbözteti a kis- és nagybetűket. 

Váltson arra a mappára, ahol létrehozta a JSON-fájlokat, és futtassa a következő parancsot a folyamat üzembe helyezéséhez: 


```powershell
Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MyHivePipeline.json"
```

## <a name="start-the-pipeline"></a>A folyamat elindítása 

1. Folyamat futásának indítása Így megőrizheti a folyamat futásának azonosítóját későbbi monitorozás céljából.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
   ```
2. A folyamatfuttatás állapotának a befejezésig való folyamatos ellenőrzéséig futtassa az alábbi szkriptet.

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }
    
    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

   Itt látható a példa futtatás kimenete:

    ```json
    Pipeline run status: In Progress
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 000000000-0000-0000-000000000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            :
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    :
    DurationInMs      :
    Status            : InProgress
    Error             :
    
    Pipeline ' MyHivePipeline' run finished. Result:
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 0000000-0000-0000-0000-000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            : {logLocation, clusterInUse, jobId, ExecutionProgress...}
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    : 9/18/2017 6:59:16 AM
    DurationInMs      : 63636
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "logLocation": "wasbs://adfjobs@adfv2samplestor.blob.core.windows.net/HiveQueryJobs/000000000-0000-47c3-9b28-1cdc7f3f2ba2/18_09_2017_06_58_18_023/Status"
    "clusterInUse": "https://adfv2HivePrivate.azurehdinsight.net"
    "jobId": "job_1505387997356_0024"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "MySelfhostedIR"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MyHiveActivity"
    ```
4. Ellenőrizze az `outputfolder` mappában a Hive-lekérdezés eredményeként létrejövő új fájlt, amelynek a következő példa kimenethez hasonlónak kell lennie: 

   ```
   8 en-US SCH-i500 California
   23 en-US Incredible Pennsylvania
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   246 en-US SCH-i500 District Of Columbia
   246 en-US SCH-i500 District Of Columbia
   ```

## <a name="next-steps"></a>Következő lépések
Az oktatóanyagban az alábbi lépéseket hajtotta végre: 

> [!div class="checklist"]
> * Adat-előállító létrehozása 
> * Saját üzemeltetésű integrációs modul készítése és beállítása
> * Társított szolgáltatások készítése és üzembe helyezése
> * Hive-tevékenységet tartalmazó folyamat készítése és üzembe helyezése
> * Folyamat futásának indítása
> * A folyamat futásának monitorozása 
> * A kimenet ellenőrzése 

Folytassa a következő oktatóanyaggal, amelyben az adatok Azure Spark-fürtök használatával való átalakítását ismerheti meg:

> [!div class="nextstepaction"]
>[Elágaztatási és láncolási Data Factory-folyamvezérlés](tutorial-control-flow.md)