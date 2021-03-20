---
title: Rövid útmutató – Docker-tároló üzembe helyezése tároló példányon – Azure CLI
description: Ebben a rövid útmutatóban az Azure CLI használatával gyorsan üzembe helyezhet egy elkülönített Azure Container-példányon futó tároló-webalkalmazást
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom:
- seo-python-october2019
- seodec18
- mvc
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 1c327fc7fc067948b5022f989e6c86f99573bd1a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93100184"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Gyors útmutató: tároló-példány üzembe helyezése az Azure-ban az Azure CLI használatával

A Azure Container Instances használatával a kiszolgáló nélküli Docker-tárolókat az Azure-ban, az egyszerűség és a gyorsaság segítségével futtathatja. Igény szerint üzembe helyezhet egy alkalmazást egy tároló-példányon, ha nincs szüksége a teljes Container-előkészítési platformra, például az Azure Kubernetes szolgáltatásra.

Ebben a rövid útmutatóban az Azure CLI használatával telepít egy elkülönített Docker-tárolót, és az alkalmazását teljes tartománynévvel (FQDN) teszi elérhetővé. Néhány másodperccel az egyetlen központi telepítési parancs végrehajtása után megkeresheti a tárolóban futó alkalmazást:

![Böngészőbe Azure Container Instances üzembe helyezett alkalmazás megtekintése][aci-app-browser]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.55 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure Container Instancest – mint minden Azure-erőforrást – egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

Először hozzon létre egy erőforráscsoportot *myResourceGroup* néven az *eastus* helyen az alábbi [az group create][az-group-create] paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Tároló létrehozása

Most, hogy rendelkezik egy erőforráscsoporttal, futtathat egy tárolót az Azure-ban. Egy tárolópéldány Azure CLI-vel való létrehozásához adjon meg egy erőforráscsoport-nevet, egy tárolópéldánynevet és egy Docker-tárolórendszerképet az [az container create][az-container-create] parancsban. Ebben a rövid útmutatóban a nyilvános `mcr.microsoft.com/azuredocs/aci-helloworld` rendszerképet használhatja. Ez a rendszerkép egy olyan kisméretű webalkalmazást csomagol, amely a statikus HTML-lapokat Node.js.

Közzéteheti a tárolókat az interneten egy vagy több port megnyitásával, egy DNS-névcímke megadásával, vagy mindkettővel. Ebben a rövid útmutatóban egy DNS-név címkével rendelkező tárolót helyez üzembe, hogy a webalkalmazás nyilvánosan elérhető legyen.

Egy tároló-példány elindításához a következőhöz hasonló parancsot kell végrehajtania. Olyan értéket állítson be `--dns-name-label` , amely egyedi azon az Azure-régión belül, ahol létrehozza a példányt. Ha „DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Az állapotát az [az container show][az-container-show] paranccsal ellenőrizheti:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

A parancs futtatásakor a tároló teljes tartományneve (FQDN) és annak kiépítési állapota jelenik meg.

```output
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Ha a tároló `ProvisioningState` **sikeres**, nyissa meg a teljes tartománynevet a böngészőben. Ha egy, az alábbihoz hasonló weboldal jelenik meg, gratulálunk! Sikeresen üzembe helyezett egy Docker-tárolóban futó alkalmazást az Azure-ban.

![Böngészőbe Azure Container Instances üzembe helyezett alkalmazás megtekintése][aci-app-browser]

Ha az alkalmazás nem indul el azonnal, lehet, hogy csak várnia kell pár másodpercet, amíg a DNS elvégzi a propagálást. Ezt követően próbálja ismét frissíteni a böngészőjét.

## <a name="pull-the-container-logs"></a>A tároló naplóinak lekérése

Amikor hibaelhárítást kell végrehajtania egy tárolón, vagy az abban futtatott alkalmazáson (vagy csak meg kell néznie a kimenetet), először tekintse meg a tárolópéldány naplóit.

A tárolópéldány naplóit az [az container logs][az-container-logs] paranccsal hívhatja le:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

A kimenet megjeleníti a tároló naplóit, és a HTTP GET kéréseknek is meg kell jelenniük, amelyek akkor jöttek létre, amikor az alkalmazást a böngészőjében megtekintette.

```output
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>Kimeneti stream csatolása

A naplók megtekintése mellett helyi standard kimeneti és hibastreameket csatolhat a tárolóhoz.

Először hajtsa végre az az [Container Attach][az-container-attach] paranccsal, hogy csatolja a helyi konzolt a tároló kimeneti streamekhez:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

A csatolást követően frissítse a böngészőt néhány alkalommal, hogy további kimeneteket hozzon létre. Amikor kész, válassza le a konzolt a `Control+C` paranccsal. A következőhöz hasonló kimenetnek kell megjelennie:

```output
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a tárolóval, az [az container delete][az-container-delete] parancs használatával távolíthatja el:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

A tároló törlésének ellenőrzéséhez futtassa az [az container list](/cli/azure/container#az-container-list) parancsot:

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

A **mycontainer** tárolónak nem szabad megjelennie a parancs kimenetében. Ha az erőforráscsoportban nincs másik tároló, akkor semmilyen kimenet nem jelenik meg.

Ha végzett a *myResourceGroup* erőforráscsoporttal és az abban lévő erőforrásokkal, törölje le az [az group delete][az-group-delete] paranccsal:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container-példányt egy nyilvános Microsoft-rendszerkép használatával. Ha szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy privát Azure-tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](./container-instances-tutorial-prepare-app.md)

Ha szeretné kipróbálni a tárolók futtatásának lehetőségeit az Azure-ban, tekintse meg az [Azure Kubernetes szolgáltatás (ak)][container-service] rövid útmutatóját.

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/view-an-application-running-in-an-azure-container-instance.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
