---
title: Fogalmak – biztonság az Azure Kubernetes Servicesben (ak)
description: Ismerje meg az Azure Kubernetes szolgáltatással (ak) kapcsolatos biztonságot, beleértve a Master és Node kommunikációt, a hálózati házirendeket és a Kubernetes titkokat.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: mlearned
ms.openlocfilehash: 6c69e46ea3510476089cd932b1cd1bdf14254021
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122374"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (AKS) alkalmazásainak és fürtjeinek biztonsági fogalmai

Ha az alkalmazás számítási feladatait az Azure Kubernetes szolgáltatásban (ak) futtatja, a fürt biztonsága kulcsfontosságú szempont. A Kubernetes olyan biztonsági összetevőket tartalmaz, mint például a *hálózati házirendek* és a *titkos kulcsok*. Az Azure ezután olyan összetevőket hoz létre, mint például a hálózati biztonsági csoportok és a előkészített fürtök frissítése. Ezek a biztonsági összetevők a legújabb operációs rendszer biztonsági frissítéseit és Kubernetes, valamint a biztonságos Pod-forgalmat és a bizalmas hitelesítő adatokhoz való hozzáférést biztosító AK-fürt megtartására szolgálnak.

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyekkel biztonságossá teheti alkalmazásait az AK-ban:

- [Az Azure Kubernetes Service (AKS) alkalmazásainak és fürtjeinek biztonsági fogalmai](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Fő biztonság](#master-security)
  - [Csomópont biztonsága](#node-security)
    - [Számítási elkülönítés](#compute-isolation)
  - [Fürt frissítései](#cluster-upgrades)
    - [Cordon és Drain](#cordon-and-drain)
  - [Hálózati biztonság](#network-security)
    - [Azure-beli hálózati biztonsági csoportok](#azure-network-security-groups)
  - [Kubernetes titkai](#kubernetes-secrets)
  - [Következő lépések](#next-steps)

## <a name="master-security"></a>Fő biztonság

Az AK-ban a Kubernetes fő összetevői a Microsoft által biztosított felügyelt szolgáltatás részét képezik. Mindegyik AK-fürt saját, egyetlen bérlős, dedikált Kubernetes-főkiszolgálóval rendelkezik az API-kiszolgáló, a ütemező stb. biztosításához. Ezt a főkiszolgálót a Microsoft felügyeli és tartja karban.

Alapértelmezés szerint a Kubernetes API-kiszolgáló egy nyilvános IP-címet és egy teljesen minősített tartománynevet (FQDN) használ. Az API-kiszolgáló végpontjának hozzáférését az [engedélyezett IP-címtartományok][authorized-ip-ranges]alapján korlátozhatja. Létrehozhat egy teljesen [privát fürtöt][private-clusters] is az API-kiszolgáló virtuális hálózathoz való hozzáférésének korlátozásához.

Az API-kiszolgáló hozzáférését az Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) és az Azure RBAC használatával szabályozhatja. További információ: [Azure ad-integráció az AK][aks-aad]-nal.

## <a name="node-security"></a>Csomópont biztonsága

Az AK-csomópontok az Ön által kezelt és karbantartott Azure-beli virtuális gépek. A Linux-csomópontok optimalizált Ubuntu-disztribúciót futtatnak a `containerd` vagy a Moby Container Runtime használatával. A Windows Server-csomópontok optimalizált Windows Server 2019 kiadást futtatnak, és a `containerd` vagy a Moby Container futtatókörnyezetet is használják. Egy AK-fürt létrehozásakor vagy skálázásakor a rendszer automatikusan telepíti a csomópontokat a legújabb biztonsági frissítésekkel és konfigurációkkal.

> [!NOTE]
> Az Kubernetes 1,19-es verziójú csomópont-készleteket és a tároló futtatókörnyezetének nagyobb használatát használó AK-fürtök `containerd` . Az Kubernetes-t használó, a csomópontok előtt a v 1.19-t használó-fürtök a [Moby](https://mobyproject.org/) (felsőbb rétegbeli Docker) tároló-futtatókörnyezetet használják.

Az Azure platform automatikusan alkalmazza az operációs rendszer biztonsági javításait Linux-csomópontokra. Ha a Linux operációs rendszer biztonsági frissítése a gazdagép újraindítását igényli, a rendszer nem hajtja végre automatikusan az újraindítást. A Linux-csomópontokat manuálisan is újraindíthatja, vagy közös megközelítéssel használhatja a [Kured][kured], egy nyílt forráskódú újraindítási démont a Kubernetes. A Kured [daemonset elemet][aks-daemonsets] fut, és figyeli az egyes csomópontokat egy olyan fájl jelenléte esetén, amely azt jelzi, hogy újraindítás szükséges. A rendszer az újraindításokat a fürtön keresztül felügyeli a fürt frissítésével megegyező [Kordon és kiürítési folyamat](#cordon-and-drain) használatával.

A Windows Server-csomópontok esetében Windows Update nem fut automatikusan, és nem alkalmazza a legújabb frissítéseket. A Windows Update kiadási ciklus és a saját ellenőrzési folyamata körüli rendszeres ütemterv esetén frissíteni kell a Windows Server Node-készlet (ek) et az AK-fürtben. Ez a frissítési folyamat olyan csomópontokat hoz létre, amelyek a legújabb Windows Server-lemezképet és-javításokat futtatják, majd eltávolítja a régebbi csomópontokat. További információ erről a folyamatról: [Node-készlet frissítése az AK-ban][nodepool-upgrade].

A csomópontok magánhálózati virtuális hálózati alhálózatba vannak telepítve, és nincsenek hozzárendelve nyilvános IP-címek. Hibaelhárítási és felügyeleti célokra az SSH alapértelmezés szerint engedélyezve van. Ez az SSH-hozzáférés csak a belső IP-cím használatával érhető el.

A tárolók biztosításához a csomópontok az Azure Managed Disks használják. A virtuálisgép-csomópontok többségének mérete esetén ezek a nagy teljesítményű SSD-k által támogatott prémium lemezek. A felügyelt lemezeken tárolt adatok automatikusan titkosítva maradnak az Azure platformon belül. A redundancia javítása érdekében ezeket a lemezeket az Azure-adatközpontban is biztonságosan replikálja a rendszer.

A Kubernetes-környezetek (ak-ban vagy máshol) jelenleg nem teljesen biztonságosak az ellenséges, több-bérlős használatra. További biztonsági funkciók, például a *Pod biztonsági szabályzatok*, vagy a csomópontok részletes Kubernetes szerepköralapú hozzáférés-vezérlése (Kubernetes RBAC), nehezebbé teszi a kihasználat. Azonban az ellenséges, több-bérlős számítási feladatok futtatásakor a megfelelő biztonság érdekében a hypervisor az egyetlen biztonsági szint, amelyet megbízhatónak tart. A Kubernetes biztonsági tartománya a teljes fürtvé válik, nem önálló csomópontként. Az ilyen típusú ellenséges több-bérlős munkaterhelések esetében fizikailag elkülönített fürtöket kell használnia. A számítási feladatok elkülönítésének módjaival kapcsolatos további információkért lásd: [ajánlott eljárások a fürtök elkülönítéséhez az AK-ban][cluster-isolation].

### <a name="compute-isolation"></a>Számítási elkülönítés

 Bizonyos munkaterhelések esetén a megfelelőségi vagy szabályozási követelmények miatt a többi ügyfél-munkaterheléstől való nagyfokú elkülönítésre lehet szükség. Ezekben a számítási feladatokban az Azure [elkülönített virtuális gépeket](../virtual-machines/isolation.md)biztosít, amelyek egy AK-fürt ügynök-csomópontjaiként használhatók. Ezek az elkülönített virtuális gépek egy adott hardver-típushoz vannak elkülönítve, és egyetlen ügyfél számára vannak kijelölve. 

 Ha ezeket az elkülönített virtuális gépeket egy AK-fürttel szeretné használni, válassza ki az [itt](../virtual-machines/isolation.md) felsorolt elkülönített virtuálisgép-méretek egyikét a **csomópont MÉRETEként** az AK-fürt létrehozásakor vagy egy csomópont-készlet hozzáadásakor.


## <a name="cluster-upgrades"></a>Fürt frissítései

A biztonság és a megfelelőség szempontjából, illetve a legújabb funkciók használatához az Azure eszközöket biztosít az AK-fürtök és-összetevők frissítésének előkészítéséhez. Ez a frissítési előkészítés magában foglalja a Kubernetes fő-és ügynök-összetevőit is. A [rendelkezésre álló Kubernetes-verziók listáját](supported-kubernetes-versions.md) az AK-fürthöz tekintheti meg. A frissítési folyamat elindításához meg kell adnia az elérhető verziók egyikét. Az Azure ezután biztonságosan látja el és üríti az egyes AK-csomópontokat, és végrehajtja a frissítést.

### <a name="cordon-and-drain"></a>Cordon és Drain

A frissítési folyamat során az AK-csomópontok külön vannak kiképezve a fürtből, így az új hüvelyek nem lesznek ütemezve. A csomópontokat ezután a következőképpen kell kiüríteni és frissíteni:

- A rendszer egy új csomópontot telepít a csomópont-készletbe. Ez a csomópont a legújabb operációsrendszer-lemezképet és-javításokat futtatja.
- A rendszer a meglévő csomópontok egyikét azonosítja a frissítéshez. A csomóponton található hüvelyek szabályosan leállnak, és a csomópont többi csomópontján vannak ütemezve.
- Ez a meglévő csomópont törölve lett az AK-fürtből.
- A fürtben a következő csomópontot a rendszer kiüríti és kiüríti ugyanazon folyamattal, amíg az összes csomópontot a frissítési folyamat részeként nem sikerült lecserélni.

További információ: AK- [fürt frissítése][aks-upgrade-cluster].

## <a name="network-security"></a>Hálózati biztonság

A helyszíni hálózatokkal létesített kapcsolat és biztonság érdekében az AK-fürtöt meglévő Azure-beli virtuális hálózati alhálózatokra is telepítheti. Ezek a virtuális hálózatok rendelkezhetnek Azure-helyek közötti VPN-vagy Express Route-kapcsolattal a helyszíni hálózathoz. A Kubernetes bemenő vezérlőket privát, belső IP-címekkel lehet meghatározni, hogy a szolgáltatások csak ezen a belső hálózati kapcsolaton keresztül legyenek elérhetők.

### <a name="azure-network-security-groups"></a>Azure-beli hálózati biztonsági csoportok

A virtuális hálózatok forgalmának szűréséhez az Azure hálózati biztonsági csoportokra vonatkozó szabályokat használ. Ezek a szabályok határozzák meg az erőforrásokhoz való hozzáférést engedélyező vagy megtagadott forrás-és cél IP-tartományokat, portokat és protokollokat. Az alapértelmezett szabályok úgy jönnek létre, hogy engedélyezzék a TLS-forgalmat a Kubernetes API-kiszolgálónak. Amikor terheléselosztó, port-hozzárendelés vagy bejövő útvonalak használatával hoz létre szolgáltatásokat, az AK automatikusan módosítja a hálózati biztonsági csoportot a megfelelő forgalomra.

Azokban az esetekben, amikor saját alhálózatot biztosít az AK-fürthöz, és módosítani szeretné a forgalom áramlását, ne módosítsa az alhálózati szintű hálózati biztonsági csoportot, amelyet az AK felügyel. További alhálózat-szintű hálózati biztonsági csoportokat is létrehozhat, amelyekkel módosíthatja a forgalom áramlását, ha nem zavarja a fürt felügyeletéhez szükséges forgalmat, például a terheléselosztó elérését, a vezérlési síkon való kommunikációt és a [kimenő][aks-limit-egress-traffic]forgalmat.

### <a name="kubernetes-network-policy"></a>Kubernetes hálózati házirend

A fürtben található hüvelyek közötti hálózati forgalom korlátozásához az AK támogatást nyújt a [Kubernetes hálózati házirendjeihez][network-policy]. A hálózati házirendek segítségével engedélyezheti vagy megtagadhatja a fürtön belüli adott hálózati elérési utakat a névterek és a címkék választói alapján.

## <a name="kubernetes-secrets"></a>A Kubernetes titkos kódjai

A *titkos* Kubernetes a bizalmas adatok hüvelybe való beadására, például a hozzáférési hitelesítő adatokra vagy kulcsokra használják. Először hozzon létre egy titkos kulcsot a Kubernetes API használatával. A pod vagy az üzembe helyezés megadásakor a rendszer egy adott titkot kérhet. A titkos kulcsokat csak olyan csomópontok számára biztosítjuk, amelyekhez szükség van egy ütemezett Pod-re, és a titkot a *tmpfs* tárolja, nem lemezre írva. Ha a titkos kulcsot tartalmazó csomópont utolsó podét törli, a titkos kulcsot a rendszer törli a csomópont tmpfs. A titkos kulcsok tárolása egy adott névtéren belül történik, és csak ugyanazon a névtéren belüli hüvelyek érhetik el.

A titkok használata csökkenti a pod vagy a Service YAML-jegyzékben definiált bizalmas adatokat. Ehelyett a Kubernetes API-kiszolgálón tárolt titkos kulcsot a YAML-jegyzék részeként kell megkérnie. Ez a megközelítés csak a titkos kulcshoz megadott Pod-hozzáférést biztosítja. Megjegyzés: a titkos titkos jegyzékfájl fájljai Base64 formátumban tartalmazzák a titkos adatokat (további részleteket a [hivatalos dokumentációban][secret-risks] talál). Ezért ezt a fájlt bizalmas információként kell kezelni, és soha nem kell véglegesíteni a verziókövetés előtt.

A Kubernetes-titkokat a rendszer a etcd-ben tárolja elosztott kulcs-érték tárolóban. A Etcd-tárolót teljes mértékben az AK felügyeli, és [az adatok titkosítása az Azure platformon belül történik][encryption-atrest]. 

## <a name="next-steps"></a>Következő lépések

Az AK-fürtök biztonságossá tételének megkezdéséhez tekintse meg [az AK-fürt frissítését][aks-upgrade-cluster]ismertető témakört.

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a fürt biztonságához és a frissítésekhez az AK][operator-best-practices-cluster-security] -ban, valamint [ajánlott eljárások a pod BIZTONSÁGhoz az AK-ban][developer-best-practices-pod-security].

Az alapvető Kubernetes és az AK-fogalmakkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Kubernetes/AK-fürtök és-munkaterhelések][aks-concepts-clusters-workloads]
- [Kubernetes/AK-identitás][aks-concepts-identity]
- [Kubernetes/AK virtuális hálózatok][aks-concepts-network]
- [Kubernetes/AK-tároló][aks-concepts-storage]
- [Kubernetes/AK-skála][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
