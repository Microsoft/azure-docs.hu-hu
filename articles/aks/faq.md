---
title: Gyakran ismételt kérdések az Azure Kubernetes szolgáltatásról (ak)
description: Válaszok az Azure Kubernetes szolgáltatással (ak) kapcsolatos gyakori kérdésekre.
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 6c2eddf3b7002b101fed8face4a58f2d2b2f4878
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820262"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Gyakori kérdések az Azure Kubernetes Service-szel (AKS) kapcsolatban

Ez a cikk az Azure Kubernetes Service (ak) szolgáltatással kapcsolatos gyakori kérdéseket tárgyalja.

## <a name="which-azure-regions-currently-provide-aks"></a>Mely Azure-régiók jelenleg biztosítanak AK-t?

Az elérhető régiók teljes listájáért lásd: [AK-régiók és rendelkezésre állás][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>El lehet osztani egy AK-fürtöt régiók között?

Nem. Az AK-fürtök regionális erőforrások, és nem terjedhetnek ki régiókra. Tekintse meg az [ajánlott eljárásokat az üzletmenet folytonossága és a vész-helyreállítás][bcdr-bestpractices] című témakörben, amely útmutatást nyújt a több régiót tartalmazó architektúra létrehozásához.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>El tudok osztani egy AK-fürtöt a rendelkezésre állási zónák között?

Igen. Egy vagy több [rendelkezésre állási zónában][availability-zones] üzembe HELYEZHET egy AK-fürtöt az [azokat támogató régiók][az-regions]között.

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Korlátozható, hogy ki férhet hozzá a Kubernetes API-kiszolgálóhoz?

Igen. Az API-kiszolgáló elérésének korlátozására két lehetőség áll rendelkezésre:

- Ha egy nyilvános végpontot szeretne fenntartani az API-kiszolgáló számára, használja az [API-kiszolgáló által jogosult IP-tartományokat][api-server-authorized-ip-ranges] , de korlátozza a hozzáférést a megbízható IP-címtartományok készletéhez.
- Használjon [privát fürtöt][private-clusters] , ha korlátozni szeretné az API-kiszolgálót, hogy *csak* a virtuális hálózaton belül legyen elérhető.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Használhatok különböző virtuálisgép-méreteket egyetlen fürtben?

Igen, [több Node-készlet][multi-node-pools]létrehozásával különböző virtuálisgép-méreteket is HASZNÁLHAT az AK-fürtben.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Az AK-ügynökök csomópontjain vannak biztonsági frissítések alkalmazva?

Az Azure automatikusan a fürt Linux-csomópontjaira alkalmazza a biztonsági javításokat. Azonban Ön felelős annak biztosításáért, hogy a Linux-csomópontok szükség szerint újraindulnak. Több lehetőség is van a csomópontok újraindítására:

- Manuálisan, a Azure Portalon vagy az Azure CLI-n keresztül.
- Az AK-fürt frissítésével. A fürt automatikusan frissíti a [Cordon lévő és a kiürítési csomópontokat][cordon-drain] , majd egy új csomópontot online állapotba helyez a legújabb Ubuntu-lemezképpel és egy új patch-verzióval vagy egy kisebb Kubernetes-verzióval. További információ: AK- [fürt frissítése][aks-upgrade].
- A [csomópontok rendszerképének frissítésével](node-image-upgrade.md).

### <a name="windows-server-nodes"></a>Windows Server-csomópontok

A Windows Server-csomópontok esetében Windows Update nem fut automatikusan, és nem alkalmazza a legújabb frissítéseket. A Windows Update kiadási ciklus és a saját ellenőrzési folyamata körüli rendszeres ütemterv esetén frissíteni kell a fürtöt és a Windows Server-csomópontok készletét az AK-fürtben. Ez a frissítési folyamat olyan csomópontokat hoz létre, amelyek a legújabb Windows Server-lemezképet és-javításokat futtatják, majd eltávolítja a régebbi csomópontokat. További információ erről a folyamatról: [Node-készlet frissítése az AK-ban][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Miért jött létre két erőforráscsoport az AK-val?

Az AK számos Azure-infrastruktúra-erőforrásra épül, beleértve a virtuálisgép-méretezési csoportokat, a virtuális hálózatokat és a felügyelt lemezeket. Ez lehetővé teszi az Azure platform számos alapvető funkciójának kihasználása az AK által biztosított felügyelt Kubernetes-környezeten belül. Például az Azure-beli virtuálisgép-típusok többsége közvetlenül is használható az AK-val, és a Azure Reservations használatával automatikusan kedvezményeket kaphat ezekről az erőforrásokról.

Az architektúra engedélyezéséhez minden AK-beli telepítés két erőforráscsoportot ölel fel:

1. Hozza létre az első erőforráscsoportot. Ez a csoport csak a Kubernetes szolgáltatás erőforrását tartalmazza. Az AK erőforrás-szolgáltatója automatikusan létrehozza a második erőforráscsoportot az üzembe helyezés során. A második erőforráscsoport példája *MC_myResourceGroup_myAKSCluster_eastus*. A második erőforráscsoport nevének megadásával kapcsolatos információkért tekintse meg a következő szakaszt.
1. A második erőforráscsoport, azaz a csomópont- *erőforráscsoport* a fürthöz társított összes infrastruktúra-erőforrást tartalmazza. Ezek az erőforrások magukban foglalják a Kubernetes csomópontos virtuális gépeket, a virtuális hálózatkezelést és a tárterületet. Alapértelmezés szerint a csomópont-erőforráscsoport neve például *MC_myResourceGroup_myAKSCluster_eastus*. Az AK automatikusan törli a csomópont-erőforrást, amikor a fürt törlődik, ezért csak olyan erőforrásokhoz használható, amelyek osztoznak a fürt életciklusán.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Megadhatom a saját nevet az AK-csomópont erőforráscsoporthoz?

Igen. Alapértelmezés szerint a (z) *MC_resourcegroupname_clustername_location* csomópont-erőforráscsoport neve lesz, de a saját nevét is megadhatja.

A saját erőforráscsoport-név megadásához telepítse az [AK – előzetes][aks-preview-cli] VERZIÓJÚ Azure CLI-bővítmény *0.3.2* vagy újabb verzióját. Amikor egy AK-fürtöt hoz létre az az [AK Create][az-aks-create] paranccsal, használja a `--node-resource-group` paramétert, és adja meg az erőforráscsoport nevét. Ha [Azure Resource Manager sablont használ][aks-rm-template] egy AK-fürt üzembe helyezéséhez, megadhatja az erőforráscsoport nevét a *nodeResourceGroup* tulajdonság használatával.

* Az Azure erőforrás-szolgáltató automatikusan létrehozza a másodlagos erőforráscsoportot a saját előfizetésében.
* Egyéni erőforráscsoport-nevet csak a fürt létrehozásakor adhat meg.

A csomópont-erőforráscsoport használata során ne feledje, hogy a következők nem használhatók:

* Válasszon egy meglévő erőforráscsoportot a csomópont-erőforráscsoport számára.
* Eltérő előfizetést kell megadni a csomópont-erőforráscsoport számára.
* Módosítsa a csomópont-erőforráscsoport nevét a fürt létrehozása után.
* A csomópont erőforráscsoporthoz tartozó felügyelt erőforrások nevének megadása.
* Módosíthatja vagy törölheti az Azure-ban létrehozott, felügyelt erőforrások címkéit a csomópont-erőforráscsoporton belül. (További információt a következő szakaszban talál.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Módosíthatom a csomóponti erőforráscsoporthoz tartozó AK-erőforrások címkéit és egyéb tulajdonságait?

Ha módosítja vagy törli az Azure által létrehozott címkéket és az egyéb erőforrás-tulajdonságokat a csomópont-erőforráscsoport esetében, váratlan eredményekhez juthat, például a skálázási és a frissítési hibákhoz. Az AK lehetővé teszi a végfelhasználók által létrehozott egyéni címkék létrehozását és módosítását, és ezeket a címkéket felveheti [a csomópont-készlet létrehozásakor](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool). Előfordulhat, hogy egyéni címkéket kíván létrehozni vagy módosítani, például egy üzleti egység vagy költséghely hozzárendeléséhez. Ezt úgy is megteheti, hogy az Azure-szabályzatokat a felügyelt erőforráscsoport hatókörével hozza létre.

Ha azonban az AK-fürt csomópont-erőforráscsoport területén lévő erőforrásokhoz tartozó összes **Azure-beli címkét** módosítja, nem támogatott művelet, amely megtöri a szolgáltatási szint célkitűzését (SLO). További információkért lásd: az [AK-t kínáló szolgáltatás szintű szerződés?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Milyen Kubernetes-beléptetési vezérlőket támogat az AK? Hozzáadhatók vagy eltávolíthatók a beléptetési vezérlők?

Az AK a következő [belépésvezérlés-vezérlőket][admission-controllers]támogatja:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *PodNodeSelector*
- *PodTolerationRestriction*
- *ExtendedResourceToleration*

Jelenleg nem módosítható a belépésvezérlés az AK-ban.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Használhatom a belépésvezérlés webhookokat az AK-on?

Igen, használhatja a belépésvezérlés webhookokat az AK-ban. Javasoljuk, hogy zárja ki a **vezérlő-sík címkével** megjelölt belső AK-névtereket. Például az alábbi hozzáadásával a webhook-konfigurációhoz:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

Az AK tűzfalakkal védi az API-kiszolgáló kimenő adatait, így a belépésvezérlés webhookoknak elérhetőknek kell lenniük a fürtön belülről.

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>A beléptetési vezérlő webhookok hatással vannak a Kube-rendszerek és a belső AK-beli névterekre?

A rendszer stabilitásának biztosítása és az egyéni belépésvezérlés megakadályozása a Kube-rendszer belső szolgáltatásainak befolyásolása érdekében a névtér AK-ban van egy **beléptetési végrehajtó**, amely automatikusan kizárja a Kube-rendszer és az AK belső névtereit. Ez a szolgáltatás biztosítja, hogy az egyéni belépésvezérlés ne befolyásolja a Kube-rendszeren futó szolgáltatásokat.

Ha olyan kritikus használati esettel rendelkezik, amely a Kube-rendszeren (nem ajánlott) van telepítve, amelyet az egyéni beléptetési webhooknak kell megadnia, akkor az alábbi címkét vagy jegyzetet is hozzáadhatja, hogy a felvételi kényszerítő figyelmen kívül hagyja azt.

Címke: ```"admissions.enforcer/disabled": "true"``` vagy Megjegyzés: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Integrálva van Azure Key Vault AK-val?

Az AK jelenleg nincs natív módon integrálva a Azure Key Vaultba. A [CSI Secrets áruház Azure Key Vault szolgáltatója][csi-driver] azonban lehetővé teszi a közvetlen integrációt a Kubernetes-hüvelyből a Key Vault titkokba.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Futtathatok Windows Server-tárolókat az AK-ban?

Igen, a Windows Server-tárolók elérhetők az AK-ban. A Windows Server-tárolók az AK-ban való futtatásához létre kell hoznia egy Windows Servert futtató csomópont-készletet a vendég operációs rendszerként. A Windows Server-tárolók csak a Windows Server 2019-es kiszolgálókat használhatják. Első lépésként tekintse meg a következőt: [AK-fürt létrehozása Windows Server Node-készlettel][aks-windows-cli].

A Windows Server rendszerhez készült csomópont-támogatás olyan korlátozásokat tartalmaz, amelyek a felsőbb rétegbeli Windows-kiszolgáló részét képezik a Kubernetes projektben. További információ ezekről a korlátozásokról: a [Windows Server-tárolók AK-beli korlátozásai][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Biztosít-e az AK szolgáltatás szintű szerződést?

Az AK SLA-garanciákat biztosít opcionális kiegészítő szolgáltatásként, amely a [rendelkezésre állási SLA][uptime-sla]-t tartalmazza. 

Az alapértelmezés szerint kínált ingyenes SLA nem garantálja a magas rendelkezésre állású API Server-végpontot (a szolgáltatási szint célkitűzése 99,5%). Előfordulhat, hogy a frissítés, a nem kifogástalan állapotú alátét csomópontok, a platformok karbantartása stb. Ha a munkaterhelés nem tűri a APIServer újraindítását, javasoljuk, hogy használja a rendelkezésre állási SLA-t.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Alkalmazhatom az Azure-beli foglalási kedvezményeket az AK-ügynökök csomópontjaira?

Az AK-ügynökök csomópontjai standard Azure-beli virtuális gépekként vannak kiszámlázva, így ha az AK-ban használt virtuálisgép-mérethez vásárolt [Azure-foglalást][reservation-discounts] , a rendszer automatikusan alkalmazza ezeket a kedvezményeket.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Áthelyezhetem/Áttelepíthetem a fürtöt az Azure-bérlők között?

Az AK-fürt bérlők közötti áthelyezése jelenleg nem támogatott.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Áthelyezhetem/Áttelepíthetem a fürtöt az előfizetések között?

A fürtök előfizetések közötti áthelyezése jelenleg nem támogatott.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Áthelyezhetem az AK-fürtöket az aktuális Azure-előfizetésből egy másikba?

Az AK-fürt és az Azure-előfizetések közötti kapcsolódó erőforrások áthelyezése nem támogatott.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Áthelyezhetem az AK-beli vagy AK-beli infrastruktúra-erőforrásokat más erőforráscsoportokba, vagy átnevezhetik őket?

Az AK-fürt és a hozzá tartozó erőforrások áthelyezése vagy átnevezése nem támogatott.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Miért törli a fürtem a munkaidőt?

A rendszer a legtöbb fürtöt törli a felhasználói kérelem után; bizonyos esetekben – különösen abban az esetben, ha az ügyfelek a saját erőforráscsoportot használják, vagy ha a több-RG feladatok törlésével – további időt vagy hibát okozhatnak. Ha probléma merül fel a törléssel kapcsolatban, ellenőrizze, hogy nincsenek-e zárolások a RG-on, hogy a RG-on kívüli összes erőforrás társítva van-e a RG-ból, és így tovább.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Ha a "NodeLost" vagy az "Unknown" állapotú Pod/üzemelő példányok továbbra is frissíthetem a fürtöt?

De az AK nem javasolja ezt. A frissítéseket akkor kell elvégezni, ha a fürt állapota ismert és kifogástalan állapotú.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Ha egy vagy több csomóponttal rendelkező fürt nem kifogástalan állapotú vagy leáll, frissíthetem a frissítést?

Nem, a frissítés előtt törölje vagy távolítsa el a meghibásodott állapotú csomópontokat, vagy más módon távolítsa el azokat a fürtből.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Futtattam egy fürtöt, de a következő hibaüzenet jelenik meg: `[Errno 11001] getaddrinfo failed`

Ezt általában az okozza, hogy a felhasználók egy vagy több hálózati biztonsági csoporttal (NSG) rendelkeznek még használatban, és a fürthöz vannak társítva.  Távolítsa el őket, és próbálkozzon újra a törléssel.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Futtattam egy frissítést, de most a hüvelyem összeomlott hurkokban van, és a készültségi mintavétel meghiúsul?

Erősítse meg, hogy a szolgáltatásnév nem járt le.  Lásd: az AK-beli [szolgáltatásnév](./kubernetes-service-principal.md) és az AK-s [frissítési hitelesítő adatai](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>A fürtem működik, de hirtelen nem lehet kiépíteni a LoadBalancers, a csatlakoztatási és a PVC-ket?

Erősítse meg, hogy a szolgáltatásnév nem járt le.  Lásd: az AK-beli [szolgáltatásnév](./kubernetes-service-principal.md)  és az AK-s [frissítési hitelesítő adatai](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Lehet nullára méretezni az AK-fürtöt?
A [futó AK-fürtök teljes mértékben leállíthatók](start-stop-cluster.md), így a megfelelő számítási költségek is megtekinthetők. Emellett dönthet úgy is, hogy az [összes vagy adott `User` csomópont-készletet 0-ra méretezi vagy Automatikus méretezéssel](scale-cluster.md#scale-user-node-pools-to-0) , csak a szükséges fürtkonfiguráció fenntartásával.
A [rendszercsomópont-készletek](use-system-pools.md) nem méretezhetők közvetlenül a nullára.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Használhatom a virtuálisgép-méretezési csoport API-jait a manuális méretezéshez?

Nem, a virtuálisgép-méretezési csoport API-jai nem támogatják a méretezési műveleteket. Használja az AK API-kat ( `az aks scale` ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>Használhatom a virtuálisgép-méretezési csoportokat a nulla csomópontokra való manuális méretezéshez?

Nem, a virtuálisgép-méretezési csoport API-jai nem támogatják a méretezési műveleteket. Az AK API-val a nem rendszerszintű, illetve a [fürt leállítása](start-stop-cluster.md) nem végezhető el.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Leállíthatom vagy Lefoglalhatom az összes virtuális gépet?

Míg az AK rugalmassági mechanizmusokkal rendelkezik, amelyek egy ilyen konfigurációt és helyreállítást végeznek, ez nem támogatott. Ehelyett [állítsa le a fürtöt](start-stop-cluster.md) .

## <a name="can-i-use-custom-vm-extensions"></a>Használhatok egyéni virtuálisgép-bővítményeket?

A Log Analytics ügynök támogatott, mert a Microsoft által felügyelt bővítmény. Ellenkező esetben nem, az AK felügyelt szolgáltatás, és a IaaS-erőforrások kezelése nem támogatott. Egyéni összetevők telepítéséhez használja a Kubernetes API-kat és mechanizmusokat. Például a DaemonSets használatával telepítse a szükséges összetevőket.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>Az AK a fürt régióján kívül tárolja a vásárlói adatmennyiséget?

Az ügyféladatok egyetlen régióban való tárolását lehetővé tevő szolgáltatás jelenleg csak a Ázsia és a Csendes-óceáni térség geo Délkelet-ázsiai régiójában (Szingapúr) érhető el. Az összes többi régió esetében az ügyféladatokat a Geo tárolja.

## <a name="are-aks-images-required-to-run-as-root"></a>A root-ként való futtatáshoz AK-lemezképek szükségesek?

A következő két kép kivételével az AK-lemezképek nem szükségesek root-ként való futtatáshoz:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Mi az az Azure CNI transzparens mód vagy a Bridge mód?

A v 1.2.0 az Azure CNI az egyszeri bérletű linuxos CNI-környezetek esetében az alapértelmezett transzparens mód lesz. Az átlátszó mód a híd üzemmódot váltja fel. Ebben a szakaszban a két mód különbségeit tárgyaljuk, valamint a transzparens üzemmód Azure CNI való használatának előnyeit és korlátozásait.

### <a name="bridge-mode"></a>Híd mód

Ahogy a neve is sugallja, az Azure CNI-híd üzemmód "just in Time" módban létrehoz egy "azure0" nevű L2 hidat. A rendszer az összes gazdagép oldali Pod `veth` pár csatolót csatlakoztatja ehhez a hídhoz. Így Pod-Pod a virtuális gépeken belüli kommunikáció és a fennmaradó forgalom ezen a hídon halad át. A szóban forgó híd egy 2. rétegbeli virtuális eszköz, amely a saját tulajdonában nem tud semmit fogadni vagy továbbítani, kivéve, ha egy vagy több valós eszközt köt hozzá. Emiatt a linuxos virtuális gép ETH0-át át kell alakítani egy alárendeltre a "azure0"-hídra. Ez egy összetett hálózati topológiát hoz létre a Linux rendszerű virtuális gépen, és a CNI is gondoskodnia kell más hálózati funkciókról, például a DNS-kiszolgáló frissítéséről és így tovább.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Híd üzemmód topológiája":::

Az alábbi példa azt szemlélteti, hogyan néz ki az IP-útvonal beállítása a Bridge módban. Függetlenül attól, hogy hány hüvely van a csomóponton, csak két útvonal lesz. Az első, ami azt jelzi, hogy az azure0-n kívüli összes forgalom az alhálózat alapértelmezett átjáróján keresztül az IP "src 10.240.0.4" (amely a csomópont elsődleges IP-címe), a másik pedig "10.20. x. x" Pod tér a rendszermagba, hogy eldöntse.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Transzparens mód
Az átlátszó mód a Linux hálózatkezelés beállításához szükséges egyszerű megközelítést veszi igénybe. Ebben a módban az Azure CNI nem változtatja meg a Linux rendszerű virtuális gépen elérhető ETH0-interfészek tulajdonságait. A Linux hálózati tulajdonságok megváltoztatásának minimális megközelítése segít csökkenteni a komplex sarki problémák mennyiségét, amelyekkel a fürtök a híd móddal szembesülnek. Transzparens módban az Azure CNI létrehoz és hozzáad egy gazdagép-oldali Pod `veth` pair-felületet, amelyet a rendszer hozzáad a gazdagéphez. A virtuális gépen belüli Pod-Pod kommunikáció a CNI által hozzáadott IP-útvonalakon keresztül történik. Lényegében a pod-Pod kommunikáció a 3. rétegbeli és a pod-forgalmat az L3-útválasztási szabályok irányítják.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Transzparens üzemmódú topológia":::

Az alábbi példa egy transzparens üzemmódú IP-útvonal beállítását mutatja be, az egyes Pod-interfészek egy statikus útvonalat kapnak, hogy a megjelenő IP-címmel ellátott forgalom közvetlenül a pod Host Side pair-felületén legyen elküldve `veth` .

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

### <a name="benefits-of-transparent-mode"></a>Az átlátszó mód előnyei

- A `conntrack` DNS párhuzamos versenyhelyzet mérséklését és az 5 mp-es DNS-késési problémák elkerülését teszi lehetővé anélkül, hogy a csomópont helyi DNS-t kellene beállítani (a csomópont helyi DNS-t továbbra is használhatja teljesítménybeli okokból).
- Kiküszöböli a kezdeti 5 mp-es DNS-késés CNI híd üzemmódját a mai napon, a híd beállítása miatt.
- A Bridge Mode egyik sarki esete az, hogy az Azure-CNI nem tudja megőrizni a felhasználók által a VNET vagy a NIC-be való hozzáadáshoz hozzáadott egyéni DNS-kiszolgáló frissítését. Ez azt eredményezi, hogy a CNI csak a DNS-kiszolgálók listájának első példányát vette fel. Transzparens módban megoldott, mivel a CNI nem változtatja meg a ETH0-tulajdonságokat. [Itt](https://github.com/Azure/azure-container-networking/issues/713)talál további információt.
- Az UDP-forgalom hatékonyabb kezelését és az UDP-beli adatváltozások enyhítését teszi lehetővé az ARP időtúllépése esetén. A Bridge módban, ha a híd nem ismeri a cél pod MAC-címeit a virtuális gépen belüli Pod-Pod kommunikációban, a terv szerint ez a csomag minden portra kiterjed. Transzparens módban megoldott, mert az elérési útban nem találhatók L2-eszközök. [Itt](https://github.com/Azure/azure-container-networking/issues/704)talál további információt.
- Az átlátszó mód jobb teljesítményt nyújt a virtuális gépen belüli Pod-to-Pod kommunikációban az átviteli sebesség és a késés tekintetében a híd üzemmódhoz képest.

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>Hogyan lehet elkerülni a jogosultságok tulajdonlását, ha a kötet sok fájllal rendelkezik?

Hagyományosan, ha a pod nem legfelső szintű felhasználóként fut (amit érdemes), meg kell adnia a `fsGroup` Pod biztonsági környezet belsejében, hogy a kötet olvasható és írható legyen a pod használatával. Ezt a követelményt az [itt](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)részletesebben tárgyaljuk.

A beállítás egyik mellékhatása azonban az `fsGroup` , hogy minden alkalommal, amikor egy kötet csatlakoztatva van, a Kubernetes rekurzív módon kell lennie, `chown()` és a `chmod()` köteten belüli összes fájlt és könyvtárat – az alábbiakban néhány kivétellel. Ez akkor is megtörténik, ha a kötet tulajdonjoga már megfelel a kértnek `fsGroup` , és nagyon drága a nagy mennyiségű kis fájlokkal, ami hosszú időt okoz a pod indításakor. Ez a forgatókönyv egy ismert probléma volt a v 1.20 előtt, és a megkerülő megoldás a pod futtató gyökérként való futtatását állítja be:

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

A problémát a Kubernetes v 1.20 oldotta meg, tekintse meg a [Kubernetes 1,20: a mennyiségi engedélyek részletesebb szabályozása](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/) című témakört.


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
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
