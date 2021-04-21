---
title: Események küldése Azure SignalR Service a Event Grid
description: Útmutató, amely bemutatja, hogyan engedélyezheti a Event Grid eseményeket a SignalR Service számára, majd hogyan küldheti el az ügyfélkapcsolattal összekapcsolt/leválasztott eseményeket egy mintaalkalmazásnak.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: 65fb54dbfc5158ef8cc2b488f267c92f601502f6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784586"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Események küldése az Azure SignalR Service-ből az Event Gridbe

Azure Event Grid egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely egy pub-sub modell használatával egységes eseményfelhasználást biztosít. Ebben az útmutatóban az Azure CLI használatával létrehoz egy virtuális Azure SignalR Service, feliratkozik a kapcsolati eseményekre, majd üzembe helyez egy minta-webalkalmazást az események fogadásához. Végül csatlakozhat és leválaszthatja a kapcsolatot, és láthatja az esemény hasznosadatát a mintaalkalmazásban.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - A cikkben említett Azure CLI-parancsok a **Bash-felülethez vannak formázva.** Ha más rendszerhéjat használ, például a PowerShellt vagy a parancssort, előfordulhat, hogy ennek megfelelően kell módosítania a sor folytatási karaktereit vagy változó-hozzárendelési sorait. Ez a cikk változók használatával minimalizálja a szükséges parancsszerkesztés mennyiségét.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport egy logikai tároló, amelyben üzembe helyezheti és kezelheti az Azure-erőforrásokat. A következő [az group create parancs létrehoz][az-group-create] egy *myResourceGroup* nevű erőforráscsoportot az *eastus régióban.* Ha más nevet szeretne használni az erőforráscsoporthoz, állítsa másik `RESOURCE_GROUP_NAME` értékre.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>SignalR szolgáltatás létrehozása

Ezután telepítsen egy Azure Signalr szolgáltatást az erőforráscsoportban az alábbi parancsokkal.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

A SignalR Service létrehozása után az Azure CLI az alábbihoz hasonló kimenetet ad vissza:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>Eseményvégpont létrehozása

Ebben a szakaszban egy GitHub Resource Manager tárban található sablonnal fog üzembe helyezni egy előre felépített minta-webalkalmazást a Azure App Service. Később előfizet a regisztrációs adatbázis Event Grid eseményekre, és ezt az alkalmazást adja meg végpontként, amelyre az eseményeket küldi.

A mintaalkalmazás üzembe helyezéséhez állítsa be a webalkalmazás egyedi nevét, és hajtsa végre a `SITE_NAME` következő parancsokat. A webhely nevének egyedinek kell lennie az Azure-ban, mert a webalkalmazás teljes tartománynevének (FQDN) részét képezi. Egy későbbi szakaszban meg kell navigálnia az alkalmazás teljes tartományához egy webböngészőben a regisztrációs adatbázis eseményeinek megtekintéséhez.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Ha az üzembe helyezés sikerült (eltarthat néhány percig), nyisson meg egy böngészőt, és navigáljon a webalkalmazáshoz, és ellenőrizze, hogy fut-e:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Feliratkozás beállításjegyzékbeli eseményekre

Ebben Event Grid feliratkozhat *egy* témakörre, amelyből meg tudja mondani, hogy mely eseményeket szeretné nyomon követni, és hová szeretné küldeni őket. A következő [az eventgrid event-subscription create][az-eventgrid-event-subscription-create] parancs feliratkozik a Azure SignalR Service létrehozott alkalmazásra, és megadja a webalkalmazás URL-címét végpontként, amelyre az eseményeket küldenie kell. A korábbi szakaszokban létrehozott környezeti változókat itt újra felhasználjuk, ezért nincs szükség szerkesztésre.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Az előfizetés befejezése után az alábbihoz hasonló kimenetnek kell lennie:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Beállításjegyzék-események aktiválása

Váltson a szolgáltatási módra, és létesítsen be egy `Serverless Mode` ügyfélkapcsolatot a SignalR Service. Referenciaként [használhatja](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) a Kiszolgáló nélküli mintát.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Beállításjegyzék-események megtekintése

Most már csatlakoztatta az ügyfelet a SignalR Service. Lépjen a Event Grid Viewer webalkalmazáshoz, és látnia kell egy `ClientConnectionConnected` eseményt. Ha megszakítja az ügyfelet, egy eseményt is `ClientConnectionDisconnected` látni fog.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create
[az-group-create]: /cli/azure/group#az_group_create
