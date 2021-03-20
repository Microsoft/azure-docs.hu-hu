---
title: Oktatóanyag – az üzenetsor-útválasztás konfigurálása az Azure IoT Hubhoz Azure PowerShell
description: Oktatóanyag – az üzenetsor-útválasztás konfigurálása az Azure IoT Hub Azure PowerShell használatával. Az üzenet tulajdonságaitól függően egy Storage-fiókra vagy egy Service Bus-várólistára kell irányítani.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: be1560bcc03ec7a26f4bc374392c746243cd731a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624072"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Oktatóanyag: a Azure PowerShell használata IoT Hub üzenet-útválasztás konfigurálásához

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>A parancsfájl letöltése (opcionális)

Az oktatóanyag második részében le kell töltenie és futtatnia kell egy Visual Studio-alkalmazást, amely üzeneteket küld a IoT Hub. A letöltésben egy olyan mappa található, amely tartalmazza a Azure Resource Manager sablont és a paramétereket tartalmazó fájlt, valamint az Azure CLI-és PowerShell-parancsfájlokat. 

Ha meg szeretné tekinteni a kész parancsfájlt, töltse le az [Azure IoT C#-mintákat](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Bontsa ki a master.zip fájlt. Az Azure CLI-szkript **iothub_routing_psh.ps1**/IOT-hub/tutorials/Routing/SimulatedDevice/Resources/van.

## <a name="create-your-resources"></a>Erőforrások létrehozása

Először hozza létre az erőforrásokat a PowerShell használatával.

### <a name="use-powershell-to-create-your-base-resources"></a>Alaperőforrások létrehozása a PowerShell használatával

Másolja és illessze be az alábbi szkriptet Cloud Shellba, majd nyomja le az ENTER billentyűt. A parancsfájl egyszerre egy sort futtat. A szkript ezen első szakasza létrehozza az oktatóanyag alaperőforrásait, beleértve a Storage-fiókot, a IoT Hub, a Service Bus névteret és a Service Bus üzenetsor-t. Az oktatóanyag során másolja az egyes parancsfájlokat, és illessze be Cloud Shellba a futtatásához.

Több olyan erőforrás neve van, amelynek globálisan egyedinek kell lennie, például a IoT Hub neve és a Storage-fiók neve. Ennek egyszerűbbé tétele érdekében az erőforrásnevek egy *randomValue* nevű véletlenszerű alfanumerikus értékkel vannak hozzáfűzve. A rendszer egyszer hozza létre a randomValue a parancsfájl tetején, és szükség szerint hozzáfűzi az erőforrások nevét a parancsfájlban. Ha nem szeretné, hogy véletlenszerű legyen, beállíthatja egy üres sztringre vagy egy adott értékre. 

> [!IMPORTANT]
> A kezdeti parancsfájlban beállított változókat az útválasztási parancsfájl is használja, ezért futtassa az összes parancsfájlt ugyanabban a Cloud Shell-munkamenetben. Ha új munkamenetet nyit meg az Útválasztás beállítására szolgáló parancsfájl futtatásához, akkor a változók közül számos érték hiányzik. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Szimulált eszköz létrehozása

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Most, hogy beállította az alaperőforrásokat, beállíthatja az üzenet-útválasztást.

## <a name="set-up-message-routing"></a>Üzenetek útválasztásának beállítása

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Útválasztási végpont létrehozásához használja az [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). A végpont üzenetküldési útvonalának létrehozásához használja az [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Útvonal egy Storage-fiókhoz 

Először állítsa be a tárolási fiók végpontját, majd hozza létre az üzenet útvonalát.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Ezek a szkriptek által használt változók, amelyeket a Cloud Shell-munkameneten belül kell beállítani:

**resourceGroup**: ennek a mezőnek két előfordulása van – mindkettőt beállíthatja az erőforráscsoporthoz.

**név**: Ez a mező annak a IoT hub a neve, amelyre az Útválasztás érvényes lesz.

**végpontneve**: Ez a mező a végpontot azonosító név. 

**endpointType**: Ez a mező a végpont típusát adja meg. Ezt az értéket a,, `azurestoragecontainer` `eventhub` `servicebusqueue` , vagy a értékre kell beállítani `servicebustopic` . Itt adja meg a kívánt célokat `azurestoragecontainer` .

**subscriptionID**: Ez a mező az Azure-fiókhoz tartozó subscriptionID van beállítva.

**storageConnectionString**: ezt az értéket az előző parancsfájlban beállított Storage-fiókból kéri le a rendszer. Ezt az Útválasztás használja a Storage-fiók eléréséhez.

**containerName**: Ez a mező annak a tárolónak a neve, amelybe az adatírás történik.

**Kódolás**: ezt a mezőt a vagy a értékre kell beállítani `AVRO` `JSON` . Ez kijelöli a tárolt adatmennyiség formátumát. Az alapértelmezett érték a AVRO.

**z**: Ez a mező a beállított útvonal neve. 

**feltétel**: Ez a mező a végpontnak küldött üzenetek szűrésére szolgáló lekérdezés. A tárolóba átirányított üzenetek lekérdezési feltétele `level="storage"` .

**engedélyezve**: Ez a mező alapértelmezés szerint a (z) értékre van beállítva `true` , ami azt jelzi, hogy a létrehozás után engedélyezni kell az üzenet útvonalát.

Másolja ezt a szkriptet, és illessze be a Cloud Shell ablakába.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

A következő lépés a Storage-fiók útválasztási végpontjának létrehozása. Megadhatja azt a tárolót is, amelyben az eredményeket tárolni fogja. A tároló létre lett hozva a Storage-fiók létrehozásakor.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Ezután hozza létre a tárolási végponthoz tartozó üzenet útvonalát. Az üzenet útvonala azt jelöli, hogy hová kell elküldeni a lekérdezési specifikációnak megfelelő üzeneteket.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Útvonal Service Bus üzenetsor felé

Most állítsa be az útválasztást a Service Bus-üzenetsorhoz. A Service Bus üzenetsor kapcsolódási karakterláncának beolvasásához létre kell hoznia egy engedélyezési szabályt, amely rendelkezik a megfelelő jogosultságokkal. A következő szkript létrehoz egy engedélyezési szabályt a nevű Service Bus üzenetsor számára `sbauthrule` , és beállítja a jogosultságokat `Listen Manage Send` . Ha ezt az engedélyezési szabályt beállította, a segítségével lekérheti a várólista kapcsolódási karakterláncát.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Most az engedélyezési szabály segítségével kérje le az Service Bus üzenetsor kulcsát. Ez az engedélyezési szabály a parancsfájlban később a kapcsolódási karakterlánc lekérésére szolgál.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Most állítsa be az útválasztási végpontot és a Service Bus üzenetsor üzenetének útvonalát. Ezek a szkriptek által használt változók, amelyeket a Cloud Shell-munkameneten belül kell beállítani:

**végpontneve**: Ez a mező a végpontot azonosító név. 

**endpointType**: Ez a mező a végpont típusát adja meg. Ezt az értéket a,, `azurestoragecontainer` `eventhub` `servicebusqueue` , vagy a értékre kell beállítani `servicebustopic` . Itt adja meg a kívánt célokat `servicebusqueue` .

**z**: Ez a mező a beállított útvonal neve. 

**feltétel**: Ez a mező a végpontnak küldött üzenetek szűrésére szolgáló lekérdezés. Az Service Bus várólistára átirányított üzenetek lekérdezési feltétele `level="critical"` .

Itt látható a Service Bus üzenetsor üzenet-útválasztási Azure PowerShell.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Üzenet-útválasztás megtekintése a portálon

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította az erőforrásokat, és konfigurálta az üzenet útvonalait, folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan küldhet üzeneteket az IoT hubhoz, és hogyan irányíthatja őket át a különböző célhelyekre. 

> [!div class="nextstepaction"]
> [2. rész – az üzenetek útválasztási eredményeinek megtekintése](tutorial-routing-view-message-routing-results.md)
