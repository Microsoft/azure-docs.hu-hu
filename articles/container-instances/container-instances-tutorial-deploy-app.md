---
title: Oktatóanyag – tároló alkalmazás üzembe helyezése az Azure Container Instances szolgáltatásban
description: Az Azure Container Instances oktatóanyaga 3 / 3. rész – a tároló üzembe helyezése az Azure Container Instances szolgáltatásban
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 210254a4404a5280e326bf40057331a784ff6148
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684195"
---
# <a name="tutorial-deploy-a-container-application-to-azure-container-instances"></a>Oktatóanyag: Tároló üzembe helyezése az Azure Container Instances szolgáltatásban

Ez az egy háromrészes sorozat utolsó oktatóanyaga. Az előző oktatóanyagokban [létrehoztunk egy tárolórendszerképet](container-instances-tutorial-prepare-app.md), és [leküldtük az Azure Container Registrybe](container-instances-tutorial-prepare-acr.md). A sorozat befejező cikke a tároló üzembe helyezését ismerteti az Azure Container Instancesben.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A tároló üzembe helyezése az Azure Container Registryből az Azure Container Instancesbe
> * A futó alkalmazás megtekintése a böngészőben
> * A tároló naplóinak megjelenítése

## <a name="before-you-begin"></a>Előzetes teendők

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Tároló üzembe helyezése az Azure CLI használatával

Ebben a szakaszban az Azure CLI használatával üzembe helyezi [az első oktatóanyagban](container-instances-tutorial-prepare-app.md) létrehozott rendszerképet, amelyet [a második oktatóanyagban](container-instances-tutorial-prepare-acr.md) leküldött az Azure Container Registrybe. Mielőtt folytatná, győződjön meg arról, hogy mindkét oktatóanyagot elvégezte.

### <a name="get-registry-credentials"></a>A tárolójegyzék hitelesítő adatainak lekérése

Például a létrehozott egy privát tárolójegyzékben üzemeltetett lemezkép központi telepítésekor a [második oktatóanyagban](container-instances-tutorial-prepare-acr.md), meg kell adnia a beállításjegyzék eléréséhez szükséges hitelesítő adatokat. Ahogyan az [hitelesítés az Azure Container Instances szolgáltatásban az Azure Container Registryvel](../container-registry/container-registry-auth-aci.md), számos ajánlott létrehozni és konfigurálni egy Azure Active Directory egyszerű szolgáltatások *lekéréses*engedélyeket a tárolójegyzékbe. Tekintse meg, hogy a cikk egy szolgáltatásnév létrehozásához szükséges engedélyekkel a minta parancsprogramok. Jegyezze fel a szolgáltatásnév-Azonosítót és a szolgáltatásnévhez tartozó jelszót. A tároló telepítésekor használhatja ezeket a hitelesítő adatokat.

Emellett a tárolójegyzék bejelentkezési kiszolgálójának teljes nevét (cserélje le `<acrName>` a tárolójegyzék nevére):

```azurecli
az acr show --name <acrName> --query loginServer
```

### <a name="deploy-container"></a>A tároló üzembe helyezése

Az [az container create][az-container-create] paranccsal helyezze üzembe a tárolót. Cserélje le `<acrLoginServer>` az előző paranccsal beszerzett értékkel. Cserélje le `<service-principal-ID>` és `<service-principal-password>` a szolgáltatásnév-Azonosítót és a beállításjegyzék eléréséhez létrehozott jelszót. Cserélje le `<aciDnsLabel>` kívánt DNS-névvel.

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

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Példa:
```console
$ az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
"aci-demo.eastus.azurecontainer.io"
```

A futó alkalmazás megtekintéséhez keresse meg a megjelenített DNS-nevet kedvenc böngészőjében:

![A Hello World alkalmazás a böngészőben][aci-app-browser]

Megtekintheti a tároló naplókimenetét is:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Példa a kimenetre:

```bash
$ az container logs --resource-group myResourceGroup --name aci-tutorial-app
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a jelen oktatóanyag-sorozatban létrehozott egyik erőforrásra sem, törölje az erőforráscsoportot és az azokban lévő erőforrásokat az [az group delete][az-group-delete] paranccsal. Ez a parancs törli a létrehozott tárolóregisztrációs adatbázist, valamint a futó tárolót és annak minden kapcsolódó erőforrását is.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

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
