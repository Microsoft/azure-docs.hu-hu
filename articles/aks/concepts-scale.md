---
title: Fogalmak – alkalmazások méretezése az Azure Kubernetes Servicesben (ak)
description: Ismerje meg az Azure Kubernetes szolgáltatás (ak) méretezését, beleértve a horizontális Pod automatikus méretezést, a fürt automatikus méretezését és a Azure Container Instances összekötőt.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: f6d2121403361c4225a7e0e64411875d83d93194
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305735"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Alkalmazásskálázási beállítások az Azure Kubernetes Service-ben (AKS)

Az Azure Kubernetes szolgáltatásban (ak) futó alkalmazások futtatása során előfordulhat, hogy a számítási erőforrások mennyiségét meg kell emelnie vagy csökkentenie kell. Ahogy a szükséges alkalmazás-példányok száma, a mögöttes Kubernetes-csomópontok számának módosítására is szükség lehet. Előfordulhat, hogy gyorsan kell kiépíteni egy nagy mennyiségű további alkalmazás-példányt is.

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek segítséget nyújtanak az AK-ban lévő alkalmazások méretezésében:

- [Manuális méretezés](#manually-scale-pods-or-nodes)
- [Vízszintes Pod automéretező (HPA)](#horizontal-pod-autoscaler)
- [Automatikus fürtskálázási eszköz](#cluster-autoscaler)
- [Az Azure Container instance (ACI) integrációja az AK-val](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Hüvelyek vagy csomópontok manuális skálázása

A replikák (hüvelyek) és a csomópontok manuális skálázásával tesztelheti, hogy az alkalmazás hogyan reagáljon a rendelkezésre álló erőforrások és állapot változására. Az erőforrások manuális skálázása azt is lehetővé teszi, hogy meghatározott mennyiségű erőforrást határozzon meg a rögzített költségeket, például a csomópontok számának fenntartásához. A manuális méretezéshez meg kell határoznia a replika vagy a csomópontok darabszámát. A Kubernetes API ezután az adott replika vagy csomópontok száma alapján további hüvelyek vagy kiürítési csomópontok létrehozását ütemezheti.

A csomópontok skálázásakor a Kubernetes API meghívja a megfelelő Azure számítási API-t a fürt által használt számítási típushoz kötve. Például a VM Scale Sets API által meghatározott csomópontok kiválasztásához az VM Scale Setsra épülő fürtök esetében. Ha többet szeretne megtudni arról, hogy a csomópontok hogyan legyenek kiválasztva az eltávolításra, tekintse meg a [VMSS kapcsolatos gyakori kérdéseket](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml#if-i-reduce-my-scale-set-capacity-from-20-to-15--which-vms-are-removed-).

A hüvelyek és a csomópontok manuális méretezésének megkezdéséhez lásd: [alkalmazások méretezése az AK-ban][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>A podok automatikus horizontális skálázási eszköze

A Kubernetes a vízszintes Pod automéretezőt (HPA) használja az erőforrás-igény figyelésére és a replikák számának automatikus méretezésére. Alapértelmezés szerint a vízszintes hüvely autoskálázása 30 másodpercenként ellenőrzi a metrikák API-t a replikák számának szükséges változásaihoz. Ha módosításokra van szükség, a replikák száma ennek megfelelően növekszik vagy csökken. A horizontális Pod autoscaleer olyan AK-fürtökkel működik, amelyek a metrikák kiszolgálóját telepítették a Kubernetes 1.8 +-hoz.

![Kubernetes vízszintes hüvely automatikus skálázása](media/concepts-scale/horizontal-pod-autoscaling.png)

Ha egy adott központi telepítéshez a vízszintes Pod automéretezőt konfigurálja, meghatározza a futtatható replikák minimális és maximális számát. Megadhatja a mérőszámot is, amely a skálázási döntések (például a CPU-használat) figyelésére és kiindulására szolgál.

A horizontálisan az AK-ban lévő autoskálázással kapcsolatos első lépésekhez lásd: [hüvelyek autoskálázása AK-ban][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Skálázási események cooldownja

Mivel a horizontális Pod autoskálázás 30 másodpercenként ellenőrzi a metrikák API-t, előfordulhat, hogy a korábbi méretezési események nem fejeződött be sikeresen, mielőtt egy másik ellenőrzést végez. Ez a viselkedés azt eredményezheti, hogy a vízszintes hüvely automatikusan méretezhető, hogy megváltoztassa a replikák számát, mielőtt az előző méretezési esemény befogadja az alkalmazás munkaterhelését, és az erőforrásnak ennek megfelelően módosítania kell.

A verseny eseményeinek minimalizálásához meg kell határozni egy késleltetési értéket. Ez az érték határozza meg, hogy mennyi ideig kell megvárnia a horizontális Pod automéretezőt egy méretezési esemény után, mielőtt egy másik méretezési eseményt aktiválhat. Ez a viselkedés lehetővé teszi az új replikák számának érvénybe léptetését, és a metrikák API-ját az elosztott munkaterhelés megjelenítéséhez. A [Kubernetes 1,12-as számú vertikális Felskálázási esemény esetében nem](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#support-for-cooldown-delay)áll rendelkezésre késés, azonban a leskálázási események késése alapértelmezés szerint 5 percet vesz igénybe.

Jelenleg nem állíthatók be ezek a hűtési értékek az alapértelmezett értékből.

## <a name="cluster-autoscaler"></a>Automatikus fürtskálázási eszköz

Ahhoz, hogy válaszoljon a Kubernetes módosítására, a fürt automatikusan méretezhető, amely a csomópontok által igényelt számítási erőforrások alapján módosítja a csomópontok számát. Alapértelmezés szerint a fürt autoskálázása 10 másodpercenként ellenőrzi a metrikák API-kiszolgálóját a csomópontok számának szükséges változásaihoz. Ha a fürt autoskálázása meghatározza, hogy szükség van-e módosításra, az AK-fürt csomópontjainak száma ennek megfelelően növekszik vagy csökken. A fürt autoskálázása olyan Kubernetes RBAC-kompatibilis AK-fürtökkel működik, amelyek Kubernetes 1,10. x vagy újabb rendszereken futnak.

![Kubernetes-fürt autoskálázása](media/concepts-scale/cluster-autoscaler.png)

A fürt autoskálázása jellemzően a horizontális Pod autoskálázás mellett használatos. Ha a kombinált, a horizontális Pod autoskálázás növeli vagy csökkenti a hüvelyek számát az alkalmazás igénye alapján, és a fürt automatikusan méretezhető, hogy a további hüvelyek megfelelő futtatásához szükség van-e a csomópontok számának megadására.

A fürt autoskálázási szolgáltatásának az AK-ban való megkezdéséhez lásd: [fürt automéretezője az AK][aks-cluster-autoscaler]-ban.

### <a name="scale-out-events"></a>Kibővíthető események

Ha egy csomópont nem rendelkezik elegendő számítási erőforrással a kért Pod futtatásához, akkor a pod nem haladhat át az ütemezési folyamaton. A pod nem indítható el, ha további számítási erőforrások állnak rendelkezésre a csomópont-készleten belül.

Ha a fürt automéretezője azt észleli, hogy a csomópont-készlet erőforrás-korlátozásai miatt nem lehet ütemezni, a csomópont-készletben lévő csomópontok száma megnő a további számítási erőforrások biztosításához. Ha a további csomópontok üzembe helyezése sikeres volt, és a csomópont-készleten belül elérhetővé válik, a hüvelyek ütemezve lesznek a rajtuk való futtatásra.

Ha az alkalmazásnak gyors skálázásra van szüksége, egyes hüvelyek úgy maradhatnak, hogy ütemezve maradnak, amíg a fürt automéretezője által üzembe helyezett további csomópontok el nem fogadják az ütemezett hüvelyeket. A nagy terhelést igénylő alkalmazások esetében a virtuális csomópontok és a Azure Container Instances is méretezhető.

### <a name="scale-in-events"></a>Méretezés az eseményeken

A fürt autoskálázása azt is figyeli, hogy az új ütemezési kérelmeket még nem fogadó csomópontok Pod ütemezési állapota. Ez a forgatókönyv azt jelzi, hogy a csomópont-készlet több számítási erőforrással rendelkezik, mint amennyi szükséges, és a csomópontok száma csökkenthető.

Egy olyan csomópontot, amely egy küszöbértéket továbbít, és alapértelmezés szerint 10 percen át nem szükséges, a törlésre van ütemezve. Ha ez a helyzet történik, a hüvelyek úgy vannak ütemezve, hogy a csomóponton belüli többi csomóponton fussanak, és a fürt autoskálázása csökkenti a csomópontok számát.

Az alkalmazások bizonyos megszakításokat tapasztalhatnak, mivel a hüvelyek különböző csomópontokon vannak ütemezve, amikor a fürt autoskálázása csökkenti a csomópontok számát. A fennakadások csökkentése érdekében ne használjon egyetlen Pod-példányt használó alkalmazásokat.

## <a name="burst-to-azure-container-instances"></a>Kitört Azure Container Instances

Az AK-fürt gyors méretezéséhez integrálható a Azure Container Instances (ACI) használatával. A Kubernetes beépített összetevőket tartalmaz a replika és a csomópontok számának skálázásához. Ha azonban az alkalmazásnak gyors méretezésre van szüksége, a horizontális Pod automéretező több hüvelyt is ütemezhet, mint amennyit a csomópont-készlet meglévő számítási erőforrásai biztosíthatnak. Ha be van állítva, ez a forgatókönyv ezt követően aktiválja a fürt automéretezőjét további csomópontok üzembe helyezéséhez a csomópont-készletben, de eltarthat néhány percig, amíg ezek a csomópontok sikeresen kiépítik és lehetővé teszik a Kubernetes-ütemező számára a hüvelyek futtatását.

![Kubernetes-méretezés az ACI-ig](media/concepts-scale/burst-scaling.png)

Az ACI lehetővé teszi, hogy gyorsan üzembe helyezzen tároló-példányokat további infrastruktúra-terhelés nélkül. Ha AK-val kapcsolódik, az ACI biztonságos, logikai kiterjesztést biztosít az AK-fürt számára. A virtuális [csomópontok][virtual-nodes-cli] virtuális [Kubelet][virtual-kubelet]alapuló összetevője telepítve van az AK-fürtben, amely az ACI-t virtuális Kubernetes-csomópontként jeleníti meg. A Kubernetes ezután olyan hüvelyeket ütemezhetnek, amelyek a virtuális csomópontokon keresztül ACI-példányként futnak, nem pedig a virtuálisgép-csomópontokon közvetlenül az AK-fürtön lévő hüvelyek.

Az alkalmazás nem igényel módosítást virtuális csomópontok használatához. Az üzembe helyezések az AK-ban és az ACI-ban is méretezhetők, és késedelem nélkül a fürt autoskálázása új csomópontokat helyez üzembe az AK-fürtben.

A virtuális csomópontok egy további alhálózatra vannak telepítve, ugyanabban a virtuális hálózatban, mint az AK-fürt. Ez a virtuális hálózati konfiguráció lehetővé teszi az ACI és az AK közötti adatforgalom védelmét. Az AK-fürtökhöz hasonlóan az ACI-példányok egy biztonságos, logikai számítási erőforrás, amely más felhasználóktól el van különítve.

## <a name="next-steps"></a>Következő lépések

Az alkalmazások méretezésének megkezdéséhez először kövesse a gyors üzembe helyezési lehetőséget, és [hozzon létre egy AK-fürtöt az Azure CLI][aks-quickstart]használatával. Ezután megkezdheti az alkalmazások manuális vagy automatikus méretezését az AK-fürtben:

- [Hüvelyek][aks-manually-scale-pods] vagy [csomópontok][aks-manually-scale-nodes] manuális skálázása
- A [vízszintes hüvely automéretező][aks-hpa] használata
- A [fürt automéretező][aks-cluster-autoscaler] használata

Az alapvető Kubernetes és az AK-fogalmakkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Kubernetes/AK-fürtök és-munkaterhelések][aks-concepts-clusters-workloads]
- [Kubernetes/AK-hozzáférés és-identitás][aks-concepts-identity]
- [Kubernetes/AK biztonság][aks-concepts-security]
- [Kubernetes/AK virtuális hálózatok][aks-concepts-network]
- [Kubernetes/AK-tároló][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md
