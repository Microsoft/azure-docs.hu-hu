---
title: Gyakori kérdések az Azure-beli virtuális gépek vész-helyreállításáról Azure Site Recovery
description: Ez a cikk az Azure-beli virtuális gépek vész-helyreállításával kapcsolatos gyakori kérdésekre ad választ Azure Site Recovery használatakor.
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: 5309fd60640c45ade42bab4c5727cf1f0a8d9d70
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025475"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Gyakori kérdések: Azure-ból Azure-ba történő vészhelyreállítás

Ez a cikk az Azure-beli virtuális gépeknek egy másik Azure-régióba való vész-helyreállításával kapcsolatos gyakori kérdésekre ad választ az [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával.

## <a name="general"></a>Általános kérdések

### <a name="how-is-site-recovery-priced"></a>Hogyan Site Recovery díjszabása?

Ismerje meg az Azure-beli virtuális gépek vész-helyreállításának [költségeit](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) .

### <a name="how-does-the-free-tier-work"></a>Hogyan működik az ingyenes platform?

Minden Site Recovery által védett példány díjmentes az első 31 nap védelméhez. Ezen időszak után az egyes példányok védelme a [díjszabásban](https://azure.microsoft.com/pricing/details/site-recovery/)foglalt díjszabás szerint történik. A költségeket az [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/?service=site-recovery)használatával becsülheti meg.

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>Számítanak fel más Azure-díjakat az első 31 napban?

Igen. Bár a védett példányok első 31 napján Azure Site Recovery ingyenes, az Azure Storage, a tárolási tranzakciók és az adatforgalom díját is felmerülhet. A helyreállított virtuális gépek is felmerülhetnek az Azure számítási díjaival. G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>Hogyan az Azure-beli virtuális gépek vész-helyreállításának első lépéseihez?

1. [Ismerje](azure-to-azure-architecture.md) meg az Azure-beli virtuális gép vész-helyreállítási architektúráját.
2. [Tekintse át](azure-to-azure-support-matrix.md) a támogatási követelményeket.
3. [Állítsa be](azure-to-azure-how-to-enable-replication.md) a vész-helyreállítást az Azure-beli virtuális gépekhez.
4. [Futtasson vész-helyreállítási gyakorlatot](azure-to-azure-tutorial-dr-drill.md) egy feladatátvételi teszttel.
5. [Futtasson egy teljes feladatátvételt](azure-to-azure-tutorial-failover-failback.md) egy másodlagos Azure-régióban.
6. [Visszatérhet a](azure-to-azure-tutorial-failback.md) másodlagos régióból az elsődleges régióba.

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Hogyan biztosítható a kapacitás a céltartományban?

A Site Recovery csapat és az Azure Capacity Management csapata megtervezi az infrastruktúra megfelelő kapacitását. A feladatátvétel indításakor a csapatok is segítenek biztosítani, hogy a Site Recovery által védett virtuálisgép-példányok a célként megadott régióban legyenek védve.

## <a name="replication"></a>Replikálás

### <a name="can-i-replicate-vms-with-disk-encryption"></a>Replikálhatók a lemezes titkosítással rendelkező virtuális gépek?

Igen. Site Recovery támogatja a Azure Disk Encryption (ADE)-t használó virtuális gépek vész-helyreállítását. Amikor engedélyezi a replikációt, az Azure a felhasználói környezetben átmásolja a szükséges lemez-titkosítási kulcsokat és titkos kódokat a forrás régiójából a célként megadott régióba. Ha nem rendelkezik a szükséges engedélyekkel, a biztonsági rendszergazda parancsfájlt használhat a kulcsok és a titkos kódok másolásához.

- Site Recovery támogatja az ADE használatát a Windows rendszerű Azure-beli virtuális gépekhez.
- Site Recovery a következőket támogatja:
    - Az ADE 0,1-es verziója, amelyhez Azure Active Directory (Azure AD) szükséges séma tartozik.
    - Az ADE 1,1-es verziója, amely nem igényel Azure AD-t. A 1,1-es verzióban a Windows Azure-beli virtuális gépeknek felügyelt lemezekkel kell rendelkezniük.
    - [További információ](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema). a bővítmény sémái.

[További](azure-to-azure-how-to-enable-replication-ade-vms.md) információ a titkosított virtuális gépek replikálásának engedélyezéséről.

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>Választhatok egy másik erőforráscsoport Automation-fiókját?

Ha engedélyezi a Site Recovery számára a replikált Azure-beli virtuális gépeken futó mobilitási szolgáltatás bővítményének frissítését, az Azure Automation-fiók használatával globális runbook (Azure-szolgáltatások által használt) helyez üzembe. Használhatja a Site Recovery által létrehozott Automation-fiókot, vagy választhat, hogy meglévő Automation-fiókot használ-e. 

A portálon jelenleg csak egy Automation-fiókot lehet kijelölni ugyanabban az erőforráscsoportban, mint a tárolót. Az Automation-fiókot egy másik erőforráscsoporthoz is kiválaszthatja a PowerShell használatával. [További információ](azure-to-azure-autoupdate.md#enable-automatic-updates).

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>Ha olyan ügyfél-automatizálási fiókot használok, amely nem a tároló erőforráscsoporthoz van, törölheti az alapértelmezett runbook?

Igen, akkor törölheti, ha nincs rá szüksége. 

### <a name="can-i-replicate-vms-to-another-subscription"></a>Lehetséges a virtuális gépek másik előfizetésbe történő replikálása?

Igen, az Azure-beli virtuális gépeket egyetlen Azure AD-bérlőn belüli előfizetésre is replikálhatja. Ha engedélyezi a virtuális gépek vész-helyreállítási feltételeit, alapértelmezés szerint a forrás virtuális gép által megjelenített cél előfizetés látható. Módosíthatja a célként megadott előfizetést, és az egyéb beállításokat (például az erőforráscsoportot és a virtuális hálózatot) automatikusan kitölti a kijelölt előfizetésből.

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>Replikálhat virtuális gépeket egy rendelkezésre állási zónában egy másik régióba?

Igen, a rendelkezésre állási zónákban lévő virtuális gépeket egy másik Azure-régióba is replikálhatja. 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>Replikálhatók a nem zónás virtuális gépeket ugyanazon a régión belüli zónába? 

Ez a portálon nem támogatott. Ezt a REST API/PowerShell használatával teheti meg.

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>Replikálható a zónákba tartozó virtuális gépek egy másik zónába ugyanabban a régióban?

Ez a támogatás csak néhány régióra korlátozódik. [További információ](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

### <a name="can-i-exclude-disks-from-replication"></a>Ki lehet zárni a lemezeket a replikációból?

Igen, kihagyhatja a lemezeket a replikáció beállításakor a PowerShell használatával. [További információ](azure-to-azure-exclude-disks.md).

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>Replikálható a replikált virtuális gépekhez hozzáadott új lemezek?

A felügyelt lemezekkel rendelkező replikált virtuális gépek esetében új lemezeket adhat hozzá, és engedélyezheti számukra a replikálást. Új lemez hozzáadásakor a replikált virtuális gép figyelmeztető üzenetet jelenít meg arról, hogy a virtuális gép egy vagy több lemeze védelmet biztosít. 

- Ha engedélyezi a további lemezek replikálását, a figyelmeztetés eltűnik a kezdeti replikálás után.
- Ha nem szeretné engedélyezni a lemez replikálását, utasítsa el a figyelmeztetést.
- Ha egy virtuális gépet egy hozzáadott lemezzel hajt végre, a replikációs pontok a helyreállításhoz elérhető lemezeket jelenítik meg. Ha például egy lemezrel egy második lemezt ad hozzá egy virtuális géphez, a Hozzáadás előtt létrehozott replikációs pont "1/2 lemezként" jelenik meg.

A Site Recovery nem támogatja a lemezek "gyors eltávolítását" a replikált virtuális gépről. Ha eltávolít egy VM-lemezt, le kell tiltania, majd újra engedélyeznie kell a virtuális gép replikálását.

### <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran lehet replikálni az Azure-ba?

Az Azure-beli virtuális gépek másik Azure-régióba való replikálásakor a replikáció folyamatos. [További](./azure-to-azure-architecture.md#replication-process) információ a replikálás működéséről.

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>Replikálható a virtuális gépek egy adott régión belül? 

A Site Recovery nem használhatók egy adott régión belüli lemezek replikálásához.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Replikálhat virtuálisgép-példányokat bármely Azure-régióba?

A virtuális gépeket replikálhatja és helyreállíthatja az azonos földrajzi fürtön belüli két régió között. A földrajzi fürtök adatkéséssel és szuverenitással vannak meghatározva. [További](./azure-to-azure-support-matrix.md#region-support) információ a régiók támogatásáról.

### <a name="does-site-recovery-need-internet-connectivity"></a>Szükség van Site Recovery internetkapcsolatra?

Nem, de a virtuális gépeknek hozzá kell férniük Site Recovery URL-címekhez és IP-tartományokhoz. [További információ](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>Replikálható az alkalmazások az erőforráscsoportok között?

Igen, replikálhatja az alkalmazást, és megtarthatja a vész-helyreállítási konfigurációt egy külön erőforráscsoporthoz.

Ha például az alkalmazások három szinten (alkalmazás/adatbázis/web) vannak a különböző erőforráscsoportok esetében, az összes csomag védeleméhez háromszor engedélyeznie kell a replikálást. Site Recovery három különböző erőforráscsoporthoz replikálja a három szintet.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>Át lehet helyezni a Storage-fiókokat az erőforráscsoportok között?

Nem, ez nem támogatott. Ha véletlenül más erőforráscsoporthoz helyezi át a tárolási fiókokat, és törli az eredeti erőforráscsoportot, akkor létrehozhat egy új erőforráscsoportot a régi erőforráscsoporthoz megegyező névvel, majd áthelyezheti a Storage-fiókot erre az erőforráscsoporthoz.

## <a name="replication-policy"></a>Replikációs szabályzat

### <a name="what-is-a-replication-policy"></a>Mi a replikációs házirend?

A replikációs házirend meghatározza a helyreállítási pontok megőrzési előzményeit, valamint az alkalmazás-konzisztens Pillanatképek gyakoriságát.  A Site Recovery a következőképpen hozza létre az alapértelmezett replikációs házirendet:

- A helyreállítási pontok 24 óráig tartása.
- Az alkalmazással konzisztens pillanatképek készítése négy óránként.

[További](azure-to-azure-how-to-enable-replication.md#customize-target-resources) információ a replikációs beállításokról.

### <a name="whats-a-crash-consistent-recovery-point"></a>Mi az összeomlás-konzisztens helyreállítási pont?

Az összeomlás-konzisztens helyreállítási pont lemezen lévő adatokkal rendelkezik, mintha a pillanatkép során kihúzta a kiszolgálóról a hálózati kábelt. A pillanatkép elkészítésekor nem tartalmaz semmit a memóriában.

Napjainkban a legtöbb alkalmazás helyreállíthatja az összeomlás-konzisztens pillanatképeket is. Az összeomlás-konzisztens helyreállítási pont általában elég a nem adatbázisos operációs rendszerekhez, valamint olyan alkalmazásokhoz, mint a fájlkiszolgálók, a DHCP-kiszolgálók és a nyomtatókiszolgálók.

A Site Recovery öt percenként automatikusan létrehoz egy összeomlás-konzisztens helyreállítási pontot.

### <a name="whats-an-application-consistent-recovery-point"></a>Mi az az alkalmazás-konzisztens helyreállítási pont?

Az alkalmazással konzisztens helyreállítási pontok az alkalmazással konzisztens Pillanatképek alapján jönnek létre. Ugyanazokat az adatokat rögzítik, mint az összeomlás-konzisztens Pillanatképek, valamint az adatok rögzítése a memóriában és a folyamatban lévő összes tranzakció.

További tartalom miatt az alkalmazás-konzisztens Pillanatképek a leginkább érintettek, és a leghosszabb időt veszik igénybe. Javasoljuk, hogy az adatbázis-operációs rendszerekhez és az alkalmazásokhoz, például a SQL Serverhoz az alkalmazás-konzisztens helyreállítási pontokat. A Windows esetében az alkalmazás-konzisztens Pillanatképek a Kötet árnyékmásolata szolgáltatás (VSS) használják.

### <a name="do-app-consistent-recovery-points-impact-performance"></a>Az alkalmazás-konzisztens helyreállítási pontok hatással vannak a teljesítményre?

 Mivel az alkalmazás-konzisztens helyreállítási pontok rögzítik a memóriában tárolt összes adat mennyiségét és feldolgozását, ha gyakran vannak rögzítve, hatással lehet a teljesítményre, ha a munkaterhelés már foglalt. Nem javasoljuk, hogy túl gyakran rögzítsen a nem adatbázis-alapú számítási feladatokhoz. Még az adatbázis-munkaterhelések esetében is elég egy óra.

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>Milyen minimális gyakorisággal hozhatók létre az alkalmazások konzisztens helyreállítási pontjai?

Site Recovery az alkalmazással konzisztens helyreállítási pontokat hozhat létre, amelyek minimális gyakorisága egy óra.

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>Engedélyezhető az alkalmazás-konzisztens replikáció Linux rendszerű virtuális gépekhez?

Igen. A Linux mobilitási ügynöke egyéni parancsfájlokat támogat az alkalmazások konzisztenciájához. Az ügynök felhasznál egy egyéni parancsfájlt az előzetes és utáni beállításokkal. [További információ](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>Hogyan történik a helyreállítási pontok létrehozása és mentése?

Ha szeretné megtudni, hogyan hozza létre a Site Recovery helyreállítási pontokat, használjon példát. 

- A replikációs házirendek 24 órán át őrzik meg a helyreállítási pontokat, és óránként egy alkalmazás-konzisztens gyakorisági pillanatképet vesznek igénybe.
- A Site Recovery öt percenként létrehoz egy összeomlás-konzisztens helyreállítási pontot. Ez a gyakoriság nem módosítható.
- Site Recovery a helyreállítási pontokat egy óra elteltével, egy pont óránkénti mentésével.

Tehát az elmúlt órában 12 összeomlás-konzisztens pontot és egy alkalmazás-konzisztens pontot választhat, ahogy az ábrán is látható.

   ![Generált helyreállítási pontok listája](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>Meddig lehet visszaállítani?

A legrégebben használható helyreállítási pont 72 óra.

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>Mi történik, ha Site Recovery nem tud 24 óránál hosszabb helyreállítási pontokat készíteni? 

Ha 24 órás replikációs házirendtel rendelkezik, és Site Recovery 24 óránál hosszabb helyreállítási pontokat nem tud előállítani, a régi helyreállítási pontok maradnak. Site Recovery csak akkor helyettesíti a legrégebbi pontot, ha új pontokat hoz létre. Amíg nincs új helyreállítási pont, a régi pontok továbbra is megmaradnak az adatmegőrzési időszak elérése után.

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>Módosíthatom a replikációs házirendet a replikáció engedélyezése után?

Igen. A tároló > **site Recovery infrastruktúra**-  >  **replikációs házirendek** területen válassza ki és szerkessze a szabályzatot. A módosítások a meglévő házirendekre is érvényesek.

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>Az összes helyreállítási pont egy teljes virtuálisgép-példány?

Az első létrehozott helyreállítási pont a teljes másolattal rendelkezik. Az egymást követő helyreállítási pontok különbözeti változásokkal rendelkeznek.

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>Növeli a helyreállítási pontok megőrzését a tárolási költségek növelésével?

Igen. Ha például az adatmegőrzést 24 órán át 72-re emeli, Site Recovery a helyreállítási pontokat további 48 óráig menti. A hozzáadott idő inkurzív tárterületének módosítása. Példaként, ha egyetlen helyreállítási pont esetében 10 GB-os különbözeti változás történt, és a havi $0,16 GB-os költséggel, akkor a további díjak havi $1,60 × 48.

## <a name="multi-vm-consistency"></a>Több virtuális gépre kiterjedő konzisztencia

### <a name="what-is-multi-vm-consistency"></a>Mi a több virtuális gépre kiterjedő konzisztencia?

A több virtuális gépre kiterjedő konzisztencia biztosítja, hogy a helyreállítási pontok konzisztensek legyenek a replikált virtuális gépek között.

- Ha engedélyezi a több virtuális gépre kiterjedő konzisztenciát, a Site Recovery az összes gépen létrehoz egy replikációs csoportot, amelyen engedélyezve van a beállítás. 
- Ha a replikációs csoportban lévő gépek feladatátvételét hajtja végre, az összeomlás-konzisztens és az alkalmazással konzisztens helyreállítási pontokat tartalmaz.

[Megtudhatja](azure-to-azure-tutorial-enable-replication.md#enable-replication) , hogyan engedélyezheti a több virtuális gépre kiterjedő konzisztenciát.

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>Átadhatok feladatátvételi csoportba egyetlen virtuális gépet?

Nem. Ha engedélyezi a több virtuális gépre kiterjedő konzisztenciát, az azt eredményezi, hogy az alkalmazás a replikációs csoportban lévő összes virtuális gépről függőséggel rendelkezik, és az egyetlen virtuális gép feladatátvétele nem engedélyezett.

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>Hány virtuális gépet lehet egyszerre replikálni egy csoportban?

A 16 virtuális gépet egyszerre replikálhatja egy replikációs csoportba.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Mikor engedélyezzem a több virtuális gépre kiterjedő konzisztenciát?

A több virtuális gépre kiterjedő konzisztencia a CPU-igényes, és lehetővé teszi, hogy a számítási feladatok teljesítményét is befolyásolja. Csak akkor engedélyezze, ha a virtuális gépek ugyanazt a számítási feladatot futtatják, és több gépen is konzisztensen kell lennie. Ha például két SQL Server példánya van, és két webkiszolgálója van egy alkalmazásban, akkor a több virtuális gépre kiterjedő konzisztenciát csak a SQL Server példányok esetében engedélyezze.

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>Hozzáadhatok replikáló virtuális gépet egy replikációs csoporthoz?

Amikor engedélyezi a replikációt egy virtuális gép számára, felveheti azt egy új replikációs csoportba vagy egy meglévő csoportba. Nem adhat hozzá olyan virtuális gépet, amely már replikál egy csoportba. 
 
## <a name="failover"></a>Feladatátvétel

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Hogyan biztosítható a kapacitás a céltartományban?

A Site Recovery csapat és az Azure Capacity Management csapata megtervezi az infrastruktúra megfelelő kapacitását. A feladatátvétel elindításakor a csapatok is segítenek biztosítani, hogy a Site Recovery által védett virtuálisgép-példányok üzembe helyezhetők legyenek a célként megadott régióban.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?

A feladatátvétel nem automatikus. A feladatátvételt egyetlen kattintással elindíthatja a portálon, vagy a  [PowerShell](azure-to-azure-powershell.md) használatával aktiválhatja a feladatátvételt.

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>Megtarthatok egy nyilvános IP-címet a feladatátvétel után?

Feladatátvétel után nem lehet megtartani a nyilvános IP-címet egy éles alkalmazás számára.

Ha a feladatátvételi folyamat részeként munkaterhelést hoz létre, hozzá kell rendelnie egy Azure nyilvános IP-cím erőforrást. Az erőforrásnak elérhetőnek kell lennie a célként megadott régióban. Az Azure nyilvános IP-cím erőforrását manuálisan is hozzárendelheti, vagy automatizálhatja egy helyreállítási terv használatával. [Megtudhatja](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan) , hogyan állíthatja be a nyilvános IP-címeket a feladatátvétel után.

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>Megtarthatok egy magánhálózati IP-címet a feladatátvétel után?

Igen. Alapértelmezés szerint az Azure-beli virtuális gépek vész-helyreállításának engedélyezésekor Site Recovery a forrás erőforrás-beállítások alapján hozza létre a cél erőforrásokat. A statikus IP-címmel konfigurált Azure-beli virtuális gépek esetében a Site Recovery a célként megadott virtuális gép esetében ugyanazt az IP-címet próbálja kiépíteni, ha az nincs használatban.
[További információ az](site-recovery-retain-ip-azure-vm-failover.md) IP-címek feladatátvétel utáni megőrzéséről.

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>Miért van egy virtuális gép hozzárendelve egy új IP-címhez a feladatátvétel után?

Site Recovery megpróbálja megadni az IP-címet a feladatátvétel időpontjában. Ha egy másik virtuális gép ezt a címet használja, Site Recovery beállítja a következő elérhető IP-címet célként.

[További információ a](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms) hálózati leképezés és a virtuális hálózatok IP-címzésének beállításáról.

### <a name="whats-the-latest-recovery-point"></a>Mi a *legújabb* helyreállítási pont?

A *Legutóbbi (legalacsonyabb RPO)* helyreállítási pont beállítása a következő:

1. Először feldolgozza a Site Recoveryba küldendő összes adatfeldolgozást.
2. Miután a szolgáltatás feldolgozta az adatfeldolgozást, minden egyes virtuális gép számára létrehoz egy helyreállítási pontot, mielőtt feladatátvételt hajt végre a virtuális gépen. Ez a beállítás a legalacsonyabb helyreállítási időkorlátot (RPO) adja meg.
3. A feladatátvételt követően létrehozott virtuális gép a feladatátvétel elindításakor a Site Recovery replikált összes adattal rendelkezik.

### <a name="do-latest-recovery-points-impact-failover-rto"></a>A *legújabb* helyreállítási pontok hatással vannak a feladatátvételi RTO?

Igen. Site Recovery a feladatátvétel előtt dolgozza fel az összes függőben lévő adatát, így ez a beállítás magasabb helyreállítási időcélkitűzéssel (RTO) rendelkezik, mint a többi lehetőség.

### <a name="whats-the-latest-processed-recovery-option"></a>Mi a *legújabb feldolgozott* helyreállítási lehetőség?

A *legújabb feldolgozott* lehetőség a következő műveleteket végzi el:

1. Az összes virtuális gép feladatátvétele a Site Recovery által feldolgozott legújabb helyreállítási pontra történik. Ez a beállítás alacsony RTO biztosít, mivel a feldolgozatlan adatmennyiségek feldolgozása nem történik meg.

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>Mi a teendő, ha váratlan leállás történt az elsődleges régióban?

Elindíthatja a feladatátvételt. Site Recovery nem kell kapcsolódnia az elsődleges régióhoz a feladatátvétel elvégzéséhez.

### <a name="what-is-the-rto-of-a-vm-failover"></a>Mi a virtuális gép feladatátvételének RTO?

Site Recovery RTO SLA-ja [két óra](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). A legtöbb esetben a Site Recovery perceken belül feladatátvételt hajt végre a virtuális gépeken. A RTO kiszámításához tekintse át a feladatátvételi feladatot, amely a virtuális gép üzembe helyezésének időpontját mutatja. 

## <a name="recovery-plans"></a>Helyreállítási tervek

### <a name="whats-a-recovery-plan"></a>Mi a helyreállítási terv?

A Site Recovery [helyreállítási terve](site-recovery-create-recovery-plans.md) összehangolja a virtuális gépek feladatátvételét és helyreállítását. Ezzel a módszerrel a helyreállítás konzisztens, ismételhető és automatizált lehet. A következő műveleteket végzi el:

- Olyan virtuális gépek csoportját határozza meg, amelyek feladatátvételsel működnek együtt
- Meghatározza a virtuális gépek közötti függőségeket, így az alkalmazás pontosan megkerül.
- Automatizálja a helyreállítást, és az egyéni manuális műveleteket is végrehajtja a virtuális gép feladatátvételét nem tartalmazó feladatokhoz. 


### <a name="how-does-sequencing-work"></a>Hogyan működik az előkészítési folyamat?

Egy helyreállítási tervben több virtuális gépet is létrehozhat az előkészítéshez. A csoportok egyidejű feladatátvételt végeznek, így az ugyanahhoz a csoporthoz tartozó virtuális gépek együttesen működnek. [További információ](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hogyan találhatom meg a helyreállítási terv RTO?

A helyreállítási terv RTO ellenőrzéséhez végezzen feladatátvételi tesztet a helyreállítási tervhez. **Site Recovery feladatokban** ellenőrizze a feladatátvételi teszt időtartamát. A példában a képernyőképen a **SAPTestRecoveryPlan** -teszt feladatátvételi feladata 8 perc és 59 másodperc volt.

![A RTO-teszt feladatátvételének időtartamát ábrázoló feladatok listázása](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>Hozzáadhatok Automation-runbookok helyreállítási tervekhez?

Igen. [További információ](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Ismételt védelem és feladat-visszavétel

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>A feladatátvétel után a másodlagos régióban lévő virtuális gépek automatikusan védettek? 

Nem. Ha az egyik régióból a másikba hajtja végre a virtuális gépek feladatátvételét, a virtuális gépek nem védett állapotban kezdődnek a cél vész-helyreállítási régióban. A másodlagos régióban lévő virtuális gépek [újravédéséhez](./azure-to-azure-how-to-reprotect.md) engedélyezze a replikálást az elsődleges régióba.

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>Az ismételt védelem során a rendszer az összes, a másodlagos régióból az elsődlegesre replikált adatforrást? 

Ez a függvénytől függ. Ha a forrásoldali virtuális gép létezik, akkor a rendszer csak a forrásfájl és a céllemez közötti változásokat szinkronizálja. Site Recovery összehasonlítja a lemezeket a különböző értékekkel, majd átviszi az adatátvitelt. Ez a folyamat általában néhány órát vesz igénybe. [További információ](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-long-does-it-take-fail-back"></a>Mennyi ideig tart a feladat-visszavétel?

Az ismételt védelem után a feladat-visszavétel az elsődleges régióból a másodlagos régióba való feladatátvételhez szükséges időt veszi igénybe.

## <a name="capacity"></a><a name="capacity"></a>Kapacitás

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Hogyan biztosítható a kapacitás a céltartományban?

A Site Recovery csapat és az Azure Capacity Management csapata elegendő infrastrukturális kapacitást tervez. A feladatátvétel elindításakor a csapatok is segítenek biztosítani, hogy a Site Recovery által védett virtuálisgép-példányok üzembe helyezhetők legyenek a célként megadott régióban.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Működik Site Recovery fenntartott példányokkal?

Igen, a vész-helyreállítási régióban megvásárolhatja a [fenntartott Azure-beli virtuális gépeket](https://azure.microsoft.com/pricing/reserved-vm-instances/) , és site Recovery feladatátvételi műveletek is használhatók. Nincs szükség további konfigurálásra.

## <a name="security"></a>Biztonság

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>A replikációs adatok el lesznek küldve a Site Recovery szolgáltatáshoz?

Nem, Site Recovery nem metszi a replikált adatokat, és nem rendelkezik információval arról, hogy mi fut a virtuális gépeken. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.

A Site Recovery ISO 27001:2013, 27018, HIPAA és DPA tanúsítvánnyal rendelkezik. A szolgáltatás SOC2 és FedRAMP a ÜSS értékeléseket.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?

Igen, az Azure-ban az átvitelben és a [titkosításban](../storage/common/storage-service-encryption.md) egyaránt támogatott a titkosítás.

## <a name="next-steps"></a>Következő lépések

- [Tekintse át az Azure-ról Azure-ra vonatkozó támogatási követelményeket](azure-to-azure-support-matrix.md).
- [Azure – Azure replikálás beállítása](azure-to-azure-tutorial-enable-replication.md).
- Ha a cikk elolvasása után kérdése merülne fel, tegye fel őket a [Microsoft Q&az Azure Recovery Services kérdéseit tartalmazó oldalra](/answers/topics/azure-site-recovery.html).
