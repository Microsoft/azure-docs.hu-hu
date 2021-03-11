---
title: Támogatási mátrix az Azure-beli virtuális gépek vész-helyreállításához Azure Site Recovery
description: Összefoglalja az Azure-beli virtuális gépek vész-helyreállításának támogatását egy másodlagos régióba Azure Site Recovery használatával.
ms.topic: article
ms.date: 11/29/2020
ms.author: raynew
ms.openlocfilehash: 1fcb998c6f9dd53a754d348020d4d90e097e9502
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102607781"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Azure-beli virtuális gépek Azure-régiók közti vészhelyreállításának támogatási mátrixa

Ez a cikk összefoglalja az Azure-beli virtuális gépeknek az egyik Azure-régióból a másikba való vész-helyreállításának támogatását és előfeltételeit a [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával.


## <a name="deployment-method-support"></a>Üzembe helyezési módszer támogatása

**Üzembe helyezés** |  **Támogatás**
--- | ---
**Azure Portal** | Támogatott.
**PowerShell** | Támogatott. [További információ](azure-to-azure-powershell.md)
**REST API** | Támogatott.
**Parancssori felület** | Egyelőre nem támogatott


## <a name="resource-support"></a>Erőforrás-támogatás

**Erőforrás-művelet** | **Részletek**
--- | ---
**Tárolók áthelyezése az erőforráscsoportok között** | Nem támogatott
**Számítási/tárolási/hálózati erőforrások áthelyezése az erőforráscsoportok között** | Nem támogatott.<br/><br/> Ha a virtuális gép replikálása után áthelyez egy virtuális gépet vagy a hozzá tartozó összetevőket, például a Storage/Network szolgáltatást, le kell tiltania, majd újra engedélyeznie kell a virtuális gép replikálását.
**Azure-beli virtuális gépek replikálása egyik előfizetésből a másikba vész-helyreállítás céljából** | Ugyanazon Azure Active Directory bérlőn belül támogatott.
**Virtuális gépek áttelepítése régiók között a támogatott földrajzi fürtökön belül (és az előfizetések között)** | Ugyanazon Azure Active Directory bérlőn belül támogatott.
**Virtuális gépek migrálása ugyanazon a régión belül** | Nem támogatott.
**Dedikált Azure-gazdagépek** | Nem támogatott.

## <a name="region-support"></a>Régiós támogatás

A virtuális gépeket replikálhatja és helyreállíthatja az azonos földrajzi fürtön belüli két régió között. A földrajzi fürtöket az adatkésés és a szuverenitás szem előtt tartásával határozzák meg.


**Földrajzi fürt** | **Azure-régiók**
-- | --
Államok | Kelet-Kanada, Közép-Kanada, az USA déli középső régiója, az USA nyugati középső régiója, az USA keleti régiója, USA 2. keleti régiója, USA nyugati régiója, USA 2. középső régiója, USA
Európa | Egyesült Királyság nyugati régiója, Egyesült Királyság déli régiója, Észak-Európa, Nyugat-Európa, Dél-Afrika nyugati régiója, Dél-Afrika északi régiója, Kelet-Kanada, Közép-Németország, Észak-Svájc, Középnyugat-Németország
Ázsia | Dél-India, Közép-India, Nyugat-India, Délkelet-Ázsia, Kelet-Ázsia, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-Korea
Ausztrália    | Kelet-Ausztrália, Délkelet-Ausztrália, Ausztrália középső régiója, Ausztrália 2. középső régiója
Azure Government    | USA-beli államigazgatás – Virginia, USA-beli államigazgatás, Egyesült Államok
Németország    | Közép-Németország, Északkelet-Németország
Kína | Kelet-Kína, Észak-Kína, China North2, China készletek
Országon belüli vész-helyreállításra fenntartott korlátozott régiók |A Észak-Svájc számára fenntartott Nyugat-Svájc, Franciaország déli régiója számára fenntartott, Egyesült Arab Emírségek, Észak-Amerikai Egyesült Királyság

>[!NOTE]
>
> - **Dél-Brazíliában** a következő régiókba replikálhatja és átveheti a feladatokat: USA déli középső régiója, USA nyugati középső régiója, USA 2. keleti régiója, USA nyugati régiója, USA 2. nyugati régiója és az USA északi középső régiója.
> - A Dél-Brazília csak olyan forrás-régióként használható, amelyből a virtuális gépek a Site Recovery használatával replikálhatók. Nem működhet célként megadott régióként. Ez a földrajzi távolságok miatti késési problémák miatt fordul elő. Vegye figyelembe, hogy ha a feladat a Dél-Brazília régióból való átadását célozza meg, a feladat-visszavétel Dél-Brazíliában is támogatott.
> - Olyan régiókban is dolgozhat, amelyekhez megfelelő hozzáférése van.
> - Ha a régió, amelyben létre kívánja hozni a tárolót, nem jelenik meg, győződjön meg arról, hogy az előfizetése hozzáfér az adott régióban található erőforrások létrehozásához.
> - Ha a replikáció engedélyezésekor nem látja a földrajzi fürtön belüli régiót, győződjön meg arról, hogy az előfizetése rendelkezik a virtuális gépek létrehozásához szükséges engedélyekkel az adott régióban.



## <a name="cache-storage"></a>Gyorsítótár

Ez a táblázat a Site Recovery által a replikálás során használt cache Storage-fiók támogatását foglalja össze.

**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Általános célú v2 Storage-fiókok (gyors és lassú elérésű szint) | Támogatott | A GPv2 használata nem ajánlott, mert a v2 tranzakciós költségei lényegesen magasabbak, mint v1 Storage-fiókok.
Prémium szintű Storage | Nem támogatott | A költségek optimalizálása érdekében a standard szintű Storage-fiókok használhatók a gyorsítótár-tároláshoz.
Azure Storage-tűzfalak virtuális hálózatokhoz  | Támogatott | Ha a tűzfalon engedélyezve lévő cache Storage-fiókot vagy a célként megadott Storage-fiókot használja, győződjön meg arról, hogy ["megbízható Microsoft-szolgáltatások engedélyezése"](../storage/common/storage-network-security.md#exceptions).<br></br>Győződjön meg arról is, hogy engedélyezi a hozzáférést a forrás vnet legalább egy alhálózatához.

Az alábbi táblázat felsorolja az egyetlen Storage-fiókba replikálható lemezek számának korlátait.

**Storage-fiók típusa**    |    **Forgalom = 4 MBps/lemez**    |    **Forgalom = 8 MB/s/lemez**
---    |    ---    |    ---
V1 Storage-fiók    |    300 lemez    |    150 lemez
V2 Storage-fiók    |    750 lemez    |    375 lemez

A lemezek átlagos adatforgalmának növekedésével a Storage-fiók által támogatott lemezek száma csökken. A fenti táblázat útmutatóként használható a kiépíteni kívánt tárolási fiókok számával kapcsolatos döntések meghozatalához.

Vegye figyelembe, hogy a fenti korlátok az Azure-ról az Azure-ra és a zóna DR. zónába tartozó forgatókönyvekre vonatkoznak. 

## <a name="replicated-machine-operating-systems"></a>Replikált gépi operációs rendszerek

Site Recovery támogatja az ebben a részben felsorolt operációs rendszereket futtató Azure-beli virtuális gépek replikálását. Vegye figyelembe, hogy ha egy már replikáló gépet egy másik nagy kernelre frissítenek (vagy leértékelnek), le kell tiltania a replikálást, és újra engedélyeznie kell a replikálást a frissítés után.

### <a name="windows"></a>Windows


**Operációs rendszer** | **Részletek**
--- | ---
Windows Server 2019 | A Server Core és a Server asztali felhasználói felülettel támogatott.
Windows Server 2016  | Támogatott Server Core, kiszolgáló asztali felülettel.
Windows Server 2012 R2 | Támogatott.
Windows Server 2012 | Támogatott.
Windows Server 2008 R2 SP1/SP2 | Támogatott.<br/><br/> Az Azure-beli virtuális gépek mobilitási szolgáltatásának [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) -es verziójától kezdve telepítenie kell egy Windows- [karbantartási verem frissítését (SSU)](https://support.microsoft.com/help/4490628) és az [SHA-2 frissítést](https://support.microsoft.com/help/4474419) a Windows Server 2008 R2 SP1/SP2 rendszert futtató számítógépeken.  Az SHA-1 nem támogatott szeptember 2019-tól, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynök bővítmény nem a várt módon fog települni/frissíteni. További információ az [SHA-2 frissítésről és a követelményekről](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Támogatott.
Windows 8,1 (x64) | Támogatott.
Windows 8 (x64) | Támogatott.
Windows 7 (x64) SP1-től | Az Azure-beli virtuális gépek mobilitási szolgáltatásának [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) -es verziójától kezdve telepítenie kell egy Windows- [karbantartási verem frissítését (SSU)](https://support.microsoft.com/help/4490628) és az [SHA-2 frissítést](https://support.microsoft.com/help/4474419) a Windows 7 SP1 rendszert futtató számítógépeken.  Az SHA-1 nem támogatott szeptember 2019-tól, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynök bővítmény nem a várt módon fog települni/frissíteni. További információ az [SHA-2 frissítésről és a követelményekről](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**Operációs rendszer** | **Részletek**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9](https://support.microsoft.com/help/4578241/), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609/), [8,3](https://support.microsoft.com/help/4597409/)
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10 </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, [7,8](https://support.microsoft.com/help/4564347/), [7,9 előzetes verzió](https://support.microsoft.com/help/4578241/), 7,9 ga verziója támogatott a 9,37 gyors javításának javításában * * </br> 8,0, 8,1, [8,2](https://support.microsoft.com/en-us/help/4570609), [8,3](https://support.microsoft.com/help/4597409/)
Ubuntu 14,04 LTS-kiszolgáló | Az összes 14,04-es támogatását tartalmazza. *x* verzió; [Támogatott kernel-verziók](#supported-ubuntu-kernel-versions-for-azure-virtual-machines); 
Ubuntu 16,04 LTS-kiszolgáló | Az összes 16,04-es támogatását tartalmazza. *x* verzió; [Támogatott kernel-verzió](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> A jelszó-alapú hitelesítést és bejelentkezést használó Ubuntu-kiszolgálók, valamint a Cloud-init csomag a felhőalapú virtuális gépek konfigurálásához előfordulhat, hogy a jelszó-alapú bejelentkezés le van tiltva a feladatátvételen (a cloudinit konfigurációjától függően). A jelszó alapú bejelentkezés a virtuális gépen újra engedélyezhető, ha alaphelyzetbe állítja a jelszót a támogatási > hibaelhárítási > beállítások menüjében (a Azure Portal a feladatátvételen átadott virtuális gép).
Ubuntu 18,04 LTS-kiszolgáló | Az összes 18,04-es támogatását tartalmazza. *x* verzió; [Támogatott kernel-verzió](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> A jelszó-alapú hitelesítést és bejelentkezést használó Ubuntu-kiszolgálók, valamint a Cloud-init csomag a felhőalapú virtuális gépek konfigurálásához előfordulhat, hogy a jelszó-alapú bejelentkezés le van tiltva a feladatátvételen (a cloudinit konfigurációjától függően). A jelszó alapú bejelentkezés a virtuális gépen újra engedélyezhető, ha alaphelyzetbe állítja a jelszót a támogatási > hibaelhárítási > beállítások menüjében (a Azure Portal a feladatátvételen átadott virtuális gép).
Ubuntu 20,04 LTS-kiszolgáló | Az összes 20,04-es támogatását tartalmazza. *x* verzió; [Támogatott kernel-verzió](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | A 7 összes támogatását tartalmazza. az *x* verziók [támogatott kernel-verziók](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | A 8 összes támogatását tartalmazza. az *x* verziók [támogatott kernel-verziók](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | A 9,1 és 9,13 közötti támogatást is tartalmaz. A Debian 9,0 nem támogatott. [Támogatott kernel-verziók](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [Támogatott kernel-verziók](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5  [(támogatott kernel-verziók)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2[(támogatott kernel-verziók)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> A replikáló gépek SP3-ről SP4-re való frissítése nem támogatott. Ha egy replikált gépet frissítettek, le kell tiltania a replikálást, és újra engedélyeznie kell a replikálást a frissítés után.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4573888/), [7,9](https://support.microsoft.com/help/4597409), [8,0](https://support.microsoft.com/help/4573888/), [8,1](https://support.microsoft.com/help/4573888/) (a Red hat kompatibilis kernel vagy nem törhető Enterprise kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)<br/><br/>8,1 (az összes UEK kernel és RedHat kernel <= 3.10.0-1062. * támogatott a [9,35](https://support.microsoft.com/help/4573888/)-es verzióban. A RedHat kernelek további támogatása a [9,36](https://support.microsoft.com/help/4578241/)-as verzióban érhető el.)

> [!NOTE]
> Linux-verziók esetében a Azure Site Recovery nem támogatja a testreszabott operációsrendszer-lemezképeket. Csak a terjesztési alverzió kiadásának vagy frissítésének részét képező állomány-kernelek támogatottak.

* * Megjegyzés: a legújabb linuxos kernelek a kiadástól számított 15 napon belül támogatottak, Azure Site Recovery a legújabb mobilitási ügynök verziójának tetején kidobjuk a gyors javítást. Ez a javítás két fő verzió közötti kiadásban érhető el. Ha frissíteni szeretné a mobilitási ügynök legújabb verziójára (beleértve a gyors javítást is), kövesse az [ebben a cikkben](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)említett lépéseket. Ez a javítás jelenleg az Azure-ban az Azure DR-ben használt mobilitási ügynökök számára készült.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Támogatott Ubuntu kernel-verziók az Azure Virtual Machines szolgáltatáshoz

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
14,04 LTS | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.13.0-24 – általános – 3.13.0 – 170 – általános,<br/>3.16.0-25 – általános – 3.16.0-77 – általános,<br/>3.19.0-18 – általános – 3.19.0 – 80 – általános,<br/>4.2.0-18 – általános – 4.2.0 – 42 – általános,<br/>4.4.0-21 – általános – 4.4.0 – 148 – általános,<br/>4.15.0-1023-Azure-4.15.0-1045-Azure |
|||
16,04 LTS | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.4.0-21 – általános – 4.4.0 – 201 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0 – 133 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1106-Azure <br/> 4.4.0-203-Generic, 4.15.0-136-Generic, 4.15.0-1108-Azure a 9,41-es gyorsjavítási javítással * *|
16,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21 – általános – 4.4.0 – 197 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0-128 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1102-Azure </br> 4.15.0-132-Generic, 4.4.0-200-Generic, 4.15.0-1106-Azure, 4.15.0-133-Generic, 4.4.0-201-általános – 9,40-es gyorsjavítási javítás * *|
16,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.4.0-21 – általános 4.4.0-194-Generic,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0 – 123 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1098-Azure </br> 4.4.0-197-Generic, 4.15.0-126-Generic, 4.15.0-128-Generic, 4.15.0-1100-Azure, 4.15.0-1102-Azure a 9,39-es gyors javítás javításával * *|
16,04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.4.0-21 – általános – 4.4.0 – 190 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0-118 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1096-Azure </br> 4.4.0-193-Generic, 4.15.0-120-Generic, 4.15.0-122-Generic, 4.15.0-1098-Azure a 9,38-es gyors javítás javításával * *|
16,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.4.0-21 – általános – 4.4.0 – 189 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13-Generic – 4.15.0-115 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1093-Azure </br> 4.4.0-190-Generic, 4.15.0-117-Generic, 4.15.0-118-Generic, 4.15.0-1095-Azure, 4.15.0-1096-Azure a 9,37-es gyors javítás javításával * *|
|||
18,04 LTS | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.15.0-20 – általános – 4.15.0 – 135 – általános </br> 4.18.0-13 – általános – 4.18.0 – 25 – általános </br> 5.0.0-15 – általános – 5.0.0-65 – általános </br> 5.3.0-19 – általános – 5.3.0 – 70 – általános </br> 5.4.0-37 – általános – 5.4.0-59 – általános</br> 5.4.0-60 – általános – 5.4.0-65 – általános </br> 4.15.0-1009-Azure-4.15.0-1106-Azure </br> 4.18.0-1006-Azure-4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure – 5.3.0-1035 – Azure </br> 5.4.0-1020-Azure-5.4.0-1039-Azure </br> 4.15.0-136-Generic, 5.4.0-66-Generic, 4.15.0-1108-Azure, 5.4.0-1040-Azure a 9,41-es gyors javítás javításával * *|
18,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20 – általános – 4.15.0 – 129 – általános </br> 4.18.0-13 – általános – 4.18.0 – 25 – általános </br> 5.0.0-15 – általános – 5.0.0 – 63 – általános </br> 5.3.0-19 – általános – 5.3.0 – 69 – általános </br> 5.4.0-37 – általános – 5.4.0-59 – általános</br> 4.15.0-1009-Azure-4.15.0-1103-Azure </br> 4.18.0-1006-Azure-4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure – 5.3.0-1035 – Azure </br> 5.4.0-1020-Azure – 5.4.0-1035 – Azure </br> 4.15.0-1104-Azure, 4.15.0-130-Generic, 4.15.0-132-Generic, 5.4.0-1036-Azure, 5.4.0-60-általános, 5.4.0-62-Generic, 4.15.0-1106-Azure, 4.15.0-134-általános, 4.15.0-135-Generic, 5.4.0-1039-Azure, 5.4.0-64-általános, 5.4.0-65-Generic, 9,40-es, gyors javítás|
18,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.15.0-20 – általános – 4.15.0 – 123 – általános </br> 4.18.0-13 – általános – 4.18.0 – 25 – általános </br> 5.0.0-15 – általános – 5.0.0 – 63 – általános </br> 5.3.0-19 – általános – 5.3.0 – 69 – általános </br> 5.4.0-37 – általános – 5.4.0 – 53 – általános</br> 4.15.0-1009-Azure – 4.15.0-1099 – Azure </br> 4.18.0-1006-Azure-4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure – 5.3.0-1035 – Azure </br> 5.4.0-1020-Azure-5.4.0-1031-Azure </br> 4.15.0-124-Generic, 5.4.0-54-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 4.15.0-1100-Azure, 4.15.0-126-Generic, 4.15.0-128-Generic, 5.4.0-58-Generic, 4.15.0-1102-Azure, 5.4.0-1034-Azure-on keresztül 9,39 gyors javítás * *|
18,04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.15.0-20 – általános – 4.15.0-118 – általános </br> 4.18.0-13 – általános – 4.18.0 – 25 – általános </br> 5.0.0-15 – általános – 5.0.0 – 61 – általános </br> 5.3.0-19 – általános – 5.3.0 – 67 – általános </br> 5.4.0-37 – általános – 5.4.0 – 48 – általános</br> 4.15.0-1009-Azure-4.15.0-1096-Azure </br> 4.18.0-1006-Azure-4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure – 5.3.0-1035 – Azure </br> 5.4.0-1020-Azure-5.4.0-1026-Azure </br> 4.15.0-121-Generic, 4.15.0-122-Generic, 5.0.0-62-Generic, 5.3.0-68-Generic, 5.4.0-51-Generic, 5.4.0-52-Generic, 4.15.0-1099-Azure, 5.4.0-1031-Azure a 9,38-es gyors javítás javításával * *|
18,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.15.0-20 – általános – 4.15.0-115 – általános </br> 4.18.0-13 – általános – 4.18.0 – 25 – általános </br> 5.0.0-15 – általános – 5.0.0 – 60 – általános </br> 5.3.0-19 – általános – 5.3.0 – 66 – általános </br> 5.4.0-37 – általános – 5.4.0 – 45 – általános</br> 4.15.0-1009-Azure-4.15.0-1093-Azure </br> 4.18.0-1006-Azure-4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure – 5.3.0-1035 – Azure </br> 5.4.0-1020-Azure-5.4.0-1023-Azure</br> 4.15.0-117-Generic, 4.15.0-118-Generic, 5.0.0-61-általános, 5.3.0-67-Generic, 5.4.0-47-Generic, 5.4.0-48-Generic, 4.15.0-1095-Azure, 4.15.0-1096-Azure, 5.4.0-1025-Azure, 5.4.0-1026-Azure, 9,37 gyors javítás * *|
|||
20,04 LTS |[9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 5.4.0-26 – általános – 5.4.0 – 65 </br> -általános 5.4.0-1010-Azure-5.4.0-1039-Azure </br> 5.8.0-29 – általános – 5.8.0 – 43 – általános </br> 5.4.0-66-Generic, 5.8.0-44-Generic, 5.4.0-1040-Azure a 9,41-es gyorsjavítási javítással * *|
20,04 LTS |[9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26 – általános – 5.4.0 – 59 </br> -általános 5.4.0-1010-Azure – 5.4.0-1035 – Azure </br> 5.8.0-29 – általános – 5.8.0-34 – általános </br> 5.4.0-1036-Azure, 5.4.0-60-Generic, 5.4.0-62-Generic, 5.8.0-36-Generic, 5.8.0-38-Generic, 5.4.0-1039-Azure, 5.4.0-64-Generic, 5.4.0-65-Generic, 5.8.0-40-Generic, 5.8.0-41-általános, 9,40-es gyorsjavítási javítás * *|
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26 – általános – 5.4.0 – 53 </br> -általános 5.4.0-1010-Azure-5.4.0-1031-Azure </br> 5.4.0-54-Generic, 5.8.0-29-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 5.8.0-31-Generic, 5.8.0-33-Generic, 5.4.0-58-Generic, 5.4.0-1034-Azure a 9,39-es gyors javítás javításával * *
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26 – általános – 5.4.0 – 53 </br> -általános 5.4.0-1010-Azure-5.4.0-1031-Azure </br> 5.4.0-54-Generic, 5.8.0-29-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 5.8.0-31-Generic, 5.8.0-33-Generic, 5.4.0-58-Generic, 5.4.0-1034-Azure a 9,39-es gyors javítás javításával * *
20,04 LTS |[9,38](https://support.microsoft.com/help/4590304/) | 5.4.0-26 – általános – 5.4.0 – 48 </br> -általános 5.4.0-1010-Azure-5.4.0-1026-Azure </br> 5.4.0-51-Generic, 5.4.0-52-Generic, 5.8.0-23-Generic, 5.8.0-25-Generic, 5.4.0-1031-Azure a 9,38-es gyors javítás javításával * *
20,04 LTS |[9,37](https://support.microsoft.com/help/4582666/) | 5.4.0-26 – általános – 5.4.0 – 45 </br> -általános 5.4.0-1010-Azure-5.4.0-1023-Azure </br> 5.4.0-47-Generic, 5.4.0-48-Generic, 5.4.0-1025-Azure, 5.4.0-1026-Azure a 9,37-es gyors javítás javításával * *

* * Megjegyzés: a legújabb linuxos kernelek a kiadástól számított 15 napon belül támogatottak, Azure Site Recovery a legújabb mobilitási ügynök verziójának tetején kidobjuk a gyors javítást. Ez a javítás két fő verzió közötti kiadásban érhető el. Ha frissíteni szeretné a mobilitási ügynök legújabb verziójára (beleértve a gyors javítást is), kövesse az [ebben a cikkben](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)említett lépéseket. Ez a javítás jelenleg az Azure-ban az Azure DR-ben használt mobilitási ügynökök számára készült.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Az Azure Virtual Machines által támogatott Debian kernel-verziók

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
Debian 7 | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) , [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.2.0-4-amd64 – 3.2.0-6-amd64, 3.16.0 -0. BPO. 4 – amd64 |
|||
Debian 8 | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.16.0-4-amd64 – 3.16.0-11-amd64, 4.9.0 -0. BPO. 4-amd64 – 4.9.0 -0. BPO. 11 – amd64 |
|||
Debian 9,1 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.9.0-1-amd64 – 4.9.0-14-amd64 </br> 4.19.0 -0. BPO. 1 – amd64 – 4.19.0 -0. BPO. 14 – amd64 </br> 4.19.0 -0. BPO. 1 – Cloud-amd64 – 4.19.0 -0. BPO. 14 – Cloud-amd64
Debian 9,1 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64 – 4.9.0-14-amd64 </br> 4.19.0 -0. BPO. 1 – amd64 – 4.19.0 -0. BPO. 13 – amd64 </br> 4.19.0 -0. BPO. 1 – Cloud-amd64 – 4.19.0 -0. BPO. 13 – Cloud-amd64 
Debian 9,1 | [9,39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 – 4.9.0-14-amd64 </br> 4.19.0 -0. BPO. 1 – amd64 – 4.19.0 -0. BPO. 12 – amd64 </br> 4.19.0 -0. BPO. 1 – Cloud-amd64 – 4.19.0 -0. BPO. 12 – Cloud-amd64 </br> 4.19.0 -0. BPO. 13-amd64, 4.19.0 -0. BPO. 13-Cloud-amd64 a 9,39-es gyors javítással * *</br> 
Debian 9,1 | [9,38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 – 4.9.0-13-amd64 </br> 4.19.0 -0. BPO. 1 – amd64 – 4.19.0 -0. BPO. 11 – amd64 </br> 4.19.0 -0. BPO. 1 – Cloud-amd64 – 4.19.0 -0. BPO. 11 – Cloud-amd64 </br> 4.9.0-14-amd64, 4.19.0 -0. BPO. 12-amd64, 4.19.0 -0. BPO. 12-Cloud-amd64 a 9,38-es gyors javítás javításával * *
Debian 9,1 | [9,37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 – 4.9.0-13-amd64, 4.19.0 -0. BPO. 6-amd64 – 4.19.0 -0. BPO. 10-amd64, 4.19.0 -0. BPO. 6 – Cloud-amd64 – 4.19.0 -0. BPO. 10 – Cloud-amd64
|||
Debian 10 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.19.0-5-amd64 – 4.19.0-14-amd64 </br> 4.19.0-6-Cloud-amd64 – 4.19.0-14-Cloud-amd64 </br> 5.8.0 -0. BPO. 2 – amd64 </br> 5.8.0 -0. BPO. 2 – Cloud-amd64
Debian 10 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-amd64 – 4.19.0-13-amd64 </br> 4.19.0-6-Cloud-amd64 – 4.19.0-13-Cloud-amd64 </br> 5.8.0 -0. BPO. 2 – amd64 </br> 5.8.0 -0. BPO. 2 – Cloud-amd64

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Támogatott SUSE Linux Enterprise Server 12 kernel-verzió az Azure Virtual Machines szolgáltatásban

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | A [SUSE 12 SP1, SP2, SP3, SP4 és SP5 kernelek összes készlete](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-Azure-4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure-4.12.14-6.43-Azure </br> 4.12.14-16.7-Azure-4.12.14-16.44-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | A [SUSE 12 SP1, SP2, SP3, SP4 és SP5 kernelek összes készlete](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-Azure-4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure-4.12.14-6.43-Azure </br> 4.12.14-16.7-Azure-4.12.14-16.38-Azure </br> 4.12.14-16.41-Azure – 9,40-es gyors javítás – javítás * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,39](https://support.microsoft.com/help/4597409/) | A [SUSE 12 SP1, SP2, SP3, SP4 és SP5 kernelek összes készlete](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-Azure-4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure-4.12.14-6.43-Azure </br> 4.12.14-16.7-Azure-4.12.14-16.34-Azure </br> 4.12.14-16.38-Azure – 9,39-es gyors javítás – javítás * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,38](https://support.microsoft.com/help/4590304/) | A [SUSE 12 SP1, SP2, SP3, SP4 és SP5 kernelek összes készlete](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-Azure-4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure-4.12.14-6.43-Azure </br> 4.12.14-16.7-Azure-4.12.14-16.28-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,37](https://support.microsoft.com/help/4582666/) | A [SUSE 12 SP1, SP2, SP3, SP4 és SP5 kernelek összes készlete](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-Azure-4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure-4.12.14-6.43-Azure </br> 4.12.14-16.7-Azure-4.12.14-16.22-Azure </br> 4.12.14-16.25-Azure, 4.12.14-16.28-Azure a 9,37-es gyors javítás javításával * *|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Támogatott SUSE Linux Enterprise Server 15 kernel-verzió az Azure Virtual Machines szolgáltatáshoz

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | Alapértelmezés szerint a rendszer az összes [SUSE 15, SP1 és SP2 kernelt](https://www.suse.com/support/kb/doc/?id=000019587) támogatja.</br></br> 4.12.14-5.5 – Azure – 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8.5 – Azure – 4.12.14 – 8.55 – Azure </br> 5.3.18 – 16 – Azure </br> 5.3.18-18,5-Azure-5.3.18-18.35-Azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Alapértelmezés szerint a rendszer az összes [SUSE 15, SP1 és SP2 kernelt](https://www.suse.com/support/kb/doc/?id=000019587) támogatja.</br></br> 4.12.14-5.5 – Azure – 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8.5 – Azure – 4.12.14 – 8.58 – Azure </br> 5.3.18 – 16 – Azure </br> 5.3.18-18,5-Azure-5.3.18-18.29-Azure </br> 5.3.18-18.32-Azure, 4.12.14-8.58-Azure a 9,40-es gyors javítás javításával * *
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,39](https://support.microsoft.com/help/4597409/)  | Alapértelmezés szerint a rendszer az összes [SUSE 15, SP1 és SP2 kernelt](https://www.suse.com/support/kb/doc/?id=000019587) támogatja.</br></br> 4.12.14-5.5 – Azure – 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8.5 – Azure – 4.12.14 – 8.47 – Azure </br> 5.3.18 – 16 – Azure </br> 5.3.18-18,5-Azure-5.3.18-18.21-Azure </br> 4.12.14-8.52-Azure, 5.3.18-18.24-Azure, 4.12.14-8.55-Azure, 5.3.18-18.29-Azure a 9,39-es gyors javítás javításával * *
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,38](https://support.microsoft.com/help/4590304/)  | Alapértelmezés szerint a rendszer az összes [SUSE 15, SP1 és SP2 kernelt](https://www.suse.com/support/kb/doc/?id=000019587) támogatja.</br></br> 4.12.14-5.5 – Azure – 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8.5 – Azure – 4.12.14 – 8.44 – Azure </br> 5.3.18 – 16 – Azure </br> 5.3.18-18,5-Azure-5.3.18-18.18-Azure </br> 4.12.14-8.47-Azure, 5.3.18-18.21-Azure a 9,38-es gyors javítás javításával * *
SUSE Linux Enterprise Server 15 és 15 SP1 | [9,37](https://support.microsoft.com/help/4582666/)  | Alapértelmezés szerint a rendszer az összes [SUSE 15, SP1 és SP2 kernelt](https://www.suse.com/support/kb/doc/?id=000019587) támogatja.</br></br> 4.12.14-5.5 – Azure – 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8.5 – Azure – 4.12.14 – 8.38 – Azure </br> 4.12.14-8.41-Azure, 4.12.14-8.44-Azure a 9,37-es gyors javítás javításával * *


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikált gépek – Linux fájlrendszer/vendég tárterület

* Fájlrendszerek: ext3, ext4, XFS, BTRFS
* Volume Manager: LVM2

> [!NOTE]
> A többutas szoftverek nem támogatottak.


## <a name="replicated-machines---compute-settings"></a>Replikált gépek – számítási beállítások

**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Méret | Bármely Azure-beli VM-méret legalább 2 CPU-maggal és 1 GB RAM-mal | Ellenőrizze az Azure-beli [virtuális gépek méretét](../virtual-machines/sizes.md).
Rendelkezésre állási csoportok | Támogatott | Ha az alapértelmezett beállításokkal engedélyezi egy Azure-beli virtuális gép replikálását, a rendszer automatikusan létrehoz egy rendelkezésre állási készletet a forrás régió beállításai alapján. Ezek a beállítások módosíthatók.
Rendelkezésreállási zónák | Támogatott |
Hybrid use Benefit (HUB) | Támogatott | Ha a forrásoldali virtuális gépen engedélyezve van a HUB-licenc, a feladatátvételi teszt vagy a feladatátvételi művelet a HUB-licencet is használja.
Virtuálisgép-méretezési csoportok | Nem támogatott |
Azure Gallery-lemezképek – Microsoft published | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Azure Gallery-rendszerképek – harmadik féltől származó közzététel | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Egyéni rendszerképek – harmadik féltől származó közzététel | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Site Recovery használatával áttelepített virtuális gépek | Támogatott | Ha a VMware rendszerű virtuális gépet vagy fizikai gépet áttelepítette az Azure-ba a Site Recovery használatával, el kell távolítania a gépen futó mobilitási szolgáltatás régebbi verzióját, majd újra kell indítania a gépet egy másik Azure-régióba való replikálás előtt.
Azure RBAC-szabályzatok | Nem támogatott | Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) virtuális gépekre vonatkozó házirendjei nem replikálódnak a célként megadott régióban található feladatátvételi virtuális gépre.
Bővítmények | Nem támogatott | A bővítmények nem replikálódnak a célként megadott régióban található feladatátvételi virtuális gépre. A feladatátvételt követően kézzel kell telepíteni.
Proximity elhelyezési csoportok | Támogatott | A közelségi elhelyezési csoportban található virtuális gépeket Site Recovery használatával lehet védeni.
Címkék  | Támogatott | A forrásként szolgáló virtuális gépeken alkalmazott, felhasználó által létrehozott címkéket a rendszer a feladatátvétel vagy feladatátvételi teszt utáni cél virtuális gépekre végzi. A virtuális gép (ek) címkéket 24 óránként egyszer replikálja a rendszer, ha a virtuális gép (ek) be van kapcsolva, és a cél régióban található.


## <a name="replicated-machines---disk-actions"></a>Replikált gépek – lemezes műveletek

**Művelet** | **Részletek**
-- | ---
Lemez átméretezése a replikált virtuális gépen | A feladatátvétel előtt a forrás virtuális gépen támogatott. Nincs szükség a replikáció letiltására/újbóli engedélyezésére.<br/><br/> Ha a feladatátvételt követően megváltoztatja a forrás virtuális gépet, a módosítások nem kerülnek rögzítésre.<br/><br/> Ha a feladatátvételt követően módosítja a lemez méretét az Azure virtuális gépen, akkor a Site Recovery nem rögzíti a módosításokat, és a feladat-visszavétel az eredeti virtuálisgép-méretre változik.
Lemez hozzáadása egy replikált virtuális géphez | Támogatott
Védett lemezek offline módosításai | A lemezek leválasztása és a kapcsolat nélküli módosítások elvégzéséhez teljes újraszinkronizálást kell indítani.

## <a name="replicated-machines---storage"></a>Replikált gépek – tárolás

Ez a táblázat az Azure VM operációsrendszer-lemez, az adatlemez és az ideiglenes lemez támogatását foglalja össze.

- Fontos, hogy megfigyelje a virtuálisgép-lemezek korlátait és a [felügyelt lemezekre](../virtual-machines/disks-scalability-targets.md) vonatkozó célokat, hogy elkerülje a teljesítménnyel kapcsolatos problémákat.
- Ha az alapértelmezett beállításokkal telepíti a szolgáltatást, Site Recovery automatikusan létrehozza a lemezeket és a Storage-fiókokat a forrás beállításai alapján.
- Ha testreszabja, győződjön meg arról, hogy kövesse az irányelveket.

**Összetevő** | **Támogatás** | **Részletek**
--- | --- | ---
OPERÁCIÓSRENDSZER-lemez maximális mérete | 2048 GB | [További](../virtual-machines/managed-disks-overview.md) információ a VM-lemezekről.
Ideiglenes lemez | Nem támogatott | Az ideiglenes lemez mindig ki van zárva a replikációból.<br/><br/> Ne tároljon állandó adatmennyiséget az ideiglenes lemezen. [További információk](../virtual-machines/managed-disks-overview.md).
Adatlemez maximális mérete | 32 TB felügyelt lemezekhez<br></br>4 TB a nem felügyelt lemezekhez|
Adatlemez minimális mérete | Nem felügyelt lemezekre vonatkozó korlátozás. 2 GB a felügyelt lemezekhez |
Adatlemez maximális száma | Akár 64-ig, egy adott Azure-beli virtuálisgép-méret támogatásával összhangban | [További](../virtual-machines/sizes.md) információ a virtuális gépek méreteiről.
Adatlemez változási aránya | A Premium Storage esetében legfeljebb 20 MB/s lemezterület. A standard szintű tároláshoz legfeljebb 2 MB/s lemez adható meg. | Ha a lemez átlagos adatváltozási sebessége folyamatosan meghaladja a maximális értéket, a replikálás nem fog megjelenni.<br/><br/>  Ha azonban a maximális érték szórványosan túllépi a replikálást, a replikáció felmerülhet, de előfordulhat, hogy némileg késleltetett helyreállítási pontok jelenhetnek meg.
Adatlemez – szabványos Storage-fiók | Támogatott |
Adatlemez – Premium Storage-fiók | Támogatott | Ha a virtuális gép rendelkezik lemezekkel a prémium és a standard szintű Storage-fiókok között, kiválaszthat egy másik cél Storage-fiókot az egyes lemezekhez, így biztosítva, hogy ugyanaz a tárolási konfiguráció a céltartományban.
Felügyelt lemez – standard | Olyan Azure-régiókban támogatott, ahol a Azure Site Recovery támogatott. |
Felügyelt lemez – prémium | Olyan Azure-régiókban támogatott, ahol a Azure Site Recovery támogatott. |
Standard SSD | Támogatott |
Redundancia | A LRS és a GRS támogatottak.<br/><br/> A ZRS nem támogatott.
Ritka elérésű és gyors tárolás | Nem támogatott | A virtuálisgép-lemezek nem támogatottak a ritka elérésű és a gyors tárolásban
Tárolóhelyek | Támogatott |
NVMe tárolási felülete | Nem támogatott
Titkosítás nyugalmi állapotban (SSE) | Támogatott | Az SSE a Storage-fiókok alapértelmezett beállítása.
Inaktív titkosítás (CMK) | Támogatott | A felügyelt lemezek esetében a szoftveres és a HSM-kulcsok is támogatottak
Dupla titkosítás a nyugalmi állapotban | Támogatott | További információ a [Windows](../virtual-machines/disk-encryption.md) és a [Linux](../virtual-machines/disk-encryption.md) támogatott régióiról
Azure Disk Encryption (ADE) Windows operációs rendszerhez | Felügyelt lemezekkel rendelkező virtuális gépek esetén támogatott. | A nem felügyelt lemezeket használó virtuális gépek nem támogatottak. <br/><br/> A HSM-védelemmel ellátott kulcsok nem támogatottak. <br/><br/> Az egyes kötetek egyetlen lemezen való titkosítása nem támogatott. |
Azure Disk Encryption (ADE) Linux operációs rendszerhez | Felügyelt lemezekkel rendelkező virtuális gépek esetén támogatott. | A nem felügyelt lemezeket használó virtuális gépek nem támogatottak. <br/><br/> A HSM-védelemmel ellátott kulcsok nem támogatottak. <br/><br/> Az egyes kötetek egyetlen lemezen való titkosítása nem támogatott. <br><br> A replikáció engedélyezésének ismert problémája. [Részletek](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
SAS-kulcs elforgatása | Nem támogatott | Ha a Storage-fiókok SAS-kulcsát elforgatják, az ügyfélnek le kell tiltania és újra engedélyeznie kell a replikálást. |
Gazdagép gyorsítótárazása | Támogatott
Gyors Hozzáadás    | Támogatott | A replikált Azure-beli virtuális géphez hozzáadott adatlemez replikálásának engedélyezése támogatott a felügyelt lemezeket használó virtuális gépek esetében. <br/><br/> Egyszerre csak egy lemezt lehet felvenni egy Azure-beli virtuális gépre. Több lemez párhuzamos hozzáadása nem támogatott. |
Gyors lemez eltávolítása    | Nem támogatott | Ha eltávolít egy adatlemezt a virtuális gépen, le kell tiltania a replikációt, és újra engedélyeznie kell a replikációt a virtuális gépen.
Lemez kizárása | Támogatás: A konfiguráláshoz a [PowerShellt](azure-to-azure-exclude-disks.md) kell használnia. |    Az ideiglenes lemezek alapértelmezés szerint ki vannak zárva.
Tárolóhelyek – Közvetlen  | Összeomlás-konzisztens helyreállítási pontok esetén támogatott. Az alkalmazás konzisztens helyreállítási pontjai nem támogatottak. |
Kibővíthető fájlkiszolgáló  | Összeomlás-konzisztens helyreállítási pontok esetén támogatott. Az alkalmazás konzisztens helyreállítási pontjai nem támogatottak. |
DRBD | A DRBD-telepítés részét képező lemezek nem támogatottak. |
LRS | Támogatott |
GRS | Támogatott |
RA-GRS | Támogatott |
ZRS | Nem támogatott |
Ritka elérésű és gyors tárolás | Nem támogatott | A virtuális gépek lemezei nem támogatottak a ritka és a gyakori tárterületen
Azure Storage-tűzfalak virtuális hálózatokhoz  | Támogatott | Ha korlátozza a virtuális hálózati hozzáférést a Storage-fiókokhoz, engedélyezze a [megbízható Microsoft-szolgáltatások engedélyezését](../storage/common/storage-network-security.md#exceptions).
Általános célú v2 Storage-fiókok (a gyors és a lassú elérésű szint) | Támogatott | A tranzakciós költségek jelentősen növekednek az általános célú v1 Storage-fiókokkal szemben
2. generációs (UEFI rendszerindítási) | Támogatott
NVMe-lemezek | Nem támogatott
Azure megosztott lemezek | Nem támogatott
Biztonságos átvitel lehetőség | Támogatott
Gyorssegéd-kompatibilis lemezek írása | Nem támogatott
Címkék  | Támogatott | A felhasználó által generált címkéket 24 óránként replikálja a rendszer.

>[!IMPORTANT]
> A teljesítményproblémák elkerülése érdekében győződjön meg arról, hogy követte a virtuális gépek méretezhetőségét és a teljesítményre vonatkozó célokat a [felügyelt lemezeken](../virtual-machines/disks-scalability-targets.md). Ha az alapértelmezett beállításokat használja, Site Recovery létrehozza a szükséges lemezeket és a Storage-fiókokat a forrás konfigurációja alapján. Ha testreszabja és kiválasztja a saját beállításait, kövesse a forrásként szolgáló virtuális gépek lemezének méretezhetőségét és teljesítményére vonatkozó célokat.

## <a name="limits-and-data-change-rates"></a>Korlátok és adatváltozások aránya

Az alábbi táblázat Site Recovery korlátozásokat foglalja össze.

- Ezek a korlátok a tesztek alapján jelennek meg, de nyilvánvalóan nem fedik le az alkalmazások minden lehetséges I/O-kombinációját.
- A tényleges eredmények az alkalmazás I/O-kombinációja alapján változhatnak.
- A lemezes adatforgalom és a virtuális gépek adatváltozása esetében két korlátozást kell figyelembe venni.
- A virtuális gépekre vonatkozó adatváltozások jelenlegi korlátja 54 MB/s, a mérettől függetlenül.

**Tárolási cél** | **Átlagos forrás lemez I/O** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |    Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |20 MB/s | 1684 GB/lemez

## <a name="replicated-machines---networking"></a>Replikált gépek – hálózatkezelés
**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Hálózati adapter | Egy adott Azure-beli virtuálisgép-méret által támogatott maximális szám | A hálózati adapterek akkor jönnek létre, amikor a virtuális gép létrejön a feladatátvétel során.<br/><br/> A feladatátvételi virtuális gépen lévő hálózati adapterek száma a forrás virtuális gépen lévő hálózati adapterek számától függ, amikor a replikáció engedélyezve volt. Ha a replikáció engedélyezése után ad hozzá vagy távolít el egy hálózati adaptert, az nem befolyásolja a replikált virtuális gépen lévő hálózati adapterek számát a feladatátvételt követően. <br/><br/> A NIC-ket a feladatátvételt követően nem garantált, hogy megegyezzen az eredeti rendeléssel. <br/><br/> A hálózati adaptereket átnevezheti a cél régióban a szervezet elnevezési konvenciói alapján. A NIC-Átnevezés a PowerShell használatával támogatott.
Internetes Load Balancer | Nem támogatott | A nyilvános/Internet Load balancert az elsődleges régióban állíthatja be. A DR régióban azonban Azure Site Recovery nem támogatja a nyilvános/internetes terheléselosztást.
Belső terheléselosztó | Támogatott | Az előre konfigurált terheléselosztó hozzárendelése egy Azure Automation parancsfájl használatával helyreállítási tervben.
Nyilvános IP-cím | Támogatott | Társítson egy meglévő nyilvános IP-címet a hálózati adapterhez. Vagy hozzon létre egy nyilvános IP-címet, és társítsa azt a hálózati adapterhez egy helyreállítási tervben Azure Automation parancsfájl használatával.
NSG a hálózati adapteren | Támogatott | Társítsa a NSG a hálózati adapterhez egy helyreállítási tervben Azure Automation parancsfájl használatával.
NSG az alhálózaton | Támogatott | Társítsa a NSG az alhálózathoz egy helyreállítási tervben Azure Automation parancsfájl használatával.
Fenntartott (statikus) IP-cím | Támogatott | Ha a forrás virtuális gépen lévő hálózati adapter statikus IP-címmel rendelkezik, és a célként megadott alhálózatnak ugyanaz az IP-címe, akkor a feladatátvételi virtuális géphez van rendelve.<br/><br/> Ha a célként megadott alhálózat nem rendelkezik a rendelkezésre álló IP-címmel, az alhálózat egyik elérhető IP-címe a virtuális gép számára van fenntartva.<br/><br/> Rögzített IP-címet és alhálózatot is megadhat a **replikált elemek**  >  **beállításainak**  >  **számítási és hálózati**  >  **adapterei** között.
Dinamikus IP-cím | Támogatott | Ha a forrás hálózati adaptere dinamikus IP-címzést tartalmaz, a feladatátvételt okozó virtuális gép hálózati adaptere alapértelmezés szerint is dinamikus.<br/><br/> Ha szükséges, módosíthatja a rögzített IP-címet.
Több IP-cím | Nem támogatott | Ha olyan virtuális gépet hajt végre, amelynek több IP-címmel rendelkező hálózati adaptere van, akkor a rendszer csak a forrás régióban lévő hálózati adapter elsődleges IP-címét tárolja. Több IP-cím hozzárendeléséhez hozzáadhat virtuális gépeket egy [helyreállítási tervhez](recovery-plan-overview.md) , és csatolhat egy parancsfájlt további IP-címek hozzárendeléséhez a csomaghoz, vagy a módosítást manuálisan vagy parancsfájllal is elvégezheti a feladatátvétel után.
Traffic Manager     | Támogatott | A Traffic Manager előre konfigurálható úgy, hogy a forgalmat a rendszer rendszeres időközönként a forrás régiójában lévő végpontra irányítsa, a feladatátvétel esetén pedig a cél régióban lévő végpontra.
Azure DNS | Támogatott |
Egyéni DNS    | Támogatott |
Nem hitelesített proxy | Támogatott | [További információ](./azure-to-azure-about-networking.md)
Hitelesített proxy | Nem támogatott | Ha a virtuális gép hitelesített proxyt használ a kimenő kapcsolathoz, nem replikálható Azure Site Recovery használatával.
Helyszíni VPN-helyek közötti kapcsolat<br/><br/>(ExpressRoute vagy anélkül)| Támogatott | Győződjön meg arról, hogy a UDR és a NSG úgy vannak konfigurálva, hogy a Site Recovery forgalom ne legyen átirányítva a helyszíni környezetbe. [További információ](./azure-to-azure-about-networking.md)
VNET – VNET-kapcsolatok    | Támogatott | [További információ](./azure-to-azure-about-networking.md)
Virtuális hálózati szolgáltatásvégpontok | Támogatott | Ha korlátozza a virtuális hálózati hozzáférést a Storage-fiókokhoz, győződjön meg arról, hogy a megbízható Microsoft-szolgáltatások hozzáférése engedélyezett a Storage-fiókhoz.
Gyorsított hálózatkezelés | Támogatott | A gyorsított hálózatkezelést engedélyezni kell a forrásoldali virtuális gépen. [További információk](azure-vm-disaster-recovery-with-accelerated-networking.md).
Palo Alto hálózati berendezés | Nem támogatott | A harmadik féltől származó készülékekkel gyakran a virtuális gépen belüli szolgáltató korlátozza a korlátozásokat. Azure Site Recovery szüksége van az ügynökre, a bővítményekre és a kimenő kapcsolatokra. A készülék azonban nem teszi lehetővé, hogy a kimenő tevékenységek a virtuális gépen belül legyenek konfigurálva.
IPv6  | Nem támogatott | Az IPv4 és az IPv6 protokollt is tartalmazó vegyes konfigurációk nem támogatottak. A Site Recovery művelet előtt szabadítson fel az IPv6-tartomány alhálózatát.
Site Recovery szolgáltatáshoz való privát hivatkozás | Támogatott | [További információ](azure-to-azure-how-to-enable-replication-private-endpoints.md)
Címkék  | Támogatott | A hálózati adapterek felhasználó által generált címkéit 24 óránként replikálja a rendszer.



## <a name="next-steps"></a>Következő lépések

- Olvassa el az Azure-beli virtuális gépek replikálásához szükséges [hálózatkezelési útmutatót](./azure-to-azure-about-networking.md)  .
- A vész-helyreállítás üzembe helyezése az Azure-beli [virtuális gépek replikálásával](./azure-to-azure-quickstart.md).
