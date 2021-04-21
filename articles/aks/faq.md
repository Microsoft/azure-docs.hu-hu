---
title: Gyakori kérdések a Azure Kubernetes Service (AKS)
description: Választ találhat néhány gyakori kérdésre, amelyek a Azure Kubernetes Service (AKS) kapcsolatosak.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: references_regions
ms.openlocfilehash: f13d7a33ce1dc04700932072fe0af80a901c681f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783196"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Gyakori kérdések az Azure Kubernetes Service-szel (AKS) kapcsolatban

Ez a cikk az (AKS Azure Kubernetes Service (AKS) Azure Kubernetes Service foglalkozik.

## <a name="which-azure-regions-currently-provide-aks"></a>Jelenleg mely Azure-régiók biztosítják az AKS-t?

Az elérhető régiók teljes listáját lásd: [AKS-régiók és rendelkezésre állás.][aks-regions]

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>El tudok terjeszteni egy AKS-fürtöt régiók között?

Nem. Az AKS-fürtök regionális erőforrások, és nem használhatja a régiókat. A [több régiót][bcdr-bestpractices] tartalmazó architektúra létrehozásához tekintse meg az üzletmenet-folytonosság és a vészhelyreállítás ajánlott eljárásait.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>El tudok terjeszteni egy AKS-fürtöt a rendelkezésre állási zónák között?

Igen. Az AKS-fürtök üzembe helyezhetők egy vagy több rendelkezésre állási zónában az azokat [támogató régiókban.][az-regions] [][availability-zones]

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Korlátozom, hogy ki férhet hozzá a Kubernetes API-kiszolgálóhoz?

Igen. Két lehetőség van az API-kiszolgálóhoz való hozzáférés korlátozására:

- Akkor [használjon engedélyezett IP-tartományokat az API Serverhez,][api-server-authorized-ip-ranges] ha az API-kiszolgáló nyilvános végpontját szeretné fenntartani, de a hozzáférést megbízható IP-címtartományok egy készletéhez szeretné korlátozni.
- Használjon [privát fürtöt,][private-clusters] ha korlátozni szeretné, hogy az API-kiszolgáló csak a virtuális hálózaton belülről legyen elérhető. 

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Eltérő virtuálisgép-méretek lehetnek egy fürtben?

Igen, az AKS-fürtben különböző virtuálisgép-méreteket használhat több [csomópontkészlet létrehozásával.][multi-node-pools]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Alkalmazva vannak biztonsági frissítések az AKS-ügynökcsomópontokra?

Az Azure automatikusan alkalmazza a biztonsági javításokat a fürt Linux-csomópontjaira egy éjszakai ütemezés szerint. Az Ön feladata azonban gondoskodni arról, hogy a Linux-csomópontok szükség szerint újraindulnak. A csomópontok újraindítására több lehetőség közül választhat:

- Manuálisan, a Azure Portal az Azure CLI használatával.
- Az AKS-fürt frissítéséhez. A fürt automatikusan frissíti a [cordont][cordon-drain] és üríti a csomópontokat, majd új csomópontot hoz online állapotba a legújabb Ubuntu-rendszerképpel és egy új javításverzióval vagy egy kisebb Kubernetes-verzióval. További információ: [AKS-fürt frissítése.][aks-upgrade]
- A csomópont [rendszerképének frissítését használva.](node-image-upgrade.md)

### <a name="windows-server-nodes"></a>Windows Server-csomópontok

Windows Server-csomópontok Windows Update nem fut automatikusan, és nem alkalmazza a legújabb frissítéseket. Az AKS-Windows Update kiadási ciklusa és a saját ellenőrzési folyamata körül rendszeres időközönként frissítést kell végeznie a fürtön és a Windows Server-csomópontkészlet(öke)n. Ez a frissítési folyamat olyan csomópontokat hoz létre, amelyek a legújabb Windows Server-rendszerképet és -javításokat futtatják, majd eltávolítja a régebbi csomópontokat. További információ erről a folyamatról: Csomópontkészlet frissítése az [AKS-ban.][nodepool-upgrade]

## <a name="why-are-two-resource-groups-created-with-aks"></a>Miért jön létre két erőforráscsoport az AKS-sel?

Az AKS számos Azure-infrastruktúra-erőforrásra épül, beleértve a virtuálisgép-méretezési készleteket, a virtuális hálózatokat és a felügyelt lemezeket. Ez lehetővé teszi, hogy kihasználja az Azure platform számos alapvető képességét az AKS által biztosított felügyelt Kubernetes-környezetben. Például a legtöbb Azure-beli virtuálisgép-típus közvetlenül használható az AKS-sel, az Azure Reservations pedig az erőforrásokra vonatkozó kedvezmények automatikus fogadására.

Az architektúra engedélyezéséhez az AKS-környezetek két erőforráscsoportra is ki vannak ásva:

1. Ön hozza létre az első erőforráscsoportot. Ez a csoport csak a Kubernetes service-erőforrást tartalmazza. Az AKS erőforrás-szolgáltató az üzembe helyezés során automatikusan létrehozza a második erőforráscsoportot. A második erőforráscsoport például a *MC_myResourceGroup_myAKSCluster_eastus.* A második erőforráscsoport nevének megadásáról a következő szakaszban talál további információt.
1. A második erőforráscsoport, más néven a *csomópont-erőforráscsoport,* a fürthöz társított összes infrastruktúra-erőforrást tartalmazza. Ilyen erőforrás például a Kubernetes-csomópont virtuális gépei, a virtuális hálózat és a tárterület. Alapértelmezés szerint a csomópont erőforráscsoportjának neve a következő: *MC_myResourceGroup_myAKSCluster_eastus.* Az AKS a fürt törlésekor automatikusan törli a csomópont-erőforrást, ezért csak olyan erőforrásokhoz szabad használni, amelyek osztoznak a fürt életciklusán.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Meg tudom adni a saját névmat az AKS-csomópont erőforráscsoporthoz?

Igen. Alapértelmezés szerint az AKS a csomópont-erőforráscsoportnak a nevet *MC_resourcegroupname_clustername_location,* de a saját nevét is meg lehet adni.

Saját erőforráscsoport-név megadásához telepítse az [aks-preview Azure CLI-bővítmény][aks-preview-cli] *0.3.2-es* vagy újabb verzióját. Amikor az [az aks create][az-aks-create] paranccsal hoz létre egy AKS-fürtöt, használja a paramétert, és adjon nevet `--node-resource-group` az erőforráscsoportnak. Ha [][aks-rm-template] egy AKS Azure Resource Manager fürt üzembe helyezéséhez egy sablont használ, az erőforráscsoport nevét a *nodeResourceGroup* tulajdonság használatával határozhatja meg.

* A másodlagos erőforráscsoportot automatikusan az Azure erőforrás-szolgáltatója hozta létre a saját előfizetésében.
* Egyéni erőforráscsoport-nevet csak a fürt létrehozásakor lehet megadni.

A csomópont-erőforráscsoporttal való munka során ne feledje, hogy nem tudja:

* Adjon meg egy meglévő erőforráscsoportot a csomópont-erőforráscsoport számára.
* Adjon meg egy másik előfizetést a csomópont-erőforráscsoporthoz.
* A fürt létrehozása után módosítsa a csomópont erőforráscsoportjának nevét.
* Adja meg a csomópont-erőforráscsoportban található felügyelt erőforrások nevét.
* A csomópont-erőforráscsoportban található felügyelt erőforrások Azure által létrehozott címkéinek módosítása vagy törlése. (További információkat a következő szakaszban láthat.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Módosít tudom a csomópont-erőforráscsoportban található AKS-erőforrások címkéit és egyéb tulajdonságait?

Ha módosítja vagy törli az Azure által létrehozott címkéket és más erőforrás-tulajdonságokat a csomópont-erőforráscsoportban, váratlan eredményeket, például skálázást és frissítéssel kapcsolatos hibákat okozhat. Az AKS lehetővé teszi a végfelhasználók által létrehozott egyéni címkék létrehozását és módosítását, és ezeket a címkéket [hozzáadhatja a csomópontkészlet létrehozásakor.](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool) Érdemes lehet egyéni címkéket létrehozni vagy módosítani, például egy üzleti egység vagy költségközpont hozzárendelése esetén. Ez az Azure-szabályzatok felügyelt erőforráscsoportra vonatkozó hatókörrel való létrehozásával is elérhető.

Az **Azure által** az AKS-fürtben található csomópont-erőforráscsoport erőforrásain létrehozott címkék módosítása azonban nem támogatott művelet, amely megszakítja a szolgáltatásiszint-célkitűzést (SLO). További információ: [Az AKS kínál szolgáltatásiszint-szerződést?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Milyen Kubernetes belépésvezérlőket támogat az AKS? Hozzáadhatók vagy eltávolíthatók a belépésvezérlők?

Az AKS a következő [belépésvezérlőket támogatja:][admission-controllers]

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds (DefaultTolerationSeconds)*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *PodNodeSelector*
- *PodTolerationRestriction*
- *ExtendedResourceToleration*

Jelenleg nem módosíthatja a belépésvezérlők listáját az AKS-ban.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Használhatok belépésvezérlő webhookokat az AKS-hez?

Igen, használhat belépésvezérlő webhookokat az AKS-hez. Javasoljuk, hogy zárja ki a vezérlősík címkével ellátott belső **AKS-névtereket.** Például adja hozzá az alábbiakat a webhook konfigurációhoz:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

Az AKS tűzfalakon keresztül az API-kiszolgáló bejövő forgalommal rendelkezik, így a belépésvezérlő webhookoknak elérhetőknek kell lenniük a fürtön belülről.

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Hatással lehetnek a belépésvezérlő webhookok a kube-rendszer és a belső AKS-névterek működésére?

A rendszer stabilitásának védelme és annak megakadályozása érdekében, hogy az egyéni belépésvezérlők hatással vannak a kube-rendszer belső szolgáltatásaira, a névtér AKS-éhez tartozik egy Belépés-kényszerítő, amely automatikusan kizárja a kube-system és az AKS belső névtereket. Ez a szolgáltatás biztosítja, hogy az egyéni belépésvezérlők ne befolyásolják a Kube-rendszerben futó szolgáltatásokat.

Ha kritikus fontosságú a kube-rendszeren üzembe helyezett valami (nem ajánlott), amelyre az egyéni beléptetési webhooknak szüksége van, hozzáadhatja az alábbi címkét vagy jegyzetet, hogy a Belépések kényszerítője figyelmen kívül hagyja.

Címke: ```"admissions.enforcer/disabled": "true"``` vagy Jegyzet: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Integrálva Azure Key Vault az AKS-sel?

Az AKS jelenleg nincs natív módon integrálva a Azure Key Vault. A CSI [][csi-driver] titkos Azure Key Vault tárolóhoz használt Azure Key Vault azonban lehetővé teszi a Kubernetes-podok és a titkos Key Vault integrációját.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Futtatok Windows Server-tárolókat az AKS-on?

Igen, a Windows Server-tárolók elérhetők az AKS-ban. A Windows Server-tárolók AKS-ben való futtatásához létre kell hoznia egy csomópontkészletet, amely a Windows Servert futtatja vendég operációs rendszerként. A Windows Server-tárolók csak Windows Server 2019-et használhatnak. Első lépésekért lásd: [AKS-fürt létrehozása Windows Server-csomópontkészletben.][aks-windows-cli]

A Csomópontkészlet Windows Server-támogatása tartalmaz néhány korlátozást, amelyek a Kubernetes-projektben a windowsos upstream Windows Server részét képezi. További információ ezekről a korlátozásokról: [Windows Server-tárolók az AKS korlátozásaiban.][aks-windows-limitations]

## <a name="does-aks-offer-a-service-level-agreement"></a>Kínál az AKS szolgáltatásiszint-szerződést?

Az AKS sla-garanciákat biztosít választható bővítményként, [üzemidőre vonatkozó SLA-val.][uptime-sla] 

Az alapértelmezés szerint kínált ingyenes termékváltozat nem rendelkezik társított szolgáltatói  *szerződéssel,* de 99,5%-os szolgáltatásiszint-célkitűzéssel rendelkezik. Előfordulhat, hogy frissítések, nem megfelelő aláfedő csomópontok, platformkarbantartás, az API Servert kérésekkel túlterhelő alkalmazások stb. esetében átmeneti csatlakozási problémákat észlel a rendszer. Ha a számítási feladat nem tűri az API Server újraindítását, javasoljuk az Üzemidő SLA használatát.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Alkalmazhatok Azure-foglalási kedvezményeket az AKS-ügynökcsomópontokra?

Az AKS-ügynökcsomópontok számlázása standard Azure-beli virtuális gépekként történik, így ha az AKS-hez használt virtuálisgép-mérethez [Azure Reservationst][reservation-discounts] vásárolt, a rendszer automatikusan alkalmazza ezeket a kedvezményeket.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Áthelyeztetheti/átemelheti a fürtöt az Azure-bérlők között?

Az AKS-fürt bérlők közötti mozgatás jelenleg nem támogatott.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Áthelyeztetheti/átemelheti a fürtöt az előfizetések között?

A fürtök előfizetések közötti mozgása jelenleg nem támogatott.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Áthelyezhetim az AKS-fürtjömet az aktuális Azure-előfizetésből egy másikba?

Az AKS-fürt és a hozzá tartozó erőforrások Azure-előfizetések közötti mozgatása nem támogatott.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Áthelyezhetők az AKS-fürtök vagy az AKS-infrastruktúra erőforrásai más erőforráscsoportokba, vagy átnevezhetők?

Az AKS-fürt és a hozzá tartozó erőforrások áthelyezését vagy újnanamináltatását nem támogatja.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Miért tart olyan sokáig a fürt törlése?

A legtöbb fürt felhasználói kérés esetén törlődik; bizonyos esetekben, különösen akkor, ha az ügyfelek saját erőforráscsoportot hoznak létre, vagy kereszt-RG-feladatokat törölnek, több időt vagy sikertelenül is eltelhet. Ha probléma adódik a törlésekkel, ellenőrizze, hogy nincs-e zárolása az erőforrás-rg-re, hogy az RG-n kívüli erőforrások nincsenek-e társítva az erőforrás-rg-ről stb.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Ha a podom/üzemelő példányom "NodeLost" vagy "Ismeretlen" állapotban van, akkor is frissíthető a fürtöm?

Igen, de az AKS ezt nem javasolja. A frissítéseket akkor kell végrehajtani, ha a fürt állapota ismert és kifogástalan.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Ha egy fürt egy vagy több csomópontja nem megfelelő állapotban van, vagy le vagyok állított állapotban, végezhetek frissítést?

Nem, a frissítés előtt törölje/távolítsa el a hibás állapotban lévő vagy egyéb módon eltávolított csomópontokat a fürtről.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Futtattam egy fürt törlését, de a következő hibaüzenet jelenik meg: `[Errno 11001] getaddrinfo failed`

Ezt leggyakrabban az okozza, hogy a felhasználók egy vagy több hálózati biztonsági csoporttal (NSG-vel) még használatban vannak, és a fürthöz vannak társítva.  Távolítsa el őket, és próbálja meg újra a törlést.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Futtattam egy frissítést, de a podok összeomlási hurkban vannak, és a készenlét-mintavételek meghiúsulnak?

Ellenőrizze, hogy a szolgáltatásnév nem járt-e le.  Lásd: [AKS-szolgáltatásnév és](./kubernetes-service-principal.md) [AKS frissítési hitelesítő adatok.](./update-credentials.md)

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>A fürtöm működött, de hirtelen nem tud terheléselosztást kiépítni, PVC-ket stb. csatlakoztatni?

Ellenőrizze, hogy a szolgáltatásnév nem járt-e le.  Lásd: [AKS-szolgáltatásnév és](./kubernetes-service-principal.md) [AKS frissítési hitelesítő adatok.](./update-credentials.md)

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Nullára skálázhatja az AKS-fürtömet?
Teljesen leállíthat [egy futó AKS-fürtöt,](start-stop-cluster.md)így megtakaríthatja a megfelelő számítási költségeket. Emellett dönthet úgy is, [ `User` ](scale-cluster.md#scale-user-node-pools-to-0) hogy az összes vagy adott csomópontkészletet 0-ra skáláz, és csak a szükséges fürtkonfigurációt tartja fenn.
A rendszercsomópont-készleteket nem [lehet közvetlenül nullára](use-system-pools.md) skálázni.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Használhatom a virtuálisgép-méretezési készlet API-it manuális skálázhatóként?

Nem, a virtuálisgép-méretezési készlet API-ját használó skáláz műveletek nem támogatottak. Használja az AKS API-kat ( `az aks scale` ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>Használhatok virtuálisgép-méretezési csoportokat a nullára manuálisan skálázható csomópontokra?

Nem, a méretezési műveletek a virtuálisgép-méretezési készlet API-ival nem támogatottak. Az AKS API-val nulla nem rendszer-csomópontkészletre skálázhat, vagy leállíthatja a [fürtöt.](start-stop-cluster.md)

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Leállítható vagy lefoglalható az összes virtuális gépem?

Bár az AKS rendelkezik olyan rugalmassági mechanizmusokkal, amelyek ellenállnak egy ilyen konfigurációnak, és helyreállítják azt, ez nem támogatott konfiguráció. [Ehelyett állítsa le a fürtöt.](start-stop-cluster.md)

## <a name="can-i-use-custom-vm-extensions"></a>Használhatok egyéni virtuálisgép-bővítményeket?

A Log Analytics-ügynök támogatott, mivel ez a Microsoft által kezelt bővítmény. Ellenkező esetben az AKS egy felügyelt szolgáltatás, és az IaaS-erőforrások kezelése nem támogatott. Egyéni összetevők telepítéséhez használja a Kubernetes API-kat és mechanizmusokat. A szükséges összetevők telepítéséhez például használja a DaemonSets programot.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>Az AKS tárol ügyféladatokat a fürt régióján kívül?

Az ügyféladatok egyetlen régióban való tárolásának engedélyezése jelenleg csak Brazília Geo Ázsia és a Csendes-óceáni térség Geo és Dél-Brazília (Sao Paulo State) régiójának Délkelet-Ázsia (Szingapúr) régiójában érhető el. Minden más régió esetében az ügyféladatokat a Geo tárolja.

## <a name="are-aks-images-required-to-run-as-root"></a>Szükséges az AKS-rendszerképek futtatása gyökérként?

A következő két rendszerkép kivételével az AKS-rendszerképek futtatásához nem szükséges gyökérként futtatni:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Mi a Azure CNI és a híd mód?

Az 1.2.0-s Azure CNI linuxos CNI-környezetek esetén a transzparens mód lesz az alapértelmezett. A transzparens mód a híd üzemmódot váltja fel. Ebben a szakaszban bővebben is tárgyaljuk a két mód közötti különbségeket, valamint a transzparens mód használatának előnyeit/korlátozását a Azure CNI.

### <a name="bridge-mode"></a>Híd üzemmód

Ahogy a neve is sugallja, a Azure CNI mód "időben" létrehoz egy "azure0" nevű L2-hidat. Ehhez a hídhoz az összes gazdaoldali podpár `veth` csatoló csatlakozik. Így Pod-Pod virtuális gépen belüli kommunikáció, a fennmaradó forgalom pedig ezen a hídon halad át. A szóban forgó híd egy 2. rétegben lévő virtuális eszköz, amely egyedül nem fogadhat és nem továbbíthat semmit, hacsak nem köt hozzá egy vagy több valós eszközt. Ezért a Linux rendszerű virtuális gép eth0-ét egy alárendeltből "azure0" hídká kell konvertálni. Ez összetett hálózati topológiát hoz létre a Linux rendszerű virtuális gépen, és ennek tüneteként a CNI-nek más hálózati funkciókat kellett ellátnia, például a DNS-kiszolgáló frissítését stb.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Híd módú topológia":::

Az alábbi példa azt mutatja be, hogyan néz ki az IP-útvonal beállítása Híd módban. Függetlenül attól, hogy a csomópont hány podtal rendelkezik, csak két útvonal lesz. Az első azt mondja, hogy az azure0-on a local kivételével minden forgalom az alhálózat alapértelmezett átjárójára kerül az "src 10.240.0.4" IP-címmel (amely a Node elsődleges IP-címe), a második pedig a "10.20.x.x" podtér a kernelhez, amelyet a kernelnek kell eldöntenie.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Transzparens mód
A transzparens mód egyértelműen közelíti meg a Linux-hálózat beállítását. Ebben a módban Azure CNI eth0 interfész tulajdonságai nem változnak meg a Linux rendszerű virtuális gépen. A Linux hálózati tulajdonságok módosításának ez a minimális megközelítése segít csökkenteni az összetett sarokeset-problémákat, amelyek a fürtök bridge (Híd) módban is szembesülnek. Transzparens módban Azure CNI gazdagépoldali podpár-interfészeket hoz létre és ad hozzá, amelyek a `veth` gazdagéphálózathoz lesznek hozzáadva. A virtuális gép podok közötti kommunikációja a CNI által hozzáadt IP-útvonalakon keresztül megy keresztül. A podok közötti kommunikáció lényegében a 3. rétegben, a podok forgalmát pedig L3 útválasztási szabályok irányítják.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Transzparens módú topológia":::

Az alábbiakban egy példa látható a transzparens mód IP-útvonalának beállítására. Minden pod felülete egy statikus útvonalat kap csatolva, így a podként dest IP-címmel csatlakozó forgalom közvetlenül a pod gazdaoldali pár felületére `veth` lesz elküldve.

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>A transzparens mód előnyei

- Enyhíti a DNS párhuzamos versenyfeltét, és elkerüli az 5 mp-es DNS-késési problémákat anélkül, hogy be kellene állítania a csomópont helyi DNS-ét (teljesítménybeli okokból továbbra is használhatja a csomópont helyi `conntrack` DNS-ét).
- Kiküszöböli a dns-késés kezdeti 5 mp-es CNI-híd üzemmódját, amely jelenleg az "időben" hídbeállítás miatt lép fel.
- Híd módban az egyik sarokpont az, hogy Azure CNI nem tudja folyamatosan frissíteni az egyéni DNS-kiszolgáló listáit, amelyekhez a felhasználók hozzáadják a VNET-et vagy a hálózati adaptert. Ennek eredménye az, hogy a CNI csak a DNS-kiszolgálólista első példányát veszi fel. Transzparens módban oldható meg, mivel a CNI nem módosítja az eth0 tulajdonságait. További információt [itt láthat.](https://github.com/Azure/azure-container-networking/issues/713)
- Jobban kezeli az UDP-forgalmat, és mérsékli az UDP-elárasztásos támadásokat, ha az ARP időkorralékot ad. Híd üzemmódban, ha a híd nem ismeri a célpod MAC-címét a virtuális gépen belüli podok közötti kommunikációban, ez a csomag minden portra való túlviharát jelenti. Transzparens módban oldható meg, mivel nincsenek L2-eszközök az útvonalon. További információt [itt láthat.](https://github.com/Azure/azure-container-networking/issues/704)
- A transzparens mód jobb teljesítményt teljesítményt ad a virtuális gépek közötti podok közötti kommunikációban az átviteli sebesség és a késés tekintetében a híd módhoz képest.

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>Hogyan kerülendő el, hogy az engedély tulajdonjogának beállítása lassú legyen, ha a köteten sok fájl található?

Ha a pod hagyományosan nem gyökér szintű felhasználóként fut (ahogyan kellene), meg kell adnia egy et a pod biztonsági környezetében, hogy a kötet olvasható és írható legyen `fsGroup` a pod számára. Erről a követelményről itt részletesebben [is lesz információ.](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)

A beállítás egyik mellékhatása azonban az, hogy a Kubernetes-nek minden egyes kötet csatlakoztatásakor rekurzívan kell lennie, valamint a köteten belüli összes fájlnak és könyvtárnak – néhány alább feljegyzett `fsGroup` `chown()` `chmod()` kivétellel. Ez akkor is előfordul, ha a kötet csoporttulajdonlása már megfelel a kértnek, és meglehetősen drága lehet a nagy méretű, sok kis fájlt tartalmazó kötetek esetén, ami miatt a pod indítása hosszú `fsGroup` időt fog igényelni. Ez a forgatókönyv az 1.20-as előtti ismert probléma volt, és a megkerülő megoldás a pod futtatásának gyökérként való beállítása:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

A problémát a Kubernetes v1.20 oldotta meg. További részletekért tekintse meg a [Kubernetes 1.20:](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/) A kötetengedélyek változásainak részletes vezérlése című fejezetet.


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
