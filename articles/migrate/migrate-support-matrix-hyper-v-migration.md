---
title: A Hyper-V áttelepítésének támogatása a Azure Migrateban
description: Ismerkedjen meg a Hyper-V áttelepítésének támogatásával Azure Migrateokkal.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 90da16789344754c02d46022160db71ee261a056
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754062"
---
# <a name="support-matrix-for-hyper-v-migration"></a>A Hyper-V áttelepítésének támogatási mátrixa

Ez a cikk a Hyper-V virtuális gépek [Azure Migrate: kiszolgáló áttelepítéssel](migrate-services-overview.md#azure-migrate-server-migration-tool) történő áttelepítésére vonatkozó támogatási beállításokat és korlátozásokat foglalja össze. Ha a Hyper-V virtuális gépek Azure-ba való áttelepítésére vonatkozó információkat keres, tekintse át az [értékelés támogatási mátrixát](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>A migrálásra vonatkozó korlátozások

Egyszerre legfeljebb 10 virtuális gépet választhat a replikáláshoz. Ha több gépet szeretne áttelepíteni, a replikálást 10 csoportba kell helyezni.


## <a name="hyper-v-host-requirements"></a>A Hyper-V gazdagépre vonatkozó követelmények

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Üzembe helyezés**       | A Hyper-V-gazdagép önálló vagy fürtben is üzembe helyezhető. <br/>Azure Migrate replikációs szoftver (Hyper-V replikációs szolgáltató) telepítve van a Hyper-V-gazdagépeken.|
| **Engedélyek**           | Rendszergazdai engedélyekkel kell rendelkeznie a Hyper-V-gazdagépen. |
| **Gazda operációs rendszer** | Windows Server 2019, Windows Server 2016 vagy Windows Server 2012 R2 a legújabb frissítésekkel. Vegye figyelembe, hogy ezek az operációs rendszerek Server Core telepítése is támogatott. |
| **Egyéb szoftverekre vonatkozó követelmények** | .NET-keretrendszer 4,7-es vagy újabb verzió |
| **Port-hozzáférés** |  Kimenő kapcsolatok a 443-es HTTPS-porton a virtuális gép replikációs adatküldéséhez.
| **Szabad lemezterület (gyorsítótár)** |  600 GB |
| **Szabad lemezterület (adatmegőrzési lemez)** |  600 GB |


## <a name="hyper-v-vms"></a>Hyper-V virtuális gépek

| **Támogatás**                  | **Részletek**               
| :----------------------------- | :------------------- |
| **Operációs rendszer** | Az Azure által támogatott összes Windows-és [Linux](../virtual-machines/linux/endorsed-distros.md) - [alapú](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) operációs rendszer. |
**Windows Server 2003** | A Windows Server 2003 rendszert futtató virtuális gépek esetében a Migrálás előtt [telepítenie kell a Hyper-V integrációs szolgáltatásokat](prepare-windows-server-2003-migration.md) . | 
**Linux rendszerű virtuális gépek az Azure-ban** | Előfordulhat, hogy egyes virtuális gépek módosításokat igényelnek, hogy az Azure-ban is futtathatók legyenek.<br/><br/> A Linux rendszerben a Azure Migrate automatikusan végrehajtja a módosításokat a következő operációs rendszereken:<br/> -Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x<br/> -Cent OS 7,7, 7,6, 7,5, 7,4, 6. x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19,04, 19,10, 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8 <br/> Oracle Linux 7,7, 7,7-CI<br/> Más operációs rendszerekhez manuálisan végezze el a [szükséges módosításokat](prepare-for-migration.md#verify-required-changes-before-migrating) .
| **Az Azure szükséges módosításai** | Előfordulhat, hogy egyes virtuális gépek módosításokat igényelnek, hogy az Azure-ban is futtathatók legyenek. A módosításokat manuálisan végezze el az áttelepítés előtt. A kapcsolódó cikkek erre vonatkozó utasításokat tartalmaznak. |
| **Linux rendszerű rendszerindítás**                 | Ha a/boot dedikált partíción van, akkor az operációsrendszer-lemezen kell lennie, és nem szabad több lemezre osztania.<br/> Ha a/boot a gyökér (/) partíció része, akkor a "/" partíciónak az operációsrendszer-lemezen kell lennie, és nem szabad más lemezekre kiterjednie. |
| **UEFI-rendszerindítás**                  | Támogatott. Az UEFI-alapú virtuális gépek migrálása az Azure 2. generációs virtuális gépekre történik.  |
| **UEFI – biztonságos rendszerindítás**         | Migrálás esetén nem támogatott.|
| **Lemez mérete**                  | 2 TB az operációsrendszer-lemezhez (BIOS-rendszerindítás), 4 TB az operációsrendszer-lemezhez (UEFI-rendszerindítás), 4 TB az adatlemezekhez.|
| **Lemez száma** | Legfeljebb 16 lemez virtuális gépenként.|
| **Titkosított lemezek/kötetek**    | Migrálás esetén nem támogatott.|
| **RDM/továbbító lemezek**      | Migrálás esetén nem támogatott.|
| **Megosztott lemez** | A megosztott lemezeket használó virtuális gépek migrálása nem támogatott.|
| **NFS**                        | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva.|
| **ISCSI**                      | Az iSCSI-tárolókkal rendelkező virtuális gépek migrálása nem támogatott.
| **Céllemez**                | Csak felügyelt lemezekkel rendelkező Azure-beli virtuális gépekre lehet migrálni. |
| **IPv6** | Nem támogatott.|
| **Hálózati adapterek összevonása** | Nem támogatott.|
| **Azure Site Recovery** | Ha a virtuális gép Azure Site Recovery használatával való replikálásra engedélyezve van, nem lehet replikálni Azure Migrate-kiszolgáló áttelepítésével.|
| **Portok** | Kimenő kapcsolatok a 443-es HTTPS-porton a virtuális gép replikációs adatküldéséhez.|

### <a name="url-access-public-cloud"></a>URL-hozzáférés (nyilvános felhő)

A Hyper-V-gazdagépeken a replikációs szolgáltató szoftverének hozzá kell férnie ezekhez az URL-címekhez.

**URL-cím** | **Részletek**
--- | ---
login.microsoftonline.com | Hozzáférés-vezérlés és Identitáskezelés Active Directory használatával.
backup.windowsazure.com | Replikálási adatok átvitele és koordinálása.
*.hypervrecoverymanager.windowsazure.com | Replikációs felügyelethez használatos.
*.blob.core.windows.net | Adatok feltöltése a Storage-fiókba. 
dc.services.visualstudio.com | A belső figyeléshez használt alkalmazás-naplók feltöltése.
time.windows.com | Ellenőrzi a rendszerek és a globális idő közötti időszinkronizálást.

### <a name="url-access-azure-government"></a>URL-hozzáférés (Azure Government)

A Hyper-V-gazdagépeken a replikációs szolgáltató szoftverének hozzá kell férnie ezekhez az URL-címekhez.

**URL-cím** | **Részletek**
--- | ---
login.microsoftonline.us | Hozzáférés-vezérlés és Identitáskezelés Active Directory használatával.
backup.windowsazure.us | Replikálási adatok átvitele és koordinálása.
*. hypervrecoverymanager.windowsazure.us | Replikációs felügyelethez használatos.
*. blob.core.usgovcloudapi.net | Adatok feltöltése a Storage-fiókba.
dc.services.visualstudio.com | A belső figyeléshez használt alkalmazás-naplók feltöltése.
time.nist.gov | Ellenőrzi a rendszerek és a globális idő közötti időszinkronizálást.

## <a name="azure-vm-requirements"></a>Azure virtuálisgép-követelmények

Az Azure-ba replikált összes helyszíni virtuális gépnek meg kell felelnie az ebben a táblázatban összefoglalt Azure-beli virtuálisgép-követelményeknek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Operációsrendszer-lemez mérete | Akár 2 048 GB-ig. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemezek száma | 16 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemez mérete | Legfeljebb 4 095 GB | Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter is támogatott. |
Megosztott VHD | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A számítógép replikálásának engedélyezése előtt le kell tiltani a BitLockert.
a virtuális gép neve | 1 – 63 karakter.<br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevének betűvel vagy számmal kell kezdődnie és végződnie. |  Frissítse az értéket a Site Recovery számítógép tulajdonságai között.
Kapcsolat az áttelepítés után – Windows | Kapcsolódás a Windows rendszerű Azure-beli virtuális gépekhez a Migrálás után:<br/><br/> – Az áttelepítés előtt engedélyezze az RDP-t a helyszíni virtuális gépen. Ellenőrizze, hogy a **Nyilvános** profilnál felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows-tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.<br/><br/> -Helyek közötti VPN-hozzáférés esetén engedélyezze az RDP-t, és engedélyezze az RDP használatát a **Windows tűzfal**  ->  **engedélyezett alkalmazásaiban és szolgáltatásaiban** a **tartományok és magánhálózatok** számára. Továbbá győződjön meg arról, hogy az operációs rendszer SAN-szabályzata **OnlineAll** értékre van állítva. [További információ](prepare-for-migration.md). |
Kapcsolat Migrálás után – Linux | Kapcsolódás az Azure-beli virtuális gépekhez az SSH használatával történő áttelepítés után:<br/><br/> – Az áttelepítés előtt a helyszíni gépen győződjön meg arról, hogy a Secure Shell szolgáltatás indításra van beállítva, és hogy a tűzfalszabályok engedélyezik az SSH-kapcsolatokat.<br/><br/> – Az áttelepítés után az Azure-beli virtuális gépen engedélyezze a hálózati biztonsági csoportra vonatkozó, a feladatátvételi virtuális gépen található szabályok bejövő kapcsolatait, valamint azt az Azure-alhálózatot, amelyhez csatlakoztatva van. Továbbá adjon hozzá egy nyilvános IP-címet a virtuális géphez. |  

## <a name="next-steps"></a>Következő lépések

[Telepítse át a Hyper-V virtuális gépeket](tutorial-migrate-hyper-v.md) az áttelepítéshez.
