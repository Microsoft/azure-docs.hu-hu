---
title: Particionálás engedélyezése Azure Service Bus üzenetsorok és témakörök számára
description: Ez a cikk azt ismerteti, hogyan engedélyezheti Azure Service Bus-üzenetsorok és témakörök particionálását az Azure Portal, a PowerShell, a parancssori felület és a programozási nyelvek (C#, Java, Python és JavaScript) használatával
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: fb704f784d490cb73c14fc73b1a6c4368d16acbc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755204"
---
# <a name="enable-partitioning-for-an-azure-service-bus-queue-or-a-topic"></a>Particionálás engedélyezése egy Azure Service Bus üzenetsorhoz vagy témakörhez
Service Bus partíciók lehetővé teszik az üzenetsorok és témakörök, más szóval az üzenetkezelési entitások több üzenetközvetítőre és üzenetkezelési tárolóra történő particionálását. A particionálás azt jelenti, hogy a particionált entitások teljes átviteli sebességét már nem korlátozza egyetlen üzenetközvetítő vagy üzenetkezelési tároló teljesítménye. Emellett az üzenetkezelési tároló ideiglenes kimaradása nem teszi elérhetetlenné a particionált üzenetsort vagy témakört. A particionált üzenetsorok és témakörök tartalmazhatnak minden speciális Service Bus funkciót, például a tranzakciók és munkamenetek támogatását. További információ: [Particionált üzenetsorok és témakörök.](service-bus-partitioning.md) Ez a cikk különböző módszereket mutat be a duplikált üzenetek észlelésének engedélyezésére egy Service Bus üzenetsor vagy témakör számára. 

> [!IMPORTANT]
> - A particionálás az entitások létrehozásakor érhető el az alapszintű vagy standard SKUS-ban lévő összes üzenetsorhoz és témakörhez. A prémium szintű üzenetkezelési termékváltozathoz nem érhető el, de a premium névterek korábban már meglévő particionált entitásai továbbra is a várt módon működnek.
> - Meglévő üzenetsoron vagy témakören nem lehet módosítani a particionálási beállítást. Ezt a beállítást csak üzenetsor vagy témakör létrehozásakor állíthatja be. 

## <a name="using-azure-portal"></a>Az Azure Portal használata
Amikor **üzenetsort hoz** létre a Azure Portal válassza a **Particionálás** engedélyezése lehetőséget az alábbi képen látható módon. 

:::image type="content" source="./media/enable-partitions/create-queue.png" alt-text="Particionálás engedélyezése az üzenetsor létrehozásakor":::

Amikor témakört hoz létre a Azure Portal válassza a **Particionálás** engedélyezése lehetőséget az alábbi képen látható módon. 

:::image type="content" source="./media/enable-partitions/create-topic.png" alt-text="Particionálás engedélyezése a témakör létrehozásakor":::

## <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Ha **engedélyezett particionálást engedélyező üzenetsort hoz létre,** használja a parancsot a [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-partitioning` `true` beállítással.

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-partitioning true
```

Ha **engedélyezni kell a particionálást,** hozzon létre egy témakört a paranccsal, és állítsa a [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) `--enable-partitioning` következőre: `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-partitioning true
```

## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Ha **engedélyezett particionálást engedélyező üzenetsort hoz létre,** használja az parancsot a [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-EnablePartitioning` `$True` beállítással. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -EnablePartitioning $True
```

Ha **engedélyezni kell a particionálást,** hozzon létre egy témakört a paranccsal, és állítsa a [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) `-EnablePartitioning` következőre: `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -EnablePartitioning $True
```

## <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablonok használata
Ha **engedélyezett particionálást** engedélyező üzenetsort hoz létre, állítsa a beállítást a következőre az `enablePartitioning` `true` üzenetsor tulajdonságai szakaszban: . További információ: [Microsoft.ServiceBus névterek/üzenetsorok sablonreferenciája.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}

```

Ha **olyan témakört hoz létre, amelyben engedélyezve van az ismétlődésészlelés,** állítsa be a következőt a `enablePartitioning` témakör tulajdonságai `true` szakaszban: . További információ: [Microsoft.ServiceBus névterek/témakörök sablonreferenciája.](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json) 

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
            "enablePartitioning": true
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