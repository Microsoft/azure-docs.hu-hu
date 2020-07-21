---
title: Az Azure Kubernetes Service-ben támogatott Kubernetes-verziók
description: Ismerje meg az Azure Kubernetes Service-ben (ak) lévő fürtök Kubernetes-verziójának támogatási szabályzatát és életciklusát
services: container-service
ms.topic: article
ms.date: 07/08/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 019ae80020dafb54f2c06dd504797f21069914ae
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507063"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service-ben (AKS) támogatott Kubernetes-verziók

A Kubernetes-Közösség nagyjából három havonta kibocsátja a kisebb verziókat. Ezek a kiadások új funkciókat és fejlesztési lehetőségeket tartalmaznak. A javítási kiadások gyakoribbak (esetenként hetente), és csak a kritikus hibajavítások számára készültek egy kisebb verzióban. Ezek a javítások a biztonsági rések vagy a nagyobb hibák javítását is tartalmazzák.

## <a name="kubernetes-versions"></a>Kubernetes-verziók

A Kubernetes a szabványos [szemantikai verziószámozási](https://semver.org/) sémát használja, ami azt jelenti, hogy a Kubernetes minden egyes verziója a következő számozási sémát követi:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

A verzióban lévő egyes számok általános kompatibilitást jeleznek az előző verzióval:

* A főverziók akkor változnak, ha a nem kompatibilis API-változások vagy a visszamenőleges kompatibilitás megszakad.
* Az alverziók akkor változnak, ha a funkcionalitás változása visszamenőlegesen kompatibilis a többi másodlagos kiadással.
* A javítási verziók megváltoznak, ha visszamenőlegesen kompatibilis hibajavítás történik.

A felhasználóknak törekedniük kell arra, hogy futtassák a futtatott alverzió legújabb kiadását, például ha az üzemi fürt be van kapcsolva, **`1.17.7`** és az **`1.17.8`** *1,17* sorozat legújabb elérhető javítási verziója, a lehető leghamarabb frissítenie kell **`1.17.8`** , hogy a fürt teljes mértékben kijavított és támogatott legyen.

## <a name="kubernetes-version-support-policy"></a>Kubernetes-verzió támogatási szabályzata

Az AK általánosan elérhető verziót határoz meg, amely minden SLO-vagy SLA-mértékben engedélyezve van, és ha minden régióban elérhető. Az AK támogatja a Kubernetes három GA-moll verzióját:

* A legújabb GA alverzió, amely az AK-ban jelent meg (amit N-ként fogunk hivatkozni). 
* Két korábbi másodlagos verzió. 
* A támogatott alverziók mindegyike legfeljebb két (2) stabil javítást is támogat.
* Az AK is támogathatja az előzetes verziókat, amelyek explicit módon címkével rendelkeznek, és az [előzetes verziójú használati][preview-terms]feltételeket is felhasználhatják.

> [!NOTE]
> Az AK olyan biztonságos üzembe helyezési eljárásokat használ, amelyek fokozatos régió üzembe helyezését igénylik. Ez azt jelenti, hogy akár 10 munkanapot is igénybe vehet, ha új kiadást vagy új verziót kíván elérhetővé tenni minden régióban.

Az "N-2" néven ismert Kubernetes-verziók támogatott ablaka: (N (legújabb kiadás) – 2 (alverzió)).

Ha például az AK a *1.17. a* mai napon bevezeti a támogatást, a következő verziókhoz nyújtunk támogatást:

Új alverzió    |    Támogatott verziók listája
-----------------    |    ----------------------
1.17. a               |    1.17. a, 1.17. b, 1.16. c, 1.16. d, 1.15. e, 1.15. f

Ahol a ". Letter" a javítási verziókra jellemző.

Új alverzió bevezetését követően a legrégebbi alverzió és a javítások támogatott kiadásai elavultak és törlődnek. Ha például az aktuálisan támogatott verziók listája a következő:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

És az AK 1,18-es kiadása, az azt \* jelenti, hogy az összes 1,15. \* verzió el lesz távolítva, és 30 napon belül nem lesz támogatott.

> [!NOTE]
> Vegye figyelembe, hogy ha az ügyfelek nem támogatott Kubernetes-verziót futtatnak, a rendszer a fürt támogatásának kérelmezése után a frissítésre kéri őket. A nem támogatott Kubernetes-kiadásokat futtató fürtöket az [AK-támogatási szabályzatok](./support-policies.md)nem tartalmazzák.

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

## <a name="release-and-deprecation-process"></a>Kiadási és elavult folyamat

A következő verziókra vonatkozó kiadásokat és elavultkat az [AK Kubernetes kiadási naptárban](#aks-kubernetes-release-calendar)is hivatkozhat.

A Kubernetes új **másodlagos** verzióihoz
1. Az AK egy előzetes bejelentést tesz közzé egy új verzió kiadásának tervezett dátumával, és a korábbi verziókban való elavult verziót az [AK kibocsátási megjegyzésekben](https://aka.ms/aks/releasenotes) legalább 30 nappal az Eltávolítás előtt.
2. Az AK a [szolgáltatás állapotáról szóló értesítést](../service-health/service-health-overview.md) tesz közzé az AK-val és a portál elérésével rendelkező összes felhasználó számára, és e-mailt küld az előfizetési rendszergazdáknak a tervezett verzió-eltávolítási dátumokkal.
3. A felhasználók a verzió eltávolítása után **30 nappal** frissíthetnek a támogatott alverzióra, hogy továbbra is megkapják a támogatást.

A Kubernetes új **javítócsomag** -verzióihoz
  * A javítási verziók sürgős jellegéből adódóan ezek a szolgáltatások elérhetővé válnak a szolgáltatásban.
  * Az AK általában nem tesz széles körű kommunikációt az új javítási verziók kiadásához. Az AK-ban azonban folyamatosan figyeli és ellenőrzi az elérhető CVE-javításokat, hogy az AK-ban időben támogassa őket. Ha a rendszer kritikus javítást talál, vagy felhasználói beavatkozásra van szükség, az AK értesíti a felhasználókat, hogy frissítsen az újonnan elérhető javításra.
  * A felhasználók **30 napon** belül elvesznek egy javítási kiadást az AK-ból egy támogatott javításba való frissítéshez és a támogatás fogadásának folytatásához.

### <a name="supported-versions-policy-exceptions"></a>Támogatott verziók házirend-kivételei

Az AK fenntartja a jogot arra, hogy új/meglévő verziókat adjon hozzá vagy távolítson el, amelyek egy vagy több kritikus termeléssel kapcsolatos hibával vagy biztonsági problémával rendelkeznek, előzetes értesítés nélkül.

Előfordulhat, hogy a rendszer kihagyja az adott javítási kiadásokat, vagy a hiba vagy a biztonsági probléma súlyossága függvényében felgyorsítja a bevezetést.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal és CLI-verziók

Ha AK-fürtöt telepít a portálon vagy az Azure CLI-vel, a fürt alapértelmezett értéke az N-1 alverzió és a legújabb javítás. Ha például az AK támogatja a következőt: *1.17. a*, *1.17. b*, *1.16. c*, *1.16. d*, *1.15. e*és *1.15. f*, a kiválasztott alapértelmezett verzió az *1.16. c*.

Ha szeretné megtudni, hogy az előfizetéshez és a régióhoz jelenleg milyen verziók érhetők el, használja az az [AK Get-Versions][az-aks-get-versions] parancsot. A következő példa a *EastUS* régió elérhető Kubernetes-verzióit sorolja fel:

```azurecli-interactive
az aks get-versions --location eastus --output table
```


## <a name="aks-kubernetes-release-calendar"></a>AK Kubernetes kiadási naptár

A korábbi kiadási előzményekért lásd [itt](https://en.wikipedia.org/wiki/Kubernetes#History).

|  K8s verziója | Felsőbb rétegbeli kiadás  | AK – előzetes verzió  | AK-GA  | Elhasználódott |
|--------------|-------------------|--------------|---------|-------------|
| 1,17  | Dec-09-19  | Január 19.   | 20. július  | 1,20 GA | 
| 1,18  | Mar-23-20  | Május 20   | Augusztus 20  | 1,21 GA | 
| 1,19  | Aug-04-20  | Augusztus 20   | Nov. 20.  | 1,22 GA | 
| 1,20  | * November 20    | * Dec. 20   | * Jan 21  | 1,23 GA | 

\*A felsőbb rétegbeli kiadási dátum megerősítése folyamatban van.

## <a name="faq"></a>GYIK

**Mi történik, ha egy felhasználó olyan alverzióval frissít egy Kubernetes-fürtöt, amely nem támogatott?**

Ha az *n-3* vagy régebbi verzióját használja, az azt jelenti, hogy a támogatáson kívül esik, és a rendszer a frissítésre kéri. Ha az n-3 verzióról n-2-ra történő verziófrissítés sikeres, akkor a támogatási szabályzatunk keretében visszakerül. Például:

- Ha a legrégebbi támogatott AK-verzió *1,15. a* , és Ön a *1,14. b* vagy régebbi verziót használja, a támogatáson kívül van.
- Ha a *1,14. b* verzióról *1.15* -re vagy annál nagyobbra történő frissítés sikeres, a támogatási szabályzatunk keretében visszakerül.

A visszalépések nem támogatottak.

**Mit jelent a "támogatáson kívül" kifejezés**

A "támogatáson kívül" kifejezés azt jelenti, hogy a futtatott verzió a támogatott verziók listáján kívül esik, és a támogatás kérelmezése után a rendszer arra kéri, hogy frissítse a fürtöt egy támogatott verzióra, hacsak nem a 30 napos türelmi időszakon belül van. Emellett az AK nem végez futtatókörnyezetet vagy egyéb garanciát a támogatott verziók listáján kívüli fürtökre.

**Mi történik, ha egy felhasználó olyan alverzióval méretezi a Kubernetes-fürtöt, amely nem támogatott?**

Az AK által nem támogatott alverziók esetén a méretezés be-és kilépésének továbbra is működnie kell, de nincs minőségi garancia a szolgáltatásra, ezért erősen ajánlott frissíteni, hogy a fürt támogassa a támogatást.

**A felhasználó örökre Kubernetes-verzióban maradhat?**

Ha egy fürtön több mint három (3) alverzió támogatott, és biztonsági kockázatokkal jár, az Azure kapcsolatba lép Önnel, hogy proaktív módon frissítse a fürtöt. Ha nem hajt végre további műveletet, az Azure fenntartja a jogot, hogy automatikusan frissítse a fürtöt az Ön nevében.

**A vezérlő síkja milyen verziójú támogatást nyújt, ha a csomópont-készlet nem szerepel a támogatott AK-verziók egyikében sem?**

A vezérlési síkon az összes csomópont-készlet verziójának egy ablakában kell lennie. A vezérlő síkja vagy a csomópont-készletek frissítésével kapcsolatos részletekért látogasson el a [Node-készletek frissítésének](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)dokumentációjában.

**Kihagyhatok egy verziót a Verziófrissítéskor?**

Nem, a kubernetes ajánlott eljárásainak követése után az AK csak a következő javítás vagy másodlagos verzió frissítését teszi lehetővé. A Azure Portal csak azokat a verziókat jeleníti meg, amelyekre frissíthet, és a parancssori felületen futtathatja `az aks get-upgrades -n MyAKSCluster -g MyResourceGroup` az aktuális verzióban elérhető frissítéseket.

**Hogyan frissíthetek egy támogatott verzióra, ha a legújabb támogatott verzió mögött több verzió is van?**

Ha továbbra is támogatni kívánja a támogatást, el kell kerülnie, hogy a jelenleg támogatott listán több verzió maradjon, de ha ebben az esetben az AK-t használja, a frissítés a minimális támogatott verzióra is engedélyezett lesz.

## <a name="next-steps"></a>Következő lépések

A fürt frissítésével kapcsolatos további információkért lásd: [Azure Kubernetes Service (ak) fürt frissítése][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
