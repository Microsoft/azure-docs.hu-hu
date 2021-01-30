---
title: Gyakori kérdések
description: Választ ad az Azure VMware megoldással kapcsolatos gyakori kérdésekre.
ms.topic: conceptual
ms.date: 1/14/2021
ms.openlocfilehash: 090b29b1e013ffbceb9296250fed99a04d15a82c
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062087"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>Gyakran ismételt kérdések az Azure VMware-megoldásról

Ebben a cikkben az Azure VMware-megoldással kapcsolatos gyakori kérdésekre fogunk válaszolni.

## <a name="general"></a>Általános

### <a name="what-is-azure-vmware-solution"></a>Mi az az Azure VMware Solution?

Mivel a vállalatok az informatikai modernizációs stratégiákat követve javítják az üzleti rugalmasságot, csökkentik a költségeket, és felgyorsítják az innovációt, a hibrid felhőalapú platformok az ügyfelek digitális átalakításának kulcsfontosságú segítői jelentek meg. Az Azure VMware megoldás a VMware Software-Defined adatközpont-(SDDC-) szoftverét ötvözi a Microsoft Azure globális felhőalapú szolgáltatás-ökoszisztémával. Az Azure VMware megoldás a teljesítményre, a rendelkezésre állásra, a biztonságra és a megfelelőségre vonatkozó követelmények teljesítésére szolgál.

## <a name="azure-vmware-solution-service"></a>Azure VMware megoldás szolgáltatás

### <a name="where-is-azure-vmware-solution-available-today"></a>Hol érhető el a jelenleg elérhető Azure VMware-megoldás?

A szolgáltatás folyamatosan bővül az új régiókban, ezért további részletekért tekintse meg a [legújabb szolgáltatás elérhetőségi információit](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) . 

### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>Az Azure VMware-megoldás példányain futó munkaterhelések az Azure-szolgáltatásokkal is felhasználhatók vagy integrálva vannak?

Minden Azure-szolgáltatás elérhető lesz az Azure VMware megoldás ügyfelei számára. Az egyes szolgáltatások teljesítmény-és rendelkezésre állási korlátozásait eseti alapon kell kezelni.

### <a name="what-guest-operating-systems-are-compatible-with-azure-vmware-solution"></a>Milyen vendég operációs rendszerek kompatibilisek az Azure VMware-megoldással?

A vSphere szolgáltatással való kompatibilitásról a [VMware kompatibilitási útmutatóban](https://www.vmware.com/resources/compatibility/search.php?deviceCategory=software&details=1&releases=485&page=1&display_interval=10&sortColumn=Partner&sortOrder=Asc&testConfig=16)talál információt.  Az Azure VMware-megoldásban futó vSphere-verzió azonosításához tekintse meg a [VMware-szoftverek](concepts-private-clouds-clusters.md#vmware-software-versions)verziószámát.

### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Használhatom ugyanazt az eszközt, amelyet most használok a saját Felhőbeli erőforrások kezeléséhez?

Igen. A Azure Portal az üzembe helyezéshez és számos felügyeleti művelethez használatos. a vCenter és a NSX Manager a vSphere és a NSX-T erőforrások kezelésére szolgál.

### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Kezelhetem a helyszíni vCenter rendelkező privát felhőket?

Az Azure VMware-megoldás elindítása esetén egyetlen felügyeleti környezetet sem támogat a helyszíni és a saját felhőalapú környezetekben. A privát Felhőbeli fürtöket a vCenter és a NSX Manager fogja kezelni a privát felhőben.

### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Használhatom a helyszíni vRealize Suite szolgáltatást? 

Bizonyos integrációk és használati esetek eseti alapon is kiértékelhető.

### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Áttelepíthetem a vSphere virtuális gépeket a helyszíni környezetből az Azure VMware-megoldás privát felhőkbe?

Igen. A VM-Migrálás és a vMotion segítségével a virtuális gépek áthelyezhetők a privát felhőbe, ha a standard szintű vCenter [vMotion](https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=2106952) teljesülnek.

### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>A vSphere adott verziója szükséges a helyszíni környezetekben?

Az összes felhőalapú környezet a VMware HCX, a vSphere 5,5 vagy a későbbi verziókban érhető el a vMotion helyszíni környezetekben.

### <a name="what-does-the-change-control-process-look-like"></a>Mire hasonlít a változás-ellenőrzési folyamat?

Magának a szolgáltatásnak a frissítései a Microsoft Azure szokásos módosítás-felügyeleti folyamatát követik. Az ügyfelek felelősek a munkaterhelés-felügyeleti feladatokért és a kapcsolódó módosítási felügyeleti folyamatokkal kapcsolatban.

### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Miben különbözik a CloudSimple Azure VMware-megoldástól?

Az új Azure VMware-megoldással a Microsoft és a VMware közvetlen felhőalapú szolgáltatói partnerséggel rendelkezik. Az új megoldást a Microsoft tervezte, építi és támogatja, és a VMware támogatja. Az építészeti megoldások konzisztensek, és a VMware Technology stack egy dedikált Azure-infrastruktúrán fut.

### <a name="can-azure-vmware-solution-vms-be-managed-by-vmrc"></a>Az Azure VMware-megoldás virtuális gépei a VMRC-ben kezelhetők?
Igen. Ha a telepített rendszer be van kapcsolva, hozzáférhet a saját Felhőbeli vCenter, és nyilvános DNS-t használ az ESXi-gazdagépek feloldásához.

### <a name="are-there-special-instructions-for-installing-and-using-vmrc-with-azure-vmware-solution-vms"></a>Van-e speciális útmutatás a VMRC Azure VMware-megoldású virtuális gépekkel történő telepítéséhez és használatához?
Nem. A virtuális gépek előfeltételeinek teljesítéséhez kövesse a [VMware utasításait](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-89E7E8F0-DB2B-437F-8F70-BA34C505053F.html). 

### <a name="is-vmware-hcx-supported-on-vpns"></a>A VMware HCX támogatott a VPN-en?
Nem, a sávszélesség és a késési követelmények miatt.

### <a name="can-azure-bastion-be-used-for-connecting-to-azure-vmware-solution-vms"></a>Használható az Azure Bastion az Azure VMware Megoldásbeli virtuális gépekhez való csatlakozáshoz?
Az Azure Bastion az a szolgáltatás, amely a Jump Box-hoz való kapcsolódáshoz ajánlott, hogy megakadályozza az Azure VMware-megoldás az interneten való kihelyezését. Az Azure Bastion nem használható az Azure VMware-megoldás virtuális gépekhez való kapcsolódáshoz, mivel azok nem Azure IaaS-objektumok.

### <a name="can-azure-load-balancer-internal-be-used-for-azure-vmware-solution-vms"></a>Használható-e az Azure VMware-megoldás virtuális gépei számára a belső Azure Load Balancer?
Nem. A belső Azure Load Balancer csak az Azure IaaS virtuális gépeket támogatja. A Azure Load Balancer nem támogatja az IP-alapú háttér-készletek használatát; csak azok az Azure-beli virtuális gépek vagy virtuálisgép-méretezési csoport objektumai, amelyekben az Azure VMware megoldás virtuális gépei nem Azure-objektumok.

### <a name="can-an-existing-expressroute-gateway-be-used-to-connect-to-azure-vmware-solution"></a>Használható meglévő ExpressRoute-átjáró az Azure VMware-megoldáshoz való kapcsolódáshoz?
Igen. Egy meglévő ExpressRoute-átjáró használatával csatlakozhat az Azure VMware-megoldáshoz, ha nem lépi túl a virtuális hálózatban lévő négy ExpressRoute áramköri korlátot. Ahhoz, hogy a ExpressRoute-en keresztül hozzáférhessen az Azure VMware-megoldáshoz a helyszínen, rendelkeznie kell ExpressRoute Global Reach, mivel a ExpressRoute-átjáró nem biztosít tranzitív útválasztást a csatlakoztatott áramkörök között.

## <a name="compute-network-storage-and-backup"></a>Számítás, hálózat, tárolás és biztonsági mentés

### <a name="is-there-more-than-one-type-of-host-available"></a>Több típusú gazdagép is elérhető?

Csak egy típusú gazdagép érhető el.

### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Mik a CPU-specifikációk az egyes típusú gazdagépeken?

A kiszolgálók kettős 18 Magos 2,3 GHz-es Intel processzorokkal rendelkeznek.

### <a name="how-much-memory-is-in-each-host"></a>Mennyi memória van az egyes gazdagépeken?

A kiszolgálók 576 GB RAM-mal rendelkeznek.

### <a name="what-is-the-storage-capacity-of-each-host"></a>Mi az egyes gazdagépek tárolási kapacitása?

Az ESXi-gazdagépek két vSAN diskgroups rendelkeznek, amelyek kapacitása 15,2 TB, a 3,2 TB NVMe cache-réteg (1,6 TB az egyes diskgroup).

### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Mennyi hálózati sávszélesség érhető el az egyes ESXi-gazdagépeken?

Az Azure VMware-megoldás minden ESXi-állomása 4 25 GB/s hálózati adapterekkel, két, ESXi rendszerforgalomhoz kiépített hálózati adapterrel és két hálózati terheléselosztási forgalomhoz van konfigurálva. 

### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Titkosítva vannak-e a vSAN-adattárolókban tárolt adatok a nyugalmi állapotban?

Igen, a rendszer alapértelmezés szerint titkosítja az összes vSAN-adatfájlt a Azure Key Vaultban tárolt kulcsok használatával.

###  <a name="what-independent-software-vendors-isvs-backup-solutions-work-with-azure-vmware-solution"></a>Milyen független szoftvergyártók (ISV-ket) biztonsági mentési megoldások működnek az Azure VMware megoldással?

A CommVault, a Veritas és a Veeam kiterjesztette a biztonsági mentési megoldásaikat az Azure VMware megoldással való együttműködéshez.  Azonban minden olyan biztonsági mentési megoldás, amely a HotAdd átviteli móddal VMware VADP-t használ, azonnal működni fog az Azure VMware-megoldás dobozán.

### <a name="what-about-support-for-isv-backup-solutions"></a>Mi a helyzet az ISV Backup-megoldások támogatásával?

Mivel ezeket a biztonsági mentési megoldásokat az ügyfelek telepítik és kezelik, a megfelelő ISV-t is elérheti a támogatáshoz. 

### <a name="what-is-the-correct-storage-policy-for-the-dedupe-setup"></a>Mi a helyes tárolási szabályzat a dedupe telepítőhöz?

Használja a virtuálisgép-sablon *thin_provision* tárolási házirendjét.  Az alapértelmezett érték *thick_provision*.

### <a name="are-the-snmp-infrastructure-logs-shared"></a>Megosztották-e az SNMP-infrastruktúra naplóit?

Nem.

## <a name="hosts-clusters-and-private-clouds"></a>Gazdagépek, fürtök és privát felhők

### <a name="is-the-underlying-infrastructure-shared"></a>A mögöttes infrastruktúra meg van osztva?

Nem, a saját Felhőbeli gazdagépek és fürtök dedikált és biztonságos törlésre kerülnek a használat előtt és után.

### <a name="what-are-the-minimum-and-maximum-number-of-hosts-per-cluster"></a>A gazdagépek minimális és maximális száma egy fürtön

A fürtök 3 és 16 ESXi-gazdagép között is méretezhetők. A próbaverziós fürtök három gazdagépre korlátozódnak.

### <a name="can-i-scale-my-private-cloud-clusters"></a>Méretezhetők a saját felhőalapú fürtök?

Igen, a fürtök az ESXi-gazdagépek minimális és maximális száma között méretezhetők. A próbaverziós fürtök három gazdagépre korlátozódnak.

### <a name="what-are-trial-clusters"></a>Mik azok a próbaverziós fürtök?

A próbaverziós fürtök három gazdagép-fürtből állnak, amelyek az Azure VMware-megoldás privát felhők egyhónapos értékeléséhez használatosak.

### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Használhatok magas szintű gazdagépeket a próbaverziós fürtökhöz?

Nem. A csúcsminőségű ESXi-gazdagépek az üzemi fürtökön való használatra vannak fenntartva.

## <a name="azure-vmware-solution-and-vmware-software"></a>Azure VMware-megoldás és VMware szoftver

### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>A VMware-szoftverek milyen verzióit használják a privát felhők?

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


### <a name="do-private-clouds-use-vmware-nsx"></a>A privát felhők a VMware NSX-t használják?

Igen, a NSX-T 2,5 a szoftveresen definiált hálózatkezeléshez használatos az Azure VMware-megoldás privát felhőkben.

### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Használhatom a VMware NSX-V-t egy privát felhőben?

Nem. A NSX-T a NSX egyetlen támogatott verziója.

### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>NSX szükséges a helyszíni környezetekben vagy a privát felhőhöz csatlakozó hálózatokban?

Nem, nem szükséges a helyszíni NSX használata.

### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Mi a VMware-szoftverek frissítési és frissítési ütemterve egy privát felhőben?

A Private Cloud szoftvercsomag frissítései megőrzik a szoftvert a VMware-ből származó legújabb szoftvercsomag-kiadás egy verzióján belül. A Private Cloud Software-verziók eltérhetnek az egyes szoftver-összetevők legújabb verzióitól (ESXi, NSX-T, vCenter, vSAN).

### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Milyen gyakran frissül a Private Cloud Software stack?

A privát felhőalapú szoftverek olyan ütemterv szerint frissülnek, amely nyomon követi a szoftveres csomag VMware-ről való kiadását. A saját felhő nem igényel állásidőt a frissítésekhez.

## <a name="connectivity"></a>Kapcsolatok

### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Milyen hálózati IP-cím megtervezésére van szükség a privát felhők helyszíni környezetekben való beépítéséhez?

Az Azure VMware-megoldás saját Felhőbeli üzembe helyezéséhez magánhálózat/22 címterület szükséges. Ez a magánhálózati címterület nem fedi át az előfizetésben vagy a helyszíni hálózatokon lévő más virtuális hálózatokat.
 
### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Hogyan csatlakozni a helyszíni környezetekről egy Azure VMware-megoldás privát felhőbe?

A szolgáltatáshoz a következő két módszer egyikét használhatja: 

- Egy olyan virtuális géppel vagy Application Gateway-mel, amely a ExpressRoute-on keresztül a privát felhőbe van társítva.
- ExpressRoute-Global Reach a helyszíni adatközpontból egy Azure ExpressRoute-áramkörre.

### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Hogyan csatlakoztasson egy számítási feladatot tartalmazó virtuális gépet az internethez vagy egy Azure szolgáltatásbeli végponthoz?

A Azure Portalban engedélyezze az internetkapcsolatot egy privát felhőben. A NSX-T kezelőjével hozzon létre egy NSX-T T1 útválasztót és egy logikai kapcsolót. Ezután a vCenter használatával telepítheti a virtuális gépet a logikai kapcsoló által definiált hálózati szegmensen. A virtuális gép hálózati hozzáféréssel fog rendelkezni az internethez és az Azure-szolgáltatásokhoz.

### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Korlátozni kell az internetről a virtuális gépekre való hozzáférést a privát felhőben található logikai hálózatokon?

Nem. Alapértelmezés szerint az internetről közvetlenül a privát felhőkbe bejövő hálózati forgalom nem engedélyezett.  Az Azure VMware-megoldás virtuális gépei azonban elérhetővé válnak az interneten keresztül a Azure Portal [nyilvános IP-](public-ip-usage.md) címén keresztül az Azure VMware-megoldás privát felhője számára.

### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Korlátozni kell az internetről a virtuális gépekről a logikai hálózatokon az internetre való hozzáférést?

Igen. A virtuális gépek internet-hozzáférésének korlátozásához a NSX-T Manager használatával kell létrehoznia a tűzfalat.


### <a name="can-azure-vmware-solution-use-azure-virtual-wan-hosted-expressroute-gateways"></a>Használható az Azure VMware-megoldás az Azure Virtual WAN üzemeltetett ExpressRoute-átjárók használatával?
Igen.

### <a name="can-transit-connectivity-be-established-between-on-premises-and-azure-vmware-solution-through-azure-virtual-wan-over-expressroute-global-reach"></a>Lehet-e tranzit kapcsolatot létesíteni a helyszíni és az Azure VMware megoldás között az Azure Virtual WAN ExpressRoute-Global Reach keresztül?
Az Azure Virtual WAN nem biztosít tranzitív útválasztást két csatlakoztatott ExpressRoute-áramkör és nem virtuális WAN ExpressRoute-átjáró között. A ExpressRoute Global Reach használata lehetővé teszi a helyszíni és az Azure VMware-megoldás közötti kapcsolatot, de a virtuális WAN-központ helyett a Microsoft globális hálózatán halad át.

### <a name="could-i-use-hcx-through-public-internet-communications-as-a-workaround-for-the-non-supportability-of-hcx-when-using-vpn-s2s-with-vwan-for-on-premises-communications"></a>Használhatom-e a HCX a nyilvános internetes kommunikáción keresztül, megkerülő megoldásként a HCX nem támogatásához, amikor VPN-S2S használ a vWAN a helyszíni kommunikációhoz?

Jelenleg az egyetlen támogatott módszer a VMware HCX-hez a ExpressRoute-on keresztül.

## <a name="accounts-and-privileges"></a>Fiókok és jogosultságok

### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Milyen fiókokat és jogosultságokat kapok az új Azure VMware-megoldás privát felhővel?

Hitelesítő adatokat ad meg egy cloudadmin-felhasználó számára a vCenter-ben, és rendszergazdai hozzáféréssel rendelkezik a NSX-T kezelőjében. Létezik egy CloudAdmin-csoport is, amely a Azure Active Directory beépítésére használható. További információ: [hozzáférés és identitás fogalmai](concepts-identity.md).

### <a name="can-have-administrator-access-to-esxi-hosts"></a>Rendszergazdai hozzáféréssel rendelkezhet az ESXi-gazdagépekhez?

Nem, az ESXi-hez való rendszergazdai hozzáférés korlátozott a megoldás biztonsági követelményeinek kielégítése érdekében.

### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Milyen jogosultságokkal és engedélyekkel rendelkezem a vCenter?

CloudAdmin-csoport jogosultságokkal fog rendelkezni. További információ: [hozzáférés és identitás fogalmai](concepts-identity.md).

### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Milyen jogosultságokkal és engedélyekkel rendelkezem a NSX-T kezelőn?

A NSX-T teljes körű rendszergazdai jogosultságokkal fog rendelkezni, és a vSphere szerepköralapú hozzáférés-vezérlést is képes kezelni, mint a helyszíni NSX-T adatközpontot. További információ: [hozzáférés és identitás fogalmai](concepts-identity.md).

> [!NOTE]
> A rendszer létrehoz egy T0-útválasztót, és konfigurálja a saját felhőalapú telepítés részeként. A logikai útválasztó vagy a NSX-T peremhálózati virtuális gépek bármely módosítása hatással lehet a privát felhőhöz való kapcsolódásra.

## <a name="billing-and-support"></a>Számlázás és támogatás

### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>Hogyan történik a díjszabás strukturálása az Azure VMware-megoldáshoz?

A díjszabással kapcsolatos általános kérdésekért tekintse meg az Azure VMware megoldás [díjszabását](https://azure.microsoft.com/pricing/details/azure-vmware) ismertető oldalt. 

### <a name="can-azure-vmware-solution-be-purchased-through-a-microsoft-csp"></a>Vásárolhat Azure VMware-megoldást Microsoft CSP-n keresztül?

Igen, az ügyfelek egy CSP által felügyelt Azure-előfizetésen belül telepíthetik az Azure VMware-megoldást.

### <a name="who-supports-azure-vmware-solution"></a>Ki támogatja az Azure VMware-megoldást?

A Microsoft támogatást nyújt az Azure VMware-megoldáshoz. Egy [támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)is küldhet. 

A CSP által felügyelt előfizetések esetében az első szintű támogatás a megoldás-szolgáltatót ugyanolyan módon biztosítja, mint a CSP más Azure-szolgáltatásokhoz.

### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Milyen fiókokra van szükségem egy Azure VMware-megoldás saját Felhőbeli létrehozásához?

Egy Azure-előfizetésben Azure-fiókra lesz szüksége.

### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>A Red Hat-megoldások támogatottak az Azure VMware-megoldásokban?

A Microsoft és a Red hat olyan integrált, közösen elhelyezett támogatási csapattal rendelkezik, amely egységes kapcsolattartási pontot biztosít az Azure platformon futó Red Hat-ökoszisztémák számára.  A Red Hat Enterprise Linux-t használó egyéb Azure platform-szolgáltatásokhoz hasonlóan az Azure VMware megoldás a felhőalapú hozzáférés és az integrált támogatás égisze alá tartozik. A Red Hat Enterprise Linux az Azure VMware-megoldáson belüli futtatására is használható.

### <a name="is-vmware-hcx-enterprise-available-and-if-so-how-much-does-it-cost"></a>Elérhető a VMware HCX Enterprise, és ha igen, Mennyibe kerül?

A VMware HCX Enterprise Azure VMware-megoldásként érhető el *előzetes* verziójú funkcióként vagy szolgáltatásként. Habár a VMware HCX Enterprise for Azure VMware megoldás előzetes verzióban érhető el, ez egy ingyenes funkció/szolgáltatás, és az előzetes verziójú szolgáltatási feltételek és kikötések érvényesek. Miután a VMware HCX Enterprise Service-t elvégezte, egy 30 napos értesítést kap arról, hogy a számlázás átvált. Kikapcsolhatja vagy letilthatja a szolgáltatást.

### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Hogyan az Azure VMware-megoldáshoz tartozó gazdagép-kvóta növelését?

A CSP által felügyelt előfizetések esetén az ügyfélnek el kell küldenie a kérést a partnernek. A partner csapat ezután a Microsofttal folytatja a kvóta növelését az előfizetéshez. További információ: [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md). 

EA-előfizetések esetén kövesse az alábbi eljárást. Először a következőkre lesz szüksége:

* [Azure-nagyvállalati szerződés (EA)](../cost-management-billing/manage/ea-portal-agreements.md) a Microsofttal.
* Azure-fiók Azure-előfizetésben.

Az Azure VMware-megoldási erőforrás létrehozása előtt egy támogatási jegyet küld a gazdagépek lefoglalásához. A kérés megerősítése és teljesítése akár öt munkanapot is igénybe vesz. Ha van egy meglévő Azure VMware-megoldás saját felhője, és több gazdagépet szeretne lefoglalni, akkor ugyanezt a folyamatot kell megtennie.

1. A Azure Portal a Súgó és **támogatás** területen hozzon létre egy **[új támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support)** , és adja meg a következő információkat a jegyhez:
   - **Probléma típusa:** Technikai
   - **Előfizetés:** Előfizetés kiválasztása
   - **Szolgáltatás:** Minden szolgáltatás > Azure VMware-megoldás
   - **Erőforrás:** Általános kérdés 
   - **Összefoglalás:** Kapacitás szükséges
   - **Probléma típusa:** Kapacitás-felügyeleti problémák
   - **Probléma altípusa:** Ügyfél iránti kérelem további gazdagép-kvótához/kapacitáshoz

1. A támogatási jegy **leírásában** a **részletek** lapon adja meg a következőt:

   - POC vagy éles üzem 
   - Régiónév
   - Gazdagépek száma
   - Bármilyen más részlet

   >[!NOTE]
   >Az Azure VMware-megoldás legalább három gazdagép használatát javasolja a saját felhő és a redundancia N + 1 gazdagépek számára. 

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kérelem elküldéséhez.

   A támogatási képviselők akár öt munkanapot is igénybe vesznek, hogy erősítse a kérelmét.

   >[!IMPORTANT] 
   >Ha már rendelkezik egy meglévő Azure VMware-megoldással, és további gazdagépeket kér, vegye figyelembe, hogy öt munkanapon belül le kell foglalni a gazdagépeket. 

1. A gazdagépek kiépítése előtt győződjön meg arról, hogy regisztrálja a **Microsoft. AVS** erőforrás-szolgáltatót a Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Az erőforrás-szolgáltató regisztrálásának további módjai: [Azure erőforrás-szolgáltatók és típusok](../azure-resource-manager/management/resource-providers-and-types.md). 

### <a name="are-reserved-instances-available-for-purchasing-through-the-cloud-solution-provider-csp-program"></a>Elérhetőek-e a fenntartott példányok a Cloud Solution Provider (CSP) programon keresztül?

Igen. A CSP fenntartott példányokat is vásárolhat ügyfelei számára. További információkért lásd: [költségek mentése fenntartott példánnyal](reserved-instance.md). 

### <a name="does-azure-vmware-solution-offer-multi-tenancy-for-hosting-csp-partners"></a>Az Azure VMware-megoldás több-bérlős szolgáltatást kínál a CSP-partnerek üzemeltetéséhez?

Nem. Az Azure VMware-megoldás jelenleg nem nyújt több-bérlőt.

### <a name="will-traffic-between-on-premises-and-azure-vmware-solution-over-expressroute-incur-any-outbound-data-transfer-charge-in-the-metered-data-plan"></a>A helyszíni és az Azure VMware-megoldás közötti forgalom ExpressRoute a forgalmi díjas adatcsomagban?

Az Azure VMware-megoldás ExpressRoute áramkörének forgalma semmilyen módon nem mérhető. A ExpressRoute-áramkörről a helyszíni Azure-hoz kapcsolódó forgalom a ExpressRoute díjszabási csomagok alapján kerül kiszámlázásra.


## <a name="customer-communication"></a>Ügyfél-kommunikáció

### <a name="how-can-i-receive-an-alert-when-azure-sends-service-health-notifications-to-my-azure-subscription"></a>Hogyan kaphatok riasztást, ha az Azure szolgáltatás-állapotra vonatkozó értesítéseket küld az Azure-előfizetésre?

A szolgáltatással kapcsolatos problémák, a tervezett karbantartás, az állapot-tanácsadók, a biztonsági tanácsadók értesítései a Azure Portal **Service Healthon** keresztül jelennek meg.  Az értesítésekhez tartozó műveletnapló-riasztások beállításakor időben hajthat végre műveleteket. További információ: [szolgáltatás állapotára vonatkozó riasztások létrehozása a Azure Portal használatával](../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal).

:::image type="content" source="media/service-health.png" alt-text="Képernyőkép Service Health értesítésekről":::



<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=21069522

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
