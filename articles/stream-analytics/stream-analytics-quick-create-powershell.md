---
title: Rövid útmutató – Stream Analytics feladat létrehozása a Azure PowerShell
description: Ez a rövid útmutató azt mutatja be, hogyan használható Azure PowerShell modul egy Azure Stream Analytics üzembe helyezésére és futtatására.
author: enkrumah
ms.author: ebnkruma
ms.date: 12/20/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 193c8b1f47960b9b42c61a9b0e394e5ebedf7bf7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867582"
---
# <a name="quickstart-create-a-stream-analytics-job-using-azure-powershell"></a>Rövid útmutató: Stream Analytics feladat létrehozása a Azure PowerShell

A Azure PowerShell modul Azure-erőforrások létrehozására és kezelésére szolgál PowerShell-parancsmagok vagy -szkriptek használatával. Ez az útmutató azt mutatja be, hogy hogyan lehet egy Azure Stream Analytics-feladatot létrehozni és futtatni az Azure PowerShell-modul használatával.

A példa feladat streamelési adatokat olvas be egy IoT Hub eszközről. A bemeneti adatokat egy Online Raspberry Pi-szimulátor generálja. Ezután a Stream Analytics feladat átalakítja az adatokat a Stream Analytics lekérdezési nyelv használatával a 27°-nál magasabb hőmérsékletű üzenetek szűréséhez. Végül az eredményül kapott kimeneti eseményeket egy blobtárolóban található fájlba írja.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)

* Ehhez a rövid útmutatóhoz szükség van Azure PowerShell modulra. Ahhoz, hogy megtudja, melyik verzió van telepítve a helyi gépen, futtassa a `Get-Module -ListAvailable Az` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

* Egyes IoT Hub műveleteket a Azure PowerShell nem támogat, és az Azure CLI 2.0.70-es vagy újabb verziójával, valamint az Azure CLI IoT-bővítményének használatával kell végrehajtani. [Telepítse az Azure CLI-t,](/cli/azure/install-azure-cli) és `az extension add --name azure-iot` a használatával telepítse az IoT-bővítményt.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe az paranccsal, és adja meg Azure-beli hitelesítő adatait az `Connect-AzAccount` előugró böngészőben:

```powershell
# Connect to your Azure account
Connect-AzAccount
```

Ha több előfizetéssel rendelkezik, az alábbi parancsmagok futtatásával válassza ki a rövid útmutatóhoz használni kívánt előfizetést. Ügyeljen rá, hogy a `<your subscription name>` helyett saját előfizetésének a nevét adja meg:

```powershell
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup használatával.](/powershell/module/az.resources/new-azresourcegroup) Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A Stream Analytics-feladat létrehozása előtt készítse elő azokat az adatokat, amelyek a feladat bemenetét fogják képezni.

A következő Azure CLI-kódblokk számos parancsot végez a feladathoz szükséges bemeneti adatok előkészítéséhez. Tekintse át az egyes szakaszokat a kód értelmezése céljából.

1. A PowerShell-ablakban futtassa [az az login](/cli/azure/authenticate-azure-cli) parancsot az Azure-fiókjába való bejelentkezéshez.

    Ha sikeresen bejelentkezik, az Azure CLI visszaadja az előfizetések listáját. Másolja ki a rövid útmutatóhoz használt előfizetést, és futtassa [az az account set](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription) parancsot az előfizetés kiválasztásához. Válassza ki az előző szakaszban kiválasztott előfizetést a PowerShell használatával. Cserélje le a `<your subscription name>` helyére az előfizetése nevét.

    ```azurecli
    az login

    az account set --subscription "<your subscription>"
    ```

2. Hozzon létre IoT Hub [az az iot hub create paranccsal.](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) Ez a példa egy **MyASAIoTHub** nevű IoT Hub hoz létre. Mivel IoT Hub egyedi nevek, saját nevet kell IoT Hub neve. Állítsa a termékváltozatot F1-re, hogy az ingyenes szintet használja, ha az elérhető az előfizetésében. Ha nem, válassza a következő legalacsonyabb szintet.

    ```azurecli
    az iot hub create --name "<your IoT Hub name>" --resource-group $resourceGroup --sku S1
    ```

    Az IoT Hub létrehozása után az [az iot hub show-connection-string](/cli/azure/iot/hub) paranccsal IoT Hub le a kapcsolati sztringet. Másolja ki a teljes kapcsolati sztringet, és mentse a fájlba, amikor hozzáadja a IoT Hub a bemeneti Stream Analytics feladathoz.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

3. Adjon hozzá egy eszközt a IoT Hub [az iothub device-identity create paranccsal.](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) Ez a példa létrehoz egy **MyASAIoTDevice nevű eszközt.**

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

4. Az az [iot hub device-identity show-connection-string paranccsal](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_show_connection_string) szerezze be az eszköz kapcsolati sztringet. Másolja ki a teljes kapcsolati sztringet, és mentse a hez a Raspberry Pi-szimulátor létrehozásakor.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Kimeneti példa:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-blob-storage"></a>Blob Storage létrehozása

A következő Azure PowerShell kódblokk parancsokat használ a feladatkimenethez használt blobtároló létrehozásához. Tekintse át az egyes szakaszokat a kód értelmezése céljából.

1. Hozzon létre egy standard általános célú tárfiókot a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) parancsmaggal.  Ez a példa létrehoz egy **myasaquickstartstorage** nevű tárfiókot helyileg redundáns tárolással (LRS) és blobtitkosítással (alapértelmezés szerint engedélyezve).

2. Kérje le a `$storageAccount.Context` nevű tárfiókkörnyezetet, amely meghatározza a használni kívánt tárfiókot. Ha a tárfiókok használatával történő munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat.

3. Hozzon létre egy tárolót a [New-AzStorageContainer használatával.](/powershell/module/az.storage/new-azstoragecontainer)

4. Másolja ki a kód által kimenetként kimásolt tárkulcsot, és mentse a kulcsot, hogy később létrehozza a streamelési feladat kimenetét.

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

Hozzon létre egy Stream Analytics a [New-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/new-azstreamanalyticsjob) parancsmaggal. Ez a parancsmag paraméterként a feladat nevét, az erőforráscsoport nevét és a feladat definícióját veszi fel. A feladat neve bármilyen, a feladatot azonosító rövid név lehet. Csak alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, és 3–63 karakter hosszúságúnak kell lennie. A feladat definíciója egy JSON-fájl, amely a feladat létrehozásához szükséges tulajdonságokat tartalmazza. Hozzon létre a helyi gépén egy `JobDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat:

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

Ezután futtassa a `New-AzStreamAnalyticsJob` parancsmagot. Cserélje le a változó értékét arra az elérési útra, ahol a feladatdefiníciós `jobDefinitionFile` JSON-fájlt tárolta.

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

Adjon bemenetet a feladathoz a [New-AzStreamAnalyticsInput parancsmag](/powershell/module/az.streamanalytics/new-azstreamanalyticsinput) használatával. Ennél a parancsmagnál a feladat neve, a feladat bemenetének a neve, az erőforráscsoport neve, és a feladat bemenetének definíciója adható meg paraméterként. A feladat bemenetének definíciója egy JSON-fájl, amely a feladat bemenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ebben a példában egy blobtárolót hoz létre bemenetként.

Hozzon létre a helyi gépén egy `JobInputDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. A értékét cserélje le az előző szakaszban IoT Hub kapcsolati sztring `accesspolicykey` `SharedAccessKey` részével.

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

Ezután futtassa a parancsmagot, és cserélje le a változó értékét arra az elérési útra, ahol a feladat bemenetének `New-AzStreamAnalyticsInput` `jobDefinitionFile` definícióját tároló JSON-fájlt tárolta.

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

Adjon hozzá egy kimenetet a feladathoz a [New-AzStreamAnalyticsOutput parancsmag](/powershell/module/az.streamanalytics/new-azstreamanalyticsoutput) használatával. Ennél a parancsmagnál a feladat neve, a feladat kimenetének a neve, az erőforráscsoport neve, és a feladat kimenetének definíciója adható meg paraméterként. A feladat kimenetének definíciója egy JSON-fájl, amely a feladat kimenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ez a példa egy Blob Storage-tárolót használ kimenetként.

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

Adjon hozzá egy átalakítást a feladathoz a [New-AzStreamAnalyticsTransformation](/powershell/module/az.streamanalytics/new-azstreamanalyticstransformation) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat transzformációjának a neve, az erőforráscsoport neve, és a feladat transzformációjának definíciója adható meg paraméterként. Hozzon létre a helyi gépén egy `JobTransformationDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. A JSON-fájl tartalmaz egy query (lekérdezés) paramétert, amellyel definiálható a transzformációs lekérdezés:

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

Ezután futtassa a `New-AzStreamAnalyticsTransformation` parancsmagot. A változó értékét cserélje le arra az elérési útra, ahol a feladat transzformációs `jobTransformationDefinitionFile` JSON-fájlját tárolta.

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="run-the-iot-simulator"></a>Az IoT-szimulátor futtatása

1. Nyissa meg a [Raspberry Pi Azure IoT online szimulátort.](https://azure-samples.github.io/raspberry-pi-web-simulator/)

2. A 15. sorban cserélje le a helyőrzőt az Azure IoT Hub előző szakaszban mentett eszközkapcsolati sztring egészére.

3. Kattintson a **Futtatás** elemre. A kimenetnek meg kell mutatnia a rendszernek küldött érzékelőadatokat és IoT Hub.

    ![Online Raspberry Pi Azure IoT-szimulátor](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

Indítsa el a feladatot a [Start-AzStreamAnalyticsJob parancsmag](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) használatával. Ennél a parancsmagnál a feladat neve, az erőforráscsoport neve, a feladat kimenetének indítási módja, és a kezdés ideje adható meg paraméterként. Az `OutputStartMode` által elfogadott értékek: `JobStartTime`, `CustomTime` és `LastOutputEventTime`. A PowerShell-dokumentáció [paraméterekkel](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) foglalkozó szakaszában talál további információt arról, hogy ezek az értékek mit jelentenek.

A következő parancsmag futtatása a `True` kimenetet adja vissza, ha a feladat elindult. A Storage-tárolóban létrejön egy kimeneti mappa, amely az átalakított adatokat tartalmazza.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, törölheti az erőforráscsoportot, a folyamatos átviteli feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha később is szeretné használni a feladatot, akkor nem kell törölnie, hanem elég, ha leállítja. Ha nem folytatja a feladat használatát, a következő parancsmag futtatásával törölje a rövid útmutatóhoz létrehozott összes erőforrást:

```powershell
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics-feladatot helyezett üzembe a PowerShell használatával. A Stream Analytics-feladatokat az [Azure Portallal](stream-analytics-quick-create-portal.md) és a [Visual Studióval](stream-analytics-quick-create-vs.md) is üzembe helyezheti.

Az egyéb bemeneti források beállításával és a valós idejű észlelés végrehajtásával kapcsolatos információkért olvassa el az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Valós idejű csalásészlelés az Azure Stream Analytics használatával](stream-analytics-real-time-fraud-detection.md)