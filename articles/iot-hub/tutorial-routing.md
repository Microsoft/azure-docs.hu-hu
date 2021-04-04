---
title: Oktatóanyag – az Azure-IoT Hub üzenet-útválasztásának konfigurálása az Azure CLI használatával
description: Oktatóanyag – az Azure-IoT Hub üzenet-útválasztásának konfigurálása az Azure CLI és a Azure Portal használatával
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: d7a10aac82286616273f7283bc3fba74f2c58f35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625215"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Oktatóanyag: az Azure CLI és a Azure Portal használatával konfigurálhatja IoT Hub üzenetek útválasztását

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Az alaperőforrások létrehozása az Azure CLI használatával

Ez az oktatóanyag az Azure CLI-vel hozza létre az alaperőforrásokat, majd a [Azure Portal](https://portal.azure.com) használatával mutatja be, hogyan konfigurálhatja az üzenetsor-útválasztást, és hogyan állíthatja be a virtuális eszközt tesztelésre.

Másolja és illessze be az alábbi szkriptet Cloud Shellba, majd nyomja le az ENTER billentyűt. A parancsfájl egyszerre egy sort futtat. Ezzel létrehozza az oktatóanyag alaperőforrásait, beleértve a Storage-fiókot, a IoT Hub, a Service Bus névteret és a Service Bus üzenetsor-t.

Több olyan erőforrás neve van, amelynek globálisan egyedinek kell lennie, például a IoT Hub neve és a Storage-fiók neve. Ennek egyszerűbbé tétele érdekében az erőforrásnevek egy *randomValue* nevű véletlenszerű alfanumerikus értékkel vannak hozzáfűzve. A rendszer egyszer hozza létre a randomValue a parancsfájl tetején, és szükség szerint hozzáfűzi az erőforrások nevét a parancsfájlban. Ha nem szeretné, hogy véletlenszerű legyen, beállíthatja egy üres sztringre vagy egy adott értékre.

> [!TIP]
> Tipp a hibakereséshez: Ez a szkript a folytatási szimbólumot (a fordított perjelet `\` ) használja, hogy a szkript olvashatóbb legyen. Ha a parancsfájl futtatása során probléma merül fel, győződjön meg arról, hogy a Cloud Shell-munkamenet fut, `bash` és a fordított perjelek bármelyike után nincsenek szóközök.
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

```

Most, hogy beállította az alaperőforrásokat, beállíthatja az üzenet-útválasztást a [Azure Portalban](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Üzenetek útválasztásának beállítása

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Útvonal egy Storage-fiókhoz

Most beállíthatja az útválasztást a tárfiókhoz. Lépjen az Üzenetirányítás panelre, majd adjon hozzá egy útvonalat. Az útvonal hozzáadásakor definiáljon egy új végpontot az útvonalhoz. Az Útválasztás beállítása után a rendszer automatikusan a Storage-fiókba írja be azokat az üzeneteket, amelyekben a **szint** tulajdonság a **Storage** -ra van beállítva. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforráscsoportok** elemet, majd válassza ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

2. Válassza ki az IoT hubot az erőforrások listájában. Ez az oktatóanyag a **ContosoTestHub** erőforrást használja.

3. Válassza az **üzenet-útválasztás** lehetőséget. Az **üzenet-útválasztás** ablaktáblán válassza a +**Hozzáadás** lehetőséget. Az **útvonal hozzáadása** panelen válassza a végpont mező melletti +**végpont hozzáadása** lehetőséget a támogatott végpontok megjelenítéséhez, ahogy az a következő képen látható:

   ![Végpont hozzáadásának megkezdése egy útvonalhoz](./media/tutorial-routing/message-routing-add-a-route-with-storage-endpoint-ver2.png)

4. Válassza a **tárterület** lehetőséget. **Megjelenik a tárolási végpont hozzáadása** panel.

   ![Végpont hozzáadása](./media/tutorial-routing/message-routing-add-storage-endpoint-ver2.png)

5. Adja meg a végpont nevét. Ez az oktatóanyag az **ContosoStorageEndpoint**-t használja.

6. Válassza **a tároló** kiválasztása lehetőséget. Ekkor megjelenik a saját tárfiókok listája. Válassza ki azt, amelyiket az előkészítő lépések során állított be. Ez az oktatóanyag a **contosostorage** tárfiókot használja. Ebben az ehhez a tárfiókhoz tartozó tárolókat láthatja. **Válassza ki** az előkészítési lépésekben beállított tárolót. Ez az oktatóanyag a **contosoresults** tárolót használja. Visszatér a Storage- **végpont hozzáadása** panelre, és megtekintheti a kiválasztott beállításokat.

7. Állítsa a kódolást AVRO vagy JSON-ra. Ebben az oktatóanyagban a többi mező esetében az alapértelmezett értékeket fogja használni. Ez a mező szürkén jelenik meg, ha a kiválasztott régió nem támogatja a JSON-kódolást.

   > [!NOTE]
   > A blob nevének formátumát a **Blob fájlnévformátuma** mezőben adhatja meg. A mező alapértelmezett értéke: `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. A formátumnak tetszőleges sorrendben tartalmaznia kell a következőket: {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} és {mm}.
   >
   > Ha például az alapértelmezett blobfájlnév-formátumot használja egy ContosoTestHub nevű központ esetében, és a dátum/idő érték 2018. október 30., 10:56, akkor a blob neve a következő lesz: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > A blobok írása Avro formátumban történik.
   >

8. A **Létrehozás** gombra kattintva hozza létre a tárolási végpontot, és adja hozzá az útvonalhoz. Ekkor visszakerül az **Útvonal hozzáadása** panelre.

9. Töltse ki az útválasztási lekérdezés hiányzó adatait. Ez a lekérdezés megszabja az üzenetek az imént végpontként hozzáadott tárolóhoz való irányításának feltételeit. Töltse ki a képernyőn látható mezőket.

   **Név**: Adja meg az útválasztási lekérdezés nevét. Ez az oktatóanyag az **ContosoStorageRoute**-t használja.

   **Végpont**: Az imént beállított végpontot jeleníti meg.

   **Adatforrás**: A legördülő listából válassza az **Eszköztelemetria-üzenetek** elemet.

   **Útvonal engedélyezése**: Ügyeljen rá, hogy a mező értéke `enabled` .
   
   **Útválasztási lekérdezés**: Lekérdezési sztringként írja be a következőt: `level="storage"`.

   ![Útválasztási lekérdezés létrehozása a Storage-fiókhoz](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Kattintson a **Mentés** gombra. A befejezés után a rendszer visszalép az Üzenetirányítás panelre, ahol láthatja a tároló új útválasztási lekérdezését. Zárja be az Útvonalak panelt, ezzel visszatér az Erőforráscsoport oldalra.

### <a name="route-to-a-service-bus-queue"></a>Útvonal Service Bus üzenetsor felé

Most állítsa be az útválasztást a Service Bus-üzenetsorhoz. Lépjen az Üzenetirányítás panelre, majd adjon hozzá egy útvonalat. Az útvonal hozzáadásakor definiáljon egy új végpontot az útvonalhoz. Miután beállította az útvonalat, az üzenetek, amelyekben a **Level** tulajdonság **kritikusra** van állítva, a rendszer a Service Bus várólistába írja, amely elindít egy logikai alkalmazást, amely ezután e-mailt küld az információkkal.

1. Az erőforráscsoport lapon válassza ki az IoT hubot, majd válassza az **üzenet-útválasztás** elemet.

2. Az **üzenet-útválasztás** ablaktáblán válassza a +**Hozzáadás** lehetőséget.

3. Az **útvonal hozzáadása** panelen kattintson a +**Hozzáadás** elemre a végpont mező mellett. Válasszon egy **Service Bus-üzenetsort**. Ekkor megjelenik a **Service Bus-végpont hozzáadása** panel.

   ![Service Bus-végpont hozzáadása](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Töltse ki a mezőket:

   **Végpont neve**: Adja meg a végpont nevét. Ez az oktatóanyag az **ContosoSBQueueEndpoint**-t használja.
   
   **Service Bus névtér**: a legördülő listából válassza ki az előkészítési lépésekben beállított Service Bus-névteret. Ez az oktatóanyag a **ContosoSBNamespace** névteret használja.

   **Service Bus üzenetsor**: a legördülő lista használatával válassza ki a Service Bus várólistát. Ez az oktatóanyag a **contososbqueue** üzenetsort használja.

5. Válassza a **Létrehozás** lehetőséget az Service Bus üzenetsor végpontjának hozzáadásához. Ekkor visszakerül az **Útvonal hozzáadása** panelre.

6. Töltse ki az útválasztási lekérdezés hiányzó adatait. Ez a lekérdezés megszabja az üzenetek az imént végpontként hozzáadott Service Bus-üzenetsorhoz való irányításának feltételeit. Töltse ki a képernyőn látható mezőket. 

   **Név**: Adja meg az útválasztási lekérdezés nevét. Ez az oktatóanyag az **ContosoSBQueueRoute**-t használja. 

   **Végpont**: Az imént beállított végpontot jeleníti meg.

   **Adatforrás**: A legördülő listából válassza az **Eszköztelemetria-üzenetek** elemet.

   **Útválasztási lekérdezés**: Lekérdezési sztringként írja be a következőt: `level="critical"`. 

   ![Útválasztási lekérdezés létrehozása az Service Bus üzenetsor számára](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Kattintson a **Mentés** gombra. Miután visszatért az Útvonalak panelre, láthatja a két új útvonalat, ahogy itt is szerepelnek.

   ![Az imént beállított útvonalak](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Az egyéni **végpontok** lapon megtekintheti a beállított egyéni végpontokat.

   ![Az imént beállított egyéni végpont](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Zárja be az Üzenetirányítás panelt, ezzel visszatér az Erőforráscsoport panelre.

## <a name="create-a-simulated-device"></a>Szimulált eszköz létrehozása

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította az erőforrásokat, és konfigurálta az üzenet útvonalait, folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan küldhet üzeneteket az IoT hubhoz, és hogyan irányíthatja őket át a különböző célhelyekre. 

> [!div class="nextstepaction"]
> [2. rész – az üzenetek útválasztási eredményeinek megtekintése](tutorial-routing-view-message-routing-results.md)