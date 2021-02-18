---
title: Gyakori kérdések az Azure Red Hat OpenShift
description: A Microsoft Azure Red Hat OpenShift kapcsolatos gyakori kérdésekre adott válaszok
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: a3721083e48774963cd761178abdb552c93b15c7
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634345"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift – gyakori kérdések

Ez a cikk a Microsoft Azure Red Hat OpenShift kapcsolatos gyakori kérdéseket (GYIK) válaszolja meg.

## <a name="installation-and-upgrade"></a>Telepítés és frissítés

### <a name="which-azure-regions-are-supported"></a>Mely Azure-régiók támogatottak?

Az Azure Red Hat OpenShift 4. x támogatott régiói listáját az [elérhető régiók](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all)részben tekintheti meg.

Az Azure Red Hat OpenShift 3,11 támogatott régiói listáját itt tekintheti meg: [régiókban elérhető termékek](supported-resources.md#azure-regions).

### <a name="what-virtual-machine-sizes-can-i-use"></a>Milyen virtuálisgép-méreteket használhatok?

Az Azure Red Hat OpenShift 4 által támogatott virtuálisgép-méretek listáját lásd: [Az Azure Red Hat OpenShift 4 támogatott erőforrásai](support-policies-v4.md).

Az Azure Red Hat OpenShift 3,11 által támogatott virtuálisgép-méretek listáját lásd: az [Azure Red Hat OpenShift 3,11 támogatott erőforrásai](supported-resources.md).

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Mi a hüvelyek maximális száma egy Azure Red Hat OpenShift-fürtben?  Mekkora a hüvelyek maximális száma egy csomóponton az Azure Red Hat OpenShift?

A támogatott hüvelyek tényleges száma az alkalmazás memóriájának, PROCESSZORának és tárolási követelményeitől függ.

Az Azure Red Hat OpenShift 4. x 250 Pod-csomópontos korláttal és egy 100-os számítási csomóponti korláttal rendelkezik. A fürtben támogatott hüvelyek maximális száma a 250 &times; 100 = 25 000.

Az Azure Red Hat OpenShift 3,11 egy 50-es Pod-csomópontos korláttal és 20 számítási csomópontos korláttal rendelkezik. A fürtben támogatott hüvelyek maximális száma a 50 &times; 20 = 1 000.

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Lehet egy fürt több Azure-régióban is számítási csomópontokkal rendelkezik?

Nem. Egy Azure Red Hat OpenShift-fürt minden csomópontjának ugyanabból az Azure-régióból kell származnia.

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>Több rendelkezésre állási zónában is üzembe helyezhetők a fürtök?

Igen. Ez automatikusan megtörténik, ha a fürt egy olyan Azure-régióban van telepítve, amely támogatja a rendelkezésre állási zónákat. További információ: [rendelkezésre állási zónák](../availability-zones/az-overview.md#availability-zones).

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>A vezérlési sík csomópontjai el vannak-e látva az Azure Kubernetes szolgáltatással (ak)?

Nem. Az összes erőforrás, beleértve a fürt fő csomópontjait is, az ügyfél-előfizetésben fut. Az ilyen típusú erőforrások írásvédett erőforráscsoporthoz kerülnek.

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>A fürt egy ügyfél-előfizetésben található? 

Az Azure által felügyelt alkalmazás egy zárolt erőforráscsoport és az ügyfél-előfizetés között él. Az ügyfelek megtekinthetik az adott erőforráscsoporthoz tartozó objektumokat, de nem módosíthatják azokat.

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>Van olyan elem az Azure Red Hat OpenShift, amelyet más ügyfelek is megosztanak? Vagy minden független?

Minden egyes Azure Red Hat OpenShift-fürt egy adott ügyfélhez van hozzárendelve, és az ügyfél előfizetésén belül él. 

### <a name="are-infrastructure-nodes-available"></a>Érhetők el infrastruktúra-csomópontok?

Az Azure Red Hat OpenShift 4. x fürtökön jelenleg nem érhetők el infrastruktúra-csomópontok.

Az Azure Red Hat OpenShift 3,11-fürtökön alapértelmezés szerint az infrastruktúra-csomópontok is szerepelnek.

## <a name="how-do-i-handle-cluster-upgrades"></a>Hogyan kezeli a fürt frissítéseit?

További információ a frissítésekről, a karbantartásról és a támogatott verziókról: [támogatási életciklus – útmutató](support-lifecycle.md).

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>Hogyan fogja frissíteni a gazda operációs rendszer és a OpenShift szoftverét?

A gazda operációs rendszerek és a OpenShift szoftverek frissülnek, mivel az Azure Red Hat OpenShift a másodlagos OpenShift-tároló platformból származó kisebb kiadási verziókat és javításokat használnak.

### <a name="whats-the-process-to-reboot-the-updated-node"></a>Mi a folyamat a frissített csomópont újraindítására?

A csomópontok a frissítés részeként újraindulnak.

## <a name="cluster-operations"></a>Fürtműveletek

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>Használhatom a Prometheus-t az alkalmazásaim figyelésére?

A Prometheus előre telepítve és konfigurálva van az Azure Red Hat OpenShift 4. x fürtökhöz. További információ a [fürtök figyeléséről](https://docs.openshift.com/container-platform/4.6/operators/operator_sdk/osdk-monitoring-prometheus.html).

Az Azure Red Hat OpenShift 3,11-fürtök esetében a Prometheus-t üzembe helyezheti a névtérben, és figyelheti az alkalmazásokat a névtérben. További információ: [a Prometheus-példány üzembe helyezése az Azure Red Hat OpenShift-fürtben](howto-deploy-prometheus.md).

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Használhatom a Prometheus-t a fürt állapotával és kapacitásával kapcsolatos metrikák figyelésére?

Az Azure Red Hat OpenShift 4. x: igen.

Az Azure Red Hat OpenShift 3,11: nem.

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Az alapul szolgáló virtuális gépek naplófájljai továbbítva lesznek egy ügyfél-naplózási elemzési rendszerbe?

Az alapul szolgáló virtuális gépek naplófájljait a felügyelt szolgáltatás kezeli, és az ügyfelek számára nem érhető el.

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>Hogyan érhetik el az ügyfelek az olyan mérőszámokat, mint a CPU/memória a csomópont szintjén, hogy a méretezési, hibakeresési és egyéb problémákhoz hasonló műveleteket végezzenek. Nem úgy tűnik, hogy a kubectl-t egy Azure Red Hat OpenShift-fürtön futtatom.

Az Azure Red Hat OpenShift 4. x fürtök esetében a OpenShift webkonzol a csomópont szintjén található összes mérőszámot tartalmazza. További információ: Red Hat dokumentáció a [fürt adatainak megtekintéséhez](https://docs.openshift.com/container-platform/4.6/web_console/using-dashboard-to-get-cluster-information.html).

Az Azure Red Hat OpenShift 3,11-fürtök esetében az ügyfelek a következő paranccsal érhetik el a CPU-/memória-metrikákat a csomópont szintjén: parancs `oc adm top nodes` vagy `kubectl top nodes` az ügyfél-rendszergazda fürt szerepkör. Az ügyfelek a (z) parancshoz tartozó CPU-/memória-metrikákat is használhatják `pods` `oc adm top pods` `kubectl top pods` .

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Ha felskálázást végez az üzembe helyezés során, hogyan képezhetők le az Azure-beli tartalék tartományok a pod-elhelyezésbe, hogy a szolgáltatáshoz tartozó összes hüvely ne legyen kiütéssel egyetlen tartalék tartomány meghibásodása esetén?

A virtuálisgép-méretezési csoportok az Azure-ban való használatakor alapértelmezés szerint öt tartalék tartomány van. A méretezési csoportokban lévő virtuálisgép-példányok az egyik tartalék tartományba kerülnek. Ez biztosítja, hogy a fürt számítási csomópontjain üzembe helyezett alkalmazások külön tartalék tartományokban legyenek elhelyezve.

További információ: [a virtuálisgép-méretezési csoporthoz tartozó tartalék tartományok megfelelő számának kiválasztása](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md).

### <a name="is-there-a-way-to-manage-pod-placement"></a>Van mód a pod elhelyezés kezelésére?

Ügyfeleinknek lehetősége van a csomópontok beszerzésére és a címkék megtekintésére ügyfél-rendszergazdaként. Ez lehetőséget biztosít a méretezési csoportba tartozó bármely virtuális gép célzására.

Adott címkék használatakor körültekintően kell eljárni:

- Az állomásnév nem használható. Az állomásnév gyakran a frissítésekkel és frissítésekkel lesz elforgatva, és a változás garantált.
- Ha az ügyfél egy adott címkére vagy egy központi telepítési stratégiára vonatkozó kéréssel rendelkezik, akkor ez a megoldás a mérnöki erőfeszítésekhez szükséges, és jelenleg nem támogatott.

További információ: a [Pod elhelyezésének szabályozása](https://docs.openshift.com/container-platform/4.6/nodes/scheduling/nodes-scheduler-about.html).

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>A rendszerkép beállításjegyzéke külsőleg is elérhető, így olyan eszközöket is használhatok, mint például a Jenkins?

4. x fürtök esetén ki kell tenni egy biztonságos beállításjegyzéket, és konfigurálnia kell a hitelesítést. További információt a következő Red Hat dokumentációban talál:

- [Beállításjegyzék kimutatása](https://docs.openshift.com/container-platform/4.6/registry/securing-exposing-registry.html)
- [A beállításjegyzék elérése](https://docs.openshift.com/container-platform/4.6/registry/accessing-the-registry.html)

3,11-fürtök esetén a Docker-rendszerkép beállításjegyzéke elérhető. A Docker-beállításjegyzék elérhető innen: `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` . Azure Container Registry is használhatja.

## <a name="networking"></a>Hálózatkezelés

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Telepíthetek fürtöt meglévő virtuális hálózatra?

4. x fürtben a fürtöket meglévő VNet is üzembe helyezheti.

3,11-fürtökben nem telepíthet fürtöt meglévő VNet. Egy Azure Red Hat OpenShift 3,11-fürtöt összekapcsolhatók egy meglévő VNet a peering használatával.

### <a name="is-cross-namespace-networking-supported"></a>Támogatott-e a névterek közötti hálózatkezelés?

Az ügyfél és az egyéni projekt rendszergazdája testre szabható a névterek közötti hálózatkezelés (beleértve a Megtagadás megtagadását is) az `NetworkPolicy` objektumok használatával projektenként.

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Egy másik előfizetésben próbálok egyenrangú virtuális hálózatra csatlakozni, de nem sikerült lekérni a VNet CIDR-hibát.

Győződjön meg arról, hogy a virtuális hálózatot tartalmazó előfizetésben regisztrálja a `Microsoft.ContainerService` szolgáltatót a következő paranccsal: `az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Megadhatjuk az IP-címtartományok üzembe helyezését a privát VNet, így elkerülhetők a többi vállalati virtuális hálózatok való ütközések?

4. x-fürtökben megadhatja saját IP-tartományait.

3,11-fürtökben az Azure Red Hat OpenShift támogatja a VNet-társítást, és lehetővé teszi, hogy az ügyfél VNet biztosítson a partnernek és egy VNet-CIDR, amelyben a OpenShift-hálózat működni fog.

Az Azure Red Hat OpenShift által létrehozott VNet védettek lesznek, és nem fogadják el a konfigurációs módosításokat. A VNet az ügyfél vezérli, és az előfizetésében található.

### <a name="is-the-software-defined-network-module-configurable"></a>Konfigurálható a szoftveresen definiált hálózati modul?

A szoftver által definiált hálózat `openshift-ovs-networkpolicy` nem konfigurálható.

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Milyen Azure Load balancert használ az Azure Red Hat OpenShift?  Standard vagy alapszintű, és ez konfigurálható?

Az Azure Red Hat OpenShift standard Azure Load Balancer használ, és nem konfigurálható.

## <a name="permissions"></a>Engedélyek

### <a name="can-an-admin-manage-users-and-quotas"></a>A rendszergazdák kezelhetik a felhasználókat és a kvótákat?

Igen. Az Azure Red Hat OpenShift rendszergazdája a felhasználók és a kvóták kezelését is képes kezelni az összes felhasználó által létrehozott projekthez való hozzáférés mellett.

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Korlátozható a fürt csak bizonyos Azure AD-felhasználókra?

Igen. Az Azure ad-alkalmazás konfigurálásával korlátozhatja, hogy mely Azure AD-felhasználók tudnak bejelentkezni a fürtbe. További információ [: How to: az alkalmazás korlátozása felhasználói csoportra](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md).

### <a name="can-i-restrict-users-from-creating-projects"></a>Korlátozható a felhasználók projekt létrehozása?

Igen. Jelentkezzen be a fürtbe rendszergazdaként, és hajtsa végre a következő parancsot:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

További információkért tekintse meg a fürt verziójának önálló kiépítés letiltására vonatkozó OpenShift dokumentációját:

- [Önálló kiépítés letiltása 4,6-fürtökben](https://docs.openshift.com/container-platform/4.6/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [Önálló kiépítés letiltása 3,11-fürtökben](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Mely UNIX-jogosultságok érhetők el a Masters/infra/app-csomópontok számára a IaaS-ben?

4. x fürtök esetén a csomópont-hozzáférés a fürt – rendszergazda szerepkörön keresztül érhető el. További információ: [KUBERNETES RBAC – áttekintés](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html).

3,11-fürtök esetében a Node-hozzáférés tiltott.

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Milyen OCP-jogosultságokkal rendelkezik? Fürt – rendszergazda? Projekt – rendszergazda?

4. x fürtök esetén a fürt – rendszergazda szerepkör elérhető. További információ: [KUBERNETES RBAC – áttekintés](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html).

3,11-fürtök esetében további részletekért tekintse meg a [Fürtfelügyelő áttekintését](https://docs.openshift.com/aro/admin_guide/index.html) .

### <a name="which-identity-providers-are-available"></a>Mely identitás-szolgáltatók érhetők el?

4. x fürtök esetén a saját identitás-szolgáltatót kell konfigurálnia. További információ: a Red Hat dokumentációja az [identitás-szolgáltatók konfigurálásáról](https://docs.openshift.com/container-platform/4.6/authentication/identity_providers/configuring-ldap-identity-provider.html).

3,11-fürtök esetében az Azure AD-integrációt használhatja. 

## <a name="storage"></a>Tárolás

### <a name="is-data-on-my-cluster-encrypted"></a>A fürtön lévő adatai titkosítva vannak?

Alapértelmezés szerint az adatok titkosítva vannak a nyugalmi állapotban. Az Azure Storage platform automatikusan titkosítja az adatait, mielőtt megőrzi azt, és visszafejti az adatok beolvasása előtt. További információ: [Azure Storage Service encryption for](../storage/common/storage-service-encryption.md)inaktív adatok.

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>A etcd-ben tárolt adatforgalom az Azure Red Hat OpenShift van titkosítva?

Az Azure Red Hat OpenShift 4 fürtök esetében az adattitkosítás alapértelmezés szerint nem történik meg, de engedélyezheti a titkosítást. További információ: a [etcd titkosításának](https://docs.openshift.com/container-platform/4.6/security/encrypting-etcd.html)útmutatója.

3,11-fürtök esetében az adattitkosítás nem a etcd szinten történik. A titkosítás bekapcsolásának lehetősége jelenleg nem támogatott. A OpenShift támogatja ezt a funkciót, de mérnöki erőfeszítésekre van szükség a közúti térképen való működéshez. Az adatátvitel a lemez szintjén történik. További információkért tekintse meg az [adatok titkosítása az adattár rétegében](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) című témakört.

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Választhatunk bármilyen állandó tárolási megoldást, például a OCS-t? 

4. x fürtök esetében az Azure Disk (Premium_LRS) alapértelmezett tárolási osztályként van konfigurálva. További tárhely-szolgáltatók esetén, valamint a konfigurációs adatokhoz (beleértve az Azure-fájlt is) tekintse meg a Red Hat dokumentációját az [állandó tárterületen](https://docs.openshift.com/container-platform/4.6/storage/understanding-persistent-storage.html).

3,11-fürtök esetében alapértelmezés szerint két tárolási osztály van megadva: az egyik az Azure Disk (Premium_LRS) és egy Azure-fájl.

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>Az ARO a fürt régióján kívül tárolja a vásárlói adatmennyiséget?

Nem. Az ARO-fürtben létrehozott összes adathalmaz a fürt régiójában marad.
