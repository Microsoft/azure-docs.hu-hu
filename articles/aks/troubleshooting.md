---
title: Gyakori Azure Kubernetes Service-problémák hibaelhárítása
description: Útmutató a gyakori problémák elhárítására Azure Kubernetes Service (AKS) használata esetén
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: f0b0ff3ff4ac742a7e850798c736eb31098f66e8
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966382"
---
# <a name="aks-troubleshooting"></a>AKS-hibaelhárítás

Amikor hoz létre, vagy az Azure Kubernetes Service (AKS)-fürtök kezelése, előfordulhat, hogy időnként tapasztal. Ez a cikk részletesen néhány gyakori probléma és a hibaelhárítási lépéseket.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Általánosságban elmondható, hol található információ a Kubernetes hibakeresésével?

Próbálja ki a [hivatalos útmutató a Kubernetes-fürtök hibaelhárítási](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Emellett van egy [hibaelhárítási útmutató](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md), egy Microsoft-mérnök podok, csomópontok, fürtök és egyéb funkciókkal kapcsolatos hibaelhárításhoz tett közzé.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Egy "kvóta túllépve" hiba kapok létrehozása vagy frissítése során. Mit tegyek? 

Kell [magok kérelem](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Mi az a maximális podok száma csomópontonként beállítás az aks-ben?

A maximális podok száma csomópontonként értéke alapértelmezés szerint 30, ha telepít egy AKS-fürtöt az Azure Portalon.
A maximális podok száma csomópontonként értéke 110 alapértelmezés szerint ha telepít egy AKS-fürtöt az Azure CLI-ben. (Ügyeljen rá, hogy az Azure CLI legújabb verzióját használja). Ez az alapértelmezett beállítás használatával módosítható a `–-max-pods` a jelzőt a `az aks create` parancsot.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>InsufficientSubnetSize hibaüzenetet kapok speciális hálózatkezelés az AKS-fürt üzembe helyezése során. Mit tegyek?

Ha Azure CNI (speciális networking) használ, az AKS preallocates IP-Címének egyikét a "max-podok" konfigurálva csomópontonként alapján. AKS-fürt a csomópontok száma 1 és a 110-es közötti bárhol lehet. A beállított maximális podok száma csomópontonként alapján, az alhálózat méretét nagyobbnak kell lennie a "termék, a csomópontok száma és a csomópontonkénti maximális pod". A következő alapvető egyenlet Ez ismerteti:

Alhálózat méretét > (figyelembe véve a jövőbeli méretezési követelmények) a fürtben található csomópontok száma * podok csomópontonkénti maximális száma.

További információkért lásd: [megtervezése IP-címkezelés a fürt](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>A pod CrashLoopBackOff módban elakadt. Mit tegyek?

Előfordulhat, hogy a pod folyamatban elakadt ebben a módban a különböző okok miatt. Az nézhet ki:

* Maga a pod használatával `kubectl describe pod <pod-name>`.
* A naplók segítségével `kubectl log <pod-name>`.

A pod-problémák elhárításáról további információk: [-alkalmazások hibakeresését](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>A tapasztalataimat szeretném RBAC ahhoz, hogy egy meglévő fürt. Hogyan tehetem ezt?

Sajnos engedélyezése szerepköralapú hozzáférés-vezérlés (RBAC) a meglévő fürtökön nem támogatott. Explicit módon létre kell hoznia az új fürtök. Ha a parancssori Felületet használja, az RBAC alapértelmezés szerint engedélyezve van. Az AKS portal használatakor egy váltógomb engedélyezése az RBAC a létrehozási munkafolyamat érhető el.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Az RBAC engedélyezve van az alapértelmezett értékeket, vagy az Azure Portalon az Azure CLI használatával létrehozott egy fürtöt, és most már sok figyelmeztetések jelennek meg a Kubernetes-irányítópultot. Az Irányítópult segítségével megvalósítható, ha figyelmeztetéseket. Mit tegyek?

A figyelmeztetések az irányítópulton az oka, hogy a fürt engedélyezve van az RBAC, és hozzáférést alapértelmezés szerint le van tiltva. Ez a megközelítés általában célszerű, mert az alapértelmezett kitettség az irányítópult a fürt minden felhasználó számára a biztonsági fenyegetések vezethet. Ha továbbra is engedélyezni szeretné az irányítópulton, kövesse a [ebben a blogbejegyzésben](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Nem lehet csatlakozni az irányítópulton. Mit tegyek?

A legegyszerűbben úgy, hogy hozzáférhessen a szolgáltatáshoz a fürtön kívül, hogy futtassa `kubectl proxy`, mely proxyk kéréseket küldeni a a localhost-8001 a Kubernetes API-kiszolgálóhoz. Itt az API-kiszolgáló is a szolgáltatás proxy: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Ha nem látja a Kubernetes-irányítópult, ellenőrizze-e a `kube-proxy` pod fut a `kube-system` névtér. Ha nem futó állapotban, törölje a pod, és újra fog.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Naplók nem jelenik meg a kubectl-naplók használatával, vagy nem tud kapcsolódni az API-kiszolgálóhoz. Érkeznek meg hozzám a "kiszolgálótól érkező hiba: hiba hangtárcsázás háttér: tárcsázza a tcp …". Mit tegyek?

Győződjön meg arról, hogy az alapértelmezett hálózati biztonsági csoport nem módosul, és, hogy 22-es port meg nyitva, az API-kiszolgálóhoz való csatlakozáshoz. Ellenőrizze-e a `tunnelfront` pod fut a *kube rendszer* névtér használatával a `kubectl get pods --namespace kube-system` parancsot. Ha nem, a pod és kényszerített törlése újraindul.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>I frissítésével, vagy méretezheti a tapasztalataimat és érkeznek meg hozzám a "üzenet: Hiba a "ImageReference" tulajdonság módosítása nem engedélyezett". Hogyan lehet kijavítani a hibát a probléma?

Előfordulhat, hogy lehet első ezt a hibát, mert az ügynökcsomópontok az AKS-fürtben lévő címkéket módosította. Módosítása és törlése a címkék és egyéb tulajdonságait a MC_ * erőforráscsoportban lévő erőforrásokat váratlan eredményekhez vezethet. Az AKS MC_ * tartozó az erőforrások módosítását a fürt a szolgáltatásiszint-célkitűzés (SLO) működésképtelenné válik.

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>A hibákat, a fürt hibás állapotban van, és frissítése, illetve skálázás csak fog működni, rögzített kapok

*A hibaelhárítási segítséget a van irányítva. https://aka.ms/aks-cluster-failed*

Ez akkor fordul elő, amikor a fürtök több okból sikertelen állapotba adja meg. Oldja meg a fürt nem sikerült állapota a korábban meghiúsult művelet megkísérlése előtt az alábbi lépésekkel:

1. Amíg a fürtön kívüli `failed` állapot, `upgrade` és `scale` művelet nem sikerült. Legfelső szintű gyakori problémák és megoldásuk a következők:
    * A méretezés **(CRP) nincs elegendő számítási kvótával**. Oldja meg, hogy először egy cél stabil állapotba kvótán belül fürt méretezése. Ezután kövesse az alábbi [lépéseket egy számítási kvótát növelése](../azure-supportability/resource-manager-core-quotas-request.md) vertikális felskálázása újra nem csupán kezdeti kvótakorlát kísérlet előtt.
    * Fürtök méretezése speciális hálózatkezelés és **megfelelő alhálózati (hálózat) erőforrások**. Oldja meg, hogy először egy cél stabil állapotba kvótán belül fürt méretezése. Ezután kövesse [növelheti ezeket a lépéseket egy erőforráscsoport-kvótát](../azure-resource-manager/resource-manager-quota-errors.md#solution) újra nem csupán kezdeti kvótakorlát vertikális felskálázása előtt.
2. Az alapul szolgáló frissítési hiba okát megszűnése után a fürt állapot kell lennie. Állapot ellenőrzése után próbálkozzon újra az eredeti művelet.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Ha szeretne frissíteni vagy a méretezési csoport, amely a fürt állapot folyamatban van jelenleg folyamatban hibák kapok frissítésének vagy a frissítés sikertelen volt

*A hibaelhárítási segítséget a van irányítva. https://aka.ms/aks-pending-upgrade*

Fürtműveletek korlátozva, ha aktív frissítési műveletek történnek, vagy frissítés történt kísérlet, de ezt követően nem sikerült. Futtassa a probléma diagnosztizálása érdekében `az aks show -g myResourceGroup -n myAKSCluster -o table` a fürt részletes állapot lekéréséhez. Az eredmény alapján:

* Ha folyamatosan frissíti a fürt, várjon, amíg a művelet véget nem ér. Ha sikeres, próbálja meg újra a korábban sikertelen műveletet.
* Ha a fürt frissítése nem sikerült, hajtsa végre a fenti lépéseket

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Áthelyezhető a fürt egy másik előfizetésben vagy az előfizetés az új bérlőhöz fürt?

Az AKS-fürt egy másik előfizetésben, vagy a fürt egy új bérlő előfizetésének tulajdonos erőforrásfájljait áthelyezték, a fürt miatt elveszett szerepkör-hozzárendelések és a szolgáltatás egyszerű jogok funkciók elvesznek. **Az AKS nem támogatja a mozgó fürtök előfizetések vagy a bérlők között** miatt ez a korlátozás.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>A virtual machine scale sets igénylő szolgáltatások használata közben hibák kapok

*A hibaelhárítási segítséget az aka.ms/aks-vmss-kompatibilitási van irányítva.*

Hibák, amelyek jelzik, hogy az AKS-fürt nem szerepel, például az alábbi példa egy virtuális gép méretezési jelenhet meg:

**AgentPool "agentpool" be van állítva, automatikus méretezés, mert engedélyezve van, de nem szerepel a Virtual Machine Scale Sets**

Az funkciók például az automatikus méretező fürt vagy több csomópont-készletek, az AKS-fürtök kell létrehozni, a virtual machine scale sets használó. Rendszer által visszaadott hibák, ha megpróbálja használni a virtual machine scale sets eseményektől függő funkciók és a egy rendszeres, a virtual machine scale set AKS-fürt célozhat meg. Virtuális gép méretezési csoport támogatni fogja a jelenleg az aks-ben előzetes verzióban érhető el.

Kövesse a *megkezdése előtt* lépések a megfelelő doc megfelelően regisztrálja a virtuális gép méretezési funkció előzetes verzió, és az AKS-fürt létrehozása:

* [Fürt automatikus méretező használatához](cluster-autoscaler.md)
* [Létrehozhat és használhat több csomópontkészletek](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>AKS-erőforrások és a paraméterek a milyen vonatkozó elnevezési korlátozás kényszerítése

*A hibaelhárítási segítséget van irányítva az aka.ms/aks-elnevezési-szabályai*

Az Azure-platform és az AKS által megvalósított vonatkozó elnevezési korlátozás. Ha egy erőforrás neve vagy a paraméter megsérti e korlátozások egyike, hibát ad vissza, amely kéri, adja meg egy másik beviteli. A következő általános elnevezési szabályok érvényesek:

* Az AKS *MC_* erőforráscsoport-név egyesíti az erőforráscsoport nevét és az erőforrás neve. Automatikusan létrehozott szintaxisát `MC_resourceGroupName_resourceName_AzureRegion` nem lehet nagyobb, mint 80 karakter. Szükség esetén csökkentse a hosszát az erőforráscsoport neve vagy az AKS-fürt nevét.
* A *dnsPrefix* kell kezdődnie és végződnie számokat. Érvényes karakterek:, számokat és kötőjelet (-). A *dnsPrefix* nem tartalmazhat különleges karaktereket, például egy pontot (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Hibák kapok, létrehozása, frissítése, méretezhető, törölje vagy fürt, amely a művelet nem engedélyezett, mert egy másik művelet van folyamatban lévő frissítése közben.

*A hibaelhárítási segítséget aka.ms/aks-folyamatban-művelet van irányítva.*

Fürtműveletek korlátozva, amikor egy korábbi művelet még folyamatban van. A fürt állapotának részletes leírását lekéréséhez használja a `az aks show -g myResourceGroup -n myAKSCluster -o table` parancsot. A saját erőforráscsoport és az AKS-fürt nevét használja, szükség szerint.

A kimenet a fürt állapotának alapján:

* Ha a fürt bármely kiépítési állapotban lévő más, *sikeres* vagy *sikertelen*, várja meg, amíg a művelet (*frissítése / frissítése / létrehozásához / méretezés / törlése /áttelepítése*) végződik. Az előző művelet befejezése után próbálja meg újra a legújabb fürtművelet.

* Ha a fürt rendelkezik a sikertelen frissítés, hajtsa végre a lépéseket követve [fogadása a hibákat, a fürt hibás állapotban van és frissítésével vagy a skálázás nem fog működni addig, amíg azt rögzített](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).