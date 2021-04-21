---
title: 'Rövid útmutató: Egyéni események küldése a Event Hubs – Event Grid, Azure CLI'
description: 'Rövid útmutató: A Azure Event Grid és az Azure CLI használatával közzétehet egy témakört, és feliratkozhat az eseményre. A rendszer Event Hubot használ végpontként.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: e1989c94cc7b260cfc36a3129d2743350ca430bf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780490"
---
# <a name="quickstart-route-custom-events-to-azure-event-hubs-with-azure-cli-and-event-grid"></a>Rövid útmutató: Egyéni események útválasztása Azure Event Hubs Azure CLI és a Event Grid

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Az Azure Event Hubs az egyik támogatott eseménykezelő. Ebben a cikkben létrehozunk egy egyéni témakört az Azure CLI-vel, feliratkozunk az adott témakörre, majd elindítjuk az eseményt az eredmény megtekintéséhez. Az eseményeket az eseményközpontba küldi.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Event Grid-témakörök Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. 

A következő példában létrehozunk egy *gridResourceGroup* nevű erőforráscsoportot a *westus2* helyen.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az Event Grid-témakörök egy felhasználó által meghatározott végpontot biztosítanak, amelyben közzéteheti az eseményeket. Az alábbi példa az erőforráscsoportban létrehozza az egyéni témakört. A `<your-topic-name>` elemet cserélje le az egyéni témakör egyedi nevére. A témakör nevének egyedinek kell lennie, mert a nevet egy DNS-bejegyzés képviseli.

```azurecli-interactive
topicname=<your-topic-name>
az eventgrid topic create --name $topicname -l westus2 -g gridResourceGroup
```

## <a name="create-event-hub"></a>Eseményközpont létrehozása

Az egyéni témakörre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. Eseményközpontot hoz létre az események begyűjtéséhez.

```azurecli-interactive
namespace=<unique-namespace-name>
hubname=demohub

az eventhubs namespace create --name $namespace --resource-group gridResourceGroup
az eventhubs eventhub create --name $hubname --namespace-name $namespace --resource-group gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>Feliratkozás egyéni témakörre

Ha előfizet egy Event Grid-témakörre, Event Grid mely eseményeket szeretné nyomon követni. Az alábbi példa feliratkozik a létrehozott egyéni témakörre, és átadja az eseményközpont erőforrás-azonosítóját a végpontnak. A végpont formátuma a következő:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.EventHub/namespaces/<namespace-name>/eventhubs/<hub-name>`

A következő szkript beszerzi az eseményközpont erőforrás-azonosítóját, és feliratkozik egy Event Grid-témakörre. A végpontot `eventhub` típusúra állítja be, és az Event Hub azonosítóját használja végpontként.

```azurecli-interactive
hubid=$(az eventhubs eventhub show --name $hubname --namespace-name $namespace --resource-group gridResourceGroup --query id --output tsv)
topicid=$(az eventgrid topic show --name $topicname -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name subtoeventhub \
  --endpoint-type eventhub \
  --endpoint $hubid
```

Az esemény-előfizetést létrehozó fióknak írási hozzáféréssel kell rendelkeznie az Event Hubhoz.

## <a name="send-an-event-to-your-custom-topic"></a>Esemény elküldése az egyéni témakörbe

Aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Először szükségünk lesz az egyéni témakör URL-címére és kulcsára.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name $topicname -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicname -g gridResourceGroup --query "key1" --output tsv)
```

A folyamat leegyszerűsítése érdekében használjon mintául szolgáló eseményadatokat, amelyeket elküldhet az egyéni témakörbe. Egy alkalmazás vagy Azure-szolgáltatás általában eseményadatokat küld el. A CURL egy olyan segédprogram, amely HTTP-kéréseket küld. Ebben a cikkben a CURL használatával küldjük el az eseményt az egyéni témakörbe.  Az alábbi példa három eseményt küld el az Event Grid-témakörnek:

```azurecli-interactive
for i in 1 2 3
do
   event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
   curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
done
```

A Portalon lépjen az eseményközponthoz, és figyelje meg, hogy az Event Grid elküldte a három eseményt az eseményközpontba.

![Az üzenetek megjelenítése](./media/custom-event-to-eventhub/show-result.png)

Általában az eseményeket az eseményközpontból lekérő alkalmazást hoz létre. Az üzeneteket az eseményközpontból lekérő alkalmazások létrehozásáról lásd:

* [Üzenetek fogadása az Event Processor Hosttal .NET Standardban – első lépések](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* [Események fogadása az Azure Event Hubsból a Java használatával](../event-hubs/event-hubs-java-get-started-send.md)
* [Események fogadása az Event Hubsról az Apache Storm használatával](../event-hubs/event-hubs-storm-getstarted-receive.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha tovább kívánja használni az eseményt, akkor ne törölje a cikkben létrehozott erőforrásokat. Ellenkező esetben a következő paranccsal törölheti a cikkben létrehozott erőforrásokat.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerkedett vele, hogyan hozhat létre témaköröket és eseményfeliratkozásokat, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Bevezetés az Event Grid használatába](overview.md)
- [Azure Blob Storage-események átirányítása egyéni webes végpontra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)
