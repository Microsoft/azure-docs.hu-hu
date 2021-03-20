---
title: Oktatóanyag – az üzenetsor-útválasztás konfigurálása az Azure IoT Hub Azure Resource Manager sablon használatával
description: Oktatóanyag – az üzenetsor-útválasztás konfigurálása az Azure IoT Hub Azure Resource Manager sablon használatával
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 53217340b0d91f3de77e5e0d8c0a82e30599d6ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98621428"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Oktatóanyag: Azure Resource Manager-sablon használata IoT Hub üzenet-útválasztás konfigurálásához

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Üzenetek útválasztása

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>A sablon és a paraméterek fájl letöltése

Az oktatóanyag második részében le kell töltenie és futtatnia kell egy Visual Studio-alkalmazást, amely üzeneteket küld a IoT Hub. A letöltésben egy olyan mappa található, amely tartalmazza a Azure Resource Manager sablont és a paramétereket tartalmazó fájlt, valamint az Azure CLI-t és a PowerShell-parancsfájlokat.

Most töltse le az [Azure IoT C#-mintákat](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) . Bontsa ki a master.zip fájlt. A Resource Manager-sablon és a Parameters fájl a/iot-hub/Tutorials/Routing/SimulatedDevice/resources/-ben **template_iothub.jsbe** -és **template_iothub_parameters.js**.

## <a name="create-your-resources"></a>Erőforrások létrehozása

Az összes erőforrás létrehozásához egy Azure Resource Manager (RM) sablont fog használni. Az Azure CLI és a PowerShell-parancsfájlok egyszerre több sort is futtathatnak. Az RM-sablonok egyetlen lépésben települnek. Ebből a cikkből megtudhatja, hogy az egyes részeket hogyan lehet megérteni. Ezután bemutatja, hogyan helyezheti üzembe a sablont, és hogyan hozhatja létre a virtuális eszközt teszteléshez. A sablon üzembe helyezését követően megtekintheti az üzenet-útválasztási konfigurációt a portálon.

Több olyan erőforrás neve van, amelynek globálisan egyedinek kell lennie, például a IoT Hub neve és a Storage-fiók neve. Az erőforrások könnyebb elnevezéséhez az adott erőforrás neveit úgy állítja be, hogy az aktuális dátum/idő alapján generált véletlenszerű alfanumerikus értéket fűzze hozzá. 

Ha megtekinti a sablont, látni fogja, hol vannak beállítva változók azokhoz az erőforrásokhoz, amelyek az átadott paramétert fogadják, és összefűzik a *randomValue* a paraméterrel. 

A következő szakasz a használt paramétereket ismerteti.

### <a name="parameters"></a>Paraméterek

Ezeknek a paramétereknek a többsége alapértelmezett értéket tartalmaz. A **_in** végződésű eszközök a *randomValue* -vel vannak összefűzve, hogy globálisan egyediek legyenek. 

**randomValue**: ez az érték a sablon központi telepítésekor a jelenlegi dátum/idő alapján jön létre. Ez a mező nem szerepel a Parameters fájlban, mert maga a sablon hozza létre.

**subscriptionId**: Ez a mező arra az előfizetésre van beállítva, amelybe a sablont telepíti. Ez a mező nem szerepel a Parameters fájlban, mert az be van állítva.

**IoTHubName_in**: Ez a mező az alap IoT hub neve, amely globálisan egyedi randomValue van fűzve.

**hely**: Ez a mező az az Azure-régió, amelyben üzembe helyezi, például "westus".

**consumer_group**: Ez a mező az útválasztási végponton keresztül érkező üzenetek fogyasztói csoportját határozza meg. A Azure Stream Analytics eredményeinek szűrésére szolgál. Például van egy teljes stream, ahol mindent megtudhat, vagy ha a consumer_groupt a **contoso**-ra beállított adatokkal, akkor beállíthat egy Azure stream Analytics streamet (és Power bi jelentést), amely csak ezeket a bejegyzéseket jeleníti meg. Ezt a mezőt az oktatóanyag 2. része használja.

**sku_name**: Ez a mező a IoT hub skálázása. Ez az érték csak S1 vagy újabb lehet. Ebben az oktatóanyagban az ingyenes szint nem működik, mert több végpontot nem engedélyez.

**sku_units**: ebben a mezőben a **sku_name** látható, a szám pedig a felhasználható IoT hub egységek számát.

**d2c_partitions**: Ez a mező az esemény-adatfolyamhoz használt partíciók száma.

**storageAccountName_in**: Ez a mező a létrehozandó Storage-fiók neve. Az üzenetek a Storage-fiókban lévő tárolóba vannak irányítva. A mező összefűzése a randomValue, hogy globálisan egyedi legyen.

**storageContainerName**: Ez a mező annak a tárolónak a neve, amelyben a Storage-fiókba továbbított üzenetek tárolódnak.

**storage_endpoint**: Ez a mező az üzenet-útválasztás által használt Storage-fiók végpontjának neve.

**service_bus_namespace_in**: Ez a mező a létrehozandó Service Bus névtér neve. Ez az érték a randomValue van összefűzve, hogy globálisan egyedi legyen.

**service_bus_queue_in**: Ez a mező az útválasztási üzenetekhez használt Service Bus üzenetsor neve. Ez az érték a randomValue van összefűzve, hogy globálisan egyedi legyen.

**AuthRules_sb_queue**: Ez a mező a Service Bus-várólista engedélyezési szabályai, amely a várólista kapcsolódási karakterláncának lekérésére szolgál.

### <a name="variables"></a>Változók

Ezek az értékek a sablonban használatosak, és többnyire paraméterekből származnak.

**queueAuthorizationRuleResourceId**: Ez a mező a Service Bus üzenetsor engedélyezési szabályának ResourceId. A ResourceId a várólista kapcsolódási karakterláncának beolvasására szolgál.

**iotHubName**: Ez a mező a IoT hub neve a randomValue összefűzése után. 

**storageAccountName**: Ez a mező a randomValue összefűzése után a Storage-fiók neve. 

**service_bus_namespace**: Ez a mező a névtér a randomValue összefűzése után.

**service_bus_queue**: Ez a mező a Service Bus üzenetsor neve, miután összefűzött egy randomValue.

**sbVersion**: a használni kívánt Service Bus API verziója. Ebben az esetben az "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Erőforrások: Storage-fiók és-tároló

Az első létrehozott erőforrás a Storage-fiók, valamint azt a tárolót, amelyhez az üzenetek irányítva vannak. A tároló egy erőforrás a Storage-fiókban. Rendelkezik egy `dependsOn` záradékkal a Storage-fiókhoz, amely megköveteli a Storage-fiók létrehozását a tároló előtt.

A szakasz a következőképpen néz ki:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Erőforrások: Service Bus névtér és üzenetsor

A második erőforrás a Service Bus névtér, valamint az a Service Bus-várólista, amelyhez az üzenetek továbbítása történik. Az SKU a standard értékre van beállítva. Az API-verziót a rendszer beolvassa a változók közül. Azt is beállítja, hogy aktiválja a Service Bus névteret a szakasz telepítésekor (állapot: aktív). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

Ez a szakasz létrehozza a Service Bus várólistát. A szkript ezen részében van egy `dependsOn` záradék, amely biztosítja, hogy a névtér a várólista előtt legyen létrehozva.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Erőforrások: IOT hub és üzenet-útválasztás

Most, hogy létrejött a Storage-fiók és a Service Bus üzenetsor, létrehozhatja az üzeneteket átirányító IoT Hub. Az RM-sablon `dependsOn` záradékokat használ, ezért nem próbálja meg létrehozni a hubot a Service Bus erőforrásai és a Storage-fiók létrehozása előtt. 

Itt látható az IoT Hub szakasz első része. A sablon ezen része beállítja a függőségeket, és elindítja a tulajdonságokat.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

A következő szakasz az IOT hub üzenet-útválasztási konfigurációjának szakasza. Az első a végpontok szakasza. A sablon ezen része beállítja a Service Bus üzenetsor és a Storage-fiók útválasztási végpontját, beleértve a kapcsolati karakterláncokat is.

A várólista kapcsolódási karakterláncának létrehozásához szükség van a queueAuthorizationRulesResourcedId, amelyet a rendszer beolvasott. A Storage-fiókhoz tartozó kapcsolódási karakterlánc létrehozásához le kell kérnie az elsődleges tároló kulcsát, majd azt a kapcsolódási karakterlánc formátumában kell használni.

A végpont konfigurációját is be kell állítani a vagy a blob-formátumra `AVRO` `JSON` .

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

Ez a következő szakasz a végpontokhoz tartozó üzenet-útvonalakat tartalmazza. Mindegyik végponthoz egy beállítás van beállítva, így a Service Bus üzenetsor és a Storage-fiók tárolója közül egyet is megadhat.

Ne feledje, hogy a tárolóba átirányított üzenetek lekérdezési feltétele `level="storage"` , valamint az Service Bus várólistára átirányított üzenetek lekérdezési feltétele `level="critical"` .

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Ez a JSON a IoT Hub további szakaszát mutatja, amely tartalmazza az alapértelmezett adatokat és a hub SKU-át.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Erőforrások: Service Bus üzenetsor-engedélyezési szabályok

A Service Bus várólista-engedélyezési szabály a Service Bus üzenetsor kapcsolódási karakterláncának beolvasására szolgál. Egy `dependsOn` záradékot használ annak biztosítására, hogy a rendszer ne hozza létre a Service Bus névtér és a Service Bus üzenetsor előtt.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Erőforrások: fogyasztói csoport

Ebben a szakaszban létrehoz egy fogyasztói csoportot az oktatóanyag második részében a Azure Stream Analytics által használandó IoT Hub-adatmennyiséghez.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Erőforrások: kimenetek

Ha vissza szeretne állítani egy értéket a megjelenítendő üzembehelyezési parancsfájlnak, használja a kimenet szakaszt. A sablon ezen része a Service Bus üzenetsor kapcsolati karakterláncát adja vissza. Egy érték visszaadása nem kötelező, ez például azt mutatja be, hogyan lehet eredményeket visszaadni a hívó parancsfájlnak.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Az RM-sablon üzembe helyezése

Ha a sablont az Azure-ba szeretné telepíteni, töltse fel a sablont és a paramétereket tartalmazó fájlt Azure Cloud Shellre, majd hajtson végre egy parancsfájlt a sablon üzembe helyezéséhez. Nyissa meg Azure Cloud Shell és jelentkezzen be. Ez a példa a PowerShellt használja.

A fájlok feltöltéséhez válassza a menüsávon a **fájlok feltöltése/letöltése** ikont, majd válassza a feltöltés lehetőséget.

![A fájlok feltöltése/letöltése ikonra kiemelő képernyőkép.](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

A fájlkezelővel megkeresheti a helyi lemezen található fájlokat, majd kiválaszthatja azokat, majd a **Megnyitás** lehetőséget választva.

A fájlok feltöltése után az eredmények párbeszédpanel az alábbi képhez hasonló módon jelenik meg.

![Kiemelt feltöltési/letöltési eredményekkel rendelkező menüsáv Cloud Shell](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

A rendszer feltölti a fájlokat a Cloud Shell-példány által használt megosztásra. 

Futtassa a parancsfájlt az üzemelő példány végrehajtásához. A parancsfájl utolsó sora a visszaadott változót kérdezi le, a Service Bus üzenetsor-kapcsolódási karakterláncot.

A szkript beállítja és ezeket a változókat használja:

**$RGName** a sablon üzembe helyezéséhez használt erőforráscsoport-név. Ez a mező a sablon telepítése előtt jön létre.

**$Location** a sablonhoz használandó Azure-beli hely, például "westus".

a **deploymentname** egy, a központi telepítéshez hozzárendelt név a visszaadott változó értékének lekéréséhez.

Itt látható a PowerShell-szkript. Másolja ezt a PowerShell-parancsfájlt, és illessze be a Cloud Shell ablakába, majd futtassa az ENTER billentyűt a futtatásához.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Ha parancsfájl-hibák léptek fel, a parancsfájlt helyileg szerkesztheti, újra feltöltheti a Cloud Shellba, majd újra futtathatja a szkriptet. Ha a parancsfájl futtatása sikeresen befejeződött, folytassa a következő lépéssel.

## <a name="create-simulated-device"></a>Szimulált eszköz létrehozása

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Üzenet-útválasztás megtekintése a portálon

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy rendelkezik az összes beállított erőforrással, és az üzenetek útvonalai konfigurálva vannak, folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan dolgozza fel és jelenítse meg az átirányított üzenetekkel kapcsolatos információkat.

> [!div class="nextstepaction"]
> [2. rész – az üzenetek útválasztási eredményeinek megtekintése](tutorial-routing-view-message-routing-results.md)
