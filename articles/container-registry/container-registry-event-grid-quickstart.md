---
title: Rövid útmutató – események küldése Event Gridba
description: Ebben a rövid útmutatóban engedélyezheti Event Grid eseményeit a tároló-beállításjegyzékben, majd elküldheti a tároló-rendszerkép leküldését és az események törlését egy minta alkalmazásba.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: b4038bb232c14278e838f2c34706dcc103def7e4
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919663"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Gyors útmutató: események küldése a privát tároló beállításjegyzékből a Event Gridba

A Azure Event Grid egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely egységes esemény-felhasználást biztosít a közzétételi és előfizetési modell használatával. Ebben a rövid útmutatóban az Azure CLI használatával hozzon létre egy tároló-beállításjegyzéket, fizessen elő a beállításjegyzék eseményeire, majd helyezzen üzembe egy minta webalkalmazást az események fogadásához. Végezetül aktiválja a tároló képét `push` és `delete` eseményeit, és tekintse meg az esemény hasznos adatait a minta alkalmazásban.

A cikk lépéseinek elvégzése után a tároló-beállításjegyzékből eljuttatott események Event Grid megjelennek a minta-webalkalmazásban:

![Webböngésző – a minta-webalkalmazás három fogadott eseménysel való megjelenítése][sample-app-01]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- A cikkben szereplő Azure CLI-parancsok a **bash** -rendszerhéjhoz vannak formázva. Ha más, például PowerShell-vagy parancssor-rendszerhéjt használ, lehetséges, hogy a sor folytatási karaktereit vagy a változók hozzárendelési sorait ennek megfelelően kell módosítania. Ez a cikk változók használatával minimálisra csökkentheti a parancsok szerkesztésének szükséges mennyiségét.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelyben üzembe helyezheti és felügyelheti Azure-erőforrásait. A következő az [Group Create][az-group-create] parancs létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *eastus* régióban. Ha más nevet szeretne használni az erőforráscsoport számára, állítsa `RESOURCE_GROUP_NAME` egy másik értékre.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Ezután helyezzen üzembe egy tároló-beállításjegyzéket az erőforráscsoporthoz az alábbi parancsokkal. Az az [ACR Create][az-acr-create] parancs futtatása előtt állítsa be a `ACR_NAME` beállításjegyzék nevét. A névnek egyedinek kell lennie az Azure-on belül, és 5-50 alfanumerikus karakterre van korlátozva.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

A beállításjegyzék létrehozása után az Azure CLI a következőhöz hasonló kimenetet ad vissza:

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

Meg kell jelennie a példaként megjelenített alkalmazásnak, amely nem jeleníti meg az esemény üzeneteit:

![Webböngészőben megjelenített, események nélküli minta webalkalmazás][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Előfizetés a beállításjegyzék eseményeire

Event Grid a *témakörre* való előfizetéssel megtudhatja, hogy mely eseményeket kívánja nyomon követni, és hová szeretné elküldeni őket. A következő az [eventgrid Event-előfizetés-létrehozási][az-eventgrid-event-subscription-create] parancs előfizet a létrehozott tároló-beállításjegyzékre, és megadja a webalkalmazás URL-címét, amelynek a végpontjának kell elküldeni az eseményeket. A korábbi szakaszokban feltöltött környezeti változók itt lesznek újra felhasználva, ezért nincs szükség módosításra.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Az előfizetés befejezésekor a következőhöz hasonló kimenetnek kell megjelennie:

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

## <a name="trigger-registry-events"></a>Beállításjegyzék-események kiváltása

Most, hogy már működik a minta alkalmazás, és előfizetett a beállításjegyzékbe Event Grid, készen áll néhány esemény előállítására. Ebben a szakaszban ACR-feladatokat használ a tárolók rendszerképének kiépítéséhez és leküldéséhez a beállításjegyzékbe. Az ACR-feladatok az Azure Container Registry egyik funkciója, amely lehetővé teszi a tároló lemezképek létrehozását a felhőben anélkül, hogy a Docker-motort telepíteni kellene a helyi gépre.

### <a name="build-and-push-image"></a>Rendszerkép létrehozása és leküldése

A következő Azure CLI-parancs végrehajtásával hozzon létre egy tároló-rendszerképet egy GitHub-tárház tartalmából. Alapértelmezés szerint az ACR-feladatok automatikusan leküldenek egy sikeresen létrehozott rendszerképet a beállításjegyzékbe, amely létrehozza az `ImagePushed` eseményt.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

A következőhöz hasonló kimenetnek kell megjelennie, míg az ACR-feladatok buildek, majd leküldi a rendszerképet. A következő minta kimenete rövidítve lett csonkítva.

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

Annak ellenőrzéséhez, hogy a beépített rendszerkép szerepel-e a beállításjegyzékben, hajtsa végre a következő parancsot a címkék megtekintéséhez a "MyImage" adattárban:

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

A létrehozott rendszerkép "v1" címkéje a kimenetben jelenik meg, a következőhöz hasonlóan:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>A rendszerkép törlése

Most állítson elő egy `ImageDeleted` eseményt a rendszerkép törlésével az az [ACR repository delete][az-acr-repository-delete] paranccsal:

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Az alábbihoz hasonló kimenetnek kell megjelennie, amely megerősítést kér a jegyzékfájl és a kapcsolódó rendszerképek törléséhez:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Beállításjegyzék-események megtekintése

Most leküldte a rendszerképet a beállításjegyzékbe, majd törölte. Navigáljon a Event Grid Viewer webalkalmazáshoz, és mindkettőt és eseményt kell látnia `ImageDeleted` `ImagePushed` . Előfordulhat, hogy megjelenik egy előfizetés-ellenőrzési esemény is, amelyet a [Feliratkozás a beállításjegyzék eseményeire](#subscribe-to-registry-events) című szakaszban található parancs végrehajtásával generált.

Az alábbi képernyőfelvételen a három eseménnyel rendelkező minta alkalmazás látható, az `ImageDeleted` esemény pedig kibontva jelenik meg a részletek megjelenítéséhez.

![Webböngésző, amely a ImagePushed és a ImageDeleted eseményekkel rendelkező minta alkalmazást jeleníti meg][sample-app-03]

Gratulálunk! Ha a és az `ImagePushed` `ImageDeleted` eseményeket látja, a beállításjegyzék az eseményeket Event Gridba küldi, és Event Grid továbbítja ezeket az eseményeket a webalkalmazás-végpontnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült az ebben a rövid útmutatóban létrehozott erőforrásokkal, az alábbi Azure CLI-paranccsal törölheti őket. Egy erőforráscsoport törlésekor a benne lévő összes erőforrás véglegesen törlődik.

**Figyelmeztetés**: Ez a művelet nem visszafordíthatatlan. A parancs futtatása előtt győződjön meg arról, hogy már nincs szüksége a csoport erőforrásaira.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Event Grid-eseményséma

A Event Grid dokumentációjában találhatja meg az Azure Container Registry esemény-üzenet sémájának hivatkozását:

[Container Registry Azure Event Gridi esemény sémája](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy tároló-beállításjegyzéket, amely egy ACR-feladatokból álló képet készített, törölte azt, és felhasználta a beállításjegyzék eseményeit Event Grid egy minta alkalmazással. Ezután lépjen be az ACR-feladatok oktatóanyagba, ahol többet tudhat meg a Felhőbeli tároló-lemezképek létrehozásáról, beleértve az alapszintű lemezkép frissítésének automatizált buildeit is:

> [!div class="nextstepaction"]
> [Tároló lemezképek létrehozása a felhőben ACR-feladatokkal](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
