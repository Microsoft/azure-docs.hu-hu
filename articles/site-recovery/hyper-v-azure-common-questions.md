---
title: Gyakori kérdések a Hyper-V vész-helyreállítással kapcsolatban Azure Site Recovery
description: Ez a cikk a helyi Hyper-V virtuális gépeken az Azure-ba való vész-helyreállítás beállításával kapcsolatos gyakori kérdéseket összegzi a Azure Site Recovery-hely használatával.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: 649bd69f14cdf8d81fe05d3a5f5cac3389419fc3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879444"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Vészhelyreállítás Hyper-V-ről Azure-ba – Gyakori kérdések

Ez a cikk a helyszíni Hyper-V virtuális gépek Azure-ba történő replikálása során megjelenő gyakori kérdésekre ad választ. 

## <a name="general"></a>Általános kérdések

### <a name="how-is-site-recovery-priced"></a>Hogyan Site Recovery díjszabása?
Tekintse át [Azure site Recovery díjszabásának](https://azure.microsoft.com/pricing/details/site-recovery/) részleteit.

### <a name="how-do-i-pay-for-azure-vms"></a>Hogyan fizetni az Azure-beli virtuális gépekért?
A replikáció során a rendszer az Azure Storage-ba replikálja az adatmennyiséget, és nem fizet a virtuális gépek változásairól. Amikor feladatátvételt futtat az Azure-ba, Site Recovery automatikusan létrehozza az Azure IaaS virtuális gépeket. Ezt követően az Azure-ban felhasznált számítási erőforrásokért kell fizetnie.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Van-e különbség a általános célú v2 Storage-fiókra való replikáláskor?

Általában a GPv2 miatti tranzakciók költségeinek növekedését tapasztalja, mivel Azure Site Recovery tranzakciós terhelést eredményez. [További információ](../storage/common/storage-account-upgrade.md#pricing-and-billing) a változás megbecsléséről.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Mit kell tennem a Hyper-V-ben a Site Recovery-vel való replikáció összehangolása érdekében?

A szükséges Hyper-V gazdakiszolgálóra vonatkozó feltételek a telepítési forgatókönyvtől függenek. A Hyper-V-vel kapcsolatos előfeltételekről az alábbi cikkekben olvashat:

* [Hyper-V virtuális gépek replikálása Azure-ba (VMM nélkül)](./hyper-v-azure-tutorial.md)
* [Hyper-V virtuális gépek replikálása Azure-ba (VMM-mel)](./hyper-v-vmm-disaster-recovery.md)
* [Hyper-V virtuális gépek replikálása másodlagos adatközpontba](./hyper-v-vmm-disaster-recovery.md)
* Ha másodlagos adatközpontba végez replikálást, olvassa el a [Hyper-V virtuális gépek támogatott vendég operációs rendszereiről](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/mt126277(v=ws.11))szóló információkat.
* Ha az Azure-ba replikál, Site Recovery az [Azure által támogatott](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10))összes vendég operációs rendszert támogatja.

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Használhatom a virtuális gépeket, ha a Hyper-V egy ügyfél operációs rendszeren fut?
Nem. A virtuális gépeknek egy támogatott Windows kiszolgáló gépen futó Hyper-V gazdakiszolgálón kell lenniük. Ha védelemmel kell ellátnia egy ügyfélszámítógépet, az [Azure](./vmware-azure-tutorial.md) -ba vagy egy [másodlagos adatközpontba](./vmware-physical-secondary-disaster-recovery.md)replikálhatja azt fizikai gépnek.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>A Hyper-V-gazdagépeknek VMM-felhőkben kell lenniük?
Ha másodlagos adatközpontba szeretne replikálni, akkor a Hyper-V virtuális gépeknek a VMM-felhőben található Hyper-V gazdagép-kiszolgálókon kell lenniük. Ha az Azure-ba szeretne replikálni, a virtuális gépeket VMM-Felhőkkel vagy anélkül is replikálhatja. [További](./hyper-v-azure-tutorial.md) információ: Hyper-V-replikáció az Azure-ba.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Replikálható Hyper-V 2. generációs virtuális gép az Azure-ba?
Igen. Site Recovery átalakítja a 2. generációról az 1. generációra a feladatátvétel során. A feladat-visszavételkor a rendszer visszaalakítja a gépet a 2. generációba. [További információk](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Üzembe helyezhetem VMM-mel a Site Recovery-t, ha csak egy VMM-kiszolgálóm van?

Igen. A virtuális gépeket replikálhatja a VMM-felhőben lévő Hyper-V-kiszolgálókon az Azure-ba, vagy a VMM-felhők közötti replikálást végezheti el ugyanazon a kiszolgálón. A helyszíni és a helyszíni replikáció esetében javasoljuk, hogy VMM-kiszolgálóval rendelkezzen mind az elsődleges, mind a másodlagos helyen. 

### <a name="what-do-i-need-in-azure"></a>Mire van szükségem az Azure-ban?
Szüksége lesz egy Azure-előfizetésre, egy Recovery Services-tárolóra, egy Storage-fiókra és egy virtuális hálózatra. A tárolónak, a Storage-fióknak és a hálózatnak ugyanabban a régióban kell lennie.

### <a name="what-azure-storage-account-do-i-need"></a>Milyen Azure Storage-fiókra van szükségem?
Szüksége van egy LRS vagy egy GRS Storage-fiókra. Mi a GRS használatát javasoljuk, mivel ez akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében gondoskodik az adatok hibatűréséről. A Premium Storage használata támogatott.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Szükség van az Azure-fióknak a virtuális gépek létrehozásához szükséges engedélyekre?
Ha Ön előfizetés-rendszergazda, a szükséges replikációs engedélyekkel rendelkezik. Ha nem, akkor engedélyre van szüksége egy Azure-beli virtuális gép létrehozásához az erőforráscsoport és a virtuális hálózat között, amelyet a Site Recovery konfigurálásakor ad meg, és engedélyt ad a kiválasztott Storage-fiókba való írásra. [További információ](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>A replikációs adatküldés a Site Recovery?
Nem, Site Recovery nem metszi a replikált adatokat, és nem rendelkezik információval arról, hogy mi fut a virtuális gépeken. A replikációs adatcsere a Hyper-V-gazdagépek és az Azure Storage között történik. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  

A Site Recovery ISO 27001:2013, 27018, HIPAA, DPA tanúsítvánnyal rendelkezik, és a SOC2 és a FedRAMP ÜSS értékelésének folyamata folyamatban van.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Megtarthatjuk a helyszíni metaadatokat egy földrajzi régión belül?
Igen. Amikor létrehoz egy tárolót egy régióban, biztosítjuk, hogy az Site Recovery által használt összes metaadat a régió földrajzi határán belül maradjon.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
Igen, [Az Azure-ban](../storage/common/storage-service-encryption.md) a titkosítás és a titkosítás is támogatott.


## <a name="deployment"></a>Üzembe helyezés

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Mire használhatom a Hyper-V-t az Azure-ba történő replikációhoz?

- Vész- **helyreállítás**: teljes vész-helyreállítást állíthat be. Ebben az esetben a helyszíni Hyper-V virtuális gépeket az Azure Storage-ba replikálja:
    - A virtuális gépeket replikálhatja az Azure-ba. Ha a helyszíni infrastruktúra nem érhető el, akkor feladatátvételt hajt végre az Azure-ban.
    - Ha feladatátvételt végez, az Azure-beli virtuális gépek a replikált adat használatával jönnek létre. Az alkalmazások és munkaterhelések az Azure-beli virtuális gépeken érhetők el.
    - Ha a helyszíni adatközpont ismét elérhetővé válik, visszatérhet az Azure-ból a helyszíni helyre.
- **Migrálás**: a helyszíni Hyper-V virtuális gépek Azure Storage-ba való áttelepítéséhez site Recovery használható. Ezt követően feladatátvételt végez a helyszínről az Azure-ba. A feladatátvételt követően az alkalmazások és a számítási feladatok elérhetők és futnak az Azure-beli virtuális gépeken.


### <a name="what-do-i-need-on-premises"></a>Mi szükséges a helyszínen?

Egy vagy több önálló vagy fürtözött Hyper-V-gazdagépen futó virtuális gépre van szüksége. A System Center Virtual Machine Manager (VMM) által felügyelt gazdagépeken futó virtuális gépeket is lehet replikálni.
- Ha nem futtatja a VMM-t, a Site Recovery üzembe helyezése során a Hyper-V-gazdagépeket és-fürtöket Hyper-V-helyekre gyűjti. Minden Hyper-V-gazdagépen telepíti a Site Recovery ügynököket (Azure Site Recovery Provider és Recovery Services Agent).
- Ha a Hyper-V-gazdagépek egy VMM-felhőben találhatók, a replikálást a VMM-ben végezheti el. A Site Recovery szolgáltatót a VMM-kiszolgálóra, valamint az egyes Hyper-V-gazdagépek Recovery Services-ügynökére telepíti. A VMM logikai/virtuálisgép-hálózatok és az Azure virtuális hálózatok között képezhető le.
- [További](hyper-v-azure-architecture.md) információ a Hyper-V-ről az Azure architektúrára.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Replikálhatók a Hyper-V fürtön található virtuális gépek?

Igen, Site Recovery támogatja a fürtözött Hyper-V-gazdagépeket. Vegye figyelembe:

- A fürt összes csomópontját ugyanahhoz a tárolóhoz kell regisztrálni.
- Ha nem használ VMM-t, a fürtben lévő összes Hyper-V-gazdagépet ugyanahhoz a Hyper-V-helyhez kell hozzáadni.
- Telepítse a Azure Site Recovery szolgáltatót és a Recovery Services ügynököt a fürt minden egyes Hyper-V-gazdagépén, és adja hozzá mindegyik gazdagépet egy Hyper-V-helyhez.
- Nem szükséges konkrét lépéseket végrehajtani a fürtön.
- Ha futtatja a Deployment Planner eszközt a Hyper-V-hez, az eszköz összegyűjti a profilt futtató csomópontot, amely a virtuális gép futását futtatja. Az eszköz nem tud adatokat gyűjteni egy olyan csomópontról, amely ki van kapcsolva, de nyomon fogja követni a csomópontot. A csomópont üzembe helyezése után az eszköz elindítja a virtuálisgép-profil adatainak gyűjtését (ha a virtuális gép a profil virtuálisgép-listájának része, és a csomóponton fut).
- Ha egy Site Recovery-tárolóban lévő Hyper-V-gazdagépen lévő virtuális gép egy másik Hyper-V-gazdagépre kerül át ugyanazon a fürtön, vagy egy önálló gazdagépre, a virtuális gép replikációja nincs hatással. A Hyper-V-gazdagépnek meg kell felelnie az [előfeltételeknek](hyper-v-azure-support-matrix.md#on-premises-servers), és konfigurálni kell egy site Recovery-tárolóban. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Használhatom a virtuális gépeket, ha a Hyper-V egy ügyfél operációs rendszeren fut?
Nem. A virtuális gépeknek egy támogatott Windows kiszolgáló gépen futó Hyper-V gazdakiszolgálón kell lenniük. Ha az ügyfélszámítógépet védelemmel kell ellátnia, az Azure-ba [replikálhatja fizikai gépnek](physical-azure-disaster-recovery.md) .

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Replikálható Hyper-V 2. generációs virtuális gép az Azure-ba?
Igen. Site Recovery átalakítja a 2. generációról az 1. generációra a feladatátvétel során. A feladat-visszavételkor a rendszer visszaalakítja a gépet a 2. generációba.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Automatizálható Site Recovery forgatókönyvek SDK-val?
Igen. A Site Recovery munkafolyamatainak automatizálásához a Rest API-t, a PowerShellt vagy az Azure SDK-t használhatja. Jelenleg támogatott forgatókönyvek a Hyper-V és az Azure közötti replikáláshoz a PowerShell használatával:

- [Hyper-V replikálása VMM nélkül a PowerShell használatával](hyper-v-azure-powershell-resource-manager.md)
- [Hyper-V replikálása a VMM a PowerShell használatával](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replikálás

### <a name="where-do-on-premises-vms-replicate-to"></a>Hol replikálódnak a helyszíni virtuális gépek?
Az adatreplikálás az Azure Storage-ba. Feladatátvétel futtatásakor a Site Recovery automatikusan létrehozza az Azure-beli virtuális gépeket a Storage-fiókból.

### <a name="what-apps-can-i-replicate"></a>Milyen alkalmazásokat lehet replikálni?
Bármely olyan alkalmazást vagy számítási feladatot replikálhat, amely egy Hyper-V virtuális gépet futtat, amely megfelel a [replikációs követelményeknek](hyper-v-azure-support-matrix.md#replicated-vms). Site Recovery támogatja az Application-Aware replikálást, így az alkalmazások feladatátvétele és visszahívása intelligens állapotba meghiúsulhat. A Site Recovery a Microsoft-alkalmazásokkal, például a SharePoint, az Exchange, a Dynamics, a SQL Server és a Active Directory szolgáltatással integrálható, és szorosan együttműködik a vezető szállítókkal, például az Oracle, az SAP, az IBM és a Red [További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="whats-the-replication-process"></a>Mi a replikációs folyamat?

1. A kezdeti replikáció indításakor létrejön egy Hyper-V virtuális gép pillanatképe.
2. A virtuális GÉPEN lévő virtuális merevlemezeket egyenként replikálja a rendszer, amíg az összes Azure-ba át nem másolja őket. A virtuális gép méretétől és a hálózati sávszélességtől függően ez eltarthat egy ideig. Megtudhatja, hogyan növelheti a hálózati sávszélességet.
3. Ha a lemez megváltozik, miközben a kezdeti replikálás folyamatban van, a Hyper-V replika replikációs nyomon követése a változásokat Hyper-V replikációs naplókként (. HRL) követi nyomon. Ezek a naplófájlok ugyanabban a mappában találhatók, mint a lemezek. Minden lemezhez tartozik egy. HRL fájl, amelyet a rendszer a másodlagos tárolóba továbbít. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
4. A kezdeti replikáció befejeztével a rendszer törli a virtuális gép pillanatképét.
5. A rendszer a naplóban történt összes változást szinkronizálja és egyesíti a szülő lemezre.
6. A kezdeti replikálás befejeződése után a védelem véglegesítése a virtuális gép feladatainak futtatásával végezhető el. Konfigurálja a hálózatot és más replikáció utáni beállításokat, hogy a virtuális gép védve legyen.
7. Ekkor megtekintheti a virtuális gép beállításait, és meggyőződhet arról, hogy készen áll a feladatátvételre. A virtuális gép vész-helyreállítási gyakorlata (feladatátvételi teszt) futtatásával ellenőrizheti, hogy az elvárt módon működik-e.
8. A kezdeti replikálást követően a replikációs házirendnek megfelelően elindul a különbözeti replikáció.
9. A változások naplózása. HRL fájlok. Minden replikációra konfigurált lemezhez tartozik egy .hrl fájl.
10. A rendszer elküldi a naplót az ügyfél Storage-fiókjába. Amikor egy napló átkerül az Azure-ba, az elsődleges lemez változásai egy másik naplófájlban, ugyanabban a mappában vannak követve.
11. A kezdeti és a különbözeti replikáció során a Azure Portal is figyelheti a virtuális gépet.

[További](hyper-v-azure-architecture.md#replication-process) információ a replikálási folyamatról.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Replikálhatók az Azure-ba helyek közötti VPN használatával?

A Site Recovery egy nyilvános végponton keresztül replikálja a helyszíni adatokból az Azure Storage-ba, vagy a Microsoft ExpressRoute használatával. A helyek közötti VPN-hálózaton keresztüli replikáció nem támogatott.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Replikálható az Azure-ba a ExpressRoute használatával?

Igen, a ExpressRoute használatával replikálhatja a virtuális gépeket az Azure-ba. A Site Recovery egy nyilvános végponton keresztül replikálja az Azure Storage-fiókba, és be kell állítania a [Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) -társítást a site Recovery replikáláshoz. Miután a virtuális gépek feladatátvételt végeztek egy Azure-beli virtuális hálózatra, a [privát](../expressroute/expressroute-circuit-peerings.md#privatepeering)kapcsolaton keresztül érheti el azokat.


### <a name="why-cant-i-replicate-over-vpn"></a>Miért nem lehet replikálni VPN-en keresztül?

Az Azure-ba való replikáláskor a replikálási forgalom elér egy Azure Storage-fiók nyilvános végpontját. Így csak a nyilvános interneten keresztül replikálhatja a ExpressRoute (Microsoft-társ), és a VPN nem működik. 

### <a name="what-are-the-replicated-vm-requirements"></a>Mik a replikált virtuális gépekre vonatkozó követelmények?

A replikáláshoz a Hyper-V virtuális gépnek támogatott operációs rendszert kell futtatnia. Emellett a virtuális gépnek meg kell felelnie az Azure-beli virtuális gépek követelményeinek. [További információ](hyper-v-azure-support-matrix.md#replicated-vms) a támogatási mátrixban található.

### <a name="why-is-an-additional-standard-storage-account-required-if-i-replicate-my-virtual-machine-disks-to-premium-storage"></a>Miért van szükség további standard szintű Storage-fiókra, ha a virtuális gépek lemezeit a Premium Storage-ba replikálom?

Ha a helyszíni virtuális gépeket/fizikai kiszolgálókat a Premium Storage-ba replikálja, a védett gép lemezén található összes adattal a Premium Storage-fiókba lesznek replikálva. A replikációs naplók tárolásához további szabványos Storage-fiókra van szükség. A lemezes adatreplikálás kezdeti fázisának befejezése után a rendszer folyamatosan nyomon követi a helyszíni lemezek összes módosítását, és replikációs naplóként tárolja ezt a további szabványos Storage-fiókban.

### <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran lehet replikálni az Azure-ba?

A Hyper-V virtuális gépek 30 másodpercenként replikálhatók (kivéve a Premium Storage esetében) vagy 5 percet.

### <a name="can-azure-site-recovery-and-hyper-v-replica-be-configured-together-on-a-hyper-v-machine"></a>A Azure Site Recovery és a Hyper-V replika együtt is konfigurálható egy Hyper-V-gépen?

Igen, a Azure Site Recovery és a Hyper-V replika is konfigurálható a gépekhez. A gépnek azonban fizikai gépnek kell lennie, és az Azure-ba replikálni kell a konfiguráció/folyamat kiszolgáló használatával. További információ a [fizikai gépek](./physical-azure-architecture.md)védelméről.

### <a name="can-i-extend-replication"></a>Ki lehet terjeszteni a replikálást?
A kiterjesztett vagy láncolt replikáció nem támogatott. Kérje ezt a funkciót a [visszajelzési fórumokban](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Végezhetek kapcsolat nélküli kezdeti replikálást?
Ez a funkció nem támogatott. Kérje ezt a szolgáltatást a [visszajelzési fórumba](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Ki lehet zárni a lemezeket?
Igen, kizárhatja a lemezeket a replikációból. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Replikálható virtuális gépek dinamikus lemezekkel?
A dinamikus lemezek replikálhatók. Az operációs rendszer lemezének alapszintű lemeznek kell lennie.



## <a name="security"></a>Biztonság

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Milyen hozzáférésre van szükség a Hyper-V-gazdagépek Site Recovery

Site Recovery a kiválasztott virtuális gépek replikálásához hozzá kell férnie a Hyper-V-gazdagépekhez. Site Recovery telepíti a következőt a Hyper-V-gazdagépeken:

- Ha nem futtatja a VMM-t, akkor a Azure Site Recovery szolgáltató és a Recovery Services ügynök telepítve van az egyes gazdagépeken.
- Ha a VMM-t futtatja, a rendszer az Recovery Services-ügynököt minden gazdagépre telepíti. A szolgáltató a VMM-kiszolgálón fut.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Mit Site Recovery telepíteni a Hyper-V virtuális gépekre?

A Site Recovery nem telepít explicit módon olyan Hyper-V virtuális gépeken, amelyeken engedélyezve van a replikáció.




## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel


### <a name="how-do-i-fail-over-to-azure"></a>Hogyan a feladatátvételt az Azure-ba?

Futtathat tervezett vagy nem tervezett feladatátvételt a helyszíni Hyper-V virtuális gépekről az Azure-ra.

- Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.
- Ha az elsődleges hely nem érhető el, a nem tervezett feladatátvételt is futtathatja.
- Elvégezheti egy gép feladatátadását, de létrehozhat több gép összehangolt feladatátadását tartalmazó helyreállítási terveket is.
- A feladatátvétel két részből áll:
    - A feladatátvétel első lépése után látnia kell a létrehozott replika virtuális gépeket az Azure-ban. Hozzárendelhet egy nyilvános IP-címet a virtuális géphez, ha szükséges.
    - Ezután véglegesíti a feladatátvételt, hogy megkezdje a munkaterhelések elérését a replika Azure-beli virtuális gépről.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hogyan Azure-beli virtuális gépeket a feladatátvételt követően?
A feladatátvételt követően az Azure-beli virtuális gépek biztonságos internetkapcsolaton keresztül, helyek közötti VPN-en vagy Azure-ExpressRoute keresztül érhetők el. A csatlakozáshoz több dolgot is elő kell készítenie. [További információ](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>Az adat-visszavételi művelet rugalmas?
Az Azure-t hibatűrőnek terveztük. Site Recovery a másodlagos Azure-adatközpontba történő feladatátvételre tervezték, az Azure SLA-val összhangban. Feladatátvétel esetén győződjön meg arról, hogy a metaadatok és a tárolók ugyanabban a földrajzi régióban maradnak, amelyet a tárolóhoz választott.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?
A [feladatátvétel](site-recovery-failover.md) nem automatikus. A feladatátvételt egyetlen kattintással kezdeményezheti a portálon, vagy a [PowerShell](/powershell/module/az.recoveryservices) használatával is aktiválhatja a feladatátvételt.

### <a name="how-do-i-fail-back"></a>Hogyan a feladat-visszavétel?

Miután a helyszíni infrastruktúra újra működik, visszatérhet. A feladat-visszavétel három szakaszban fordul elő:

1. A tervezett feladatátvételt az Azure-ból a helyszíni helyre indíthatja el néhány különböző lehetőség használatával:

    - Az állásidő csökkentése: Ha ezt a beállítást használja, Site Recovery a feladatátvétel előtt szinkronizálja az adatokat. Ellenőrzi a módosított adatblokkokat, és letölti azokat a helyszíni helyre, míg az Azure-beli virtuális gép folyamatosan fut, és minimalizálja az állásidőt. Ha manuálisan megadja, hogy a feladatátvétel befejeződik, az Azure-beli virtuális gép leáll, a rendszer minden végső változást átmásol, és elindul a feladatátvétel.
    - Teljes Letöltés: ezzel a beállítással az adatokat szinkronizálja a rendszer a feladatátvétel során. Ez a lehetőség letölti a teljes lemezt. Gyorsabb, mert nincs ellenőrzőösszeg kiszámítva, de több állásidő is van. Akkor használja ezt a beállítást, ha a replika Azure-beli virtuális gépeket egy ideig, vagy ha a helyszíni virtuális gépet törölték.

2. Kiválaszthatja, hogy a feladat-visszavétel ugyanarra a virtuális gépre vagy egy másik virtuális gépre történjen. Azt is megadhatja, hogy Site Recovery hozza létre a virtuális gépet, ha még nem létezik.
3. A kezdeti szinkronizálás befejeződése után kiválaszthatja a feladatátvétel befejezését. A befejezést követően bejelentkezhet a helyszíni virtuális gépre, és megtekintheti, hogy minden a várt módon működik-e. A Azure Portalban láthatja, hogy az Azure-beli virtuális gépek le lettek állítva.
4. Véglegesíti a feladatátvételt a befejezéshez, és ismét megkezdi a munkaterhelések elérését a helyszíni virtuális gépről.
5. A munkaterhelések visszaállítása után engedélyezze a visszirányú replikálást, hogy a helyszíni virtuális gépek újra replikálódnak az Azure-ba.

### <a name="can-i-fail-back-to-a-different-location"></a>Visszatérhetek egy másik helyre?
Igen, ha a feladatátvételt az Azure-ba hajtja végre, visszatérhet egy másik helyre, ha az eredeti nem érhető el. [További információk](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).