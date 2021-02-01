---
title: Az Azure-beli virtuális gépek másik régióba való áthelyezésének támogatása az Azure-beli erőforrás-mozgató használatával
description: Tekintse át az Azure-beli virtuális gépek régiók közötti áthelyezésének támogatását az Azure erőforrás-mozgató szolgáltatással.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 10/11/2020
ms.author: raynew
ms.openlocfilehash: fa0017b0633d856906609818dd56b5971b1879a7
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222798"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>Azure-beli virtuális gépek Azure-régiók közötti áthelyezésének támogatása

Ez a cikk a virtuális gépek és a kapcsolódó hálózati erőforrások Azure-régiókban való áthelyezésével kapcsolatos támogatást és előfeltételeket foglalja össze az erőforrás-mozgató használatával.

> [!IMPORTANT]
> Az Azure-beli erőforrás-mozgató szolgáltatás jelenleg előzetes verzióban érhető el.


## <a name="windows-vm-support"></a>Windows rendszerű virtuális gépek támogatása

Az erőforrás-mozgató támogatja az ezeket a Windows operációs rendszereket futtató Azure-beli virtuális gépeket.

**Operációs rendszer** | **Részletek**
--- | ---
Windows Server 2019 | A Server Core és a Server asztali felhasználói felülettel támogatott.
Windows Server 2016  | Támogatott Server Core, kiszolgáló asztali felülettel.
Windows Server 2012 R2 | Támogatott.
Windows Server 2012 | Támogatott.
Windows Server 2008 R2 SP1/SP2 | Támogatott.<br/><br/> A SP1/SP2 szervizcsomaggal rendelkező Windows Server 2008 R2 rendszerű gépeken telepítenie kell egy Windows [karbantartási verem frissítését (SSU)](https://support.microsoft.com/help/4490628) és az [SHA-2 frissítést](https://support.microsoft.com/help/4474419).  Az SHA-1 nem támogatott szeptember 2019-tól, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynök bővítmény nem a várt módon fog települni/frissíteni. További információ az [SHA-2 frissítésről és a követelményekről](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Támogatott.
Windows 8,1 (x64) | Támogatott.
Windows 8 (x64) | Támogatott.
Windows 7 (x64) SP1-től | Telepítsen Windows- [karbantartási verem-frissítést (SSU)](https://support.microsoft.com/help/4490628) és [SHA-2 frissítést](https://support.microsoft.com/help/4474419) a Windows 7 SP1 rendszert futtató gépeken.  Az SHA-1 nem támogatott szeptember 2019-tól, és ha az SHA-2 kód aláírása nincs engedélyezve, az "előkészítés" lépés nem fog sikerülni. További információ az [SHA-2 frissítésről és a követelményekről](https://aka.ms/SHA-2KB).


## <a name="linux-vm-support"></a>Linux rendszerű virtuális gépek támogatása

Az erőforrás-áthelyezés támogatja az ezeket a Linux operációs rendszereket futtató Azure-beli virtuális gépeket.

**Operációs rendszer** | **Részletek**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, 8,0, 8,1
Ubuntu 14,04 LTS-kiszolgáló | [Támogatott kernel-verziók](#supported-ubuntu-kernel-versions)
Ubuntu 16,04 LTS-kiszolgáló | [Támogatott kernel-verzió](#supported-ubuntu-kernel-versions)<br/><br/> A jelszó-alapú hitelesítést és a bejelentkezést használó Ubuntu-kiszolgálók, valamint a felhőalapú virtuális gépek konfigurálására szolgáló Cloud-init csomag esetében előfordulhat, hogy a jelszó-alapú bejelentkezés le van tiltva a feladatátvételen (a Cloud-init konfigurációtól függően). A jelszó-alapú bejelentkezés a virtuális gépen engedélyezhető, ha alaphelyzetbe állítja a jelszót a támogatási > hibaelhárítási > beállítások menüjében (a Azure Portal a feladatátvételen átadott virtuális gépen).
Ubuntu 18,04 LTS-kiszolgáló | A [kernel támogatott verziója](#supported-ubuntu-kernel-versions).
Debian 7 | [Támogatott kernel-verziók](#supported-debian-kernel-versions).
Debian 8 | [Támogatott kernel-verziók](#supported-debian-kernel-versions).
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [Támogatott kernel-verziók](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 és 15 SP1. [(Támogatott kernel-verziók)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | SP3
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> A Red hat-kompatibilis kernel vagy a nem törhető vállalati kernel kiadásának futtatása 3, 4 & 5 (UEK3, UEK4, UEK5)


### <a name="supported-ubuntu-kernel-versions"></a>Támogatott Ubuntu kernel-verziók

**Kiadás** | **Kernel verziója** 
--- | --- 
14,04 LTS |  3.13.0-24 – általános – 3.13.0 – 170 – általános,<br/>3.16.0-25 – általános – 3.16.0-77 – általános,<br/>3.19.0-18 – általános – 3.19.0 – 80 – általános,<br/>4.2.0-18 – általános – 4.2.0 – 42 – általános,<br/>4.4.0-21 – általános – 4.4.0 – 148 – általános,<br/>4.15.0-1023-Azure-4.15.0-1045-Azure 
16,04 LTS |  4.4.0-21 – általános – 4.4.0 – 171 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0 – 74 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure – 4.15.0 – 1066 – Azure
18,04 LTS | 4.15.0-20 – általános – 4.15.0 – 74 – általános </br> 4.18.0-13 – általános – 4.18.0 – 25 – általános </br> 5.0.0-15 – általános – 5.0.0 – 37 – általános </br> 5.3.0-19 – általános – 5.3.0 – 24 – általános </br> 4.15.0-1009-Azure-4.15.0-1037-Azure </br> 4.18.0-1006-Azure-4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1028-Azure </br> 5.3.0-1007-Azure-5.3.0-1009-Azure


### <a name="supported-debian-kernel-versions"></a>Támogatott Debian kernel verziók 

**Kiadás** |  **Kernel verziója** 
--- |  --- 
Debian 7 |  3.2.0-4-amd64 – 3.2.0-6-amd64, 3.16.0 -0. BPO. 4 – amd64 
Debian 8 |  3.16.0-4-amd64 – 3.16.0-10-amd64, 4.9.0 -0. BPO. 4-amd64 – 4.9.0 -0. BPO. 11 – amd64 
Debian 8 |  3.16.0-4-amd64 – 3.16.0-10-amd64, 4.9.0 -0. BPO. 4-amd64 – 4.9.0 -0. BPO. 9 – amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>Támogatott SUSE Linux Enterprise Server 12 kernel-verzió 

**Kiadás** | **Kernel verziója** 
--- |  --- 
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) |  A [SUSE 12 SP1, SP2, SP3 és SP4 kernelek összes készlete](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-Azure-4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure-4.12.14-6.34-Azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>Támogatott SUSE Linux Enterprise Server 15 kernel-verzió

**Kiadás** | **Kernel verziója** |
--- |  --- |
SUSE Linux Enterprise Server 15 és 15 SP1 |  A rendszer minden SUSE 15 és 15 kernelt támogat.</br></br> 4.12.14-5.5 – Azure – 4.12.14 – 8.22 – Azure |

## <a name="supported-linux-file-systemguest-storage"></a>Támogatott Linux fájlrendszer/vendég tárterület

* Fájlrendszerek: ext3, ext4, XFS, BTRFS
* Volume Manager: LVM2
* Többutas szoftver: Device Mapper


## <a name="supported-vm-compute-settings"></a>Támogatott virtuális gépek számítási beállításai

**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Méret | Bármely Azure-beli VM-méret legalább két CPU-mag és 1 GB RAM-mal | Ellenőrizze az Azure-beli [virtuális gépek méretét](../virtual-machines/sizes-general.md).
Rendelkezésre állási csoportok | Támogatott | Támogatott.
Rendelkezésreállási zónák | Támogatott | Támogatott, a célcsoportok támogatásának függvényében.
Azure Gallery-lemezképek (közzétette: Microsoft) | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Azure Gallery-lemezképek (harmadik fél által közzétett)  | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Egyéni lemezképek (harmadik fél által közzétett)| Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Site Recovery használó virtuális gépek | Nem támogatott | Az erőforrásokat a különböző régiók között helyezheti át a virtuális gépek között, a háttérbeli Site Recovery használatával. Ha már használja a Site Recovery, tiltsa le a replikációt, majd indítsa el az előkészítési folyamatot.
Azure RBAC-szabályzatok | Nem támogatott | A virtuális gépeken futó Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) házirendek nem másolódnak át a célként megadott régióban lévő virtuális gépre.
Bővítmények | Nem támogatott | A bővítmények nem másolódnak át a célként megadott régióban lévő virtuális gépre. Az áthelyezés befejezése után manuálisan telepítse őket.


## <a name="supported-vm-storage-settings"></a>Támogatott virtuális gépek tárolási beállításai

Ez a táblázat az Azure VM operációsrendszer-lemez, az adatlemez és az ideiglenes lemez támogatását foglalja össze. Fontos, hogy megfigyelje a virtuálisgép-lemezek korlátait és a [felügyelt lemezekre](../virtual-machines/disks-scalability-targets.md) vonatkozó célokat, hogy elkerülje a teljesítménnyel kapcsolatos problémákat.

> [!NOTE]
> A cél virtuális gép méretének egyenlőnek vagy annál nagyobbnak kell lennie a forrás virtuális géppel. Az érvényesítéshez használt paraméterek a következők: adatlemezek száma, hálózati adapterek száma, rendelkezésre álló processzorok, memória GB-ban. Ha nem ad meg hibát, a rendszer nem adja ki.


**Összetevő** | **Támogatás** | **Részletek**
--- | --- | ---
OPERÁCIÓSRENDSZER-lemez maximális mérete | 2048 GB | [További](../virtual-machines/managed-disks-overview.md) információ a VM-lemezekről.
Ideiglenes lemez | Nem támogatott | Az ideiglenes lemez mindig ki van zárva az előkészítési folyamatból.<br/><br/> Ne tároljon állandó adatmennyiséget az ideiglenes lemezen. [További információ](../virtual-machines/managed-disks-overview.md#temporary-disk).
Adatlemez maximális mérete | 8192 GB felügyelt lemezekhez
Adatlemez minimális mérete |  2 GB a felügyelt lemezekhez |
Adatlemez maximális száma | Akár 64-ig, egy adott Azure-beli virtuálisgép-méret támogatásával összhangban | [További](../virtual-machines/sizes.md) információ a virtuális gépek méreteiről.
Adatlemez változási aránya | A Premium Storage esetében legfeljebb 10 MB/s lemezterület. A standard szintű tároláshoz legfeljebb 2 MB/s lemez adható meg. | Ha a lemez átlagos adatváltozási sebessége folyamatosan meghaladja a maximális értéket, az előkészítés nem fog megjelenni.<br/><br/>  Ha azonban a maximális értéket szórványosan túllépik, az előkészítés felmerülhet, de előfordulhat, hogy némileg késleltetett helyreállítási pontok jelenhetnek meg.
Adatlemez (standard Storage-fiók) | Nem támogatott. | Módosítsa a tárolási típust a felügyelt lemezre, majd próbálja meg áthelyezni a virtuális gépet.
Adatlemez (prémium szintű Storage-fiók) | Nem támogatott | Módosítsa a tárolási típust a felügyelt lemezre, majd próbálja meg áthelyezni a virtuális gépet.
Felügyelt lemez (standard) | Támogatott  |
Felügyelt lemez (prémium) | Támogatott |
Standard SSD | Támogatott |
2. generációs (UEFI rendszerindítási) | Támogatott
Rendszerindítási diagnosztika Storage-fiókja | Nem támogatott | A virtuális gép a célként megadott régióba való áthelyezése után engedélyezze újra.

### <a name="limits-and-data-change-rates"></a>Korlátok és adatváltozások aránya

A következő táblázat összefoglalja a tesztek alapján megjelenő korlátokat. Ezek nem fedik le az alkalmazások minden lehetséges I/O-kombinációját. A tényleges eredmények az alkalmazás I/O-kombinációja alapján változnak. Két korlátozást kell figyelembe venni, a lemezes adatváltozások és a virtuálisgép-adatforgalom esetében.

**Tárolási cél** | **Átlagos forrás lemez I/O** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |    Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |20 MB/s | 1684 GB/lemez

## <a name="supported-vm-networking-settings"></a>Támogatott virtuális gépek hálózati beállításai

**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Hálózati adapter | Támogatott | Válasszon egy meglévő erőforrást a célként megadott régióban, vagy hozzon létre egy új erőforrást az előkészítési folyamat során. 
Belső terheléselosztó | Támogatott | Válasszon egy meglévő erőforrást a célként megadott régióban, vagy hozzon létre egy új erőforrást az előkészítési folyamat során.  
Nyilvános Load Balancer | Támogatott | Válasszon egy meglévő erőforrást a célként megadott régióban, vagy hozzon létre egy új erőforrást az előkészítési folyamat során.  
Nyilvános IP-cím | Támogatott | Válasszon egy meglévő erőforrást a célként megadott régióban, vagy hozzon létre egy új erőforrást az előkészítési folyamat során.<br/><br/> A nyilvános IP-cím a régióra jellemző, és az áthelyezés után nem lesz megtartva a célként megadott régióban. Tartsa szem előtt, ha módosítja a hálózati beállításokat (beleértve a terheléselosztási szabályokat is) a célhelyen.
Hálózati biztonsági csoporttal | Támogatott | Válasszon egy meglévő erőforrást a célként megadott régióban, vagy hozzon létre egy új erőforrást az előkészítési folyamat során.  
Fenntartott (statikus) IP-cím | Támogatott | Jelenleg nem konfigurálható. Az érték alapértelmezés szerint a forrás értékét. <br/><br/> Ha a forrás virtuális gépen lévő hálózati adapter statikus IP-címmel rendelkezik, és a célként megadott alhálózatnak ugyanaz az IP-címe, akkor a cél virtuális géphez van rendelve.<br/><br/> Ha a célként megadott alhálózat nem rendelkezik ugyanazzal az IP-címmel, akkor a virtuális gép kezdeményezésének áthelyezése sikertelen lesz.
Dinamikus IP-cím | Támogatott | Jelenleg nem konfigurálható. Az érték alapértelmezés szerint a forrás értékét.<br/><br/> Ha a forrás hálózati adaptere dinamikus IP-címmel rendelkezik, a célként megadott virtuális gép hálózati adaptere alapértelmezés szerint is dinamikus.
IP-konfigurációk | Támogatott | Jelenleg nem konfigurálható. Az érték alapértelmezés szerint a forrás értékét.

## <a name="outbound-access-requirements"></a>Kimenő hozzáférési követelmények

Az áthelyezni kívánt Azure-beli virtuális gépeknek kimenő hozzáférésre van szükségük.


### <a name="url-access"></a>URL-hozzáférés

 Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, engedélyezze az alábbi URL-címek elérését:

**Név** | **Azure nyilvános felhő** | **Részletek** 
--- | --- | --- 
Tárolás | `*.blob.core.windows.net`  | Lehetővé teszi az adatok írását a virtuális gépről a forrásrégió gyorsítótárjának tárfiókjába. 
Azure Active Directory | `login.microsoftonline.com`  | Hitelesítést és engedélyezést biztosít a Site Recovery szolgáltatás URL-címeihez. 
Replikáció | `*.hypervrecoverymanager.windowsazure.com` | Lehetővé teszi a virtuális gép és a Site Recovery szolgáltatás közötti kommunikációt. 
Service Bus | `*.servicebus.windows.net` | Lehetővé teszi a virtuális gép számára a Site Recovery monitorozási és diagnosztikai adatainak írását. 

## <a name="nsg-rules"></a>NSG-szabályok
Ha hálózati biztonsági csoport (NSG) szabályokat használ a kimenő kapcsolatok vezérléséhez, hozza létre ezeket a [szolgáltatási címke](../virtual-network/service-tags-overview.md) szabályait. Minden szabálynak engedélyeznie kell a kimenő hozzáférést a HTTPS-en (443).
- Hozzon létre egy tárolási címke szabályt a forrás régióhoz.
- Hozzon létre egy *AzureSiteRecovery* -címkét, amely lehetővé teszi a site Recovery szolgáltatás elérését bármely régióban. Ez a címke függőségekkel rendelkezik a többi címkével kapcsolatban, ezért szabályokat kell létrehoznia a következőkhöz:
    - *AzureActiveDirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- Javasoljuk, hogy tesztelje a szabályokat nem éles környezetben. [Tekintse át a példákat](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags). 


## <a name="next-steps"></a>Következő lépések

Próbáljon [egy Azure-beli virtuális gépet](tutorial-move-region-virtual-machines.md) egy másik régióba helyezni az erőforrás-mozgató használatával.
