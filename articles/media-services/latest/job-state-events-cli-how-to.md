---
title: Azure Media Services események figyelése a Event Grid
description: Ez a cikk bemutatja, hogyan fizethet elő Event Gridre az Azure CLI-vel való Azure Media Services események figyelése érdekében.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: e01083b8618e4751770ed69aa4b1c20f8a072d88
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092310"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Media Services események létrehozása és figyelése Event Grid az Azure CLI használatával

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ez a szolgáltatás [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Media Services események tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. Azonosíthatja Media Services eseményt, mert a eventType tulajdonság a "Microsoft. Media" karakterlánccal kezdődik. További információ: [Media Services esemény sémái](media-services-event-schemas.md).

Ebben a cikkben az Azure CLI használatával fizethet elő Azure Media Services-fiókjához tartozó eseményekre. Ezután aktiválhatja az eseményeket az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. Ebben a cikkben az eseményeket egy webalkalmazásba küldi, amely összegyűjti és megjeleníti az üzeneteket.

## <a name="prerequisites"></a>Előfeltételek

- Aktív Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- A parancssori felület helyi telepítése és használata: ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára lesz szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

    Jelenleg nem minden [Media Services v3 CLI](/cli/azure/ams) -parancs működik a Azure Cloud Shellban. Javasoljuk, hogy helyileg használja a CLI-t.

- [Hozzon létre egy Media Services fiókot](./create-account-howto.md).

    Ügyeljen arra, hogy az erőforráscsoport neveként használt értékeket jegyezze fel, és Media Services a fiók nevét.

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

Az Media Services-fiók eseményeire való feliratkozás előtt hozzon létre egy végpontot az esemény üzenethez. A végpont általában az eseményadatok alapján hajt végre műveleteket. Ebben a cikkben egy [előre elkészített webes alkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyez üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

   [![Az "üzembe helyezés az Azure-ban" feliratú gombot ábrázoló kép.](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

Ha átvált a "Azure Event Grid Viewer" webhelyre, akkor azt láthatja, hogy még nincsenek események.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Az Azure-előfizetés beállítása

Az alábbi parancsban adja meg a Media Services-fiókhoz használni kívánt Azure-előfizetés azonosítóját. Az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lapot megnyitva tekintheti meg az Ön által elérhető előfizetések listáját.

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Előfizetés Media Services eseményekre

A cikkre való előfizetéssel megtudhatja, hogy Event Grid mely eseményeket kívánja nyomon követni. A következő példa előfizet a létrehozott Media Services fiókra, és átadja az URL-címet az esemény-értesítés végpontja létrehozott webhelyről. 

A helyére írja `<event_subscription_name>` be az esemény-előfizetés egyedi nevét. `<resource_group_name>`És esetében `<ams_account_name>` használja a Media Services fiók létrehozásakor használt értékeket. A `<endpoint_URL>` alkalmazásban adja meg a webalkalmazás URL-címét, és adja hozzá `api/updates` a Kezdőlap URL-címét. Ha a végpontot a feliratkozáskor megadta, Event Grid kezeli az események útválasztását az adott végpontra. 

1. Erőforrás-azonosító lekérése

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Például:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Előfizetés az eseményekre

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Például:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Előfordulhat, hogy az érvényesítési kézfogás figyelmeztetést kap. Adjon meg néhány percet, és a kézfogásnak érvényesíteni kell.

Most aktiváljuk az eseményeket, hogy meglássuk, hogyan osztja el a Event Grid az üzenetet a végpontnak.

## <a name="send-an-event-to-your-endpoint"></a>Esemény elküldése a végpontra

A Media Services-fiókhoz tartozó eseményeket a kódolási feladatok futtatásával aktiválhatja. [Ezt](stream-files-dotnet-quickstart.md) a rövid útmutatót követve kódolhat egy fájlt, és megkezdheti az események küldését. 

Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A végpontnak a következőre kell beállítania: `validationResponse` `validationCode` . További információ: [Event Grid biztonság és hitelesítés](../../event-grid/security-authentication.md). Megtekintheti a webalkalmazás kódját, hogy megtudja, hogyan érvényesíti az előfizetést.

> [!TIP]
> Az eseményadatok kibontásához kattintson a szem ikonra. Ne frissítse az oldalt, ha meg szeretné tekinteni az összes eseményt.

![Előfizetési esemény megtekintése](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Következő lépések

[Feltöltés, kódolás és streamelés](stream-files-tutorial-with-api.md)