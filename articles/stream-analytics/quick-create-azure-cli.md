---
title: Rövid útmutató – Azure Stream Analytics-feladatok létrehozása az Azure CLI használatával
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure Stream Analytics feladatot az Azure CLI használatával.
services: stream-analytics
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 07/01/2020
ms.openlocfilehash: a3cc4c3d6936a51ca2010209ce23e4d82c9333eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016340"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>Gyors útmutató: Azure Stream Analytics-feladatok létrehozása az Azure CLI használatával

Ebben a rövid útmutatóban az Azure CLI használatával határozhat meg egy olyan Stream Analytics feladatot, amely a 27-nél nagyobb hőmérséklet-olvasási feladatokkal szűri a valós idejű érzékelői üzeneteket. A Stream Analytics-feladata beolvassa az adatok IoT Hubból való beolvasását, az adatok átalakítását és az adatoknak a blob Storage tárolóba való visszaírását. Az ebben a rövid útmutatóban használt bemeneti adatokat egy málna PI online szimulátor hozza létre.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Hozzon létre egy erőforráscsoportot. Az összes Azure-erőforrást egy erőforráscsoporthoz kell telepíteni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

   Ebben a rövid útmutatóban hozzon létre egy *streamanalyticsrg* nevű erőforráscsoportot a *eastus* helyen a következő az [Group Create](/cli/azure/group#az-group-create) paranccsal:

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A Stream Analytics feladatának meghatározása előtt készítse elő a feladatok bemenetéhez használt adatokat.

A következő Azure CLI-kódrészletek olyan parancsok, amelyek előkészítik a feladathoz szükséges bemeneti adatokat. Tekintse át az egyes szakaszokat a kód értelmezése céljából.

1. Hozzon létre egy IoT Hub az az [IoT hub Create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) paranccsal. Ez a példa egy **MyASAIoTHub** nevű IoT hub hoz létre. Mivel a IoT Hub nevek egyediek, a saját IoT Hub nevét kell megadnia. Állítsa az SKU-t az F1-re, hogy az ingyenes szintet használja, ha az előfizetése elérhető. Ha nem, válassza a következő legalacsonyabb szintet.

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    Miután létrejött az IoT hub, szerezze be a IoT Hub kapcsolati karakterláncot az az [IoT hub show-kapcsolat-string](/cli/azure/iot/hub) parancs használatával. Másolja a teljes kapcsolódási karakterláncot, és mentse azt, amikor hozzáadja a IoT Hub a Stream Analytics feladathoz.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. Az az [iothub Device-Identity Create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) paranccsal adhat hozzá IoT hub eszközt. Ez a példa egy **MyASAIoTDevice** nevű eszközt hoz létre.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. Szerezze be az eszköz kapcsolati karakterláncát az az [IOT hub Device-Identity show-kapcsolat-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) parancs használatával. Másolja a teljes kapcsolódási karakterláncot, és mentse azt a málna PI szimulátor létrehozásakor.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Példa a kimenetre:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>BLOB Storage-fiók létrehozása

A következő Azure CLI-kód blokkolja a feladatok kimenetéhez használt BLOB Storage-fiók létrehozását. Tekintse át az egyes szakaszokat a kód értelmezése céljából.

1. Az [az storage account create](/cli/azure/storage/account) paranccsal hozzon létre egy általános célú tárfiókot. Az általános célú tárfiók mind a négy szolgáltatással (blobok, fájlok, táblák és üzenetsorok) használható.

   Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. Szerezze be a Storage-fiók kulcsát az az [Storage Account Keys List](/cli/azure/storage/account/keys) parancs futtatásával. Mentse ezt a kulcsot a következő lépésben való használatra.

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. Hozzon létre blobok tárolására alkalmas tárolót az [az storage container create](/cli/azure/storage/container) parancs segítségével. A Storage-fiók kulcsa segítségével engedélyezheti a művelet számára a tároló létrehozását. Az adatműveletek Azure CLI-vel való engedélyezésével kapcsolatos további információkért lásd: [hozzáférés engedélyezése blob-vagy üzenetsor-adatokhoz az Azure CLI-vel](../storage/blobs/authorize-data-operations-cli.md).

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

A következő Azure CLI-kód blokkolja a Stream Analytics feladatok létrehozását. A kódok megismeréséhez tekintse át a szakaszt

1. Hozzon létre egy Stream Analytics feladatot az az [stream-Analytics Job Create](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-create) paranccsal.

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

Adjon hozzá egy bemenetet a feladathoz az az [stream-Analytics input](/cli/azure/ext/stream-analytics/stream-analytics/input#ext-stream-analytics-az-stream-analytics-input-create) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat bemenetének a neve, az erőforráscsoport neve, és a feladat bemenetének definíciója adható meg paraméterként. A feladathoz tartozó bemeneti definíció egy olyan JSON-fájl, amely a feladatok bemenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ebben a példában egy IoT Hub fog létrehozni bemenetként.

Hozzon létre a helyi gépén egy `datasource.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. Győződjön meg arról, hogy az értéket lecseréli a `sharedAccessPolicyKey` `SharedAccessKey` IoT hub az előző szakaszban mentett kapcsolódási karakterlánc egy részével.

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

Ezután futtassa a `az stream-analytics input create` parancsmagot. Ügyeljen arra, hogy a változó értékét cserélje le arra `datasource` az elérési útra, ahol a feladathoz megadott bemeneti DEFINÍCIÓ JSON-fájlját tárolta, valamint a `serialization` változó értékét azzal a útvonallal, ahol a SZERIALIZÁLÁSi JSON-fájlt tárolta.

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

Adja hozzá a kimenetet a feladatokhoz az az [stream-Analytics output Create](/cli/azure/ext/stream-analytics/stream-analytics/output#ext-stream-analytics-az-stream-analytics-output-create) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat kimenetének a neve, az erőforráscsoport neve, és a feladat kimenetének definíciója adható meg paraméterként. A feladatok kimenetének definíciója egy olyan JSON-fájl, amely a feladatok kimenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ez a példa egy Blob Storage-tárolót használ kimenetként.

Hozzon létre a helyi gépén egy `datasink.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. Ügyeljen arra, hogy az értékét cserélje le a `accountKey` Storage-fiókja hozzáférési kulcsára, amely $storageAccountKey értékben tárolt érték.

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

Ezután futtassa a `az stream-analytics output` parancsmagot. Ne felejtse el lecserélni a változó értékét arra `datasource` az elérési útra, ahol a feladatot a kimenet definíciójának JSON-fájlját tárolja, valamint a `serialization` változó értékét azzal a útvonallal, ahol a SZERIALIZÁLÁSi JSON-fájlt tárolta.

```azurecli
az stream-analytics output create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

Vegyen fel egy átalakítási feladatot az az [stream-Analytics Transformation Create](/cli/azure/ext/stream-analytics/stream-analytics/transformation#ext-stream-analytics-az-stream-analytics-transformation-create) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat transzformációjának a neve, az erőforráscsoport neve, és a feladat transzformációjának definíciója adható meg paraméterként. 

Futtassa a `az stream-analytics transformation create` parancsmagot.

```azurecli
az stream-analytics transformation create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>A IoT Simulator futtatása

1. Nyissa meg a [málna PI Azure IoT online szimulátort](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. A 15. sorban található helyőrzőt cserélje le az előző szakaszban mentett teljes Azure-IoT Hub eszköz-összekapcsolási sztringre.

3. Kattintson a **Futtatás** elemre. A kimenetnek meg kell jelenítenie az érzékelő adatait és a IoT Hub küldött üzeneteket.

    ![Online Raspberry Pi Azure IoT-szimulátor](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

Indítsa el a feladatot az az [stream-Analytics Job Start](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-start) parancsmag használatával. Ennél a parancsmagnál a feladat neve, az erőforráscsoport neve, a feladat kimenetének indítási módja, és a kezdés ideje adható meg paraméterként. Az `OutputStartMode` által elfogadott értékek: `JobStartTime`, `CustomTime` és `LastOutputEventTime`.

A következő parancsmag futtatása a `True` kimenetet adja vissza, ha a feladat elindult. A Storage-tárolóban létrejön egy kimeneti mappa, amely az átalakított adatokat tartalmazza.

```azurecli
az stream-analytics job start \
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, törölheti az erőforráscsoportot, a folyamatos átviteli feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha később is szeretné használni a feladatot, akkor nem kell törölnie, hanem elég, ha leállítja. Ha nem kívánja tovább használni ezt a feladatot, törölje az ebben a rövid útmutatóban létrehozott összes erőforrást a következő parancsmag futtatásával:

```azurecli
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics feladatot telepített az Azure CLI használatával. A Stream Analytics-feladatokat az [Azure Portallal](stream-analytics-quick-create-portal.md) és a [Visual Studióval](stream-analytics-quick-create-vs.md) is üzembe helyezheti.

Az egyéb bemeneti források beállításával és a valós idejű észlelés végrehajtásával kapcsolatos információkért olvassa el az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Valós idejű csalásészlelés az Azure Stream Analytics használatával](stream-analytics-real-time-fraud-detection.md)