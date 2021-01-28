---
title: Támogatási mátrix a VMware/fizikai vész-helyreállításhoz Azure Site Recovery
description: Összefoglalja a VMware virtuális gépek és a fizikai kiszolgálók vész-helyreállításának támogatását az Azure-ban Azure Site Recovery használatával.
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: f525666e91a482a6c23128e266b4f5669437a502
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956021"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>A VMware virtuális gépek és fizikai kiszolgálók Azure-ba való vész-helyreállítási mátrixának támogatása

Ez a cikk a VMware virtuális gépek és a fizikai kiszolgálók Azure-ba való vész-helyreállításának támogatott összetevőit és beállításait foglalja össze [Azure site Recovery](site-recovery-overview.md)használatával.

- [További](vmware-azure-architecture.md) információ a VMware VM/fizikai kiszolgáló vész-helyreállítási architektúráról.
- A vész-helyreállítás kipróbálásához kövesse az [oktatóanyagokat](tutorial-prepare-azure.md) .

> [!NOTE]
> A Site Recovery nem helyezi át vagy nem tárolja az ügyféladatokat a célként megadott régióból, amely során a forrásként szolgáló gépekhez a vész-helyreállítás lett beállítva. Az ügyfelek választhatnak egy Recovery Services-tárolót egy másik régióból is. A Recovery Services-tároló metaadatokat tartalmaz, de nincsenek tényleges ügyféladatok.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

**Forgatókönyv** | **Részletek**
--- | ---
A VMware virtuális gépek vész-helyreállítása | Helyszíni VMware virtuális gépek replikálása az Azure-ba. Ezt a forgatókönyvet a Azure Portal vagy a [PowerShell](vmware-azure-disaster-recovery-powershell.md)használatával is telepítheti.
Fizikai kiszolgálók vész-helyreállítása | Helyszíni Windows-/Linux-alapú fizikai kiszolgálók replikálása az Azure-ba. Ezt a forgatókönyvet a Azure Portal helyezheti üzembe.

## <a name="on-premises-virtualization-servers"></a>Helyszíni virtualizálási kiszolgálók

**Kiszolgáló** | **Követelmények** | **Részletek**
--- | --- | ---
vCenter Server | Az 7,0-es verzió & ezt a verziót, 6,7, 6,5, 6,0 vagy 5,5 | Javasoljuk, hogy használjon egy vCenter-kiszolgálót a vész-helyreállítási környezetben.
vSphere-gazdagépek | Az 7,0-es verzió & ezt a verziót, 6,7, 6,5, 6,0 vagy 5,5 | Azt javasoljuk, hogy a vSphere-gazdagépek és a vCenter-kiszolgálók ugyanabban a hálózatban legyenek, mint a Process Server. Alapértelmezés szerint a Process Server fut a konfigurációs kiszolgálón. [További információ](vmware-physical-azure-config-process-server-overview.md).

## <a name="site-recovery-configuration-server"></a>Site Recovery konfigurációs kiszolgáló

A konfigurációs kiszolgáló egy helyszíni számítógép, amely Site Recovery összetevőket futtat, beleértve a konfigurációs kiszolgálót, a feldolgozási kiszolgálót és a fő célkiszolgáló kiszolgálóját.

- A VMware virtuális gépek esetében a konfigurációs kiszolgálót úgy állíthatja be, hogy letölt egy OVF-sablont egy VMware virtuális gép létrehozásához.
- Fizikai kiszolgálók esetében manuálisan állítsa be a konfigurációs kiszolgáló számítógépét.

**Összetevő** | **Követelmények**
--- |---
Processzormagok | 8
RAM | 16 GB
Lemezek száma | 3 lemez<br/><br/> A lemezek közé tartozik az operációsrendszer-lemez, a Process Server cache lemez és a visszatartási meghajtó a feladat-visszavételhez.
Lemez szabad területe | 600 GB lemezterület a folyamat kiszolgálói gyorsítótárához.
Lemez szabad területe | 600 GB lemezterület az adatmegőrzési meghajtó számára.
Operációs rendszer  | Windows Server 2012 R2 vagy Windows Server 2016 asztali felülettel <br/><br> Ha a berendezés beépített fő célját szeretné használni a feladat-visszavételhez, győződjön meg arról, hogy az operációs rendszer verziója azonos vagy magasabb, mint a replikált elemek.|
Operációs rendszer területi beállítása | Angol (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | A konfigurációs kiszolgáló [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) -es vagy újabb verziójához nem szükséges.
Windows Server-szerepkörök | Ne engedélyezze a Active Directory Domain Services; Internet Information Services (IIS) vagy Hyper-V.
Csoportházirendek| – A parancssor elérésének tiltása. <br/> – A beállításjegyzék szerkesztési eszközeihez való hozzáférés megakadályozása. <br/> – A fájlmellékletek megbízhatósági logikája. <br/> – A parancsfájlok végrehajtásának bekapcsolása. <br/> - [További információ](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
IIS | Győződjön meg róla, hogy:<br/><br/> -Nem rendelkezik egy korábban létező alapértelmezett webhellyel <br/> – [Névtelen hitelesítés](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) engedélyezése <br/> – [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) -beállítás engedélyezése  <br/> – Ne hallgassa meg előre meglévő webhelyet/alkalmazást a 443-as porton<br/>
Hálózati adapter típusa | VMXNET3 (VMware virtuális gépként való üzembe helyezéskor)
IP-cím típusa | Statikus
Portok | 443 a vezérlési csatorna koordinálásához használatos<br/>9443 adatátvitelhez

> [!NOTE]
Az operációs rendszert angol nyelvvel kell telepíteni. A területi beállítások telepítésének átalakítása potenciális problémákat eredményezhet.

## <a name="replicated-machines"></a>Replikált gépek

Site Recovery támogatja a támogatott gépen futó munkaterhelések replikálását.

**Összetevő** | **Részletek**
--- | ---
Számítógép beállításai | Az Azure-ba replikált gépeknek meg kell felelniük az [Azure-követelményeknek](#azure-vm-requirements).
Gépi munkaterhelés | Site Recovery támogatja a támogatott gépen futó munkaterhelések replikálását. [További információ](./site-recovery-workload.md).
Számítógépnév | Győződjön meg arról, hogy a számítógép megjelenítendő neve nem tartozik az Azure-beli [fenntartott erőforrás-nevekhez](../azure-resource-manager/templates/error-reserved-resource-name.md)<br/><br/> A logikai kötetek neve nem megkülönbözteti a kis-és nagybetűket. Győződjön meg arról, hogy az eszközön nincs két kötet ugyanazzal a névvel. Például: a "voLUME1", a "voLUME1" névvel rendelkező kötetek nem védhetők Azure Site Recoveryon keresztül.

### <a name="for-windows"></a>Windows esetén

**Operációs rendszer** | **Részletek**
--- | ---
Windows Server 2019 | A [34 kumulatív frissítés](https://support.microsoft.com/help/4490016) (a mobilitási szolgáltatás 9,22-es verziója) támogatott.
Windows Server 2016 64 bites | A Server Core és a Server asztali felhasználói felülettel támogatott.
Windows Server 2012 R2/Windows Server 2012 | Támogatott.
Windows Server 2008 R2 SP1 és újabb verziók. | Támogatott.<br/><br/> A mobilitási szolgáltatás ügynökének [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) -es verziójától kezdve telepítenie kell a [SSU](https://support.microsoft.com/help/4490628) -t és az [SHA-2 frissítést](https://support.microsoft.com/help/4474419) a Windows 2008 R2 SP1 vagy újabb verzióját futtató gépekre. Az SHA-1 nem támogatott szeptember 2019-tól, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynök bővítmény nem a várt módon fog települni/frissíteni. További információ az [SHA-2 frissítésről és a követelményekről](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339).
Windows Server 2008 SP2 vagy újabb (64 bites/32 bites) |  Csak Migrálás esetén támogatott. [További információ](migrate-tutorial-windows-server-2008.md).<br/><br/> A mobilitási szolgáltatás ügynökének [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) -es verziójától kezdve a Windows 2008 SP2 rendszerű gépekre telepíteni kell a [SSU](https://support.microsoft.com/help/4493730) -t és az [SHA-2 frissítést](https://support.microsoft.com/help/4474419) . Az SHA-1 nem támogatott szeptember 2019-tól, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynök bővítmény nem a várt módon fog települni/frissíteni. További információ az [SHA-2 frissítésről és a követelményekről](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339).
Windows 10, Windows 8.1, Windows 8 | Csak a 64 bites rendszer támogatott. a 32 bites rendszer nem támogatott.
Windows 7 SP1 64 bites | A [36 kumulatív frissítés](https://support.microsoft.com/help/4503156) (a mobilitási szolgáltatás 9,22-es verziója) támogatott. </br></br> A mobilitási szolgáltatás ügynökének [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) -es verziójától kezdve a Windows 7 SP1 rendszerű gépeken telepíteni kell a [SSU](https://support.microsoft.com/help/4490628) -t és az [SHA-2 frissítést](https://support.microsoft.com/help/4474419) .  Az SHA-1 nem támogatott szeptember 2019-tól, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynök bővítmény nem a várt módon fog települni/frissíteni. További információ az [SHA-2 frissítésről és a követelményekről](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339).

### <a name="for-linux"></a>Linux esetén

**Operációs rendszer** | **Részletek**
--- | ---
Linux | Csak a 64 bites rendszer támogatott. a 32 bites rendszer nem támogatott.<br/><br/>Minden Linux-kiszolgálón telepítve kell lennie a [Linux Integration Services (lis) összetevőknek](https://www.microsoft.com/download/details.aspx?id=55106) . A feladatátvétel/feladatátvételi teszt után a kiszolgálót az Azure-ban kell elindítania. Ha a beépített LIS-összetevők hiányoznak, győződjön meg arról, hogy az [összetevőket](https://www.microsoft.com/download/details.aspx?id=55106) telepíteni kell, mielőtt engedélyezné a gépek replikálását az Azure-ban. <br/><br/> Site Recovery összehangolja a feladatátvételt, hogy Linux-kiszolgálókat futtasson az Azure-ban. Előfordulhat azonban, hogy a Linux-szállítók csak olyan terjesztési verziókra korlátozzák a támogatást, amelyek nem értek véget az élettartamuk.<br/><br/> A Linux-disztribúciók esetében csak a terjesztési alverzió kiadásának vagy frissítésének részét képező állomány-kernelek támogatottak.<br/><br/> Nem támogatott a védett gépek frissítése a nagy Linux-disztribúciós verziók között. A frissítéshez tiltsa le a replikálást, frissítse az operációs rendszert, majd engedélyezze újra a replikációt.<br/><br/> [További](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) információ a Linux és a nyílt forráskódú technológiák támogatásáról az Azure-ban.
Linux Red Hat Enterprise | 5,2 – 5,11</b><br/> 6,1 – 6,10</b> </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9 Beta Version](https://support.microsoft.com/help/4578241/), [7,9](https://support.microsoft.com/help/4590304/) </br> [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609), [8,3](https://support.microsoft.com/help/4597409/) <br/> A Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10-t futtató kiszolgálókon néhány régebbi kernel nem rendelkezik előre telepített [Linux Integration Services-(lis-) összetevőkkel](https://www.microsoft.com/download/details.aspx?id=55106) . Ha a beépített LIS-összetevők hiányoznak, győződjön meg arról, hogy az [összetevőket](https://www.microsoft.com/download/details.aspx?id=55106) telepíteni kell, mielőtt engedélyezné a gépek replikálását az Azure-ban.
Linux: CentOS | 5,2 – 5,11</b><br/> 6,1 – 6,10</b><br/> </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9](https://support.microsoft.com/help/4578241/) </br> [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609), [8,3](https://support.microsoft.com/help/4597409/) <br/><br/> A CentOS 5.2-5.11 & 6,1-6.10-t futtató kiszolgálók néhány régebbi kernele nem rendelkezik előre telepített  [Linux Integration Services-(lis-) összetevőkkel](https://www.microsoft.com/download/details.aspx?id=55106) . Ha a beépített LIS-összetevők hiányoznak, győződjön meg arról, hogy az [összetevőket](https://www.microsoft.com/download/details.aspx?id=55106) telepíteni kell, mielőtt engedélyezné a gépek replikálását az Azure-ban.
Ubuntu | Ubuntu 14,04 * LTS [-kiszolgáló (a támogatott kernel-verziók áttekintése)](#ubuntu-kernel-versions)<br/>Ubuntu 16,04 * LTS [-kiszolgáló (a támogatott kernel-verziók áttekintése)](#ubuntu-kernel-versions) </br> Ubuntu 18,04 * LTS [-kiszolgáló (a támogatott kernel-verziók áttekintése)](#ubuntu-kernel-versions) </br> Ubuntu 20,04 * LTS [-kiszolgáló (a támogatott kernel-verziók áttekintése)](#ubuntu-kernel-versions) </br> (*az összes 14,04 támogatását tartalmazza.* x *, 16,04.* x *, 18,04.* x *, 20,04.* x * verziók)
Debian | Debian 7/Debian 8 (tartalmazza az összes 7 támogatását). *x*, 8. *x* verziók); Debian 9 (a 9,1 és 9,13 közötti támogatással is rendelkezik. A Debian 9,0 nem támogatott.), Debian 10 [(a támogatott kernel-verziók áttekintése)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4, [SP5](https://support.microsoft.com/help/4570609) [(támogatott kernel-verziók áttekintése)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(a támogatott kernel-verziók áttekintése)](#suse-linux-enterprise-server-15-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 11 SP3. [Győződjön meg arról, hogy letölti a legújabb mobilitási ügynök telepítőjét a konfigurációs kiszolgálón](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server). </br> 11 SP4 SUSE Linux Enterprise Server </br> **Megjegyzés**: a replikált gépek frissítése SUSE Linux Enterprise Server 11 SP3-ről SP4-re nem támogatott. A frissítéshez tiltsa le a replikációt, majd engedélyezze újra a frissítést. <br/>|
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4573888/), [7,9](https://support.microsoft.com/help/4597409/), [8,0](https://support.microsoft.com/help/4573888/), [8,1](https://support.microsoft.com/help/4573888/), [8,2](https://support.microsoft.com/help/4573888/), [8,3](https://support.microsoft.com/help/4597409/)  <br/> A Red hat-kompatibilis kernel vagy a nem törhető vállalati kernel kiadásának futtatása 3, 4 & 5 (UEK3, UEK4, UEK5)<br/><br/>8.1<br/>Az összes UEK-kernel és a RedHat kernel <= 3.10.0-1062. * használata támogatott a [9,35](https://support.microsoft.com/help/4573888/) -as számú RedHat-kernel számára a [9,36](https://support.microsoft.com/help/4578241/) -es verzióban

> [!Note]
>- Az egyes Windows-verziók esetében Azure Site Recovery csak a [hosszú távú karbantartási csatorna (LTSC)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) buildeket támogatja.  A [féléves csatorna](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) kiadásai jelenleg nem támogatottak.
>- Győződjön meg arról, hogy a Linux-verziók esetében a Azure Site Recovery nem támogatja a testreszabott operációsrendszer-lemezképeket. Csak a terjesztési alverzió kiadásának vagy frissítésének részét képező állomány-kernelek támogatottak.

### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel-verziók

**Támogatott kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
14,04 LTS | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 3.13.0-24 – általános – 3.13.0 – 170 – általános,<br/>3.16.0-25 – általános – 3.16.0-77 – általános,<br/>3.19.0-18 – általános – 3.19.0 – 80 – általános,<br/>4.2.0-18 – általános – 4.2.0 – 42 – általános,<br/>4.4.0-21 – általános – 4.4.0 – 148 – általános,<br/>4.15.0-1023-Azure-4.15.0-1045-Azure |
|||
16,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21 – általános – 4.4.0 – 197 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0-128 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1102-Azure |
16,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.4.0-21 – általános 4.4.0-194-Generic,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0 – 123 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1098-Azure|
16,04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.4.0-21 – általános – 4.4.0 – 190 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0-118 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1096-Azure|
16,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.4.0-21 – általános – 4.4.0 – 189 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13-Generic – 4.15.0-115 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1093-Azure |
16,04 LTS | [9,36](https://support.microsoft.com/help/4578241/)| 4.4.0-21 – általános 4.4.0-186-Generic,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0 – 112 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1092-Azure |
|||
18,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20 – általános – 4.15.0 – 129 – általános </br> 4.18.0-13 – általános – 4.18.0 – 25 – általános </br> 5.0.0-15 – általános – 5.0.0 – 63 – általános </br> 5.3.0-19 – általános – 5.3.0 – 69 – általános </br> 5.4.0-37 – általános – 5.4.0-59 – általános</br> 4.15.0-1009-Azure-4.15.0-1103-Azure </br> 4.18.0-1006-Azure-4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure – 5.3.0-1035 – Azure </br> 5.4.0-1020-Azure – 5.4.0-1035 – Azure|
18,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.15.0-20 – általános – 4.15.0 – 123 – általános </br> 4.18.0-13 – általános – 4.18.0 – 25 – általános </br> 5.0.0-15 – általános – 5.0.0 – 63 – általános </br> 5.3.0-19 – általános – 5.3.0 – 69 – általános </br> 5.4.0-37 – általános – 5.4.0 – 53 – általános</br> 4.15.0-1009-Azure – 4.15.0-1099 – Azure </br> 4.18.0-1006-Azure-4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure – 5.3.0-1035 – Azure </br> 5.4.0-1020-Azure-5.4.0-1031-Azure|
18,04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.15.0-20 – általános – 4.15.0-118 – általános </br> 4.18.0-13 – általános – 4.18.0 – 25 – általános </br> 5.0.0-15 – általános – 5.0.0 – 61 – általános </br> 5.3.0-19 – általános – 5.3.0 – 67 – általános </br> 5.4.0-37 – általános – 5.4.0 – 48 – általános</br> 4.15.0-1009-Azure-4.15.0-1096-Azure </br> 4.18.0-1006-Azure-4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure – 5.3.0-1035 – Azure </br> 5.4.0-1020-Azure-5.4.0-1026-Azure|
18,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.15.0-20 – általános – 4.15.0-115 – általános </br> 4.18.0-13 – általános – 4.18.0 – 25 – általános </br> 5.0.0-15 – általános – 5.0.0 – 60 – általános </br> 5.3.0-19 – általános – 5.3.0 – 66 – általános </br> 5.4.0-37 – általános – 5.4.0 – 45 – általános</br> 4.15.0-1009-Azure-4.15.0-1093-Azure </br> 4.18.0-1006-Azure-4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure – 5.3.0-1035 – Azure </br> 5.4.0-1020-Azure-5.4.0-1023-Azure|
18,04 LTS | [9,36](https://support.microsoft.com/help/4578241/) | 4.15.0-20 – általános – 4.15.0 – 112 – általános </br> 4.18.0-13 – általános – 4.18.0 – 25 – általános </br> 5.0.0-15 – általános – 5.0.0 – 58 – általános </br> 5.3.0-19 – általános – 5.3.0 – 64 – általános </br> 5.4.0-37 – általános – 5.4.0-42 – általános</br> 4.15.0-1009-Azure-4.15.0-1092-Azure </br> 4.18.0-1006-Azure-4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure-5.3.0-1032-Azure </br> 5.4.0-1020-Azure-5.4.0-1022-Azure|
|||
20,04 LTS |[9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26 – általános – 5.4.0 – 59 </br> -általános 5.4.0-1010-Azure – 5.4.0-1035 – Azure </br> 5.8.0-29 – általános – 5.8.0-34 – általános|
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26 – általános – 5.4.0 – 53 </br> -általános 5.4.0-1010-Azure-5.4.0-1031-Azure
20,04 LTS |[9,38](https://support.microsoft.com/help/4590304/) | 5.4.0-26 – általános – 5.4.0 – 48 </br> -általános 5.4.0-1010-Azure-5.4.0-1026-Azure
20,04 LTS |[9,37](https://support.microsoft.com/help/4582666/) | 5.4.0-26 – általános – 5.4.0 – 45 </br> -általános 5.4.0-1010-Azure-5.4.0-1023-Azure
20,04 LTS |[9,36](https://support.microsoft.com/help/4578241/) | 5.4.0-26 – általános – 5.4.0 – 42 </br> -általános 5.4.0-1010-Azure-5.4.0-1022-Azure

### <a name="debian-kernel-versions"></a>Debian kernel-verziók


**Támogatott kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
Debian 7 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 3.2.0-4-amd64 – 3.2.0-6-amd64, 3.16.0 -0. BPO. 4 – amd64 |
|||
Debian 8 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 3.16.0-4-amd64 – 3.16.0-11-amd64, 4.9.0 -0. BPO. 4-amd64 – 4.9.0 -0. BPO. 11 – amd64 |
|||
Debian 9,1 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64 – 4.9.0-14-amd64 </br> 4.19.0 -0. BPO. 1 – amd64 – 4.19.0 -0. BPO. 13 – amd64 </br> 4.19.0 -0. BPO. 1 – Cloud-amd64 – 4.19.0 -0. BPO. 13 – Cloud-amd64 </br>
Debian 9,1 | [9,39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 – 4.9.0-14-amd64 </br> 4.19.0 -0. BPO. 1 – amd64 – 4.19.0 -0. BPO. 12 – amd64 </br> 4.19.0 -0. BPO. 1 – Cloud-amd64 – 4.19.0 -0. BPO. 12 – Cloud-amd64 </br> 
Debian 9,1 | [9,38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 – 4.9.0-13-amd64 </br> 4.19.0 -0. BPO. 1 – amd64 – 4.19.0 -0. BPO. 11 – amd64 </br> 4.19.0 -0. BPO. 1 – Cloud-amd64 – 4.19.0 -0. BPO. 11 – Cloud-amd64 </br> 
Debian 9,1 | [9,37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 – 4.9.0-13-amd64, 4.19.0 -0. BPO. 6-amd64 – 4.19.0 -0. BPO. 10-amd64, 4.19.0 -0. BPO. 6 – Cloud-amd64 – 4.19.0 -0. BPO. 10 – Cloud-amd64

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 támogatott kernel-verzió

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | A [SUSE 12 SP1, SP2, SP3 és SP4 kernelek összes készlete](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-Azure-4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure-4.12.14-6.43-Azure </br> 4.12.14-16.7-Azure-4.12.14-16.38-Azure|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,39](https://support.microsoft.com/help/4597409/) | A [SUSE 12 SP1, SP2, SP3 és SP4 kernelek összes készlete](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-Azure-4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure-4.12.14-6.43-Azure </br> 4.12.14-16.7-Azure-4.12.14-16.34-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,38](https://support.microsoft.com/help/4590304/) | A [SUSE 12 SP1, SP2, SP3 és SP4 kernelek összes készlete](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-Azure-4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure-4.12.14-6.43-Azure </br> 4.12.14-16.7-Azure-4.12.14-16.28-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/),  | A [SUSE 12 SP1, SP2, SP3 és SP4 kernelek összes készlete](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-Azure-4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure-4.12.14-6.43-Azure </br> 4.12.14-16.7-Azure-4.12.14-16.22-Azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 támogatott kernel-verzió

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Alapértelmezés szerint a rendszer az összes [SUSE 15 és 15 kernelt](https://www.suse.com/support/kb/doc/?id=000019587) támogatja.</br></br> 4.12.14-5.5 – Azure – 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8.5 – Azure – 4.12.14 – 8.55 – Azure </br> 5.3.18 – 16 – Azure </br> 5.3.18-18,5-Azure-5.3.18-18.29-Azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,39](https://support.microsoft.com/help/4597409/)  | Alapértelmezés szerint a rendszer az összes [SUSE 15 és 15 kernelt](https://www.suse.com/support/kb/doc/?id=000019587) támogatja.</br></br> 4.12.14-5.5 – Azure – 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8.5 – Azure – 4.12.14 – 8.47 – Azure </br> 5.3.18 – 16 – Azure </br> 5.3.18-18,5-Azure-5.3.18-18.21-Azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,38](https://support.microsoft.com/help/4590304/)  | Alapértelmezés szerint a rendszer az összes [SUSE 15 és 15 kernelt](https://www.suse.com/support/kb/doc/?id=000019587) támogatja.</br></br> 4.12.14-5.5 – Azure – 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8.5 – Azure – 4.12.14 – 8.44 – Azure </br> 5.3.18 – 16 – Azure </br> 5.3.18-18,5-Azure-5.3.18-18.18-Azure
SUSE Linux Enterprise Server 15 és 15 SP1 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/)  | Alapértelmezés szerint a rendszer az összes [SUSE 15 és 15 kernelt](https://www.suse.com/support/kb/doc/?id=000019587) támogatja.</br></br> 4.12.14-5.5 – Azure – 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8.5 – Azure – 4.12.14 – 8.38 – Azure

## <a name="linux-file-systemsguest-storage"></a>Linux-fájlrendszerek/Guest Storage

**Összetevő** | **Támogatott**
--- | ---
Fájlrendszerek | ext3, ext4, XFS, BTRFS (az erre a táblára vonatkozó feltételek)
A logikai kötetek kezelése (LVM) kiépítés| Sűrű kiépítés – igen <br></br> Vékony kiépítés – nem
A Volume Manager | -Az LVM támogatott.<br/> -a/boot on LVM a [31. kumulatív frissítéssel](https://support.microsoft.com/help/4478871/) (a mobilitási szolgáltatás 9,20-es verziójával) kezdődően támogatott. A korábbi mobilitási szolgáltatás verzióiban nem támogatott.<br/> – Több operációsrendszer-lemez nem támogatott.
Paravirtualizált | A paravirtualizált illesztőprogramok által exportált eszközök nem támogatottak.
Több üzenetsor-blokkoló IO-eszközök | Nem támogatott.
Fizikai kiszolgálók a HP CCISS Storage-vezérlővel | Nem támogatott.
Eszköz/csatlakoztatási pont elnevezési konvenciója | Az eszköz nevének vagy a csatlakoztatási pont nevének egyedinek kell lennie.<br/> Győződjön meg arról, hogy két eszköz/csatlakoztatási pont nem tartalmaz kis-és nagybetűket megkülönböztető neveket. Például a *device1* és a *device1* azonos virtuális géphez tartozó eszközök elnevezése nem támogatott.
Könyvtárak | Ha a 9,20-es verziónál korábbi (a [31. kumulatív frissítésben](https://support.microsoft.com/help/4478871/)kiadott) mobilitási szolgáltatás verzióját futtatja, akkor ezek a korlátozások érvényesek:<br/><br/> – Ezeket a könyvtárakat (ha különálló partícióként/fájlrendszerként kell beállítani) a forráskiszolgáló ugyanazon operációsrendszer-lemezén kell lennie:/(root),/boot,/usr,/usr/local,/var,/etc.</br> – A/boot könyvtárának lemezes partíción kell lennie, és nem lehet LVM-kötet.<br/><br/> A 9,20-es verziótól kezdődően ezek a korlátozások nem érvényesek. 
Rendszerindítási könyvtár | – A rendszerindító lemezek nem lehetnek GPT-partíciós formátumban. Ez egy Azure-architektúra korlátozása. A GPT-lemezek adatlemezként támogatottak.<br/><br/> Egy virtuális gépen több rendszerindító lemez nem támogatott<br/><br/> – a/boot egy LVM köteten több lemezen nem támogatott.<br/> – A rendszerindító lemez nélküli gépet nem lehet replikálni.
Szabad lemezterületre vonatkozó követelmények| 2 GB a/root-partíción <br/><br/> 250 MB a telepítési mappában
XFSv5 | A XFS-fájlrendszerek (például a metaadatok ellenőrzőösszege) XFSv5 funkciói támogatottak (a mobilitási szolgáltatás 9,10-os verziója).<br/> A xfs_info segédprogram használatával keresse meg a partíció XFS-letiltását. Ha a `ftype` értéke 1, akkor a XFSv5 funkciók használatban vannak.
BTRFS | A BTRFS a 34-es [kumulatív frissítéssel](https://support.microsoft.com/help/4490016) (a mobilitási szolgáltatás 9,22-es verziójával) támogatott. A BTRFS nem támogatott, ha:<br/><br/> – A védelem engedélyezése után a rendszer megváltoztatja a BTRFS fájlrendszer alkötetét.</br> – A BTRFS fájlrendszer több lemezre oszlik.</br> – A BTRFS fájlrendszer támogatja a RAID-t.

## <a name="vmdisk-management"></a>VIRTUÁLIS gépek/lemezek kezelése

**Művelet** | **Részletek**
--- | ---
Lemez átméretezése a replikált virtuális gépen | A feladatátvétel előtt a forrás virtuális gép támogatja, közvetlenül a virtuális gép tulajdonságai között. Nincs szükség a replikáció letiltására/újbóli engedélyezésére.<br/><br/> Ha a feladatátvételt követően megváltoztatja a forrás virtuális gépet, a módosítások nem kerülnek rögzítésre.<br/><br/> Ha a feladatátvétel után megváltoztatja az Azure virtuális gépen a lemez méretét, Site Recovery egy új virtuális gépet hoz létre a frissítésekkel.
Lemez hozzáadása a replikált virtuális gépen | Nem támogatott.<br/> Tiltsa le a virtuális gép replikációját, adja hozzá a lemezt, majd engedélyezze újra a replikációt.

> [!NOTE]
> A lemez identitásának módosítása nem támogatott. Ha például a lemez particionálása a GPT-ról MBR-re változott, vagy fordítva, akkor ez megváltoztatja a lemez identitását. Ilyen esetben a replikálás megszakad, és egy új telepítésre lesz szükség. 

## <a name="network"></a>Network (Hálózat)

**Összetevő** | **Támogatott**
--- | ---
Gazdagép hálózati hálózati ADAPTERek összevonása | VMware virtuális gépek esetén támogatott. <br/><br/>Fizikai gépek replikálásához nem támogatott.
Gazdagép hálózati VLAN-je | Igen.
Gazda hálózati IPv4 | Igen.
Gazda hálózati IPv6 | Nem.
Vendég/kiszolgáló hálózati ADAPTERek összevonása | Nem.
Vendég/kiszolgáló hálózati IPv4 | Igen.
Vendég/kiszolgáló hálózati IPv6 | Nem.
Vendég/kiszolgáló hálózati statikus IP-címe (Windows) | Igen.
Vendég/kiszolgáló hálózati statikus IP-címe (Linux) | Igen. <br/><br/>A virtuális gépek DHCP használatára vannak konfigurálva a feladat-visszavétel során.
Vendég/kiszolgáló hálózata több hálózati adapterrel | Igen.
Site Recovery szolgáltatáshoz való privát hivatkozás | Igen. [További információ](hybrid-how-to-enable-replication-private-endpoints.md).


## <a name="azure-vm-network-after-failover"></a>Azure VM-hálózat (feladatátvétel után)

**Összetevő** | **Támogatott**
--- | ---
Azure ExpressRoute | Yes
ILB | Yes
ELB | Yes
Azure Traffic Manager | Yes
Több hálózati adapter | Yes
Fenntartott IP címe | Yes
IPv4 | Yes
Forrás IP-címének megőrzése | Yes
Azure Virtual Network szolgáltatásbeli végpontok<br/> | Yes
Gyorsított hálózatkezelés | No

## <a name="storage"></a>Tárolás
**Összetevő** | **Támogatott**
--- | ---
Dinamikus lemez | Az operációsrendszer-lemeznek alaplemeznek kell lennie. <br/><br/>Az adatlemezek lehetnek dinamikus lemezek
Docker-lemez konfigurációja | No
NFS-gazdagép | Igen, VMware-hez<br/><br/> Nem a fizikai kiszolgálók esetében
Gazdagép SAN (iSCSI/FC) | Yes
Gazdagép vSAN | Igen, VMware-hez<br/><br/> N/A fizikai kiszolgálókhoz
Gazdagép többutas (MPIO) | Igen, tesztelték a Microsoft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM for CLARiiON
Gazdagép virtuális kötetei (VVols) | Igen, VMware-hez<br/><br/> N/A fizikai kiszolgálókhoz
Vendég/kiszolgáló VMDK | Yes
Vendég/kiszolgáló megosztott fürtözött lemeze | No
Vendég/kiszolgáló által titkosított lemez | No
Vendég/kiszolgáló NFS | No
Vendég/kiszolgáló iSCSI | Áttelepítéshez – igen<br/>Vész-helyreállítás esetén az iSCSI a virtuális géphez csatlakoztatott lemezként fogja felvenni a feladat-visszavételt
Vendég/kiszolgáló SMB 3,0 | No
Vendég/kiszolgáló RDM | Yes<br/><br/> N/A fizikai kiszolgálókhoz
Vendég/kiszolgáló lemez > 1 TB | Igen, a lemeznek nagyobbnak kell lennie, mint 1024 MB<br/><br/>Akár 8 192 GB a felügyelt lemezekre való replikáláskor (9,26-es verzió)<br></br> Akár 4 095 GB a Storage-fiókokba való replikáláskor
Vendég/kiszolgáló lemez 4K logikai és 4k fizikai szektor méretével | No
Vendég/kiszolgáló lemez 4K logikai és 512 bájtos fizikai szektor méretével | No
Vendég/kiszolgáló kötet szalagos lemezzel >4 TB | Yes
Logikai kötet kezelése (LVM)| Sűrű kiépítés – igen <br></br> Dinamikus kiépítés – nem
Vendég/kiszolgáló – tárolóhelyek | No
Vendég/kiszolgáló – NVMe felület | No
Vendég/kiszolgáló – gyors Hozzáadás/Eltávolítás lemez | No
Vendég/kiszolgáló – lemez kizárása | Yes
Vendég/kiszolgáló többutas (MPIO) | No
Vendég/kiszolgáló GPT-partíciók | Az 37-es [kumulatív frissítés](https://support.microsoft.com/help/4508614/) (a mobilitási szolgáltatás 9,25-es verziója) a következő öt partíciót támogatja:. Korábban négy támogatott volt.
ReFS | A rugalmas fájlrendszer a mobilitási szolgáltatás 9,23-es vagy újabb verziójával támogatott
Vendég/kiszolgáló EFI/UEFI rendszerindítás | – Az összes [Azure MARKETPLACE UEFI operációs](../virtual-machines/generation-2.md#generation-2-vm-images-in-azure-marketplace) rendszerhez támogatott, site Recovery mobilitási ügynök 9,30-es verziójával. <br/> – A Secure UEFI rendszerindítási típus nem támogatott. [Részletek](../virtual-machines/generation-2.md#on-premises-vs-azure-generation-2-vms)

## <a name="replication-channels"></a>Replikációs csatornák

|**Replikáció típusa**   |**Támogatott**  |
|---------|---------|
|Kiszervezett adatátvitel (ODX)    |       No  |
|Offline előkészítés        |   No      |
| Azure Data Box | No

## <a name="azure-storage"></a>Azure Storage tárterület

**Összetevő** | **Támogatott**
--- | ---
Helyileg redundáns tárolás | Yes
Georedundáns tárolás | Yes
Írásvédett georedundáns tárolás (RA-GRS) | Yes
Ritkán használt tároló | No
Gyors tárolás| No
Blokkblobok | No
Titkosítás – Rest (SSE)| Yes
Titkosítás – Rest (CMK)| Igen (a PowerShell az 3.3.0 modultól kezdődően)
Dupla titkosítás a nyugalmi állapotban | Igen (a PowerShell az 3.3.0 modultól kezdődően). További információ a [Windows](../virtual-machines/disk-encryption.md) és a [Linux](../virtual-machines/disk-encryption.md)támogatott régióiról.
Prémium szintű Storage | Yes
Biztonságos átvitel lehetőség | Yes
Importálási/exportálási szolgáltatás | No
Azure Storage-tűzfalak a virtuális hálózatok | Igen.<br/> A cél Storage/cache Storage-fiókra van konfigurálva (a replikációs adattároláshoz használatos).
Általános célú v2 Storage-fiókok (gyakori és ritka elérésű szintek) | Igen (a tranzakciós költségek lényegesen magasabbak a v2-höz képest, mint a v1)

## <a name="azure-compute"></a>Azure Compute

**Szolgáltatás** | **Támogatott**
--- | ---
Rendelkezésre állási csoportok | Yes
Rendelkezésreállási zónák | No
HUB | Yes
Felügyelt lemezek | Yes

## <a name="azure-vm-requirements"></a>Azure virtuálisgép-követelmények

Az Azure-ba replikált helyszíni virtuális gépeknek meg kell felelniük az ebben a táblázatban összefoglalt Azure-beli virtuálisgép-követelményeknek. Ha Site Recovery futtatja az előfeltétel-ellenőrzés replikálását, akkor az ellenőrzés sikertelen lesz, ha egyes követelmények nem teljesülnek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | A replikált gépek [támogatott operációs rendszereinek](#replicated-machines) ellenőrzése. | Az ellenőrzés sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 64 bites. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemez mérete | Akár 2 048 GB-ig. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 </br> a rendszerindító és a rendszerpartíciók nem támogatottak a különböző lemezeken | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemezek száma | 64 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemez mérete | Akár 8 192 GB a felügyelt lemezre való replikáláskor (9,26-es verzió)<br></br>Akár 4 095 GB a Storage-fiókba való replikáláskor| Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter is támogatott. |
Megosztott VHD | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A számítógép replikálásának engedélyezése előtt le kell tiltani a BitLockert. |
a virtuális gép neve | 1 – 63 karakter.<br/><br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevének betűvel vagy számmal kell kezdődnie és végződnie. |  Frissítse az értéket a Site Recovery számítógép tulajdonságai között.

## <a name="resource-group-limits"></a>Erőforráscsoport korlátai

Az egyetlen erőforráscsoport által védett virtuális gépek számának megismeréséhez tekintse meg az [előfizetés korlátai és kvótái](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)című cikket.

## <a name="churn-limits"></a>Adatforgalom korlátai

Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza.
- Ezek a korlátok a tesztek alapján jelennek meg, de nem fedik le az alkalmazások összes lehetséges I/O-kombinációját.
- A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak.
- A legjobb eredmények elérése érdekében javasoljuk, hogy futtassa az [Deployment Planner eszközt](site-recovery-deployment-planner.md), és végezzen el nagy mennyiségű alkalmazást tesztelési feladatátvétel használatával, hogy az alkalmazás valódi teljesítményű képet kapjon.

**Replikációs cél** | **Átlagos forrás lemez I/O-mérete** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |    Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |20 MB/s | 1684 GB/lemez


**Forrásadat-változás** | **Felső korlát**
---|---
Adatváltozás csúcsértéke az összes lemezen virtuális gépenként | 54 MB/s
Folyamatkiszolgáló által támogatott napi adatváltozás maximuma | 2 TB

- Ezek átlagos értékek, amelyek 30 százalékos I/O-átfedést feltételeznek.
- A Site Recovery képes magasabb átviteli sebesség kezelésére az átfedési arány, a nagyobb írási méretek és a számítási feladatok tényleges I/O-viselkedése alapján.
- Ezek a számok körülbelül öt percet vesznek igénybe. Ez azt jelenti, hogy a feltöltést követő öt percben megtörténik az adat feldolgozása, és létrejön egy helyreállítási pont.

## <a name="vault-tasks"></a>Tár feladatai

**Művelet** | **Támogatott**
--- | ---
Tár áthelyezése az erőforráscsoportok között | No
Tár áthelyezése az előfizetések között | No
Tárterület, hálózat, Azure-beli virtuális gépek áthelyezése az erőforráscsoportok között | No
Helyezze át a tárterületet, a hálózatot és az Azure-beli virtuális gépeket az előfizetések között. | No


## <a name="obtain-latest-components"></a>Legújabb összetevők beszerzése

**Név** | **Leírás** | **Részletek**
--- | --- | ---
Konfigurációs kiszolgáló | A helyszíni telepítése.<br/> Koordinálja a helyszíni VMware-kiszolgálók, a fizikai gépek és az Azure közötti kommunikációt. | - A konfigurációs kiszolgáló [ismertetése](vmware-physical-azure-config-process-server-overview.md) .<br/> - [További](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) információ a legújabb verzióra való frissítésről.<br/> - [Tudnivalók a konfigurációs kiszolgáló beállításáról](vmware-azure-deploy-configuration-server.md) .
Folyamatkiszolgáló | Alapértelmezés szerint telepítve van a konfigurációs kiszolgálón.<br/> Fogadja a replikációs adatokat, optimalizálja azt gyorsítótárazással, tömörítéssel és titkosítással, majd elküldi az Azure-nak.<br/> Az üzembe helyezés során további folyamat-kiszolgálókat adhat hozzá a replikációs forgalom nagyobb mennyiségének kezeléséhez. | - A folyamat kiszolgálójának [megismerése](vmware-physical-azure-config-process-server-overview.md) .<br/> - [További](vmware-azure-manage-process-server.md#upgrade-a-process-server) információ a legújabb verzióra való frissítésről.<br/> - [További információ a](vmware-physical-large-deployment.md#set-up-a-process-server) kibővíthető folyamat-kiszolgálók beállításáról.
Mobility szolgáltatás | A replikálni kívánt VMware virtuális gépen vagy fizikai kiszolgálókon telepítve van.<br/> A helyszíni VMware-kiszolgálók/fizikai kiszolgálók és az Azure közötti replikáció koordinálása.| - [Ismerje meg](vmware-physical-mobility-service-overview.md) a mobilitási szolgáltatást.<br/> - [További](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) információ a legújabb verzióra való frissítésről.<br/>



## <a name="next-steps"></a>Következő lépések
[Ismerje meg, hogyan](tutorial-prepare-azure.md) készítheti elő az Azure-t a VMWare virtuális gépek vész-helyreállításához.

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
