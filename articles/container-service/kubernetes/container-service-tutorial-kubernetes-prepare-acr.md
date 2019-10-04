---
title: (ELAVULT) Az Azure Container Service oktatóanyag – az ACR előkészítése
description: Azure Container Service-oktatóanyag – Az ACR előkészítése
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d0107e09bf8706ba7d4c813814103ca109262d8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641177"
---
# <a name="deprecated-deploy-and-use-azure-container-registry"></a>(ELAVULT) Üzembe helyezése és használata Azure Container Registrybe

> [!TIP]
> Ebben az oktatóanyagban, amely használja az Azure Kubernetes Service-ben, lásd: a frissített verzió [oktatóanyag: Üzembe helyezése és használata Azure Container Registry](../../aks/tutorial-kubernetes-prepare-acr.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Az Azure Container Registry (ACR) egy Azure-alapú privát regisztrációs adatbázis Docker-tárolórendszerképekhez. Ez az oktatóanyag, amely egy hétrészes sorozat második része, azt ismerteti, hogyan lehet üzembe helyezni egy Azure Container Registry-példányt, és hogyan lehet továbbítani rá egy tárolórendszerképet. Ennek lépései az alábbiak:

> [!div class="checklist"]
> * Egy Azure Container Registry- (ACR-) példány üzembe helyezése
> * Egy tárolórendszerkép címkézése az ACR-hez
> * A rendszerkép feltöltése az ACR-be

Az ezt követő oktatóanyagokban ezt az ACR-példányt integráljuk egy Azure Container Service-beli Kubernetes-fürttel. 

## <a name="before-you-begin"></a>Előzetes teendők

Az [előző lépésben](./container-service-tutorial-kubernetes-prepare-app.md) létrehoztunk egy tárolórendszerképet egy egyszerű Azure-szavazóalkalmazáshoz. Ha még nem hozta létre az Azure-szavazóalkalmazás rendszerképét, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása](./container-service-tutorial-kubernetes-prepare-app.md) részhez.

Az oktatóanyag elvégzéséhez az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="deploy-azure-container-registry"></a>Azure Container Registry üzembe helyezése

Az Azure Container Registry üzembe helyezéséhez először is szükség van egy erőforráscsoportra. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Ebben a példában egy `myResourceGroup` nevű erőforráscsoport jön létre a `westeurope` régióban.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Hozzon létre egy Azure tárolóregisztrációs adatbázist az [az acr create](/cli/azure/acr#az-acr-create) paranccsal. A tárolóregisztrációs adatbázisoknak **egyedi nevekkel kell rendelkezniük**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Az oktatóanyag hátralevő részében az `<acrname>` elem helyettesíti a tárolóregisztrációs adatbázis nevét.

## <a name="container-registry-login"></a>Bejelentkezés a tárolóregisztrációs adatbázisba

Az [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) paranccsal jelentkezzen be az ACR-példányba. Meg kell adnia a tárolóregisztrációs adatbázis egyedi nevét, amelyet az adatbázis létrehozásakor adott meg.

```azurecli
az acr login --name <acrName>
```

A parancs a „Bejelentkezés sikeres” üzenetet adja vissza, ha befejeződött.

## <a name="tag-container-images"></a>Tárolórendszerképek címkézése

A meglévő rendszerképek listájának megtekintéséhez használja a [docker images](https://docs.docker.com/engine/reference/commandline/images/) parancsot.

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Minden tárolórendszerképet fel kell címkézni a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának nevével. Ezt a címkét a rendszer az útválasztáshoz használja, amikor tárolórendszerképeket küld le egy regisztrációs adatbázisba.

A bejelentkezési kiszolgáló nevének lekéréséhez futtassa a következő parancsot.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Ezután címkézze fel az `azure-vote-front` rendszerképet a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának nevével. Adja hozzá a `:v1` kifejezést a rendszerkép nevének végéhez. Ez a címke a rendszerkép verziószámát jelöli.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Ha elkészült a címkézéssel, futtassa a [docker images](https://docs.docker.com/engine/reference/commandline/images/) parancsot a művelet ellenőrzéséhez.

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Rendszerképek leküldése a regisztrációs adatbázisba

Küldje le az `azure-vote-front` rendszerképet a regisztrációs adatbázisba. 

Az alábbi példában helyettesítse be az ACR bejelentkezési kiszolgálójának nevét az adott környezet bejelentkezési kiszolgálójának nevével.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Ez eltarthat néhány percig.

## <a name="list-images-in-registry"></a>A regisztrációs adatbázisban található rendszerképek felsorolása

Az Azure Container Registrybe elküldött rendszerképek listájának lekéréséhez használja az [az acr repository list](/cli/azure/acr/repository#az-acr-repository-list) parancsot. Frissítse a parancsot az ACR-példány nevével.

```azurecli
az acr repository list --name <acrName> --output table
```

Kimenet:

```azurecli
Result
----------------
azure-vote-front
```

Ezután egy adott rendszerkép címkéinek megtekintéséhez használja az [az acr repository show-tags](/cli/azure/acr/repository) parancsot.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Kimenet:

```azurecli
Result
--------
v1
```

Az oktatóanyag végeztével a tárolórendszerképet egy privát Azure Container Registry-példány tárolja. Ezt a rendszerképet telepítjük az ACR-ből egy Kubernetes-fürtre a következő oktatóanyagok során.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban előkészítettünk egy Azure Container Registry tárolóregisztrációs adatbázist az ACS Kubernetes-fürtben való használatra. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Telepített egy Azure Container Registry-példányt
> * Címkézett egy tárolórendszerképet az ACR-hez
> * Feltöltötte a rendszerképet az ACR-be

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan helyezhető üzembe egy Kubernetes-fürt az Azure-ban.

> [!div class="nextstepaction"]
> [Kubernetes-fürt üzembe helyezése](./container-service-tutorial-kubernetes-deploy-cluster.md)
