---
title: A fizikai kiszolgálók áttelepítésének támogatása a Azure Migrateban
description: Ismerkedjen meg a fizikai kiszolgálók áttelepítésének támogatásával Azure Migrateban.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 4ebc363f29ed8956d10e91f41be1d257cbcc492f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98703886"
---
# <a name="support-matrix-for-physical-server-migration"></a>A fizikai kiszolgáló áttelepítésének támogatási mátrixa

Ez a cikk a fizikai kiszolgálók Azure-ba történő áttelepítésére vonatkozó támogatási beállításokat és korlátozásokat foglalja össze [Azure Migrate: a kiszolgáló áttelepítése](migrate-services-overview.md#azure-migrate-server-migration-tool) . Ha a fizikai kiszolgálók Azure-ba való áttelepítésére vonatkozó információkat keres, tekintse át az [értékelés támogatási mátrixát](migrate-support-matrix-physical.md).

## <a name="migrating-machines-as-physical"></a>Gépek áttelepítése fizikaiként

A helyszíni gépeket fizikai kiszolgálóként is áttelepítheti az ügynök-alapú replikáció használatával. Az eszköz használatával számos gépet telepíthet át az Azure-ba:

- Helyszíni fizikai kiszolgálók.
- A platformokkal virtualizált virtuális gépek, például Xen, KVM.
- Hyper-V virtuális gépek vagy VMware virtuális gépek, ha valamilyen okból kifolyólag nem kívánja használni a szabványos [Hyper-v-](tutorial-migrate-hyper-v.md) vagy [VMware](server-migrate-overview.md) -folyamatokat.
- Privát felhőben futó virtuális gépek.
- Nyilvános felhőben futó virtuális gépek, beleértve a Amazon Web Services (AWS) vagy a Google Cloud Platform (GCP) szolgáltatást.


## <a name="migration-limitations"></a>A migrálásra vonatkozó korlátozások

Egyszerre legfeljebb 10 gépet választhat a replikáláshoz. Ha több gépet szeretne áttelepíteni, a replikálást a 10 csoportba.


## <a name="physical-server-requirements"></a>Fizikai kiszolgáló követelmények

A tábla az áttelepíteni kívánt fizikai kiszolgálók támogatását összegzi az ügynök-alapú áttelepítés használatával.

**Támogatás** | **Részletek**
--- | ---
**Gépi munkaterhelés** | A Azure Migrate támogatja az olyan munkaterhelések áttelepítését (például Active Directory, SQL Server stb.), amelyek egy támogatott gépen futnak.
**Operációs rendszerek** | A legfrissebb információkért tekintse át Site Recovery [operációs rendszer támogatását](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) . Azure Migrate az operációs rendszer azonos támogatását biztosítja.
**Linux fájlrendszer/vendég tárterület** | A legfrissebb információkért tekintse át a Site Recovery [Linux-fájlrendszer támogatását](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) . A Azure Migrate azonos Linux fájlrendszer-támogatást biztosít.
**Hálózat/tárterület** | A legfrissebb információkért tekintse át a Site Recovery [hálózati](../site-recovery/vmware-physical-azure-support-matrix.md#network) és [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#storage) előfeltételeit. Azure Migrate azonos hálózati/tárolási követelményeket biztosít.
**Azure-követelmények** | A legfrissebb információkért tekintse át az [Azure hálózati](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)és [számítási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) követelményeit site Recovery. Azure Migrate a fizikai kiszolgálók áttelepítésére vonatkozó követelményekkel rendelkezik.
**Mobilitási szolgáltatás** | Minden áttelepíteni kívánt gépre telepíteni kell a mobilitási szolgáltatás ügynökét.
**UEFI-rendszerindítás** | Támogatott. Az UEFI-alapú gépek migrálása az Azure 2. generációs virtuális gépekre történik.  <br/><br/> Az operációsrendszer-lemez legfeljebb négy partíciót tartalmazhat, és a köteteket NTFS fájlrendszerrel kell formázni.
**UEFI – biztonságos rendszerindítás**         | Migrálás esetén nem támogatott.
**Céllemez** | A gépeket csak felügyelt lemezekre lehet áttelepíteni (standard HDD, standard SSD, prémium SSD) az Azure-ban.
**Lemez mérete** | 2 TB operációsrendszer-lemez; 32 TB adatlemezek esetében.
**Lemezterület-korlátok** |  Legfeljebb 63 lemez/gép.
**Titkosított lemezek/kötetek** |  A titkosított lemezekkel/kötetekkel rendelkező gépek migrálása nem támogatott.
**Megosztott lemezes fürt** | Nem támogatott.
**Független lemezek** | Támogatott.
**Továbbító lemezek** | Támogatott.
**NFS** | A gépek kötetként csatlakoztatott NFS-kötetei nem lesznek replikálva.
**iSCSI-tárolók** | Az iSCSI-tárolókkal rendelkező gépek nem támogatottak az ügynök nélküli Migrálás esetén.
**Többutas IO** | Nem támogatott.
**Összevont hálózati adapterek** | Nem támogatott.
**IPv6** | Nem támogatott.



## <a name="replication-appliance-requirements"></a>Replikációs berendezés követelményei

Ha a replikációs készüléket manuálisan állítja be egy fizikai kiszolgálón, akkor győződjön meg arról, hogy az megfelel a táblázatban összefoglalt követelményeknek. Ha a Azure Migrate replikációs berendezést VMware virtuális gépként állítja be a Azure Migrate hub-ban megadott petesejtek sablonnal, a készülék a Windows Server 2016 rendszerrel van beállítva, és megfelel a támogatási követelményeknek. 

- További információ a [replikációs berendezések követelményeiről](migrate-replication-appliance.md#appliance-requirements).
- A készülékre telepíteni kell a MySQL-t. Tudnivalók a [telepítési lehetőségekről](migrate-replication-appliance.md#mysql-installation).
- További információ a replikációs berendezés eléréséhez szükséges [URL-címekről](migrate-replication-appliance.md#url-access) .

## <a name="azure-vm-requirements"></a>Azure virtuálisgép-követelmények

Az Azure-ba replikált összes helyszíni virtuális gépnek meg kell felelnie az ebben a táblázatban összefoglalt Azure-beli virtuálisgép-követelményeknek. Ha Site Recovery futtatja az előfeltétel-ellenőrzés replikálását, akkor az ellenőrzés sikertelen lesz, ha egyes követelmények nem teljesülnek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | Ellenőrzi a támogatott operációs rendszereket.<br/> A támogatott operációs rendszereken futó munkaterhelések áttelepíthetők. | Az ellenőrzés sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 64 bites. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemez mérete | Akár 2 048 GB-ig. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemezek száma | 64 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemez mérete | Akár 32 TB | Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter is támogatott. |
Megosztott VHD | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A számítógép replikálásának engedélyezése előtt le kell tiltani a BitLockert.
a virtuális gép neve | 1 – 63 karakter.<br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevének betűvel vagy számmal kell kezdődnie és végződnie. |  Frissítse az értéket a Site Recovery számítógép tulajdonságai között.
Kapcsolat az áttelepítés után – Windows | Kapcsolódás a Windows rendszerű Azure-beli virtuális gépekhez a Migrálás után:<br/> – Az áttelepítés előtt engedélyezi az RDP-t a helyszíni virtuális gépen. Ellenőrizze, hogy a **Nyilvános** profilnál felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows-tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.<br/> A helyek közötti VPN-hozzáféréshez engedélyezze az RDP-t, és engedélyezze az RDP használatát a **Windows tűzfal**  ->  **engedélyezett alkalmazásaiban és szolgáltatásaiban** a **tartomány és a magánhálózatok** számára. Továbbá győződjön meg arról, hogy az operációs rendszer SAN-szabályzata **OnlineAll** értékre van állítva. [További információ](prepare-for-migration.md). |
Kapcsolat Migrálás után – Linux | Kapcsolódás az Azure-beli virtuális gépekhez az SSH használatával történő áttelepítés után:<br/> Az áttelepítés előtt a helyszíni gépen győződjön meg arról, hogy a Secure Shell szolgáltatás indításra van beállítva, és hogy a tűzfalszabályok engedélyezik az SSH-kapcsolatokat.<br/> A feladatátvételt követően az Azure-beli virtuális gépen engedélyezze az SSH-porthoz való bejövő kapcsolatokat a hálózati biztonsági csoportra vonatkozó szabályokra vonatkozóan a feladatátvételen átesett virtuális gépen, valamint azt az Azure-alhálózatot, amelyhez csatlakoztatva van. Továbbá adjon hozzá egy nyilvános IP-címet a virtuális géphez. |  


## <a name="next-steps"></a>Következő lépések

Fizikai kiszolgálók [áttelepíthetők](tutorial-migrate-physical-virtual-machines.md) .
