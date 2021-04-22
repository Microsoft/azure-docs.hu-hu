---
title: Rövid útmutató – Azure Stream Analytics létrehozása az Azure CLI használatával
description: Ez a rövid útmutató bemutatja, hogyan használható az Azure CLI egy Azure Stream Analytics feladat létrehozására.
services: stream-analytics
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 07/01/2020
ms.openlocfilehash: bb8f2d77c04e01c47318042337db819ac2f36d46
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863190"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>Rövid útmutató: Azure Stream Analytics létrehozása az Azure CLI használatával

Ebben a rövid útmutatóban az Azure CLI használatával határoz meg egy olyan Stream Analytics-feladatot, amely 27-esnél magasabb hőmérsékleti értékkel szűri a valós idejű érzékelőüzeneteket. Az Stream Analytics feladat adatokat fog beolvasni a IoT Hub, átalakítja az adatokat, és visszaírja őket egy blobtárolóba. Az ebben a rövid útmutatóban használt bemeneti adatokat egy Raspberry Pi online szimulátor generálja.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Hozzon létre egy erőforráscsoportot. Minden Azure-erőforrást egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

   Ebben a rövid útmutatóban hozzon létre egy *streamanalyticsrg* nevű erőforráscsoportot az *eastus* helyen a következő [az group create paranccsal:](/cli/azure/group#az_group_create)

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A feladat Stream Analytics előtt készítse elő a feladat bemenetéhez használt adatokat.

A következő Azure CLI-kódblokkok olyan parancsok, amelyek előkészítik a feladathoz szükséges bemeneti adatokat. Tekintse át az egyes szakaszokat a kód értelmezése céljából.

1. Hozzon létre IoT Hub [az az iot hub create paranccsal.](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) Ez a példa egy **MyASAIoTHub** nevű IoT Hub hoz létre. Mivel IoT Hub egyedi nevek, saját nevet kell IoT Hub neve. Állítsa a termékváltozatot F1-re, hogy az ingyenes szintet használja, ha az elérhető az előfizetésében. Ha nem, válassza a következő legalacsonyabb szintet.

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    Az IoT Hub létrehozása után az [az iot hub show-connection-string](/cli/azure/iot/hub) paranccsal IoT Hub le a kapcsolati sztringet. Másolja ki a teljes kapcsolati sztringet, és mentse a fájlba, amikor hozzáadja a IoT Hub a bemeneti Stream Analytics feladathoz.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. Adjon hozzá egy eszközt a IoT Hub [az iothub device-identity create paranccsal.](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) Ez a példa létrehoz egy **MyASAIoTDevice nevű eszközt.**

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. Az az [iot hub device-identity show-connection-string paranccsal](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_show_connection_string) szerezze be az eszköz kapcsolati sztringet. Másolja ki a teljes kapcsolati sztringet, és mentse a hez a Raspberry Pi-szimulátor létrehozásakor.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Kimeneti példa:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>Blob Storage-fiók létrehozása

A következő Azure CLI-kódblokkok egy blobtároló-fiókot hoznak létre, amely a feladat kimenetére szolgál. Tekintse át az egyes szakaszokat a kód értelmezése céljából.

1. Az [az storage account create](/cli/azure/storage/account) paranccsal hozzon létre egy általános célú tárfiókot. Az általános célú tárfiók mind a négy szolgáltatással (blobok, fájlok, táblák és üzenetsorok) használható.

   Ne felejtse el lecserélni a szögletes zárójelben lévő helyőrzőértékeket a saját értékeire:

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. Az az storage account keys [list](/cli/azure/storage/account/keys) paranccsal szerezze be a tárfiók kulcsát. Mentse ezt a kulcsot a következő lépésben való használathoz.

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. Hozzon létre blobok tárolására alkalmas tárolót az [az storage container create](/cli/azure/storage/container) parancs segítségével. A tárfiókkulcs használatával engedélyezheti a tároló létrehozására szolgáló műveletet. További információ az adatműveletek Azure CLI-beli engedélyével kapcsolatban: Blob- vagy üzenetsoradatok elérésének jogosultsága [az Azure CLI használatával.](../storage/blobs/authorize-data-operations-cli.md)

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

A következő Azure CLI-kódblokkok létrehoznak egy Stream Analytics feladatot. Tekintse át a szakaszokat a kódért

1. Hozzon létre Stream Analytics feladatot [az az stream-analytics job create paranccsal.](/cli/azure/stream-analytics/job#az_stream_analytics_job_create)

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location eastus \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>A feladat bemenetének konfigurálása

Adjon hozzá egy bemenetet a feladathoz az [az stream-analytics input](/cli/azure/stream-analytics/input#az_stream_analytics_input_create) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat bemenetének a neve, az erőforráscsoport neve, és a feladat bemenetének definíciója adható meg paraméterként. A feladat bemenetének definíciója egy JSON-fájl, amely a feladat bemenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ebben a példában egy új IoT Hub fog létrehozni bemenetként.

Hozzon létre a helyi gépén egy `datasource.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. Ügyeljen arra, hogy a értékét cserélje le az előző IoT Hub mentett kapcsolati `sharedAccessPolicyKey` `SharedAccessKey` sztring részével.

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

Hozzon létre a helyi gépén egy `serialization.json` nevű fájlt, és adja hozzá a következő JSON-adatokat.

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

Ezután futtassa a `az stream-analytics input create` parancsmagot. A változó értékét cserélje le arra az elérési útra, ahol a feladat bemenet-definíciós JSON-fájlját tárolta, a változó értékét pedig arra az elérési útra, ahol a szerializálási `datasource` `serialization` JSON-fájlt tárolta.

```azurecli
az stream-analytics input create \
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>A feladat kimenetének konfigurálása

Adjon hozzá egy kimenetet a feladathoz az [az stream-analytics output create](/cli/azure/stream-analytics/output#az_stream_analytics_output_create) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat kimenetének a neve, az erőforráscsoport neve, és a feladat kimenetének definíciója adható meg paraméterként. A feladat kimenetének definíciója egy JSON-fájl, amely a feladat kimenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ez a példa egy Blob Storage-tárolót használ kimenetként.

Hozzon létre a helyi gépén egy `datasink.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. A értékét cserélje le a tárfiók hozzáférési kulcsával, amely a tárfiókban tárolt `accountKey` $storageAccountKey érték.

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

Ezután futtassa a `az stream-analytics output` parancsmagot. A változó értékét cserélje le arra az elérési útra, ahol a feladatkimenet-definíció JSON-fájlját tárolta, a változó értékét pedig arra az elérési útra, ahol a szerializálási `datasource` `serialization` JSON-fájlt tárolta.

```azurecli
az stream-analytics output create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

Adjon hozzá egy transzformációt a feladathoz [az az stream-analytics transformation create](/cli/azure/stream-analytics/transformation#az_stream_analytics_transformation_create) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat transzformációjának a neve, az erőforráscsoport neve, és a feladat transzformációjának definíciója adható meg paraméterként. 

Futtassa `az stream-analytics transformation create` a parancsmagot.

```azurecli
az stream-analytics transformation create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>Az IoT-szimulátor futtatása

1. Nyissa meg a [Raspberry Pi Azure IoT online szimulátort.](https://azure-samples.github.io/raspberry-pi-web-simulator/)

2. A 15. sorban cserélje le a helyőrzőt az Azure IoT Hub előző szakaszban mentett eszközkapcsolati sztring egészére.

3. Kattintson a **Futtatás** elemre. A kimenetnek meg kell mutatnia a rendszernek küldött érzékelőadatokat és IoT Hub.

    ![Online Raspberry Pi Azure IoT-szimulátor](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

Indítsa el a feladatot az [az stream-analytics job start](/cli/azure/stream-analytics/job#az_stream_analytics_job_start) parancsmag használatával. Ennél a parancsmagnál a feladat neve, az erőforráscsoport neve, a feladat kimenetének indítási módja, és a kezdés ideje adható meg paraméterként. Az `OutputStartMode` által elfogadott értékek: `JobStartTime`, `CustomTime` és `LastOutputEventTime`.

A következő parancsmag futtatása a `True` kimenetet adja vissza, ha a feladat elindult. A Storage-tárolóban létrejön egy kimeneti mappa, amely az átalakított adatokat tartalmazza.

```azurecli
az stream-analytics job start \
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, törölheti az erőforráscsoportot, a folyamatos átviteli feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha később is szeretné használni a feladatot, akkor nem kell törölnie, hanem elég, ha leállítja. Ha nem folytatja a feladat használatát, a következő parancsmag futtatásával törölje a rövid útmutatóhoz létrehozott összes erőforrást:

```azurecli
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics helyezett üzembe az Azure CLI használatával. A Stream Analytics-feladatokat az [Azure Portallal](stream-analytics-quick-create-portal.md) és a [Visual Studióval](stream-analytics-quick-create-vs.md) is üzembe helyezheti.

Az egyéb bemeneti források beállításával és a valós idejű észlelés végrehajtásával kapcsolatos információkért olvassa el az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Valós idejű csalásészlelés az Azure Stream Analytics használatával](stream-analytics-real-time-fraud-detection.md)