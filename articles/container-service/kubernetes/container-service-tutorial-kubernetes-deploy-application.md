---
title: ELAVULT Azure Container Service oktatóanyag – alkalmazás üzembe helyezése
description: Azure Container Service oktatóanyag – Alkalmazás üzembe helyezése
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 2c2d50da4328ff07c3d2fda4a8721839aa3aa6e7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78274052"
---
# <a name="deprecated-run-applications-in-kubernetes"></a>ELAVULT Alkalmazások futtatása a Kubernetes-ben

> [!TIP]
> Az Azure Kubernetes szolgáltatást használó oktatóanyag frissített verziója: [oktatóanyag: alkalmazások futtatása az Azure Kubernetes szolgáltatásban (ak)](../../aks/tutorial-kubernetes-deploy-application.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Ebben az oktatóanyagban, amely egy hétrészes sorozat negyedik része, egy alkalmazást helyezünk üzembe egy Kubernetes-fürtön. Ennek lépései az alábbiak:

> [!div class="checklist"]
> * Kubernetes-jegyzékfájlok frissítése
> * Alkalmazás futtatása a Kubernetesben
> * Az alkalmazás tesztelése

Az ezt követő oktatóanyagokban horizontálisan felskálázzuk és frissítjük az alkalmazást, és a Log Analyticst a Kubernetes-fürt monitorozására konfiguráljuk.

Ez az oktatóanyag feltételezi, hogy ismeri a Kubernetes alapvető fogalmait. A Kubernetesszel kapcsolatos részletes információkat a [Kubernetes dokumentációjában](https://kubernetes.io/docs/home/) találja.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt. 

Az oktatóanyag teljesítéséhez szüksége lesz az előzőleg létrehozott `azure-vote-all-in-one-redis.yml` Kubernetes-jegyzékfájlra. Ezt a fájlt az alkalmazás forráskódjával együtt egy korábbi oktatóanyagban letöltöttük. Bizonyosodjon meg róla, hogy az adattár klónja létrejött, és hogy a könyvtárakat átállította a klónozott adattárra.

Ha ezeket a lépéseket még nem hajtotta végre, és szeretné követni az oktatóanyagot, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása](./container-service-tutorial-kubernetes-prepare-app.md) részhez. 

## <a name="update-manifest-file"></a>A jegyzékfájl frissítése

Ebben az oktatóanyagban az Azure Container Registryt (ACR) tárolólemezkép tárolására használtuk. Mielőtt futtatná az alkalmazást, az ACR bejelentkezési kiszolgálójának nevét frissíteni kell a Kubernetes-jegyzékfájlban.

Kérje le az ACR bejelentkezési kiszolgáló nevét az [az acr list](/cli/azure/acr#az-acr-list) paranccsal.

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A jegyzékfájlt előzőleg a `microsoft` bejelentkezési kiszolgálónévvel hoztuk létre. Nyissa meg a fájlt bármilyen szövegszerkesztőben. Jelen példában a fájlt a `vi` eszközzel nyitjuk meg.

```bash
vi azure-vote-all-in-one-redis.yml
```

Helyettesítse be a `microsoft` nevet az ACR bejelentkezési kiszolgálójának nevével. Ez az érték a jegyzékfájl **47**. sorában található.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Mentse és zárja be a fájlt.

## <a name="deploy-application"></a>Alkalmazás üzembe helyezése

Az alkalmazást a [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) paranccsal futtathatja. A parancs elemzi jegyzékfájlt, és létrehozza a meghatározott Kubernetes-objektumokat.

```console
kubectl create -f azure-vote-all-in-one-redis.yml
```

Kimenet:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Alkalmazás tesztelése

A rendszer létrehoz egy [Kubernetes-szolgáltatást](https://kubernetes.io/docs/concepts/services-networking/service/), amely közzéteszi az alkalmazást az interneten. Ez eltarthat pár percig. 

A folyamat állapotának monitorozásához használja [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot a `--watch` argumentummal.

```console
kubectl get service azure-vote-front --watch
```

Kezdetben az `azure-vote-front` szolgáltatás **EXTERNAL-IP** értéke `pending` állapotú. Miután az EXTERNAL-IP cím `pending` állapotról `IP address` állapotúra változik, a `CTRL-C` billentyűparanccsal állítsa le a kubectl figyelési folyamatát.

```output
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Az alkalmazás megtekintéséhez navigáljon a külső IP-címhez.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>További lépések

Az oktatóanyagban az Azure Vote alkalmazást egy Azure Container Service-beli Kubernetes-fürtön helyeztünk üzembe. Az eddig végrehajtott feladatok a következők:  

> [!div class="checklist"]
> * Letöltöttük a Kubernetes-jegyzékfájlokat
> * Futtattuk az alkalmazást a Kubernetesben
> * Teszteltük az alkalmazást

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan méretezhető együtt egy Kubernetes-alkalmazás, és az alapul szolgáló Kubernetes-infrastruktúra. 

> [!div class="nextstepaction"]
> [Kubernetes-alkalmazás és -infrastruktúra méretezése](./container-service-tutorial-kubernetes-scale.md)
