---
title: Ajánlott eljárások a Scheduler funkcióihoz
titleSuffix: Azure Kubernetes Service
description: Megismerheti a fürtüzemeltetőkre vonatkozó ajánlott eljárásokat a speciális ütemező funkciók , például a fertőzöttség és a leraktálás, a csomópontválasztók és affinitás, illetve a podok közötti affinitás és az affinitás-gátló funkciók alkalmazásával kapcsolatban az Azure Kubernetes Service-ben (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 971916c3fc903ff5d69db2e0f82fd884acf807b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831581"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a Scheduler speciális funkcióihoz az Azure Kubernetes Service (AKS) szolgáltatásban

A fürtök AKS-Azure Kubernetes Service kezelése során gyakran el kell különítenie a csapatokat és a számítási feladatokat. A Kubernetes-ütemező által biztosított speciális funkciókkal szabályozhatja a következő funkciókat:
* Mely podok ütemezve vannak bizonyos csomópontokon?
* A több podos alkalmazások megfelelő elosztása a fürtön. 

Ez az ajánlott eljárásokat bemutató cikk a fürtüzemeltetők speciális Kubernetes-ütemezési funkcióival foglalkozik. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A fertőzöttek és a toleransok használatával korlátozhatja a csomópontokon ütemezhető podok számát.
> * Előnyben részesítheti a podokat, hogy bizonyos csomópontokon csomópontválasztókkal vagy csomópont-affinitással fusson.
> * Podok felosztása vagy csoportosítása podok közötti affinitással vagy antiaffinitással.

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Dedikált csomópontok biztosítanak fertőzöttek és leraktások használatával

> **Ajánlott eljárás:** 
>
> Korlátozza az erőforrás-igényes alkalmazások, például a bejövő forgalom vezérlői hozzáférését adott csomópontokra. Tartsa elérhetővé a csomópont-erőforrásokat az azokat igénylő számítási feladatokhoz, és ne engedélyezze más számítási feladatok ütemezését a csomópontokon.

Az AKS-fürt létrehozásakor GPU-támogatással vagy nagy számú nagy teljesítményű processzorokkal is üzembe helyezhet csomópontokat. Ezeket a csomópontokat olyan nagy adatfeldolgozási számítási feladatokhoz használhatja, mint a gépi tanulás (ML) vagy a mesterséges intelligencia (AI). 

Mivel ez a csomópont-erőforráshardver üzembe helyezése általában költséges, korlátozza az ezeken a csomópontokon ütemezható számítási feladatokat. Ehelyett a fürt néhány csomópontját a bejövő szolgáltatások futtatására és más számítási feladatok megakadályozására kell dedikálni.

A különböző csomópontok támogatása több csomópontkészlet használatával történik. Egy AKS-fürt egy vagy több csomópontkészletet biztosít.

A Kubernetes-ütemező fertőzötteket és leránsokat használ a csomópontokon futtatható számítási feladatok korlátozására.

* Egy fertőzött **állapot alkalmazása** egy csomópontra annak jelzésére, hogy csak adott podok ütemezheti őket.
* Ezután alkalmazza a **leranítást** egy podra, amely lehetővé teszi *számukra* a csomópontok fertőzöttségének tűrését.

Amikor podot helyez üzembe egy AKS-fürtön, a Kubernetes csak olyan csomópontokon ütemez podokat, amelyek fertőzöttségéhez igazodik a leranítás. Tegyük fel például, hogy hozzáadott egy csomópontkészletet az AKS-fürthöz a GPU-támogatással bíró csomópontokhoz. Adja meg a nevet, például *gpu,* majd egy értéket az ütemezéshez. Ennek az értéknek a *NoSchedule* értékre való beállítása korlátozza a Kubernetes-ütemezőt a csomóponton meghatározatlan tűrő podok ütemezésében.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

A csomópontkészlet csomópontjaira alkalmazott fertőzöttség esetén a podspecifikációban definiálni kell egy lerálást, amely lehetővé teszi a csomópontok ütemezését. Az alábbi példa meghatározza a és a értékeket, hogy tűrje az előző lépésben a csomópontkészletre alkalmazott `sku: gpu` `effect: NoSchedule` fertőzöttet:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Ha a podot a használatával telepíti, a Kubernetes sikeresen ütemezheti a podot a csomópontokon a `kubectl apply -f gpu-toleration.yaml` fertőzöttság alkalmazásával. Ez a logikai elkülönítés lehetővé teszi a fürtön belüli erőforrásokhoz való hozzáférés szabályozását.

A fertőzöttek alkalmazásakor az alkalmazásfejlesztőkkel és a tulajdonosokkal együtt lehetővé teheti számukra, hogy meghatározzák a szükséges rendellenességeket az üzemelő példányaikban.

További információ több csomópontkészlet AKS-ben való használatával kapcsolatban: Több csomópontkészlet létrehozása és kezelése egy fürthöz az [AKS-ban.][use-multiple-node-pools]

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>A fertőzöttek és a leraktások viselkedése az AKS-ben

Amikor csomópontkészletet frissít az AKS-ben, a fertőzöttek és a toleranák egy készletmintát követnek, amikor azok új csomópontokra vonatkoznak:

#### <a name="default-clusters-that-use-vm-scale-sets"></a>Virtuálisgép-méretezési csoportokat használ alapértelmezett fürtök
Az AKS API-ból származó csomópontkészletek fertőzöttek, hogy az újonnan felskálált csomópontok megkapják [a][taint-node-pool] megadott API-csomóponti fertőzötteket.

Tegyük fel, hogy:
1. Egy kétcsomópontos fürtöt kell elkezdenie: *node1* és *node2.* 
1. Frissítse a csomópontkészletet.
1. Két további csomópont jön létre: *node3* és *node4.* 
1. A fertőzötteket a rendszer a megfelelő módon továbbküldi.
1. Az eredeti *node1 és* *node2* csomópont törlődik.

#### <a name="clusters-without-vm-scale-set-support"></a>Virtuálisgép-méretezési csoport támogatása nélküli fürtök

Most is tegyük fel, hogy:
1. Kétcsomópontos fürtöt tartalmaz: *node1* és *node2.* 
1. Ezután frissítheti a csomópontkészletet.
1. Létrejön egy további csomópont: *node3*.
1. A *node1* csomópontból származó fertőzöttek a *node3 csomópontra vannak alkalmazva.*
1. *A node1* csomópont törlődik.
1. Létrejön egy *új node1,* amely az eredeti *node1-re lesz lecserélve.*
1. A *node2 fertőzöttek* az új *node1 csomópontra vannak alkalmazva.* 
1. *A node2* csomópont törlődik.

A *node1 lényegében* *node3* lesz, a *node2* pedig az új *node1.*

Amikor az AKS-ben skáláz egy csomópontkészletet, a fertőzöttek és a lerokációk nem hatnak tovább a tervezés során.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Podütemezés vezérlése csomópontválasztók és affinitás használatával

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> Csomópont-választók, csomópont-affinitás vagy podok közötti affinitás használatával szabályozhatja a podok ütemezését a csomópontokon. Ezekkel a beállításokkal a Kubernetes-ütemező logikailag elkülönítheti a számítási feladatokat, például a csomópont hardverei alapján.

A fertőzöttek és a leraktások logikailag elkülönítik az erőforrásokat egy erősen levágással. Ha a pod nem tolerálja egy csomópont fertőzött voltát, nincs ütemezve a csomóponton.

Csomópontválasztókat is használhat. Megcímkézheti például a csomópontokat a helyileg csatlakoztatott SSD-tárolók vagy nagy mennyiségű memória jelzésére, majd a pod specifikációjában meghatározhat egy csomópontválasztót. A Kubernetes ezeket a podokat egy megfelelő csomóponton ütemezi.

A leraktálásokkal ellentétben a megfelelő csomópontválasztó nélküli podok továbbra is ütemezhetőek címkével jelölt csomópontokon. Ez a viselkedés lehetővé teszi a nem használt erőforrások felhasználását a csomópontokon, de priorizálja az egyező csomópontválasztót meghatározó podokat.

Nézzünk meg egy példát a nagy mennyiségű memóriával és csomópontokkal. Ezek a csomópontok olyan podokat rangsorolnak, amelyek nagy mennyiségű memóriát kérnek. Annak érdekében, hogy az erőforrások ne tétlenek, más podok futtatását is engedélyezik. A következő példaparancs egy *hardware=highmem* címkével jelölt csomópontkészletet ad hozzá a *myResourceGroup* *myAKSCluster* fürtje számára. A csomópontkészletben lévő összes csomópont rendelkezik ezzel a címkével.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels hardware=highmem \
    --no-wait
```

A pod specifikációja ezután hozzáadja a tulajdonságot egy csomópontválasztó meghatározásához, amely megfelel a csomóponton `nodeSelector` beállított címkének:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  nodeSelector:
      hardware: highmem
```

Ha ezeket az ütemezői beállításokat használja, az alkalmazásfejlesztőkkel és -tulajdonosokkal együtt engedélyezze számukra a podsok specifikációinak helyes meghatározását.

További információ a csomópontválasztók használatával kapcsolatban: [Podok hozzárendelése csomópontokhoz.][k8s-node-selector]

### <a name="node-affinity"></a>Csomópont-affinitás

A csomópontválasztó egy alapszintű megoldás podok egy adott csomóponthoz való hozzárendeléshez. *A csomópont-affinitás* nagyobb rugalmasságot biztosít, így meghatározhatja, mi történjen, ha a pod nem felel meg egy csomópontnak. A következőket teheti: 
* *A* Kubernetes-ütemezőnek meg kell egyezést végeznie egy címkével címkével jelölt gazdagéphez. Vagy
* *Egyezést* részesít előnyben, de ha nem érhető el egyezés, engedélyezze a pod másik gazdagépen való ütemezését.

Az alábbi példa a *szükségesDuringSchedulingIgnoredDuringExecution csomópont-affinitást állítja be.* Ehhez az affinitáshoz a Kubernetes-ütemezésnek egyező címkével kell használnia egy csomópontot. Ha nincs elérhető csomópont, a podnak meg kell várnia az ütemezés folytatását. Ahhoz, hogy a pod ütemezve legyen egy másik csomóponton, ehelyett beállíthatja a ***előnyben** részesített DuringSchedulingIgnoreDuringExecution* értéket:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

A *beállítás IgnoredDuringExecution* része azt jelzi, hogy a csomópont címkéinek módosítása esetén a podot nem szabad kiszűkülni a csomópontból. A Kubernetes Scheduler csak a frissített csomópontcímkéket használja az ütemezett új podokhoz, a csomópontokon már ütemezett podokat nem.

További információ: [Affinitás és affinitás-gátló.][k8s-affinity]

### <a name="inter-pod-affinity-and-anti-affinity"></a>Podok közötti affinitás és affinitás-gátló

A Kubernetes-ütemező egyik utolsó megközelítése a számítási feladatok logikai elkülönítésére a podok közötti affinitás vagy az affinitás-gátló kapcsolat használata. Ezek a beállítások határozzák meg,  hogy a podokat nem szabad vagy ütemezni egy olyan csomóponton, amely rendelkezik egy meglévő egyező podtal.  Alapértelmezés szerint a Kubernetes-ütemező több podot próbál ütemezni egy replikakészletben a csomópontok között. Ennél a viselkedésnél pontosabb szabályokat is meghatározhat.

Tegyük fel például, hogy van egy webalkalmazása, amely szintén Azure Cache for Redis. 
1. A podok affinitás-gátló szabályaival kérheti, hogy a Kubernetes-ütemező elosztsa a replikákat a csomópontok között. 
1. Affinitási szabályokkal biztosíthatja, hogy minden webalkalmazás-összetevő ugyanazon a gazdagépen legyen ütemezve, mint a megfelelő gyorsítótár. 

A podok csomópontok közötti eloszlása az alábbi példához hasonló:

| **1. csomópont** | **2. csomópont** | **3. csomópont** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

A podok közötti affinitás és az antiaffinitás összetettebb üzembe helyezést biztosít, mint a csomópontválasztók vagy a csomópont-affinitás. Az üzembe helyezéssel logikailag elkülönítheti az erőforrásokat, és szabályozhatja, hogy a Kubernetes hogyan ütemezi a podokat a csomópontokon. 

A webalkalmazás teljes példájért és Azure Cache for Redis lásd: Podok közös keresése ugyanazon a [csomóponton.][k8s-pod-affinity]

## <a name="next-steps"></a>Következő lépések

Ez a cikk a Kubernetes Scheduler speciális funkcióira összpontosított. Az AKS-fürtműveletekkel kapcsolatos további információkért tekintse meg az alábbi ajánlott eljárásokat:

* [Több-bérlős működés és fürtelkülönítés][aks-best-practices-scheduler]
* [A Kubernetes Scheduler alapszintű funkciói][aks-best-practices-scheduler]
* [Hitelesítés és engedélyezés][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[taint-node-pool]: use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
