---
title: Általános kérdések a Azure Site Recovery szolgáltatással kapcsolatban
description: Ez a cikk a Azure Site Recoveryekkel kapcsolatos népszerű általános kérdéseket tárgyalja.
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 9db91a15c0ee5c982f73f36a36f12b38b969a125
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99820196"
---
# <a name="general-questions-about-azure-site-recovery"></a>Általános kérdések az Azure Site Recovery szolgáltatásról

Ez a cikk a Azure Site Recoveryokkal kapcsolatos gyakori kérdéseket foglalja össze. Adott forgatókönyvek esetében tekintse át ezeket a cikkeket

- [Azure-beli virtuális gépek Azure-ba történő vészhelyreállításával kapcsolatos kérdések](azure-to-azure-common-questions.md)
- [VMware virtuális gépek Azure-ba történő vészhelyreállításával kapcsolatos kérdések](vmware-azure-common-questions.md)
- [Hyper-V virtuális gépek Azure-ba történő vészhelyreállításával kapcsolatos kérdések](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Általános kérdések

### <a name="what-does-site-recovery-do"></a>Mire való a Site Recovery?

Site Recovery az üzletmenet-folytonossági és a vész-helyreállítási (BCDR-) stratégiához járul hozzá azáltal, hogy az Azure-beli virtuális gépeket a régiók, a helyszíni virtuális gépek és a fizikai kiszolgálók között az Azure-ba, a helyszíni gépeket pedig másodlagos adatközpontba irányítja és automatizálja. [További információ](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Biztosítható a Docker-lemezzel rendelkező virtuális gépek elleni védelem?

Nem, ez egy nem támogatott forgatókönyv.

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>Mit tesz Site Recovery az adatok integritásának biztosításához?

Az adatok integritásának biztosítása érdekében a Site Recovery különböző intézkedéseket tett. A HTTPS protokoll használatával biztonságos kapcsolat jön az összes szolgáltatás között. Ez gondoskodik arról, hogy a kártevők és a külső entitások ne tudják meghamisítani az adatvédelmet. Egy másik mérték ellenőrzőösszegeket használ. A forrás és a cél közötti adatátvitelt a közöttük lévő adatok számítási ellenőrzőösszegei végzik. Ez biztosítja, hogy az átvitt adatforgalom konzisztens legyen.

## <a name="service-providers"></a>Szolgáltatók

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Szolgáltató vagyok. Működik Site Recovery a dedikált és közös infrastruktúra-modellekhez?
Igen, a Site Recovery támogatja mind a dedikált, mind a megosztott infrastruktúra-modelleket.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>A szolgáltatónál a bérlő identitása a Site Recovery szolgáltatással közösen van megosztva?
Nem. A bérlői identitás névtelen marad. A bérlőnek nincs szüksége a Site Recovery-portál elérésére. Csak a szolgáltatást nyújtó rendszergazdája kommunikál a portállal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>A bérlői alkalmazásadatok még mindig az Azure-ba kerülnek?
Ha a replikáció a szolgáltató által birtokolt helyek között történik, az alkalmazásadatok soha nem kerülnek az Azure-ba. Az adatforgalom átvitel alatt áll, és közvetlenül replikálódik a szolgáltatói helyek között.

Ha az Azure-ba replikál, az alkalmazásadatok Azure-tárterületre kerülnek, a Site Recovery szolgáltatáshoz azonban nem jutnak el. Az adatforgalom átvitel alatt áll, és az Azure-ban is titkosítva marad.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Kapnak a bérlőim számlát valamilyen Azure-szolgáltatásról?
Nem. Az Azure közvetlenül a szolgáltatóval áll számlázási kapcsolatban. A bérlők felé történő számlázásért a szolgáltató felel.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Ha az Azure-ba replikálok, szükséges a virtuális gépeket folyamatosan az Azure-ban futtatni?
Nem, az Azure Storage-ba replikálja az adatait az előfizetésében. Ha feladatátvételi tesztet (vészhelyreállítási gyakorlatot) vagy tényleges feladatátvételt végez, a Site Recovery automatikusan létrehozza a virtuális gépeket az előfizetéséhez.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Elérhető bérlő szintű elkülönítés az Azure-ba való replikációnál?
Igen.

### <a name="what-platforms-do-you-currently-support"></a>Jelenleg milyen platformok támogatottak?
Támogatjuk az Azure Pack, a Cloud platform System és a System Center-alapú (2012-es és újabb) üzembe helyezést. [További](/previous-versions/azure/windows-server-azure-pack/dn850370(v=technet.10)) információ az Azure Pack és a site Recovery integrációról.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Támogatott az egyetlen Azure Pack-re és az egyetlen VMM-kiszolgálóra alapuló üzembe helyezési modell?
Igen, a Hyper-V virtuális gépek replikálása az Azure-ba vagy a szolgáltatói helyek között végezhető el.  Vegye figyelembe, hogy ha a szolgáltatói helyek között replikál, az Azure runbook-integráció nem érhető el.

## <a name="pricing"></a>Díjszabás

### <a name="where-can-i-find-pricing-information"></a>Hol találhatok díjszabási információkat?
Tekintse át [site Recovery díjszabásának](https://azure.microsoft.com/pricing/details/site-recovery/) részleteit.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Hogyan számítható ki a becsült díjak a Site Recovery használata során?

A [díjszabási számológép](https://aka.ms/asr_pricing_calculator) használatával megbecsülheti a költségeket site Recovery használata közben.

A költségek részletes becsléséhez futtassa a Deployment Planner eszközt a [VMware](./site-recovery-deployment-planner.md) -hez vagy a [Hyper-V-](https://aka.ms/asr-deployment-planner)hez, és használja a [Cost becslése jelentést](./site-recovery-vmware-deployment-planner-cost-estimation.md).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>A felügyelt lemezek mostantól a VMware virtuális gépek és a fizikai kiszolgálók replikálására szolgálnak. Felmerülhetek-e további díjak a gyorsítótárbeli Storage-fiókhoz a felügyelt lemezekkel?

Nem, a gyorsítótárra vonatkozóan nem számítunk fel további díjakat. A standard Storage-fiókba való replikáláskor a gyorsítótár tárolója ugyanahhoz a célként megadott Storage-fiókhoz tartozik.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Egy hónapig Azure Site Recovery felhasználó vagyok. Minden védett példány esetében továbbra is ingyenes az első 31 nap?

Igen. Minden védett példány esetében az első 31 nap során nem számítunk fel Azure Site Recovery díjat. Ha például az elmúlt 6 hónap során 10 példányt védett, és egy 11. példányt hoz Azure Site Recoveryhoz, akkor a 11. példányra az első 31 nap során nem számítunk fel díjat. Az első 10 példány továbbra is Azure Site Recovery díjat von maga után, mert több mint 31 napja volt védetté tenni.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Az első 31 napban bármilyen más Azure-díjat számolunk fel?

Igen, annak ellenére, hogy Site Recovery a védett példányok első 31 napján ingyenes, díjköteles lehet az Azure Storage, a tárolási tranzakciók és az adatátvitelek esetében. A helyreállított virtuális gépek is felmerülhetnek az Azure számítási díjaival.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Van olyan díja, amely vész-helyreállítási részletezést vagy feladatátvételi tesztet hajt végre?

A DR működéshez nincs külön díj. A virtuális gép a feladatátvételi teszt után létrejött után számítunk fel díjat.



## <a name="security"></a>Biztonság

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>A replikációs adatok el lesznek küldve a Site Recovery szolgáltatáshoz?
Nem, Site Recovery nem metszi a replikált adatokat, és nem rendelkezik információval arról, hogy mi fut a virtuális gépeken vagy a fizikai kiszolgálókon.
A replikációs adatcsere a helyszíni Hyper-V gazdagépek, a VMware hipervizorok vagy fizikai kiszolgálók és az Azure tárolási szolgáltatás között történik. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  

A Site Recovery ISO 27001:2013, 27018, HIPAA, DPA tanúsítvánnyal rendelkezik, és a SOC2 és a FedRAMP ÜSS értékelésének folyamata folyamatban van.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>A megfelelőségi okokból még a helyszíni metaadatoknak is ugyanabban a földrajzi régióban kell maradniuk. Segíthet Site Recovery?
Igen. Amikor létrehoz egy Site Recovery tárolót egy régióban, biztosítjuk, hogy a replikáció és a feladatátvétel engedélyezéséhez és előkészítéséhez szükséges metaadatok a régió földrajzi határain belül maradnak.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
A virtuális gépek és a fizikai kiszolgálók esetében a replikálása a helyszíni helyek között a titkosítás-átvitel során támogatott. Az Azure-ba replikált virtuális gépek és fizikai kiszolgálók esetében a titkosítást és a [titkosítást](../storage/common/storage-service-encryption.md) is támogatja az Azure-ban.

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>Az Azure-hoz-Azure Site Recovery a TLS 1,2-et használja az Azure összes szolgáltatásával kapcsolatos kommunikációhoz?
Igen, az Azure-Azure Site Recovery forgatókönyvhöz alapértelmezés szerint a TLS 1,2 protokollt kell kikényszeríteni. 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>Hogyan állíthatom be a TLS 1,2-et a VMware – Azure és a fizikai kiszolgálók közötti Azure Site Recovery helyzetekben?
A replikált elemekre telepített mobilitási ügynökök csak a TLS 1,2-es adatfeldolgozási kiszolgálóval kommunikálnak. A konfigurációs kiszolgálóról az Azure-ra és a Process Serverről az Azure-ra való kommunikáció azonban a TLS 1,1-es vagy 1,0-es verziójában lehet. Kövesse az [útmutatást](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) a TLS 1,2 betartatásához az Ön által beállított konfigurációs kiszolgálókon és folyamat-kiszolgálókon.

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>Hogyan állíthatom be a TLS 1,2-et a HyperV-Azure Site Recovery helyzetekben?
Az Azure Site Recovery-szolgáltatások közötti összes kommunikáció a TLS 1,2 protokollon történik. Site Recovery a rendszerben konfigurált biztonsági szolgáltatókat használ, és a legújabb elérhető TLS protokollt használja. Az egyiknek explicit módon engedélyeznie kell a TLS 1,2-et a beállításjegyzékben, majd Site Recovery a TLS 1,2-et fogja használni a szolgáltatásokkal való kommunikációhoz. 

### <a name="how-can-i-enforce-restricted-access-on-my-storage-accounts-which-are-accessed-by-site-recovery-service-for-readingwriting-replication-data"></a>Hogyan érvényesíthető a korlátozott hozzáférés a saját Storage-fiókokon, amelyeket Site Recovery szolgáltatás a replikációs információk olvasására/írására használ?
A Recovery Services-tároló felügyelt identitásának bekapcsolásához nyissa meg az *Identity (identitás* ) beállítást. Miután a tároló regisztrálva lesz Azure Active Directoryban, megtekintheti a Storage-fiókjait, és a következő szerepkör-hozzárendeléseket adhatja a tárolóhoz:

- Resource Manager-alapú Storage-fiókok (szabványos típus):
  - [Közreműködő](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage-blobadatok közreműködője](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager-alapú Storage-fiókok (prémium típus):
  - [Közreműködő](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage-blobadatok tulajdonosa](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klasszikus Storage-fiókok:
  - [Klasszikus Storage-fiók közreműködői](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [A klasszikus Storage-fiók kulcs-kezelő szolgáltatásának szerepköre](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

## <a name="disaster-recovery"></a>Vészhelyreállítás

### <a name="what-can-site-recovery-protect"></a>Mit lehet Site Recovery védelemmel ellátni?
* **Azure-beli virtuális gépek**: site Recovery a támogatott Azure-beli virtuális gépen futó bármilyen számítási feladatot replikálhat
* **Hyper-v virtuális gépek**: a site Recovery képes a Hyper-v virtuális gépen futó bármilyen munkaterhelés elleni védelemre.
* **Fizikai kiszolgálók**: site Recovery a Windows vagy Linux rendszerű fizikai kiszolgálókat is képes védelemmel ellátni.
* **VMware Virtual Machines**: a site Recovery a VMWare virtuális gépeken futó munkaterhelések elleni védelemre is alkalmas.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Milyen számítási feladatokat tud védeni a Site Recovery?
A Site Recovery használatával biztosíthatja a legtöbb, támogatott virtuális gépen vagy fizikai kiszolgálón futó számítási feladatot. Site Recovery támogatja az Application-Aware replikálást, így az alkalmazások intelligens állapotba állíthatók helyre. Integrálható a Microsoft-alkalmazásokkal, például a SharePoint, az Exchange, a Dynamics, a SQL Server és a Active Directoryval, és szorosan együttműködik a vezető szállítókkal, például az Oracle, az SAP, az IBM és a Red Hat. [További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Elérhető a vészhelyreállítás a fiókirodák számára a Site Recoveryvel?
Igen. Ha a fiókirodákban a replikáció és a feladatátvétel összekapcsolásához Site Recovery használ, az összes fiókirodai munkaterhelést egy központi helyen fogja megtekinteni. A központi telephelyről könnyen intézheti a feladatátvételt és a vészhelyreállítást az összes fiókiroda tekintetében anélkül, hogy el kellene látogatnia a fiókirodák telephelyeire.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Az Azure-beli virtuális gépek esetében támogatott a vész-helyreállítási szolgáltatás?

Igen, Site Recovery támogatja az Azure-beli virtuális gépek Azure-régiók közötti katasztrófáját. [Tekintse át](azure-to-azure-common-questions.md) az Azure virtuális gépek vész-helyreállítási szolgáltatásával kapcsolatos gyakori kérdéseket. Ha két Azure-régió között szeretne replikálni ugyanazon a kontinensen, használja az Azure-t az Azure-beli DR-ajánlathoz. Nem kell beállítania a konfigurációs kiszolgáló/folyamat kiszolgálóját és a ExpressRoute-kapcsolatokat.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Támogatott-e a vész-helyreállítási szolgáltatás a VMware virtuális gépeken?

Igen, Site Recovery támogatja a helyszíni VMware virtuális gépek vész-helyreállítását. [Tekintse át](vmware-azure-common-questions.md) a VMWare virtuális gépek vész-helyreállításával kapcsolatos gyakori kérdéseket.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Támogatott-e a vész-helyreállítási szolgáltatás a Hyper-V virtuális gépeken?
Igen, Site Recovery támogatja a helyszíni Hyper-V virtuális gépek vész-helyreállítását. [Tekintse át](hyper-v-azure-common-questions.md) a Hyper-V virtuális gépek vész-helyreállításával kapcsolatos gyakori kérdéseket.

### <a name="is-disaster-recovery-supported-for-physical-servers"></a>A vész-helyreállítási támogatott a fizikai kiszolgálókon?
Igen, Site Recovery támogatja a Windows és Linux rendszerű helyszíni fizikai kiszolgálók vész-helyreállítását az Azure-ba vagy egy másodlagos helyre. Ismerje meg az [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)-ba irányuló vész-helyreállítási követelményeket és[a másodlagos helyet](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Vegye figyelembe, hogy a fizikai kiszolgálók az Azure-beli virtuális gépekként fognak futni a feladatátvétel után. Az Azure-ból a helyszíni fizikai kiszolgálóra történő feladat-visszavétel jelenleg nem támogatott. Csak egy VMware virtuális géphez lehet visszaadni.





## <a name="replication"></a>Replikálás

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Replikálható helyek közötti VPN-ről az Azure-ba?
Azure Site Recovery replikálja az Azure Storage-fiókba vagy a felügyelt lemezekre egy nyilvános végponton keresztül. A replikáció nem haladja meg a helyek közötti VPN-t. 

### <a name="why-cant-i-replicate-over-vpn"></a>Miért nem lehet replikálni VPN-en keresztül?

Az Azure-ba való replikáláskor a replikálási forgalom eléri az Azure Storage nyilvános végpontját. Így csak a nyilvános interneten vagy a ExpressRoute (Microsoft-társ vagy egy meglévő nyilvános társon) keresztül lehet replikálni.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Használhatom a Riverbed-SteelHeads a replikáláshoz?

Partnerünk, Riverbed, részletes útmutatást nyújt a Azure Site Recovery használatáról. Tekintse át a [megoldási útmutatót](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Használhatom a ExpressRoute a virtuális gépek Azure-ba történő replikálásához?
Igen, a [ExpressRoute](concepts-expressroute-with-site-recovery.md) használatával replikálhatja a helyszíni virtuális gépeket az Azure-ba.

- Azure Site Recovery replikálja az Azure Storage-ba egy nyilvános végponton keresztül. Be kell állítania a [Microsoft-társat](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) , vagy egy meglévő [nyilvános](../expressroute/about-public-peering.md) (új áramköröknél elavult) kapcsolatot kell használnia a ExpressRoute site Recovery replikációhoz való használatához.
- A replikációhoz a Microsoft-társ a javasolt útválasztási tartomány.
- A replikáció nem támogatott privát társak esetén.
- Ha a VMware-gépeket vagy fizikai gépeket védi, győződjön meg arról, hogy a konfigurációs kiszolgáló [hálózati követelményei](vmware-azure-configuration-server-requirements.md#network-requirements) is teljesülnek. A konfigurációs kiszolgáló a Site Recovery replikáció összehangolása érdekében meghatározott URL-címekhez való kapcsolódást igényel. A ExpressRoute nem használható ehhez a kapcsolathoz.
- Miután a virtuális gépeket átadta egy Azure-beli virtuális hálózatnak, elérheti őket az Azure Virtual Network szolgáltatással elérhető [privát](../expressroute/expressroute-circuit-peerings.md#privatepeering) társítási beállítással.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Ha az Azure-ba replikálok, milyen típusú Storage-fiókra vagy felügyelt lemezre van szükségem?

Azure Site Recovery nem támogatja a Storage-fiókok használatát célként tárolóként. Azt javasoljuk, hogy inkább a felügyelt lemezeket használja célként tárolóként a gépekhez. A felügyelt lemezek csak az adatrugalmasság LRS-típusát támogatják.

### <a name="how-often-can-i-replicate-data"></a>Milyen gyakran replikálhatom az adatokat?
* **Hyper-V:** A Hyper-V virtuális gépek 30 másodpercenként replikálhatók (kivéve a Premium Storage esetében), öt perc vagy 15 perc.
* **Azure-beli virtuális gépek, VMWare virtuális gépek, fizikai kiszolgálók:** A replikálási gyakoriság itt nem releváns. A replikáció folyamatos.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Ki lehet terjeszteni a replikálást a meglévő helyreállítási helyről egy másik harmadlagos helyre?
A kiterjesztett vagy láncolt replikáció nem támogatott. Kérje ezt a funkciót a [visszajelzési fórumokban](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Végezhetek offline replikációt, amikor első alkalommal replikálok Azure-ba?
Ez a funkció nem támogatott. Kérje ezt a szolgáltatást a [visszajelzési fórumba](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Kizárhatok a replikációból bizonyos lemezeket?
Ez akkor támogatott, ha VMware virtuális gépeket és Hyper-V virtuális gépeket replikál az Azure-ba a Azure Portal használatával.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Replikálható a virtuális gépek dinamikus lemezek használatával?
A dinamikus lemezek a Hyper-V rendszerű virtuális gépek replikálásakor és a VMware virtuális gépek és fizikai gépek Azure-ba történő replikálásakor támogatottak. Az operációs rendszer lemezének alapszintű lemeznek kell lennie.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Szabályozható a replikálási forgalom számára kiosztott sávszélesség?
Igen. A sávszélesség szabályozásával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [A VMware virtuális gépek és fizikai kiszolgálók replikálásának kapacitásának megtervezése](site-recovery-plan-capacity-vmware.md)
* [A Hyper-V virtuális gépek Azure-ba történő replikálásának kapacitásának megtervezése](./hyper-v-deployment-planner-overview.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Engedélyezhető a replikáció az App-konzisztencia használatával a Linux-kiszolgálókon? 
Igen. A Linux operációs rendszer Azure Site Recovery támogatja az alkalmazások egyéni parancsfájljait az alkalmazás-konzisztencia számára. Az előzetes és utáni beállításokkal rendelkező egyéni szkriptet a Azure Site Recovery mobilitási ügynök fogja használni az alkalmazások konzisztenciája során. Az alábbi lépésekkel engedélyezheti.

1. Jelentkezzen be root-ként a gépre.
2. Módosítsa a könyvtárat Azure Site Recovery mobilitási ügynök telepítési helyére. Az alapértelmezett érték a "/usr/local/ASR"<br>
    `# cd /usr/local/ASR`
3. A telepítés helye alatt váltson a "VX/Scripts" könyvtárra.<br>
    `# cd VX/scripts`
4. Hozzon létre egy "customscript.sh" nevű bash shell-szkriptet a root user végrehajtási engedélyeivel.<br>
    a. A parancsfájlnak támogatnia kell a "--pre" és a "--post" (a dupla kötőjeleket) parancssori kapcsolókat.<br>
    b. Ha a parancsfájlt előzetes beállítással hívja meg, akkor az alkalmazás bemenetének/kimenetének rögzítése, valamint a post-Option paraméterrel való hívás esetén fel kell szólítani az alkalmazás bemenetét/kimenetét.<br>
    c. Egy minta sablon –<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. Adja hozzá a bemeneti/kimeneti parancsok befagyasztása és feloldása az alkalmazás-konzisztenciaot igénylő alkalmazások előzetes és utólagos lépéseiben. Dönthet úgy is, hogy hozzáad egy másik szkriptet, és meghívja azt a "customscript.sh" értékkel a pre és a post kapcsolóval.

>[!Note]
>Az egyéni parancsfájlok támogatásához a Site Recovery-ügynök verziószámának 9,24-es vagy újabb verziójúnak kell lennie.

## <a name="replication-policy"></a>Replikációs szabályzat

### <a name="what-is-a-replication-policy"></a>Mi a replikációs házirend?

A replikációs házirend határozza meg a helyreállítási pontok megőrzési előzményeinek beállításait. A szabályzat az alkalmazás-konzisztens Pillanatképek gyakoriságát is meghatározza. Alapértelmezés szerint a Azure Site Recovery új replikációs házirendet hoz létre az alapértelmezett beállításokkal:

- 24 óra a helyreállítási pontok megőrzési előzményeihez.
- az alkalmazás-konzisztens Pillanatképek gyakoriságának 4 óra.

### <a name="what-is-a-crash-consistent-recovery-point"></a>Mi az összeomlás-konzisztens helyreállítási pont?

Az összeomlás-konzisztens helyreállítási pont a lemezen lévő adatokkal rendelkezik, mintha a pillanatkép során kihúzta a hálózati kábelt a kiszolgálóról. Az összeomlás-konzisztens helyreállítási pont nem tartalmaz olyan semmit, ami memóriában volt a pillanatkép készítésekor.

Napjainkban a legtöbb alkalmazás helyreállíthatja az összeomlás-konzisztens pillanatképeket is. Az összeomlás-konzisztens helyreállítási pontok általában elegendőek az adatbázis nélküli operációs rendszerekhez és alkalmazásokhoz, például a fájlkiszolgálók, a DHCP-kiszolgálók és a nyomtatókiszolgálók számára.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Milyen gyakorisággal történik az összeomlás-konzisztens helyreállítási pontok létrehozása?

A Site Recovery 5 percenként létrehoz egy összeomlás-konzisztens helyreállítási pontot.

### <a name="what-is-an-application-consistent-recovery-point"></a>Mi az az alkalmazás-konzisztens helyreállítási pont?

Az alkalmazással konzisztens helyreállítási pontok az alkalmazás-konzisztens Pillanatképek alapján jönnek létre. Az alkalmazás-konzisztens helyreállítási pontok ugyanazokat az adatmennyiségeket rögzítik, mint az összeomlás-konzisztens Pillanatképek, ugyanakkor a memóriában lévő és a folyamatban lévő összes tranzakció rögzítése is.

A további tartalom miatt az alkalmazás-konzisztens Pillanatképek a leginkább érintettek, és a leghosszabb időt veszik igénybe. Javasoljuk, hogy az alkalmazás-konzisztens helyreállítási pontokat az adatbázis-operációs rendszerekhez és alkalmazásokhoz, például a SQL Serverhoz.

>[!Note]
>Az alkalmazás-konzisztens helyreállítási pontok létrehozása a Windows rendszerű gépen meghiúsul, ha több mint 64 kötet van.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Milyen hatással van az alkalmazás-konzisztens helyreállítási pontok alkalmazása az alkalmazások teljesítményére?

Az alkalmazás-konzisztens helyreállítási pontok rögzítik a memóriában és a folyamatban lévő összes adatmennyiséget. Mivel a helyreállítási pontok rögzítik ezeket az adatkereteket, a Windows rendszerhez Kötet árnyékmásolata szolgáltatás hasonló keretrendszerre van szükségük, hogy fokozatos leválasztása az alkalmazást. Ha a rögzítési folyamat gyakori, akkor hatással lehet a teljesítményre, ha a munkaterhelés már foglalt. A nem adatbázis-alapú számítási feladatok esetében nem ajánlott alacsony gyakorisággal használni az alkalmazás-konzisztens helyreállítási pontokat. Még az adatbázis-munkaterhelés esetében is, 1 óra elegendő.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Mi a minimális gyakorisága az alkalmazás-konzisztens helyreállítási pontok generálásának?

Site Recovery létrehozhat egy alkalmazás-konzisztens helyreállítási pontot, amelynek minimális gyakorisága 1 óra.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hogyan történik a helyreállítási pontok létrehozása és mentése?

Ha szeretné megtudni, hogyan hozza létre a Site Recovery helyreállítási pontokat, tekintse meg a replikációs házirendet. Ez a replikációs házirend egy 24 órás adatmegőrzési időszaktal rendelkező helyreállítási ponttal és 1 órás alkalmazás-konzisztens gyakorisági pillanatképtel rendelkezik.

A Site Recovery 5 percenként létrehoz egy összeomlás-konzisztens helyreállítási pontot. Ez a gyakoriság nem módosítható. Az elmúlt órában 12 összeomlás-konzisztens pont és 1 alkalmazás-konzisztens pont közül választhat. Az idő előrehaladtával Site Recovery az utolsó órában túli összes helyreállítási pontot, és óránként csak 1 helyreállítási pontot ment.

A következő képernyőkép szemlélteti a példát. A képernyőképen:

- Az elmúlt órában a helyreállítási pontok 5 perces gyakorisággal vannak ellátva.
- Az elmúlt órában az Site Recovery csak 1 helyreállítási pontot tart.

   ![Generált helyreállítási pontok listája](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Meddig lehet visszaállítani?

A legrégebben használható helyreállítási pont 72 óra.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>24 órás replikációs házirendtel rendelkezem. Mi történik, ha egy probléma megakadályozza, hogy Site Recovery a helyreállítási pontokat 24 óránál hosszabb ideig hozza létre? A korábbi helyreállítási pontok elvesznek?

Nem, Site Recovery fogja megőrizni az összes korábbi helyreállítási pontot. A helyreállítási pontok adatmegőrzési időszaka alapján a Site Recovery csak akkor cseréli le a legrégebbi pontot, ha új pontokat hoz létre. A probléma miatt Site Recovery nem tud új helyreállítási pontokat előállítani. Amíg új helyreállítási pontra nem kerül sor, a régi pontok addig maradnak, amíg el nem éri a megőrzöttség ablakát.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Ha a replikáció engedélyezve van egy virtuális gépen, hogyan változtathatom meg a replikációs házirendet?

Nyissa meg **site Recovery** tároló  >  **site Recovery infrastruktúra**-  >  **replikációs házirendek** lehetőséget. Válassza ki a szerkeszteni kívánt szabályzatot, és mentse a módosításokat. A módosítások az összes meglévő replikációra érvényesek lesznek.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Az összes helyreállítási pont a virtuális gép teljes másolatát vagy a különbözetet?

Az első létrehozott helyreállítási pont a teljes másolattal rendelkezik. Az egymást követő helyreállítási pontok különbözeti változásokkal rendelkeznek.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Növeli a helyreállítási pontok megőrzési időtartamát?

Igen, ha 24 óra és 72 óra között növeli a megőrzési időtartamot, Site Recovery a helyreállítási pontokat további 48 óráig fogja menteni. A hozzáadott idő tárolási díjat von maga után. Előfordulhat például, hogy egy helyreállítási pont 10 GB-os különbözeti változásokkal rendelkezik, és GB-os $0,16-os díjat tartalmaz. További díjak: $1,60 × 48/hó.


## <a name="failover"></a>Feladatátvétel
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Ha az Azure-ban nem végeztem el az Azure-t, hogyan férhetnek hozzá az Azure-beli virtuális gépekhez a feladatátvétel után?

Az Azure virtuális gépeket biztonságos internetkapcsolaton keresztül, helyek közötti VPN-en keresztül, vagy Azure ExpressRoute segítségével érheti el. A csatlakozáshoz több dolgot is elő kell készíteni. [További információ](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Ha az Azure-t átadja az Azure-nak, hogyan gondoskodik róla, hogy az adataim rugalmasak legyenek?
Az Azure-t hibatűrőnek terveztük. A Site Recovery már a másodlagos Azure-adatközpontba történő feladatátvételre lett tervezve, az Azure SLA-val összhangban. Ha ez történik, győződjön meg róla, hogy a metaadatok és a tárolók ugyanabban a földrajzi régióban maradnak, amelyet a tárolóhoz választott.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Ha két adatközpont között replikálok, mi történik, ha az elsődleges adatközpont váratlan kimaradást tapasztal?
Elindíthat egy nem tervezett feladatátvételt a másodlagos helyről. A Site Recovery nem követeli meg a feladatátvétel végrehajtásához, hogy az elsődleges helyről kapcsolódjon.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?
A feladatátvétel nem automatikus. A feladatátvételt egyetlen kattintással kezdeményezheti a portálon, vagy a [site Recovery PowerShell](/powershell/module/az.recoveryservices) használatával is elindíthat feladatátvételt. A feladat-visszavétel egy egyszerű művelet a Site Recovery-portálon.

A helyszíni Orchestrator vagy Operations Manager segítségével automatizálhatja a virtuális gép hibáit, majd a feladatátvételt az SDK használatával aktiválhatja.

* [További](site-recovery-create-recovery-plans.md) információ a helyreállítási tervekről.
* [További](site-recovery-failover.md) információ a feladatátvételről.
* [További](./vmware-azure-failback.md) információ a VMWare virtuális gépek és a fizikai kiszolgálók meghibásodásáról

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Ha a helyszíni gazdagép nem válaszol vagy összeomlott, Visszatérhetek egy másik gazdagépre?
Igen, a másik helyre történő helyreállítást használhatja a feladat-visszavételhez egy másik gazdagépre az Azure-ból.

* [VMware virtuális gépekhez](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Hyper-V rendszerű virtuális gépek esetén](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

### <a name="what-is-the-difference-between-complete-migration-commit-and-disable-replication"></a>Mi a különbség a teljes áttelepítés, a véglegesítés és a replikáció letiltása között?

Miután a forrás helyéről a gép átvette a feladatátvételt a célhelyre, három lehetőség közül választhat. Mindhárom szolgál különböző célokra –

1.  A **teljes áttelepítés** azt jelenti, hogy a forrás helyére többé nem kerül vissza. Áttelepítette a célhelyre, és most már elkészült. Kattintson a teljes áttelepítési eseményindítók elvégzése elemre, majd tiltsa le a replikációt belsőleg. 
2.  A **commit** érték azt jelenti, hogy ez nem a replikálási folyamat vége. A replikálási elem és az összes konfiguráció továbbra is fennáll, és egy későbbi időpontban is megtalálhatja az **újbóli védekezést** , hogy lehetővé váljon a számítógépek replikálása a forrás régiójába. 
3.  A **replikáció letiltása** letiltja a replikálást, és eltávolítja az összes kapcsolódó konfigurációt. Ez nem befolyásolja a megcélzott régió már meglévő számítógépét.

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Automatizálható Site Recovery forgatókönyvek SDK-val?
Igen. A Site Recovery munkafolyamatainak automatizálásához a Rest API-t, a PowerShellt vagy az Azure SDK-t használhatja. Jelenleg támogatott forgatókönyvek Site Recovery üzembe helyezéséhez a PowerShell használatával:

* [VMMs-felhőkben lévő Hyper-V virtuális gépek replikálása a Resource Manager Azure PowerShell](hyper-v-vmm-powershell-resource-manager.md)
* [Hyper-V virtuális gépek replikálása VMM nélkül Azure PowerShell Resource Managerrel](hyper-v-azure-powershell-resource-manager.md)
* [VMware replikálása az Azure-ba a PowerShell Resource Managerrel](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Összetevő/szolgáltató frissítése

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Hol találhatók a Site Recovery frissítéseinek kibocsátási megjegyzései/kumulatív frissítései

[Ismerje meg](site-recovery-whats-new.md) az új frissítéseket, és szerezze be a [kumulatív információkat](service-updates-how-to.md).

## <a name="next-steps"></a>Következő lépések
* Olvassa el a [Site Recovery áttekintését](site-recovery-overview.md)