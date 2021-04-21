---
title: Azure Service Bus – üzenetek száma
description: Lekéri az üzenetsorban és előfizetésben lévő üzenetek számát a Azure Resource Manager és a NamespaceManager API Azure Service Bus használatával.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5fc7211673badfde664d77128f9d79523926ccc9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814599"
---
# <a name="get-message-counters"></a>Üzenetszámlálók lekérte
Ez a cikk különböző módszereket mutat be az üzenetsorok vagy előfizetések alábbi üzenetszámának lekért kéréséhez. Az aktív üzenetek számának ismerete hasznos annak meghatározásához, hogy egy üzenetsor olyan hátralékot hoz-e létre, amely több erőforrást igényel, mint amit jelenleg üzembe helyezett. 

| Számláló | Description |
| ----- | ---------- | 
| ActiveMessageCount | Azon üzenetek száma az üzenetsorban vagy előfizetésben, amelyek aktív állapotban vannak, és kézbesítésre készek. |
| ScheduledMessageCount | Az ütemezett állapotban az üzenetek száma. |
| DeadLetterMessageCount | A holtbetűs üzenetsorban lévő üzenetek száma. |
| TransferMessageCount | A függőben lévő üzenetek száma egy másik üzenetsorba vagy témakörbe való átvitelre vár. |
| TransferDeadLetterMessageCount | Azon üzenetek száma, amelyek nem sikerültek átkerülni egy másik üzenetsorba vagy témakörbe, és átkerültek az átvitelhez nem kapcsolódó üzenetek üzenetsorába. |

Ha egy alkalmazás az üzenetsor hossza alapján szeretné skálázni az erőforrásokat, azt mért ütemben kell megtennie. Az üzenetszámlálók beszerzése költséges művelet az üzenetközvetítőn belül, és a végrehajtása gyakran közvetlenül, kedvezőtlen hatással van az entitás teljesítményére.

> [!NOTE]
> A témakörbe küldött Service Bus a témakör előfizetéseibe továbbítja a rendszer. Tehát a témakör aktív üzenetszáma 0, mivel ezeket az üzeneteket sikeresen továbbította az előfizetéshez. Szerezze be az üzenetek számát az előfizetésben, és ellenőrizze, hogy nagyobb-e 0-snál. Bár az előfizetésben üzeneteket lát, azok valójában a témakör tulajdonában lévő tárolóban vannak tárolva. Ha megnézi az előfizetéseket, akkor azok nem nulla üzenetszámban térnek el (ami 323 MB-ot ad hozzá a teljes entitás számára).


## <a name="using-azure-portal"></a>Az Azure Portal használata
Lépjen a névtérre, és válassza ki az üzenetsort. Az üzenetsor Áttekintés  lapján üzenetszámlálók megjelenik.

:::image type="content" source="./media/message-counters/queue-overview.png" alt-text="Üzenetszámlálók az üzenetsor áttekintő oldalán":::

Lépjen a névtérre, válassza ki a témakört, majd válassza ki a témakör előfizetését. Az üzenetsor Áttekintés  lapján üzenetszámlálók megjelenik.

:::image type="content" source="./media/message-counters/subscription-overview.png" alt-text="Üzenetszámlálók az előfizetés áttekintő oldalán":::

## <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Az paranccsal lekért üzenetsor üzenetszámának részletei az [`az servicebus queue show`](/cli/azure/servicebus/queue#az_servicebus_queue_show) alábbi példában látható módon. 

```azurecli-interactive
az servicebus queue show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --query countDetails
```

Példa a kimenetre:

```bash
ActiveMessageCount    DeadLetterMessageCount    ScheduledMessageCount    TransferMessageCount    TransferDeadLetterMessageCount
--------------------  ------------------------  -----------------------  ----------------------  --------------------------------
0                     0                         0                        0                       0
```

Az [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) paranccsal lekérte az előfizetés üzenetszámának részleteit az alábbi példában látható módon. 

```azurecli-interactive
az servicebus topic subscription show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysub \
    --query countDetails
```

## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
A PowerShell segítségével a következőképpen szerezheti be az üzenetsorok üzenetszámának részleteit:

```azurepowershell-interactive
$queueObj=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
                    -NamespaceName mynamespace `
                    -QueueName myqueue 

$queueObj.CountDetails
```

A példa kimenete a következő:

```bash
ActiveMessageCount             : 7
DeadLetterMessageCount         : 1
ScheduledMessageCount          : 3
TransferMessageCount           : 0
TransferDeadLetterMessageCount : 0
```

Az előfizetés üzenetszámának részleteit a következőképpen szerezheti be:

```azurepowershell-interactive
$topicObj= Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
                -NamespaceName mynamespace `
                -TopicName mytopic `
                -SubscriptionName mysub

$topicObj.CountDetails
```

A `MessageCountDetails` visszaadott objektum a következő tulajdonságokkal rendelkezik: `ActiveMessageCount` , , , , `DeadLetterMessageCount` `ScheduledMessageCount` `TransferDeadLetterMessageCount` `TransferMessageCount` . 

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
