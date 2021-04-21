---
title: Az Azure Kubernetes Service-ben támogatott Kubernetes-verziók
description: A Kubernetes-verzió támogatási szabályzatának és a fürtök életciklusának Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/29/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: a46d3e8362d48225e5fec11213bfbff370252d8e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782674"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service-ben (AKS) támogatott Kubernetes-verziók

A Kubernetes-közösség nagyjából három hónaponként ad ki alverziókat. A Kubernetes-közösség a közelmúltban 9 hónapról [12](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/)hónapra növelte az egyes verziók támogatási ablakát az 1.19-es verziótól kezdve. 

Az alverzió-kiadások új funkciókat és fejlesztéseket tartalmaznak. A hibajavítások gyakoribbak (néha hetente), és az alverziók kritikus hibajavítására szolgálnak. A javítás-kiadások tartalmazzák a biztonsági rések vagy a fő hibák javítását.

## <a name="kubernetes-versions"></a>Kubernetes-verziók

A Kubernetes a szabványos [szemantikus](https://semver.org/) verziószámozási verziószámozási sémát használja az egyes verziókhoz:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

A verzióban minden szám általános kompatibilitást jelez az előző verzióval:

* **A főverziók akkor** változnak, ha a nem kompatibilis API-frissítések vagy a visszamenőleges kompatibilitás megszakadhat.
* **Az alverziók** akkor változnak, ha a funkciófrissítések visszamenőlegesen kompatibilisek a többi kisebb kiadással.
* **A javításverziók** a visszamenőlegesen kompatibilis hibajavításokkor változnak.

Törekedj a futtatott alverzió legújabb javítás kiadásának futtatására. Az éles fürt például a következőn van: **`1.17.7`** . **`1.17.8`** A az *1.17-es* sorozathoz elérhető legújabb javításverzió. A lehető leghamarabb frissítsen a verzióra, hogy a fürt teljes mértékben ki legyen **`1.17.8`** javítva és támogatott legyen.

## <a name="kubernetes-version-support-policy"></a>Kubernetes-verzió támogatási szabályzata

Az AKS általánosan elérhető verziót határoz meg olyan verzióként, amely minden SLO- vagy SLA-mérésben engedélyezve van, és minden régióban elérhető. Az AKS a Kubernetes három alverzióját támogatja:

* Az AKS-ben kiadott legújabb GA alverzió (amelyre N-nek fogunk hivatkozni).
* Két korábbi alverzió.
    * Minden támogatott alverzió legfeljebb két (2) stabil javítást támogat.

Az AKS előzetes verziókat is támogathat, amelyek kifejezetten fel vannak címkézve, és amelyekre az előzetes verzió [használati feltételei vonatkoznak.][preview-terms]

> [!NOTE]
> Az AKS biztonságos üzembe helyezési eljárásokat használ, amelyek magukban foglalják a fokozatos régiótelepítést. Ez azt jelenti, hogy akár 10 munkanapot is igénybe vehet, hogy egy új kiadás vagy egy új verzió minden régióban elérhető legyen.

Az AKS-ben a Kubernetes-verziók támogatott ablakát "N-2"-nek nevezik: (N (legújabb kiadás) – 2 (alverziók)).

Ha például az AKS ma bevezeti az *1.17.a* verziót, a következő verziókhoz nyújt támogatást:

Új alverzió    |    Támogatott verziólista
-----------------    |    ----------------------
1.17.a               |    1.17.a, 1.17.b, 1.16.c, 1.16.d, 1.15.e, 1.15.f

Ahol a ".letter" a javításverziókra jellemző.

Egy új alverzió bevezetésekor a támogatott legrégebbi alverzió és javításverzió elavult és el lesz távolítva. Az aktuális támogatott verziólista például a következő:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

Az AKS kiadásának 1.18. verziója 30 nap alatt eltávolítja az \* 1.15-ös \* verziókat.

> [!NOTE]
> Ha az ügyfelek nem támogatott Kubernetes-verziót futtatnak, a rendszer a frissítésre kéri őket, amikor támogatást kérnek a fürthöz. A nem támogatott Kubernetes-kiadásokat futtató fürtökre nem vonatkoznak [az AKS támogatási szabályzatai.](./support-policies.md)

A fentieken kívül az AKS egy  adott alverzió legfeljebb két javítási kiadását támogatja. Így tehát a következő támogatott verziók esetén:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Ha az AKS a és a kiadást tartalmazza, a legrégebbi javításverziók elavultak és el vannak távolítva, és a támogatott verziók listája a következő `1.17.9` `1.16.11` lesz:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>Támogatott `kubectl` verziók

A kube-apiserver verziójához képest egy régebbi vagy újabb alverziót is használhat, amely megfelel a `kubectl` [kubectl Kubernetes támogatási szabályzatának.](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl) 

Ha például a *kube-apiserver* *1.17-es,* akkor használhatja a kube-apiserver 1.16–1.18-as   `kubectl` verzióját. 

A verziójának telepítéséhez vagy frissítéséhez `kubectl` futtassa a(on) `az aks install-cli` programot.

## <a name="release-and-deprecation-process"></a>Kiadási és elalasztási folyamat

Az [AKS Kubernetes](#aks-kubernetes-release-calendar)kiadási naptárában a következő verziókra és elalasztásokra hivatkozhat: .

A  Kubernetes új alverziói esetén:
  * Az AKS legalább 30 nappal az eltávolítás előtt közzétesz egy előzetes bejelentést az [új](https://aka.ms/aks/releasenotes) verzió kiadásának tervezett dátumával és a megfelelő régi verzió elalasztásával.
  * Az AKS [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) riasztást küld a felhasználóknak, ha egy új verzió az elavult API-k miatt problémákat okoz a fürtben. Azure Advisor a rendszer riasztást is küld a felhasználónak, ha jelenleg nem áll rendelkezésre támogatás.
  * Az AKS [](../service-health/service-health-overview.md) egy szolgáltatás állapotával kapcsolatos értesítést tesz közzé, amely minden AKS- és portál-hozzáféréssel rendelkezik, és e-mailt küld az előfizetés-rendszergazdáknak a tervezett verzióeltávolítási dátumokkal.

    > [!NOTE]
    > Ha meg kell tudni, hogy kik az előfizetések rendszergazdái, vagy ha módosítani tudja, tekintse meg az [Azure-előfizetések kezelésével kapcsolatos további részleteket.](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)
    
  * A felhasználóknak **30 napjuk van** a verzió eltávolításától a támogatott alverzió-kiadásra való frissítésig, hogy továbbra is támogatást kapnak.

A Kubernetes **új** javításverziói:
  * A javításverziók sürgős jellege miatt azok azonnal bevezethetőek a szolgáltatásba, amint elérhetővé válnak.
  * Általánosságban elmondható, hogy az AKS nem kommunikál széles körben az új javításverziók kiadását. Az AKS azonban folyamatosan figyeli és ellenőrzi az elérhető CVE-javításokat, hogy időben támogassa őket az AKS-ben. Ha kritikus javítást talál, vagy felhasználói műveletre van szükség, az AKS értesíti a felhasználókat, hogy frissítsenek az újonnan elérhető javításra.
  * A felhasználóknak **30** napja van, hogy eltávolítják a javításokat az AKS-ről, hogy egy támogatott javításra frissítsenek, és továbbra is támogatást kapnak.

### <a name="supported-versions-policy-exceptions"></a>Támogatott verziók házirend-kivételei

Az AKS fenntartja a jogot új/meglévő verziók hozzáadására vagy eltávolítására, amelyek egy vagy több kritikus fontosságú, éles környezetre hatással lévő hibát vagy biztonsági problémát okozhatnak előzetes értesítés nélkül.

A hiba súlyosságától vagy a biztonsági probléma súlyosságától függően bizonyos javítások kiadása kihagyható vagy a bevezetés felgyorsítható.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal és cli-verziók

Amikor AKS-fürtöt helyez üzembe a portálon vagy az Azure CLI-n, a fürt alapértelmezés szerint az N-1 alverziót és a legújabb javítást használja. Ha például az AKS az *1.17.a*, *1.17.b,* *1.16.c,* *1.16.d,* *1.15.e* és *1.15.f* címeket támogatja, a kiválasztott alapértelmezett verzió *az 1.16.c*.

Az az [aks get-versions][az-aks-get-versions] paranccsal kideríthető, hogy jelenleg milyen verziók érhetők el az előfizetéshez és a régióhoz. Az alábbi példa az EastUS régióban elérhető Kubernetes-verziókat *sorolja* fel:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>Az AKS Kubernetes kiadási naptára

A korábbi kiadási előzményekért lásd: [Kubernetes.](https://en.wikipedia.org/wiki/Kubernetes#History)

|  K8s-verzió | Upstream kiadás  | AKS előzetes verzió  | AKS GA  | Életciklus vége |
|--------------|-------------------|--------------|---------|-------------|
| 1.18  | Mar-23-20  | 2020. május   | 2020. aug.  | 1.21 GA | 
| 1.19  | Aug-04-20  | 2020. szeptember   | 2020. nov.  | 1.22 GA | 
| 1.20  | Dec. 08.20.  | 2021. január   | 2021. március  | 1.23 GA |
| 1.21  | Április 08.21. | 2021. május   | 2021. június  | 1.24 GA |



## <a name="faq"></a>GYIK

**Hogyan értesít a Microsoft az új Kubernetes-verziókról?**

Az AKS-csapat előzetes közleményeket tesz közzé az új Kubernetes-verziók tervezett dátumával együtt a dokumentációnkban, a [GitHubon,](https://github.com/Azure/AKS/releases) valamint e-maileket küld az előfizetés-rendszergazdáknak, akiknek a fürtjei nem fognak támogatást kapni.  A közlemények mellett az AKS az [Azure Advisor-t](https://docs.microsoft.com/azure/advisor/advisor-overview) is használja az ügyfél értesítésére az Azure Portalon, hogy értesítse a felhasználókat, ha nincs támogatásuk, valamint riasztja őket az alkalmazást vagy a fejlesztési folyamatot befolyásoló elavult API-król. 

**Milyen gyakran kell a Kubernetes-verziókat frissíteni, hogy támogatásban maradjanak?**

A Kubernetes 1.19-től kezdődően a nyílt forráskódú közösség [1 évre bővítette a támogatást.](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/) Az AKS véglegesítéseket biztosít a javítások engedélyezéséhez és a felfelé irányuló kötelezettségvállalásoknak való megfelelő támogatáshoz. Az 1.19-es vagy újabb verziójú AKS-fürtökön legalább évente egyszer frissítheti a frissítést, hogy a támogatott verzióval maradjon. 

Az 1.18-as vagy újabb verziók esetén a támogatási időkeret 9 hónapig marad, így 9 havonta frissítést igényel a támogatott verzióra való frissítés. Rendszeresen tesztelje az új verziókat, és készüljön fel az újabb verziókra való frissítésre a Kubernetes legújabb stabil fejlesztéseinek rögzítéséhez.

**Mi történik, ha egy felhasználó nem támogatott alverzióval frissít egy Kubernetes-fürtöt?**

Ha az *n-3-as* vagy annál régebbi verziót van frissítve, az azt jelenti, hogy a támogatáson kívül esik, és a rendszer kérni fogja a frissítést. Ha az n-3-as verzióról az n-2-es verzióra való frissítés sikeres, visszatér a támogatási szabályzatainkhoz. Például:

- Ha a legrégebbi támogatott AKS-verzió *1.15.a,* és *az 1.14.b* vagy régebbi verzióval van, akkor a támogatáson kívül esik.
- Ha sikeresen frissít *az 1.14.b-ről* *az 1.15.a-es* vagy újabb verzióra, akkor visszatér a támogatási szabályzatainkhoz.

A visszalépések nem támogatottak.

**Mit jelent a "támogatáson kívüli"?**

A "támogatáson kívüli" kifejezés a következőt jelenti:
* A futtatott verzió a támogatott verziók listáján kívül esik.
* Ha támogatást kér, a rendszer arra fogja kérni, hogy frissítse a fürtöt egy támogatott verzióra, kivéve, ha a verzió elalasztása utáni 30 napos türelmi időszakon belül van. 

Emellett az AKS semmilyen futásidejű vagy egyéb garanciát nem vállal a támogatott verziók listáján kívüli fürtökre.

**Mi történik, ha egy felhasználó olyan alverzióval skáláz egy Kubernetes-fürtöt, amely nem támogatott?**

Az AKS által nem támogatott alverziók esetén a le- vagy felméretezésnek továbbra is működnie kell. Mivel nincsenek szolgáltatásminőségi garanciák, javasoljuk, hogy a frissítéssel hozza vissza a fürtöt a támogatáshoz.

**A felhasználó örökre a Kubernetes-verziót használhatja?**

Ha egy fürt több mint három (3) alverziót nem támogat, és biztonsági kockázatokkal jár, az Azure proaktívan kapcsolatba lép a fürt frissítésével. Ha nem hoz létre további lépéseket, az Azure fenntartja a jogot a fürt automatikus frissítéséhez az Ön nevében.

**Milyen verziót támogat a vezérlősík, ha a csomópontkészlet nem a támogatott AKS-verziók egyikében található?**

A vezérlősíknak az összes csomópontkészlet verzióablakában kell lennie. A vezérlősík vagy csomópontkészletek frissítésével kapcsolatos részletekért keresse fel a csomópontkészletek [frissítésével kapcsolatos dokumentációt.](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)

**Kihagyhatom több AKS-verziót a fürt frissítése során?**

Támogatott AKS-fürt frissítésekkor a Kubernetes alverziói nem hagyhatóak ki. Például a következő frissítések között:
  * *1.12.x*  ->  *1.13.x:* engedélyezett.
  * *1.13.x*  ->  *1.14.x:* engedélyezett.
  * *1.12.x*  ->  *1.14.x:* nem engedélyezett.

Frissítés az *1.12.x*  ->  *1.14.x verzióról:*
1. Frissítsen *az 1.12.x*  ->  *1.13.x verzióról.*
1. Frissítsen *az 1.13.x*  ->  *1.14.x verzióról.*

Több verzió kihagyása csak akkor használhatja, ha nem támogatott verzióról támogatott verzióra frissít. Frissíthet például egy nem támogatott *1.10.x-ről* egy támogatott *1.15.x verzióra.*

**Létrehozhatok új 1.xx.x fürtöt a 30 napos támogatási időszakban?**

Nem. Ha egy verzió elavult/el lett távolítva, nem hozhat létre fürtöt ezzel a verzióval. A változás útjára indulva látni fogja, hogy a régi verzió el lett távolítva a verziólistából. Ez a folyamat a bejelentéstől függően akár két hétig is eltelhet, régiónkénti fokozatosan.

**Egy teljesen elavult verzióban vagyok. Felvehetek új csomópontkészleteket? Vagy frissítenem kell?**

Nem. Az elavult verzió csomópontkészletét nem adjuk hozzá a fürthöz. Új verziójú csomópontkészleteket is hozzáadhat. Ehhez azonban előfordulhat, hogy először frissítenie kell a vezérlősíkot. 

## <a name="next-steps"></a>Következő lépések

A fürt frissítésének mikéntjéhez lásd: Frissítési Azure Kubernetes Service [(AKS-) fürt frissítése.][aks-upgrade]

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az_aks_get_versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
