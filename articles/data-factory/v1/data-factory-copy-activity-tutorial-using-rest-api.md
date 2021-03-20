---
title: 'Oktatóanyag: Azure Data Factory folyamat létrehozása a REST API használatával '
description: Ebben az oktatóanyagban a REST API használatával hoz létre egy másolási tevékenységgel rendelkező Azure Data Factory folyamatot az adatok Azure Blob Storage-ból Azure SQL Databaseba való másolásához.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7488834252dcd4e231c2d91a1435838befe7b1d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377023"
---
# <a name="tutorial-use-rest-api-to-create-an-azure-data-factory-pipeline-to-copy-data"></a>Oktatóanyag: REST API használata adatmásoló Azure Data Factory-folyamat létrehozásához 
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [másolási tevékenység oktatóanyagát](../quickstart-create-data-factory-rest-api.md). 

Ebből a cikkből megtudhatja, hogyan hozhat létre egy adatfeldolgozót egy olyan folyamattal, amely egy Azure Blob Storage-ból másolja a Azure SQL Databaseba az REST API használatával. Ha még csak ismerkedik az Azure Data Factory szolgáltatással, olvassa el a [Bevezetés az Azure Data Factory használatába](data-factory-introduction.md) című cikket az oktatóanyag elvégzése előtt.   

Az oktatóanyag segítségével egyetlen tevékenységgel (másolási tevékenységgel) rendelkező folyamatot hozhat létre. A másolási tevékenység adatokat másol a forrásadattárból egy támogatott fogadó adattárba. A forrásként és fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats). A tevékenységet egy globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és skálázható módon másolja az adatokat a különböző adattárak között. További információ a másolási tevékenységről: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md).

Egy folyamathoz több tevékenység is tartozhat. Ezenkívül össze is fűzhet két tevékenységet (egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. További információ az [egy folyamaton belüli több tevékenységről](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) szóló témakörben található.

> [!NOTE]
> Ez a cikk nem tárgyalja a Data Factoryhoz használható REST API egészét. A Data Factory-parancsmagokkal kapcsolatos átfogó dokumentációért tekintse meg a [Data Factory REST API Reference](/rest/api/datafactory/) (Data Factory REST API referenciája) című cikket.
>  
> Az oktatóanyagban található adatfeldolgozási folyamat adatokat másol egy forrásadattárból egy céladattárba. Az adatok Azure Data Factory használatával történő átalakításának útmutatásáért olvassa el [az adatok Hadoop-fürt segítségével történő átalakítására szolgáló folyamat létrehozását ismertető oktatóanyagot](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Tekintse meg [Az oktatóanyag áttekintése](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című cikket, és hajtsa végre az **előfeltételként** felsorolt lépéseket.
* Telepítse gépére a [Curl](https://curl.haxx.se/dlwiz/) eszközt. A Curl eszköz a REST-parancsokkal együtt az adat-előállító létrehozására használható. 
* Az [ebben a cikkben](../../active-directory/develop/howto-create-service-principal-portal.md) szereplő utasításokat követve végezze el a következőket: 
  1. Hozzon létre egy **ADFCopyTutorialApp** nevű webalkalmazást az Azure Active Directoryban.
  2. Szerezze be az **ügyfél-azonosítót** és a **titkos kulcsot**. 
  3. **Bérlő azonosítójának** beolvasása. 
  4. Rendelje az **ADFCopyTutorialApp** alkalmazáshoz a **Data Factory közreműködője** szerepkört.  
* Telepítse a [Azure PowerShell](/powershell/azure/).  
* Indítsa el a **PowerShellt**, és végezze el az alábbi lépéseket. Az Azure PowerShellt hagyja megnyitva az oktatóanyag végéig. Ha bezárja és újra megnyitja a programot, akkor újra le kell futtatnia a parancsokat.
  
  1. Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.
    
     ```PowerShell 
     Connect-AzAccount
     ```   
  2. Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.

     ```PowerShell     
     Get-AzSubscription
     ``` 
  3. Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Cserélje le az **&lt; NameOfAzureSubscription** - &gt; t az Azure-előfizetés nevére. 
     
     ```PowerShell
     Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
     ```
  4. Hozzon létre egy **ADFTutorialResourceGroup** nevű Azure-erőforráscsoportot a következő parancs futtatásával a PowerShellben:  

     ```PowerShell     
      New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
     ```
     
      Ha az erőforráscsoport már létezik, adja meg, hogy frissítse azt a rendszer (Y), vagy hagyja változatlanul (N). 
     
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
    "name": "ADFCopyTutorialDF",  
    "location": "WestUS"
}  
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Az **accountname** és az **accountkey** kifejezés helyére írja be Azure Storage-tárfiókja nevére, illetve kulcsát. A Storage-hozzáférési kulcs beszerzéséről a Storage- [fiók hozzáférési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md)című témakörben olvashat bővebben.

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

A JSON tulajdonságokról további részleteket tartalmaz az [Azure Storage társított szolgáltatás](data-factory-azure-blob-connector.md#azure-storage-linked-service) című cikk.

### <a name="azuresqllinkedservicejson"></a>azuresqllinkedservice.jsbekapcsolva
> [!IMPORTANT]
> A **kiszolgálónév**, a **databasename**, a **Felhasználónév** és a **jelszó** helyére írja be a kiszolgáló nevét, az SQL Database nevét, a felhasználói fiókot és a jelszót a fiókhoz.  
> 
>

```JSON
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

A JSON tulajdonságokról további részleteket tartalmaz az [Azure SQL társított szolgáltatás](data-factory-azure-sql-connector.md#linked-service-properties) című cikk.

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "AzureStorageLinkedService",
    "typeProperties": {
      "folderPath": "adftutorial/",
      "fileName": "emp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ","
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

| Tulajdonság | Leírás |
|:--- |:--- |
| típus | A tulajdonság beállításának értéke **AzureBlob**, mert az adatok egy Azure Blob Storage-tárban találhatók. |
| linkedServiceName | A korábban létrehozott **AzureStorageLinkedService** szolgáltatásra hivatkozik. |
| folderPath | A bemeneti blobokat tartalmazó **blobtárolót** és **mappát** adja meg. Ebben az oktatóanyagban az adftutorial a blobtároló és a folder a gyökérmappa. | 
| fileName | Ez a tulajdonság nem kötelező. Ha kihagyja, a rendszer a folderPath elérési úton található összes fájlt kiválasztja. Ebben az oktatóanyagban az **emp.txt** a fileName értéke, így a rendszer csak ezt a fájlt használja a feldolgozáshoz. |
| formátum -> típus |A bemeneti fájl szöveges formátumú, ezért a **TextFormat** értéket használjuk. |
| columnDelimiter | A bemeneti fájlban **vesszővel (`,`)** vannak elválasztva az oszlopok. |
| frequency/interval | A frequency (gyakoriság) beállítása **Hour** (Óra), az interval (időköz) beállítása pedig **1**, ami azt jelenti, hogy a bemeneti szeletek **óránként** érhetők el. Vagyis a Data Factory szolgáltatás óránként keres bemeneti adatokat a megadott blobtároló (**adftutorial**) gyökérmappájában. A szolgáltatás a folyamat kezdő és befejező időpontja közti időszakban – és nem azon kívül – keres adatokat.  |
| external | Ez a tulajdonság a **true** (igaz) értékre van állítva, ha az adatokat nem ez a folyamat hozta létre. Az oktatóanyagban használt bemeneti adatok az emp.txt fájlban találhatók, amelyet nem ez a folyamat hoz létre, ezért ezt a tulajdonságot true (igaz) értékre állítottuk. |

Ezekről a JSON-tulajdonságokról további tudnivalók az [Azure Blob-összekötőről](data-factory-azure-blob-connector.md#dataset-properties) szóló cikkben olvashatók.

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "emp"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

| Tulajdonság | Leírás |
|:--- |:--- |
| típus | A Type tulajdonság a **tulajdonsága azuresqltable** értékre van állítva, mert az adatmásolása egy Azure SQL Database egy táblába történik. |
| linkedServiceName | A korábban létrehozott **AzureSqlLinkedService** szolgáltatásra hivatkozik. |
| tableName | Azt a **táblát** határozza meg, amelybe a rendszer az adatokat másolja. | 
| frequency/interval | A frequency (gyakoriság) értéke **Hour** (Óra), az interval (időköz) értéke pedig **1**, azaz a rendszer a kimeneti szeleteket **óránként** állítja elő a folyamat kezdő és befejező időpontja közti időszakban (és nem azon kívül).  |

Az adatbázis emp táblájában három oszlop van – **ID**, **FirstName** és **LastName**. Az ID azonosítóoszlop, ezért itt csak a **FirstName** és **LastName** tulajdonságokat kell megadnia.

További információ ezekről a JSON-tulajdonságokról: [Azure SQL-összekötő](data-factory-azure-sql-connector.md#dataset-properties).

### <a name="pipelinejson"></a>pipeline.json

```JSON
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "description": "Push Regional Effectiveness Campaign data to Azure SQL Database",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2017-05-11T00:00:00Z",
    "end": "2017-05-12T00:00:00Z"
  }
}
```

Vegye figyelembe a következő szempontokat:

- A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa****Copy** értékre van beállítva. További információ a másolási tevékenységről: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md). A Data Factory megoldásaiban használhatja az [adatátalakítási tevékenységeket](data-factory-data-transformation-activities.md) is.
- A tevékenység bemenetének beállítása **AzureBlobInput**, a kimeneté pedig **AzureSqlOutput**. 
- A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva. A másolási tevékenység által forrásként és fogadóként támogatott adattárak teljes listájáért lásd: [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Egy forrásként/fogadóként támogatott konkrét adattár használatával kapcsolatos útmutatóért kattintson a tábla adott hivatkozására.  
 
A **start** (kezdés) tulajdonság értékét cserélje az aktuális, az **end** (befejezés) tulajdonság értékét pedig a következő napra. Azt is megteheti, hogy a dátum-időpont paraméternek csak a dátum részét adja meg, az időpont részét pedig kihagyja. Megadhatja például a „2017-07-10” értéket, amely a következőnek felel meg: „2017-07-10T00:00:00Z”
 
Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](https://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2016-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk. 
 
Ha nem ad meg értéket a **Befejezés** tulajdonsághoz, a rendszer a következőt számítja ki: "**Start + 48 óra**". A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz.
 
Az előző példában 24 adatszelet van, mert a rendszer óránként létrehoz egy adatszeletet.

A folyamathoz tartozó definíció JSON-tulajdonságainak leírásáért lásd: [folyamatok létrehozása](data-factory-create-pipelines.md). A másolási tevékenységhez tartozó definíció JSON-tulajdonságainak leírásáért lásd: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md). A BlobSource által támogatott JSON-tulajdonságok leírásáért lásd: [Azure Blob-összekötő](data-factory-azure-blob-connector.md). Az SqlSink által támogatott JSON-tulajdonságok leírása az [Azure SQL Database-összekötő](data-factory-azure-sql-connector.md) című cikkben található.

## <a name="set-global-variables"></a>Globális változók beállítása
Adja meg saját értékeit, majd az Azure PowerShellben hajtsa végre a következő parancsokat:

> [!IMPORTANT]
> Az ügyfél-azonosító, a titkos ügyfélkód, a bérlőazonosító és az előfizetés-azonosító beszerzésével kapcsolatban olvassa el az [Előfeltételek](#prerequisites) című fejezetet.   
> 
> 

```JSON
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
```

A használt adat-előállító nevének frissítése után futtassa le a következő parancsot: 

```
$adf = "ADFCopyTutorialDF"
```

## <a name="authenticate-with-aad"></a>Hitelesítés az AAD segítségével
Az Azure Active Directory (AAD) segítségével végzett hitelesítéshez futtassa a következő parancsokat: 

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken) 
```

## <a name="create-data-factory"></a>Data factory létrehozása
Ebben a lépésben egy **ADFCopyTutorialDF** nevű Azure-adatelőállítót fog létrehozni. A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Ilyen például a másolási tevékenység, amely adatokat másol a forrásból a célként megadott adattárba, vagy a HDInsight Hive tevékenység, amely Hive-parancsprogram futtatásával átalakítja a bemeneti adatokat, így biztosít védelmet a kimeneti adatoknak. Az adat-előállító létrehozásához futtassa az alábbi parancsokat: 

1. Rendelje a parancsot a **cmd** nevű változóhoz. 
   
    > [!IMPORTANT]
    > Ellenőrizze, hogy itt is azt az adatelőállító-nevet adja-e meg, amit a **datafactory.json** fájlban (ADFCopyTutorialDF). 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@datafactory.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF0411?api-version=2015-10-01};
    ```
2. Az **Invoke-Command** használatával futtassa a parancsot.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Tekintse meg az eredményeket. Ha az adat-előállító sikeresen létrejött, az **eredmények** között meg kell jelennie az adat-előállítóhoz tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.  
   
    ```
    Write-Host $results
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure Data Factory nevének globálisan egyedinek kell lennie. Ha az eredmények között a következő hibaüzenet jelenik meg: a **"ADFCopyTutorialDF" nevű adatgyár nem érhető el**, hajtsa végre az alábbi lépéseket:  
  
  1. Módosítsa a nevet (például yournameADFCopyTutorialDF) a **datafactory.json** fájlban.
  2. Az első parancsnál, amikor a **$cmd** változóhoz értéket rendel, cserélje le az ADFCopyTutorialDF értéket az új névre, és futtassa le így a parancsot. 
  3. A következő két parancs lefuttatásával hívja meg a REST API-t, amely létrehozza az adat-előállítót, és kiírja a művelet eredményeit. 
     
     A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
* Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének/rendszergazdájának kell lennie.
* Az adat-előállító neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá tehető.
* Ha a „**This subscription is not registered to use namespace Microsoft.DataFactory**” (Az előfizetés nem jogosult használni a Microsoft.DataFactory névteret) hibaüzenetet kapja, tegye a következők egyikét, és próbálkozzon újra a közzététellel: 
  
  * Az Azure PowerShellben futtassa az alábbi parancsot a Data Factory-szolgáltató regisztrálásához. 

    ```PowerShell    
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Az alábbi parancs futtatásával ellenőrizheti, hogy a Data Factory-szolgáltató regisztrálva van-e. 
    
    ```PowerShell
    Get-AzResourceProvider
    ```
  * Az Azure-előfizetés használatával jelentkezzen be az [Azure Portalra](https://portal.azure.com), és navigáljon egy Data Factory panelre, vagy hozzon létre egy data factoryt az Azure Portalon. Ezzel a művelettel automatikusan regisztrálja a szolgáltatót.

A folyamat létrehozása előtt először létre kell hoznia néhány Data Factory-entitást. Először hozza létre a társított szolgáltatást, amely összekapcsolja a forrásként és célként használt adattárakat az Ön adattárával. Majd definiálja a bemeneti és kimeneti adatkészleteket, amelyek adatként szolgálnak a társított adattárakban. Végül hozza létre az adatcsatornát egy tevékenységgel, amely ezeket az adatkészleteket használja.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben az oktatóanyagban nem használunk számítási szolgáltatásokat (például Azure HDInsight vagy Azure Data Lake Analytics). Csak kétféle típusú adattárat használunk: Azure Storage (forrás) és Azure SQL Database (cél). Ezért két társított szolgáltatást fog létrehozni AzureStorageLinkedService és AzureSqlLinkedService néven (típus: AzureStorage és AzureSqlDatabase).  

Az AzureStorageLinkedService az Azure Storage-fiókot társítja az adat-előállítóval. Ebben a tárfiókban hozta létre a tárolót, és ebbe töltötte fel adatokat az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként.   

A AzureSqlLinkedService a Azure SQL Database az adatelőállítóra mutató hivatkozásokat. A blobtárolóból másolt adatokat a rendszer ebben az adatbázisban tárolja. Az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként létrehozta az emp táblát az adatbázisban.  

### <a name="create-azure-storage-linked-service"></a>Azure Storage társított szolgáltatás létrehozása
Ebben a lépésben társítja az Azure Storage-fiókot az adat-előállítójához. Ebben a szakaszban megadhatja az Azure-tárfiók nevét és kulcsát. Az Azure Storage társított szolgáltatás definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Storage társított szolgáltatás](data-factory-azure-blob-connector.md#azure-storage-linked-service) című szakaszt.  

1. Rendelje a parancsot a **cmd** nevű változóhoz. 

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Az **Invoke-Command** használatával futtassa a parancsot.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Tekintse meg az eredményeket. Ha a társított szolgáltatás sikeresen létrejött, az **eredmények** között meg kell jelennie a társított szolgáltatáshoz tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.

    ```PowerShell   
    Write-Host $results
    ```

### <a name="create-azure-sql-linked-service"></a>Azure SQL társított szolgáltatás létrehozása
Ebben a lépésben összekapcsolja Azure SQL Databaset az adatai-gyárhoz. Ebben a szakaszban a logikai SQL-kiszolgáló nevét, az adatbázis nevét, a felhasználónevet és a felhasználói jelszót kell megadnia. Az Azure SQL társított szolgáltatás definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure SQL társított szolgáltatás](data-factory-azure-sql-connector.md#linked-service-properties) című szakaszt.

1. Rendelje a parancsot a **cmd** nevű változóhoz. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azuresqllinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
    ```
2. Az **Invoke-Command** használatával futtassa a parancsot.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Tekintse meg az eredményeket. Ha a társított szolgáltatás sikeresen létrejött, az **eredmények** között meg kell jelennie a társított szolgáltatáshoz tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.
   
    ```PowerShell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Adatkészletek létrehozása
Az előző lépésben társított szolgáltatásokat hozott létre az Azure Storage-fiók összekapcsolásához és a Azure SQL Databaseához. Ebben a lépésben két adatkészletet határoz meg – AzureBlobInput és AzureSqlOutput néven –, amelyek az AzureStorageLinkedService és az AzureSqlLinkedService szolgáltatás által hivatkozott bemeneti és kimeneti adatokat jelölik.

Az Azure Storage társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure Storage-fiók csatlakoztatásához használ. A bemeneti blob adatkészlete (AzureBlobInput) pedig a tárolót és a bemeneti adatokat tartalmazó mappát határozza meg.  

Hasonlóképpen, a Azure SQL Database társított szolgáltatás határozza meg azt a kapcsolati karakterláncot, amelyet a Data Factory szolgáltatás futás közben használ a Azure SQL Databasehoz való kapcsolódáshoz. Az SQL-tábla kimeneti adatkészlete (OututDataset) határozza meg azt az adatbázistáblát, amelybe a rendszer a blobtárolóból származó adatokat másolja. 

### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása
Ebben a lépésben hozza létre az AzureBlobInput nevű adatkészletet, amely az AzureStorageLinkedService társított szolgáltatás által hivatkozott Azure Storage blobtárolójának (adftutorial) gyökérmappájában található blobfájlra mutat (emp.txt). Ha nem ad meg értéket a fájlnévnek (vagy kihagyja azt), a rendszer a bemeneti mappában található összes blob adatát a célhelyre másolja. Ebben az oktatóanyagban a fileName értékét adja meg. 

1. Rendelje a parancsot a **cmd** nevű változóhoz. 

    ```PowerSHell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. Az **Invoke-Command** használatával futtassa a parancsot.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Tekintse meg az eredményeket. Ha az adatkészlet sikeresen létrejött, az **eredmények** között meg kell jelennie az adatkészlethez tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.
   
    ```PowerShell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Kimeneti adatkészlet létrehozása
A Azure SQL Database társított szolgáltatás határozza meg azt a kapcsolati karakterláncot, amelyet a Data Factory szolgáltatás futás közben használ a Azure SQL Databasehoz való kapcsolódáshoz. Az ebben a lépésben létrehozandó SQL-tábla kimeneti adatkészlete (OututDataset) határozza meg azt az adatbázistáblát, amelybe a rendszer a blobtárolóból származó adatokat másolja.

1. Rendelje a parancsot a **cmd** nevű változóhoz.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
    ```
2. Az **Invoke-Command** használatával futtassa a parancsot.
    
    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Tekintse meg az eredményeket. Ha az adatkészlet sikeresen létrejött, az **eredmények** között meg kell jelennie az adatkészlethez tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.
   
    ```PowerShell
    Write-Host $results
    ``` 

## <a name="create-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy **másolási tevékenységgel** rendelkező folyamatot, amely bemenetként az **AzureBlobInput**, kimenetként az **AzureSqlOutput** adatkészletet használja.

Jelenleg a kimeneti adatkészlet határozza meg az ütemezést. Az oktatóanyagban a kimeneti adatkészletet úgy konfiguráljuk, hogy a szeletek létrehozása óránként történjen meg. A folyamat kezdő és befejező időpontja között egy nap, azaz 24 óra telik el. Ezért a folyamat a kimeneti adatkészletből 24 szeletet hoz létre. 

1. Rendelje a parancsot a **cmd** nevű változóhoz.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. Az **Invoke-Command** használatával futtassa a parancsot.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Tekintse meg az eredményeket. Ha az adatkészlet sikeresen létrejött, az **eredmények** között meg kell jelennie az adatkészlethez tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.  

    ```PowerShell   
    Write-Host $results
    ```

**Gratulálunk!** Sikeresen létrehozott egy Azure-beli adatelőállítót egy olyan folyamattal, amely átmásolja az Azure Blob Storageról Azure SQL Databaseba.

## <a name="monitor-pipeline"></a>Folyamat figyelése
Ebben a lépésben a Data Factory REST API segítségével figyelheti meg az adatcsatorna által készített szeleteket.

```PowerShell
$ds ="AzureSqlOutput"
```

> [!IMPORTANT] 
> Ügyeljen rá, hogy a következő parancsban megadott kezdési és befejezési időpontok megegyezzenek a folyamat kezdési és befejezési idejével. 

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=2017-05-11T00%3a00%3a00.0000000Z"&"end=2017-05-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
```

```PowerShell
$results2 = Invoke-Command -scriptblock $cmd;
```

```PowerShell
IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

Futtassa addig az Invoke-Command parancsot, illetve a következő parancsot, amíg egy szelet **Kész** vagy **Sikertelen** állapotba nem kerül. Ha a szelet Ready (kész) állapotú, a kimeneti adatokat a Azure SQL Databaseban található **EMP** táblában találja. 

Az egyes szeletek esetében a forrásfájl két sorát másolja a rendszer a Azure SQL Database EMP táblájába. Ezért az összes szelet sikeres feldolgozását követően (azaz amikor az összes szelet Kész állapotban van) 24 új bejegyzésnek kell szerepelnie az emp táblában. 

## <a name="summary"></a>Összefoglalás
Ebben az oktatóanyagban egy Azure-beli adatfeldolgozó létrehozásához REST APIt használt, amely az adatok Azure-blobból Azure SQL Databaseba való másolását használta. Az oktatóanyag során a következő főbb lépéseket végezte el:  

1. Létrehozott egy Azure **data factoryt**.
2. **Társított szolgáltatásokat** hozott létre:
   1. Egy Azure Storage társított szolgáltatást a bemeneti adatokat tároló Azure Storage-fiók csatlakoztatásához.     
   2. Egy Azure SQL társított szolgáltatás a kimeneti adatokat tároló adatbázis összekapcsolásához. 
3. **Adatkészleteket** hozott létre, amelyek az adatcsatorna bemeneti és kimeneti adatait írják le.
4. Létrehozott egy **folyamatot** egy Másolási tevékenységgel, ahol a BlobSource a forrás, az SqlSink pedig a fogadó. 

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban az Azure Blob Storage-t forrás adattárként használta, és a másolási művelet során Azure SQL Database célként megadott adattárként. Az alábbi táblázatban a másolási tevékenység által támogatott forrásadattárak és céladattárak listája látható: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

A táblázatban lévő adattárak hivatkozására kattintva megismerheti az adattárakba és az adattárakból történő adatmásolás módszereit.
