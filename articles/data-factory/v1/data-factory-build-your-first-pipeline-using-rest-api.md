---
title: Az első adatgyár létrehozása (REST)
description: Az oktatóanyag során egy minta Azure Data Factory-adatcsatornát fogunk létrehozni a Data Factory REST API-val.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: tutorial
ms.date: 11/01/2017
ms.openlocfilehash: eb9ce65985521fb3f82032f04aec62538a157639
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785462"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>Oktatóanyag: Az első data factory létrehozása a Data Factory REST API használatával
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-sablon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>


> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg [az adat-előállító Azure Data Factoryvel való létrehozását ismertető rövid útmutatót](../quickstart-create-data-factory-rest-api.md).

Ebből a cikkből megtudhatja, hogyan hozza létre első Azure data factoryját a Data Factory REST API segítségével. Ha ezt az oktatóanyagot más eszközök/SDK-k használatával szeretné elvégezni, válassza ki az egyik lehetőséget a legördülő listából.

A jelen oktatóanyagban szereplő folyamat egyetlen tevékenységet tartalmaz: ez a **HDInsight Hive-tevékenység**. A tevékenység egy hive-szkriptet futtat egy Azure HDInsight fürtön, amely a bemeneti adatokat átalakítja a kimeneti adatok előállításához. A folyamat úgy van ütemezve, hogy havonta egyszer fusson a megadott kezdő és befejező időpontok közt.

> [!NOTE]
> Ez a cikk nem tárgyalja a teljes REST API-t. A REST API átfogó dokumentációjáért lásd: [A Data Factory REST API-jának leírása](/rest/api/datafactory/).
> 
> Egy folyamathoz több tevékenység is tartozhat. Ezenkívül össze is fűzhet két tevékenységet (egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. További tudnivalókért lásd: [Ütemezés és végrehajtás a Data Factoryban](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).


## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Olvassa el [Az oktatóanyag áttekintése](data-factory-build-your-first-pipeline.md) című cikket, és hajtsa végre az **előfeltételként** felsorolt lépéseket.
* Telepítse gépére a [Curl](https://curl.haxx.se/dlwiz/) eszközt. A Curl eszköz a REST-parancsokkal együtt az adat-előállító létrehozására használható.
* Az [ebben a cikkben](../../active-directory/develop/howto-create-service-principal-portal.md) szereplő utasításokat követve végezze el a következőket:
  1. Hozzon létre egy **ADFGetStartedApp** nevű webalkalmazást az Azure Active Directoryban.
  2. Szerezze be az **ügyfél-azonosítót** és a **titkos kulcsot**.
  3. **Bérlő azonosítójának** beolvasása.
  4. Rendelje az **ADFGetStartedApp** alkalmazáshoz a **Data Factory közreműködője** szerepkört.
* Telepítse a [Azure PowerShell](/powershell/azure/).
* Indítsa el a **PowerShellt**, és futtassa az alábbi parancsot. Az Azure PowerShellt hagyja megnyitva az oktatóanyag végéig. Ha bezárja és újra megnyitja a programot, akkor újra le kell futtatnia a parancsokat.
  1. Futtassa a **AzAccount** , és írja be a Azure Portalba való bejelentkezéshez használt felhasználónevet és jelszót.
  2. A **Get-AzSubscription** futtatásával megtekintheti a fiókhoz tartozó összes előfizetést.
  3. A **Get-AzSubscription-SubscriptionName NameOfAzureSubscription futtatása | A set-AzContext** elem kiválasztásával válassza ki a használni kívánt előfizetést. A **NameOfAzureSubscription** helyére írja be Azure-előfizetése nevét.
* Hozzon létre egy **ADFTutorialResourceGroup** nevű Azure-erőforráscsoportot a következő parancs futtatásával a PowerShellben:

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

   Az oktatóanyag különböző lépései során feltételezzük, hogy az ADFTutorialResourceGroup elnevezésű erőforráscsoportot használja. Ha másik erőforráscsoportot használ, használja ehelyett saját erőforráscsoportja nevét, amikor az oktatóanyag az ADFTutorialResourceGroup csoportra utal.

## <a name="create-json-definitions"></a>JSON-definíciók létrehozása
Hozza létre a következő JSON-fájlokat abban a mappában, ahol a curl.exe is található.

### <a name="datafactoryjson"></a>datafactory.json
> [!IMPORTANT]
> Globálisan egyedi nevet kell használnia, ezért érdemes lehet előtagot/utótagot adnia az ADFCopyTutorialDF névhez, hogy az egyedivé váljon.
>
>

```JSON
{
    "name": "FirstDataFactoryREST",
    "location": "WestUS"
}
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Az **accountname** és az **accountkey** kifejezés helyére írja be Azure Storage-tárfiókja nevére, illetve kulcsát. A Storage-hozzáférési kulcs beszerzéséről a Storage- [fiók hozzáférési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md)című témakörben olvashat bővebben.
>
>

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.json

```JSON
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

| Tulajdonság | Leírás |
|:--- |:--- |
| clusterSize |A HDInsight-fürt mérete. |
| timeToLive |Megadja, hogy a HDInsight-fürt mennyi ideig lehet tétlen, mielőtt törölné a rendszer. |
| linkedServiceName |Megadja a HDInsight által előállított naplók tárolására szolgáló tárfiókot. |

Vegye figyelembe a következő szempontokat:

* A Data Factory létrehoz egy **Linux-alapú** HDInsight-fürtöt a fenti JSON-fájllal. További információkért lásd: [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Igény szerinti HDInsight társított szolgáltatás).
* Igény szerinti HDInsight-fürt használata helyett **saját HDInsight-fürtöt** is használhat. További információ: [HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) (HDInsight társított szolgáltatás).
* A HDInsight-fürt létrehoz egy **alapértelmezett tárolót** a JSON-fájlban megadott blob-tárolóban (**linkedServiceName**). A fürt törlésekor a HDInsight nem törli ezt a tárolót. Ez a működésmód szándékos. Igény szerinti HDInsight társított szolgáltatás esetén a rendszer a szeletek feldolgozásakor mindig létrehoz egy HDInsight-fürtöt, kivéve, ha van meglévő élő fürt (**timeToLive**). Ha befejeződött a feldolgozás, a rendszer törli a fürtöt.

    Ahogy a rendszer egyre több szeletet dolgoz fel, egyre több tároló jelenik meg az Azure Blob Storage-fiókban. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: "ADF **yourdatafactoryname** - **linkedservicename**-datetimestamp". Az Azure Blob Storage-tárolóban lévő tárolók törléséhez használja a [Microsoft Azure Storage Explorer](https://storageexplorer.com/) eszközöket.

További információkért lásd: [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Igény szerinti HDInsight társított szolgáltatás).

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
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
| columnDelimiter |A naplófájlokban a vessző karakter (,) szolgál az oszlopok elválasztására. |
| frequency/interval |A frequency (gyakoriság) beállítása Month (Hónap), az interval (időköz) beállítása pedig 1, ami azt jelenti, hogy a bemeneti szeletek havonta érhetők el. |
| external |Ez a tulajdonság true (igaz) értékre van állítva, ha a bemeneti adatokat nem a Data Factory szolgáltatás hozta létre. |

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
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

### <a name="pipelinejson"></a>pipeline.json
> [!IMPORTANT]
> Cserélje a **storageaccountname** kifejezést Azure Storage-fiókja nevére.
>
>

```JSON
{
    "name": "MyFirstPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                "scriptLinkedService": "AzureStorageLinkedService",
                "defines": {
                    "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                    "partitionedtable": "wasb://adfgetstarted@<storageaccountname>t.blob.core.windows.net/partitioneddata"
                }
            },
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
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
        }],
        "start": "2017-07-10T00:00:00Z",
        "end": "2017-07-11T00:00:00Z",
        "isPaused": false
    }
}
```

A JSON-kódrészletben létrehoz egy adatcsatornát, amely egyetlen tevékenységből áll, és a tevékenység a Hive használatával dolgozza fel az adatokat egy HDInsight-fürtön.

A **partitionweblogs.hql** Hive-parancsfájl tárolása az Azure Storage-fiókban (az **StorageLinkedService** nevű scriptLinkedService szolgáltatás által megadva), és az **adfgetstarted** tároló **script** mappájában történik.

A **defines** szakasz meghatározza a futásidő beállításait, amelyek Hive konfigurációs értékekként (például ${hiveconf:inputtable}, ${hiveconf:partitionedtable}) lesznek átadva a Hive-parancsprogramnak.

A folyamat **start** (kezdés) és **end** (befejezés) tulajdonságai a folyamat aktív időszakát határozzák meg.

A tevékenység JSON-fájljában meg van határozva, hogy a Hive-parancsfájl a **linkedServiceName** – **HDInsightOnDemandLinkedService** által meghatározott számítási szolgáltatáson fut.

> [!NOTE]
> Az előző példában használt JSON-tulajdonságokkal kapcsolatos részletekért lásd „A folyamat JSON-fájlja” szakaszt a [Folyamatok és tevékenységek az Azure Data Factoryban](data-factory-create-pipelines.md) című témakörben.
>
>

## <a name="set-global-variables"></a>Globális változók beállítása
Adja meg saját értékeit, majd az Azure PowerShellben hajtsa végre a következő parancsokat:

> [!IMPORTANT]
> Az ügyfél-azonosító, a titkos ügyfélkód, a bérlőazonosító és az előfizetés-azonosító beszerzésével kapcsolatban olvassa el az [Előfeltételek](#prerequisites) című fejezetet.
>
>

```powershell
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
$adf = "FirstDataFactoryREST"
```


## <a name="authenticate-with-aad"></a>Hitelesítés az AAD segítségével

```powershell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken)
```


## <a name="create-data-factory"></a>Data factory létrehozása
Ebben a lépésben egy **FirstDataFactoryREST** nevű Azure-adatelőállítót fog létrehozni. A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Például a másolási tevékenység, amely adatokat másol a forrásadattárból a céladattárba, és a HDInsight Hive-tevékenység, amely Hive-parancsfájlt futtat az adatok átalakításához. Az adat-előállító létrehozásához futtassa az alábbi parancsokat:

1. Rendelje a parancsot a **cmd** nevű változóhoz.

    Ellenőrizze, hogy itt is azt az adatelőállító-nevet adja-e meg, amit a **datafactory.json** fájlban (ADFCopyTutorialDF).

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@datafactory.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
    ```
2. Az **Invoke-Command** használatával futtassa a parancsot.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Tekintse meg az eredményeket. Ha az adat-előállító sikeresen létrejött, az **eredmények** között meg kell jelennie az adat-előállítóhoz tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.

    ```powershell
    Write-Host $results
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure Data Factory nevének globálisan egyedinek kell lennie. Ha az eredményeknél a következő hibaüzenet jelenik meg: **A(z) „FirstDataFactoryREST” Data factory nem érhető el**, végezze el a következő lépéseket:
  1. Módosítsa a nevet (például yournameFirstDataFactoryREST) a **datafactory.json** fájlban. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
  2. Az első parancsnál, amikor a **$cmd** változóhoz értéket rendel, cserélje le a FirstDataFactoryREST értéket az új névre, és futtassa le így a parancsot.
  3. A következő két parancs lefuttatásával hívja meg a REST API-t, amely létrehozza az adat-előállítót, és kiírja a művelet eredményeit.
* Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének/rendszergazdájának kell lennie.
* Az adat-előállító neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá tehető.
* Ha a „**This subscription is not registered to use namespace Microsoft.DataFactory**” (Az előfizetés nem jogosult használni a Microsoft.DataFactory névteret) hibaüzenetet kapja, tegye a következők egyikét, és próbálkozzon újra a közzététellel:

  * Az Azure PowerShellben futtassa az alábbi parancsot a Data Factory-szolgáltató regisztrálásához.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

      Az alábbi parancs futtatásával ellenőrizheti, hogy a Data Factory-szolgáltató regisztrálva van-e:
    ```powershell
    Get-AzResourceProvider
    ```
  * Az Azure-előfizetés használatával jelentkezzen be az [Azure Portalra](https://portal.azure.com), és navigáljon egy Data Factory panelre, vagy hozzon létre egy data factoryt az Azure Portalon. Ezzel a művelettel automatikusan regisztrálja a szolgáltatót.

A folyamat létrehozása előtt először létre kell hoznia néhány Data Factory-entitást. Először társított szolgáltatásokat kell létrehoznia, amelyek adattárakat/számítási szolgáltatásokat társítanak az adattárhoz, majd bemeneti és kimeneti adatkészleteket kell meghatároznia, amelyek a társított adattárakban lévő adatokat képviselik.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a lépésben az Azure Storage-fiókját és egy igény szerinti Azure HDInsight-fürtöt társít az adat-előállítóhoz. Ebben a példában az Azure Storage-fiók a bemeneti és a kimeneti adatokat tárolja a folyamathoz. A HDInsight társított szolgáltatás a mintában szereplő folyamat tevékenységében meghatározott Hive-szkriptet futtatja.

### <a name="create-azure-storage-linked-service"></a>Azure Storage társított szolgáltatás létrehozása
Ebben a lépésben társítani fogja Azure Storage-fiókját az adat-előállítóhoz. Ebben az oktatóanyagban ugyanazt az Azure Storage-fiókot fogja használni a bemeneti/kimeneti adatok és a HQL-parancsfájl tárolásához.

1. Rendelje a parancsot a **cmd** nevű változóhoz.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Az **Invoke-Command** használatával futtassa a parancsot.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Tekintse meg az eredményeket. Ha a társított szolgáltatás sikeresen létrejött, az **eredmények** között meg kell jelennie a társított szolgáltatáshoz tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.

    ```powershell
    Write-Host $results
    ```

### <a name="create-azure-hdinsight-linked-service"></a>Azure HDInsight társított szolgáltatás létrehozása
Ebben a lépésben egy igény szerinti HDInsight-fürtöt társít a data factoryhoz. A HDInsight-fürtöt a rendszer automatikusan létrehozza a futásidő során, majd törli a feldolgozás befejezését követően, miután egy adott ideig tétlen volt. Igény szerinti HDInsight-fürt helyett saját HDInsight-fürtöt is használhat. További információ: [Compute Linked Services](data-factory-compute-linked-services.md) (Számítási társított szolgáltatás).

1. Rendelje a parancsot a **cmd** nevű változóhoz.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
    ```
2. Az **Invoke-Command** használatával futtassa a parancsot.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Tekintse meg az eredményeket. Ha a társított szolgáltatás sikeresen létrejött, az **eredmények** között meg kell jelennie a társított szolgáltatáshoz tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.

    ```powershell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre, amelyek a Hive-feldolgozás bemeneti és kimeneti adatait képviselik. Ezek az adatkészletek az oktatóanyag során korábban létrehozott **StorageLinkedService** szolgáltatásra hivatkoznak. A társított szolgáltatás egy Azure Storage-fiókra mutat, az adatkészletek pedig meghatározzák a bemeneti és kimeneti adatokat tartalmazó tárban lévő tárolót, mappát és fájlnevet.

### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása
Ebben a lépésben létrehozza a bemeneti adatkészletet, amely az Azure Blob Storage-fiókban tárolt bemeneti adatokat jelöli.

1. Rendelje a parancsot a **cmd** nevű változóhoz.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. Az **Invoke-Command** használatával futtassa a parancsot.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Tekintse meg az eredményeket. Ha az adatkészlet sikeresen létrejött, az **eredmények** között meg kell jelennie az adatkészlethez tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.

    ```powershell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Kimeneti adatkészlet létrehozása
Ebben a lépésben létrehozza a kimeneti adatkészletet, amely az Azure Blob Storage-fiókban tárolt kimeneti adatokat jelöli.

1. Rendelje a parancsot a **cmd** nevű változóhoz.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
    ```
2. Az **Invoke-Command** használatával futtassa a parancsot.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Tekintse meg az eredményeket. Ha az adatkészlet sikeresen létrejött, az **eredmények** között meg kell jelennie az adatkészlethez tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.

    ```powershell
    Write-Host $results
    ```

## <a name="create-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehozza a **HDInsightHive** tevékenységgel rendelkező első adatcsatornát. A bemeneti szelet havonta érhető el (frequency: Month, interval: 1), a kimeneti szelet előállítása havonta történik, és a tevékenység scheduler (ütemező) tulajdonsága szintén a hónap értékre van állítva. A kimeneti adatkészlet és a tevékenységütemező beállításainak egyezniük kell. Jelenleg a kimeneti adatkészlet vezérli az ütemezést, ezért kimeneti adatkészletet akkor is létre kell hoznia, ha a tevékenység nem állít elő semmilyen kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását.

Győződjön meg arról, hogy az **input.log** fájl megjelenik az Azure Blob Storage **adfgetstarted/inputdata** mappájában, és futtassa az alábbi parancsot a folyamat üzembe helyezéséhez. Mivel a **start** (kezdés) és az **end** (befejezés) időpontok múltbeli értékekre vannak beállítva, és az **isPaused** tulajdonság értéke false (hamis), a folyamat (a folyamatban foglalt tevékenység) az üzembe helyezés után azonnal fut.

1. Rendelje a parancsot a **cmd** nevű változóhoz.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. Az **Invoke-Command** használatával futtassa a parancsot.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Tekintse meg az eredményeket. Ha az adatkészlet sikeresen létrejött, az **eredmények** között meg kell jelennie az adatkészlethez tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.

    ```powershell
    Write-Host $results
    ```
4. Gratulálunk, sikeresen létrehozta első folyamatát az Azure PowerShell használatával!

## <a name="monitor-pipeline"></a>Folyamat figyelése
Ebben a lépésben a Data Factory REST API segítségével figyelheti meg az adatcsatorna által készített szeleteket.

```powershell
$ds ="AzureBlobOutput"

$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

$results2 = Invoke-Command -scriptblock $cmd;

IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

> [!IMPORTANT]
> Az igény szerinti HDInsight-fürt létrehozása általában eltart egy ideig (körülbelül 20 percig). Ezért várhatóan **körülbelül 30 percet** vesz igénybe a folyamat a szelet feldolgozásához.
>
>

Futtassa addig az Invoke-Command parancsot, illetve a következő parancsot, amíg a szelet **Kész** vagy **Sikertelen** állapotba nem kerül. Ha a szelet Ready (Kész) állapotú, a kimeneti adatok blobtárolójának **adfgetstarted** tárolójában ellenőrizze a **partitioneddata** mappát.  Az igény szerinti HDInsight-fürt létrehozása általában hosszabb időt vesz igénybe.

![kimeneti adatok](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [!IMPORTANT]
> A szelet sikeres feldolgozásakor a rendszer törli a bemeneti fájlt. Ezért ha újra le szeretné futtatni a szeletet, vagy újra el szeretné végezni az oktatóanyagban foglaltakat, töltse fel a bemeneti fájlt (input.log) az adfgetstarted tároló inputdata mappájába.
>
>

A szeletek figyelésére és a felmerülő problémák megoldására az Azure Portalt is használhatja. További információk: [Monitor pipelines using Azure portal](data-factory-monitor-manage-pipelines.md) (Adatcsatornák figyelése az Azure Portal használatával).

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
| Témakör | Leírás |
|:--- |:--- |
| [Data Factory REST API referenciája](/rest/api/datafactory/) |A Data Factory-parancsmagokkal kapcsolatos átfogó dokumentáció. |
| [Pipelines](data-factory-create-pipelines.md) |Ennek a cikknek a segítségével megismerheti a Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját forgatókönyvéhez vagy vállalkozásához. |
| [Adatkészletek](data-factory-create-datasets.md) |Ennek a cikknek a segítségével megismerheti az adatkészleteket az Azure Data Factoryban. |
| [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) |Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. |
| [Folyamatok figyelése és felügyelete a Monitoring App használatával](data-factory-monitor-manage-app.md) |Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management App használatával. |
