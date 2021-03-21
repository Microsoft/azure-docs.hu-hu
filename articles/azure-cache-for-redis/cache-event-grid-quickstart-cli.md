---
title: 'Gyors útmutató: az Azure cache átirányítása a Redis eseményeihez a webes végponthoz az Azure CLI-vel'
description: A Azure Event Grid segítségével előfizethet az Azure cache-re az Redis eseményekhez, aktiválhat egy eseményt, és megtekintheti az eredményeket.
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 7f33ca0043400962054fabb1aadb1da612fe5426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806426"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-azure-cli"></a>Gyors útmutató: az Azure cache átirányítása a Redis eseményeihez a webes végponthoz az Azure CLI-vel

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a rövid útmutatóban az Azure CLI-vel feliratkozhat az Azure cache-re a Redis eseményekhez, aktiválhat egy eseményt, és megtekintheti az eredményeket.

Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A rövid útmutató egyszerűsítése érdekében azonban olyan webalkalmazásnak küldheti az eseményeket, amely összegyűjti és megjeleníti az üzeneteket. Az ebben a rövid útmutatóban ismertetett lépések elvégzése után láthatja, hogy a rendszer elküldte az eseményre vonatkozó információt a webalkalmazásnak.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI legújabb verzióját (2.0.70 vagy újabb verzió) kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Ha nem a Cloud Shellt használja, először be kell jelentkeznie az `az login` paranccsal.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Event Grid témaköröket különálló Azure-erőforrásként helyezi üzembe, és egy Azure-erőforráscsoport alatt kell kiépíteni. Az erőforráscsoport olyan logikai gyűjtemény, amelyben az Azure-erőforrások üzembe helyezése és kezelése történik.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. 

A következő példában létrehozunk egy `<resource_group_name>` nevű erőforráscsoportot a *westcentralus* helyen.  A `<resource_group_name>` elemet az erőforráscsoport egyedi nevére cserélje le.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-cache-instance"></a>Gyorsítótár-példány létrehozása

```azurecli-interactive
#/bin/bash

# Create a Basic C0 (256 MB) Azure Cache for Redis instance
az redis create --name <cache_name> --resource-group <resource_group_name> --location westcentralus --sku Basic --vm-size C0
```


## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A témakörre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Az útmutató egyszerűsítése érdekében egy olyan [előre létrehozott webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyezünk üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

A `<your-site-name>` elemet a webalkalmazás egyedi nevére cserélje le. A webalkalmazás nevének egyedinek kell lennie, mert a DNS-bejegyzés része.

```azurecli-interactive
sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

A helynek megjelenített üzenetek nélkül kell megjelennie.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-azure-cache-for-redis-instance"></a>Fizessen elő az Azure cache Redis-példányra

Ebben a lépésben előfizet egy témakörre, hogy elmondja Event Grid mely eseményeket szeretné nyomon követni, és hová szeretné elküldeni ezeket az eseményeket. Az alábbi példa előfizet az Azure cache-re a létrehozott Redis-példányhoz, és átadja az URL-címet a webalkalmazásból az eseményekről szóló értesítés végpontjának. Az `<event_subscription_name>` elemet cserélje le az esemény-előfizetéshez választott névre. A `<resource_group_name>` és `<cache_name>` elemnél a korábban létrehozott értékeket adja meg.

A webalkalmazás végpontjának az `/api/updates/` utótagot kell tartalmaznia.

```azurecli-interactive
cacheId=$(az redis show --name <cache_name> --resource-group <resource_group_name> --query id --subscription <subscription_id>)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --source-resource-id $cacheId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Esemény indítása az Azure cache Redis

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Exportálja a Redis-példányhoz tartozó Azure cache-ben tárolt adatait. Ismét használja a `{cache_name}` korábban létrehozott és a-hoz tartozó értékeket `{resource_group_name}` .

```azurecli-interactive
az redis export  --ids '/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}' \
    --prefix '<prefix_for_exported_files>' \
    --container '<SAS_url>'  
```

Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Tekintse meg a webalkalmazást az imént elküldött esemény megtekintéséhez.


```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ExportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ExportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ExportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy folytatja az Azure cache használatát a Redis-példány és az esemény-előfizetés esetében, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő parancs használatával törölheti az ebben a rövid útmutatóban létrehozott erőforrásokat.

A `<resource_group_name>` elemet cserélje le a fent létrehozott erőforráscsoportra.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan hozhat létre témaköröket és esemény-előfizetéseket, többet is megtudhat az Azure cache Redis-eseményekről, illetve arról, hogy milyen Event Grid segíthet:

- [Az Azure cache-re való reagálás Redis eseményekhez](cache-event-grid.md)
- [Bevezetés az Event Grid használatába](../event-grid/overview.md)
