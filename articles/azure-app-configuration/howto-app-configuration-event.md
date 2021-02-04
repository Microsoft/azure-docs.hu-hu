---
title: Event Grid használata az alkalmazás konfigurációs adatmódosítási értesítéseihez
description: Ismerje meg, hogyan küldhet kulcs-érték módosítási eseményeket webes végpontra az Azure-alkalmazás konfigurációs esemény-előfizetései használatával
services: azure-app-configuration
author: AlexandraKemperMS
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 03/04/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: c188a4b7fe8e9223faa1cdeb52ae01ed83b94d84
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549779"
---
# <a name="use-event-grid-for-app-configuration-data-change-notifications"></a>Event Grid használata az alkalmazás konfigurációs adatmódosítási értesítéseihez

Ebből a cikkből megtudhatja, hogyan állíthatja be az Azure-alkalmazás konfigurációs esemény-előfizetéseit a kulcs-érték módosítási események webes végpontra való küldéséhez. Az Azure-alkalmazások konfigurációjának felhasználói előfizethetnek a kulcs-érték módosításakor kibocsátott eseményekre. Ezek az események indíthatnak webhookokat, Azure Functionseket, Azure Storage-várólistákat vagy bármely más, a Azure Event Grid által támogatott eseménykezelőt. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A cikk egyszerűsítése érdekében azonban az eseményeket egy olyan webalkalmazásnak küldjük el, amely az üzenetek gyűjtésével és megjelenítésével foglalkozik.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/). Igény szerint a Azure Cloud Shell is használhatja.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI legújabb verzióját (2.0.70 vagy újabb verzió) kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Ha nem a Cloud Shellt használja, először be kell jelentkeznie az `az login` paranccsal.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Event Grid-témakörök Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. 

A következő példában létrehozunk egy nevű erőforráscsoportot `<resource_group_name>` a *westus* helyen.  A `<resource_group_name>` elemet az erőforráscsoport egyedi nevére cserélje le.

```azurecli-interactive
az group create --name <resource_group_name> --location westus
```

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

A helyére írja `<appconfig_name>` be a konfigurációs tároló egyedi nevét, valamint `<resource_group_name>` a korábban létrehozott erőforráscsoportot. A névnek egyedinek kell lennie, mert DNS-névként van használatban.

```azurecli-interactive
az appconfig create \
  --name <appconfig_name> \
  --location westus \
  --resource-group <resource_group_name> \
  --sku free
```

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A témakörre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Az útmutató egyszerűsítése érdekében egy olyan [előre létrehozott webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyezünk üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

A `<your-site-name>` elemet a webalkalmazás egyedi nevére cserélje le. A webalkalmazás nevének egyedinek kell lennie, mert a DNS-bejegyzés része.

```azurecli-interactive
$sitename=<your-site-name>

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

A helynek megjelenített üzenetek nélkül kell megjelennie.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store"></a>Előfizetés az alkalmazás konfigurációs tárolójára

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni, és hová szeretné küldeni azokat. Az alábbi példa előfizet a létrehozott alkalmazás-konfigurációra, és átadja az URL-címet a webalkalmazásból az eseményekről szóló értesítés végpontjának. Az `<event_subscription_name>` elemet cserélje le az esemény-előfizetéshez választott névre. A `<resource_group_name>` és `<appconfig_name>` elemnél a korábban létrehozott értékeket adja meg.

A webalkalmazás végpontjának az `/api/updates/` utótagot kell tartalmaznia.

```azurecli-interactive
appconfigId=$(az appconfig show --name <appconfig_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $appconfigId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

![Előfizetési esemény megtekintése](./media/quickstarts/event-grid/view-subscription-event.png)

## <a name="trigger-an-app-configuration-event"></a>Alkalmazás-konfigurációs esemény elindítása

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Hozzon létre egy kulcs-érték értéket a- `<appconfig_name>` ből a korábbi verziók használatával.

```azurecli-interactive
az appconfig kv set --name <appconfig_name> --key Foo --value Bar --yes
```

Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Tekintse meg a webalkalmazást az imént elküldött esemény megtekintéséhez.

```json
[{
  "id": "deb8e00d-8c64-4b6e-9cab-282259c7674f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/eventDemoGroup/providers/microsoft.appconfiguration/configurationstores/{appconfig-name}",
  "subject": "https://{appconfig-name}.azconfig.io/kv/Foo",
  "data": {
    "key": "Foo",
    "etag": "a1LIDdNEIV6wCnfv3xaip7fMXD3"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T18:59:54Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy folytatja az alkalmazás konfigurálását és az esemény-előfizetést, ne törölje az ebben a cikkben létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő paranccsal törölheti a cikkben létrehozott erőforrásokat.

A `<resource_group_name>` elemet cserélje le a fent létrehozott erőforráscsoportra.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan hozhat létre témaköröket és esemény-előfizetéseket, többet tudhat meg a kulcs-érték eseményekről, és arról, hogy milyen Event Grid segíthet:

- [Key-Value eseményekre való reagálás](concept-app-configuration-event.md)
- [Bevezetés az Event Grid használatába](../event-grid/overview.md)
- [Azure Event Grid kezelők](../event-grid/event-handlers.md)
