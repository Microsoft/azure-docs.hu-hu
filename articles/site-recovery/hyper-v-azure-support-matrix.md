---
title: A Hyper-V virtuális gépek vész-helyreállításának támogatása az Azure-ba Azure Site Recovery
description: Összefoglalja a Hyper-V virtuális gépeknek az Azure-ba történő helyreállításának támogatott összetevőit és követelményeit Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 8d748f93337a770e0d565bab79fdfb3625bda70d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735522"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>A helyszíni Hyper-V virtuális gépek Azure-ba való vész-helyreállításának támogatási mátrixa

Ez a cikk a helyszíni Hyper-V virtuális gépek Azure-ba történő helyreállításának támogatott összetevőit és beállításait foglalja össze [Azure site Recovery](site-recovery-overview.md)használatával.

>[!NOTE]
> A Site Recovery nem helyezi át vagy nem tárolja az ügyféladatokat a célként megadott régióból, amely során a forrásként szolgáló gépekhez a vész-helyreállítás lett beállítva. Az ügyfelek választhatnak egy Recovery Services-tárolót egy másik régióból is. A Recovery Services-tároló metaadatokat tartalmaz, de nincsenek tényleges ügyféladatok.

## <a name="supported-scenarios"></a>Támogatott esetek

**Forgatókönyv** | **Részletek**
--- | ---
Hyper-V Virtual Machine Manager <br> <br>| A System Center Virtual Machine Manager-hálóban felügyelt Hyper-V-gazdagépeken futó virtuális gépek esetén vész-helyreállítást hajthat végre az Azure-ban.<br/><br/> Ezt a forgatókönyvet a Azure Portal vagy a PowerShell használatával is telepítheti.<br/><br/> Ha a Hyper-V-gazdagépeket a Virtual Machine Manager felügyeli, a másodlagos helyszíni helyre is elvégezheti a vész-helyreállítást. Ha többet szeretne megtudni erről a forgatókönyvről, olvassa el [ezt az oktatóanyagot](hyper-v-vmm-disaster-recovery.md).
Hyper-V Virtual Machine Manager nélkül | A nem a Virtual Machine Manager által felügyelt Hyper-V-gazdagépeken futó virtuális gépek esetén vész-helyreállítást végezhet az Azure-ban.<br/><br/> Ezt a forgatókönyvet a Azure Portal vagy a PowerShell használatával is telepítheti.

## <a name="on-premises-servers"></a>Helyszíni kiszolgálók

**Kiszolgáló** | **Követelmények** | **Részletek**
--- | --- | ---
Hyper-V (Virtual Machine Manager nélkül fut) |  Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 a legújabb frissítésekkel <br/><br/> **Megjegyzés:** Ezen operációs rendszerek Server Core telepítése is támogatott. | Ha már konfigurálta a Windows Server 2012 R2-t a/vagy a SCVMM 2012 R2-vel Azure Site Recovery és tervezi az operációs rendszer frissítését, kövesse az útmutató [dokumentációját.](upgrade-2012R2-to-2016.md)
Hyper-V (fut Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 <br/><br/> **Megjegyzés:** Ezen operációs rendszerek Server Core telepítése is támogatott.  | Ha Virtual Machine Manager van használatban, a Windows Server 2019-gazdagépeket Virtual Machine Manager 2019-ben kell felügyelni. Hasonlóképpen, a Windows Server 2016-gazdagépeket Virtual Machine Manager 2016-ben kell felügyelni.

> [!NOTE]
> Győződjön meg arról, hogy a .NET-keretrendszer 4.6.2 vagy újabb verziója megtalálható a helyszíni kiszolgálón.

## <a name="replicated-vms"></a>Replikált virtuális gépek


A következő táblázat összefoglalja a virtuális gépek támogatását. Site Recovery támogatja a támogatott operációs rendszereken futó munkaterheléseket.

 **Összetevő** | **Részletek**
--- | ---
Virtuális gép konfigurációja | Az Azure-ba replikált virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](#azure-vm-requirements).
Vendég operációs rendszer | Bármely [, az Azure](../cloud-services/cloud-services-guestos-update-matrix.md#family-5-releases)-hoz támogatott vendég operációs rendszer.<br/><br/> A Windows Server 2016 Nano Server nem támogatott.


## <a name="vmdisk-management"></a>VIRTUÁLIS gépek/lemezek kezelése

**Művelet** | **Részletek**
--- | ---
Lemez átméretezése replikált Hyper-V virtuális gépen | Nem támogatott. Tiltsa le a replikációt, végezze el a módosítást, majd engedélyezze újra a virtuális gép replikálását.
Lemez hozzáadása a replikált Hyper-V virtuális gépen | Nem támogatott. Tiltsa le a replikációt, végezze el a módosítást, majd engedélyezze újra a virtuális gép replikálását.

## <a name="hyper-v-network-configuration"></a>Hyper-V hálózati konfiguráció

**Összetevő** | **Hyper-V Virtual Machine Manager** | **Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
Gazdagép hálózata: hálózati ADAPTERek összevonása | Igen | Yes
Gazdagép hálózata: VLAN | Igen | Yes
Gazdagép hálózata: IPv4 | Igen | Yes
Gazdagép hálózata: IPv6 | Nem | Nem
Vendég virtuálisgép-hálózat: hálózati adapterek összevonása | Nem | Nem
Vendég virtuálisgép-hálózat: IPv4 | Igen | Yes
Vendég virtuálisgép-hálózat: IPv6 | Nem | Igen
Vendég virtuálisgép-hálózat: statikus IP-cím (Windows) | Igen | Yes
Vendég virtuálisgép-hálózat: statikus IP-cím (Linux) | Nem | Nem
Vendég VM-hálózat: több hálózati adapter | Igen | Yes
Https-proxy | Nem | Nem
Site Recovery szolgáltatáshoz való privát hivatkozás | Igen. [További információ](hybrid-how-to-enable-replication-private-endpoints.md). | Igen. [További információ](hybrid-how-to-enable-replication-private-endpoints.md).




## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM hálózati konfiguráció (feladatátvétel után)

**Összetevő** | **Hyper-V Virtual Machine Manager** | **Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
Azure ExpressRoute | Igen | Yes
ILB | Igen | Yes
ELB | Igen | Yes
Azure Traffic Manager | Igen | Yes
Több hálózati adapter | Igen | Yes
Fenntartott IP | Igen | Yes
IPv4 | Igen | Yes
Forrás IP-címének megőrzése | Igen | Yes
Azure Virtual Network szolgáltatási végpontok<br/> (Azure Storage-tűzfalak nélkül) | Igen | Yes
Gyorsított hálózatkezelés | Nem | Nem


## <a name="hyper-v-host-storage"></a>Hyper-V gazdagép tárolója

**Storage** | **Hyper-V Virtual Machine Manager** | **Hyper-V Virtual Machine Manager nélkül**
--- | --- | --- 
NFS | NA | NA
SMB 3.0 | Igen | Yes
SAN (ISCSI) | Igen | Yes
Több elérési út (MPIO). Tesztelve:<br></br> Microsoft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM for CLARiiON | Igen | Yes

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V virtuális gép Guest Storage

**Storage** | **Hyper-V Virtual Machine Manager** | **Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
VMDK | NA | NA
VHD/VHDX | Igen | Yes
2. generációs VM | Igen | Yes
EFI/UEFI<br></br>Az Azure-ban áttelepített virtuális gép automatikusan BIOS rendszerindító virtuális gépre lesz konvertálva. A virtuális gépnek csak a Windows Server 2012-es vagy újabb verziójának kell futnia. Az operációsrendszer-lemez legfeljebb öt partíciót tartalmazhat, és az operációsrendszer-lemez mérete nem haladhatja meg a 300 GB-ot.| Igen | Yes
Megosztott fürtözött lemez | Nem | Nem
Titkosított lemez | Nem | Nem
NFS | NA | NA
SMB 3.0 | Nem | Nem
RDM | NA | NA
Lemez >1 TB | Igen, akár 4 095 GB-ig | Igen, akár 4 095 GB-ig
Lemez: 4K logikai és fizikai szektor | Nem támogatott: 1. generációs/2. gen | Nem támogatott: 1. generációs/2. gen
Lemez: 4K logikai és 512 – bájtos fizikai szektor | Igen |  Yes
Logikai kötet kezelése (LVM). Az LVM csak adatlemezeken támogatott. Az Azure csak egyetlen operációsrendszer-lemezt biztosít. | Igen | Yes
Kötet szalagos lemezzel >1 TB | Igen | Yes
Tárolóhelyek | Nem | Nem
Lemez gyors hozzáadása/eltávolítása | Nem | Nem
Lemez kizárása | Igen | Yes
Több elérési út (MPIO) | Igen | Yes

## <a name="azure-storage"></a>Azure Storage

**Összetevő** | **Hyper-V Virtual Machine Manager** | **Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
Helyileg redundáns tárolás | Igen | Yes
Georedundáns tárolás | Igen | Yes
Írásvédett georedundáns tárolás (RA-GRS) | Igen | Yes
Zónaredundáns tárolás | Nem | Nem
Ritkán használt tároló | Nem | Nem
Gyors tárolás| Nem | Nem
Blokkblobok | Nem | Nem
Titkosítás nyugalmi állapotban (SSE)| Igen | Yes
Inaktív titkosítás (CMK) <br></br> (Csak a felügyelt lemezekre történő feladatátvétel esetén)| Igen (a PowerShell az 3.3.0 modultól kezdődően) | Igen (a PowerShell az 3.3.0 modultól kezdődően)
Dupla titkosítás a nyugalmi állapotban <br></br> (Csak a felügyelt lemezekre történő feladatátvétel esetén) <br></br> További információ a [Windows](../virtual-machines/disk-encryption.md) és a [Linux](../virtual-machines/disk-encryption.md) támogatott régióiról | Igen (a PowerShell az 3.3.0 modultól kezdődően) | Igen (a PowerShell az 3.3.0 modultól kezdődően)
Prémium szintű Storage | Igen | Yes
Standard szintű Storage | Igen | Yes
Importálási/exportálási szolgáltatás | Nem | Nem
Azure Storage-fiókok engedélyezve tűzfallal | Igen. A cél tárolásához és a gyorsítótárhoz. | Igen. A cél tárolásához és a gyorsítótárhoz.
Storage-fiók módosítása | Nem. A cél Azure Storage-fiók nem módosítható a replikáció engedélyezése után. A vész-helyreállítási szolgáltatás módosításához tiltsa le, majd engedélyezze újra. | No
Biztonságos átvitel lehetőség | Igen | Yes


## <a name="azure-compute-features"></a>Az Azure számítási funkciói

**Szolgáltatás** | **Hyper-V Virtual Machine Manager** | **Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
Rendelkezésre állási csoportok | Igen | Yes
HUB | Igen | Yes  
Felügyelt lemezek | Igen, a feladatátvételhez.<br/><br/> A felügyelt lemezek feladat-visszavétele nem támogatott. | Igen, a feladatátvételhez.<br/><br/> A felügyelt lemezek feladat-visszavétele nem támogatott.

## <a name="azure-vm-requirements"></a>Azure virtuálisgép-követelmények

Az Azure-ba replikált helyszíni virtuális gépeknek meg kell felelniük az ebben a táblázatban összefoglalt Azure-beli virtuálisgép-követelményeknek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | A Site Recovery az [Azure által támogatott](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10))összes operációs rendszert támogatja.  | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 32 bites (Windows Server 2008)/64-bit | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Operációsrendszer-lemez mérete | Akár 2 048 GB az 1. generációs virtuális gépekhez.<br/><br/> Akár 300 GB a 2. generációs virtuális gépek számára.  | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Adatlemezek száma | 16 vagy kevesebb  | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Adatlemez virtuális merevlemezének mérete | Legfeljebb 4 095 GB | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter támogatott |
Megosztott VHD | Nem támogatott | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Merevlemez formátuma | VHD <br/><br/> VHDX | A Site Recovery automatikusan átalakítja a VHDX a VHD-re, amikor feladatátvételt hajt végre az Azure-ba. Ha a feladatátvételt a helyszíni rendszerre végzi, a virtuális gépek továbbra is a VHDX formátumot használják.
BitLocker | Nem támogatott | A virtuális gép replikálásának engedélyezése előtt le kell tiltani a BitLockert.
a virtuális gép neve | 1 és 63 karakter között. Csak betűket, számokat és kötőjelet tartalmazhat. A virtuális gép nevének betűvel vagy számmal kell kezdődnie és végződnie. | Frissítse az értéket a virtuális gép tulajdonságainál Site Recoveryban.
Virtuális gép típusa | 1. generációs<br/><br/> 2. generációs – Windows | A 2. generációs virtuális gépek alapszintű operációsrendszer-lemezzel (amely egy vagy két VHDX formázott adatkötetet tartalmaz) és kevesebb mint 300 GB lemezterületet támogat.<br></br>A Linux 2. generációs virtuális gépek nem támogatottak. [További információ](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Recovery Services tároló műveletei

**Művelet** |  **Hyper-V és VMM** | **Hyper-V VMM nélkül**
--- | --- | ---
Tár áthelyezése az erőforráscsoportok között<br/><br/> Előfizetéseken belül és között | Nem | Nem
Tárterület, hálózat, Azure-beli virtuális gépek áthelyezése az erőforráscsoportok között<br/><br/> Előfizetéseken belül és között | Nem | Nem

> [!NOTE]
> Amikor a helyszíni rendszerről az Azure-ba replikálja Hyper-VMsokat, replikálhat csak egy AD-bérlőre egy adott környezetből – a Hyper-V-helyről vagy a Hyper-V-t a VMM megfelelő módon.


## <a name="provider-and-agent"></a>Provider és Agent

Annak ellenőrzéséhez, hogy a telepítés kompatibilis-e a jelen cikkben található beállításokkal, győződjön meg arról, hogy a legújabb szolgáltatói és ügynök-verziókat futtatja.

**Név** | **Leírás** | **Részletek**
--- | --- | --- 
Azure Site Recovery szolgáltató | A helyszíni kiszolgálók és az Azure közötti kommunikáció koordinálása <br/><br/> Hyper-V Virtual Machine Managerkal: telepítve Virtual Machine Manager kiszolgálókon<br/><br/> Hyper-V Virtual Machine Manager nélkül: telepítve a Hyper-V gazdagépeken| Legújabb verzió: 5.1.2700.1 (elérhető a Azure Portal)<br/><br/> [Legújabb funkciók és javítások](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Recovery Services-ügynök | A Hyper-V virtuális gépek és az Azure közötti replikáció koordinálása<br/><br/> Helyszíni Hyper-V-kiszolgálókra telepítve (Virtual Machine Manager) vagy anélkül | A portálon elérhető legújabb ügynök






## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan [készítheti elő az Azure](tutorial-prepare-azure.md) -t a helyszíni Hyper-V virtuális gépek vész-helyreállításához.