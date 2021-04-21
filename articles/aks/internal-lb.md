---
title: Hozzon létre egy belső terheléselosztót
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre és használhat belső terheléselosztást a szolgáltatások Azure Kubernetes Service (AKS) használatával.
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: cbb898d05ecc1f0796f3609adb1368c3d77de2c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779740"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Belső terheléselosztás használata Azure Kubernetes Service (AKS)

Az alkalmazásokhoz való hozzáférés korlátozása a Azure Kubernetes Service (AKS) használatával létrehozhat és használhat belső terheléselosztást. A belső terheléselosztással a Kubernetes-szolgáltatások csak a Kubernetes-fürttel azonos virtuális hálózaton futó alkalmazások számára érhetők el. Ez a cikk bemutatja, hogyan hozhat létre és használhat belső terheléseltöltőt Azure Kubernetes Service (AKS) használatával.

> [!NOTE]
> Azure Load Balancer két SKUs -ban érhető el : *Alapszintű* és *Standard*. Alapértelmezés szerint a Standard termékváltozatot használja a rendszer az AKS-fürtök létrehozásakor.  Amikor LoadBalancer típusú szolgáltatást hoz létre, a fürt kiépítésekor is ugyanazt az LB-típust fogja kapni. További információ: [Azure Load Balancer termékváltozatok összehasonlítása.][azure-lb-comparison]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van egy meglévő AKS-fürt. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

Az AKS-fürtidentitásnak engedéllyel kell rendelkeznie a hálózati erőforrások kezeléséhez, ha meglévő alhálózatot vagy erőforráscsoportot használ. További információ: Kubenet-hálózat használata a saját IP-címtartományokkal az [Azure Kubernetes Service -ban (AKS)][use-kubenet] vagy [Configure Azure CNI networking in Azure Kubernetes Service (AKS) (AKS-][advanced-networking]hálózat Azure Kubernetes Service konfigurálása). Ha a terheléselosztást egy másik [][different-subnet]alhálózat IP-címének használatára konfigurálja, győződjön meg arról, hogy az AKS-fürtidentitás is rendelkezik olvasási hozzáféréssel az alhálózathoz.

Az engedélyekkel kapcsolatos további információkért lásd: [AKS-hozzáférés delegálása más Azure-erőforrásokhoz.][aks-sp]

## <a name="create-an-internal-load-balancer"></a>Hozzon létre egy belső terheléselosztót

Belső terheléseltöltő létrehozásához hozzon létre egy nevű szolgáltatásjegyzéket `internal-lb.yaml` *LoadBalancer* szolgáltatástípussal és *az azure-load-balancer-internal* jegyzetgel az alábbi példában látható módon:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Telepítse a belső terheléselosztást a [kubectl apply][kubectl-apply] használatával, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f internal-lb.yaml
```

Létrejön egy Azure Load Balancer a csomópont-erőforráscsoportban, és ugyanabba a virtuális hálózathoz csatlakozik, mint az AKS-fürt.

A szolgáltatás részleteinek megtekintésekor a belső terheléselosztás *IP-címe az EXTERNAL-IP* oszlopban jelenik meg. Ebben a környezetben a *Külső* a terheléselosztás külső felületével kapcsolatos, nem pedig nyilvános, külső IP-címet kap. Az IP-cím módosítása egy-két percet is igénybe vehet, amint az az alábbi *\<pending\>* példában látható:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>IP-cím megadása

Ha egy adott IP-címet szeretne használni a belső terhelésel balancerhez, adja hozzá a *loadBalancerIP* tulajdonságot a terheléselelosztás YAML-jegyzékfájlhoz. Ebben a forgatókönyvben a megadott IP-címnek ugyanabban az alhálózatban kell lennie, mint az AKS-fürtnek, és még nem rendelhető hozzá erőforráshoz. Például ne használjon IP-címet a Kubernetes-alhálózathoz kijelölt tartományban.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Az üzembe helyezéskor és a szolgáltatás részleteinek megtekintésekor az *EXTERNAL-IP oszlopban található IP-cím* a megadott IP-címet tükrözi:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

A terheléselosztás másik alhálózatban való konfigurálásával kapcsolatos további információkért lásd: [Másik alhálózat megadása.][different-subnet]

## <a name="use-private-networks"></a>Magánhálózatok használata

Az AKS-fürt létrehozásakor speciális hálózati beállításokat is megadhat. Ez a módszer lehetővé teszi a fürt üzembe helyezését egy meglévő Azure-beli virtuális hálózaton és alhálózaton. Az egyik forgatókönyv az, hogy az AKS-fürtöt egy, a helyszíni környezethez csatlakoztatott magánhálózaton telepíti, és csak belsőleg elérhető szolgáltatásokat futtat. További információ: Saját virtuális hálózati alhálózatok konfigurálása [a Kubenet][use-kubenet] vagy a [Azure CNI.][advanced-networking]

A belső terheléselosztás magánhálózatot használó AKS-fürtön való üzembe helyezéséhez nincs szükség az előző lépések módosításaira. A terheléselosztás ugyanabban az erőforráscsoportban jön létre, mint az AKS-fürt, de a magánhálózathoz és az alhálózathoz csatlakozik, ahogyan az alábbi példában látható:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Előfordulhat, hogy az AKS-fürt fürtidentitását a Hálózati közreműködő szerepkört kell megadja ahhoz az erőforráscsoporthoz, amelyben az Azure-beli virtuális hálózati erőforrások üzembe vannak helyezni.  Tekintse meg a fürtidentitást [az az aks show,][az-aks-show]például a következővel: `az aks show --resource-group myResourceGroup --name myAKSCluster --query "identity"` . Szerepkör-hozzárendelés létrehozásához használja az [az role assignment create][az-role-assignment-create] parancsot.

## <a name="specify-a-different-subnet"></a>Másik alhálózat megadása

Ha meg szeretne adni egy alhálózatot a terheléselosztáshoz, adja hozzá az *azure-load-balancer-internal-subnet* jegyzetet a szolgáltatáshoz. A megadott alhálózatnak és az AKS-fürtnek ugyanabban a virtuális hálózatban kell lennie. Üzembe helyezéskor a *terheléselosztási EXTERNAL-IP-cím* a megadott alhálózat része.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>A terheléselosztás törlése

Ha a belső terheléselosztást felhasználó összes szolgáltatást törlik, maga a terheléselosztás is törlődik.

A szolgáltatásokat közvetlenül is törölheti, mint bármely Más Kubernetes-erőforrás, például a esetén, ami ezután törli a `kubectl delete service internal-app` mögöttes Azure Load Balancert is.

## <a name="next-steps"></a>Következő lépések

A Kubernetes-szolgáltatásokról a [Kubernetes-szolgáltatások][kubernetes-services]dokumentációjában talál további információt.

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[different-subnet]: #specify-a-different-subnet