---
title: Rövid útmutató – Események küldése a Event Grid
description: Ebben a rövid útmutatóban engedélyezi a Event Grid eseményeket a tároló-beállításjegyzékhez, majd elküldi a tároló rendszerképének leküldéses és törlési eseményeit egy mintaalkalmazásnak.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 43dea2640c9c9445ea464205f6c586bc1e486206
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784024"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Rövid útmutató: Események küldése a privát tároló-beállításjegyzékből a Event Grid

Azure Event Grid egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely egységes eseményfelhasználást biztosít közzétételi-feliratkozás modell használatával. Ebben a rövid útmutatóban az Azure CLI használatával létrehoz egy tároló-beállításjegyzéket, feliratkozik a regisztrációs adatbázis eseményeire, majd üzembe helyez egy minta-webalkalmazást az események fogadásához. Végül aktiválja a tároló rendszerképét és eseményeit, és megtekinti az `push` esemény hasznosadatát a `delete` mintaalkalmazásban.

A cikkben található lépések befejezése után a tároló-beállításjegyzékből a Event Grid a minta-webalkalmazásban:

![Webböngésző, amely a minta-webalkalmazást rendereli három fogadott esemény esetén][sample-app-01]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- A cikkben említett Azure CLI-parancsok a **Bash-felülethez vannak formázva.** Ha más rendszerhéjat használ, például a PowerShellt vagy a parancssort, előfordulhat, hogy ennek megfelelően kell módosítania a sor folytatási karaktereit vagy változó-hozzárendelési sorait. Ez a cikk változók használatával minimalizálja a szükséges parancsszerkesztés mennyiségét.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport egy logikai tároló, amelyben üzembe helyezheti és kezelheti az Azure-erőforrásokat. Az alábbi [az group create parancs][az-group-create] létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus régióban.* Ha más nevet szeretne használni az erőforráscsoporthoz, állítson `RESOURCE_GROUP_NAME` be egy másik értéket.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Ezután telepítsen egy tároló-beállításjegyzéket az erőforráscsoportban az alábbi parancsokkal. Az az [acr create parancs futtatása][az-acr-create] előtt állítsa be a beállításjegyzék `ACR_NAME` nevét. A névnek egyedinek kell lennie az Azure-ban, és 5–50 alfanumerikus karakterre van korlátozva.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

A regisztrációs adatbázis létrehozása után az Azure CLI az alábbihoz hasonló kimenetet ad vissza:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```

## <a name="create-an-event-endpoint"></a>Eseményvégpont létrehozása

Ebben a szakaszban egy GitHub Resource Manager tárban található sablonnal fog üzembe helyezni egy előre felépített minta-webalkalmazást a Azure App Service. Később előfizet a regisztrációs adatbázis Event Grid eseményekre, és ezt az alkalmazást adja meg végpontként, amelyre az eseményeket küldi.

A mintaalkalmazás üzembe helyezéséhez állítsa be a webalkalmazás egyedi nevét, és hajtsa végre a `SITE_NAME` következő parancsokat. A webhely nevének egyedinek kell lennie az Azure-ban, mert a webalkalmazás teljes tartománynevének (FQDN) részét képezi. Egy későbbi szakaszban meg kell navigálnia az alkalmazás teljes tartományához egy webböngészőben a beállításjegyzék eseményeinek megtekintéséhez.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Ha az üzembe helyezés sikeresen lefutott (eltarthat néhány percig), nyisson meg egy böngészőt, és navigáljon a webalkalmazáshoz, és ellenőrizze, hogy fut-e:

`http://<your-site-name>.azurewebsites.net`

A mintaalkalmazást eseményüzenetek megjelenítése nélkül kell renderelni:

![Webböngésző, amely megjeleníti a minta-webalkalmazást, és nem jelenik meg esemény][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Feliratkozás beállításjegyzékbeli eseményekre

Ebben Event Grid feliratkozik egy témakörre, amely tudatja vele, hogy mely eseményeket szeretné nyomon követni, és hová szeretné küldeni őket.  Az [alábbi az eventgrid event-subscription create][az-eventgrid-event-subscription-create] parancs feliratkozik a létrehozott tároló-beállításjegyzékre, és megadja a webalkalmazás URL-címét végpontként, amelyre az eseményeket küldenie kell. A korábbi szakaszokban létrehozott környezeti változókat itt újra felhasználjuk, ezért nincs szükség szerkesztésre.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Az előfizetés befejezése után az alábbihoz hasonló kimenetnek kell lennie:

```json
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Beállításjegyzék-események aktiválása

Most, hogy a mintaalkalmazás működik, és feliratkozott a regisztrációs adatbázisra a Event Grid, készen áll néhány esemény útjára. Ebben a szakaszban a ACR-feladatok egy tároló-rendszerképet hoz létre és fog lekedni a regisztrációs adatbázisba. ACR-feladatok szolgáltatása Azure Container Registry, amely lehetővé teszi tároló-rendszerképek felhőben való felépítését anélkül, hogy a Docker Engine telepítve lenne a helyi gépen.

### <a name="build-and-push-image"></a>Rendszerkép összeállítása és leküldése

Hajtsa végre a következő Azure CLI-parancsot egy tároló rendszerképének GitHub-adattár tartalmából való felépítéséhez. Alapértelmezés szerint a ACR-feladatok automatikusan leküld egy sikeresen felépített rendszerképet a regisztrációs adatbázisba, amely létrehozza az `ImagePushed` eseményt.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

A buildek buildek ACR-feladatok leküldése közben az alábbihoz hasonló kimenetnek kell látsza. Az alábbi példakimenetet a rövidség kedvéért csonkoltuk.

```output
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Annak ellenőrzéséhez, hogy a beépített rendszerkép a beállításjegyzékben van-e, hajtsa végre a következő parancsot a "myimage" adattár címkéinek megtekintéséhez:

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

A kiépített rendszerkép "v1" címkéje az alábbihoz hasonlóan jelenik meg a kimenetben:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>A rendszerkép törlése

Most hozzon létre egy eseményt a rendszerkép az `ImageDeleted` [az acr repository delete paranccsal való törlésével:][az-acr-repository-delete]

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

A következőhöz hasonló kimenetnek kell lennie, amely megerősítést kér a jegyzékfájl és a társított képek törléséhez:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Beállításjegyzék-események megtekintése

Most lekért egy rendszerképet a regisztrációs adatbázisba, majd törölte azt. Lépjen a Event Grid Viewer webalkalmazáshoz, és látnia kell a és az `ImageDeleted` `ImagePushed` eseményt is. A Regisztrációs adatbázis eseményeire való feliratkozás szakaszban található parancs végrehajtásával létrehozott [előfizetés-érvényesítési esemény is előfordulhat.](#subscribe-to-registry-events)

Az alábbi képernyőképen a mintaalkalmazás látható a három eseménysel, az esemény pedig kibontva látható `ImageDeleted` a részleteivel.

![A mintaalkalmazást ImagePushed és ImageDeleted eseményekkel megjelenítő webböngésző][sample-app-03]

Gratulálunk! Ha látja a és az eseményt, a regisztrációs adatbázis eseményeket küld a Event Grid, Event Grid továbbítja ezeket az eseményeket a `ImagePushed` `ImageDeleted` webalkalmazás végpontjának.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett az ebben a rövid útmutatóban létrehozott erőforrásokkal, az alábbi Azure CLI-paranccsal törölheti őket. Erőforráscsoport törlésekor a benne található összes erőforrás véglegesen törlődik.

**FIGYELMEZTETÉS:** Ez a művelet nem visszafordítható. A parancs futtatása előtt győződjön meg arról, hogy már nincs szüksége a csoport egyik erőforrására sem.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Event Grid-eseményséma

A Azure Container Registry üzenetsémára vonatkozó referenciát az Event Grid dokumentációjában találja:

[Azure Event Grid-eseménysémát a Container Registry](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy tároló-beállításjegyzéket, felépített egy rendszerképet az ACR-feladatok-val, törölte azt, és felhasználta a regisztrációs adatbázis eseményeit a Event Grid egy mintaalkalmazással. Ezután lépjen tovább a ACR-feladatok oktatóanyagra, amelyből többet is megtudhat a tárolórendszerképek felhőben való felépítéséről, beleértve az alapként szolgáló rendszerképek frissítésének automatizált buildjére vonatkozó tudnivalókat:

> [!div class="nextstepaction"]
> [Tároló-rendszerképek összeállítása a felhőben ACR-feladatok](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create
[az-group-create]: /cli/azure/group#az_group_create
