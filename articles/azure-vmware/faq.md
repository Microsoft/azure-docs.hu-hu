---
title: Gyakori kérdések
description: Választ ad az Azure VMware megoldással kapcsolatos gyakori kérdésekre.
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: dikamath
ms.openlocfilehash: 816f64f9987abbdcd49de93b3bc218ec1606d9f3
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461651"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>Gyakran ismételt kérdések az Azure VMware-megoldásról

Válaszok az Azure VMware megoldással kapcsolatos gyakori kérdésekre.

## <a name="general"></a>Általános kérdések

#### <a name="what-is-azure-vmware-solution"></a>Mi az az Azure VMware Solution?

Mivel a vállalatok az informatikai modernizációs stratégiákat követve javítják az üzleti rugalmasságot, csökkentik a költségeket, és felgyorsítják az innovációt, a hibrid felhőalapú platformok az ügyfelek digitális átalakításának kulcsfontosságú segítői jelentek meg. Az Azure VMware megoldás a VMware szoftveres adatközpont-(SDDC-) szoftverét kombinálja Microsoft Azure globális felhőalapú szolgáltatás-ökoszisztémával. Az Azure VMware megoldás a teljesítményre, a rendelkezésre állásra, a biztonságra és a megfelelőségre vonatkozó követelmények teljesítése érdekében van kezelve.

## <a name="azure-vmware-solution-service"></a>Azure VMware megoldás szolgáltatás

#### <a name="where-is-azure-vmware-solution-available-today"></a>Hol érhető el a jelenleg elérhető Azure VMware-megoldás?

A szolgáltatás folyamatosan bővül az új régiókban, ezért további részletekért tekintse meg a [legújabb szolgáltatás elérhetőségi információit](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) . 

#### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>Az Azure VMware-megoldás példányain futó munkaterhelések az Azure-szolgáltatásokkal is felhasználhatók vagy integrálva vannak?

Minden Azure-szolgáltatás elérhető lesz az Azure VMware megoldás ügyfelei számára. Az egyes szolgáltatások teljesítmény-és rendelkezésre állási korlátozásait eseti alapon kell kezelni.

#### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Használhatom ugyanazt az eszközt, amelyet most használok a saját Felhőbeli erőforrások kezeléséhez?

Igen. A Azure Portal az üzembe helyezéshez és számos felügyeleti művelethez használatos. a vCenter és a NSX Manager a vSphere és a NSX-T erőforrások kezelésére szolgál.

#### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Kezelhetem a helyszíni vCenter rendelkező privát felhőket?

Az Azure VMware-megoldás elindítása esetén egyetlen felügyeleti környezetet sem támogat a helyszíni és a saját felhőalapú környezetekben. A privát Felhőbeli fürtöket a vCenter és a NSX Manager fogja kezelni a privát felhőben.

#### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Használhatom a helyszíni vRealize Suite szolgáltatást? 

Bizonyos integrációk és használati esetek eseti alapon is kiértékelhető.

#### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Áttelepíthetem a vSphere virtuális gépeket a helyszíni környezetből az Azure VMware-megoldás privát felhőkbe?

Igen. A VM-Migrálás és a vMotion segítségével a virtuális gépek áthelyezhetők a privát felhőbe, ha a standard szintű vCenter [vMotion](https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=2106952) teljesülnek.

#### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>A vSphere adott verziója szükséges a helyszíni környezetekben?

Mivel az összes felhőalapú környezet a VMware HCX, a vSphere 5,5-es vagy újabb verziójával rendelkezik a vMotion helyszíni környezetekben.

#### <a name="what-does-the-change-control-process-look-like"></a>Mire hasonlít a változás-ellenőrzési folyamat?

A szolgáltatásban végzett frissítések a Microsoft Azure szokásos módosítás-felügyeleti folyamatát követik. Az ügyfelek felelősek a munkaterhelés-felügyeleti feladatokért és a kapcsolódó módosítási felügyeleti folyamatokkal kapcsolatban.

#### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Miben különbözik a CloudSimple Azure VMware-megoldástól?

Az új Azure VMware-megoldással a Microsoft és a VMware közvetlen felhőalapú szolgáltatói partnerséggel rendelkezik. Az új megoldást a Microsoft tervezte, építi és támogatja, és a VMware támogatja. Építészeti szempontból a megoldások konzisztensek, és a VMware Technology stack egy Azure dedikált infrastruktúrán fut.

#### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>A Red Hat-megoldások támogatottak az Azure VMware-megoldásokban?

A Microsoft és a Red hat olyan integrált, közösen elhelyezett támogatási csapattal rendelkezik, amely egységes kapcsolattartási pontot biztosít az Azure platformon futó Red Hat-ökoszisztémák számára.  A Red Hat Enterprise Linux-vel dolgozó egyéb Azure platform-szolgáltatásokhoz hasonlóan az Azure VMware megoldás a Felhőbeli hozzáférés és az integrált támogatás égisze alá tartozik, és a Red Hat Enterprise Linux támogatott az Azure VMware-megoldáson belüli futtatásához.

#### <a name="is-vmware-hcx-enterprise-edition-available-and-if-so-how-much-does-it-cost"></a>Elérhető a VMware HCX Enterprise Edition, és ha igen, Mennyibe kerül?

A VMware HCX Enterprise Edition (EE) elérhető az Azure VMware-megoldással, amely *előzetes* funkcióként vagy szolgáltatásként érhető el. Habár az Azure VMware-megoldáshoz készült VMware HCX EE előzetes verzióban érhető el, ingyenes funkció vagy szolgáltatás, és az előzetes verziójú szolgáltatási feltételek és kikötések érvényesek. Miután a VMware HCX EE szolgáltatás elérhetővé válik, 30 napos értesítést kap arról, hogy a számlázás átvált. Kikapcsolhatja vagy letilthatja a szolgáltatást.

#### <a name="can-azure-vmware-solution-vms-be-managed-by-vmrc"></a>Az Azure VMware-megoldás virtuális gépei a VMRC-ben kezelhetők?
Igen, ha a telepített rendszer be van kapcsolva, hozzáférhet a saját Felhőbeli vCenter, és nyilvános DNS-t használ (így az ESXi-gazdagépeket képes megoldani).

#### <a name="are-there-special-instructions-for-installing-and-using-vmrc-with-azure-vmware-solution-vms"></a>Van-e speciális útmutatás a VMRC Azure VMware-megoldású virtuális gépekkel történő telepítéséhez és használatához?
Nem, használja a [VMware által megadott utasításokat](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-89E7E8F0-DB2B-437F-8F70-BA34C505053F.html) , és teljesíti az utasításokban megadott virtuálisgép-előfeltételeket. 

#### <a name="is-vmware-hcx-supported-on-vpns"></a>A VMware HCX támogatott a VPN-en?
Nem, a sávszélesség és a késési követelmények miatt.

#### <a name="can-azure-bastion-be-used-for-connecting-to-avs-vms"></a>Használható az Azure Bastion az AVS virtuális gépekhez való csatlakozáshoz?
Az Azure Bastion az a szolgáltatás, amely a Jump Box-hoz való kapcsolódáshoz ajánlott, hogy megakadályozza az Azure VMware-megoldás az interneten való kihelyezését. Az Azure Bastion nem használható az Azure VMware-megoldás virtuális gépekhez való kapcsolódáshoz, mivel azok nem Azure IaaS-objektumok.

#### <a name="can-an-existing-expressroute-gateway-be-used-to-connect-to-azure-vmware-solution"></a>Használható meglévő ExpressRoute-átjáró az Azure VMware-megoldáshoz való kapcsolódáshoz?
Igen, használhat egy meglévő ExpressRoute-átjárót az Azure VMware-megoldáshoz való kapcsolódáshoz, ha az nem lépi túl a virtuális hálózatban lévő négy ExpressRoute áramköri korlátot.  Ahhoz azonban, hogy a helyszíni Azure VMware-megoldás elérhető legyen a ExpressRoute-on keresztül, rendelkeznie kell ExpressRoute Global Reach, mivel a ExpressRoute-átjáró nem biztosít tranzitív útválasztást a csatlakoztatott áramkörök között.

## <a name="compute-network-storage-and-backup"></a>Számítás, hálózat, tárolás és biztonsági mentés

#### <a name="is-there-more-than-one-type-of-host-available"></a>Több típusú gazdagép is elérhető?

A gazdagépnek csak egy típusa érhető el.

#### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Mik a CPU-specifikációk az egyes típusú gazdagépeken?

A kiszolgálók kettős 18 Magos 2,3 GHz-es Intel processzorokkal rendelkeznek.

#### <a name="how-much-memory-is-in-each-host"></a>Mennyi memória van az egyes gazdagépeken?

A kiszolgálók 576 GB RAM-mal rendelkeznek.

#### <a name="what-is-the-storage-capacity-of-each-host"></a>Mi az egyes gazdagépek tárolási kapacitása?

Az ESXi-gazdagépek két vSAN diskgroups rendelkeznek, amelyek kapacitása 15,2 TB, a 3,2 TB NVMe cache-réteg (1,6 TB az egyes diskgroup).

#### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Mennyi hálózati sávszélesség érhető el az egyes ESXi-gazdagépeken?

Mindegyik ESXi-gazdagép az Azure VMware-megoldás 4 25-Gbps hálózati adapterrel van konfigurálva, és két hálózati adapterrel rendelkezik, amelyek az ESXi rendszerforgalmához és két hálózati terheléselosztási forgalomhoz vannak kiépítve. 

#### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Titkosítva vannak-e a vSAN-adattárolókban tárolt adatok a nyugalmi állapotban?

Igen, a rendszer alapértelmezés szerint titkosítja az összes vSAN-adatfájlt a Azure Key Vaultban tárolt kulcsok használatával.

#### <a name="you-document-that-commvault-veritas-and-veeam-have-extended-their-backup-solutions-to-work-with-azure-vmware-solution-what-about-other-independent-software-vendor-isv-backup-solutions"></a>Dokumentálja, hogy a CommVault, a Veritas és a Veeam kibővítette a biztonsági mentési megoldásait az Azure VMware megoldással való együttműködéshez. Mi a helyzet a többi független szoftvergyártó (ISV) biztonsági mentési megoldással?

Amennyire tudjuk, minden olyan biztonsági mentési megoldás, amely a HotAdd átviteli móddal VMware VADP-t használ, azonnal működnie kell az Azure VMware megoldáson.

#### <a name="what-about-support-for-isv-backup-solutions"></a>Mi a helyzet az ISV Backup-megoldások támogatásával?

Mivel ezeket a biztonsági mentési megoldásokat az ügyfelek telepítik és kezelik, a megfelelő ISV-t is elérheti a támogatáshoz. 

#### <a name="what-is-the-correct-storage-policy-for-the-dedup-setup"></a>Mi a megfelelő tárolási szabályzat a deduplikáció telepítéséhez?

Használja a virtuálisgép-sablon *thin_provision* tárolási házirendjét.  Az alapértelmezett érték *thick_provision*.

#### <a name="are-the-snmp-infrastructure-logs-shared"></a>Megosztották-e az SNMP-infrastruktúra naplóit?

Nem.

## <a name="hosts-clusters-and-private-clouds"></a>Gazdagépek, fürtök és privát felhők

#### <a name="is-the-underlying-infrastructure-shared"></a>A mögöttes infrastruktúra meg van osztva?

Nem, a saját Felhőbeli gazdagépek és fürtök dedikált és biztonságos törlésre kerülnek a használat előtt és után.

#### <a name="what-are-the-minimum-and-maximum-number-of-hosts-per-cluster"></a>A gazdagépek minimális és maximális száma egy fürtön

A fürtök 3 és 16 ESXi-gazdagép között is méretezhetők. A próbaverziós fürtök három gazdagépre korlátozódnak.

#### <a name="can-i-scale-my-private-cloud-clusters"></a>Méretezhetők a saját felhőalapú fürtök?

Igen, a fürtök az ESXi-gazdagépek minimális és maximális száma között méretezhetők. A próbaverziós fürtök három gazdagépre korlátozódnak.

#### <a name="what-are-trial-clusters"></a>Mik azok a próbaverziós fürtök?

A próbaverziós fürtök három, az Azure VMware-megoldás privát felhőkének egy hónapos értékeléséhez használt gazdagép-fürt.

#### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Használhatok magas szintű gazdagépeket a próbaverziós fürtökhöz?

Nem. A csúcsminőségű ESXi-gazdagépek az üzemi fürtökön való használatra vannak fenntartva.

## <a name="azure-vmware-solution-and-vmware-software"></a>Azure VMware-megoldás és VMware szoftver

#### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>A VMware-szoftverek milyen verzióit használják a privát felhők?

A privát felhők a vSphere 6,7, a vSAN 6,7, a VMware HCX és a NSX-T 2,5-es verzióját használják.  

#### <a name="do-private-clouds-use-vmware-nsx"></a>A privát felhők a VMware NSX-t használják?

Igen, a NSX-T 2,5 az Azure VMware megoldás privát felhőkben definiált szoftveres hálózatkezeléshez használatos.

#### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Használhatom a VMware NSX-V-t egy privát felhőben?

Nem. A NSX-T a NSX egyetlen támogatott verziója.

#### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>NSX szükséges a helyszíni környezetekben vagy a privát felhőhöz csatlakozó hálózatokban?

Nem, nem szükséges a helyszíni NSX használata.

#### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Mi a VMware-szoftverek frissítési és frissítési ütemterve egy privát felhőben?

A Private Cloud szoftvercsomag frissítései a szoftvernek a VMware szoftverből származó legújabb kiadásának egy-egy verzióján belül maradnak. A Private Cloud Software-verziók eltérőek lehetnek az egyes szoftver-összetevők legújabb verzióiban (ESXi, NSX-T, vCenter, vSAN).

#### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Milyen gyakran frissül a Private Cloud Software stack?

A privát felhőalapú szoftverek olyan ütemterv szerint frissülnek, amely nyomon követi a VMware-től származó szoftveres csomag kiadását. A saját felhő nem igényel állásidőt a frissítésekhez.

## <a name="connectivity"></a>Kapcsolatok

#### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Milyen hálózati IP-cím megtervezésére van szükség a privát felhők helyszíni környezetekben való beépítéséhez?

Az Azure VMware-megoldás saját Felhőbeli üzembe helyezéséhez magánhálózat/22 címterület szükséges. Ez a magánhálózati címterület nem fedi át az előfizetésben lévő más virtuális hálózatokat, illetve a helyszíni hálózatokat.
 
#### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Hogyan csatlakozni a helyszíni környezetekről egy Azure VMware-megoldás privát felhőbe?

A szolgáltatáshoz a következő két módszer egyikét használhatja: 

- Egy olyan virtuális géppel vagy Application Gateway-mel, amely a ExpressRoute-on keresztül a privát felhőbe van társítva.
- ExpressRoute-Global Reach a helyszíni adatközpontból egy Azure ExpressRoute-áramkörre.

#### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Hogyan csatlakoztasson egy számítási feladatot tartalmazó virtuális gépet az internethez vagy egy Azure szolgáltatásbeli végponthoz?

A Azure Portalban engedélyezze az internetkapcsolatot egy privát felhőben. A NSX-T kezelőjével hozzon létre egy NSX-T T1 útválasztót és egy logikai kapcsolót. Ezután a vCenter használatával telepítheti a virtuális gépet a logikai kapcsoló által definiált hálózati szegmensen. A virtuális gép hálózati hozzáféréssel fog rendelkezni az internethez és az Azure-szolgáltatásokhoz.

#### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Korlátozni kell az internetről a virtuális gépekre való hozzáférést a privát felhőben található logikai hálózatokon?

Nem. Az internetről közvetlenül a privát felhőkbe bejövő hálózati forgalom nem engedélyezett.

#### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Korlátozni kell az internetről a virtuális gépekről a logikai hálózatokon az internetre való hozzáférést?

Igen. A NSX-T Manager használatával olyan tűzfalat kell létrehoznia, amely korlátozza a virtuális gépek internet-hozzáférését.



## <a name="accounts-and-privileges"></a>Fiókok és jogosultságok

#### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Milyen fiókokat és jogosultságokat kapok az új Azure VMware-megoldás privát felhővel?

Hitelesítő adatokat ad meg egy cloudadmin-felhasználó számára a vCenter-ben, és rendszergazdai hozzáféréssel rendelkezik a NSX-T kezelőjében. Létezik egy CloudAdmin-csoport is, amely a Azure Active Directory beépítésére használható. További információ: [hozzáférés és identitás fogalmai](concepts-identity.md).

#### <a name="can-have-administrator-access-to-esxi-hosts"></a>Rendszergazdai hozzáféréssel rendelkezhet az ESXi-gazdagépekhez?

Nem, az ESXi-hez való rendszergazdai hozzáférés korlátozott a megoldás biztonsági követelményeinek kielégítése érdekében.

#### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Milyen jogosultságokkal és engedélyekkel rendelkezem a vCenter?

CloudAdmin-csoport jogosultságokkal fog rendelkezni. További információ: [hozzáférés és identitás fogalmai](concepts-identity.md).

#### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Milyen jogosultságokkal és engedélyekkel rendelkezem a NSX-T kezelőn?

A NSX-T teljes körű rendszergazdai jogosultságokkal rendelkezik, és a szerepköralapú hozzáférés-vezérlést ugyanúgy kezelheti, mint a helyszíni NSX-T adatközpontot. További információ: [hozzáférés és identitás fogalmai](concepts-identity.md).

> [!NOTE]
> A rendszer létrehoz egy T0-útválasztót, és konfigurálja a saját felhőalapú telepítés részeként. A logikai útválasztó vagy a NSX-T peremhálózati virtuális gépek bármely módosítása hatással lehet a privát felhőhöz való kapcsolódásra.

## <a name="billing-and-support"></a>Számlázás és támogatás

#### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>Hogyan történik a díjszabás strukturálása az Azure VMware-megoldáshoz?

A díjszabással kapcsolatos általános kérdésekért tekintse meg az Azure VMware megoldás [díjszabását](https://azure.microsoft.com/pricing/details/azure-vmware) ismertető oldalt. 

#### <a name="who-supports-azure-vmware-solution"></a>Ki támogatja az Azure VMware-megoldást?

Az Azure VMware-megoldás támogatását a Microsoft továbbítja. Egy [támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)is küldhet.

#### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Milyen fiókokra van szükségem egy Azure VMware-megoldás saját Felhőbeli létrehozásához?

Egy Azure-előfizetésben Azure-fiókra lesz szüksége.

#### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Hogyan az Azure VMware-megoldáshoz tartozó gazdagép-kvóta növelését?

* Szüksége lesz egy [Azure nagyvállalati Szerződésra (EA)](../cost-management-billing/manage/ea-portal-agreements.md) a Microsofttal.
* Egy Azure-előfizetésben Azure-fiókra lesz szüksége.

Az Azure VMware-megoldási erőforrás létrehozása előtt egy támogatási jegyet kell benyújtania a csomópontok lefoglalásához. A kérés megerősítése és a csomópontok lefoglalása akár öt munkanapot is igénybe vesz. Ha van egy meglévő Azure VMware-megoldás saját felhője, és több csomópontot szeretne lefoglalni, akkor ugyanezt a folyamatot kell megtennie.


1. A Azure Portal a Súgó és **támogatás**területen hozzon létre egy **[új támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support)** , és adja meg a következő információkat a jegyhez:
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
   - Csomópontok száma
   - Bármilyen más részlet

   >[!NOTE]
   >Az Azure VMware-megoldás legalább három csomópontot javasol a saját felhő és a redundancia N + 1 csomópontok kiépítéséhez. 

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kérelem elküldéséhez.

   A támogatási képviselők akár öt munkanapot is igénybe vesznek, hogy erősítse a kérelmét.

   >[!IMPORTANT] 
   >Ha már rendelkezik egy meglévő Azure VMware-megoldással, és további csomópontokra van szüksége, vegye figyelembe, hogy öt munkanapra van szükség a csomópontok lefoglalásához. 

1. A csomópontok kiépítése előtt győződjön meg arról, hogy regisztrálja a **Microsoft. AVS** erőforrás-szolgáltatót a Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Az erőforrás-szolgáltató regisztrálásának további módjaiért tekintse meg az [Azure erőforrás-szolgáltatók és-típusok](../azure-resource-manager/management/resource-providers-and-types.md)című témakört.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=21069522

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md