---
title: Támogatás mátrix – Hyper-V vész-helyreállítás egy másodlagos VMM-helyre Azure Site Recovery
description: Összefoglalja a Hyper-V virtuális gépek replikációjának támogatását a VMM-felhőkben egy másodlagos helyre Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: af7baf413c9054ef3e5bf527851ac06c113cdce7
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86131174"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Támogatási mátrix Hyper-V virtuális gépek másodlagos helyre történő vészhelyreállításához

Ez a cikk összefoglalja, hogy mi támogatott, ha a [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával replikálja a System Center Virtual Machine Manager (VMM) felhőkben felügyelt Hyper-V virtuális gépeket egy másodlagos helyre. Ha a Hyper-V virtuális gépeket az Azure-ba szeretné replikálni, tekintse át [ezt a támogatási mátrixot](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Csak másodlagos helyre replikálhat, ha a Hyper-V-gazdagépek kezelése a VMM-felhőkben történik.


## <a name="host-servers"></a>Gazdagép-kiszolgálók

**Operációs rendszer** | **Részletek**
--- | ---
Windows Server 2012 R2 | A kiszolgálóknak a legújabb frissítéseket kell futtatniuk.
Windows Server 2016 |  A VMM 2016-es felhők a Windows Server 2016 és a 2012 R2 operációs rendszert futtató gazdagépek jelenleg nem támogatottak.<br/><br/> A System Center 2012 R2 VMM 2012 R2 rendszerről a System Center 2016-re frissített központi telepítések jelenleg nem támogatottak.


## <a name="replicated-vm-support"></a>Replikált virtuális gépek támogatása

A következő táblázat összefoglalja az operációs rendszerek támogatását Site Recovery-mel replikált gépekhez. Bármilyen munkaterhelés futtatható a támogatott operációs rendszeren.

**Windows-verzió** | **Hyper-V (VMM)**
--- | ---
Windows Server 2016 | A [Hyper-V által támogatott](/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) bármely vendég operációs rendszer Windows Server 2016 rendszeren 
Windows Server 2012 R2 | A [Hyper-V által támogatott](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) bármely vendég operációs rendszer Windows Server 2012 R2 rendszeren

## <a name="linux-machine-storage"></a>Linux rendszerű gép tárterülete

Csak a következő tárterülettel rendelkező Linux rendszerű gépek replikálása lehetséges:

- Fájlrendszer (EXT3, ETX4, ReiserFS, XFS).
- Többutas szoftver – Device mapper.
- A Volume Manager (LVM2).
- A HP CCISS-vezérlőt tároló fizikai kiszolgálók nem támogatottak.
- A ReiserFS fájlrendszer csak SUSE Linux Enterprise Server 11 SP3 rendszeren támogatott.

## <a name="network-configuration---hostguest-vm"></a>Hálózati konfiguráció – gazdagép/vendég virtuális gép

**Konfigurálás** | **Támogatott**  
--- | --- 
Gazdagép – hálózati adapterek összevonása | Igen 
Gazdagép – VLAN | Igen 
Gazdagép – IPv4 | Igen 
Gazdagép – IPv6 | Nem 
Vendég virtuális hálózati adapterek összevonása | Nem
Vendég virtuális gép – IPv4 | Igen
Vendég virtuális gép – IPv6 | Nem
Vendég virtuális gép – Windows/Linux – statikus IP-cím | Igen
Vendég virtuális gép – több hálózati adapter | Igen


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Gazdagép tárterülete

**Storage (gazdagép)** | **Támogatott**
--- | --- 
NFS | N.A.
SMB 3.0 |  Igen
SAN (ISCSI) | Igen
Több elérési út (MPIO) | Igen

### <a name="guest-or-physical-server-storage"></a>Vendég vagy fizikai kiszolgáló tárterülete

**Konfigurálás** | **Támogatott**
--- | --- | 
VMDK |  N.A.
VHD/VHDX | Igen (legfeljebb 16 lemez)
2. generációs VM | Igen
Megosztott fürtözött lemez | Nem
Titkosított lemez | Nem
UEFI| N.A.
NFS | Nem
SMB 3.0 | Nem
RDM | N.A.
Lemez > 1 TB | Igen
Kötet szalagos lemezzel > 1 TB<br/><br/> LVM | Igen
Tárolóhelyek | Igen
Lemez gyors hozzáadása/eltávolítása | Nem
Lemez kizárása | Igen
Több elérési út (MPIO) | Igen

## <a name="vaults"></a>Tárolók

**Művelet** | **Támogatott**
--- | --- 
Tárolók áthelyezése az erőforráscsoportok között (vagy az előfizetések között) |  Nem
Tárterület, hálózat, Azure-beli virtuális gépek áthelyezése az erőforráscsoportok között (vagy az előfizetések között) | Nem

## <a name="azure-site-recovery-provider"></a>Azure Site Recovery szolgáltató

A szolgáltató koordinálja a VMM-kiszolgálók közötti kommunikációt. 

**Legújabb** | **Frissítések**
--- | --- 
5.1.19 ([elérhető a portálról](https://aka.ms/downloaddra) | [Legújabb funkciók és javítások](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Következő lépések

[VMM-felhőkben lévő Hyper-V virtuális gépek replikálása másodlagos helyre](./hyper-v-vmm-disaster-recovery.md)
