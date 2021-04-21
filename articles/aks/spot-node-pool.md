---
title: Helyszíni csomópontkészlet hozzáadása egy Azure Kubernetes Service (AKS) szolgáltatásbeli fürthöz
description: Megtudhatja, hogyan adhat hozzá egy helyszíni csomópontkészletet egy Azure Kubernetes Service (AKS-) fürthöz.
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: f46a421ae2ad1a4d9c590c7e0b47784760ebcb9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782800"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Helyszíni csomópontkészlet hozzáadása egy Azure Kubernetes Service (AKS) szolgáltatásbeli fürthöz

A spot csomópontkészlet egy, a virtuálisgép-méretezési csoportot kiszolgáló [csomópontkészlet.][vmss-spot] Ha kihasználatlan virtuális gépeket használ az AKS-fürt csomópontjaihoz, azzal lehetővé teszi, hogy jelentős költségmegtakarítás mellett kihasználja az Azure kihasználatlan kapacitását. A kihasználatlan rendelkezésre álló kapacitások mennyisége számos tényezőtől függ, többek között a csomópont méretétől, a régiótól és a naptól.

Kihasználható csomópontkészlet üzembe helyezésekor az Azure lefoglalja a kihasználható csomópontokat, ha van szabad kapacitás. A spot csomópontokhoz azonban nincs SLA. A spot csomópontkészletet kiszolgáló spot méretezési csoport egyetlen tartalék tartományban van üzembe állítva, és nem nyújt magas rendelkezésre állási garanciát. Amikor az Azure-nak vissza kell kapnia a kapacitást, az Azure-infrastruktúra ki fogja szúrni a kihasznált csomópontokat.

A spot csomópontok remekül használhatók olyan számítási feladatokhoz, amelyek a megszakításokat, a korai megszakításokat és a kilakoltatásokat is kezelik. Például az olyan számítási feladatok, mint a kötegelt feldolgozási feladatok, a fejlesztési és tesztelési környezetek, valamint a nagy számítási feladatok jó jelöltek lehetnek egy helyszíni csomópontkészleten való ütemezésre.

Ebben a cikkben egy másodlagos csomópontkészletet fog hozzáadni egy meglévő Azure Kubernetes Service (AKS-) fürthöz.

Ez a cikk feltételezi a Kubernetes és a Azure Load Balancer alapfogalmait. További információkért lásd a Kubernetes alapfogalmait a Azure Kubernetes Service [(AKS) című oldalon.][kubernetes-concepts]

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Előkészületek

Amikor létrehoz egy fürtöt egy spot csomópontkészlet használatára, a fürtnek a csomópontkészletek és a *standard* termékváltozatú terheléselosztási Virtual Machine Scale Sets is használnia kell. A fürt létrehozása után egy további csomópontkészletet is hozzá kell adni a spot csomópontkészletek használatára. A további csomópontkészletek hozzáadásáról egy későbbi lépésben lesz lehetőség.

Ehhez a cikkhez az Azure CLI 2.14-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

### <a name="limitations"></a>Korlátozások

Az AKS-fürtök helyszíni csomópontkészletekkel való létrehozásakor és kezelésekor a következő korlátozások érvényesek:

* A spot csomópontkészlet nem lehet a fürt alapértelmezett csomópontkészlete. A spot csomópontkészletek csak másodlagos készlethez használhatók.
* A spot csomópontkészletek nem frissíthetőek, mivel a spot csomópontkészletek nem garantálják a cordont és az ürítését. Az olyan műveletek végrehajtásához, mint például a Kubernetes-verzió frissítése, a meglévő helyszíni csomópontkészletet egy újra kell cserélnie. A spot csomópontkészletek cseréjéhez hozzon létre egy új, a Kubernetes egy másik verziójára vonatkozó csomópontkészletet, várjon, amíg az állapota *Kész,* majd távolítsa el a régi csomópontkészletet.
* A vezérlősík és a csomópontkészletek nem frissíthetőek egyszerre. Ezeket külön kell frissítenie, vagy el kell távolítania a spot csomópontkészletet, hogy egyszerre frissítse a vezérlősíkot és a fennmaradó csomópontkészleteket.
* A spot csomópontkészletnek a Virtual Machine Scale Sets.
* Létrehozás után a ScaleSetPriority és a SpotMaxPrice nem változtatható meg.
* A SpotMaxPrice értékének -1 vagy legfeljebb öt tizedesjegy pontossággal megadott pozitív értéknek kell lennie.
* A spot csomópontkészletek címkéje *a kubernetes.azure.com/scalesetpriority:spot*, a fertőzöttség kubernetes.azure.com/scalesetpriority=spot:NoSchedule *,* a rendszerpodok pedig antiaffinitással fognak rendelkezik.
* A számítási [feladatoknak][spot-toleration] a spot csomópontkészleten való ütemezéséhez hozzá kell adni egy megfelelő tűrést.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Kihasználatlan csomópontkészlet hozzáadása egy AKS-fürthöz

A több csomópontkészletet engedélyező meglévő fürthöz hozzá kell adni egy spot csomópontkészletet. A több csomópontkészlettel rendelkező AKS-fürtök létrehozásával kapcsolatos további részletek itt [érhetők el.][use-multiple-node-pools]

Hozzon létre egy csomópontkészletet [az az aks nodepool add használatával.][az-aks-nodepool-add]
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Alapértelmezés szerint egy Normál prioritású  csomópontkészletet hoz létre az AKS-fürtben, ha több csomópontkészletet is tartalmaz.  A fenti parancs egy kiegészítő csomópontkészletet ad hozzá  egy meglévő, Spot prioritású AKS-fürthöz.  A *Spot prioritás* miatt a csomópontkészlet egy spot csomópontkészlet lesz.  A *fenti példában az eviction-policy* paraméter *Delete* értékre van állítva, amely az alapértelmezett érték. Ha a [][eviction-policy] törlési szabályzatot Törlésre adja *meg,* a csomópontkészlet mögöttes méretezési csoportjának csomópontjai törlődnek a kiesésükkor. A kiépítési szabályzatot a *Felszabadítás beállításra is beállíthatja.* Ha a felszabadításra vonatkozó kiépítési szabályzatot ad *meg,* a mögöttes méretezési csoportban lévő csomópontok leállított-felszabadított állapotba vannak állítva a kiépítéskor. A leállított-felszabadított állapotban lévő csomópontok beleszámolnak a számítási kvótába, és problémákat okozhatnak a fürtök skálázása vagy frissítése során. A *prioritás és* az *eviction-policy* értékek csak a csomópontkészlet létrehozása során beállíthatók. Ezek az értékek később nem frissíthetők.

A parancs a fürt [automatikus][cluster-autoscaler]skálázási eszközét is engedélyezi, amelyet a spot csomópontkészletekkel ajánlott használni. A fürtön futó számítási feladatok alapján az automatikus fürtméretozó felskálizálja és leskálizálja a csomópontkészletben lévő csomópontok számát. A spot csomópontkészletek esetén az automatikus fürtméretozó felskálizálja a csomópontok számát a kiesés után, ha további csomópontokra van szükség. Ha módosítja a csomópontkészletek maximális csomópontszámát, akkor az automatikus fürtméretozóhoz társított értéket `maxCount` is módosítania kell. Ha nem használ automatikus fürtméretozót, a kieséskor a spot készlet végül nullára csökken, és manuális műveletre van szükség a további spot csomópontok fogadása érdekében.

> [!Important]
> Számítási feladatokat csak olyan helyszíni csomópontkészletekhez ütemezhet, amelyek kezelni tudnak megszakításokat, például kötegelt feldolgozási feladatokat és tesztelési környezeteket. Javasoljuk, hogy a [][taints-tolerations] fertőzöttségeket és a leranításokat a helyszíni csomópontkészleten állítsa be, hogy csak a csomópont-kilakoltatások kezelésére képes számítási feladatok legyen ütemezve a spot csomópontkészleten. A fenti ny parancs például alapértelmezés szerint  egy fertőzöttség-kubernetes.azure.com/scalesetpriority=spot:NoSchedule így csak a megfelelő tűrő podok vannak ütemezve ezen a csomóponton.

## <a name="verify-the-spot-node-pool"></a>A spot csomópontkészlet ellenőrzése

Annak ellenőrzéséhez, hogy a csomópontkészlet hozzá lett-e adva spot csomópontkészletként:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Győződjön meg *arról, hogy a scaleSetPriority* *Spot*.

Ha egy podot úgy szeretne ütemezni, hogy egy spot csomóponton fusson, adjon hozzá egy olyan lerálást, amely megfelel a spot csomópontra alkalmazott fertőzöttségnek. Az alábbi példa egy yaml-fájl egy részét mutatja be, amely az előző lépésben használt fertőzöttségnek *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* tűrést definiál.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Ha ilyen tűrő podot helyez üzembe, a Kubernetes sikeresen ütemezheti a podot a csomópontokon a fertőzöttség alkalmazásával.

## <a name="max-price-for-a-spot-pool"></a>A spot-készlet maximális ára
[A spot példányok díjszabása változó a][pricing-spot]régió és a termékváltozat alapján. További információ: A [Linux és a][pricing-linux] Windows [díjszabása.][pricing-windows]

Változó díjszabással beállíthatja a maximális árat amerikai dollárban (USD), legfeljebb 5 tizedesjegyet használva. A *0,98765* érték például 0,98765 USD/óra maximális ára lenne. Ha a maximális árat *-1-re* adja meg, a példány nem lesz az ár alapján lesz kiszűkülve. A példány ára a Spot aktuális ára vagy egy standard példány ára lesz (amelyik kisebb, ha rendelkezésre áll kapacitás és kvóta).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan adhat hozzá egy spot csomópontkészletet egy AKS-fürthöz. A podok csomópontkészletek közötti szabályozásával kapcsolatos további információkért tekintse meg az AKS speciális ütemezői funkcióinak [ajánlott eljárásait.][operator-best-practices-advanced-scheduler]

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md