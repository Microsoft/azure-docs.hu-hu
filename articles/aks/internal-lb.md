---
title: Hozzon létre egy belső terheléselosztót
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre és használhat belső Load balancert a szolgáltatások Azure Kubernetes szolgáltatással (ak) való elérhetővé tétele érdekében.
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: ec8fd1f1b32d5bba6dc4dc756e1f95f4a74f9a96
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285883"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Belső terheléselosztó használata az Azure Kubernetes szolgáltatással (ak)

Ha korlátozni szeretné az alkalmazásaihoz való hozzáférést az Azure Kubernetes szolgáltatásban (ak), létrehozhat és használhat belső Load balancert. A belső terheléselosztó csak a Kubernetes-fürttel azonos virtuális hálózaton futó alkalmazások számára teszi elérhetővé a Kubernetes szolgáltatást. Ebből a cikkből megtudhatja, hogyan hozhat létre és használhat belső terheléselosztó szolgáltatást az Azure Kubernetes szolgáltatással (ak).

> [!NOTE]
> Azure Load Balancer két SKU-ban érhető el – *Alapszintű* és *standard*. Alapértelmezés szerint a standard SKU-t használja a rendszer, amikor egy AK-fürtöt hoz létre.  Ha terheléselosztó típusú szolgáltatást hoz létre, ugyanazt az LB-típust fogja kapni, mint a fürt kiépítésekor. További információ: [Azure Load BALANCER SKU-összehasonlítás][azure-lb-comparison].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra.  `az --version`A verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

Ha meglévő alhálózatot vagy erőforráscsoportot használ, a (z) alhálózati erőforrás-kezelőhöz a hálózati erőforrások kezeléséhez engedély szükséges. További információ: a [kubenet hálózatkezelés használata saját IP-címtartományok az Azure Kubernetes szolgáltatásban (ak)][use-kubenet] vagy [Az Azure CNI hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak)][advanced-networking]. Ha úgy konfigurálja a terheléselosztó-t, hogy egy [másik alhálózat IP-címét][different-subnet]használja, győződjön meg arról, hogy az AK-fürt egyszerű szolgáltatása olvasási hozzáféréssel is rendelkezik az adott alhálózathoz.

Egyszerű szolgáltatásnév helyett használhatja a rendszerhez rendelt felügyelt identitást is az engedélyekhez. További információ: [felügyelt identitások használata](use-managed-identity.md). Az engedélyekkel kapcsolatos további információkért lásd: [AK-hozzáférés delegálása más Azure-erőforrásokhoz][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Hozzon létre egy belső terheléselosztót

Belső terheléselosztó létrehozásához hozzon létre egy nevű szolgáltatási jegyzékfájlt `internal-lb.yaml` a szolgáltatás típusa *terheléselosztó* és az *Azure-Load-Balancer – belső* jegyzettel az alábbi példában látható módon:

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

Helyezze üzembe a belső terheléselosztó-t a [kubectl alkalmazásával][kubectl-apply] , és adja meg a YAML-jegyzék nevét:

```console
kubectl apply -f internal-lb.yaml
```

Az Azure Load Balancer a csomópont-erőforráscsoporthoz jön létre, és ugyanahhoz a virtuális hálózathoz csatlakozik, mint az AK-fürt.

A szolgáltatás részleteinek megtekintésekor a belső terheléselosztó IP-címe megjelenik a *külső IP-* oszlopban. Ebben a kontextusban a *külső* a terheléselosztó külső felületével kapcsolatos, nem pedig nyilvános, külső IP-címet kap. Előfordulhat, hogy az IP-cím egy percet vagy kettőt is igénybe vehet *\<pending\>* egy tényleges belső IP-címről, ahogy az az alábbi példában is látható:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>IP-cím meghatározása

Ha egy adott IP-címet szeretne használni a belső terheléselosztó számára, adja hozzá a *loadBalancerIP* tulajdonságot a terheléselosztó YAML jegyzékfájlhoz. Ebben az esetben a megadott IP-címnek ugyanabban az alhálózatban kell lennie, mint az AK-fürtnek, és még nem lehet erőforráshoz rendelni. Például ne használjon IP-címet a Kubernetes alhálózathoz kijelölt tartományban.

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

Üzembe helyezéskor és a szolgáltatás részleteinek megtekintéséhez a *külső IP* -cím OSZLOPBAN szereplő IP-cím a megadott IP-címet tükrözi:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

A terheléselosztó másik alhálózatban való konfigurálásával kapcsolatos további információkért lásd: [másik alhálózat megadása][different-subnet]

## <a name="use-private-networks"></a>Magánhálózati hálózatok használata

Az AK-fürt létrehozásakor megadhatja a speciális hálózati beállításokat. Ez a módszer lehetővé teszi, hogy a fürtöt egy meglévő Azure-beli virtuális hálózatba és alhálózatba telepítse. Az egyik forgatókönyv az AK-fürt üzembe helyezése a helyszíni környezethez csatlakoztatott magánhálózati hálózatban, és a szolgáltatások csak belsőleg érhetők el. További információ: saját virtuális hálózati alhálózatok konfigurálása a [Kubenet][use-kubenet] vagy az [Azure CNI][advanced-networking].

Az előző lépések módosítása nem szükséges ahhoz, hogy egy belső terheléselosztó üzembe helyezése egy magánhálózatot használó AK-fürtön történjen. A terheléselosztó ugyanabban az erőforráscsoportban jön létre, mint az AK-fürt, de csatlakozik a privát virtuális hálózathoz és az alhálózathoz, ahogy az az alábbi példában is látható:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Előfordulhat, hogy a *hálózati közreműködő* szerepkört egy olyan erőforráscsoporthoz kell megadnia, ahol az Azure-beli virtuális hálózati erőforrásokat üzembe helyezi. Tekintse meg a szolgáltatásnevet az [az AK show][az-aks-show]paranccsal, például: `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"` . Szerepkör-hozzárendelés létrehozásához használja az az [role-hozzárendelés létrehozása][az-role-assignment-create] parancsot.

## <a name="specify-a-different-subnet"></a>Válasszon másik alhálózatot

A terheléselosztó alhálózatának megadásához adja hozzá az *Azure-Load-Balancer – belső alhálózat* megjegyzését a szolgáltatáshoz. A megadott alhálózatnak ugyanabban a virtuális hálózatban kell lennie, mint az AK-fürtnek. Telepítésekor a terheléselosztó *külső IP-* címe a megadott alhálózat része.

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

## <a name="delete-the-load-balancer"></a>A terheléselosztó törlése

Ha a belső terheléselosztó által használt összes szolgáltatás törlődik, akkor a terheléselosztó is törlődik.

Közvetlenül is törölhet egy szolgáltatást, mint bármely Kubernetes-erőforrást, például a (z `kubectl delete service internal-app` )-t, amely a mögöttes Azure Load balancert is törli.

## <a name="next-steps"></a>További lépések

További információ a Kubernetes Services szolgáltatásról a [Kubernetes Services dokumentációjában][kubernetes-services].

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[different-subnet]: #specify-a-different-subnet