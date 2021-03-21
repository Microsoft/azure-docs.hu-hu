---
title: Azure-on futó Kubernetes oktatóanyag – Fürtök üzembe helyezése
description: Az Azure Kubernetes Service (AKS) ezen oktatóanyagában egy AKS-fürtöt fog létrehozni, és kapcsolódni fog a Kubernetes-főcsomóponthoz a kubectl használatával.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c39169c0531a73bd00db7de5fe393ef8c51c8c96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102509421"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Oktatóanyag: Azure Kubernetes Service- (AKS-) fürt üzembe helyezése

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Az AK használatával gyorsan létrehozhat egy éles használatra kész Kubernetes-fürtöt. Ebben az oktatóanyagban, amely egy hétrészes sorozat harmadik része, egy Kubernetes-fürtöt helyezünk üzembe az AKS-ben. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az Azure Container registryben hitelesítésre képes Kubernetes AK-fürt üzembe helyezése
> * A Kubernetes parancssori felület (kubectl) telepítése
> * A kubectl konfigurálása az AKS-fürthöz való csatlakozásra

A későbbi oktatóanyagokban az Azure vote alkalmazást üzembe helyezi a fürtön, méretezhető és frissül.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban létrehoztunk egy tárolórendszerképet, és feltöltöttük egy Azure Container Registry-példányra. Ha még nem tette meg ezeket a lépéseket, és szeretné követni, kezdje az [1. oktatóanyag – tároló lemezképek létrehozása][aks-tutorial-prepare-app]című témakörben.

Ehhez az oktatóanyaghoz az Azure CLI 2.0.53 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Az AK-fürtök Kubernetes szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC) használhatnak. Ezekkel a vezérlőkkel a felhasználókhoz rendelt szerepkörök alapján definiálható az erőforrásokhoz való hozzáférés. Az engedélyek kombinálva vannak, ha egy felhasználó több szerepkörhöz van rendelve, és az engedélyek hatóköre egyetlen névtérre vagy a teljes fürtre is kiterjed. Alapértelmezés szerint az Azure CLI automatikusan engedélyezi a Kubernetes RBAC, amikor létrehoz egy AK-fürtöt.

AKS-fürtöket az [az aks create][] paranccsal hozhat létre. A következő példában létrehozunk egy *myAKSCluster* nevű fürtöt a *myResourceGroup* nevű erőforráscsoportban. Ez az erőforráscsoport az [előző oktatóanyagban][aks-tutorial-prepare-acr] , a *eastus* régióban lett létrehozva. A következő példa nem ad meg régiót, így az AK-fürt is létrejön az *eastus* régióban. További információ: a [kvóták, a virtuálisgép-méretre vonatkozó korlátozások és a régió rendelkezésre állása az Azure Kubernetes szolgáltatásban (ak)][quotas-skus-regions] további információ az erőforrás-korlátokról és a régió rendelkezésre állásáról az AK-ban.

Annak engedélyezéséhez, hogy egy AK-fürt más Azure-erőforrásokkal is működjön, automatikusan létrejön egy fürt identitása, mert nem adott meg ilyet. Itt a fürt identitása jogosult az előző oktatóanyagban létrehozott Azure Container Registry (ACR) példányból származó [képek lekérésére][container-registry-integration] . A parancs sikeres végrehajtásához rendelkeznie kell egy **tulajdonos** vagy **Azure-fiók rendszergazdai** szerepkörrel az Azure-előfizetésben.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Ha el szeretné kerülni a **tulajdonos** vagy az **Azure-fiók rendszergazdai** szerepkörének megkövetelését, manuálisan is konfigurálhat egy egyszerű szolgáltatást a rendszerképek ACR-ből való lekéréséhez. További információkért tekintse meg az [ACR-hitelesítés egyszerű szolgáltatásokkal](../container-registry/container-registry-auth-service-principal.md) vagy a [Kubernetes egy lekéréses titokkal történő hitelesítését](../container-registry/container-registry-auth-kubernetes.md)ismertető témakört. Azt is megteheti, hogy az egyszerű felügyelet érdekében [felügyelt identitást](use-managed-identity.md) használ egy egyszerű szolgáltatás helyett.

Néhány perc elteltével az üzembe helyezés befejeződik, és visszaadja a JSON-formátumú adatokat az AK-beli telepítésről.

> [!NOTE]
> Annak biztosítása érdekében, hogy a fürt megbízhatóan működjön, legalább 2 (két) csomópontot kell futtatnia.

## <a name="install-the-kubernetes-cli"></a>A Kubernetes parancssori felület telepítése

Ahhoz, hogy csatlakozni tudjon a Kubernetes-fürthöz a helyi számítógépről, használja a Kubernetes [kubectl][kubectl] nevű parancssori ügyfelét.

Ha az Azure Cloud Shellt használja, a `kubectl` már telepítve van. Helyben is telepítheti az [az aks install-cli][] paranccsal:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Csatlakozás fürthöz a kubectl használatával

Az [az aks get-credentials][] paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. A következő példa a *myAKSCluster* nevű AK-fürt hitelesítő adatait kéri le a *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való kapcsolódás ellenőrzéséhez futtassa a [kubectl Get Nodes][kubectl-get] parancsot a fürtcsomópontok listájának visszaküldéséhez:

```
$ kubectl get nodes

NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-37463671-vmss000000   Ready    agent   2m37s   v1.18.10
aks-nodepool1-37463671-vmss000001   Ready    agent   2m28s   v1.18.10
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Kubernetes-fürtöt helyezett üzembe az AKS-ben, és úgy konfigurálta a `kubectl` elemet, hogy a fürthöz csatlakozzon. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure Container registryben hitelesítésre képes Kubernetes AK-fürt üzembe helyezése
> * A Kubernetes parancssori felület (kubectl) telepítése
> * A kubectl konfigurálása az AKS-fürthöz való csatlakozásra

A következő oktatóanyag azt mutatja be, hogyan helyezhet üzembe egy alkalmazást a fürtben.

> [!div class="nextstepaction"]
> [Alkalmazások üzembe helyezése a Kubernetesben][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
[quotas-skus-regions]: quotas-skus-regions.md
