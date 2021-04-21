---
title: Üzenet-Azure Service Bus engedélyezése a | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan engedélyezhetők az üzenet-munkamenetek a Azure Portal, a PowerShell, a parancssori felület és a programozási nyelvek (C#, Java, Python és JavaScript) használatával
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: dc2667b746a57c7f2e4ac5faec88c4540bed1180
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755208"
---
# <a name="enable-message-sessions-for-an-azure-service-bus-queue-or-a-subscription"></a>Üzenet-munkamenetek engedélyezése egy Azure Service Bus üzenetsorhoz vagy előfizetéshez
Azure Service Bus munkamenetek lehetővé teszik a kapcsolódó üzenetek kötetlen sorozatának együttes és rendezett kezelését. A munkamenetek első be- és **lekéréses (FIFO)** és **kérés-válasz mintákban használhatók.** További információ: [Üzenet-munkamenetek.](message-sessions.md) Ez a cikk bemutatja, hogyan engedélyezheti a munkameneteket egy adott Service Bus üzenetsorhoz vagy előfizetéshez. 

> [!IMPORTANT]
> - A szolgáltatás alapszintű Service Bus nem támogatja a munkameneteket. A standard és a prémium szint támogatja a munkameneteket. A tarifacsomagok közötti különbségekért lásd: [Service Bus díjszabása.](https://azure.microsoft.com/pricing/details/service-bus/)
> - Az üzenetsor vagy előfizetés létrehozása után nem engedélyezheti vagy tilthatja le az üzenet-munkameneteket. Ezt csak az üzenetsor vagy az előfizetés létrehozásakor lehet megtenni. 

## <a name="using-azure-portal"></a>Az Azure Portal használata
Amikor létrehoz egy **üzenetsort** a Azure Portal válassza a **Munkamenetek engedélyezése** lehetőséget az alábbi képen látható módon. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Munkamenet engedélyezése az üzenetsor létrehozásakor":::

Amikor feliratkozik egy témakörre a Azure Portal válassza a **Munkamenetek** engedélyezése lehetőséget az alábbi képen látható módon. 

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Munkamenet engedélyezése az előfizetés létrehozásakor":::

## <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Ha **üzenetsort hoz létre engedélyezett üzenet-munkamenetekkel,** használja a parancsot a [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-session` `true` beállítással.

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-session true
```

Ha **olyan témakörre vonatkozó előfizetést** hoz létre, amely engedélyezett üzenet-munkameneteket tartalmaz, használja a parancsot a [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) `--enable-session` `true` beállításával.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-session true
```

## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Ha **üzenetsort hoz létre engedélyezett üzenet-munkamenetekkel,** használja a parancsot a [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-RequiresSession` `$True` beállítással. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresSession $True
```

Ha **olyan témakörre vonatkozó előfizetést** hoz létre, amely engedélyezett üzenet-munkameneteket tartalmaz, használja a parancsot a [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) `-RequiresSession` `true` beállításával. 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -RequiresSession $True
```

## <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablonok használata
Ha **üzenetsort hoz létre engedélyezett üzenet-munkamenetekkel,** állítsa a beállítást a következőre `requiresSession` az `true` üzenetsor tulajdonságai szakaszban: . További információ: [Microsoft.ServiceBus névterek/üzenetsorok sablonreferenciája.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "requiresSession": true
          }
        }
      ]
    }
  ]
}

```

Ha **olyan témakörre vonatkozó előfizetést** hoz létre, amely engedélyezett üzenet-munkameneteket tartalmaz, állítsa a beállítást a következőre az `requiresSession` előfizetés tulajdonságai `true` szakaszban: . További információkért lásd: [Microsoft.ServiceBus névterek/témakörök/előfizetések sablonreferenciája.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json) 

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
    "serviceBusSubscriptionName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Subscription"
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
            "maxSizeInMegabytes": 1024
          },
          "resources": [
            {
              "apiVersion": "2017-04-01",
              "name": "[parameters('serviceBusSubscriptionName')]",
              "type": "Subscriptions",
              "dependsOn": [
                "[parameters('serviceBusTopicName')]"
              ],
              "properties": {
                "requiresSession": true
              }
            }
          ]
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