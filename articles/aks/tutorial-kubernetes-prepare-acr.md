---
title: Az Azure-on futó Kubernetes oktatóanyaga – Tárolóregisztrációs adatbázis létrehozása
description: Az Azure Kubernetes Service (AKS) jelen oktatóanyagában egy Azure Container Registry-példányt hozhat létre, és feltöltheti egy mintaalkalmazás tárolórendszerképét.
services: container-service
ms.topic: tutorial
ms.date: 01/31/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9f6ec14cea20192aef7d3010201e6613c5d03a9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99430964"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Oktatóanyag: Az Azure Container Registry üzembe helyezése és használata

A Azure Container Registry (ACR) a tároló lemezképek privát beállításjegyzéke. A privát tárolóregisztrációs adatbázisok segítségével biztonságosan állíthatja össze és helyezheti üzembe alkalmazásait és egyéni kódját. Ez az oktatóanyag egy hétrészes sorozat második része, és azt ismerteti, hogyan lehet üzembe helyezni egy ACR-példányt, és hogyan lehet továbbítani rá egy tárolórendszerképet. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Azure Container Registry (ACR)-példány létrehozása
> * Egy tárolórendszerkép címkézése az ACR-hez
> * A rendszerkép feltöltése az ACR-be
> * A regisztrációs adatbázisban lévő rendszerképek megtekintése

A későbbi oktatóanyagokban ez az ACR-példány egy AK-beli Kubernetes-fürttel van integrálva, és egy alkalmazás a lemezképből lett üzembe helyezve.

## <a name="before-you-begin"></a>Előkészületek

Az [előző lépésben][aks-tutorial-prepare-app] létrehoztunk egy tárolórendszerképet egy egyszerű Azure-szavazóalkalmazáshoz. Ha még nem hozta létre az Azure-szavazóalkalmazás rendszerképét, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.

Ehhez az oktatóanyaghoz az Azure CLI 2.0.53 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása

Az Azure Container Registry létrehozásához először is egy erőforráscsoportra lesz szükség. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. A következő példában egy *myResourceGroup* nevű erőforráscsoportot hozunk létre az *eastus* régióban:

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy Azure Container Registry-példányt az [az acr create][az-acr-create] paranccsal, és nevezze el a saját regisztrációs adatbázisát. A tárolóregisztrációs adatbázis nevének egyedinek kell lennie az Azure-ban, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az oktatóanyag hátralevő részében az `<acrName>` elem helyettesíti a tárolóregisztrációs adatbázis nevét. Adja meg a saját egyedi regisztrációs nevét. Az *Alapszintű* termékváltozat költséghatékony, fejlesztési célú belépési pontként szolgál, és kiegyenlített tárolási kapacitást és teljesítményt biztosít.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>Bejelentkezés a tárolóregisztrációs adatbázisba

Az ACR-példány használatához először be kell jelentkeznie. Használja az [az acr login][az-acr-login] parancsot, és adja meg a tárolóregisztrációs adatbázis az előző lépésben megadott egyedi nevét.

```azurecli
az acr login --name <acrName>
```

A parancs a *Bejelentkezés sikeres* üzenetet adja vissza, ha befejeződött.

## <a name="tag-a-container-image"></a>A tárolórendszerképek címkézése

A meglévő helyi rendszerképek listájának megtekintéséhez használja a [docker images][docker-images] parancsot:

```console
docker images
```
A fenti parancs kimenete a jelenlegi helyi rendszerképek listáját jeleníti meg:

```output
REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        7 minutes ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

Az *azure-vote-front* tárolórendszerkép ACR-ben való használatához a rendszerképet fel kell címkézni a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának címével. Ezt a címkét a rendszer az útválasztáshoz használja, amikor tárolórendszerképeket küld le egy regisztrációs adatbázisba.

A bejelentkezési kiszolgáló címének lekéréséhez az [az acr list][az-acr-list] paranccsal keresse meg a *loginServer* kiszolgálót az alábbiak szerint:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Most címkézze fel a helyi *Azure-vote-elülső* rendszerképet a Container Registry *acrLoginServer* -címeként. A rendszerkép verziójának jelölésére adja hozzá a *:v1* címkét a rendszerkép nevének végéhez:

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v1
```

A címkék kiosztásának ellenőrzéséhez futtassa ismét a [docker images][docker-images] parancsot.

```console
docker images
```

Ez felcímkézi a rendszerképet az ACR-példány címével és egy verziószámmal.

```
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front    v1                  84b41c268ad9        16 minutes ago      944MB
mycontainerregistry.azurecr.io/azure-vote-front v1                  84b41c268ad9        16 minutes ago      944MB
mcr.microsoft.com/oss/bitnami/redis             6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                      python3.6           a16ce562e863        6 weeks ago         944MB
```

## <a name="push-images-to-registry"></a>Rendszerképek leküldése a regisztrációs adatbázisba

A létrehozott és címkézett képpel küldje el az *Azure-vote-elülső* rendszerképet az ACR-példányra. Használja a [docker push][docker-push] parancsot, és adja meg a saját *acrLoginServer* címet a rendszerkép neveként az alábbiak szerint:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

A rendszerkép ACR-be való leküldése eltarthat néhány percig.

## <a name="list-images-in-registry"></a>A regisztrációs adatbázisban található rendszerképek felsorolása

Az ACR-példányra leküldött rendszerképek listájának lekéréséhez használja az [az acr repository list][az-acr-repository-list] parancsot. Adja meg a saját `<acrName>` adatbázisnevét:

```azurecli
az acr repository list --name <acrName> --output table
```

A következő példakimeneten az *azure-vote-front* rendszerkép szerepel a regisztrációs adatbázis listájában:

```output
Result
----------------
azure-vote-front
```

Egy adott rendszerkép címkéinek megtekintéséhez használja az [az acr repository show-tags][az-acr-repository-show-tags] parancsot az alábbiak szerint:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

A következő példakimeneten az egyik előző lépésben felcímkézett *v1* rendszerkép látható:

```output
Result
--------
v1
```

Így most rendelkezik egy privát Azure Container Registry-példányon tárolt tárolórendszerképpel. Ezt a rendszerképet telepítjük az ACR-ből egy Kubernetes-fürtre a következő oktatóanyagban.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure Container Registry tárolóregisztrációs adatbázist hozott létre, és leküldött egy rendszerképet egy AKS-fürtben való használatra. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Container Registry (ACR)-példány létrehozása
> * Egy tárolórendszerkép címkézése az ACR-hez
> * A rendszerkép feltöltése az ACR-be
> * A regisztrációs adatbázisban lévő rendszerképek megtekintése

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan helyezhető üzembe egy Kubernetes-fürt az Azure-ban.

> [!div class="nextstepaction"]
> [Kubernetes-fürt üzembe helyezése][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
