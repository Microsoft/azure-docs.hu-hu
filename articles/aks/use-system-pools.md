---
title: Rendszer-csomópontkészletek használata a Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan hozhat létre és kezelhet rendszer-csomópontkészleteket a Azure Kubernetes Service (AKS) segítségével
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: 8b41b43c70f72ab327de2f1d59415cc1f49e5a5b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767402"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Rendszer-csomópontkészletek kezelése a Azure Kubernetes Service (AKS)

Az Azure Kubernetes Service (AKS) szolgáltatásban az azonos konfigurációban lévő csomópontok csomópontkészletekbe *vannak csoportosítva.* A csomópontkészletek tartalmazzák az alkalmazásokat futtató mögöttes virtuális gépeket. A rendszer-csomópontkészletek és a felhasználói csomópontkészletek két különböző csomópontkészlet-mód az AKS-fürtök számára. A rendszercsomópont-készletek szolgálják az elsődleges célt a kritikus rendszerpodok ( például és `CoreDNS` ) üzemeltetése `metrics-server` érdekében. A felhasználói csomópontkészletek szolgálják az alkalmazáspodok üzemeltetése elsődleges célját. Az alkalmazáspodok azonban ütemezheti a rendszer-csomópontkészleteket, ha csak egy készletet szeretne az AKS-fürtben. Minden AKS-fürtnek tartalmaznia kell legalább egy olyan rendszer-csomópontkészletet, amely legalább egy csomóponttal rendelkezik.

> [!Important]
> Ha éles környezetben egyetlen rendszer-csomópontkészletet futtat az AKS-fürthöz, javasoljuk, hogy legalább három csomópontot használjon a csomópontkészlethez.

## <a name="before-you-begin"></a>Előkészületek

* Az Azure CLI 2.3.1-es vagy újabb verzióját kell telepítenie és konfigurálnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="limitations"></a>Korlátozások

A rendszer-csomópontkészleteket támogató AKS-fürtök létrehozásakor és kezelésekor az alábbi korlátozások érvényesek.

* Lásd: [Kvóták, virtuálisgép-méretkorlátozások és][quotas-skus-regions]régiónkénti rendelkezésre állás Azure Kubernetes Service (AKS) .
* Az AKS-fürtöt virtuálisgép-méretezési csoportokkal kell kiépítve virtuálisgép-típusként és *standard* termékváltozatú terheléseltöltőként.
* A csomópontkészletek neve csak kisbetűs alfanumerikus karaktereket tartalmazhat, és kisbetűvel kell kezdődnie. Linux-csomópontkészletek esetén a hossznak 1–12 karakter hosszúságúnak kell lennie. Windows-csomópontkészletek esetén a hossznak 1–6 karakter hosszúságúnak kell lennie.
* A csomópontkészlet mód beállításához a 2020-03-01-es vagy újabb API-verziót kell használni. A 2020-03-01-es verziónál régebbi API-verziókban létrehozott fürtök csak felhasználói csomópontkészleteket tartalmaznak, de a frissítési készlet mód lépéseit követve áttelepítheti rendszer-csomópontkészletekbe. [](#update-existing-cluster-system-and-user-node-pools)
* A csomópontkészlet üzemmódja kötelező tulajdonság, és explicit módon kell beállítani ARM-sablonok vagy közvetlen API-hívások használata esetén.

## <a name="system-and-user-node-pools"></a>Rendszer- és felhasználói csomópontkészletek

Rendszer-csomópontkészletek esetén az AKS automatikusan hozzárendeli a **kubernetes.azure.com/mode: rendszer címkét** a csomópontjaihoz. Ennek hatására az AKS előnyben részesíti a rendszerpodok ütemezését az ezt a címkét tartalmazó csomópontkészletek esetén. Ez a címke nem akadályozza meg az alkalmazáspodok rendszer-csomópontkészletekbe való ütemezését. Azt javasoljuk azonban, hogy különítse el a kritikus rendszerpodokat az alkalmazás podjaitól, hogy megakadályozza a helytelenül konfigurált vagy engedélyezetlen alkalmazáspodok véletlen leállítja a rendszerpodokat. Ezt a viselkedést egy dedikált rendszer-csomópontkészlet létrehozásával kényszerítheti ki. A fertőzés használatával megakadályozhatja, hogy az alkalmazáspodok ütemezve vannak `CriticalAddonsOnly=true:NoSchedule` a rendszercsomópont-készleteken.

A rendszercsomópont-készletekre a következő korlátozások vonatkoznak:

* A rendszerkészletek osType típusának Linux rendszernek kell lennie.
* A felhasználói csomópontkészletek osType típusa Linux vagy Windows lehet.
* A rendszerkészletek legalább egy csomópontot tartalmaznak, a felhasználói csomópontkészletek pedig nulla vagy több csomópontot is tartalmazhatnak.
* A rendszer-csomópontkészletek legalább 2 vCPU és 4 GB memória virtuálisgép-termékváltozatot igényelnek. A burstable-VM (B sorozat) azonban nem ajánlott.
* Legalább két csomópont ajánlott 4 virtuális processzor (például Standard_DS4_v2), különösen nagy fürtök (Több CoreDNS podreplikák, 3–4+ bővítmény stb.).
* A rendszercsomópont-készleteknek legalább 30 podot kell támogatniuk a podok minimális és maximális értékképlete [alapján.][maximum-pods]
* A spot csomópontkészletek felhasználói csomópontkészleteket igényelnek.
* Ha további rendszercsomópont-készletet ad hozzá, vagy módosítja, hogy melyik csomópontkészlet rendszercsomópont-készlet, *nem* fogja automatikusan áthelyezni a rendszerpodokat. A rendszerpodok akkor is tovább futhatnak ugyanazon a csomópontkészleten, ha felhasználói csomópontkészletre módosítja. Ha olyan rendszerpodokat futtató csomópontkészletet töröl vagy skáláz le, amelyek korábban rendszercsomópont-készletként voltak futtatva, a rendszer ezeket a rendszerpodokat az új rendszercsomópont-készlet előnyben részesített ütemezésével újra üzembe lesz ásva.

A csomópontkészletekkel a következő műveleteket használhatja:

* Hozzon létre egy dedikált rendszer-csomópontkészletet (előnyben részesíti a rendszerpodok ütemezését a csomópontkészletei `mode:system` számára)
* Módosítsa a rendszercsomópont-készletet felhasználói csomópontkészletre, feltéve, hogy rendelkezik egy másik rendszer-csomópontkészletet, amely az AKS-fürtön veszi át a helyét.
* Módosítsa a felhasználói csomópontkészletet rendszer-csomópontkészletre.
* Felhasználói csomópontkészletek törlése.
* A rendszer-csomópontkészletek törölhetők, feltéve, hogy van egy másik rendszer-csomópontkészlete, amely az AKS-fürtön lesz a helyén.
* Egy AKS-fürt több rendszer-csomópontkészletet is tartalmazhat, és legalább egy rendszer-csomópontkészletet igényel.
* Ha módosítani szeretné a meglévő csomópontkészletek különböző nem módosítható beállításait, létrehozhat új csomópontkészleteket a lecserélésükhez. Például hozzáadhat egy új csomópontkészletet egy új maxPods beállítással, és törölheti a régi csomópontkészletet.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Új AKS-fürt létrehozása rendszer-csomópontkészletekkel

Amikor új AKS-fürtöt hoz létre, automatikusan létrehoz egy rendszer-csomópontkészletet egyetlen csomóponttal. A kezdeti csomópontkészlet alapértelmezés szerint rendszer típusú. Amikor új csomópontkészleteket hoz létre a használatával, ezek a csomópontkészletek felhasználói csomópontkészletek, kivéve, ha explicit módon `az aks nodepool add` megadja a mód paramétert.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus régióban.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Használja az [az aks create][az-aks-create] parancsot egy AKS-fürt létrehozásához. Az alábbi példa egy *myAKSCluster* nevű fürtöt hoz létre, amely egy csomópontot tartalmazó dedikált rendszerkészletet tartalmaz. Az éles számítási feladatokhoz győződjön meg arról, hogy legalább három csomóponttal rendelkezik rendszer-csomópontkészletekkel. A művelet több percig is eltarthat.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>Dedikált rendszer-csomópontkészlet hozzáadása meglévő AKS-fürthöz

> [!Important]
> A csomópontkészlet létrehozása után nem módosíthatja a csomópontok fertőzöttit a CLI-ben.

Hozzáadhat egy vagy több rendszercsomópont-készletet a meglévő AKS-fürtökhöz. Ajánlott az alkalmazáspodokat felhasználói csomópontkészleten ütemezni, és a rendszercsomópont-készleteket csak kritikus rendszerpodok számára dedikálni. Ez megakadályozza, hogy a nem engedélyezetlen alkalmazáspodok véletlenül lesiklák a rendszerpodokat. Ezt a viselkedést a `CriticalAddonsOnly=true:NoSchedule` [rendszer-csomópontkészletek][aks-taints] fertőzött útjára kényszerítheti. 

A következő parancs hozzáad egy dedikált, mód típusú rendszer típusú csomópontkészletet, amely alapértelmezés szerint három csomópontot tartalmaz.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>A csomópontkészlet részleteinek megjelenítése

A csomópontkészlet részleteit az alábbi paranccsal ellenőrizheti.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

A rendszercsomópont-készletekhez **rendszer** típusú mód,  felhasználói csomópontkészletek esetén pedig Felhasználó típusú mód van definiálva. Rendszerkészlet esetén ellenőrizze, hogy a fertőzés beállítása , ami megakadályozza, hogy az alkalmazáspodok ütemezve vannak ezen a `CriticalAddonsOnly=true:NoSchedule` csomópontkészleten.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Meglévő fürtrendszer- és felhasználói csomópontkészletek frissítése

> [!NOTE]
> A rendszercsomópontkészlet-mód beállításához a 2020-03-01-es vagy újabb API-verziót kell használni. A 2020-03-01-esnél régebbi API-verziókban létrehozott fürtök ezért csak felhasználói csomópontkészleteket tartalmaznak. A rendszer-csomópontkészlet funkcióinak és előnyeinek régebbi fürtökön való használatához frissítse a meglévő csomópontkészletek üzemmódját az alábbi parancsokkal az Azure CLI legújabb verzióján.

A rendszer- és a felhasználói csomópontkészletek módjai is megváltoztathatóak. A rendszer-csomópontkészletet csak akkor módosíthatja felhasználói készletre, ha már létezik másik rendszer-csomópontkészlet az AKS-fürtön.

Ez a parancs felhasználói csomópontkészletre módosítja a rendszer-csomópontkészletet.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Ez a parancs egy felhasználói csomópontkészletet rendszer-csomópontkészletre módosít.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Rendszer-csomópontkészlet törlése

> [!Note]
> Ha az AKS-fürtökön a 2020-03-02-es API-verzió előtt szeretné használni a rendszer-csomópontkészleteket, adjon hozzá egy új rendszer-csomópontkészletet, majd törölje az eredeti alapértelmezett csomópontkészletet.

Az AKS-fürtön legalább két rendszer-csomópontkészletnek kell lennie, mielőtt törölhet egyet.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A fürt törléséhez használja az [az group delete parancsot][az-group-delete] az AKS-erőforráscsoport törléséhez:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre és kezelhet rendszer-csomópontkészleteket egy AKS-fürtben. További információ a több csomópontkészlet használatával kapcsolatban: [Több csomópontkészlet használata.][use-multiple-node-pools]

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#setting-nodepool-taints
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
