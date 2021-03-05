---
title: Oktatóanyag – az Azure IoT Hub üzenet-útválasztásának konfigurálása az Azure CLI használatával
description: Oktatóanyag – az Azure-IoT Hub üzenet-útválasztásának konfigurálása az Azure CLI használatával. Az üzenet tulajdonságaitól függően egy Storage-fiókra vagy egy Service Bus-várólistára kell irányítani.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a1cce7264e56b16d285685392186a05e2f075439
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199764"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Oktatóanyag: az Azure CLI használata IoT Hub üzenet-útválasztás konfigurálásához

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>A parancsfájl letöltése (opcionális)

Az oktatóanyag második részében le kell töltenie és futtatnia kell egy Visual Studio-alkalmazást, amely üzeneteket küld a IoT Hub. A letöltésben egy olyan mappa található, amely tartalmazza a Azure Resource Manager sablont és a paramétereket tartalmazó fájlt, valamint az Azure CLI-és PowerShell-parancsfájlokat.

Ha meg szeretné tekinteni a kész parancsfájlt, töltse le az [Azure IoT C#-mintákat](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Bontsa ki a master.zip fájlt. Az Azure CLI-szkript a/iot-hub/Tutorials/Routing/SimulatedDevice/resources/-ben **iothub_routing_cli. azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Erőforrások létrehozása az Azure CLI használatával

Másolja és illessze be az alábbi szkriptet Cloud Shellba, majd nyomja le az ENTER billentyűt. A parancsfájl egyszerre egy sort futtat. A szkript ezen első szakasza létrehozza az oktatóanyag alaperőforrásait, beleértve a Storage-fiókot, a IoT Hub, a Service Bus névteret és a Service Bus üzenetsor-t. Ahogy az oktatóanyag többi részén, másolja az egyes parancsfájlokat, és illessze be Cloud Shellba a futtatásához.

> [!TIP]
> Tipp a hibakereséshez: Ez a szkript a folytatási szimbólumot (a fordított perjelet `\` ) használja, hogy a szkript olvashatóbb legyen. Ha a parancsfájl futtatása során probléma merül fel, győződjön meg arról, hogy a Cloud Shell-munkamenet fut, `bash` és a fordított perjelek bármelyike után nincsenek szóközök.
> 

Több olyan erőforrás neve van, amelynek globálisan egyedinek kell lennie, például a IoT Hub neve és a Storage-fiók neve. Ennek egyszerűbbé tétele érdekében az erőforrásnevek egy *randomValue* nevű véletlenszerű alfanumerikus értékkel vannak hozzáfűzve. A rendszer egyszer hozza létre a randomValue a parancsfájl tetején, és szükség szerint hozzáfűzi az erőforrások nevét a parancsfájlban. Ha nem szeretné, hogy véletlenszerű legyen, beállíthatja egy üres sztringre vagy egy adott értékre. 

> [!IMPORTANT]
> A kezdeti parancsfájlban beállított változókat az útválasztási parancsfájl is használja, ezért futtassa az összes parancsfájlt ugyanabban a Cloud Shell-munkamenetben. Ha új munkamenetet nyit meg az Útválasztás beállítására szolgáló parancsfájl futtatásához, akkor a változók közül számos érték hiányzik.
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

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

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Most, hogy beállította az alaperőforrásokat, beállíthatja az üzenet-útválasztást.

## <a name="set-up-message-routing"></a>Üzenetek útválasztásának beállítása

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Útválasztási végpont létrehozásához használja [az az IOT hub Routing-Endpoint Create](/cli/azure/iot/hub/routing-endpoint#az-iot-hub-routing-endpoint-create)lehetőséget. A végponthoz tartozó üzenet útvonalának létrehozásához használja az [az IOT hub Route Create](/cli/azure/iot/hub/route#az-iot-hub-route-create)lehetőséget.

### <a name="route-to-a-storage-account"></a>Útvonal egy Storage-fiókhoz

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Először állítsa be a végpontot a Storage-fiókhoz, majd állítsa be az útvonalat. 

Ezek a szkriptek által használt változók, amelyeket a Cloud Shell-munkameneten belül kell beállítani:

**storageConnectionString**: ezt az értéket az előző parancsfájlban beállított Storage-fiókból kéri le a rendszer. Ezt az üzenet-útválasztás használja a Storage-fiók eléréséhez.

  **resourceGroup**: az erőforráscsoport két előfordulása van – állítsa be őket az erőforráscsoporthoz.

**végpont subscriptionID**: Ez a mező a végpont Azure-subscriptionID van beállítva. 

**endpointType**: Ez a mező a végpont típusát adja meg. Ezt az értéket a,, `azurestoragecontainer` `eventhub` `servicebusqueue` , vagy a értékre kell beállítani `servicebustopic` . Itt adja meg a kívánt célokat `azurestoragecontainer` .

**iotHubName**: Ez a mező az útválasztást elvégező központ neve.

**containerName**: Ez a mező annak a tárolónak a neve, amelybe az adatírás történik.

**kódolás**: Ez a mező a vagy a érték lesz `avro` `json` . Ez a tárolt adatmennyiség formátumát jelöli.

**z**: Ez a mező a beállított útvonal neve. 

**végpontneve**: Ez a mező a végpontot azonosító név. 

**engedélyezve**: Ez a mező alapértelmezés szerint a (z) értékre van beállítva `true` , ami azt jelzi, hogy a létrehozás után engedélyezni kell az üzenet útvonalát.

**feltétel**: Ez a mező a végpontnak küldött üzenetek szűrésére szolgáló lekérdezés. A tárolóba átirányított üzenetek lekérdezési feltétele `level="storage"` .

Másolja ezt a szkriptet, és illessze be a Cloud Shell ablakába, és futtassa.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

A következő lépés a Storage-fiók útválasztási végpontjának létrehozása. Megadhatja azt a tárolót is, amelyben az eredményeket tárolni fogja. A tároló korábban lett létrehozva a Storage-fiók létrehozásakor.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Ezután hozza létre a tárolási végpont útvonalát. Az üzenet útvonala azt jelöli, hogy hová kell elküldeni a lekérdezési specifikációnak megfelelő üzeneteket. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Útvonal Service Bus üzenetsor felé

Most állítsa be az útválasztást a Service Bus-üzenetsorhoz. A Service Bus üzenetsor kapcsolódási karakterláncának beolvasásához létre kell hoznia egy engedélyezési szabályt, amely rendelkezik a megfelelő jogosultságokkal. A következő szkript létrehoz egy engedélyezési szabályt a nevű Service Bus üzenetsor számára `sbauthrule` , és beállítja a jogosultságokat `Listen Manage Send` . Az engedélyezési szabály meghatározása után a segítségével lekérheti a várólista kapcsolódási karakterláncát.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Most az engedélyezési szabály segítségével kérje le a kapcsolódási karakterláncot a Service Bus várólistára.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Most állítsa be az útválasztási végpontot és a Service Bus üzenetsor üzenetének útvonalát. Ezek a szkriptek által használt változók, amelyeket a Cloud Shell-munkameneten belül kell beállítani:

**végpontneve**: Ez a mező a végpontot azonosító név. 

**endpointType**: Ez a mező a végpont típusát adja meg. Ezt az értéket a,, `azurestoragecontainer` `eventhub` `servicebusqueue` , vagy a értékre kell beállítani `servicebustopic` . Itt adja meg a kívánt célokat `servicebusqueue` .

**z**: Ez a mező a beállított útvonal neve. 

**feltétel**: Ez a mező a végpontnak küldött üzenetek szűrésére szolgáló lekérdezés. Az Service Bus várólistára átirányított üzenetek lekérdezési feltétele `level="critical"` .

Itt látható az útválasztási végpont Azure CLI-je és az Service Bus üzenetsor üzenetének útvonala.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Üzenet-útválasztás megtekintése a portálon

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította az erőforrásokat, és konfigurálta az üzenet útvonalait, folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan küldhet üzeneteket az IoT hubhoz, és hogyan irányíthatja őket át a különböző célhelyekre. 

> [!div class="nextstepaction"]
> [2. rész – az üzenetek útválasztási eredményeinek megtekintése](tutorial-routing-view-message-routing-results.md)
