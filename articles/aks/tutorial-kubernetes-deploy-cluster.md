---
title: Azure-on futó Kubernetes oktatóanyag – Fürtök üzembe helyezése
description: Az Azure Kubernetes Service (AKS) ezen oktatóanyagában egy AKS-fürtöt fog létrehozni, és kapcsolódni fog a Kubernetes-főcsomóponthoz a kubectl használatával.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d7e931a55ec0a9d46a8b92d4353bd2de8edd8818
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777832"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Oktatóanyag: Azure Kubernetes Service- (AKS-) fürt üzembe helyezése

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Az AKS-sel gyorsan létrehozhat egy éles üzemre kész Kubernetes-fürtöt. Ebben az oktatóanyagban, amely egy hétrészes sorozat harmadik része, egy Kubernetes-fürtöt helyezünk üzembe az AKS-ben. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kubernetes AKS-fürt üzembe helyezése, amely hitelesíthető egy Azure-beli tároló-beállításjegyzékben
> * A Kubernetes parancssori felület (kubectl) telepítése
> * A kubectl konfigurálása az AKS-fürthöz való csatlakozásra

A későbbi oktatóanyagokban az Azure Vote alkalmazást üzembe helyezjük a fürtön, skálázjuk és frissítjük.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban létrehoztunk egy tárolórendszerképet, és feltöltöttük egy Azure Container Registry-példányra. Ha még nem tette meg ezeket a lépéseket, és szeretné követni a lépéseket, kezdje az 1. oktatóanyag [– Tároló-rendszerképek létrehozása lépéssel.][aks-tutorial-prepare-app]

Az oktatóanyaghoz az Azure CLI 2.0.53-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Az AKS-fürtök kubernetes szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC) használhatnak. Ezekkel a vezérlőkkel a felhasználókhoz rendelt szerepkörök alapján definiálható az erőforrásokhoz való hozzáférés. Az engedélyek akkor vannak egyesítve, ha egy felhasználóhoz több szerepkör is hozzá van rendelve, és az engedélyek hatóköre egyetlen névtérre vagy a teljes fürtre terjedhet ki. Alapértelmezés szerint az Azure CLI automatikusan engedélyezi a Kubernetes RBAC-t egy AKS-fürt létrehozásakor.

AKS-fürtöket az [az aks create][] paranccsal hozhat létre. A következő példában létrehozunk egy *myAKSCluster* nevű fürtöt a *myResourceGroup* nevű erőforráscsoportban. Ezt az erőforráscsoportot az előző [oktatóanyagban,][aks-tutorial-prepare-acr] az *eastus régióban hoztuk* létre. Az alábbi példa nem ad meg régiót, így az AKS-fürt is az *eastus* régióban jön létre. További információ: Kvóták, virtuálisgép-méretkorlátozások és régiónkénti rendelkezésre állás az [Azure Kubernetes Service-ban (AKS) az AKS][quotas-skus-regions] erőforráskorlátait és régiónkénti elérhetőségét illetően.

Ahhoz, hogy egy AKS-fürt kommunikálhat más Azure-erőforrásokkal, a rendszer automatikusan létrehoz egy fürtidentitást, mivel nem adott meg egyet. Itt ez a [][container-registry-integration] fürtidentitás jogosultságot kap a rendszerképek az előző oktatóanyagban létrehozott Azure Container Registry -példányból (ACR). A parancs sikeres végrehajtásához tulajdonosi vagy  Azure-fiók-rendszergazdai szerepkörrel kell rendelkezik az Azure-előfizetésben. 

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Annak érdekében, hogy ne legyen **szükség tulajdonosi** vagy Azure-fiók-rendszergazdai szerepkörre, manuálisan is konfigurálhat egy szolgáltatásnévt, hogy rendszerképeket lekért az ACR-ból.  További információ: [ACR-hitelesítés szolgáltatásnévvel](../container-registry/container-registry-auth-service-principal.md) vagy [Hitelesítés a Kubernetesből lekért titkos adatokat használva.](../container-registry/container-registry-auth-kubernetes.md) Az egyszerűbb kezelés érdekében [](use-managed-identity.md) szolgáltatásnév helyett felügyelt identitást is használhat.

Néhány perc múlva befejeződik az üzembe helyezés, és visszaadja az AKS üzemelő példányával kapcsolatos JSON-formátumú információkat.

> [!NOTE]
> A fürt megbízható működéséhez legalább 2 (két) csomópontot kell futtatnia.

## <a name="install-the-kubernetes-cli"></a>A Kubernetes parancssori felület telepítése

Ahhoz, hogy csatlakozni tudjon a Kubernetes-fürthöz a helyi számítógépről, használja a Kubernetes [kubectl][kubectl] nevű parancssori ügyfelét.

Ha az Azure Cloud Shellt használja, a `kubectl` már telepítve van. Helyben is telepítheti az [az aks install-cli][] paranccsal:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Csatlakozás fürthöz a kubectl használatával

Az [az aks get-credentials][] paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Az alábbi példa lekért hitelesítő adatokat kap a *myResourceGroup* *myAKSCluster* nevű AKS-fürthöz:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez futtassa a [kubectl get nodes][kubectl-get] parancsot a fürtcsomópontok listájának visszaadása érdekében:

```
$ kubectl get nodes

NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-37463671-vmss000000   Ready    agent   2m37s   v1.18.10
aks-nodepool1-37463671-vmss000001   Ready    agent   2m28s   v1.18.10
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Kubernetes-fürtöt helyezett üzembe az AKS-ben, és úgy konfigurálta a `kubectl` elemet, hogy a fürthöz csatlakozzon. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Olyan Kubernetes AKS-fürt üzembe helyezése, amely képes hitelesítést végezni egy Azure-beli tároló-beállításjegyzékben
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
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az acr show]: /cli/azure/acr#az_acr_show
[az role assignment create]: /cli/azure/role/assignment#az_role_assignment_create
[az aks create]: /cli/azure/aks#az_aks_create
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
[quotas-skus-regions]: quotas-skus-regions.md
