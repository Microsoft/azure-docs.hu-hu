---
title: Kezelői ajánlott eljárások – alapszintű Scheduler-funkciók az Azure Kubernetes Servicesben (ak)
description: Az Azure Kubernetes Service (ak) szolgáltatás alapszintű ütemező funkcióinak, például az erőforrás-kvóták és a pod-megszakadási költségvetések használatára vonatkozó ajánlott eljárásainak megismerése
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 087c1d2efc93b8460a3683a4e66916d73fd4e885
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87015680"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások az Azure Kubernetes szolgáltatás alapszintű ütemező funkcióinak használatához (ak)

A fürtök Azure Kubernetes szolgáltatásban (ak) való kezelése során gyakran kell elkülönítenie a csapatokat és a munkaterheléseket. A Kubernetes Scheduler olyan funkciókat biztosít, amelyek segítségével szabályozhatja a számítási erőforrások eloszlását, vagy korlátozhatja a karbantartási események hatását.

Ez az ajánlott eljárási cikk a fürtcsomópontok alapszintű Kubernetes-ütemezési szolgáltatásaira koncentrál. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Erőforrás-kvóták használata a csapatok vagy munkaterhelések számára meghatározott mennyiségű erőforrás biztosításához
> * Az ütemezett karbantartás hatásának korlátozása a pod-megszakadási költségvetések használatával
> * Hiányzó Pod-erőforrásokra vonatkozó kérelmek és korlátozások keresése az `kube-advisor` eszköz használatával

## <a name="enforce-resource-quotas"></a>Erőforrás-kvóták betartatása

**Ajánlott eljárási útmutató** – erőforrás-kvóták megtervezése és alkalmazása a névtér szintjén. Ha a hüvely nem határozza meg az erőforrás-kérelmeket és a korlátozásokat, utasítsa el a telepítést. Figyelje az erőforrás-használatot, és szükség szerint módosítsa a kvótákat.

Az erőforrás-kérelmek és a korlátozások a pod-specifikációba kerülnek. Ezeket a korlátokat a Kubernetes ütemező használja a központi telepítés ideje alatt, hogy megtalálják a fürtben elérhető csomópontot. Ezek a korlátozások és kérések az egyes Pod-szinten működnek. További információ ezekről az értékekről: [Pod-erőforrásokra vonatkozó kérelmek és korlátok meghatározása][resource-limits] .

Az erőforrások egy fejlesztői csapaton vagy projekten belüli lefoglalásához és korlátozásához használjon erőforrás- *kvótákat*. Ezek a kvóták definiálva vannak egy névtérhez, és a következők alapján adhatók meg a kvóták:

* **Számítási erőforrások**, például CPU és memória, vagy GPU-k.
* A **tárolási erőforrások**, beleértve az adott tárolási osztály köteteinek teljes számát vagy lemezterületét.
* Az **objektumok száma**, például a titkok, szolgáltatások vagy feladatok maximális száma hozható létre.

A Kubernetes nem véglegesíti az erőforrásokat. Ha az erőforrás-kérelmek vagy-korlátok összesített összesített összege eléri a hozzárendelt kvótát, a további központi telepítések nem lesznek sikeresek.

Az erőforrás-kvóták meghatározásakor a névtérben létrehozott összes hüvelynek korlátozásokat vagy kéréseket kell megadnia a pod-specifikációkban. Ha nem adja meg ezeket az értékeket, elutasítja az üzemelő példányt. Ehelyett beállíthatja [a névterek alapértelmezett kéréseit és korlátait][configure-default-quotas].

A következő példa a *dev-app-Team-kvóták* nevű YAML jegyzékfájlt tartalmazza. a YAML összesen *10* processzor, *20Gi* és *10* hüvely rögzített korlátot állít be:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Ezt az erőforrás-kvótát a névtér, például a *fejlesztői alkalmazások* megadásával lehet alkalmazni:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Az alkalmazás-fejlesztőknek és a tulajdonosoknak az igényeik megértéséhez és a megfelelő erőforrás-kvóták alkalmazásához kell dolgozniuk.

Az elérhető erőforrás-objektumokkal, hatókörökkel és prioritásokkal kapcsolatos további információkért lásd: [erőforrás-kvóták a Kubernetes-ben][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Megtervezheti a rendelkezésre állást a pod megszakadási költségkeretek használatával

**Ajánlott eljárási útmutató** – az alkalmazások rendelkezésre állásának fenntartása, a pod-megszakadási költségvetések (PDBs-EK) meghatározása, amelyekkel meggyőződhet arról, hogy a fürtben minimális számú hüvely érhető el.

Két, a hüvelyek eltávolítását okozó zavaró esemény:

* A *kényszer nélküli fennakadások* olyan események, amelyek túlmutatnak a fürt vagy az alkalmazás tulajdonosának jellemző irányításán.
  * Ezek a akaratlanul fellépő hibák a fizikai gépen, a kernel pánikján vagy a csomópontos virtuális gépek törlésén is hardverhiba
* Az *önkéntes megszakítások* a fürt vagy az alkalmazás tulajdonosa által kért események.
  * Ezek az önkéntes fennakadások közé tartoznak a fürtök frissítése, a frissített központi telepítési sablon, vagy a pod véletlen törlése.

A kényszer nélküli fennakadások enyhítése a hüvelyek több replikájának használatával végezhető el egy üzemelő példányban. Ha több csomópontot futtat az AK-fürtben, a nem kötelezően megszakadó fennakadásokkal is segít. Az önkéntes megszakadások esetén a Kubernetes olyan *Pod-megszakítási költségvetést* biztosít, amely lehetővé teszi, hogy a fürt operátora meghatározza a minimális rendelkezésre álló vagy maximális számú erőforrás-darabszámot. Ezek a pod-megszakadási költségvetések lehetővé teszik, hogy megtervezze, hogyan reagálnak az üzembe helyezések vagy a replikák egy önkéntes megszakítási esemény bekövetkeztekor.

Ha egy fürtöt frissíteni kell, vagy a központi telepítési sablon frissült, a Kubernetes ütemező gondoskodik arról, hogy a további hüvelyek más csomópontokon is ütemezhetők legyenek, mielőtt az önkéntes megszakítási események folytatódnak. Az ütemező várakozik a csomópont újraindítása előtt, amíg a megadott számú hüvely sikeresen ütemezve lett a fürt más csomópontjain.

Lássunk egy példát egy olyan másodpéldányra, amelyben öt, NGINX-t futtató hüvely található. A rendszer a replika hüvelyét rendeli hozzá a címkéhez `app: nginx-frontend` . Egy önkéntes megszakítási esemény, például egy fürt frissítése során győződjön meg arról, hogy legalább három hüvely továbbra is fut. A *PodDisruptionBudget* objektum következő YAML-jegyzékfájlja határozza meg ezeket a követelményeket:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
    matchLabels:
      app: nginx-frontend
```

Meghatározhatja a százalékos értéket is *(például 60%*), amely lehetővé teszi, hogy automatikusan kompenzálja a másodpéldányok számát a hüvelyek számának skálázása során.

Megadhatja a kópiakészlet nem elérhető példányainak maximális számát. A maximálisan nem elérhető hüvelyek esetében a százalékos érték is meghatározható. A következő Pod-megszakadási költségvetési YAML-jegyzék azt határozza meg, hogy a kópiakészlet legfeljebb két hüvelye nem érhető el:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
    matchLabels:
      app: nginx-frontend
```

Miután definiálta a pod-megszakadási költségvetést, azt a többi Kubernetes objektummal együtt hozza létre az AK-fürtben:

```console
kubectl apply -f nginx-pdb.yaml
```

Az alkalmazás-fejlesztőknek és a tulajdonosoknak az igényeik megértéséhez és a megfelelő Pod-megszakadási költségvetések alkalmazásához kell dolgozniuk.

További információ a pod-megszakadási költségvetések használatáról: [megszakadási költségvetés megadása az alkalmazáshoz][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>A fürtökkel kapcsolatos problémák rendszeres keresése a Kube-Advisor szolgáltatással

**Ajánlott eljárások – útmutató** – rendszeresen futtassa a `kube-advisor` nyílt forráskódú eszköz legújabb verzióját a fürtben felmerülő problémák észlelése érdekében. Ha egy meglévő AK-fürthöz erőforrás-kvótát alkalmaz, `kube-advisor` először futtassa a parancsot az erőforrás-kérelmeket és korlátozásokat nem tartalmazó hüvelyek kereséséhez.

A [Kube-Advisor][kube-advisor] eszköz egy kapcsolódó, AK-beli nyílt forráskódú projekt, amely egy Kubernetes-fürtöt és a megtalált problémákkal kapcsolatos jelentéseket keres. Az egyik hasznos lehetőség az, hogy azonosítsa azokat a hüvelyeket, amelyek nem rendelkeznek erőforrás-kérelmekkel és korlátokkal.

Az Kube-Advisor eszköz jelentést készíthet az erőforrás-kérésekről, valamint a Windows-alkalmazások és a Linux-alkalmazások PodSpecs hiányzó korlátairól, de a Kube-Advisor eszköznek egy linuxos Pod-on kell ütemeznie. Egy Pod-t úgy ütemezhet, hogy egy adott operációs rendszert futtató csomópont-készleten fusson a pod konfigurációjában a [csomópont-választó][k8s-node-selector] használatával.

Egy több fejlesztői csapatot és alkalmazást futtató AK-fürtben nehéz lehet nyomon követni a hüvelyeket ezen erőforrás-kérelmek és korlátok beállítása nélkül. Ajánlott eljárásként rendszeresen futtasson `kube-advisor` az AK-fürtökön, különösen akkor, ha nem rendel hozzá erőforrás-kvótákat a névterekhez.

## <a name="next-steps"></a>Következő lépések

Ez a cikk az alapszintű Kubernetes Scheduler-funkciókra összpontosít. Az AK-beli fürtműveleteket kapcsolatos további információkért tekintse meg az alábbi ajánlott eljárásokat:

* [Több-bérlős működés és fürtelkülönítés][aks-best-practices-cluster-isolation]
* [Speciális Kubernetes Scheduler-funkciók][aks-best-practices-advanced-scheduler]
* [Hitelesítés és engedélyezés][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
