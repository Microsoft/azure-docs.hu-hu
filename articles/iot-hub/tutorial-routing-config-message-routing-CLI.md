---
title: Oktatóanyag – Üzenetek útválasztásának konfigurálása Azure IoT Hub Azure CLI használatával
description: Oktatóanyag – Üzenetek útválasztásának konfigurálása Azure IoT Hub Azure CLI használatával. Az üzenet tulajdonságaitól függően irányítsa az útvonalat egy tárfiókba vagy egy Service Bus üzenetsorba.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cf994dfe3d53232ab1e2374fda620dc768127097
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788888"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Oktatóanyag: Üzenet-útválasztás konfigurálása az Azure CLI IoT Hub használatával

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>A szkript letöltése (nem kötelező)

Az oktatóanyag második részében letölt és futtat egy Visual Studio, amely üzeneteket küld a IoT Hub. A letöltésben található egy mappa, amely tartalmazza a Azure Resource Manager sablont és a paraméterfájlt, valamint az Azure CLI- és PowerShell-szkripteket.

Ha meg szeretné tekinteni a kész szkriptet, töltse le az [Azure IoT C#-mintákat.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Csomagolja ki a master.zip fájlt. Az Azure CLI-szkript az /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ útvonalon **iothub_routing_cli.azcli.**

## <a name="use-the-azure-cli-to-create-your-resources"></a>Erőforrások létrehozása az Azure CLI használatával

Másolja és illessze be az alábbi szkriptet a Cloud Shell és nyomja le az Enter billentyűt. A szkriptet sorról sorra futtatja. A szkript első szakasza létrehozza az oktatóanyag alaperőforrását, beleértve a tárfiókot, a IoT Hub-t, a Service Bus-t és az Service Bus üzenetsort. Az oktatóanyag hátralevő részében másolja ki az egyes szkriptblokkokat, és illessze be Cloud Shell a futtatáshoz.

> [!TIP]
> Tipp a hibakeresésről: ez a szkript a folytatási szimbólumot (a perjelet) használja, hogy olvashatóbb `\` legyen. Ha problémája van a szkript futtatásával, ellenőrizze, hogy fut-e Cloud Shell munkamenet, és hogy a két perjel után ne legyen `bash` szóköz.
> 

Számos erőforrásnévnek globálisan egyedinek kell lennie, például a IoT Hub és a tárfiók neve. Ennek érdekében a rendszer hozzáfűzi ezeket az erőforrásneveket egy randomValue nevű véletlenszerű alfanumerikus *értékkel.* A randomValue egyszer jön létre a szkript tetején, és szükség szerint hozzáfűzi az erőforrásnevekhez a szkriptben. Ha nem szeretné, hogy véletlenszerű legyen, beállíthatja üres sztringre vagy egy adott értékre. 

> [!IMPORTANT]
> A kezdeti szkriptben beállított változókat az útválasztási szkript is használja, ezért futtassa az összes szkriptet ugyanabban a Cloud Shell munkamenetben. Ha új munkamenetet nyit meg az útválasztás beállításához futtatott szkript futtatásához, több változóból is hiányoznak értékek.
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

Most, hogy beállította az alaperőforrásokat, konfigurálhatja az üzenetek útválasztását.

## <a name="set-up-message-routing"></a>Üzenetek útválasztásának beállítása

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Útválasztási végpont létrehozásához használja az [az iot hub routing-endpoint create et.](/cli/azure/iot/hub/routing-endpoint#az_iot_hub_routing_endpoint_create) A végpont üzenetútvonalának létrehozásához használja [az az iot hub route create et.](/cli/azure/iot/hub/route#az_iot_hub_route_create)

### <a name="route-to-a-storage-account"></a>Útvonal egy tárfiókhoz

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Először állítsa be a tárfiók végpontját, majd állítsa be az útvonalat. 

A szkript ezeket a változókat használja, és ezeket kell beállítani a Cloud Shell munkamenetben:

**storageConnectionString:** Ez az érték az előző szkriptben beállított tárfiókból van lekérve. Az üzenet-útválasztás használja a tárfiók eléréséhez.

  **resourceGroup:** Az erőforráscsoportnak két előfordulása van – állítsa be őket az erőforráscsoportra.

**endpoint subscriptionID:** Ez a mező a végpont Azure subscriptionID-jára van beállítva. 

**endpointType:** Ez a mező a végpont típusa. Ezt az értéket , , vagy értékre `azurestoragecontainer` `eventhub` kell `servicebusqueue` `servicebustopic` állítani. A jelen célra állítsa be a `azurestoragecontainer` beállítását.

**iotHubName:** Ez a mező az útválasztást osztó központ neve.

**containerName:** Ez a mező annak a tárolónak a neve a tárfiókban, amelybe az adatok írva lesznek.

**encoding**: Ez a mező vagy `avro` `json` lesz. Ez a tárolt adatok formátumát jelöli.

**routeName:** Ez a mező a beállított útvonal neve. 

**endpointName:** Ez a mező a végpontot azonosító név. 

**enabled**: Ez a mező alapértelmezés szerint , ami azt jelzi, hogy az üzenetútvonalat a létrehozás `true` után engedélyezni kell.

**condition:** Ez a mező az erre a végpontra küldött üzenetek szűrésére használt lekérdezés. A tárolóba irányított üzenetek lekérdezési feltétele `level="storage"` a következő: .

Másolja ki ezt a szkriptet, illessze be a Cloud Shell ablakába, és futtassa.

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

A következő lépés a tárfiók útválasztási végpontjának létrehozása. Megadhatja azt a tárolót is, amelyben az eredmények tárolva lesznek. A tárolót korábban a tárfiók létrehozásakor hozták létre.

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

Ezután hozza létre a tárolási végpont útvonalát. Az üzenetútvonal azt jelöli, hogy hová küldje a lekérdezési specifikációnak megfelelő üzeneteket. 

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

### <a name="route-to-a-service-bus-queue"></a>Útvonal egy Service Bus üzenetsorhoz

Most állítsa be az útválasztást a Service Bus-üzenetsorhoz. Az üzenetsor kapcsolati sztringének Service Bus létre kell hoznia egy engedélyezési szabályt, amely a megfelelő jogosultságokkal rendelkezik. A következő szkript létrehoz egy engedélyezési szabályt a Service Bus nevű üzenetsorhoz, és a jogosultságokat a `sbauthrule` következőre állítja `Listen Manage Send` be: . Miután meghatározta ezt az engedélyezési szabályt, annak használatával lekérheti az üzenetsor kapcsolati sztringét.

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

Most használja az engedélyezési szabályt a kapcsolati sztring lekérésére a Service Bus üzenetsorba.

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

Most állítsa be az útválasztási végpontot és az üzenetsor üzenetútvonalát Service Bus üzenetsorhoz. A szkript ezeket a változókat használja, és ezeket kell beállítani a Cloud Shell munkamenetben:

**endpointName:** Ez a mező a végpontot azonosító név. 

**endpointType:** Ez a mező a végpont típusa. Ezt az értéket , , vagy értékre `azurestoragecontainer` `eventhub` kell `servicebusqueue` `servicebustopic` állítani. A jelen célra állítsa be a `servicebusqueue` beállítását.

**routeName:** Ez a mező a beállított útvonal neve. 

**condition:** Ez a mező az erre a végpontra küldött üzenetek szűrésére használt lekérdezés. A lekérdezési üzenetsorba irányított üzenetek lekérdezési Service Bus a `level="critical"` következő: .

Itt található az Azure CLI az útválasztási végponthoz és az üzenetsor üzenetútvonala Service Bus üzenetsorhoz.

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

### <a name="view-message-routing-in-the-portal"></a>Üzenetek útválasztásának megtekintése a portálon

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította az erőforrásokat és beállította az üzenetútvonalat, a következő oktatóanyagból megtudhatja, hogyan küldhet üzeneteket az IoT Hubra, és hogyan irányíthatja át őket a különböző célhelyre. 

> [!div class="nextstepaction"]
> [2. rész – Az üzenetek útválasztási eredményeinek megtekintése](tutorial-routing-view-message-routing-results.md)
