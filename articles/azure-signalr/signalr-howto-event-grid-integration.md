---
title: Az Azure szignáló szolgáltatás eseményeinek küldése Event Grid
description: Egy útmutató, amelyből megtudhatja, hogyan engedélyezheti Event Grid eseményeit a Signaler szolgáltatáshoz, majd az ügyfélkapcsolathoz csatlakoztatott/leválasztott eseményeket küldhet egy minta alkalmazásba.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: 84b83c1dd541418c446a89a6f51be668cb41e54e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94562644"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Események küldése az Azure SignalR Service-ből az Event Gridbe

A Azure Event Grid egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely a pub-sub modell használatával egységes esemény-felhasználást biztosít. Ebben az útmutatóban az Azure CLI használatával hozzon létre egy Azure Signaler szolgáltatást, fizessen elő a kapcsolódási eseményekre, majd helyezzen üzembe egy minta webalkalmazást az események fogadásához. Végezetül kapcsolódhat és leválaszthat, és megtekintheti az esemény hasznos adatait a minta alkalmazásban.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - A cikkben szereplő Azure CLI-parancsok a **bash** -rendszerhéjhoz vannak formázva. Ha más, például PowerShell-vagy parancssor-rendszerhéjt használ, lehetséges, hogy a sor folytatási karaktereit vagy a változók hozzárendelési sorait ennek megfelelően kell módosítania. Ez a cikk változók használatával minimálisra csökkentheti a parancsok szerkesztésének szükséges mennyiségét.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelyben üzembe helyezheti és felügyelheti Azure-erőforrásait. A következő az [Group Create][az-group-create] parancs létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *eastus* régióban. Ha más nevet szeretne használni az erőforráscsoport számára, állítsa `RESOURCE_GROUP_NAME` egy másik értékre.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>SignalR szolgáltatás létrehozása

Ezután helyezzen üzembe egy Azure Signaler szolgáltatást az erőforráscsoporthoz az alábbi parancsokkal.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

A jelző szolgáltatás létrehozása után az Azure CLI a következőhöz hasonló kimenetet ad vissza:

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

## <a name="create-an-event-endpoint"></a>Esemény-végpont létrehozása

Ebben a szakaszban egy GitHub-tárházban található Resource Manager-sablont használ egy előre elkészített minta webalkalmazás üzembe helyezéséhez Azure App Service. Később előfizet a beállításjegyzék Event Grid eseményeire, és megadhatja az alkalmazást, mint az eseményeket küldő végpontot.

A minta alkalmazás üzembe helyezéséhez állítson be `SITE_NAME` egy egyedi nevet a webalkalmazás számára, és hajtsa végre a következő parancsokat. A hely nevének egyedinek kell lennie az Azure-ban, mivel az a webalkalmazás teljes tartománynevének (FQDN) részét képezi. Egy későbbi szakaszban navigáljon az alkalmazás teljes tartománynevéhez egy böngészőben a beállításjegyzék eseményeinek megtekintéséhez.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Ha az üzembe helyezés sikeres volt (eltarthat néhány percig), nyisson meg egy böngészőt, és navigáljon a webalkalmazáshoz, és győződjön meg arról, hogy az fut:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Előfizetés a beállításjegyzék eseményeire

Event Grid a *témakörre* való előfizetéssel megtudhatja, hogy mely eseményeket kívánja nyomon követni, és hová szeretné elküldeni őket. A következő az [eventgrid Event-előfizetés-létrehozási][az-eventgrid-event-subscription-create] parancs előfizet a létrehozott Azure Signaler szolgáltatásra, és megadja a webalkalmazás URL-címét a végpontként, amelyhez az eseményeket küldeni kell. A korábbi szakaszokban feltöltött környezeti változók itt lesznek újra felhasználva, ezért nincs szükség módosításra.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Az előfizetés befejezésekor a következőhöz hasonló kimenetnek kell megjelennie:

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

## <a name="trigger-registry-events"></a>Beállításjegyzék-események kiváltása

Váltson a szolgáltatás módba, `Serverless Mode` és állítson be egy ügyfélkapcsolatot a signaler szolgáltatáshoz. A kiszolgáló nélküli [mintát](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) hivatkozásként is elvégezheti.

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

Ezzel csatlakoztatta a-ügyfelet a Signaler szolgáltatáshoz. Navigáljon a Event Grid Viewer webalkalmazáshoz, és egy eseményt kell megjelennie `ClientConnectionConnected` . Ha leállítja az ügyfelet, akkor egy eseményt is láthat `ClientConnectionDisconnected` .

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
