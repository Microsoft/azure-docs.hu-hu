---
title: Oktatóanyag – Container-alkalmazás üzembe helyezése a tároló-példányon
description: Azure Container Instances oktatóanyag 3. része – a Container Application üzembe helyezése Azure Container Instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 2ea3d285f00d38df84587d9a7c15242fff38453b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87500572"
---
# <a name="tutorial-deploy-a-container-application-to-azure-container-instances"></a>Oktatóanyag: Container-alkalmazás üzembe helyezése Azure Container Instances

Ez az egy háromrészes sorozat utolsó oktatóanyaga. Az előző oktatóanyagokban [létrehoztunk egy tárolórendszerképet](container-instances-tutorial-prepare-app.md), és [leküldtük az Azure Container Registrybe](container-instances-tutorial-prepare-acr.md). A sorozat befejező cikke a tároló üzembe helyezését ismerteti az Azure Container Instancesben.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A tároló üzembe helyezése az Azure Container Registryből az Azure Container Instancesbe
> * A futó alkalmazás megtekintése a böngészőben
> * A tároló naplóinak megjelenítése

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Tároló üzembe helyezése az Azure CLI használatával

Ebben a szakaszban az Azure CLI használatával üzembe helyezi [az első oktatóanyagban](container-instances-tutorial-prepare-app.md) létrehozott rendszerképet, amelyet [a második oktatóanyagban](container-instances-tutorial-prepare-acr.md) leküldött az Azure Container Registrybe. Mielőtt folytatná, győződjön meg arról, hogy mindkét oktatóanyagot elvégezte.

### <a name="get-registry-credentials"></a>A tárolójegyzék hitelesítő adatainak lekérése

Ha olyan rendszerképet telepít, amely egy, a [második oktatóanyagban](container-instances-tutorial-prepare-acr.md)létrehozott privát Azure Container registryben található, meg kell adnia a hitelesítő adatokat a beállításjegyzék eléréséhez. 

Az ajánlott eljárás sok esetben egy Azure Active Directory egyszerű szolgáltatás létrehozása és konfigurálása a beállításjegyzékhez a *lekéréses* engedélyekkel. A szükséges engedélyekkel rendelkező szolgáltatásnév létrehozásához tekintse meg a [hitelesítés a Azure Container Registry használatával Azure Container instances](../container-registry/container-registry-auth-aci.md) a minta-parancsfájlok segítségével című témakört. Jegyezze fel az *egyszerű szolgáltatás azonosítóját* és a *szolgáltatás egyszerű jelszavát*. Ezeknek a hitelesítő adatoknak a használatával férhet hozzá a beállításjegyzékhez a tároló telepítésekor.

Szüksége lesz a tároló beállításjegyzékbeli bejelentkezési kiszolgálójának teljes nevére is (a helyére a `<acrName>` beállításjegyzék nevével kell helyettesíteni):

```azurecli
az acr show --name <acrName> --query loginServer
```

### <a name="deploy-container"></a>A tároló üzembe helyezése

Az [az container create][az-container-create] paranccsal helyezze üzembe a tárolót. Cserélje le az elemet az `<acrLoginServer>` előző parancsból beszerzett értékre. Cserélje le a és a lehetőséget `<service-principal-ID>` `<service-principal-password>` a beállításjegyzék eléréséhez létrehozott egyszerű szolgáltatásnév és jelszó megadásával. Cserélje le a `<aciDnsLabel>` nevet a kívánt DNS-névre.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <service-principal-ID> --registry-password <service-principal-password> --dns-name-label <aciDnsLabel> --ports 80
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát. A `--dns-name-label` értéknek egyedinek kell lennie abban az Azure-régióban, ahol a tárolópéldányt létrehozza. Módosítsa az előző parancsban szereplő értéket, ha a parancs futtatásakor **DNS-névcímke** hibaüzenetet kap.

### <a name="verify-deployment-progress"></a>Üzembe helyezés állapotának ellenőrzése

Az üzembe helyezés állapotának megtekintéséhez használja az [az container show][az-container-show] parancsot:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Ismételje meg az [az container show][az-container-show] parancsot addig, amíg az állapot *Függőben* helyett *Fut* értékre nem vált, amelynek egy percen belül meg kell történnie. Ha a tároló állapota *Fut*, folytassa a következő lépéssel.

## <a name="view-the-application-and-container-logs"></a>Az alkalmazás és a tároló naplóinak megtekintése

Ha az üzembe helyezés sikeresen megtörtént, az [az container show][az-container-show] paranccsal jelenítse meg a tároló teljes tartománynevét:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Például:
```output
"aci-demo.eastus.azurecontainer.io"
```

A futó alkalmazás megtekintéséhez keresse meg a megjelenített DNS-nevet kedvenc böngészőjében:

![A Hello World alkalmazás a böngészőben][aci-app-browser]

Megtekintheti a tároló naplókimenetét is:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Példa a kimenetre:

```output
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a jelen oktatóanyag-sorozatban létrehozott egyik erőforrásra sem, törölje az erőforráscsoportot és az azokban lévő erőforrásokat az [az group delete][az-group-delete] paranccsal. Ez a parancs törli a létrehozott tárolóregisztrációs adatbázist, valamint a futó tárolót és annak minden kapcsolódó erőforrását is.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban elvégezte a tároló üzembe helyezését az Azure Container Instancesben. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Üzembe helyezte az Azure Container Registryben található tárolót az Azure CLI használatával
> * Megtekintette az alkalmazást a böngészőben
> * Megtekintette a tároló naplóit

Most, hogy megismerte az alapokat, többet is megtudhat az Azure Container Instancesről, például a tárolócsoportok működéséről:

> [!div class="nextstepaction"]
> [Tárolócsoportok az Azure Container Instancesben](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
