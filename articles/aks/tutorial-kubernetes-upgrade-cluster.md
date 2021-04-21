---
title: Az Azure-on futó Kubernetes oktatóanyaga – Fürtök frissítése
description: Ebben az Azure Kubernetes Service-hez (AKS-hez) tartozó oktatóanyagban megismerheti, hogyan frissítheti a meglévő AKS-fürtöket a legújabb elérhető Kubernetes-verzióra.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: 68aedbe90d5f08a4b6b67d134c0460caa11c542b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786368"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Oktatóanyag: A Kubernetes frissítése az Azure Kubernetes Service (AKS) szolgáltatásban

Az alkalmazás és a fürt életciklusának részeként frissíthet a Kubernetes legújabb elérhető verziójára, és használhatja az új szolgáltatásokat. Az Azure Kubernetes Service- (AKS-) fürt frissíthető az Azure CLI segítségével.

Ebben az oktatóanyagban, amely egy hétrészes sorozat hetedik része, egy Kubernetes-fürtöt frissítünk. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az aktuális és az elérhető Kubernetes-verzió azonosítása
> * A Kubernetes-csomópontok frissítése
> * A frissítés sikerességének ellenőrzése

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltak egy tároló-rendszerképbe. Ezt a képet feltöltötte a Azure Container Registry, és létrehozott egy AKS-fürtöt. Az alkalmazást ezután üzembe helyezni az AKS-fürtön. Ha ezeket a lépéseket még nem tette meg, és szeretné követni a lépéseket, kezdje az 1. oktatóanyag [– Tároló rendszerképek létrehozása lépéssel.][aks-tutorial-prepare-app]

Az oktatóanyaghoz az Azure CLI 2.0.53-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Az elérhető fürtverziók lekérése

A fürtök frissítése előtt az [az aks get-upgrades][] paranccsal ellenőrizze, hogy mely Kubernetes-kiadások frissíthetők:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

A következő példában az aktuális verzió *1.18.10,* és az elérhető verziók a *verziófrissítések alatt jelennek meg.*

```json
{
  "agentPoolProfiles": null,
  "controlPlaneProfile": {
    "kubernetesVersion": "1.18.10",
    ...
    "upgrades": [
      {
        "isPreview": null,
        "kubernetesVersion": "1.19.1"
      },
      {
        "isPreview": null,
        "kubernetesVersion": "1.19.3"
      }
    ]
  },
  ...
}
```

## <a name="upgrade-a-cluster"></a>Fürt frissítése

A futó alkalmazások megszakításának minimalizálása érdekében az AKS-csomópontok gondosan el vannak cordálva és ki vannak ürítve. Ebben a folyamatban a következő lépéseket hajtjuk végre:

1. A Kubernetes Scheduler megakadályozza, hogy további podok ütemezve legyen a frissíteni tervezett csomóponton.
1. A csomóponton futó podok a fürt más csomópontjaira vannak ütemezve.
1. Létrejön egy csomópont, amely a legújabb Kubernetes-összetevőket futtatja.
1. Amikor az új csomópont készen áll, és csatlakozik a fürthöz, a Kubernetes-ütemező elkezd podokat futtatni rajta.
1. A régi csomópont törlődik, és a fürt következő csomópontja megkezdi a cordon és a kiürítési folyamatot.

Az AKS-fürtök az [az aks upgrade][] paranccsal frissíthetők.

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

> [!NOTE]
> Egyszerre csak egy alverzió frissíthető. Frissíthet például az *1.14.x* verzióról *az 1.15.x* verzióra, de nem frissíthet közvetlenül *az 1.14.x-ről* *az 1.16.x verzióra.* Az *1.14.x* verzióról *az 1.16.x* verzióra való frissítéshez először frissítsen *az 1.14.x-ről* *az 1.15.x-re,* majd végezzen el egy újabb frissítést *az 1.15.x-ről* az *1.16.x* verzióra.

Az alábbi rövid példa kimenete az *1.19.1-es* verzióra való frissítés eredményét mutatja. Figyelje meg, hogy a *kubernetesVersion* mostantól *az 1.19.1-et jelenti:*

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.19.1",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Frissítés ellenőrzése

Az alábbiak szerint ellenőrizze az [az aks show][] paranccsal, hogy sikerült-e a frissítés:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Az alábbi példa kimenetében az látható, hogy az AKS-fürt a *KubernetesVersion 1.19.1-es* verzióját futtatja:

```output
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.19.1               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>A fürt törlése

Mivel ez az oktatóanyag a sorozat utolsó része, törölheti az AKS-fürtöt. Mivel a Kubernetes-csomópontok Azure-beli virtuális gépeken (VM) futnak, a csomópontok futtatása akkor is költségekkel jár, ha nem használja a fürtöt. Az [az group delete][az-group-delete] paranccsal eltávolíthatja az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket. Ha felügyelt identitást használt, az identitást a platform kezeli, és nem követeli meg a titkos kulcsok üzembeését vagy váltogatát.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban frissítettük a Kubernetest egy AKS-fürtben. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Az aktuális és az elérhető Kubernetes-verzió azonosítása
> * A Kubernetes-csomópontok frissítése
> * A frissítés sikerességének ellenőrzése

További információ az AKS-ről: [Az AKS áttekintése.][aks-intro] A teljes megoldások AKS-sel való létrehozásával kapcsolatos útmutatásért tekintse meg az [AKS megoldási útmutatóját.][aks-solution-guidance]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az_aks_show
[az aks get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az aks upgrade]: /cli/azure/aks#az_aks_upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az_group_delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[aks-solution-guidance]: /azure/architecture/reference-architectures/containers/aks-start-here?WT.mc_id=AKSDOCSPAGE
