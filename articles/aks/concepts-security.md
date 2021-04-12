---
title: Fogalmak – biztonság az Azure Kubernetes Servicesben (ak)
description: Ismerje meg az Azure Kubernetes szolgáltatással (ak) kapcsolatos biztonságot, beleértve a Master és Node kommunikációt, a hálózati házirendeket és a Kubernetes titkokat.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: mlearned
ms.openlocfilehash: 3fafbe3f4b1c53f929682f4ca160fb19a5e91918
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105306"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (AKS) alkalmazásainak és fürtjeinek biztonsági fogalmai

A fürt biztonsága védi az ügyféladatokat az alkalmazások számítási feladatainak Azure Kubernetes szolgáltatásban (ak) való futtatásakor. 

A Kubernetes tartalmazza a biztonsági összetevőket, például a *hálózati házirendeket* és a *titkos kulcsokat*. Eközben az Azure olyan összetevőket is tartalmaz, mint például a hálózati biztonsági csoportok és a előkészített fürtök frissítése. Az AK ezeket a biztonsági összetevőket a következőhöz ötvözi:
* Tartsa meg a legújabb operációs rendszer biztonsági frissítéseit és a Kubernetes kiadásait futtató AK-fürtöt.
* Biztonságos Pod-forgalom és a bizalmas hitelesítő adatokhoz való hozzáférés biztosítása.

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

Az AK-ban a Kubernetes fő összetevői a Microsoft által biztosított, felügyelt és karbantartott felügyelt szolgáltatás részei. Mindegyik AK-fürt saját, egyetlen bérlős, dedikált Kubernetes-főkiszolgálóval rendelkezik az API-kiszolgáló, a ütemező stb. biztosításához.

Alapértelmezés szerint a Kubernetes API-kiszolgáló egy nyilvános IP-címet és egy teljesen minősített tartománynevet (FQDN) használ. Az API-kiszolgáló végpontjának hozzáférését az [engedélyezett IP-címtartományok][authorized-ip-ranges]alapján korlátozhatja. Létrehozhat egy teljesen [privát fürtöt][private-clusters] is az API-kiszolgáló virtuális hálózathoz való hozzáférésének korlátozásához.

Az API-kiszolgáló hozzáférését az Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) és az Azure RBAC használatával szabályozhatja. További információ: [Azure ad-integráció az AK][aks-aad]-nal.

## <a name="node-security"></a>Csomópont biztonsága

Az AK-csomópontok az Ön által kezelt és karbantartott Azure-beli virtuális gépek (VM-EK). 
* A Linux-csomópontok optimalizált Ubuntu-disztribúciót futtatnak a `containerd` vagy a Moby Container Runtime használatával. 
* A Windows Server-csomópontok egy optimalizált Windows Server 2019 kiadást futtatnak a `containerd` vagy a Moby Container Runtime használatával. 

Egy AK-fürt létrehozásakor vagy skálázásakor a rendszer automatikusan telepíti a csomópontokat a legújabb biztonsági frissítésekkel és konfigurációkkal.

> [!NOTE]
> AK-fürtök a következőket használják:
> * A Kubernetes 1,19-es és újabb verziójú csomópontja `containerd` a tároló futtatókörnyezete. 
> * A v 1.19 Node-készletek előtti Kubernetes a [Moby](https://mobyproject.org/) (upstream Docker)-t használják tároló-futtatókörnyezetként.

### <a name="node-security-patches"></a>Csomópont biztonsági javításai

#### <a name="linux-nodes"></a>Linux-csomópontok
Az Azure platform automatikusan alkalmazza az operációs rendszer biztonsági javításait Linux-csomópontokra. Ha egy Linux operációs rendszer biztonsági frissítéséhez szükség van egy gazdagép újraindítására, az nem fog automatikusan újraindulni. A következő lehetőségek közül választhat:
* A Linux-csomópontok manuális újraindítása.
* A Kubernetes egy nyílt forráskódú újraindítási démont használ a [Kured][kured]. A Kured [daemonset elemet][aks-daemonsets] fut, és figyeli az egyes csomópontokat egy fájlhoz, amely jelzi, hogy újraindítás szükséges. 

A rendszer az újraindításokat a fürtön keresztül felügyeli a fürt frissítésével megegyező [Kordon és kiürítési folyamat](#cordon-and-drain) használatával.

#### <a name="windows-server-nodes"></a>Windows Server-csomópontok

A Windows Server-csomópontok esetében Windows Update nem fut automatikusan, és nem alkalmazza a legújabb frissítéseket. Ütemezze a Windows Server Node-készlet frissítéseit az AK-fürtön a normál Windows Update kiadási ciklus és a saját ellenőrzési folyamata körül. Ez a frissítési folyamat olyan csomópontokat hoz létre, amelyek a legújabb Windows Server-lemezképet és-javításokat futtatják, majd eltávolítja a régebbi csomópontokat. További információ erről a folyamatról: [Node-készlet frissítése az AK-ban][nodepool-upgrade].

### <a name="node-deployment"></a>Csomópont központi telepítése
A csomópontok magánhálózati virtuális hálózati alhálózatba vannak telepítve, és nincsenek hozzárendelve nyilvános IP-címek. Hibaelhárítási és felügyeleti célokra az SSH alapértelmezés szerint engedélyezve van, és csak a belső IP-cím használatával érhető el.

### <a name="node-storage"></a>Csomópont-tároló
A tárolók biztosításához a csomópontok az Azure Managed Disks használják. A virtuálisgép-csomópontok legtöbb méretéhez az Azure Managed Disks a nagy teljesítményű SSD-k által támogatott prémium szintű lemezek. A felügyelt lemezeken tárolt adatok automatikusan titkosítva maradnak az Azure platformon belül. A redundancia javítása érdekében az Azure Managed Disks biztonságosan replikálódnak az Azure-adatközpontban.

### <a name="hostile-multi-tenant-workloads"></a>Ellenséges több-bérlős számítási feladatok

Jelenleg a Kubernetes-környezetek nem biztonságosak az ellenséges, több-bérlős használatra. A további biztonsági funkciók, például a *Pod biztonsági házirendek* vagy a csomópontok Kubernetes RBAC hatékonyan blokkolják a biztonsági réseket. Az ellenséges több-bérlős számítási feladatok futtatásakor az igaz biztonság érdekében csak a Hypervisort bízza meg. A Kubernetes biztonsági tartománya a teljes fürtvé válik, nem önálló csomópontként. 

Az ilyen típusú ellenséges több-bérlős munkaterhelések esetében fizikailag elkülönített fürtöket kell használnia. A számítási feladatok elkülönítésének módjaival kapcsolatos további információkért lásd: [ajánlott eljárások a fürtök elkülönítéséhez az AK-ban][cluster-isolation].

### <a name="compute-isolation"></a>Számítási elkülönítés

A megfelelőségi vagy szabályozási követelmények miatt bizonyos számítási feladatok nagyfokú elkülönítést igényelhetnek a többi ügyfél-munkaterheléstől. Ezekben a számítási feladatokban az Azure [elkülönített virtuális gépeket](../virtual-machines/isolation.md) biztosít, amelyek ügynök-csomópontként használhatók egy AK-fürtben. Ezek a virtuális gépek egy adott hardvereszközhöz vannak elkülönítve, és egyetlen ügyfélhez vannak hozzárendelve. 

Válassza ki az [elkülönített virtuális gépek](../virtual-machines/isolation.md) méretét a **csomópont méreteként** egy AK-fürt létrehozásakor vagy egy csomópont-készlet hozzáadásakor.

## <a name="cluster-upgrades"></a>Fürt frissítései

Az Azure frissítési előkészítési eszközöket biztosít az AK-fürtök és-összetevők frissítésére, a biztonság és a megfelelőség fenntartására, valamint a legújabb funkciók elérésére. Ez a frissítési előkészítés magában foglalja a Kubernetes fő-és ügynök-összetevőit is. 

A frissítési folyamat elindításához a [felsorolt elérhető Kubernetes-verziók](supported-kubernetes-versions.md)egyikét kell megadnia. Az Azure ezután biztonságosan kiüríti az egyes AK-csomópontokat és a frissítéseket.

### <a name="cordon-and-drain"></a>Cordon és Drain

A frissítési folyamat során az AK-csomópontok külön vannak kiképezve a fürtből annak megakadályozása érdekében, hogy új hüvelyek legyenek ütemezve. A csomópontokat ezután a következőképpen kell kiüríteni és frissíteni:

1.  A rendszer egy új csomópontot telepít a csomópont-készletbe. 
    * Ez a csomópont a legújabb operációsrendszer-lemezképet és-javításokat futtatja.
1. A rendszer a meglévő csomópontok egyikét azonosítja a frissítéshez. 
1. Az azonosított csomóponton lévő hüvelyeket a rendszer szabályosan leállítja és ütemezi a csomópont-készlet többi csomópontján.
1. Az üres csomópont törlődik az AK-fürtből.
1. Az 1-4-es lépések addig ismétlődnek, amíg az összes csomópontot nem sikerült lecserélni a frissítési folyamat részeként.

További információ: AK- [fürt frissítése][aks-upgrade-cluster].

## <a name="network-security"></a>Hálózati biztonság

A helyszíni hálózatokkal létesített kapcsolat és biztonság érdekében az AK-fürtöt meglévő Azure-beli virtuális hálózati alhálózatokra is telepítheti. Ezek a virtuális hálózatok az Azure-helyek közötti VPN vagy Express Route használatával csatlakoznak a helyszíni hálózathoz. A belső hálózati kapcsolathoz való hozzáférés korlátozására szolgáló belső IP-címekkel rendelkező Kubernetes-beáramlási vezérlők definiálása.

### <a name="azure-network-security-groups"></a>Azure-beli hálózati biztonsági csoportok

A virtuális hálózati forgalom szűréséhez az Azure hálózati biztonsági csoportokra vonatkozó szabályokat használ. Ezek a szabályok határozzák meg az erőforrásokhoz való hozzáférést engedélyező vagy megtagadott forrás-és cél IP-tartományokat, portokat és protokollokat. Az alapértelmezett szabályok úgy jönnek létre, hogy engedélyezzék a TLS-forgalmat a Kubernetes API-kiszolgálónak. A szolgáltatásokat terheléselosztó, Port-hozzárendelések vagy bejövő útvonalak útján hozhatja létre. Az AK automatikusan módosítja a hálózati biztonsági csoportot a forgalmi folyamat számára.

Ha a saját alhálózatát adja meg az AK-fürthöz, **Ne módosítsa az** alhálózati szintű hálózati biztonsági csoportot, amelyet az AK felügyel. Ehelyett hozzon létre több alhálózati szintű hálózati biztonsági csoportot a forgalom forgalmának módosításához. Győződjön meg arról, hogy nem zavarja a fürtöt kezelő szükséges forgalmat, például a terheléselosztó elérését, a vezérlési síkon folytatott kommunikációt és a [kimenő][aks-limit-egress-traffic]adatokat.

### <a name="kubernetes-network-policy"></a>Kubernetes hálózati házirend

A fürtben található hüvelyek közötti hálózati forgalom korlátozásához az AK támogatást nyújt a [Kubernetes hálózati házirendjeihez][network-policy]. A hálózati házirendekkel engedélyezheti vagy megtagadhatja a fürtön belüli adott hálózati elérési utakat a névterek és a címkék választói alapján.

## <a name="kubernetes-secrets"></a>A Kubernetes titkos kódjai

A *titkos* Kubernetes a bizalmas adatokat hüvelybe helyezi, például a hozzáférési hitelesítő adatokat vagy a kulcsokat. 
1. Hozzon létre egy titkos kulcsot a Kubernetes API használatával. 
1. Adja meg a pod vagy az üzembe helyezést, és kérjen egy adott titkot. 
    * A titkos kulcsokat csak egy ütemezett Pod-vel rendelkező csomópontok számára biztosítjuk, amelyekhez szükség van rájuk.
    * A titkos kulcsot a rendszer a *tmpfs* tárolja, nem lemezre írva. 
1. Ha törli az utolsó Pod-t egy titkos kulcsot igénylő csomóponton, a titkos kulcsot a rendszer törli a csomópont tmpfs. 
   * A titkos kulcsok tárolása egy adott névtéren belül történik, és csak ugyanazon a névtéren belüli hüvelyek érhetik el.

A titkok használata csökkenti a pod vagy a Service YAML-jegyzékben meghatározott bizalmas adatokat. Ehelyett a Kubernetes API-kiszolgálón tárolt titkos kulcsot a YAML-jegyzék részeként kell megkérnie. Ez a megközelítés csak a titkos kulcshoz megadott Pod-hozzáférést biztosítja. 

> [!NOTE]
> A nyers titkos jegyzékfájl fájljai Base64 formátumban tartalmazzák a titkos adatokat (további részletekért tekintse meg a [hivatalos dokumentációt][secret-risks] ). Ezeket a fájlokat bizalmas adatokként kezelje, és soha ne véglegesítse őket a verziókövetés során.

A Kubernetes-titkokat a rendszer a etcd-ben tárolja elosztott kulcs-érték tárolóban. A Etcd-tárolót teljes mértékben az AK felügyeli, és [az adatok titkosítása az Azure platformon belül történik][encryption-atrest]. 

## <a name="next-steps"></a>Következő lépések

Az AK-fürtök biztonságossá tételének megkezdéséhez tekintse meg [az AK-fürt frissítését][aks-upgrade-cluster]ismertető témakört.

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a fürt biztonságához és a frissítésekhez az AK][operator-best-practices-cluster-security] -ban, valamint [ajánlott eljárások a pod BIZTONSÁGhoz az AK-ban][developer-best-practices-pod-security].

Az alapvető Kubernetes és az AK-fogalmakkal kapcsolatos további információkért lásd:

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
