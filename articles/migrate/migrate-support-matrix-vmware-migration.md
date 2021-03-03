---
title: VMware-Migrálás támogatása Azure Migrateban
description: További információ a VMware virtuális gépek áttelepítésének támogatásáról Azure Migrateban.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: fc3ba062f4995e975015d7c4db145ccde0c3f701
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705211"
---
# <a name="support-matrix-for-vmware-migration"></a>A VMware-migrálás támogatási mátrixa

Ez a cikk a VMware virtuális gépek [Azure Migrate: kiszolgáló áttelepítéssel](migrate-services-overview.md#azure-migrate-server-migration-tool) történő áttelepítésére vonatkozó támogatási beállításokat és korlátozásokat foglalja össze. Ha a VMware virtuális gépeknek az Azure-ba való áttelepítésre való vizsgálatával kapcsolatos információkat keres, tekintse át az [értékelés támogatási mátrixát](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Migrálási lehetőségek

A VMware virtuális gépeket több módon is áttelepítheti:

- **Ügynök nélküli áttelepítés használata**: a virtuális gépek áttelepítése anélkül, hogy bármit telepíteni kellene. Az ügynök nélküli Migrálás [Azure Migrate berendezését](migrate-appliance.md) telepíti.
- **Ügynök alapú áttelepítés használata**: telepítsen egy ügynököt a virtuális gépre a replikáláshoz. Az ügynök-alapú áttelepítés esetében egy [replikációs berendezést](migrate-replication-appliance.md)kell üzembe helyezni.

Tekintse át [ezt a cikket](server-migrate-overview.md) , hogy kiderítse, melyik módszert szeretné használni.

## <a name="migration-limitations"></a>A migrálásra vonatkozó korlátozások

- Egyszerre legfeljebb 10 virtuális gépet választhat a Azure Portalon keresztüli replikáláshoz. Ha több gépet szeretne áttelepíteni, a replikálást a 10 csoportba. A PowerShell-parancsmagok használatával replikálható virtuális gépek száma nem korlátozható. Javasoljuk, hogy az optimális teljesítmény érdekében a PowerShell használatával legfeljebb 500 virtuális gépet replikáljon egyetlen vCenter keresztül.
- A VMware ügynök nélküli Migrálás esetében akár 500 replikációt is futtathat egyszerre az egyes vCenter Serverokból.

## <a name="agentless-migration"></a>Ügynök nélküli áttelepítés 

Ez a szakasz az ügynök nélküli áttelepítés követelményeit foglalja össze.

### <a name="vmware-requirements-agentless"></a>VMware-követelmények (ügynök nélküli)

A táblázat összefoglalja a VMware hypervisor követelményeit.

**VMware** | **Részletek**
--- | ---
**VMware vCenter Server** | Verzió: 5,5, 6,0, 6,5, 6,7, 7,0.
**VMware vSphere ESXI-gazdagép** | Verzió: 5,5, 6,0, 6,5, 6,7, 7,0.
**engedélyek vCenter Server** | Az ügynök nélküli áttelepítés az áttelepítési [készüléket](migrate-appliance.md)használja. A készüléknek az alábbi engedélyekkel kell rendelkeznie vCenter Serverban:<br/><br/> - **Adattár. Tallózás** (adattár – > tallózási adattár): lehetővé teszi a virtuális gépek naplófájljainak böngészését a pillanatképek létrehozásával és törlésével kapcsolatos hibák megoldásához.<br/><br/> - **Adattár. FileManagement** (adattár – > alacsony szintű file Operations): az olvasási/írási/törlési/átnevezési műveletek engedélyezése az adattár böngészőben a pillanatkép létrehozásának és törlésének hibakereséséhez.<br/><br/> - **VirtualMachine.Config. Változáskövetési** (virtuális gép – > lemezellenőrzés nyomon követése): engedélyezheti vagy letilthatja a virtuálisgép-lemezek módosításának nyomon követését, így a pillanatképek között megváltoztathatja az adatblokkokat.<br/><br/> - **VirtualMachine.Config. DiskLease** (virtuális gép – > lemez bérlete): engedélyezze a lemez címbérleti műveleteit egy virtuális gép számára, hogy beolvassa a lemezt a VMware vSphere Virtual Disk Development Kit (VDDK) használatával.<br/><br/> - **VirtualMachine. kiépítés. DiskRandomRead** (virtuális gép – > kiépítés – > csak olvasható lemezes hozzáférés engedélyezése): engedélyezze a lemez megnyitását egy virtuális gépen a lemez olvasásához a VDDK használatával.<br/><br/> - **VirtualMachine. kiépítés. DiskRandomAccess** (virtuális gép – > kiépítés – > a lemezes hozzáférés engedélyezése): engedélyezze a lemez megnyitását egy virtuális gépen a lemez olvasásához a VDDK használatával.<br/><br/> - **VirtualMachine. kiépítés. GetVmFiles** (virtuális gép – > kiépítés – > a virtuális gépek letöltésének engedélyezése): engedélyezi az olvasási műveleteket a virtuális géphez társított fájlokon, letölti a naplókat, és hibaelhárítást végez, ha hiba történik.<br/><br/> - **VirtualMachine. State. \* *_ (Virtuális gép – > pillanatkép-kezelés): lehetővé teszi a virtuális gépek pillanatképek létrehozását és kezelését a replikáláshoz. <br/> <br/> -_* VirtualMachine. Interact.** áramellátás (virtuális gép – > interakció – > kikapcsolva): lehetővé teszi a virtuális gép kikapcsolását az Azure-ba való áttelepítés során.



### <a name="vm-requirements-agentless"></a>VIRTUÁLIS gépekre vonatkozó követelmények (ügynök nélküli)

A táblázat összefoglalja a VMware virtuális gépek ügynök nélküli áttelepítési követelményeit.

**Támogatás** | **Részletek**
--- | ---
**Támogatott operációs rendszerek** | Áttelepítheti az Azure által támogatott [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -és [Linux](../virtual-machines/linux/endorsed-distros.md) -operációs rendszereket.
**Windows rendszerű virtuális gépek az Azure-ban** | Előfordulhat, hogy a Migrálás előtt [módosításokat kell végeznie](prepare-for-migration.md#verify-required-changes-before-migrating) a virtuális gépeken. 
**Linux rendszerű virtuális gépek az Azure-ban** | Előfordulhat, hogy egyes virtuális gépek módosításokat igényelnek, hogy az Azure-ban is futtathatók legyenek.<br/><br/> A Linux rendszerben a Azure Migrate automatikusan végrehajtja a módosításokat a következő operációs rendszereken:<br/> -Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x<br/> -Cent OS 7,7, 7,6, 7,5, 7,4, 6. x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19,04, 19,10, 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8, 9 <br/> Oracle Linux 7,7, 7,7-CI<br/> Más operációs rendszerekhez manuálisan végezze el a [szükséges módosításokat](prepare-for-migration.md#verify-required-changes-before-migrating) .
**Linux rendszerű rendszerindítás** | Ha a/boot dedikált partíción van, akkor az operációsrendszer-lemezen kell lennie, és nem szabad több lemezre osztania.<br/> Ha a/boot a gyökér (/) partíció része, akkor a "/" partíciónak az operációsrendszer-lemezen kell lennie, és nem szabad más lemezekre kiterjednie.
**UEFI-rendszerindítás** | Támogatott. Az UEFI-alapú virtuális gépek migrálása az Azure 2. generációs virtuális gépekre történik. 
**Lemez mérete** | 2 TB operációsrendszer-lemez;  32 TB adatlemezek esetében.
**Lemezterület-korlátok** |  Akár 60 lemez/virtuális gép.
**Titkosított lemezek/kötetek** | A titkosított lemezekkel/kötetekkel rendelkező virtuális gépek migrálása nem támogatott.
**Megosztott lemezes fürt** | Nem támogatott.
**Független lemezek** | Nem támogatott.
**RDM/továbbító lemezek** | Ha a virtuális gépek RDM vagy továbbító lemezzel rendelkeznek, ezek a lemezek nem replikálódnak az Azure-ba.
**NFS** | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva.
**iSCSI-tárolók** | Az iSCSI-tárolókkal rendelkező virtuális gépek nem támogatottak az ügynök nélküli Migrálás esetén.
**Többutas IO** | Nem támogatott.
**Tárolási vMotion** | Nem támogatott. A replikáció nem fog működni, ha a virtuális gép tárolási vMotion használ.
**Összevont hálózati adapterek** | Nem támogatott.
**IPv6** | Nem támogatott.
**Céllemez** | A virtuális gépeket csak felügyelt lemezekre lehet áttelepíteni (standard HDD, standard SSD, prémium SSD) az Azure-ban.
**Egyidejű replikáció** | 500 virtuális gépek száma vCenter Server. Ha többre van szüksége, a 500-es kötegekben telepítse át őket.
**Azure-beli VM-ügynök automatikus telepítése (Windows-ügynök)** | A Windows Server 2008 R2 és újabb verziók esetében támogatott.

### <a name="appliance-requirements-agentless"></a>Berendezésre vonatkozó követelmények (ügynök nélküli)

Az ügynök nélküli áttelepítés a [Azure Migrate készüléket](migrate-appliance.md)használja. A készüléket VMware virtuális gépként is telepítheti egy PETESEJT-sablonnal, amely a vCenter Serverba lett importálva, vagy egy [PowerShell-parancsfájl](deploy-appliance-script.md)használatával.

- A VMware-es [készülékekre vonatkozó követelmények](migrate-appliance.md#appliance---vmware) ismertetése.
- Ismerje meg azokat az URL-címeket, amelyekhez a készüléknek [nyilvános](migrate-appliance.md#public-cloud-urls) és [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkben kell hozzáférnie.
- Azure Government a készüléket [a szkript használatával](deploy-appliance-script-government.md)kell telepítenie.

### <a name="port-requirements-agentless"></a>Portra vonatkozó követelmények (ügynök nélküli)

**Eszköz** | **Kapcsolat**
--- | ---
Berendezés | Az 443-as porton kimenő kapcsolatok a replikált adatok az Azure-ba való feltöltéséhez, valamint a replikáció és az áttelepítés összehangolása Azure Migrate szolgáltatásokkal való kommunikációhoz.
vCenter-kiszolgáló | Bejövő kapcsolatok a 443-as porton, hogy a készülék koordinálja a replikációt – pillanatképek létrehozása, Adatmásolás, kiadási Pillanatképek
vSphere/ESXI-gazdagép | Bejövő a 902-es TCP-porton, hogy a készülék Pillanatképek adatait replikálja.

## <a name="agent-based-migration"></a>Ügynök alapú áttelepítés 


Ez a szakasz az ügynök alapú áttelepítés követelményeit foglalja össze.


### <a name="vmware-requirements-agent-based"></a>VMware-követelmények (ügynök-alapú)

Ez a táblázat összefoglalja a VMware virtualizációs kiszolgálók értékelésének támogatását és korlátozásait.

**VMware-követelmények** | **Részletek**
--- | ---
**VMware vCenter Server** | 5,5, 6,0, 6,5 vagy 6,7.
**VMware vSphere ESXI-gazdagép** | 5,5, 6,0, 6,5 vagy 6,7.
**engedélyek vCenter Server** | Írásvédett fiók a vCenter Serverhoz.

### <a name="vm-requirements-agent-based"></a>VIRTUÁLIS gépekre vonatkozó követelmények (ügynök-alapú)

A táblázat összefoglalja a VMware VM-támogatást az áttelepíteni kívánt VMware virtuális gépekhez az ügynök-alapú áttelepítés használatával.

**Támogatás** | **Részletek**
--- | ---
**Gépi munkaterhelés** | A Azure Migrate támogatja az olyan munkaterhelések áttelepítését (például Active Directory, SQL Server stb.), amelyek egy támogatott gépen futnak.
**Operációs rendszerek** | A legfrissebb információkért tekintse át Site Recovery [operációs rendszer támogatását](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) . Azure Migrate azonos virtuálisgép-operációsrendszer-támogatást biztosít.
**Linux fájlrendszer/vendég tárterület** | A legfrissebb információkért tekintse át a Site Recovery [Linux-fájlrendszer támogatását](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) . Azure Migrate azonos a Linux fájlrendszer támogatásával.
**Hálózat/tárterület** | A legfrissebb információkért tekintse át a Site Recovery [hálózati](../site-recovery/vmware-physical-azure-support-matrix.md#network) és [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#storage) előfeltételeit. Azure Migrate azonos hálózati/tárolási követelményeket biztosít.
**Azure-követelmények** | A legfrissebb információkért tekintse át az [Azure hálózati](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)és [számítási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) követelményeit site Recovery. A Azure Migrate a VMware-Migrálás követelményeivel azonos.
**Mobilitási szolgáltatás** | Az áttelepíteni kívánt virtuális gépeken telepíteni kell a mobilitási szolgáltatás ügynökét.
**UEFI-rendszerindítás** | Támogatott. Az UEFI-alapú virtuális gépek migrálása az Azure 2. generációs virtuális gépekre történik. 
**UEFI – biztonságos rendszerindítás**         | Migrálás esetén nem támogatott.
**Céllemez** | A virtuális gépeket csak felügyelt lemezekre lehet áttelepíteni (standard HDD, standard SSD, prémium SSD) az Azure-ban.
**Lemez mérete** | 2 TB operációsrendszer-lemez; 32 TB adatlemezek esetében.
**Lemezterület-korlátok** |  Akár 63 lemez/virtuális gép.
**Titkosított lemezek/kötetek** | A titkosított lemezekkel/kötetekkel rendelkező virtuális gépek migrálása nem támogatott.
**Megosztott lemezes fürt** | Nem támogatott.
**Független lemezek** | Támogatott.
**Továbbító lemezek** | Támogatott.
**NFS** | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva.
**iSCSI-tárolók** | Támogatott.
**Többutas IO** | Nem támogatott.
**Tárolási vMotion** | Támogatott
**Összevont hálózati adapterek** | Nem támogatott.
**IPv6** | Nem támogatott.




### <a name="appliance-requirements-agent-based"></a>Berendezésre vonatkozó követelmények (ügynök-alapú)

Amikor beállítja a replikációs berendezést az Azure Migrate központban megadott petesejtek sablonnal, a készülék futtatja a Windows Server 2016-et, és megfelel a támogatási követelményeknek. Ha a replikációs készüléket manuálisan állítja be egy fizikai kiszolgálón, akkor ellenőrizze, hogy az megfelel-e a követelményeknek.

- Ismerje meg a VMware-hez készült [replikációs berendezések követelményeit](migrate-replication-appliance.md#appliance-requirements) .
- A készülékre telepíteni kell a MySQL-t. Tudnivalók a [telepítési lehetőségekről](migrate-replication-appliance.md#mysql-installation).
- Ismerje meg azokat az URL-címeket, amelyekhez a replikációs berendezésnek [nyilvános](migrate-replication-appliance.md#url-access) és [kormányzati](migrate-replication-appliance.md#azure-government-url-access) felhőkben kell hozzáférnie.
- Tekintse át azokat a [portokat](migrate-replication-appliance.md#port-access) , amelyeket a replikációs berendezésnek el kell érnie.

### <a name="port-requirements-agent-based"></a>Portra vonatkozó követelmények (ügynök-alapú)

**Eszköz** | **Kapcsolat**
--- | ---
Virtuális gépek | A virtuális gépeken futó mobilitási szolgáltatás a replikációs felügyelet érdekében a HTTPS 443 bejövő porton keresztül kommunikál a helyszíni replikációs berendezéssel (konfigurációs kiszolgálóval).<br/><br/> A virtuális gépek replikációs adatküldést küldenek a folyamat-kiszolgálónak (amely a konfigurációs kiszolgáló gépen fut) a HTTPS 9443 bejövő porton. Ez a port módosítható.
Replikációs berendezés | A replikációs berendezés az Azure-ba irányuló replikációt a HTTPS 443 kimenő porton keresztül hangolja össze.
Folyamatkiszolgáló | A Process Server replikációs adatokat fogad, optimalizálja és titkosítja, majd az Azure Storage-ba küldi az 443-as porton keresztül.<br/> Alapértelmezés szerint a Process Server fut a replikációs berendezésen.

## <a name="azure-vm-requirements"></a>Azure virtuálisgép-követelmények

Az Azure-ba replikált összes helyszíni virtuális gépnek (ügynök nélküli vagy ügynök-alapú áttelepítéssel) meg kell felelnie az ebben a táblázatban szereplő Azure-beli virtuális gépekre vonatkozó követelményeknek. 

**Összetevő** | **Követelmények** 
--- | --- | ---
Vendég operációs rendszer | Ellenőrzi a VMware virtuális gépek támogatott operációs rendszereinek áttelepítését.<br/> A támogatott operációs rendszereken futó munkaterhelések áttelepíthetők. 
Vendég operációs rendszer architektúrája | 64 bites. 
Operációsrendszer-lemez mérete | Akár 2 048 GB-ig. 
Operációsrendszer-lemezek száma | 1 
Adatlemezek száma | 64 vagy kevesebb. 
Adatlemez mérete | Akár 32 TB
Hálózati adapterek | Több adapter is támogatott.
Megosztott VHD | Nem támogatott. 
FC-lemez | Nem támogatott. 
BitLocker | Nem támogatott.<br/><br/> A számítógép migrálása előtt le kell tiltani a BitLockert.
a virtuális gép neve | 1 – 63 karakter.<br/><br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevének betűvel vagy számmal kell kezdődnie és végződnie. 
Kapcsolat az áttelepítés után – Windows | Kapcsolódás a Windows rendszerű Azure-beli virtuális gépekhez a Migrálás után:<br/><br/> – Az áttelepítés előtt engedélyezze az RDP-t a helyszíni virtuális gépen.<br/><br/> Ellenőrizze, hogy a **Nyilvános** profilnál felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows-tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.<br/><br/> A helyek közötti VPN-hozzáféréshez engedélyezze az RDP-t, és engedélyezze az RDP használatát a **Windows tűzfal**  ->  **engedélyezett alkalmazásaiban és szolgáltatásaiban** a **tartomány és a magánhálózatok** számára.<br/><br/> Továbbá győződjön meg arról, hogy az operációs rendszer SAN-szabályzata **OnlineAll** értékre van állítva. [További információk](prepare-for-migration.md).
Kapcsolat Migrálás után – Linux | Kapcsolódás az Azure-beli virtuális gépekhez az SSH használatával történő áttelepítés után:<br/><br/> Az áttelepítés előtt a helyszíni gépen győződjön meg arról, hogy a Secure Shell szolgáltatás indításra van beállítva, és hogy a tűzfalszabályok engedélyezik az SSH-kapcsolatokat.<br/><br/> A feladatátvételt követően az Azure-beli virtuális gépen engedélyezze az SSH-porthoz való bejövő kapcsolatokat a hálózati biztonsági csoportra vonatkozó szabályokra vonatkozóan a feladatátvételen átesett virtuális gépen, valamint azt az Azure-alhálózatot, amelyhez csatlakoztatva van.<br/><br/> Továbbá adjon hozzá egy nyilvános IP-címet a virtuális géphez.  


## <a name="next-steps"></a>Következő lépések

[Válassza ki](server-migrate-overview.md) a VMware áttelepítési lehetőséget.
