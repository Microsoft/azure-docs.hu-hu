---
title: Az Azure Kubernetes Service-ben támogatott Kubernetes-verziók
description: Ismerje meg az Azure Kubernetes Service-ben (ak) lévő fürtök Kubernetes-verziójának támogatási szabályzatát és életciklusát
services: container-service
ms.topic: article
ms.date: 03/29/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: ba75e11a067a257c659f8c659f68bb2bba6fa2e0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012086"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service-ben (AKS) támogatott Kubernetes-verziók

A Kubernetes-Közösség nagyjából három havonta kibocsátja a kisebb verziókat. A közelmúltban a Kubernetes Közösség [9 hónap és 12 hónap között megnövelte a támogatási időszakot](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/), a 1,19-es verziótól kezdődően. 

Az alverziók kiadásai új funkciókat és fejlesztési lehetőségeket tartalmaznak. A javítási kiadások gyakoribbak (esetenként hetente), és a kritikus hibajavítások egy alverzión belül vannak. A javítási kiadások közé tartoznak a biztonsági rések vagy a súlyos hibák javításai.

## <a name="kubernetes-versions"></a>Kubernetes-verziók

A Kubernetes a szabványos [szemantikai](https://semver.org/) verziószámozási sémát használja az egyes verziókhoz:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

A verzióban lévő egyes számok általános kompatibilitást jeleznek az előző verzióval:

* A **főverziók** akkor változnak, ha inkompatibilis API-frissítéseket vagy visszamenőleges kompatibilitást lehet megszakítani.
* Az **alverziók** akkor változnak, ha a funkciók frissítése olyankor történik, amely visszafelé kompatibilis a többi másodlagos kiadással.
* A **javítási verziók** megváltoznak, ha visszamenőlegesen kompatibilis hibajavítás történik.

Célozza meg a futtatott alverzió legújabb javítócsomagjának futtatását. Az üzemi fürt például be van kapcsolva **`1.17.7`** . **`1.17.8`** elérhető a *1,17* sorozat legújabb elérhető javítási verziója. A lehető leghamarabb frissítenie kell a-t, **`1.17.8`** hogy a fürt teljes mértékben kijavított és támogatott legyen.

## <a name="kubernetes-version-support-policy"></a>Kubernetes-verzió támogatási szabályzata

Az AK egy általánosan elérhető verziót határoz meg, amely minden SLO-vagy SLA-mérésben engedélyezve van, és minden régióban elérhető. Az AK támogatja a Kubernetes három GA-moll verzióját:

* A legújabb GA alverzió, amely az AK-ban jelent meg (amit N-ként fogunk hivatkozni).
* Két korábbi másodlagos verzió.
    * A támogatott alverziók mindegyike legfeljebb két (2) stabil javítást is támogat.

Az AK is támogathatja az előzetes verziókat, amelyek explicit módon címkével rendelkeznek, és az [előzetes verziójú használati][preview-terms]feltételeket is felhasználhatják.

> [!NOTE]
> Az AK olyan biztonságos üzembe helyezési eljárásokat használ, amelyek fokozatos régió üzembe helyezését igénylik. Ez azt jelenti, hogy akár 10 munkanapot is igénybe vehet, ha új kiadást vagy új verziót kíván elérhetővé tenni minden régióban.

Az "N-2" néven ismert Kubernetes-verziók támogatott ablaka: (N (legújabb kiadás) – 2 (alverzió)).

Ha például az AK a *1.17. a* mai napon bevezeti a támogatást, a következő verziókhoz nyújtunk támogatást:

Új alverzió    |    Támogatott verziók listája
-----------------    |    ----------------------
1.17. a               |    1.17. a, 1.17. b, 1.16. c, 1.16. d, 1.15. e, 1.15. f

Ahol a ". Letter" a javítási verziókra jellemző.

Új alverzió bevezetését követően a legrégebbi alverzió és a javítások támogatott kiadásai elavultak és törlődnek. A jelenleg támogatott verziók listája például a következő:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AK 1,18-es kiadást bocsát \* ki, és 30 nap alatt eltávolítja az összes 1,15. \* verziót.

> [!NOTE]
> Ha az ügyfelek nem támogatott Kubernetes-verziót futtatnak, a rendszer a fürt támogatásának kérelmezése után kéri a frissítést. A nem támogatott Kubernetes-kiadásokat futtató fürtöket az [AK-támogatási szabályzatok](./support-policies.md)nem tartalmazzák.

A fentiek mellett az AK a megadott alverzióban legfeljebb két **javítási** kiadást támogat. Így a következő támogatott verziók szerepelnek:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Ha az AK-kiadások `1.17.9` és `1.16.11` a, a legrégebbi javítási verziók elavultak és törlődnek, és a támogatott verziók listája a következő lesz:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>Támogatott `kubectl` verziók

A `kubectl` *Kube-apiserver* verziójához viszonyítva egy régebbi vagy újabb verziót is használhat, amely összhangban van a [Kubectl-hez készült Kubernetes-támogatási szabályzattal](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl).

Ha például a *Kube-apiserver* értéke *1,17*, akkor a *1,18* *1,16* -es verzióit használhatja a következővel: `kubectl` *Kube-apiserver*.

A verziójának telepítéséhez vagy frissítéséhez futtassa a következőt: `kubectl` `az aks install-cli` .

## <a name="release-and-deprecation-process"></a>Kiadási és elavult folyamat

A következő verziókra vonatkozó kiadásokat és elavultkat az [AK Kubernetes kiadási naptárban](#aks-kubernetes-release-calendar)is hivatkozhat.

A Kubernetes új **másodlagos** verzióihoz:
  * Az AK egy előzetes bejelentést tesz közzé egy új verzió kiadásának tervezett dátumával, és a korábbi verziókban való elavult verziót az [AK kibocsátási megjegyzésekben](https://aka.ms/aks/releasenotes) legalább 30 nappal az Eltávolítás előtt.
  * Az AK a [szolgáltatás állapotáról szóló értesítést](../service-health/service-health-overview.md) tesz közzé az AK-val és a portál elérésével rendelkező összes felhasználó számára, és e-mailt küld az előfizetési rendszergazdáknak a tervezett verzió-eltávolítási dátumokkal.

    ````
    To find out who is your subscription administrators or to change it, please refer to [manage Azure subscriptions](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator).
    ````
  * A felhasználók a verzió eltávolítása után **30 nappal** frissíthetnek a támogatott alverzióra, hogy továbbra is megkapják a támogatást.

A Kubernetes új **javítócsomag** -verzióihoz:
  * A javítási verziók sürgős jellegéből adódóan a szolgáltatásban elérhetővé válnak.
  * Az AK általában nem közli az új javítási verziók kiadását. Az AK-ban azonban folyamatosan figyeli és ellenőrzi az elérhető CVE-javításokat, hogy az AK-ban időben támogassa őket. Ha a rendszer kritikus javítást talál, vagy felhasználói beavatkozásra van szükség, az AK értesíti a felhasználókat, hogy frissítsen az újonnan elérhető javításra.
  * A felhasználók **30 napon** belül elvégezték a javítási kiadás eltávolítását az AK-ból, hogy egy támogatott javításba frissítsen, és továbbra is megkapja a támogatást.

### <a name="supported-versions-policy-exceptions"></a>Támogatott verziók házirend-kivételei

Az AK fenntartja a jogot arra, hogy új/meglévő verziókat adjon hozzá vagy távolítson el egy vagy több kritikus termelési hatású hibával vagy biztonsági problémával, előzetes értesítés nélkül.

A hibajavítás vagy a biztonsági probléma súlyossága alapján a javítási kiadások kihagyhatók vagy bekerülhetnek a gyorsított állapotba.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal és CLI-verziók

Ha AK-fürtöt telepít a portálon vagy az Azure CLI-vel, a fürt alapértelmezett értéke az N-1 alverzió és a legújabb javítás. Ha például az AK támogatja a következőt: *1.17. a*, *1.17. b*, *1.16. c*, *1.16. d*, *1.15. e* és *1.15. f*, a kiválasztott alapértelmezett verzió az *1.16. c*.

Ha szeretné megtudni, hogy az előfizetéshez és a régióhoz jelenleg milyen verziók érhetők el, használja az az [AK Get-Versions][az-aks-get-versions] parancsot. A következő példa a *EastUS* régió elérhető Kubernetes-verzióit sorolja fel:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>AK Kubernetes kiadási naptár

A korábbi kiadási előzményekért lásd: [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History).

|  K8s verziója | Felsőbb rétegbeli kiadás  | AK – előzetes verzió  | AK-GA  | Elhasználódott |
|--------------|-------------------|--------------|---------|-------------|
| 1,17  | Dec-09-19  | Jan 2019   | Jul 2020  | 1,20 GA | 
| 1,18  | Mar-23-20  | 2020. május   | Augusztus 2020  | 1,21 GA | 
| 1,19  | Aug-04-20  | Sep 2020   | November 2020  | 1,22 GA | 
| 1.20  | Dec-08-20  | Jan 2021   | Mar 2021  | 1,23 GA |
| 1,21  | Apr-08-21 * | 2021. május   | Június 2021  | 1,24 GA |

\* A Kubernetes 1,21 felsőbb rétegbeli kiadás a felsőbb rétegbeli naptárban még a véglegesítésig változhat.


## <a name="faq"></a>GYIK

**Milyen gyakran várható a Kubernetes-verziók frissítése a támogatásban?**

A Kubernetes 1,19-től kezdődően a [nyílt forráskódú közösség 1 évre kibővített támogatást](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/)nyújt. Az AK véglegesíti a javításokat, és támogatja a felsőbb rétegbeli kötelezettségvállalásoknak megfelelő támogatást. A 1,19-es és annál újabb AK-fürtök esetében évente egyszer frissíthető, hogy egy támogatott verzióban maradjon. 

A 1,18-es vagy alacsonyabb verziójú verziók esetében a támogatási ablak 9 hónapon belül marad, és 9 havonta egyszer kell frissíteni, hogy egy támogatott verzió maradjon. Rendszeresen tesztelheti az új verziókat, és készen áll az újabb verzióra való frissítésre, hogy rögzítse a legújabb stabil fejlesztéseket a Kubernetes belül.

**Mi történik, ha egy felhasználó olyan alverzióval frissít egy Kubernetes-fürtöt, amely nem támogatott?**

Ha az *n-3* vagy régebbi verzióját használja, az azt jelenti, hogy a támogatáson kívül esik, és a rendszer a frissítésre kéri. Ha az n-3 verzióról n-2-ra történő verziófrissítés sikeres, akkor a támogatási szabályzatunk keretében visszakerül. Például:

- Ha a legrégebbi támogatott AK-verzió *1,15. a* , és Ön a *1,14. b* vagy régebbi verziót használja, a támogatáson kívül van.
- Ha a *1.14. b* -ről a- *re való frissítés* sikeresen megtörtént, akkor a támogatási szabályzatunk keretében visszakerül.

A visszalépések nem támogatottak.

**Mit jelent a "támogatáson kívül" kifejezés**

A "támogatáson kívül" az alábbiakat jelenti:
* A futtatott verzió kívül esik a támogatott verziók listáján.
* A rendszer a támogatás kérelmezése után a fürt frissítését egy támogatott verzióra kéri, ha a verzió elavulttá tételét követő 30 napos türelmi időszakon belül van. 

Emellett az AK nem végez futtatókörnyezetet vagy egyéb garanciát a támogatott verziók listáján kívüli fürtökre.

**Mi történik, ha egy felhasználó olyan alverzióval méretezi a Kubernetes-fürtöt, amely nem támogatott?**

Az AK által nem támogatott alverziók esetén a méretezés be-és kilépésének továbbra is működnie kell. Mivel a szolgáltatáshoz nem biztosítunk minőségi garanciát, javasoljuk, hogy frissítsen a fürt támogatásához.

**A felhasználó örökre Kubernetes-verzióban maradhat?**

Ha egy fürt több mint három (3) alverzión már nem támogatott, és biztonsági kockázatokkal jár, az Azure proaktív módon kapcsolatba lép Önnel a fürt frissítésével. Ha nem hajt végre további műveletet, az Azure fenntartja a jogot, hogy automatikusan frissítse a fürtöt az Ön nevében.

**A vezérlő síkja milyen verziójú támogatást nyújt, ha a csomópont-készlet nem szerepel a támogatott AK-verziók egyikében sem?**

A vezérlési síkon az összes csomópont-készlet verziójának egy ablakában kell lennie. A vezérlő síkja vagy a csomópont-készletek frissítésével kapcsolatos részletekért látogasson el a [Node-készletek frissítésének](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)dokumentációjában.

**Ki lehet hagyni több AK-verziót a fürt frissítése során?**

Ha egy támogatott AK-fürtöt frissít, a Kubernetes alverzióit nem lehet kihagyni. Például a következő frissítések között:
  * *1.12. x*  ->  *1.13. x*: engedélyezve.
  * *1.13. x*  ->  *1.14. x*: engedélyezve.
  * *1.12. x*  ->  *1.14. x*: nem engedélyezett.

A *1.12. x*  ->  *1.14. x* verzióról történő frissítéshez:
1. Frissítés *1.12. x*  ->  *1.13. x* verzióról.
1. Frissítsen a *1.13. x*  ->  *1.14. x* verzióról.

Több verzió kihagyása csak akkor végezhető el, ha nem támogatott verzióról frissít vissza egy támogatott verzióra. Frissíthet például egy nem támogatott *1.10. x* verzióról egy támogatott *1.15. x*-re.

**Létrehozhatok új 1. xx. x fürtöt a 30 napos támogatási időszakban?**

Nem. Ha egy verzió elavult/el lett távolítva, nem hozhat létre fürtöt az adott verzióval. A váltás során a rendszer elkezdi megtekinteni a régi verziót a verziók listájáról. Ez a folyamat akár két hetet is igénybe vehet a bejelentésből, fokozatosan régiónként.

**Frissen elavult verzióban vagyok, továbbra is Hozzáadhatok új csomópont-készleteket? Vagy frissíteni kell?**

Nem. Az elavult verzió csomópont-készletei nem adhatók hozzá a fürthöz. Új verziójú csomópont-készleteket is hozzáadhat. Előfordulhat azonban, hogy először frissítenie kell a vezérlő síkot. 

## <a name="next-steps"></a>Következő lépések

A fürt frissítésével kapcsolatos további információkért lásd: [Azure Kubernetes Service (ak) fürt frissítése][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
