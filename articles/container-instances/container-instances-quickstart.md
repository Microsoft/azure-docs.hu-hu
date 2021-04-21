---
title: Rövid útmutató – Docker-tároló üzembe helyezése tárolópéldányon – Azure CLI
description: Ebben a rövid útmutatóban az Azure CLI használatával gyorsan üzembe helyezhet egy elkülönített Azure-tárolópéldányon futó tárolóba helyezett webalkalmazást
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom:
- seo-python-october2019
- seodec18
- mvc
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 2027bdc4e77fefe2219b22671494b6d4f4b0ccb8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763972"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Rövid útmutató: Tárolópéldány üzembe helyezése az Azure-ban az Azure CLI használatával

A Azure Container Instances egyszerűséggel és sebességgel futtathat kiszolgáló nélküli Docker-tárolókat az Azure-ban. Igény szerint helyezhet üzembe egy alkalmazást egy tárolópéldányon, ha nincs szüksége egy teljes körű tárolóvezénylési platformra, például a Azure Kubernetes Service.

Ebben a rövid útmutatóban az Azure CLI használatával üzembe helyez egy elkülönített Docker-tárolót, és elérhetővé teszi az alkalmazását egy teljes tartománynévvel (FQDN). Néhány másodperccel egyetlen üzembe helyezési parancs végrehajtása után megkeresheti a tárolóban futó alkalmazást:

![A böngészőben üzembe helyezett Azure Container Instances megtekintése][aci-app-browser]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.55-ös vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure Container Instancest – mint minden Azure-erőforrást – egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

Először hozzon létre egy erőforráscsoportot *myResourceGroup* néven az *eastus* helyen az alábbi [az group create][az-group-create] paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Tároló létrehozása

Most, hogy rendelkezik egy erőforráscsoporttal, futtathat egy tárolót az Azure-ban. Egy tárolópéldány Azure CLI-vel való létrehozásához adjon meg egy erőforráscsoport-nevet, egy tárolópéldánynevet és egy Docker-tárolórendszerképet az [az container create][az-container-create] parancsban. Ebben a rövid útmutatóban a nyilvános rendszerképet `mcr.microsoft.com/azuredocs/aci-helloworld` használja. Ez a kép egy statikus HTML-oldalt Node.js webalkalmazást tartalmaz.

Közzéteheti a tárolókat az interneten egy vagy több port megnyitásával, egy DNS-névcímke megadásával, vagy mindkettővel. Ebben a rövid útmutatóban egy DNS-névcímkével fog üzembe helyezni egy tárolót, hogy a webalkalmazás nyilvánosan elérhető legyen.

A tárolópéldányok indításhoz hajtsa végre az alábbihoz hasonló parancsot. Állítson be egy egyedi értéket abban az `--dns-name-label` Azure-régióban, ahol a példányt létrehozza. Ha „DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

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

Ha a tároló teljes tartománya Sikeres, a böngészőben menjen a teljes `ProvisioningState` tartomány útjára.  Ha egy, az alábbihoz hasonló weboldal jelenik meg, gratulálunk! Sikeresen üzembe helyezett egy Docker-tárolóban futó alkalmazást az Azure-ban.

![A böngészőben üzembe helyezett Azure Container Instances megtekintése][aci-app-browser]

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

Először hajtsa végre [az az container attach][az-container-attach] parancsot, hogy csatolja a helyi konzolt a tároló kimeneti streamjéhez:

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

A tároló törlésének ellenőrzéséhez futtassa az [az container list](/cli/azure/container#az_container_list) parancsot:

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

A **mycontainer** tárolónak nem szabad megjelennie a parancs kimenetében. Ha az erőforráscsoportban nincs másik tároló, akkor semmilyen kimenet nem jelenik meg.

Ha végzett a *myResourceGroup* erőforráscsoporttal és az abban lévő erőforrásokkal, törölje le az [az group delete][az-group-delete] paranccsal:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy nyilvános Microsoft-rendszerkép használatával létrehozott egy Azure-tárolópéldányt. Ha szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy privát Azure-tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](./container-instances-tutorial-prepare-app.md)

A tárolók Azure-beli vezénylési rendszerben való futtatásának lehetőségeiért tekintse meg az Azure Kubernetes Service [(AKS)][container-service] rövid útmutatóit.

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/view-an-application-running-in-an-azure-container-instance.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-create]: /cli/azure/container#az_container_create
[az-container-delete]: /cli/azure/container#az_container_delete
[az-container-list]: /cli/azure/container#az_container_list
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
