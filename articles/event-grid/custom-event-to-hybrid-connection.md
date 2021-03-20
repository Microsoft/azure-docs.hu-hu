---
title: 'Oktatóanyag: egyéni események küldése hibrid kapcsolatok számára – Event Grid'
description: 'Oktatóanyag: a Azure Event Grid és az Azure CLI használatával tehet közzé egy témakört, és feliratkozhat erre az eseményre. Végpontként egy hibrid kapcsolatot használunk.'
ms.date: 07/07/2020
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: c709d77827551860cc917c3c84c5a849d9fdc512
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566826"
---
# <a name="tutorial-route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Oktatóanyag: egyéni események irányítása Azure Relay Hibrid kapcsolatok az Azure CLI-vel és Event Grid

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. A hibrid Azure Relay-kapcsolat az egyik támogatott eseménykezelő. A hibrid kapcsolatok eseménykezelőként való használatára akkor lehet szükség, ha olyan alkalmazásokból kell eseményeket kezelnie, amelyek nem rendelkeznek nyilvános végponttal. A vállalati hálózaton belül is lehetnek ilyen alkalmazások. Ebben a cikkben létrehozunk egy egyéni témakört az Azure CLI-vel, feliratkozunk az adott témakörre, majd elindítjuk az eseményt az eredmény megtekintéséhez. Az eseményeket hibrid kapcsolatokba küldjük.

## <a name="prerequisites"></a>Előfeltételek

- Ez a cikk feltételezi, hogy már rendelkezik egy hibrid kapcsolattal és egy figyelőalkalmazással. A hibrid kapcsolatok használatbavételéhez tekintse át a [Bevezetés a hibrid Relay-kapcsolatok használatába – .NET](../azure-relay/relay-hybrid-connections-dotnet-get-started.md) vagy a [Bevezetés a hibrid Relay-kapcsolatok használatába – Node](../azure-relay/relay-hybrid-connections-node-get-started.md) című témakört.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2.0.56 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Event Grid-témakörök Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. 

A következő példában létrehozunk egy *gridResourceGroup* nevű erőforráscsoportot a *westus2* helyen.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az Event Grid-témakörök egy felhasználó által meghatározott végpontot biztosítanak, amelyben közzéteheti az eseményeket. Az alábbi példa az erőforráscsoportban létrehozza az egyéni témakört. A `<topic_name>` elemet cserélje le az egyéni témakör egyedi nevére. Az Event Grid-témakör nevének egyedinek kell lennie, mert a nevet egy DNS-bejegyzés jelöli.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>Feliratkozás egyéni témakörre

Előfizet egy Event Grid-témakörre, hogy elmondja Event Grid mely eseményeket szeretné nyomon követni. A következő példa előfizet a létrehozott egyéni témakörre, és átadja a hibrid kapcsolatok erőforrás-AZONOSÍTÓját a végponthoz. A hibrid kapcsolat azonosítója a következő formátumot követi:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

Az alábbi szkript lekéri a Relay-névtér erőforrás-azonosítóját. Létrehozza a hibrid kapcsolat azonosítóját, és feliratkozik egy Event Grid-témakörre. A szkript a végpontot `hybridconnection` típusúra állítja be, és a hibrid kapcsolat azonosítóját használja végpontként.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid \
  --expiration-date "<yyyy-mm-dd>"
```

Vegye figyelembe, hogy az előfizetéshez [lejárati dátum](concepts.md#event-subscription-expiration) tartozik.

## <a name="create-application-to-process-events"></a>Alkalmazás létrehozása események feldolgozásához

Olyan alkalmazásra van szüksége, amely eseményeket tud lekérni a hibrid kapcsolatból. Ezt a műveletet a [Microsoft Azure Event Grid hibrid kapcsolat C# fogyasztói mintája](https://github.com/Azure-Samples/event-grid-dotnet-hybridconnection-destination) hajtja végre. Már befejezte az előfeltételként felsorolt lépéseket.

1. Győződjön meg arról, hogy rendelkezik a Visual Studio 2019-es vagy újabb verziójával.

1. Klónozza az adattárat a helyi gépre.

1. Töltse be a HybridConnectionConsumer projektet a Visual Studióban.

1. A Program.cs fájlban cserélje le a `<relayConnectionString>` és `<hybridConnectionName>` elemeket a továbbítási kapcsolat sztringjére és a létrehozott hibrid kapcsolatra.

1. Fordítsa és futtassa az alkalmazást a Visual Studióból.

## <a name="send-an-event-to-your-topic"></a>Esemény elküldése a témakörbe

Aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Ez a cikk bemutatja, hogyan aktiválhatja az eseményt az Azure CLI használatával. Használhatja az [Event Grid közzétételi alkalmazást](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridPublisher) is.

Először szükségünk lesz az egyéni témakör URL-címére és kulcsára. A `<topic_name>` helyett használja ismét az egyéni témakör nevét.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

A folyamat leegyszerűsítése érdekében használjon mintául szolgáló eseményadatokat, amelyeket elküldhet az egyéni témakörbe. Egy alkalmazás vagy Azure-szolgáltatás általában eseményadatokat küld el. A CURL egy olyan segédprogram, amely HTTP-kéréseket küld. Ebben a cikkben a CURL használatával küldjük el az eseményt az egyéni témakörbe.

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

A figyelőalkalmazásnak meg kell kapnia az eseményüzenetet.

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