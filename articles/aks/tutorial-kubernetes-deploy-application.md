---
title: Az Azure-on futó Kubernetes oktatóanyaga – Alkalmazás üzembe helyezése
description: Az Azure Kubernetes Service (AKS) ezen oktatóanyagában üzembe fog helyezni egy többtárolós alkalmazást a fürtön egy, az Azure Container Registryben tárolt egyéni rendszerkép használatával.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: a0de097a545a831e39a671fe4cf5eadcd336ce24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98250179"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Oktatóanyag: Alkalmazások futtatása az Azure Kubernetes Service-ben (AKS)

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Ön hozz létre és helyezi üzembe saját alkalmazásait és szolgáltatásait a Kubernetes-fürtön, a rendelkezésre állást és a kapcsolatokat pedig a fürt kezeli. Ebben az oktatóanyagban, amely egy hétrészes sorozat negyedik része, egy alkalmazást helyezünk üzembe egy Kubernetes-fürtön. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kubernetes manifest-fájl frissítése
> * Alkalmazás futtatása a Kubernetesben
> * Az alkalmazás tesztelése

A későbbi oktatóanyagokban az alkalmazás méretezése és frissítése megtörtént.

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt.

Az oktatóanyag teljesítéséhez szüksége lesz az előzőleg létrehozott `azure-vote-all-in-one-redis.yaml` Kubernetes-jegyzékfájlra. Ezt a fájlt az alkalmazás forráskódjával együtt egy korábbi oktatóanyagban letöltöttük. Ellenőrizze, hogy klónozotta-e a tárházat, és hogy módosította-e a címtárakat a klónozott tárházban. Ha még nem tette meg ezeket a lépéseket, és követni szeretné a lépéseket, kezdje az [1. oktatóanyag – tároló lemezképek létrehozásával][aks-tutorial-prepare-app].

Ehhez az oktatóanyaghoz az Azure CLI 2.0.53 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="update-the-manifest-file"></a>A jegyzékfájl frissítése

Ezekben az oktatóanyagokban egy Azure Container Registry- (ACR-) példány tárolja a mintaalkalmazáshoz való tároló rendszerképét. Az alkalmazás üzembe helyezéséhez frissíteni kell a rendszerkép nevét a Kubernetes-jegyzékfájlban úgy, hogy magában foglalja az ACR bejelentkezési kiszolgálójának nevét.

Kérje le az ACR bejelentkezési kiszolgáló nevét az [az acr list][az-acr-list] paranccsal a következőképpen:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Az első oktatóanyagban klónozott, Git-adattárból származó mintajegyzékfájl a *microsoft* bejelentkezési kiszolgálónevet használja. Győződjön meg arról, hogy a klónozott *Azure-szavazó-app-Redis* könyvtárban van, majd nyissa meg a jegyzékfájlt egy szövegszerkesztővel, például `vi` :

```console
vi azure-vote-all-in-one-redis.yaml
```

Helyettesítse be a *microsoft* nevet az Ön ACR bejelentkezési kiszolgálójának nevével. A rendszerkép neve a jegyzékfájl 60. sorában található. Az alábbi példa az alapértelmezett rendszerképnevet mutatja:

```yaml
containers:
- name: azure-vote-front
  image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
```

Adja meg saját ACR bejelentkezési kiszolgálójának nevét, hogy a jegyzékfájl a következő példához hasonlítson:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Mentse és zárja be a fájlt. A `vi` alkalmazásban használja a t `:wq` .

## <a name="deploy-the-application"></a>Az alkalmazás üzembe helyezése

Az alkalmazást a [kubectl apply][kubectl-apply] paranccsal helyezheti üzembe. A parancs elemzi jegyzékfájlt, és létrehozza a meghatározott Kubernetes-objektumokat. Adja meg a mintajegyzékfájlt a következő példa szerint:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

A következő példa kimenetében az AK-fürt sikeresen létrehozott erőforrásai láthatók:

```console
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes szolgáltatás az előtér-alkalmazást az internethez teszi elérhetővé. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```console
kubectl get service azure-vote-front --watch
```

Kezdetben az *Azure-vote-elülső* szolgáltatás *külső IP-címe* az *alábbi módon jelenik* meg:

```output
azure-vote-front   LoadBalancer   10.0.34.242   <pending>     80:30676/TCP   5s
```

Ha a *külső IP-* cím *függőben* ÁLLAPOTRÓL tényleges nyilvános IP-címről változik, akkor a `CTRL-C` figyelési folyamat leállításához használja a következőt: `kubectl` . A következő példa kimenete a szolgáltatáshoz hozzárendelt érvényes nyilvános IP-címet jeleníti meg:

```output
azure-vote-front   LoadBalancer   10.0.34.242   52.179.23.131   80:30676/TCP   67s
```

Az alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére:

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote.png" alt-text="Képernyőfelvétel: a tároló rendszerképét megjelenítő Azure-beli szavazási alkalmazás, amely egy helyi webböngészőben megnyitott AK-fürtön fut" lightbox="./media/container-service-kubernetes-tutorials/azure-vote.png":::

Ha az alkalmazás nem töltődött be, lehetséges, hogy az a rendszerkép-beállításjegyzékkel kapcsolatos hitelesítési probléma oka. A tárolók állapotának megtekintéséhez használja a `kubectl get pods` parancsot. Ha a tároló lemezképeit nem lehet kihúzni, tekintse meg a [hitelesítés a Azure Container Registry az Azure Kubernetes szolgáltatással](cluster-container-registry-integration.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure vote-alkalmazást telepítettünk egy AK-beli Kubernetes-fürtre. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Kubernetes-jegyzékfájlok frissítése
> * Alkalmazás futtatása a Kubernetesben
> * Az alkalmazás tesztelése

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan méretezhető egy Kubernetes-alkalmazás és az alapul szolgáló Kubernetes-infrastruktúra.

> [!div class="nextstepaction"]
> [Kubernetes-alkalmazás és -infrastruktúra méretezése][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
[kubernetes-concepts]: concepts-clusters-workloads.md
[kubernetes-service]: concepts-network.md#services
