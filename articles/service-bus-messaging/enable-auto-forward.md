---
title: Automatikus továbbítás engedélyezése a Azure Service Bus üzenetsorok és előfizetések számára
description: Ez a cikk azt ismerteti, hogyan engedélyezheti az üzenetsorok és előfizetések automatikus továbbítását az Azure Portal, a PowerShell, a parancssori felület és a programozási nyelvek (C#, Java, Python és JavaScript) használatával
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: ef22ae08485dc896c94858db4e422cf89a00ec1f
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755283"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>Automatikus továbbítás engedélyezése a Azure Service Bus üzenetsorok és előfizetések számára
Az Service Bus automatikus továbbítási funkció lehetővé teszi egy üzenetsor vagy előfizetés egy másik üzenetsorhoz vagy témakörhez való láncolását, amely ugyanannak a névtérnek a része. Ha az automatikus továbbítás engedélyezve van, a Service Bus automatikusan eltávolítja az első üzenetsorba vagy előfizetésbe (forrás) helyezett üzeneteket, és a második üzenetsorba vagy témakörbe (cél) helyezi őket. Továbbra is küldhet üzenetet közvetlenül a célentitásnak. További információ: [Láncolás Service Bus entitások automatikus továbbítással.](service-bus-auto-forwarding.md) Ez a cikk az automatikus továbbítás engedélyezésének különböző módjait mutatja be Service Bus üzenetsorok és előfizetések számára. 

> [!IMPORTANT]
> A szolgáltatás alapszintű Service Bus nem támogatja az automatikus továbbítási funkciót. A standard és a prémium szint támogatja ezt a funkciót. A tarifacsomagok közötti különbségekért lásd: [Service Bus díjszabása.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="using-azure-portal"></a>Az Azure Portal használata
Amikor **üzenetsort** vagy előfizetést hoz létre **egy** témakörhez a Azure Portal válassza az Üzenetek továbbítása üzenetsorba/témakörbe lehetőséget az alábbi példákban látható módon.  Ezután adja meg, hogy szeretné-e az üzeneteket egy üzenetsorba vagy témakörbe küldeni. Ebben a példában az **Üzenetsor** lehetőség van kiválasztva, és egy üzenetsor **(myqueue**) ugyanattól a névtértől.

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>Üzenetsor létrehozása engedélyezett automatikus továbbítással
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="Automatikus továbbítás engedélyezése az üzenetsor létrehozásakor":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>Előfizetés létrehozása olyan témakör számára, amely számára engedélyezve van az automatikus továbbítás
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="Automatikus továbbítás engedélyezése az előfizetés létrehozásakor":::

## <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Ha **olyan üzenetsort** szeretne létrehozni, amelyen engedélyezve van az automatikus továbbítás, használja a parancsot, amelynek beállítása az üzenetsor vagy témakör nevére van állítva, amelyre az üzeneteket [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--forward-to` szeretné küldeni. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

Ha **olyan témakörre** szeretne feliratkozni, amelyen engedélyezve van az automatikus továbbítás, használja a parancsot, amelynek beállítása az üzenetsor vagy témakör nevére van állítva, amelyre az üzeneteket [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) `--forward-to` szeretné küldeni.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Ha **olyan üzenetsort** szeretne létrehozni, amelyen engedélyezve van az automatikus továbbítás, használja az parancsot, amelynek beállítása az üzenetsor vagy témakör nevére van állítva, amelyre az üzeneteket [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-ForwardTo` szeretné küldeni. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -ForwardTo myqueue2
```

Ha **olyan témakörre** szeretne feliratkozni, amelyen engedélyezve van az automatikus továbbítás, használja a parancsot, amelynek beállítása az üzenetsor vagy témakör nevére van állítva, amelyre az üzeneteket [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) `-ForwardTo` szeretné küldeni.

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

## <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablonok használata
Ha **olyan üzenetsort** szeretne létrehozni, amelyen engedélyezve van az automatikus továbbítás, állítsa be az üzenetsor tulajdonságai szakaszban annak az üzenetsornak vagy témakörnek a nevét, amelybe az üzeneteket `forwardTo` szeretné küldeni. További információ: [Microsoft.ServiceBus névterek/üzenetsorok sablonreferenciája.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "forwardTo": "myqueue2"
          }
        }
      ]
    }
  ]
}

```

Ha **olyan témakörre** szeretne feliratkozni, amelyen engedélyezve van az automatikus továbbítás, állítsa az üzenetsor tulajdonságainak szakaszában az üzenetsor vagy témakör nevére, amelyre az üzeneteket szeretné `forwardTo` küldeni. További információkért lásd: [Microsoft.ServiceBus névterek/témakörök/előfizetések sablonreferenciája.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json) 

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
                "forwardTo": "myqueue2"
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