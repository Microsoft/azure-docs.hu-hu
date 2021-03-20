---
title: Rövid útmutató – Stream Analytics-feladatok létrehozása Azure PowerShell használatával
description: Ez a rövid útmutató azt ismerteti, hogyan használható a Azure PowerShell modul a Azure Stream Analytics feladatok üzembe helyezéséhez és futtatásához.
author: enkrumah
ms.author: ebnkruma
ms.date: 12/20/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 377abef31dbc4364f37161194923bbf74d272d80
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012291"
---
# <a name="quickstart-create-a-stream-analytics-job-using-azure-powershell"></a>Gyors útmutató: Stream Analytics-feladatok létrehozása Azure PowerShell használatával

A Azure PowerShell modul Azure-erőforrások létrehozásához és kezeléséhez használható PowerShell-parancsmagokkal vagy parancsfájlokkal. Ez az útmutató azt mutatja be, hogy hogyan lehet egy Azure Stream Analytics-feladatot létrehozni és futtatni az Azure PowerShell-modul használatával.

A példában szereplő művelet beolvassa az adatfolyam-adatokat egy IoT Hub eszközről. A bemeneti adatokat a málna PI online szimulátor hozza létre. Ezután a Stream Analytics feladatot a Stream Analytics lekérdezési nyelv segítségével alakítja át az üzeneteket a 27 °-nál nagyobb hőmérsékletű üzenetek szűréséhez. Végezetül az eredményül kapott kimeneti eseményeket a blob Storage-ban lévő fájlba írja.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)

* Ehhez a rövid útmutatóhoz a Azure PowerShell modul szükséges. Ahhoz, hogy megtudja, melyik verzió van telepítve a helyi gépen, futtassa a `Get-Module -ListAvailable Az` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

* Egyes IoT Hub műveleteket a Azure PowerShell nem támogat, és az Azure CLI 2.0.70 vagy újabb verziójával, valamint az Azure CLI-hez készült IoT-bővítménnyel kell befejezni. [Telepítse az Azure CLI](/cli/azure/install-azure-cli) -t, és használja `az extension add --name azure-iot` az IoT-bővítmény telepítéséhez.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és adja meg az Azure-beli hitelesítő adatait az előugró böngészőben:

```powershell
# Connect to your Azure account
Connect-AzAccount
```

Ha egynél több előfizetéssel rendelkezik, válassza ki a rövid útmutatóhoz használni kívánt előfizetést a következő parancsmagok futtatásával. Ügyeljen rá, hogy a `<your subscription name>` helyett saját előfizetésének a nevét adja meg:

```powershell
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A Stream Analytics-feladat létrehozása előtt készítse elő azokat az adatokat, amelyek a feladat bemenetét fogják képezni.

A következő Azure CLI-kódrészlet számos parancsot tartalmaz a feladathoz szükséges bemeneti adatok előkészítéséhez. Tekintse át az egyes szakaszokat a kód értelmezése céljából.

1. A PowerShell-ablakban futtassa az az [login](/cli/azure/authenticate-azure-cli) parancsot az Azure-fiókjába való bejelentkezéshez.

    Ha sikeresen bejelentkezik, az Azure CLI az előfizetések listáját adja vissza. Másolja ki a rövid útmutatóhoz használt előfizetést, és futtassa az az [Account set](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription) parancsot az előfizetés kiválasztásához. Válassza ki ugyanazt az előfizetést, amelyet az előző szakaszban kiválasztott a PowerShell-lel. Ügyeljen rá, hogy `<your subscription name>` az előfizetése nevére cserélje le.

    ```azurecli
    az login

    az account set --subscription "<your subscription>"
    ```

2. Hozzon létre egy IoT Hub az az [IoT hub Create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) paranccsal. Ez a példa egy **MyASAIoTHub** nevű IoT hub hoz létre. Mivel a IoT Hub nevek egyediek, a saját IoT Hub nevét kell megadnia. Állítsa az SKU-t az F1-re, hogy az ingyenes szintet használja, ha az előfizetése elérhető. Ha nem, válassza a következő legalacsonyabb szintet.

    ```azurecli
    az iot hub create --name "<your IoT Hub name>" --resource-group $resourceGroup --sku S1
    ```

    Miután létrejött az IoT hub, szerezze be a IoT Hub kapcsolati karakterláncot az az [IoT hub show-kapcsolat-string](/cli/azure/iot/hub) parancs használatával. Másolja a teljes kapcsolódási karakterláncot, és mentse azt, amikor hozzáadja a IoT Hub a Stream Analytics feladathoz.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

3. Az az [iothub Device-Identity Create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) paranccsal adhat hozzá IoT hub eszközt. Ez a példa egy **MyASAIoTDevice** nevű eszközt hoz létre.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

4. Szerezze be az eszköz kapcsolati karakterláncát az az [IOT hub Device-Identity show-kapcsolat-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) parancs használatával. Másolja a teljes kapcsolódási karakterláncot, és mentse azt a málna PI szimulátor létrehozásakor.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Példa a kimenetre:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-blob-storage"></a>BLOB Storage létrehozása

Az alábbi Azure PowerShell-kód a parancsok használatával hozza létre a feladatok kimenetéhez használt BLOB Storage-tárolót. Tekintse át az egyes szakaszokat a kód értelmezése céljából.

1. Hozzon létre egy szabványos általános célú Storage-fiókot a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) parancsmag használatával.  Ez a példa létrehoz egy **myasaquickstartstorage** nevű Storage-fiókot a helyileg redundáns tárolással (LRS) és a blob titkosításával (alapértelmezés szerint engedélyezve van).

2. Kérje le a `$storageAccount.Context` nevű tárfiókkörnyezetet, amely meghatározza a használni kívánt tárfiókot. Ha a tárfiókok használatával történő munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat.

3. Hozzon létre egy Storage-tárolót a [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)használatával.

4. Másolja ki a kód által leképezett tárolási kulcsot, és mentse a kulcsot a folyamatos átviteli feladatok kimenetének későbbi létrehozásához.

    ```powershell
    $storageAccountName = "myasaquickstartstorage"
    $storageAccount = New-AzStorageAccount `
      -ResourceGroupName $resourceGroup `
      -Name $storageAccountName `
      -Location $location `
      -SkuName Standard_LRS `
      -Kind Storage

    $ctx = $storageAccount.Context
    $containerName = "container1"

    New-AzStorageContainer `
      -Name $containerName `
      -Context $ctx

    $storageAccountKey = (Get-AzStorageAccountKey `
      -ResourceGroupName $resourceGroup `
      -Name $storageAccountName).Value[0]

    Write-Host "The <storage account key> placeholder needs to be replaced in your output json files with this key value:"
    Write-Host $storageAccountKey -ForegroundColor Cyan
    ```

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Hozzon létre egy Stream Analytics feladatot a [New-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/new-azstreamanalyticsjob) parancsmaggal. Ez a parancsmag a feladatok nevét, az erőforráscsoport nevét és a feladatdefiníció paraméterként való megadását végzi. A feladat neve bármilyen, a feladatot azonosító rövid név lehet. Csak alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhat, és 3 – 63 karakter hosszúnak kell lennie. A feladat definíciója egy JSON-fájl, amely a feladat létrehozásához szükséges tulajdonságokat tartalmazza. Hozzon létre a helyi gépén egy `JobDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat:

```json
{
  "location":"WestUS2",
  "properties":{
    "sku":{
      "name":"standard"
    },
    "eventsOutOfOrderPolicy":"adjust",
    "eventsOutOfOrderMaxDelayInSeconds":10,
    "compatibilityLevel": 1.1
  }
}
```

Ezután futtassa a `New-AzStreamAnalyticsJob` parancsmagot. Cserélje le a változó értékét arra `jobDefinitionFile` az elérési útra, ahol a FELADATDEFINÍCIÓ JSON-fájlját mentette.

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force
```

## <a name="configure-input-to-the-job"></a>A feladat bemenetének konfigurálása

Adjon hozzá egy bemenetet a feladathoz a [New-AzStreamAnalyticsInput](/powershell/module/az.streamanalytics/new-azstreamanalyticsinput) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat bemenetének a neve, az erőforráscsoport neve, és a feladat bemenetének definíciója adható meg paraméterként. A feladat bemenetének definíciója egy JSON-fájl, amely a feladat bemenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ebben a példában egy blob Storage-t hoz létre bemenetként.

Hozzon létre a helyi gépén egy `JobInputDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. Győződjön meg arról, hogy az értéket lecseréli a `accesspolicykey` `SharedAccessKey` IoT hub az előző szakaszban mentett kapcsolódási karakterlánc egy részével.

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Devices/IotHubs",
            "properties": {
                "iotHubNamespace": "MyASAIoTHub",
                "sharedAccessPolicyName": "iothubowner",
                "sharedAccessPolicyKey": "accesspolicykey",
                "endpoint": "messages/events",
                "consumerGroupName": "$Default"
                }
        },
        "compression": {
            "type": "None"
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "IoTHubInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Ezután futtassa a `New-AzStreamAnalyticsInput` parancsmagot, ügyeljen arra, hogy a változó értékét cserélje le arra `jobDefinitionFile` az elérési útra, ahol a feladathoz megadott bemeneti definíció JSON-fájlját mentette.

```powershell
$jobInputName = "IoTHubInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName
```

## <a name="configure-output-to-the-job"></a>A feladat kimenetének konfigurálása

Adja hozzá a kimenetet a feladatokhoz a [New-AzStreamAnalyticsOutput](/powershell/module/az.streamanalytics/new-azstreamanalyticsoutput) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat kimenetének a neve, az erőforráscsoport neve, és a feladat kimenetének definíciója adható meg paraméterként. A feladat kimenetének definíciója egy JSON-fájl, amely a feladat kimenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ez a példa egy Blob Storage-tárolót használ kimenetként.

Hozzon létre a helyi gépén egy `JobOutputDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. Ügyeljen arra, hogy az `accountKey` értékét lecserélje a tárfiókja hozzáférési kulcsára, amelyet a $storageAccountKey értéke ad meg.

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "asaquickstartstorage",
                      "accountKey": "<storage account key>"
                    }
                ],
                "container": "container1",
                "pathPattern": "output/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "BlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Ezután futtassa a `New-AzStreamAnalyticsOutput` parancsmagot. Ne felejtse el lecserélni a `jobOutputDefinitionFile` változó értékét arra az elérési útra, ahol a feladat kimenetét definiáló JSON-fájl található.

```powershell
$jobOutputName = "BlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force
```

## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

Adja hozzá a feladatot a [New-AzStreamAnalyticsTransformation](/powershell/module/az.streamanalytics/new-azstreamanalyticstransformation) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat transzformációjának a neve, az erőforráscsoport neve, és a feladat transzformációjának definíciója adható meg paraméterként. Hozzon létre a helyi gépén egy `JobTransformationDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. A JSON-fájl tartalmaz egy query (lekérdezés) paramétert, amellyel definiálható a transzformációs lekérdezés:

```json
{
    "name":"MyTransformation",
    "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",
    "properties":{
        "streamingUnits":1,
        "script":null,
        "query":" SELECT * INTO BlobOutput FROM IoTHubInput HAVING Temperature > 27"
    }
}
```

Ezután futtassa a `New-AzStreamAnalyticsTransformation` parancsmagot. Ügyeljen arra, hogy a változó értékét cserélje le arra `jobTransformationDefinitionFile` az elérési útra, ahol a feladatütemezés-DEFINÍCIÓ JSON-fájlját mentette.

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="run-the-iot-simulator"></a>A IoT Simulator futtatása

1. Nyissa meg a [málna PI Azure IoT online szimulátort](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. A 15. sorban található helyőrzőt cserélje le az előző szakaszban mentett teljes Azure-IoT Hub eszköz-összekapcsolási sztringre.

3. Kattintson a **Futtatás** elemre. A kimenetnek meg kell jelenítenie az érzékelő adatait és a IoT Hub küldött üzeneteket.

    ![Online Raspberry Pi Azure IoT-szimulátor](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

Indítsa el a feladatot a [Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) parancsmag használatával. Ennél a parancsmagnál a feladat neve, az erőforráscsoport neve, a feladat kimenetének indítási módja, és a kezdés ideje adható meg paraméterként. Az `OutputStartMode` által elfogadott értékek: `JobStartTime`, `CustomTime` és `LastOutputEventTime`. A PowerShell-dokumentáció [paraméterekkel](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) foglalkozó szakaszában talál további információt arról, hogy ezek az értékek mit jelentenek.

A következő parancsmag futtatása a `True` kimenetet adja vissza, ha a feladat elindult. A Storage-tárolóban létrejön egy kimeneti mappa, amely az átalakított adatokat tartalmazza.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, törölheti az erőforráscsoportot, a folyamatos átviteli feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha később is szeretné használni a feladatot, akkor nem kell törölnie, hanem elég, ha leállítja. Ha nem kívánja tovább használni ezt a feladatot, törölje az ebben a rövid útmutatóban létrehozott összes erőforrást a következő parancsmag futtatásával:

```powershell
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics-feladatot helyezett üzembe a PowerShell használatával. A Stream Analytics-feladatokat az [Azure Portallal](stream-analytics-quick-create-portal.md) és a [Visual Studióval](stream-analytics-quick-create-vs.md) is üzembe helyezheti.

Az egyéb bemeneti források beállításával és a valós idejű észlelés végrehajtásával kapcsolatos információkért olvassa el az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Valós idejű csalásészlelés az Azure Stream Analytics használatával](stream-analytics-real-time-fraud-detection.md)