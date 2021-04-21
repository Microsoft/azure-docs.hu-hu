---
title: Ismétlődő üzenetek észlelésének engedélyezése – Azure Service Bus
description: Ez a cikk bemutatja, hogyan engedélyezheti a duplikált üzenetek észlelését Azure Portal, a PowerShell, a parancssori felület és a programozási nyelvek (C#, Java, Python és JavaScript) használatával.
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 708009fcf2479660316b38ac0b7d545d450de28c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755211"
---
# <a name="enable-duplicate-message-detection-for-an-azure-service-bus-queue-or-a-topic"></a>Ismétlődő üzenetek észlelésének engedélyezése egy Azure Service Bus üzenetsorhoz vagy témakörhez
Ha engedélyezi a duplikált üzenetek észlelését egy üzenetsorhoz vagy témakörhez, a Azure Service Bus adott ideig megőrzi az üzenetsorba vagy témakörbe küldött összes üzenet előzményeit. Ebben az időszakban az üzenetsor vagy témakör nem tárol ismétlődő üzeneteket. Ennek a tulajdonságnak az engedélyezése pontosan egyszer biztosítja a kézbesítést egy felhasználó által meghatározott időtartamon belül. További információ: [Duplikált elemek észlelése.](duplicate-detection.md) Ez a cikk különböző módszereket mutat be az ismétlődő üzenetek észlelésének engedélyezésére egy Service Bus üzenetsor vagy témakör számára. 


> [!NOTE]
> - A szolgáltatás alapszintű Service Bus nem támogatja a duplikált elemek észlelését. A standard és a prémium szint támogatja a duplikált elemek észlelését. A csomagok közötti különbségeket a [díjszabást Service Bus tekintse meg.](https://azure.microsoft.com/pricing/details/service-bus/)
> - Az üzenetsor vagy témakör létrehozása után nem engedélyezheti vagy tilthatja le az ismétlődések észlelését. Ezt csak az üzenetsor vagy témakör létrehozásakor lehet megtenni. 

## <a name="using-azure-portal"></a>Az Azure Portal használata
Amikor **üzenetsort hoz létre** a Azure Portal válassza az **Ismétlődések** észlelésének engedélyezése lehetőséget az alábbi képen látható módon. Az ismétlődő észlelési ablak méretét üzenetsor vagy témakör létrehozásakor konfigurálhatja. 

:::image type="content" source="./media/enable-duplicate-detection/create-queue.png" alt-text="Ismétlődések észlelésének engedélyezése az üzenetsor létrehozásakor":::

Amikor témakört hoz létre a Azure Portal válassza az **Ismétlődések** észlelésének engedélyezése lehetőséget az alábbi képen látható módon. 

:::image type="content" source="./media/enable-duplicate-detection/create-topic.png" alt-text="Ismétlődések észlelésének engedélyezése a témakör létrehozásakor":::

Ezt a beállítást meglévő üzenetsorhoz vagy témakörhez is konfigurálhatja, ha a létrehozáskor engedélyezte az ismétlődések észlelését. 

### <a name="update-duplicate-detection-window-size-for-an-existing-queue-or-a-topic"></a>Duplikátálásészlelési ablak méretének frissítése meglévő üzenetsorhoz vagy témakörhez
Egy meglévő üzenetsor vagy témakör duplikált észlelési  ablakának méretének módosításához az Áttekintés lapon válassza a Módosítás lehetőséget a Duplikátálás **észlelése ablakban.**   

#### <a name="queue"></a>Üzenetsor
:::image type="content" source="./media/enable-duplicate-detection/window-size.png" alt-text="Ismétlődő észlelési ablak méretének beállítása egy üzenetsorhoz":::

#### <a name="topic"></a>Témakör
:::image type="content" source="./media/enable-duplicate-detection/window-size-topic.png" alt-text="Ismétlődő észlelési ablak méretének beállítása egy témakörben":::


## <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Ha **olyan üzenetsort hoz létre, amelyben engedélyezve** van a duplikált elemek észlelése, használja a parancsot a [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-duplicate-detection` `true` beállítással. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

Ha **olyan témakört hoz létre, amelyben engedélyezve** van a duplikált elemek észlelése, használja a parancsot a [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) `--enable-duplicate-detection` `true` beállításával.

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

A fenti példák a duplikált elemek észlelési ablakának méretét is beállítják a paraméter `--duplicate-detection-history-time-window` használatával. Az ablak mérete egy napra van állítva. Az alapértelmezett érték 10 perc, a maximálisan megengedett érték pedig hét nap.

Az **üzenetsor új észlelési ablakmérettel való frissítéséhez** használja az [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) parancsot a `--duplicate-detection-history-time-window` paraméterrel. Ebben a példában az ablakméret hét napra frissül. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```

Hasonlóképpen, egy témakör új észlelési ablakmérettel való **frissítéséhez** használja a parancsot [`az servicebus topic update`](/cli/azure/servicebus/topic#az_servicebus_topic_update) a `--duplicate-detection-history-time-window` paraméterrel. Ebben a példában az ablakméret hét napra frissül.

```azurecli-interactive
az servicebus topic update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```
 
## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Ha **olyan üzenetsort hoz létre, amelyben engedélyezve** van a duplikált elemek észlelése, használja a parancsot a [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-RequiresDuplicateDetection` `$True` beállítással. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresDuplicateDetection $True `
    -DuplicateDetectionHistoryTimeWindow P1D
```

Ha **olyan témakört hoz létre, amelyben engedélyezve** van a duplikált elemek észlelése, használja a parancsot a [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) `-RequiresDuplicateDetection` `true` beállításával. 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -RequiresDuplicateDetection $True
    -DuplicateDetectionHistoryTimeWindow P1D
```

A fenti példák a duplikált elemek észlelési ablakának méretét is beállítják a paraméter `-DuplicateDetectionHistoryTimeWindow` használatával. Az ablak mérete egy napra van állítva. Az alapértelmezett érték 10 perc, a maximálisan megengedett érték pedig hét nap.

Az **üzenetsor új észlelési ablakmérettel való frissítéséhez** tekintse meg az alábbi példát.  Ebben a példában az ablakméret hét napra frissül.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
```

A **témakör új észlelési ablakmérettel való frissítéséhez** tekintse meg az alábbi példát. Ebben a példában az ablakméret hét napra frissül.

```azurepowershell-interactive
$topic=Get-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic

$topic.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -TopicObj $topic
```

## <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablonok használata
Ha **olyan üzenetsort hoz létre, amelyben engedélyezve** van a duplikált elemek észlelése, állítsa a beállítást a következőre az `requiresDuplicateDetection` `true` üzenetsor tulajdonságai szakaszban: . További információ: [Microsoft.ServiceBus névterek/üzenetsorok sablonreferenciája.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) Adja meg a tulajdonság `duplicateDetectionHistoryTimeWindow` értékét a duplikált észlelési ablak méretének beállítására. A következő példában ez egy napra van beállítva.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.ServiceBus/namespaces",
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('serviceBusNamespaceName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "type": "Queues",
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}

```

Ha **olyan témakört hoz létre, amelyben engedélyezve van az ismétlődésészlelés,** állítsa be a következőt a `requiresDuplicateDetection` témakör tulajdonságai `true` szakaszban: . További információ: [Microsoft.ServiceBus névterek/témakörök sablonreferenciája.](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json) 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "service_BusNamespace_Name": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusTopicName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Topic"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('service_BusNamespace_Name')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusTopicName')]",
          "type": "topics",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces/', parameters('service_BusNamespace_Name'))]"
          ],
          "properties": {
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>Következő lépések
Próbálja ki a mintákat a választott nyelven, és fedezze fel Azure Service Bus funkcióit. 

- [Azure Service Bus java-ügyféloldali kódtárminták](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus Pythonhoz készült ügyféloldali kódtárminták](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus JavaScripthez való ügyféloldali kódtárminták](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus TypeScript ügyféloldali kódtárminái](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Azure.Messaging.ServiceBus-minták a .NET-hez](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Az alábbiakban példákat talál a régebbi .NET- és Java-ügyfélkódtárakhoz:
- [Microsoft.Azure.ServiceBus-minták a .NET-hez](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [azure-servicebus-minták Javához](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)