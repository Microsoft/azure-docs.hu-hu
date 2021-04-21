---
title: Üzenetsorok és előfizetések Azure Service Bus üzenetsorok és előfizetések esetén
description: Ez a cikk bemutatja, hogyan engedélyezheti az üzenetsorok és előfizetések üzenetsorok és előfizetések esetében a Azure Portal, a PowerShell, a parancssori felület és a programozási nyelvek (C#, Java, Python és JavaScript) használatával
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 4d4a232d9129cf110a33ece56330ee708f9341b6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819369"
---
# <a name="enable-dead-lettering-on-message-expiration-for-azure-service-bus-queues-and-subscriptions"></a>Üzenetsorok és előfizetések Azure Service Bus üzenetek lejárati dátumának engedélyezése
Azure Service Bus üzenetsorok és előfizetések egy másodlagos alsort biztosítanak, az úgynevezett "holtbetűs üzenetsort" (DLQ). A holtbetűs üzenetsort nem kell explicit módon létrehozni, és nem törölhető és nem kezelhető a fő entitástól függetlenül. A kézbesített üzenetek üzenetsorának célja, hogy olyan üzeneteket tartalmaz, amelyek egyetlen fogadónak sem kézbesítve, sem pedig feldolgoz nem feldolgozható üzenetek. További információ: A Service Bus [üzenetsorok áttekintése.](service-bus-dead-letter-queues.md) Ez a cikk különböző módszereket mutat be az üzenetsorok és előfizetések Service Bus a nem Service Bus számára. 

## <a name="using-azure-portal"></a>Az Azure Portal használata
Amikor **üzenetsort**  vagy előfizetést hoz létre egy témakör számára a Azure Portal válassza az **Üzenetek** elévülésének engedélyezése a levélkéséskor lehetőséget az alábbi példákban látható módon. 

### <a name="create-a-queue-with-dead-lettering-enabled"></a>Üzenetsor létrehozása engedélyezett" üzenetsorokkal
:::image type="content" source="./media/enable-dead-letter/create-queue.png" alt-text="Az üzenetsor létrehozásakor engedélyezze a nem küldött leveleket":::

### <a name="create-a-subscription-with-dead-lettering-enabled"></a>Előfizetés létrehozása engedélyezett nem használt levelekkel
:::image type="content" source="./media/enable-dead-letter/create-subscription.png" alt-text="A levélkésés engedélyezése az előfizetés létrehozásakor":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-queue"></a>Egy meglévő üzenetsor üzenet-elévülési beállításának holtbetűinek frissítése
Az **üzenetsor** Áttekintés Service Bus válassza ki az Üzenet elévülésekor nem látható üzenetek aktuális értékét.  A következő példában az aktuális érték **Letiltva.** A felugró ablakban engedélyezheti vagy letilthatja a nem látható üzeneteket az üzenetek lejáratakor. 

:::image type="content" source="./media/enable-dead-letter/queue-configuration.png" alt-text="Üzenetek elévülésének engedélyezése egy meglévő üzenetsorhoz":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-subscription"></a>Egy meglévő előfizetés üzenet-elévülési beállításának üzenet-elévülési beállítása frissítve
A **tanúsítvány-előfizetés** Áttekintés Service Bus válassza ki az Üzenet elévülésekor üzenet elévüléskor **üzenetüzenet** esetén beállítás aktuális értékét. A következő példában az aktuális érték **Letiltva.** A felugró ablakban engedélyezheti vagy letilthatja a nem látható leveleket az üzenetek lejáratakor. 

:::image type="content" source="./media/enable-dead-letter/subscription-configuration.png" alt-text="Üzenetek elévülésének engedélyezése egy meglévő előfizetéshez":::

## <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Ha **olyan üzenetsort** hoz létre, amely az üzenetek elévülése esetén nem aktív leveleket használ, használja a parancsot a [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-dead-lettering-on-message-expiration` `true` beállításával. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```

Ha **egy meglévő üzenetsorhoz** szeretné engedélyezni az üzenet elévülési beállításának nem megfelelő betűjelét, használja a parancsot a [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) érték `--enable-dead-lettering-on-message-expiration` `true` beállításával. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```


Ha **olyan témakörre való előfizetést** hoz létre, amely az üzenetek elévülése esetén nem aktív leveleket tartalmaz, használja a parancsot a [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) `--enable-dead-lettering-on-message-expiration` `true` beállításával.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

Ha **engedélyezni szeretné a** üzenet elévülési beállítását egy témakörre feliratkozó előfizetésben, használja a parancsot a set [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) `--enable-dead-lettering-on-message-expiration` `true` paranccsal.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Ha **olyan üzenetsort** hoz létre, amely az üzenetek elévülése esetén nem aktív leveleket használ, használja a parancsot a [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-DeadLetteringOnMessageExpiration` `$True` beállításával. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -DeadLetteringOnMessageExpiration $True
```

Ha **egy meglévő üzenetsorhoz** szeretné engedélyezni az üzenet elévülési beállítását, használja a parancsot az alábbi [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) példában látható módon.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

Ha **olyan témakörre vonatkozó előfizetést** hoz létre, amely az üzenetek elévülése esetén nem aktív leveleket tartalmaz, használja a parancsot a [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) `-DeadLetteringOnMessageExpiration` `$True` beállításával. 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -DeadLetteringOnMessageExpiration $True
```

Ha egy meglévő előfizetéshez szeretné engedélyezni a nem látható leveleket az üzenetek elévülési **beállításakor,** tekintse meg az alábbi példát.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablonok használata
Ha **olyan üzenetsort** hoz létre, amely az üzenetek elévülése esetén nem aktív leveleket kap, állítsa az `deadLetteringOnMessageExpiration` üzenetsor tulajdonságainak szakaszában a következőre: `true` . További információkért tekintse meg a [Microsoft.ServiceBus-névterek/üzenetsorok sablonreferenciáját.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "deadLetteringOnMessageExpiration": true
          }
        }
      ]
    }
  ]
}

```

Ha **olyan témakörre vonatkozó előfizetést** hoz létre, amely az üzenetek elévülése esetén nem aktív leveleket tartalmaz, állítsa az `deadLetteringOnMessageExpiration` üzenetsor tulajdonságainak szakaszában a következőt: `true` . További információkért lásd a [Microsoft.ServiceBus-névterek/topics/subscriptions sablonreferenciát.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json) 

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
                "deadLetteringOnMessageExpiration": true
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