---
title: Windows Server Node Pools – gyakori kérdések
titleSuffix: Azure Kubernetes Service
description: A Windows Server Node-készletek és az alkalmazások számítási feladatainak Azure Kubernetes szolgáltatásban (ak) való futtatásakor tekintse meg a gyakran ismételt kérdéseket.
services: container-service
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: cc5a5ec2bbfb64a1e787277bf67579bad0543cd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739576"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>Gyakori kérdések a Windows Server Node-készletekről az AK-ban

Az Azure Kubernetes szolgáltatásban (ak) olyan csomópont-készletet hozhat létre, amely a Windows Server rendszert futtatja a csomópontokon lévő vendég operációs rendszerként. Ezek a csomópontok natív Windows-tárolókat futtathatnak, például a .NET-keretrendszerre épülő alkalmazásokat. A Linux és a Windows operációs rendszer a tárolók támogatását is tartalmazza. A Linux Kubernetes és a pod-vel kapcsolatos gyakori funkciók jelenleg nem érhetők el Windows-csomópontos készletekhez.

Ez a cikk a Windows Server-csomópontokkal kapcsolatos gyakori kérdéseket és operációsrendszer-fogalmakat ismerteti az AK-ban.

## <a name="which-windows-operating-systems-are-supported"></a>Mely Windows operációs rendszerek támogatottak?

Az AK a Windows Server 2019-es verzióját használja gazda operációs rendszerként, és csak a folyamatok elkülönítését támogatja. A más Windows Server-verziók használatával létrehozott tároló-lemezképek nem támogatottak. További információ: a [Windows-tároló verziójának kompatibilitása][windows-container-compat].

## <a name="is-kubernetes-different-on-windows-and-linux"></a>A Kubernetes különbözik a Windows és a Linux rendszereken?

A Windows Server-csomópontok készletének támogatása olyan korlátozásokat tartalmaz, amelyek a felsőbb rétegbeli Windows-kiszolgáló részét képezik a Kubernetes projektben. Ezek a korlátozások nem kifejezetten az AK-ra vonatkoznak. További információ a Windows Server rendszerhez készült, a Kubernetes-ben elérhető felső szintű támogatásról: a Kubernetes-projektből származó [Windows-támogatásra való bevezetés][intro-windows] [támogatott funkcionalitás és korlátozások][upstream-limitations] című szakasza.

A Kubernetes történelmileg Linux-alapú. A felsőbb rétegbeli [Kubernetes.IO][kubernetes] webhely számos példája Linux-csomópontokon használható. Ha Windows Server-tárolókat használó központi telepítéseket hoz létre, a következő szempontokat kell figyelembe venni az operációs rendszer szintjén:

- **Identitás** – a Linux a felhasználót egy egész számú felhasználói azonosító (UID) alapján azonosítja. A felhasználó a bejelentkezéshez alfanumerikus felhasználónevet is tartalmaz, amelyet a Linux a felhasználó UID azonosítójának fordít. A Linux hasonló módon azonosítja a felhasználói csoportokat egy egész számú csoportazonosító (GID) alapján, és a csoport nevét a hozzá tartozó GID-re fordítja.
    - A Windows Server egy nagyobb bináris biztonsági azonosítót (SID) használ, amelyet a Windows Security Access Manager (SAM) adatbázisa tárol. Ez az adatbázis nincs megosztva a gazdagép és a tárolók között, vagy a tárolók között.
- **Fájlengedélyek –** a Windows Server SID-alapú hozzáférés-vezérlési listát használ a bitmaszk és az UID + GID helyett
- **Fájlelérési utak** – a Windows Server-konvenció a \ helyett a következőt használja:/.
    - A kötetek csatlakoztatására szolgáló Pod specifikációban a Windows Server-tárolók megfelelő elérési útját kell megadnia. Például egy Linux-tárolóban a */mnt/Volume* csatlakoztatási pontja helyett olyan meghajtóbetűjelet és helyet kell megadnia, mint amilyen például a */K/Volume* a *K:* meghajtó csatlakoztatására.

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Milyen típusú lemezeket támogat a Windows?

Az Azure-lemezek és a Azure Files támogatott mennyiségi típusok. Ezek a Windows Server-tárolóban NTFS-kötetekként érhetők el.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Futtathatok csak Windows rendszerű fürtöket az AK-ban?

Egy AK-fürt fő csomópontjai (a vezérlő síkja) a szolgáltatásban találhatók, a fő összetevőket üzemeltető csomópontok operációs rendszere nem lesz elérhető. Az összes AK-fürt a Linux-alapú alapértelmezett első csomópont-készlettel jön létre. Ez a csomópont-készlet rendszerszolgáltatásokat tartalmaz, amelyek szükségesek a fürt működéséhez. Azt javasoljuk, hogy legalább két csomópontot futtasson az első csomópont-készletben a fürt megbízhatóságának és a fürt működésének a biztosításához. Az első Linux-alapú csomópont-készlet nem törölhető, ha maga az AK-fürt nem törlődik.

## <a name="how-do-i-patch-my-windows-nodes"></a>Hogyan a Windows-csomópontok javítását?

A Windows-csomópontok legújabb javításait a [csomópont-készlet frissítésével][nodepool-upgrade] vagy [a csomópont rendszerképének frissítésével][upgrade-node-image]érheti el. A Windows-frissítések nincsenek engedélyezve az AK-beli csomópontokon. Az AK új csomópont-rendszerképeket bocsát ki, amint a javítások elérhetők, és a felhasználó feladata a csomópont-készletek frissítése, hogy a jelenlegi javításokon és gyorsjavításokon maradjon. Ez a használatban lévő Kubernetes-verzióra is igaz. Az [AK kibocsátási megjegyzései][aks-release-notes] azt jelzik, hogy mikor érhetők el új verziók. A teljes Windows Server Node-készlet frissítésével kapcsolatos további információkért lásd: [csomópont-készlet frissítése az AK-ban][nodepool-upgrade]. Ha csak a csomópont rendszerképének frissítését érdekli, tekintse meg a következő témakört: [AK-csomóponti képek][upgrade-node-image]frissítése.

> [!NOTE]
> A frissített Windows Server-rendszerkép csak akkor lesz használatban, ha a fürt frissítése (a vezérlési sík frissítése) a csomópont-készlet frissítése előtt lett elvégezve.
>

## <a name="what-network-plug-ins-are-supported"></a>Milyen hálózati beépülő modulok támogatottak?

A Windows Node-készletekkel rendelkező AK-fürtöknek az Azure CNI (Advanced) hálózatkezelési modellt kell használniuk. A Kubenet (alapszintű) hálózatkezelés nem támogatott. További információ a hálózati modellekkel kapcsolatos különbségekről: az [AK-beli alkalmazások hálózati fogalmai][azure-network-models]. Az Azure CNI hálózati modellje további tervezést és szempontokat igényel az IP-címek kezeléséhez. Az Azure-CNI tervezésével és megvalósításával kapcsolatos további információkért lásd: [Az Azure CNI hálózatkezelésének konfigurálása az AK-ban][configure-azure-cni].

Az AK-fürtökön futó Windows-csomópontok esetében a rendszer alapértelmezés szerint engedélyezi a [közvetlen kiszolgáló visszaküldését (DSR)][dsr] , ha a tarka engedélyezve van.

## <a name="is-preserving-the-client-source-ip-supported"></a>Az ügyfél forrás-IP-címének megőrzése támogatott?

A Windows-csomópontok jelenleg nem támogatják az [ügyfél forrás IP-címének megőrzését][client-source-ip] .

## <a name="can-i-change-the-max--of-pods-per-node"></a>Módosíthatom-e a csomópontok max. #-ot?

Igen. Az elérhető hatásokkal és lehetőségekkel kapcsolatban lásd: [hüvelyek maximális száma][maximum-number-of-pods].

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>Miért jelenik meg hibaüzenet, amikor új Windows Agent-készletet próbálok létrehozni?

Ha a fürtöt február 2020 előtt hozta létre, és még soha nem végezte el a fürt frissítési műveleteit, a fürt továbbra is egy régi Windows-rendszerképet használ. Előfordulhat, hogy a következőhöz hasonló hibaüzenetet észlelt:

"A központi telepítési sablonban hivatkozott rendszerképek alábbi listája nem található: közzétevő: MicrosoftWindowsServer, ajánlat: WindowsServer, SKU: 2019-Datacenter-Core-smalldisk-2004, verzió: Latest. https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimageAz elérhető rendszerképek keresésével kapcsolatos utasításokért tekintse meg a következő témakört:.

A hiba elhárítása:

1. Frissítse a [fürt vezérlőjét][upgrade-cluster-cp] a rendszerkép-ajánlat és a közzétevő frissítéséhez.
1. Hozzon létre új Windows Agent-készleteket.
1. Windows-hüvelyek áthelyezése meglévő Windows Agent-készletekből új Windows Agent-készletekbe.
1. Régi Windows-ügynök készletek törlése.

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Hogyan elforgatja a szolgáltatásnevet a Windows-csomópontos készlethez?

A Windows-csomópontok nem támogatják a szolgáltatás egyszerű elforgatását. Az egyszerű szolgáltatás frissítéséhez hozzon létre egy új Windows-csomópontot, és telepítse át a hüvelyeket a régi készletből az újat. Ha ez befejeződött, törölje a régi csomópont-készletet.

Ehelyett használjon felügyelt identitásokat, amelyek lényegében burkolók az egyszerű szolgáltatások köré. További információ: [felügyelt identitások használata az Azure Kubernetes szolgáltatásban][managed-identity].

## <a name="how-many-node-pools-can-i-create"></a>Hány Node-készletet hozhatok létre?

Az AK-fürt legfeljebb 10 csomópont-készletet tartalmazhat. Ezekhez a csomópont-készletekhez legfeljebb 1000 csomópont tartozhat. [Csomópont-készletre vonatkozó korlátozások][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Mire használhatom a Windows-csomópontok készleteit?

A nevet legfeljebb 6 (hat) karakterrel kell megtartania. Ez az AK jelenlegi korlátozása.

## <a name="are-all-features-supported-with-windows-nodes"></a>Támogatottak-e a Windows-csomópontok összes funkciója?

A Windows-csomópontok jelenleg nem támogatják a Kubenet.

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Futtathatok bejövő vezérlőket Windows-csomópontokon?

Igen, a Windows Server-tárolókat támogató bejövő adatkezelők az AK-ban Windows-csomópontokon is futtathatók.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Használhatom az Azure dev Spaces szolgáltatást Windows-csomópontokkal?

Az Azure dev Spaces jelenleg csak a Linux-alapú csomópont-készletek esetében érhető el.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Használhatom a Windows Server-tárolókat a gMSA?

A csoportosan felügyelt szolgáltatásfiókok (gMSA) támogatása jelenleg nem érhető el az AK-ban.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Használhatom a Windows-csomópontokkal és-tárolókkal rendelkező tárolók Azure Monitor?

Igen, azonban Azure Monitor nyilvános előzetes verzióban érhető el a naplók (StdOut, stderr) és a Windows-tárolók metrikáinak összegyűjtéséhez. Az stdout-naplók élő streamjét egy Windows-tárolóból is csatlakoztathatja.

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>Vannak korlátozások a Windows-csomópontokkal rendelkező fürtökön található szolgáltatások számával kapcsolatban?

A Windows-csomópontokkal rendelkező fürtök körülbelül 500 szolgáltatással rendelkezhetnek a portok kimerülése előtt.

## <a name="can-i-use-azure-hybrid-benefit-with-windows-nodes"></a>Használhatom Azure Hybrid Benefit Windows-csomópontokkal?

Igen. A Windows Server Azure Hybrid Benefit csökkenti a működési költségeket azzal, hogy lehetővé teszi a helyszíni Windows Server-licencet AK-alapú Windows-csomópontokra.

Azure Hybrid Benefit használható a teljes AK-fürtön vagy az egyes csomópontokon. Az egyes csomópontok esetében navigáljon a csomópont- [erőforráscsoporthoz][resource-groups] , és alkalmazza a Azure Hybrid Benefit közvetlenül a csomópontokra. A Azure Hybrid Benefit az egyes csomópontokra való alkalmazásával kapcsolatos további információkért lásd: [Azure Hybrid Benefit a Windows Serverhez][hybrid-vms]. 

Ha a Azure Hybrid Benefitt egy új AK-fürtön szeretné használni, használja az `--enable-ahub` argumentumot.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku Standard \
    --windows-admin-password 'Password1234$' \
    --windows-admin-username azure \
    --network-plugin azure
    --enable-ahub
```

Ha meglévő AK-fürtön szeretné használni a Azure Hybrid Benefitt, frissítse a fürtöt az `--enable-ahub` argumentum használatával.

```azurecli
az aks update \
    --resource-group myResourceGroup
    --name myAKSCluster
    --enable-ahub
```

A következő parancs használatával ellenőrizze, hogy a Azure Hybrid Benefit be van-e állítva a fürtön:

```azurecli
az vmss show --name myAKSCluster --resource-group MC_CLUSTERNAME
```

Ha a fürt Azure Hybrid Benefit engedélyezve van, a kimenete a `az vmss show` következőhöz fog hasonlítani:

```console
"platformFaultDomainCount": 1,
  "provisioningState": "Succeeded",
  "proximityPlacementGroup": null,
  "resourceGroup": "MC_CLUSTERNAME"
```

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>Használhatom a Kubernetes webes irányítópultot Windows-tárolókkal?

Igen, használhatja a [Kubernetes webes irányítópultot][kubernetes-dashboard] a Windows-tárolókkal kapcsolatos információk eléréséhez, de jelenleg nem futtathatja a *kubectl exec* alkalmazást egy futó Windows-tárolóba közvetlenül a Kubernetes webes irányítópultján. További információ a futó Windows-tárolóhoz való csatlakozásról: [Csatlakozás RDP-vel az Azure Kubernetes Service (ak) fürthöz Windows Server-csomópontok karbantartáshoz vagy hibaelhárításhoz][windows-rdp].

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>Mi a teendő, ha nem támogatott funkcióra van szükségem?

Keményen dolgozunk, hogy az összes olyan funkciót felhasználjuk, amire szüksége van az AK-ban, de ha hiányosságok lépnek fel, a nyílt forráskódú és a felsőbb rétegbeli [AK-motor][aks-engine] projekt egyszerű és teljes mértékben testreszabható módot kínál a Kubernetes Azure-ban való futtatására, beleértve a Windows-támogatást is. Mindenképpen tekintse meg a következő [AK-útitervtel][aks-roadmap]kapcsolatos szolgáltatások ütemtervét.

## <a name="next-steps"></a>Következő lépések

A Windows Server-tárolók az AK-ban való megkezdéséhez [hozzon létre egy olyan csomópont-készletet, amely a Windows Server rendszerű][windows-node-cli]

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
[hybrid-vms]: ../virtual-machines/windows/hybrid-use-benefit-licensing.md
[resource-groups]: faq.md#why-are-two-resource-groups-created-with-aks
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip