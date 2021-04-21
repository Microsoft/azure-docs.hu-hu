---
title: Automatikus továbbítás engedélyezése a Azure Service Bus üzenetsorok és előfizetések számára
description: Ez a cikk azt ismerteti, hogyan engedélyezheti az üzenetsorok és előfizetések automatikus továbbítását a Azure Portal, a PowerShell, a parancssori felület és a programozási nyelvek (C#, Java, Python és JavaScript) használatával.
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: e5f69a82aac96deaf96f0ae6aaa1a26d0ee3aaf3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814671"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>Automatikus továbbítás engedélyezése a Azure Service Bus üzenetsorok és előfizetések számára
Az Service Bus automatikus továbbítási funkció lehetővé teszi egy üzenetsor vagy előfizetés egy másik üzenetsorhoz vagy témakörhez való láncolását, amely ugyanannak a névtérnek a része. Ha az automatikus továbbítás engedélyezve van, a Service Bus automatikusan eltávolítja az első üzenetsorba vagy előfizetésbe (forrás) helyezett üzeneteket, és elhelyezi őket a második üzenetsorba vagy témakörbe (cél). Továbbra is lehetséges üzenetet küldeni közvetlenül a célentitásnak. További információ: [Láncolás Service Bus automatikus továbbítással.](service-bus-auto-forwarding.md) Ez a cikk különböző módszereket mutat be a különböző üzenetsorok és előfizetések automatikus Service Bus engedélyezéséhez. 

> [!IMPORTANT]
> A szolgáltatás alapszintű Service Bus nem támogatja az automatikus továbbítás funkciót. A standard és a prémium szint támogatja ezt a funkciót. A csomagok közötti különbségeket a [díjszabást Service Bus tekintse meg.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="using-azure-portal"></a>Az Azure Portal használata
Amikor **üzenetsort** vagy előfizetést hoz létre **egy** témakör számára a Azure Portal válassza az Üzenetek továbbítása üzenetsorba/témakörbe lehetőséget az alábbi példákban látható módon.  Ezután adja meg, hogy szeretné-e az üzeneteket egy üzenetsorba vagy témakörbe küldeni. Ebben a példában az **Üzenetsor** lehetőség van kiválasztva, és egy ugyanannak a névtérnek az üzenetsora.

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>Üzenetsor létrehozása engedélyezett automatikus továbbítással
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="Automatikus továbbítás engedélyezése az üzenetsor létrehozásakor":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>Előfizetés létrehozása olyan témakör számára, amely számára engedélyezve van az automatikus továbbítás
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="Automatikus továbbítás engedélyezése az előfizetés létrehozásakor":::

### <a name="update-the-auto-forward-setting-for-an-existing-queue"></a>Meglévő üzenetsor automatikus továbbítási beállításának frissítése
Az **üzenetsor** Áttekintés Service Bus válassza ki az Üzenetek továbbítása a következőbe beállítás **aktuális** értékét. A következő példában az aktuális érték **Letiltva.** Az **Üzenetek továbbítása üzenetsorba/témakörbe** ablakban kiválaszthatja azt az üzenetsort vagy témakört, ahová az üzeneteket szeretné küldeni. 

:::image type="content" source="./media/enable-auto-forward/queue-auto-forward.png" alt-text="Automatikus továbbítás engedélyezése meglévő üzenetsorhoz":::

### <a name="update-the-auto-forward-setting-for-an-existing-subscription"></a>Meglévő előfizetés automatikus továbbítási beállításának frissítése
A saját **előfizetésének** Áttekintés Service Bus válassza ki az Üzenetek továbbítása a következőbe beállítás **aktuális** értékét. A következő példában az aktuális érték **Letiltva.** Az **Üzenetek továbbítása üzenetsorba/témakörbe** ablakban kiválaszthatja azt az üzenetsort vagy témakört, ahová az üzeneteket szeretné küldeni. 

:::image type="content" source="./media/enable-auto-forward/subscription-auto-forward.png" alt-text="Automatikus továbbítás engedélyezése meglévő előfizetéshez":::

## <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Ha **olyan üzenetsort** szeretne létrehozni, amelyen engedélyezve van az automatikus továbbítás, használja a parancsot, amelynek beállítása az üzenetsor vagy témakör nevére van állítva, amelyre az üzeneteket [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--forward-to` szeretné küldeni. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

Egy **meglévő üzenetsor** automatikus továbbítási beállításának frissítéséhez használja a parancsot a paranccsal annak az üzenetsornak vagy témakörnek a nevére beállítva, amelyre az üzeneteket [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) szeretné `--forward-to` küldeni. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```


Ha **olyan témakörre** szeretne feliratkozni, amelyen engedélyezve van az automatikus továbbítás, használja az parancsot, amelynek beállítása az üzenetsor vagy témakör nevére van beállítva, amelyre az üzeneteket [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) szeretné `--forward-to` küldeni.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

Ha frissíteni szeretné az előfizetés automatikus továbbítási beállítását egy témakörre, használja a parancsot a paranccsal annak az üzenetsornak vagy témakörnek **a** nevével, amelyre az üzeneteket szeretné [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) `--forward-to` küldeni.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Ha **olyan üzenetsort** szeretne létrehozni, amelyen engedélyezve van az automatikus továbbítás, használja a parancsot, amelynek beállítása az üzenetsor vagy témakör nevére van állítva, amelyre az üzeneteket [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-ForwardTo` szeretné küldeni. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -ForwardTo myqueue2
```

Egy meglévő üzenetsor automatikus továbbítási **beállításának frissítéséhez** használja az parancsot [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) az alábbi példában látható módon.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.ForwardTo='myqueue2'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

Ha **előfizetést** szeretne létrehozni egy olyan témakörhez, amelyen engedélyezve van az automatikus továbbítás, használja az parancsot, amelynek beállítása az üzenetsor vagy témakör nevére van állítva, amelyre az üzeneteket [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) szeretné `-ForwardTo` küldeni.

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

Meglévő **előfizetés automatikus továbbítási beállításának frissítéséhez** tekintse meg az alábbi példát.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.ForwardTo='mytopic2'

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
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