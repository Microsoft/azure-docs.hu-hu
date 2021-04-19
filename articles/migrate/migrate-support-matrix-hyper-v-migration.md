---
title: Hyper-V áttelepítés támogatása a Azure Migrate
description: További információ a Hyper-V migrálás Azure Migrate.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 33e34e777a78e1c609d2eacdcb501c0bce1f5c9d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714919"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Támogatási mátrix a Hyper-V áttelepítéséhez

Ez a cikk összefoglalja a Hyper-V virtuális gépek áttelepítésének támogatási beállításait és korlátait a [Azure Migrate: Server Migration .](migrate-services-overview.md#azure-migrate-server-migration-tool) Ha információra van szükség a Hyper-V virtuális gépek Azure-ba való migrálásra való értékeléséről, tekintse át a felmérés [támogatási mátrixát.](migrate-support-matrix-hyper-v.md)

## <a name="migration-limitations"></a>A migrálásra vonatkozó korlátozások

Egyszerre legfeljebb 10 virtuális gép közül választhat a replikációhoz. Ha több gépet szeretne átemelni, replikálja őket 10-es csoportokban.


## <a name="hyper-v-host-requirements"></a>Hyper-V-gazdagépre vonatkozó követelmények

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Üzembe helyezés**       | A Hyper-V gazdagép lehet önálló, vagy üzembe helyezhető egy fürtben. <br/>Azure Migrate -replikációs szoftver (Hyper-V replikációszolgáltató) telepítve van a Hyper-V-gazdagépekre.|
| **Engedélyek**           | Rendszergazdai jogosultságokkal kell rendelkeznie a Hyper-V-gazdagépen. |
| **Gazda operációs rendszer** | Windows Server 2019, Windows Server 2016 vagy Windows Server 2012 R2 a legújabb frissítésekkel. Vegye figyelembe, hogy ezeknek az operációs rendszereknek a Server Core telepítése is támogatott. |
| **Egyéb szoftverkövetelmények** | .NET-keretrendszer 4.7-es vagy újabb |
| **Port elérése** |  Kimenő kapcsolatok a 443-as HTTPS-porton a virtuális gép replikációs adatainak küldése érdekében.
| **Szabad lemezterület (gyorsítótár)** |  600 GB |
| **Szabad lemezterület (adatmegőrzési lemez)** |  600 GB |


## <a name="hyper-v-vms"></a>Hyper-V virtuális gépek

| **Támogatás**                  | **Részletek**               
| :----------------------------- | :------------------- |
| **Operációs rendszer** | Az [Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) által támogatott [összes Windows](../virtual-machines/linux/endorsed-distros.md) és Linux operációs rendszer. |
**Windows Server 2003** | A Windows Server 2003 rendszert futtató virtuális gépek esetén a migrálás előtt telepítenie kell a [Hyper-V integrációs](prepare-windows-server-2003-migration.md) szolgáltatásokat. | 
**Linux rendszerű virtuális gépek az Azure-ban** | Egyes virtuális gépeken módosításokra lehet szükség, hogy futtathatók az Azure-ban.<br/><br/> Linux rendszeren Azure Migrate a módosításokat a következő operációs rendszereken automatikusan:<br/> - Red Hat Enterprise Linux 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x<br/> - Cent OS 7.7, 7.6, 7.5, 7.4, 6.x</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - SUSE Linux Enterprise Server 15 SP1 <br/>- Ubuntu 19.04, 19.10, 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8 <br/> Oracle Linux 7.7, 7.7-CI<br/> Más operációs rendszerek esetében manuálisan kell [elvégeznie a szükséges](prepare-for-migration.md#verify-required-changes-before-migrating) módosításokat.
| **Az Azure szükséges módosításai** | Egyes virtuális gépeken módosításokra lehet szükség, hogy futtathatók az Azure-ban. A migrálás előtt manuálisan kell elvégezni a módosításokat. A vonatkozó cikkek erre vonatkozó utasításokat tartalmaznak. |
| **Linux rendszerindítás**                 | Ha a /boot egy dedikált partíción található, akkor az operációsrendszer-lemezen kell lennie, és nem lehet több lemez között elosztva.<br/> Ha a /boot a gyökérpartíció (/) része, akkor a "/" partíciónak az operációsrendszer-lemezen kell lennie, és nem lehet több lemezen. |
| **UEFI-rendszerindítás**                  | Támogatott. Az UEFI-alapú virtuális gépek migrálva lesznek az Azure 2. generációs virtuális gépekre.  |
| **UEFI – Biztonságos rendszerindítás**         | A migrálás nem támogatott.|
| **Lemezméret**                  | 2 TB az operációsrendszer-lemez (BIOS-rendszerindítás), 4 TB az operációsrendszer-lemez (UEFI-rendszerindítás), 4 TB az adatlemezek számára.|
| **Lemezszám** | Virtuális gépenként legfeljebb 16 lemez.|
| **Titkosított lemezek/kötetek**    | A migrálás nem támogatott.|
| **RDM/áthaladó lemezek**      | A migrálás nem támogatott.|
| **Megosztott lemez** | A megosztott lemezeket használó virtuális gépek nem támogatottak a migráláshoz.|
| **NFS**                        | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva.|
| **Iscsi**                      | Az iSCSI-tárolókat tároló virtuális gépek nem támogatottak a migráláshoz.
| **Céllemez**                | Az Azure-beli virtuális gépekre csak felügyelt lemezekkel miminálhat. |
| **IPv6** | Nem támogatott.|
| **Hálózati adapterek összevonása** | Nem támogatott.|
| **Azure Site Recovery** | Nem replikálhat a Azure Migrate Server Migration használatával, ha a virtuális gép replikációja engedélyezve van a Azure Site Recovery.|
| **Portok** | Kimenő kapcsolatok a 443-as HTTPS-porton a virtuális gép replikációs adatainak küldése érdekében.|

### <a name="url-access-public-cloud"></a>URL-hozzáférés (nyilvános felhő)

A Hyper-V-gazdagépek replikációszolgáltató szoftverének hozzá kell férni ezekhez az URL-címekhez.

**URL-cím** | **Részletek**
--- | ---
login.microsoftonline.com | Hozzáférés-vezérlés és identitáskezelés Active Directory.
backup.windowsazure.com | Replikációs adatátvitel és koordináció.
*.hypervrecoverymanager.windowsazure.com | Replikációkezelésre használatos.
*.blob.core.windows.net | Adatok feltöltése tárfiókba. 
dc.services.visualstudio.com | Töltse fel a belső monitorozáshoz használt alkalmazásnaplókat.
time.windows.com | Ellenőrzi a rendszer és a globális idő közötti időszinkronizálást.

### <a name="url-access-azure-government"></a>URL-hozzáférés (Azure Government)

A Hyper-V-gazdagépek replikációszolgáltató szoftverének hozzá kell férni ezekhez az URL-címekhez.

**URL-cím** | **Részletek**
--- | ---
login.microsoftonline.us | Hozzáférés-vezérlés és identitáskezelés Active Directory.
backup.windowsazure.us | Replikációs adatátvitel és koordináció.
*.hypervrecoverymanager.windowsazure.us | Replikációkezelésre használatos.
*.blob.core.usgovcloudapi.net | Adatok feltöltése tárfiókba.
dc.services.visualstudio.com | Töltse fel a belső monitorozáshoz használt alkalmazásnaplókat.
time.nist.gov | Ellenőrzi a rendszer és a globális idő közötti időszinkronizálást.   

>[!Note]
>
> Ha a Projekt áttelepítése privát végpontkapcsolattal **rendelkezik,** a Hyper-V gazdagépek replikációszolgáltató szoftverének hozzá kell férni ezekhez az URL-címekhez a privát kapcsolatok támogatásához. 
> - *.blob.core.windows.com – A replikált adatokat tároló tárfiók elérése. Ez nem kötelező, és nem szükséges, ha a tárfiókhoz privát végpont van csatolva. 
> - login.windows.net hozzáférés-vezérléshez és identitáskezeléshez a Active Directory.

## <a name="azure-vm-requirements"></a>Azure virtuálisgép-követelmények

Az Azure-ba replikált összes helyszíni virtuális gépnek meg kell felelnie a táblázatban összefoglalt Azure-beli virtuális gépekre vonatkozó követelményeknek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Operációsrendszer-lemez mérete | Legfeljebb 2048 GB. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemezek száma | 16 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemez mérete | Legfeljebb 4095 GB | Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter is támogatott. |
Megosztott VHD | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A gép replikációjának engedélyezése előtt le kell tiltani a BitLockert.
a virtuális gép neve | 1 és 63 karakter között.<br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevének betűvel vagy számmal kell kezdődnie és végződnie. |  Frissítse a gép tulajdonságaiban található értéket a Site Recovery.
Csatlakozás a migrálás után – Windows | Csatlakozás Windows rendszerű Azure-beli virtuális gépekhez a migrálás után:<br/><br/> – A migrálás előtt engedélyezze az RDP-t a helyszíni virtuális gépen. Ellenőrizze, hogy a **Nyilvános** profilnál felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows-tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.<br/><br/> - A hely–hely VPN-hozzáféréshez engedélyezze az RDP-t, és engedélyezze az RDP-t a **Windows tűzfal** által engedélyezett alkalmazásokban és szolgáltatásokban a tartomány- és  ->   **magánhálózatok** számára. Emellett ellenőrizze, hogy az operációs rendszer SAN-szabályzata **OnlineAll (OnlineAll) beállításra van-e állítva.** [További információ](prepare-for-migration.md). |
Csatlakozás a migrálás után – Linux | Csatlakozás Azure-beli virtuális gépekhez a migrálás után SSH használatával:<br/><br/> – A migrálás előtt a helyszíni gépen ellenőrizze, hogy a Secure Shell szolgáltatás Indításra van-e állítva, és hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.<br/><br/> – A migrálás után az Azure-beli virtuális gépen engedélyezze a bejövő kapcsolatokat az SSH-porthoz a hálózati biztonsági csoport szabályaihoz a virtuális gépen és az Azure-alhálózaton, amelyhez csatlakozik. Emellett adjon hozzá egy nyilvános IP-címet a virtuális géphez. |  

## <a name="next-steps"></a>Következő lépések

[Hyper-V virtuális gépek áttelepítése](tutorial-migrate-hyper-v.md) migráláshoz.
