---
title: Ajánlott eljárások a Scheduler funkcióihoz
titleSuffix: Azure Kubernetes Service
description: Ismerje meg a fürt operátorának ajánlott eljárásait a speciális Scheduler-funkciók, például a szennyező elemek és a tolerálás, a csomópont-választók és az affinitás, illetve az Azure Kubernetes szolgáltatásban (ak) közötti affinitás és affinitás
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 27b32d7d10b691ed806e4d7aa31a095630d2bfc9
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103623"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a Scheduler speciális funkcióihoz az Azure Kubernetes Service (AKS) szolgáltatásban

A fürtök Azure Kubernetes szolgáltatásban (ak) való kezelése során gyakran kell elkülönítenie a csapatokat és a munkaterheléseket. A Kubernetes Scheduler által biztosított speciális funkciók lehetővé teszik a következők felügyeletét:
* Mely hüvelyek ütemezhetnek bizonyos csomópontokon.
* A több-Pod alkalmazások megfelelő elosztása a fürtön keresztül. 

Ez az ajánlott eljárási cikk a fürtcsomópontok Speciális Kubernetes-ütemezési szolgáltatásaira koncentrál. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A szennyező elemek és a tolerancia használatának korlátozásával korlátozhatja, hogy a rendszer hogyan ütemezheti a csomópontokon a hüvelyeket.
> * Előnyt biztosíthat a hüvelyek számára, hogy a csomópont-választókkal vagy csomópont-affinitással bizonyos csomópontokon fussanak.
> * Ossza szét egymástól a hüvelyeket, és hozzon össze egy Pod affinitást vagy egy affinitást.

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Célzott csomópontok biztosítása a megfertőzés és a tolerálás használatával

> **Ajánlott eljárási útmutató:** 
>
> Az erőforrás-igényes alkalmazások, például a bejövő vezérlők hozzáférésének korlátozása adott csomópontokra. Tartsa elérhetővé a csomópont-erőforrásokat a szükséges számítási feladatokhoz, és ne engedélyezze más számítási feladatok ütemezését a csomópontokon.

Az AK-fürt létrehozásakor a csomópontokat GPU-támogatással vagy nagy számú nagy teljesítményű processzorral is üzembe helyezheti. Ezek a csomópontok nagy adatfeldolgozási számítási feladatokhoz használhatók, például gépi tanuláshoz (ML) vagy mesterséges intelligencia (AI). 

Mivel ez a csomópont-erőforrás hardver általában költséges, a csomópontokon ütemezhető munkaterhelések korlátozásával korlátozható. Ehelyett a fürt egyes csomópontjait a bejövő szolgáltatások futtatására és más munkaterhelések megelőzésére szánja.

A különböző csomópontok támogatását több csomópontos készlet is használja. Az AK-fürtök egy vagy több csomópontot biztosítanak.

A Kubernetes Scheduler a kihasználat és a tolerancia használatát használja annak a korlátozására, hogy milyen számítási feladatok futhatnak a csomópontokon.

* Ha csak bizonyos hüvelyeket szeretne ütemezni, alkalmazza a **Taint** egy csomópontra.
* Ezután alkalmazzon **egy-egy Pod-t,** amely lehetővé *teszi a* csomópontok megromlását.

Ha a pod-t egy AK-fürtre helyezi üzembe, a Kubernetes csak azokat a csomópontokat ütemezhet, amelyeknek a romlásával összhangban vannak. Tegyük fel például, hogy rendelkezik egy Node-készlettel az AK-fürtben a GPU-támogatással rendelkező csomópontok számára. Megadhatja a nevet, például a *GPU*-t, majd az ütemezés értékét. Ha ez az érték nem *ütemezhető* , a Kubernetes ütemező az ütemezési hüvelyeket a csomóponton definiálatlan tolerancia alapján korlátozza.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Ha a csomópontokon a szennyező adatok vannak alkalmazva, akkor a pod-specifikációban meg kell határoznia az ütemezést a csomópontokon. A következő példa meghatározza a `sku: gpu` és az `effect: NoSchedule` általa az előző lépésben a csomópontra alkalmazott adatszennyezettség eltűriét:

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

Ha ezt a hüvelyt a használatával helyezi üzembe `kubectl apply -f gpu-toleration.yaml` , a Kubernetes sikeresen ütemezhetik a pod-t a csomópontokon az alkalmazott szennyező adataival. Ez a logikai elkülönítés lehetővé teszi a fürtön belüli erőforrásokhoz való hozzáférés szabályozását.

Ha a szennyező adatait alkalmazza, együttműködik az alkalmazás-fejlesztővel és a tulajdonosokkal, hogy meghatározza a szükséges megtartásokat az üzemelő példányokban.

További információ arról, hogyan használható több Node-készlet az AK-ban: [több csomópontos készlet létrehozása és kezelése a fürthöz az AK-ban][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>A megfertőzés és a tolerancia viselkedése az AK-ban

Ha AK-ban frissít egy csomópont-készletet, a szennyező elem és a tolerálás egy meghatározott mintát követ, amelyet az új csomópontokra alkalmaz:

#### <a name="default-clusters-that-use-vm-scale-sets"></a>VM-méretezési csoportokat használó alapértelmezett fürtök
[Egy csomópont-készletet][taint-node-pool] az AK API-ból úgy is megfertőzheti, hogy az újonnan kibővített csomópontok megkapják a csomópontok megadott API-ját.

Tegyük fel, hogy:
1. Két csomópontos fürttel kezdődik: *csomópont1* és *Csomópont2*. 
1. Frissíti a csomópont-készletet.
1. Két további csomópont jön létre: *csomópont3* és *csomópont4*. 
1. A rendszer átadja a megfertőzt.
1. A rendszer törli az eredeti *csomópont1* és *Csomópont2* .

#### <a name="clusters-without-vm-scale-set-support"></a>A virtuálisgép-méretezési csoport támogatását nem támogató fürtök

Ismét tegyük fel, hogy:
1. Két csomópontos fürttel rendelkezik: *csomópont1* és *Csomópont2*. 
1. Ezután frissítse a csomópont-készletet.
1. A rendszer létrehoz egy további csomópontot: *csomópont3*.
1. A *csomópont1* származó adatszennyező adatok a *csomópont3* vonatkoznak.
1. a *csomópont1* törölve.
1. Létrejön egy új *csomópont1* , amely az eredeti *csomópont1* vált.
1. A *Csomópont2* -megfertőzi a rendszer az új *csomópont1* alkalmazza. 
1. a *Csomópont2* törölve.

A Essence *csomópont1* *csomópont3* válik, a *Csomópont2* pedig az új *csomópont1* válik.

Ha AK-ban méretezi a csomópont-készletet, a rendszer nem hajtja végre a megtervezést.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>A hüvely ütemezésének vezérlése csomópont-választókkal és affinitással

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> A hüvelyek csomópontjainak a csomópont-választókkal, csomópont-affinitással vagy Inter-Pod affinitással való használatának szabályozása. Ezek a beállítások lehetővé teszik a Kubernetes ütemező számára, hogy logikailag elkülönítse a munkaterheléseket, például a csomópontban található hardvereket.

A szennyező anyagok és a tolerálás logikailag elkülöníti az erőforrásokat a nehezen kivágott terheléssel. Ha a pod nem tűri el a csomópontok megromlását, nem ütemezi a csomópontot. 

Másik lehetőségként a csomópont-választókat is használhatja. Például megcímkézheti a csomópontokat, hogy a helyileg csatlakoztatott SSD-tárolót vagy nagy mennyiségű memóriát jelezzen, majd definiálja a hüvely specifikációjában a csomópont-választót. A Kubernetes egy megfelelő csomóponton ütemezze ezeket a hüvelyeket. 

A toleranciatől eltérően a hüvelyek nem megfelelő csomópont-választóval is ütemezhetők a címkézett csomópontokon. Ez a viselkedés lehetővé teszi a csomópontok nem használt erőforrásainak használatát, de rangsorolja a megfelelő csomópont-választót meghatározó hüvelyeket.

Tekintsük át a nagy mennyiségű memóriával rendelkező csomópontok példáját. Ezek a csomópontok rangsorolják a hüvelyeket, amelyek nagy mennyiségű memóriát igényelnek. Annak biztosítása érdekében, hogy az erőforrások ne tétlenek legyenek, más hüvelyek futtatását is lehetővé teszi.

```console
kubectl label node aks-nodepool1 hardware=highmem
```

A pod-specifikáció ezt követően hozzáadja a `nodeSelector` tulajdonságot olyan csomópont-választó definiálásához, amely megfelel a csomóponton beállított címkének:

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

Ha ezeket a Scheduler-beállításokat használja, az alkalmazás fejlesztőivel és a tulajdonosokkal együttműködve lehetővé teszi számukra, hogy megfelelően meghatározzák a pod-specifikációkat.

További információ a csomópont-választók használatáról: [hüvelyek csomópontokhoz rendelése][k8s-node-selector].

### <a name="node-affinity"></a>Csomópont-affinitás

A csomópont-választó egy alapszintű megoldás a hüvelyek egy adott csomóponthoz való hozzárendeléséhez. A *csomópont-affinitás* nagyobb rugalmasságot biztosít, így meghatározhatja, hogy mi történik, ha a pod nem egyeztethető össze egy csomóponttal. A következőket teheti: 
* *Megkövetelheti* , hogy a Kubernetes Scheduler megfeleljen egy Pod címkével ellátott gazdagépnek. Vagy
* *Inkább* egy egyezésre van szükség, de lehetővé teszi, hogy a pod egy másik gazdagépen legyen ütemezve, ha nem érhető el egyezés.

A következő példa beállítja a csomópont-affinitást a *requiredDuringSchedulingIgnoredDuringExecution*. Ez az affinitás megköveteli, hogy a Kubernetes-ütemterv megfelelő címkével rendelkező csomópontot használjon. Ha nincs elérhető csomópont, a pod-nek várnia kell, amíg az ütemezés folytatódni fog. Annak engedélyezéséhez, hogy a pod egy másik csomóponton legyen ütemezve, ehelyett állítsa az értéket ***előnyben részesített** DuringSchedulingIgnoreDuringExecution * értékre:

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

A beállítás *IgnoredDuringExecution* része azt jelzi, hogy a hüvelyt nem szabad kizárni a csomópontról, ha a csomópontok címkéi megváltoznak. A Kubernetes Scheduler csak az új hüvelyek frissített csomópontjait használja az ütemezéshez, nem pedig a csomópontokon már ütemezve van a hüvelyek.

További információ: [affinitás és affinitás][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Inter-Pod affinitás és affinitás

Az Kubernetes Scheduler egyik végső megközelítése, hogy logikailag elkülönítse a munkaterheléseket, és az egymástól származó affinitást vagy affinitást használ. Ezek a beállítások határozzák meg, hogy a hüvelyek *ne* legyenek, vagy ne legyenek ütemezve egy meglévő egyező *Pod-t* tartalmazó csomóponton. Alapértelmezés szerint a Kubernetes Scheduler több hüvely ütemezését kísérli meg a csomópontok közötti replikában. A viselkedés körül részletesebb szabályokat is meghatározhat.

Például van egy webalkalmazása, amely egy Azure cache-t is használ a Redis. 
1. A pod-affinitási szabályok használatával kérheti, hogy a Kubernetes Scheduler elossza a replikákat a csomópontok között. 
1. Az affinitási szabályok használatával biztosíthatja, hogy minden webalkalmazás-összetevő a megfelelő gyorsítótárral azonos gazdagépre legyen ütemezve. 

A hüvelyek csomópontok közötti eloszlása a következő példához hasonlóan néz ki:

| **1. csomópont** | **2. csomópont** | **3. csomópont** |
|------------|------------|------------|
| WebApp-1   | WebApp – 2   | WebApp – 3   |
| gyorsítótár – 1    | gyorsítótár – 2    | gyorsítótár – 3    |

Az Inter-Pod affinitás és a kapcsolati affinitás olyan összetettebb telepítést biztosít, mint a csomópont-választó vagy a csomópont affinitása. Az üzembe helyezés során logikailag elkülöníti az erőforrásokat, és szabályozhatja, hogy a Kubernetes hogyan ütemezze a csomópontokon a hüvelyeket. 

A webalkalmazásnak az Azure cache for Redis példaként való teljes példáját lásd: [a hüvelyek együttes megkeresése ugyanazon a csomóponton][k8s-pod-affinity].

## <a name="next-steps"></a>Következő lépések

Ez a cikk a speciális Kubernetes Scheduler-funkciókra összpontosít. Az AK-beli fürtműveleteket kapcsolatos további információkért tekintse meg az alábbi ajánlott eljárásokat:

* [Több-bérlős működés és fürtelkülönítés][aks-best-practices-scheduler]
* [Alapszintű Kubernetes Scheduler-funkciók][aks-best-practices-scheduler]
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
