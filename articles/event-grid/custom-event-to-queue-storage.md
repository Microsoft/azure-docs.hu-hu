---
title: 'Gyors útmutató: egyéni események küldése a Storage-üzenetsor számára – Event Grid, Azure CLI'
description: 'Rövid útmutató: a Azure Event Grid és az Azure CLI használatával tehet közzé egy témakört, és feliratkozhat erre az eseményre. A rendszer tárolási üzenetsort használ végpontként.'
ms.date: 02/02/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 00808e7eca13824833673ef820d39b70bf618dd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493260"
---
# <a name="quickstart-route-custom-events-to-azure-queue-storage-with-azure-cli-and-event-grid"></a>Gyors útmutató: egyéni események irányítása az Azure üzenetsor-tárolóba az Azure CLI-vel és a Event Grid

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Az Azure Queue Storage az egyik támogatott eseménykezelő. Ebben a cikkben létrehozunk egy egyéni témakört az Azure CLI-vel, feliratkozunk az adott témakörre, majd elindítjuk az eseményt az eredmény megtekintéséhez. Az eseményeket elküldi a Queue Storage-ba.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.56 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

- Ha Azure PowerShellt használ a helyi gépen a Azure Portal Cloud Shell használata helyett, győződjön meg arról, hogy Azure PowerShell 1.1.0 vagy újabb verzióra van szüksége. Töltse le a Azure PowerShell legújabb verzióját a Windows rendszerű gépén az [Azure downloads-parancssori eszközökről](https://azure.microsoft.com/downloads/). 

Ez a cikk az Azure CLI használatának parancsait ismerteti. 

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Event Grid-témakörök Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. 

A következő példában létrehozunk egy *gridResourceGroup* nevű erőforráscsoportot a *westus2* helyen.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az Event Grid-témakörök egy felhasználó által meghatározott végpontot biztosítanak, amelyben közzéteheti az eseményeket. Az alábbi példa az erőforráscsoportban létrehozza az egyéni témakört. A `<topic_name>` elemet cserélje le az egyéni témakör egyedi nevére. Az Event Grid-témakör nevének egyedinek kell lennie, mert a nevet egy DNS-bejegyzés jelöli.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-queue-storage"></a>Queue Storage-tároló létrehozása

Az egyéni témakörre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. Az események tárolásához létrehozhat egy Queue Storage-tárolót.

```azurecli-interactive
storagename="<unique-storage-name>"
queuename="eventqueue"

az storage account create -n $storagename -g gridResourceGroup -l westus2 --sku Standard_LRS
az storage queue create --name $queuename --account-name $storagename
```

## <a name="subscribe-to-a-custom-topic"></a>Feliratkozás egyéni témakörre

Az egyéni témakörre való előfizetéssel megállapíthatja, hogy Event Grid mely eseményeket kívánja nyomon követni. Az alábbi példa előfizet a létrehozott egyéni témakörre, és átadja a végponthoz tartozó várólista-tároló erőforrás-AZONOSÍTÓját. Az Azure CLI használatakor a Queue Storage-azonosítót kell megadnia végpontként. A végpont formátuma a következő:

`/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/queueservices/default/queues/<queue-name>`

Az alábbi szkript lekéri az üzenetsor tárfiókjának erőforrás-azonosítóját. Létrehozza a Queue Storage-tároló azonosítóját, és feliratkozik egy Event Grid-témakörre. A végpontot `storagequeue` típusúra állítja be, és az üzenetsor azonosítóját használja végpontként.

```azurecli-interactive
storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)
queueid="$storageid/queueservices/default/queues/$queuename"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type storagequeue \
  --endpoint $queueid \
  --expiration-date "<yyyy-mm-dd>"
```

Az esemény-előfizetést létrehozó fióknak írási hozzáféréssel kell rendelkeznie a Queue Storage-tárolóhoz. Vegye figyelembe, hogy az előfizetéshez [lejárati dátum](concepts.md#event-subscription-expiration) tartozik.

Ha a REST API használatával hozza létre az előfizetést, a tárfiók azonosítóját és az üzenetsor nevét külön paraméterként kell megadnia.

```json
"destination": {
  "endpointType": "storagequeue",
  "properties": {
    "queueName":"eventqueue",
    "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>"
  }
  ...
```

## <a name="send-an-event-to-your-custom-topic"></a>Esemény elküldése az egyéni témakörbe

Aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Először szükségünk lesz az egyéni témakör URL-címére és kulcsára. A `<topic_name>` helyett használja ismét az egyéni témakör nevét.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

A folyamat leegyszerűsítése érdekében használjon mintául szolgáló eseményadatokat, amelyeket elküldhet az egyéni témakörbe. Egy alkalmazás vagy Azure-szolgáltatás általában eseményadatokat küld el. A CURL egy olyan segédprogram, amely HTTP-kéréseket küld. Ebben a cikkben a CURL használatával küldjük el az eseményt az egyéni témakörbe.  Az alábbi példa három eseményt küld el az Event Grid-témakörnek:

```azurecli-interactive
for i in 1 2 3
do
   event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
   curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
done
```

A Portalon lépjen a Queue Storage-tárolóhoz, és figyelje meg, hogy az Event Grid elküldte a három eseményt az üzenetsorba.

![Az üzenetek megjelenítése](./media/custom-event-to-queue-storage/messages.png)

> [!NOTE]
> Ha [Azure üzenetsor-tárolási triggert](../azure-functions/functions-bindings-storage-queue-trigger.md) használ egy olyan várólista Azure functions számára, amely üzeneteket fogad Event Gridtól, a függvény végrehajtásához a következő hibaüzenet jelenhet meg: `The input is not a valid Base-64 string as it contains a non-base 64 character, more than two padding characters, or an illegal character among the padding characters.`
> 
> Ennek az az oka, hogy amikor [Azure üzenetsor-tárolási triggert](../azure-functions/functions-bindings-storage-queue-trigger.md)használ, Azure functions egy **Base64 kódolású karakterláncot** vár, de a Event Grid egyszerű szöveges formátumban küld üzeneteket egy tárolási várólistára. Jelenleg nem lehet konfigurálni a várólista-triggert Azure Functions egyszerű szöveg fogadására. 


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
