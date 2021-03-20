---
title: Ajánlott eljárások a Scheduler funkcióihoz
titleSuffix: Azure Kubernetes Service
description: Ismerje meg a fürt operátorának ajánlott eljárásait a speciális Scheduler-funkciók, például a szennyező elemek és a tolerálás, a csomópont-választók és az affinitás, illetve az Azure Kubernetes szolgáltatásban (ak) közötti affinitás és affinitás
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 1a8138b4b2fdab2cdef8d2cb4c27de8d12ef38cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97107346"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a Scheduler speciális funkcióihoz az Azure Kubernetes Service (AKS) szolgáltatásban

A fürtök Azure Kubernetes szolgáltatásban (ak) való kezelése során gyakran kell elkülönítenie a csapatokat és a munkaterheléseket. A Kubernetes Scheduler olyan speciális funkciókat biztosít, amelyekkel szabályozható, hogy mely hüvelyek ütemezhetők bizonyos csomópontokon, illetve hogyan lehet a több-Pod alkalmazásokat a fürtön belül elosztani. 

Ez az ajánlott eljárási cikk a fürtcsomópontok Speciális Kubernetes-ütemezési szolgáltatásaira koncentrál. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A kihelyezés és a tolerancia használata a csomópontokon ütemezhető hüvelyek korlátozására
> * Előnyt biztosíthat a hüvelyek számára, hogy bizonyos csomópontokon, csomópont-választókkal vagy csomópont-affinitással fussanak
> * Szétosztva vagy csoportosítsa a hüvelyeket az Inter-Pod affinitással vagy a kapcsolat összekapcsolásával

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Célzott csomópontok biztosítása a megfertőzés és a tolerálás használatával

**Ajánlott eljárási útmutató** – az erőforrás-igényes alkalmazások, például a bejövő vezérlők hozzáférésének korlátozása adott csomópontokra. Tartsa elérhetővé a csomópont-erőforrásokat a szükséges számítási feladatokhoz, és ne engedélyezze más számítási feladatok ütemezését a csomópontokon.

Az AK-fürt létrehozásakor a csomópontokat GPU-támogatással vagy nagy számú nagy teljesítményű processzorral is üzembe helyezheti. Ezeket a csomópontokat gyakran használják a nagyméretű adatfeldolgozási számítási feladatokhoz, például a gépi tanuláshoz (ML) vagy a mesterséges intelligenciához (AI). Mivel az ilyen típusú hardver általában egy költséges csomópont-erőforrás, amely a csomópontokon ütemezhető munkaterheléseket korlátozza. Ehelyett érdemes lehet a fürt egyes csomópontjait a bejövő szolgáltatások futtatására használni, és meg kell akadályozni más munkaterheléseket.

A különböző csomópontok támogatását több csomópontos készlet is használja. Az AK-fürtök egy vagy több csomópontot biztosítanak.

A Kubernetes-ütemező használatával megtilthatja, hogy milyen számítási feladatok futhatnak a csomópontokon.

* A rendszer egy olyan csomópontra alkalmazza a **megromlást** , amely azt jelzi, hogy csak bizonyos hüvelyek ütemezhetők.
* A **rendszer egy olyan** Pod-ra alkalmazza a betartást, amely lehetővé *teszi a* csomópontok megromlását.

Ha a pod-t egy AK-fürtön helyezi üzembe, a Kubernetes csak azokat a csomópontokat ütemezhet, amelyekben a tolerancia megfelel a szennyező elemnek. Tegyük fel például, hogy rendelkezik egy Node-készlettel az AK-fürtben a GPU-támogatással rendelkező csomópontok számára. Megadhatja a nevet, például a *GPU*-t, majd az ütemezés értékét. Ha ez az érték nem *ütemezhető*, a Kubernetes Scheduler nem ütemezhet hüvelyeket a csomóponton, ha a pod nem határozza meg a megfelelő tolerancia értékét.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

A csomópontokra alkalmazott szennyező adatokkal meghatározható a pod-specifikációban a csomópontok ütemezését lehetővé tevő tolerancia. A következő példa meghatározza a `sku: gpu` és az `effect: NoSchedule` általa az előző lépésben a csomópontra alkalmazott adatszennyezettség eltűriét:

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

A hüvely üzembe helyezésekor, például a használatával a `kubectl apply -f gpu-toleration.yaml` Kubernetes sikeresen ütemezhetik a pod-t a csomópontokon az alkalmazott szennyező adataival. Ez a logikai elkülönítés lehetővé teszi a fürtön belüli erőforrásokhoz való hozzáférés szabályozását.

Ha a szennyező adatait alkalmazza, együttműködik az alkalmazás-fejlesztővel és a tulajdonosokkal, hogy meghatározza a szükséges megtartásokat az üzemelő példányokban.

További információ arról, hogyan használható több Node-készlet az AK-ban: [több csomópontos készlet létrehozása és kezelése a fürthöz az AK-ban][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>A megfertőzés és a tolerancia viselkedése az AK-ban

Ha AK-ban frissít egy csomópont-készletet, a szennyező elem és a tolerálás egy meghatározott mintát követ, amelyet az új csomópontokra alkalmaz:

- **Virtuálisgép-méretezési csoportokat használó alapértelmezett fürtök**
  - A nodepool az AK API-ból [fertőzheti][taint-node-pool] meg, hogy az újonnan kibővített csomópontok megkapják a csomópontok megadott API-ját.
  - Tegyük fel, hogy van egy két csomópontos *csomópont1* és *Csomópont2*. Frissíti a csomópont-készletet.
  - Két további csomópont jön létre, *csomópont3* és *csomópont4*, és a rendszer átadja a megfertőzt állapotokat.
  - A rendszer törli az eredeti *csomópont1* és *Csomópont2* .

- **Virtuálisgép-méretezési csoport támogatása nélküli fürtök**
  - Először is tegyük fel, hogy van egy két csomópontos *csomópont1* és *Csomópont2*. A frissítésekor egy további csomópont (*csomópont3*) jön létre.
  - A rendszer a *csomópont1* -től származó adatszennyező adatokra alkalmazza a *csomópont3*, majd a *csomópont1* törölve lesz.
  - Létrejön egy másik új csomópont ( *csomópont1* néven, mivel az előző *csomópont1* törölték), és a *Csomópont2* -adatszennyező elemek az új *csomópont1* lesznek alkalmazva. Ezt követően a rendszer törli a *Csomópont2* .
  - A Essence *csomópont1* *csomópont3* válik, és  a Csomópont2 *csomópont1* válik.

Ha AK-ban méretezi a csomópont-készletet, a rendszer nem hajtja végre a megtervezést.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>A hüvely ütemezésének vezérlése csomópont-választókkal és affinitással

**Ajánlott eljárási útmutató** – a hüvelyek ütemezésének szabályozása csomópont-választókkal, csomópont-affinitással vagy Inter-Pod affinitással. Ezek a beállítások lehetővé teszik a Kubernetes ütemező számára, hogy logikailag elkülönítse a munkaterheléseket, például a csomópontban található hardvereket.

A szennyező anyagok és a tolerálás a nehezen kivágott erőforrások logikai elkülönítésére szolgál, ha a pod nem tűri a csomópontok megromlását, nem ütemezi a csomópontot. Alternatív módszer a csomópont-választók használata. A csomópontok címkézésével, például a helyileg csatlakoztatott SSD-tárolóval vagy nagy mennyiségű memóriával jelezheti a memóriát, majd definiálhatja a hüvely specifikációjában a csomópont-választót. A Kubernetes ezután a hüvelyeket egy megfelelő csomóponton ütemezheti. A tolerancia használatával ellentétben a hüvelyek megfelelő csomópont-választó nélkül ütemezhetők a címkézett csomópontokon. Ez a viselkedés lehetővé teszi a csomópontok nem használt erőforrásainak felhasználását, de elsőbbséget biztosít a hüvelyeknek, amelyek meghatározzák a megfelelő csomópont-választót.

Tekintsük át a nagy mennyiségű memóriával rendelkező csomópontok példáját. Ezek a csomópontok előnyt biztosíthatnak a nagy mennyiségű memóriát igénylő hüvelyek számára. Győződjön meg arról, hogy az erőforrások nem tétlenek, hanem más hüvelyek futtatását is lehetővé teszik.

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

A csomópont-választó egy alapszintű módszer a hüvelyek egy adott csomóponthoz való hozzárendelésére. A *csomópont affinitása* nagyobb rugalmasságot biztosít. A csomópont-affinitással meghatározhatja, hogy mi történjen, ha a hüvely nem egyeztethető össze egy csomóponttal. *Megkövetelheti* , hogy a Kubernetes Scheduler megfeleljen egy Pod címkével ellátott gazdagépnek. Másik *lehetőségként választhat,* de lehetővé teszi, hogy a pod más gazdagépre legyen ütemezve, ha nem áll rendelkezésre egyezés.

A következő példa beállítja a csomópont-affinitást a *requiredDuringSchedulingIgnoredDuringExecution*. Ez az affinitás megköveteli, hogy a Kubernetes-ütemterv megfelelő címkével rendelkező csomópontot használjon. Ha nincs elérhető csomópont, a pod-nek várnia kell, amíg az ütemezés folytatódni fog. Annak engedélyezéséhez, hogy a pod egy másik csomóponton legyen ütemezve, ehelyett a következőt állíthatja be a *preferredDuringSchedulingIgnoreDuringExecution* értékre:

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

A beállítás *IgnoredDuringExecution* része azt jelzi, hogy ha a csomópontok felirata megváltozik, a hüvelyt nem szabad kizárni a csomópontból. A Kubernetes Scheduler csak az új hüvelyek frissített csomópontjait használja az ütemezéshez, nem pedig a csomópontokon már ütemezve van a hüvelyek.

További információ: [affinitás és affinitás][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Inter-Pod affinitás és affinitás

Az Kubernetes Scheduler egyik végső megközelítése, hogy logikailag elkülönítse a munkaterheléseket, és az egymástól származó affinitást vagy affinitást használ. A beállítások azt határozzák meg, hogy a hüvelyek *ne* legyenek ütemezve olyan csomóponton, amely rendelkezik egy meglévő megfelelő Pod-vel, vagy *hogy be legyen* ütemezve. Alapértelmezés szerint a Kubernetes Scheduler több hüvely ütemezését kísérli meg a csomópontok közötti replikában. A viselkedés körül részletesebb szabályokat is meghatározhat.

A jó példa egy olyan webalkalmazás, amely egy Azure cache-t is használ a Redis. A pod-affinitási szabályok segítségével kérheti, hogy a Kubernetes Scheduler elossza a replikákat a csomópontok között. Ezután az affinitási szabályok segítségével gondoskodhat arról, hogy minden webalkalmazás-összetevő ütemezve legyen ugyanazon a gazdagépen, mint a megfelelő gyorsítótár. A hüvelyek csomópontok közötti eloszlása a következő példához hasonlóan néz ki:

| **1. csomópont** | **2. csomópont** | **3. csomópont** |
|------------|------------|------------|
| WebApp-1   | WebApp – 2   | WebApp – 3   |
| gyorsítótár – 1    | gyorsítótár – 2    | gyorsítótár – 3    |

Ez a példa összetettebb üzembe helyezés, mint a csomópont-választó vagy a csomópont-affinitás használata. Az üzembe helyezés lehetővé teszi, hogy a Kubernetes hogyan ütemezze a csomópontokon a hüvelyeket, és képes legyen logikai módon elkülöníteni az erőforrásokat. A webalkalmazásnak az Azure cache for Redis példaként való teljes példáját lásd: [a hüvelyek együttes megkeresése ugyanazon a csomóponton][k8s-pod-affinity].

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
