---
title: Az első adatgyár létrehozása (PowerShell)
description: Az oktatóanyag során létrehoz egy minta Azure Data Factory-folyamatot az Azure PowerShell használatával.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: 60fb3a8c1fed8e8005ba0b8f67865a7d39c754d5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392051"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-powershell"></a>Oktatóanyag: Az első Azure data factory létrehozása az Azure PowerShell használatával
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-sablon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg [az adat-előállító Azure Data Factoryvel való létrehozását ismertető rövid útmutatót](../quickstart-create-data-factory-powershell.md).

Ez a cikk azt ismerteti, hogyan hozhatja létre első Azure data factoryját az Azure PowerShell használatával. Ha ezt az oktatóanyagot más eszközök/SDK-k használatával szeretné elvégezni, válassza ki az egyik lehetőséget a legördülő listából.

A jelen oktatóanyagban szereplő folyamat egyetlen tevékenységet tartalmaz: ez a **HDInsight Hive-tevékenység**. A tevékenység egy hive-szkriptet futtat egy Azure HDInsight fürtön, amely a bemeneti adatokat átalakítja a kimeneti adatok előállításához. A folyamat úgy van ütemezve, hogy havonta egyszer fusson a megadott kezdő és befejező időpontok közt. 

> [!NOTE]
> Az oktatóanyagban található adatfolyamat átalakítja a bemeneti adatokat, hogy ezzel kimeneti adatokat hozzon létre. A forrásadattár adatait nem másolja egy céladattárba. Az adatok Azure Data Factory használatával történő másolásának útmutatásáért olvassa el [az adatok Blob Storage-ból SQL Database-be történő másolását ismertető oktatóanyagot](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Egy folyamathoz több tevékenység is tartozhat. Ezenkívül össze is fűzhet két tevékenységet (egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. További tudnivalókért lásd: [Ütemezés és végrehajtás a Data Factoryban](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Olvassa el [Az oktatóanyag áttekintése](data-factory-build-your-first-pipeline.md) című cikket, és hajtsa végre az **előfeltételként** felsorolt lépéseket.
* Kövesse a [How to install and configure Azure PowerShell](/powershell/azure/) (Az Azure PowerShell telepítése és konfigurálása) című cikkben foglalt utasításokat az Azure PowerShell telepítéséhez a számítógépre.
* (elhagyható) Ez a cikk nem tárgyalja az összes Data Factory-parancsmagot. A Data Factory-parancsmagokkal kapcsolatos átfogó dokumentációért tekintse meg a [Data Factory Cmdlet Reference](/powershell/module/az.datafactory) (Data Factory-parancsmagok referenciája) című cikket.

## <a name="create-data-factory"></a>Data factory létrehozása
Ebben a lépésben az Azure PowerShell használatával létrehozza a **FirstDataFactoryPSH** nevű Azure data factoryt. A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Ilyen például a másolási tevékenység, amely adatokat másol a forrásadattárból a céladattárba, és a HDInsight Hive tevékenység, amely egy Hive-szkriptet futtat a bemeneti adatok átalakításához. Ebben a lépésben létrehozzuk a data factoryt.

1. Indítsa el az Azure PowerShellt, és futtassa az alábbi parancsot. Az Azure PowerShellt hagyja megnyitva az oktatóanyag végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell ezeket a parancsokat.
   * Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.
     ```PowerShell
     Connect-AzAccount
     ```    
   * Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.
     ```PowerShell
     Get-AzSubscription  
     ```
   * Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Ennek az előfizetésnek egyeznie kell az Azure Portalon használt előfizetéssel.
     ```PowerShell
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```     
2. Hozzon létre egy Azure-erőforráscsoportot **ADFTutorialResourceGroup** néven a következő parancs futtatásával:
    
    ```PowerShell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    Az oktatóanyag különböző lépései során feltételezzük, hogy az ADFTutorialResourceGroup elnevezésű erőforráscsoportot használja. Ha másik erőforráscsoportot használ, akkor az oktatóanyagban azt használja az ADFTutorialResourceGroup helyett.
3. Futtassa a **New-AzDataFactory** parancsmagot, amely létrehoz egy **FirstDataFactoryPSH** nevű adatelőállítót.

    ```PowerShell
    New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"
    ```
   Vegye figyelembe a következő szempontokat:

* Az Azure Data Factory nevének globálisan egyedinek kell lennie. Ha a **Data factory name “FirstDataFactoryPSH” is not available** (A „FirstDataFactoryPSH” data factory-név nem érhető el) hibaüzenetet kapja, módosítsa a nevet (például: azÖnneveFirstDataFactoryPSH). Használja ezt az ADFTutorialFactoryPSH helyett az oktatóanyag lépéseinek végrehajtása során. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
* Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének/rendszergazdájának kell lennie.
* Az adat-előállító neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá tehető.
* Ha a „**This subscription is not registered to use namespace Microsoft.DataFactory**” (Az előfizetés nem jogosult használni a Microsoft.DataFactory névteret) hibaüzenetet kapja, tegye a következők egyikét, és próbálkozzon újra a közzététellel:

  * Az Azure PowerShellben futtassa az alábbi parancsot a Data Factory-szolgáltató regisztrálásához.

    ```PowerShell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
      Az alábbi parancs futtatásával ellenőrizheti, hogy a Data Factory-szolgáltató regisztrálva van-e:

    ```PowerShell
    Get-AzResourceProvider
    ```
  * Az Azure-előfizetés használatával jelentkezzen be az [Azure Portalra](https://portal.azure.com), és navigáljon egy Data Factory panelre, vagy hozzon létre egy data factoryt az Azure Portalon. Ezzel a művelettel automatikusan regisztrálja a szolgáltatót.

A folyamat létrehozása előtt először létre kell hoznia néhány Data Factory-entitást. Először társított szolgáltatásokat kell létrehoznia, amelyek adattárakat/számítási szolgáltatásokat társítanak az adattárhoz, majd bemeneti és kimeneti adatkészleteket kell meghatároznia, amelyek a társított adattárakban lévő bemeneti/kimeneti adatokat képviselik, végül létrehozhatja a folyamatot egy olyan tevékenységgel, amely ezeket az adatkészleteket használja.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a lépésben az Azure Storage-fiókját és egy igény szerinti Azure HDInsight-fürtöt társít az adat-előállítóhoz. Ebben a példában az Azure Storage-fiók a bemeneti és a kimeneti adatokat tárolja a folyamathoz. A HDInsight társított szolgáltatás a mintában szereplő folyamat tevékenységében meghatározott Hive-szkriptet futtatja. Határozza meg, hogy melyik adattárat/számítási szolgáltatásokat használja a forgatókönyvben, és társítsa ezeket a szolgáltatásokat a data factoryhoz úgy, hogy társított szolgáltatásokat hoz létre.

### <a name="create-azure-storage-linked-service"></a>Azure Storage társított szolgáltatás létrehozása
Ebben a lépésben társítani fogja Azure Storage-fiókját az adat-előállítóhoz. Ugyanazt az Azure Storage-fiókot fogja használni a bemeneti/kimeneti adatok és a HQL-parancsfájl tárolásához.

1. Hozzon létre egy StorageLinkedService.json nevű JSON-fájlt a C:\ADFGetStarted mappában az alábbi tartalommal. Ha még nem létezik, hozza létre az ADFGetStarted mappát.

    ```json
    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "description": "",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }
    ```
    Cserélje le a **fióknevet** az Azure Storage-fiókja nevére, és adja meg az Azure Storage-fiók elérési **kulcsát** . A Storage-hozzáférési kulcs beszerzéséről a Storage- [fiók hozzáférési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md)című témakörben olvashat bővebben.
2. Az Azure PowerShellben váltson az ADFGetStarted mappára.
3. Használhatja a **New-AzDataFactoryLinkedService** parancsmagot, amely létrehoz egy társított szolgáltatást. Ehhez, valamint az oktatóanyagban használt többi Data Factory-parancsmaghoz is meg kell adnia értékeket a *ResourceGroupName* és a *DataFactoryName* paraméterek számára. Azt is megteheti, hogy a **Get-AzDataFactory** használatával beolvas egy **DataFactory** objektumot, és a *ResourceGroupName* és a *DataFactoryName* megadása nélkül adja át az objektumot a parancsmagok minden egyes futtatásakor. Futtassa a következő parancsot a **Get-AzDataFactory** parancsmag kimenetének **$DF** változóhoz való hozzárendeléséhez.

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
4. Most futtassa a **New-AzDataFactoryLinkedService** parancsmagot, amely létrehozza a csatolt **StorageLinkedService** szolgáltatást.

    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```
    Ha nem futtatta a **Get-AzDataFactory** parancsmagot, és hozzárendelte a kimenetet a **$DF** változóhoz, a következőképpen kell megadnia a *ResourceGroupName* és a *DataFactoryName* paraméterek értékeit.

    ```PowerShell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json
    ```
    Ha lezárta Azure PowerShell az oktatóanyag közepén, akkor a következő indításakor futtatnia kell a **Get-AzDataFactory** parancsmagot, Azure PowerShell az oktatóanyag befejezéséhez.

### <a name="create-azure-hdinsight-linked-service"></a>Azure HDInsight társított szolgáltatás létrehozása
Ebben a lépésben egy igény szerinti HDInsight-fürtöt társít a data factoryhoz. A HDInsight-fürtöt a rendszer automatikusan létrehozza a futásidő során, majd törli a feldolgozás befejezését követően, miután egy adott ideig tétlen volt. Igény szerinti HDInsight-fürt helyett saját HDInsight-fürtöt is használhat. További információ: [Compute Linked Services](data-factory-compute-linked-services.md) (Számítási társított szolgáltatás).

1. Hozzon létre egy **HDInsightOnDemandLinkedService**.json nevű JSON-fájlt a **C:\ADFGetStarted** mappában az alábbi tartalommal.

    ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }
    ```
    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

   | Tulajdonság | Leírás |
   |:--- |:--- |
   | clusterSize |Megadja a HDInsight-fürt méretét. |
   | timeToLive |Megadja, hogy a HDInsight-fürt mennyi ideig lehet tétlen, mielőtt törölné a rendszer. |
   | linkedServiceName |Megadja a HDInsight által előállított naplók tárolására szolgáló tárfiókot. |

    Vegye figyelembe a következő szempontokat:

   * A Data Factory létrehoz egy **Linux-alapú** HDInsight-fürtöt a JSON-fájllal. További információkért lásd: [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Igény szerinti HDInsight társított szolgáltatás).
   * Igény szerinti HDInsight-fürt használata helyett **saját HDInsight-fürtöt** is használhat. További információ: [HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) (HDInsight társított szolgáltatás).
   * A HDInsight-fürt létrehoz egy **alapértelmezett tárolót** a JSON-fájlban megadott blob-tárolóban (**linkedServiceName**). A fürt törlésekor a HDInsight nem törli ezt a tárolót. Ez a működésmód szándékos. Igény szerinti HDInsight társított szolgáltatás esetén a rendszer a szeletek feldolgozásakor mindig létrehoz egy HDInsight-fürtöt, kivéve, ha van meglévő élő fürt (**timeToLive**). A fürt automatikusan törlődik a feldolgozás megtörténtekor.

       Ahogy a rendszer egyre több szeletet dolgoz fel, egyre több tároló jelenik meg az Azure Blob Storage-fiókban. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: "ADF **yourdatafactoryname** - **linkedservicename**-datetimestamp". Az Azure Blob Storage-tárolóban lévő tárolók törléséhez használja a [Microsoft Azure Storage Explorer](https://storageexplorer.com/) eszközöket.

     További információkért lásd: [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Igény szerinti HDInsight társított szolgáltatás).
2. Futtassa a **New-AzDataFactoryLinkedService** parancsmagot, amely létrehozza a HDInsightOnDemandLinkedService nevű társított szolgáltatást.
    
    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json
    ```

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre, amelyek a Hive-feldolgozás bemeneti és kimeneti adatait képviselik. Ezek az adatkészletek az oktatóanyag során korábban létrehozott **StorageLinkedService** szolgáltatásra hivatkoznak. A társított szolgáltatás egy Azure Storage-fiókra mutat, az adatkészletek pedig meghatározzák a bemeneti és kimeneti adatokat tartalmazó tárban lévő tárolót, mappát és fájlnevet.

### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása
1. Hozzon létre egy **InputTable.json** nevű JSON-fájlt a **C:\ADFGetStarted** mappában az alábbi tartalommal:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    A JSON-kódrészlet meghatározza az **AzureBlobInput** nevű adatkészletet, amely a bemeneti adatokat jelöli az adatcsatorna egyik tevékenységéhez. Emellett azt is meghatározza, hogy a bemeneti adatok az **adfgetstarted** nevű blobtárolóban és az **inputdata** nevű mappában találhatók.

    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

   | Tulajdonság | Leírás |
   |:--- |:--- |
   | típus |A tulajdonság beállítása AzureBlob, mert az adatok az Azure Blob Storage-tárban találhatók. |
   | linkedServiceName |A korábban létrehozott StorageLinkedService szolgáltatásra hivatkozik. |
   | fileName |Ez a tulajdonság nem kötelező. Ha kihagyja, az összes fájl ki lesz választva a folderPath útvonalról. Ebben az esetben csak az input.log fájl lesz feldolgozva. |
   | típus |A naplófájlok szövegformátumúak, ezért a TextFormat típust használjuk. |
   | columnDelimiter |A naplófájlokban vesszővel (,) vannak elválasztva az oszlopok. |
   | frequency/interval |A frequency (gyakoriság) beállítása Month (Hónap), az interval (időköz) beállítása pedig 1, ami azt jelenti, hogy a bemeneti szeletek havonta érhetők el. |
   | external |Ez a tulajdonság true (igaz) értékre van állítva, ha a bemeneti adatokat nem a Data Factory szolgáltatás hozta létre. |
2. A Data Factory-adathalmaz létrehozásához futtassa az alábbi parancsot az Azure PowerShellben:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\InputTable.json
    ```

### <a name="create-output-dataset"></a>Kimeneti adatkészlet létrehozása
Most a kimeneti adatkészletet hozza létre, amely az Azure Blob Storage-tárban tárolt kimeneti adatokat jelöli.

1. Hozzon létre egy **OutputTable.json** nevű JSON-fájlt a **C:\ADFGetStarted** mappában az alábbi tartalommal:

    ```json
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    A JSON-kódrészlet meghatározza az **AzureBlobOutput** nevű adatkészletet, amely az adatcsatorna egyik tevékenységének kimeneti adatait jelöli. Emellett azt is meghatározza, hogy az eredmények tárolása az **adfgetstarted** nevű blobtárolóban és a **partitioneddata** nevű mappában történik. Az **availability** (rendelkezésre állás) szakasz meghatározza, hogy a kimeneti adatkészlet előállítása havonta történik.
2. A Data Factory-adathalmaz létrehozásához futtassa az alábbi parancsot az Azure PowerShellben:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\OutputTable.json
    ```

## <a name="create-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehozza a **HDInsightHive** tevékenységgel rendelkező első adatcsatornát. A bemeneti szelet havonta érhető el (frequency: Month, interval: 1), a kimeneti szelet előállítása havonta történik, és a tevékenység scheduler (ütemező) tulajdonsága szintén a hónap értékre van állítva. A kimeneti adatkészlet és a tevékenységütemező beállításainak egyezniük kell. Jelenleg a kimeneti adatkészlet vezérli az ütemezést, ezért kimeneti adatkészletet akkor is létre kell hoznia, ha a tevékenység nem állít elő semmilyen kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. Az alábbi JSON-fájlban használt tulajdonságok magyarázata a szakasz végén található.

1. Hozzon létre egy MyFirstPipelinePSH.json nevű JSON-fájlt a C:\ADFGetStarted mappában az alábbi tartalommal:

   > [!IMPORTANT]
   > A JSON-fájlban cserélje a **storageaccountname** kifejezést a tárfiókja nevére.
   >
   >

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```
    A JSON-kódrészletben létrehoz egy folyamatot, amely egyetlen tevékenységből áll, és a tevékenység a Hive használatával dolgozza fel az adatokat egy HDInsight-fürtön.

    A rendszer a **partitionweblogs. HQL** nevű kaptár-parancsfájlt az Azure Storage-fiókban tárolja (a scriptlinkedservice szolgáltatás, az úgynevezett **StorageLinkedService**), valamint a tároló **adfgetstarted** található **script** mappában.

    A **defines** (meghatározza) szakasz meghatározza a futásidő beállításait, amelyek Hive konfigurációs értékekként (például ${hiveconf:inputtable}, ${hiveconf:partitionedtable}) lesznek átadva a Hive-parancsfájlnak.

    A folyamat **start** (kezdés) és **end** (befejezés) tulajdonságai a folyamat aktív időszakát határozzák meg.

    A tevékenység JSON-fájljában meg van határozva, hogy a Hive-parancsfájl a **linkedServiceName** – **HDInsightOnDemandLinkedService** által meghatározott számítási szolgáltatáson fut.

   > [!NOTE]
   > A példában használt JSON-tulajdonságokkal kapcsolatos részletekért lásd „A folyamat JSON-fájlja” szakaszt a [Folyamatok és tevékenységek az Azure Data Factoryban](data-factory-create-pipelines.md) című témakörben.

2. Győződjön meg arról, hogy az **input.log** fájl megjelenik az Azure Blob Storage **adfgetstarted/inputdata** mappájában, és futtassa az alábbi parancsot a folyamat üzembe helyezéséhez. Mivel a **start** (kezdés) és az **end** (befejezés) időpontok múltbeli értékekre vannak beállítva, és az **isPaused** tulajdonság értéke false (hamis), a folyamat (a folyamatban foglalt tevékenység) az üzembe helyezés után azonnal fut.

    ```PowerShell
    New-AzDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
    ```
3. Gratulálunk, sikeresen létrehozta első folyamatát az Azure PowerShell használatával!

## <a name="monitor-pipeline"></a>Folyamat figyelése
Ebben a lépésben az Azure PowerShell használatával figyeli az Azure data factory eseményeit.

1. Futtassa a **Get-AzDataFactory** parancsot, és rendelje hozzá a kimenetet egy **$DF** változóhoz.

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
2. A **Get-AzDataFactorySlice** futtatásával részletes információkat kaphat a **EmpSQLTable** összes szeletéről, amely a folyamat kimeneti táblázata.

    ```PowerShell
    Get-AzDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```
    Megfigyelheti, hogy a StartDateTime itt megadott értéke megegyezik a folyamat JSON-fájljában megadott kezdési idővel. Itt látható a minta kimenete:

    ```PowerShell
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : FirstDataFactoryPSH
    DatasetName       : AzureBlobOutput
    Start             : 7/1/2017 12:00:00 AM
    End               : 7/2/2017 12:00:00 AM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0
    ```
3. A **Get-AzDataFactoryRun** futtatásával lekérheti egy adott szelet tevékenység-futtatásának részleteit.

    ```PowerShell
    Get-AzDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```

    Itt látható a minta kimenete: 

    ```PowerShell
    Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : FirstDataFactoryPSH
    DatasetName         : AzureBlobOutput
    ProcessingStartTime : 12/18/2015 4:50:33 AM
    ProcessingEndTime   : 12/31/9999 11:59:59 PM
    PercentComplete     : 0
    DataSliceStart      : 7/1/2017 12:00:00 AM
    DataSliceEnd        : 7/2/2017 12:00:00 AM
    Status              : AllocatingResources
    Timestamp           : 12/18/2015 4:50:33 AM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : RunSampleHiveActivity
    PipelineName        : MyFirstPipeline
    Type                : Script
    ```
    Futtassa a parancsmagot, amíg a szelet **Ready** (Kész) vagy **Failed** (Sikertelen) állapotú nem lesz. Ha a szelet Ready (Kész) állapotú, a kimeneti adatok blobtárolójának **adfgetstarted** tárolójában ellenőrizze a **partitioneddata** mappát.  Az igény szerinti HDInsight-fürt létrehozása általában eltart egy ideig.

    ![kimeneti adatok](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)

> [!IMPORTANT]
> Az igény szerinti HDInsight-fürt létrehozása általában eltart egy ideig (körülbelül 20 percig). Ezért várhatóan **körülbelül 30 percet** vesz igénybe a folyamat a szelet feldolgozásához.
>
> A szelet sikeres feldolgozásakor a rendszer törli a bemeneti fájlt. Ezért ha újra le szeretné futtatni a szeletet, vagy újra el szeretné végezni az oktatóanyagban foglaltakat, töltse fel a bemeneti fájlt (input.log) az adfgetstarted tároló inputdata mappájába.
>
>

## <a name="summary"></a>Összefoglalás
Az oktatóanyag során létrehozott egy Azure data factoryt, amely egy HDInsight Hadoop-fürtön futtatott Hive-parancsfájllal dolgozza fel az adatokat. Az Azure Portal Data Factory Editor eszközét használta a következő lépések végrehajtásához:

1. Létrehozott egy Azure **data factoryt**.
2. Létrehozott két **társított szolgáltatást**:
   1. Az **Azure Storage** társított szolgáltatást, amely a bemeneti és kimeneti adatokat tároló Azure blob-tárolót társítja a data factoryval.
   2. Az **Azure HDInsight** igény szerinti társított szolgáltatást, amely egy igény szerinti HDInsight Hadoop-fürtöt társít a data factoryval. Az Azure Data Factory létrehoz egy HDInsight Hadoop-fürtöt, amely igény szerint dolgozza fel a bemeneti adatokat és állítja elő a kimeneti adatokat.
3. Létrehozott két **adatkészletet**, amelyek leírják a bemeneti és kimeneti adatokat az adatcsatorna HDInsight Hive-tevékenysége számára.
4. Létrehozott egy **folyamatot** egy **HDInsight Hive**-tevékenységgel.

## <a name="next-steps"></a>Következő lépések
Az oktatóanyag során létrehozott egy folyamatot egy adatátalakítási tevékenységgel (HDInsight-tevékenység), amely Hive-parancsfájlt futtat egy igény szerinti Azure HDInsight-fürtön. Ha tudni szeretné, hogyan használhatja a Másolás tevékenységet az adatok Azure-blobból Azure SQL Database adatbázisba történő másolásához, tekintse meg a következő cikket: [Tutorial: Copy data from an Azure blob to Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Oktatóanyag: adatok másolása Azure-blobból Azure SQL Database adatbázisba).

## <a name="see-also"></a>Lásd még:

| Témakör | Description |
|:--- |:--- |
| [A Data Factory parancsmagjainak leírása](/powershell/module/az.datafactory) |A Data Factory-parancsmagokkal kapcsolatos átfogó dokumentáció. |
| [Pipelines](data-factory-create-pipelines.md) |Ennek a cikknek a segítségével megismerheti a Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját forgatókönyvéhez vagy vállalkozásához. |
| [Adatkészletek](data-factory-create-datasets.md) |Ennek a cikknek a segítségével megismerheti az adatkészleteket az Azure Data Factoryban. |
| [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) |Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. |
| [Folyamatok figyelése és felügyelete a Monitoring App használatával](data-factory-monitor-manage-app.md) |Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management App használatával. |
