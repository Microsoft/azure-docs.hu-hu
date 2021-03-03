---
title: Az Azure arc-kompatibilis Kubernetes-ügynökök frissítése
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Vezérlő ügynök frissítései az Azure arc-kompatibilis Kubernetes
keywords: Kubernetes, arc, Azure, K8s, tárolók, ügynök, frissítés
ms.openlocfilehash: 3a5355269db4ccfc91612f661b464556b22c1d35
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662264"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>Az Azure arc-kompatibilis Kubernetes-ügynökök frissítése

Az Azure arc-kompatibilis Kubernetes automatikus frissítést és manuális frissítési képességeket biztosít az ügynökök számára. Ha az automatikus frissítés letiltása beállítást használja, és ehelyett manuális frissítésre támaszkodik, a verzió támogatási szabályzata az ív-ügynökökre és a mögöttes Kubernetes-fürtre is érvényes.

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>Az automatikus frissítés be-és kikapcsolása a fürt Azure-ív csatlakoztatásakor

Az Azure arc-kompatibilis Kubernetes az ügynököket beépített automatikus frissítési képességekkel látja el.

A következő paranccsal csatlakoztathat egy fürtöt az Azure arc szolgáltatáshoz az automatikus frissítés **engedélyezésével**:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

Ha az automatikus frissítés engedélyezve van, az ügynök óránként kérdezi le az Azure-t az ügynökök újabb verziójának rendelkezésre állása érdekében. Ha az ügynök talál egy elérhető újabb verziót, elindítja a Helm Chart frissítést az Azure arc-ügynökök számára.

Az automatikus frissítés letiltásához határozza meg a `--disable-auto-upgrade` paramétert, miközben csatlakoztatja a fürtöt az Azure-ív szolgáltatáshoz. A következő paranccsal csatlakoztathat egy fürtöt az Azure-ívet az automatikus frissítés **letiltásával**:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> Ha azt tervezi, hogy letiltja az automatikus frissítést, tekintse meg az Azure arc-kompatibilis Kubernetes [verziójának támogatási szabályzatát](#version-support-policy) .

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>Automatikus frissítés bekapcsolása be-és kikapcsolása a fürt Azure-ív csatlakoztatása után

Miután összekapcsolta a fürtöt az Azure arc szolgáltatással, az automatikus frissítés funkcióját a `az connectedk8s update` következő módon válthat az paranccsal:

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>Ügynökök manuális frissítése

Ha letiltotta az ügynökök automatikus frissítését, az alábbi parancs futtatásával manuálisan indíthatja el az ügynökök frissítéseit `az connectedk8s upgrade` :

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.0.1
```

Az Azure arc-kompatibilis Kubernetes az ügynökök verziójának szabványos [szemantikai verziószámozási sémáját](https://semver.org/) követi `MAJOR.MINOR.PATCH` . 

A verzióban lévő egyes számok általános kompatibilitást jeleznek az előző verzióval:

* A **főverziók** akkor változnak, ha nem kompatibilis API-frissítések vagy visszamenőleges kompatibilitás is megszakadhat.
* Az **alverziók** akkor változnak, ha a funkciók változásai visszafelé vannak – kompatibilisek más kisebb kiadásokkal.
* A **javítási verziók** megváltoznak, ha visszamenőlegesen kompatibilis hibajavítás történik.

## <a name="version-support-policy"></a>Verzió támogatási szabályzata

A támogatási problémák létrehozásakor az Azure arc-kompatibilis Kubernetes-gyakorlatok a következő verziójú támogatási szabályzatot támogatják:

* Az Azure arc használatára képes Kubernetes-ügynökök "N-2" támogatási ablaka van, ahol az "N" az ügynökök legújabb másodlagos kiadása. 
  * Ha például az Azure arc engedélyezve Kubernetes bevezeti a 0.28. a Today verziót, a 0.28. a, a 0.28. b, a 0.27. c, a 0.27. d, a 0.26. e és a 0.26. f verziókat az Azure arc támogatja.

* Az Azure arc-hoz csatlakozó Kubernetes-fürtök "N-2" támogatási ablakával rendelkeznek, ahol az "N" a [felsőbb rétegbeli Kubernetes](https://github.com/kubernetes/kubernetes/releases)legújabb stabil másodlagos kiadása. 
  * Ha például a Kubernetes a következőt mutatja be: 1,20. a, 1,20. a, 1,20. b, 1.19. c, 1.19. d, 1.18. e és 1.18. f verziók támogatottak.

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>Milyen gyakran érhető el az Azure arc-kompatibilis Kubernetes alverziójának kiadása?

Az Azure arc-kompatibilis Kubernetes-ügynökök egyetlen másodlagos verziója havonta egyszer jelent meg.

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>Mi történik, ha ügynök-verziót vagy Kubernetes-verziót használok a hivatalos támogatási ablakban?

A "támogatáson kívül" kifejezés azt jelenti, hogy a futtatott verziók az ügynökök és a felsőbb rétegbeli Kubernetes-fürtök "N-2" támogatott verzióin kívül esnek. A támogatási probléma folytatásához a rendszer arra kéri, hogy frissítse a fürtöt és az ügynököt egy támogatott verzióra.

## <a name="next-steps"></a>Következő lépések

* [Fürt összekötése az Azure-ív használatával](./connect-cluster.md)
* [Konfigurációk létrehozása az ív használatára képes Kubernetes-fürtön](./use-gitops-connected-cluster.md)
* [A Azure Policy használata a konfigurációk nagy méretekben való alkalmazásához](./use-azure-policy.md)