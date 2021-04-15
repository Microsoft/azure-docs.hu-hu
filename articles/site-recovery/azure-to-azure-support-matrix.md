---
title: Támogatási mátrix az Azure-beli virtuális gépek vészhelyreállításához Azure Site Recovery
description: Összegzi az Azure-beli virtuális gépek másodlagos régióba való vészhelyreállításának támogatását a Azure Site Recovery.
ms.topic: article
ms.date: 11/29/2020
ms.openlocfilehash: 02268471d58cbd473493b6001aa9f1df271077bb
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376154"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Azure-beli virtuális gépek Azure-régiók közti vészhelyreállításának támogatási mátrixa

Ez a cikk az Azure-beli virtuális gépek egyik Azure-régióból egy másikba való vészhelyreállításának támogatását és előfeltételeit foglalja össze az Azure Site Recovery [használatával.](site-recovery-overview.md)


## <a name="deployment-method-support"></a>Üzembe helyezési módszerek támogatása

**Üzembe helyezés** |  **Támogatás**
--- | ---
**Azure Portal** | Támogatott.
**PowerShell** | Támogatott. [További információ](azure-to-azure-powershell.md)
**REST API** | Támogatott.
**Parancssori felület** | Egyelőre nem támogatott


## <a name="resource-support"></a>Erőforrás-támogatás

**Erőforrás-művelet** | **Részletek**
--- | ---
**Tárolók áthelyezése erőforráscsoportok között** | Nem támogatott
**Számítási/tárolási/hálózati erőforrások áthelyezése erőforráscsoportok között** | Nem támogatott.<br/><br/> Ha a virtuális gép replikálása után áthelyez egy virtuális gépet vagy társított összetevőket, például tárolót/hálózatot, le kell tiltania, majd újra engedélyeznie kell a virtuális gép replikációját.
**Azure-beli virtuális gépek replikálása egyik előfizetésből a másikba vészhelyreállításhoz** | Ugyanazon a bérlőn belül Azure Active Directory támogatott.
**Virtuális gépek áttelepítése támogatott földrajzi fürtökön belüli régiók között (előfizetésen belül és előfizetések között)** | Ugyanazon a bérlőn belül Azure Active Directory támogatott.
**Azonos régióban található virtuális gépek áttelepítése** | Nem támogatott.
**Azure-beli dedikált gazdagépek** | Nem támogatott.

## <a name="region-support"></a>Régiós támogatás

A virtuális gépeket replikálhatja és helyreállíthatja ugyanazon a földrajzi fürtön belül bármelyik két régió között. A földrajzi fürtök definiálása az adatkésés és a függetlenség szem előtt tartva van meghatározva.


**Földrajzi fürt** | **Azure-régiók**
-- | --
Amerika | Kelet-Kanada, Közép-Kanada, USA déli középső régiója, USA nyugati középső régiója, USA keleti régiója, USA 2. keleti régiója, USA nyugati régiója, USA 2. nyugati régiója, USA középső régiója, USA északi középső régiója
Európa | Egyesült Királyság nyugati régiója, Egyesült Királyság déli régiója, Észak-Európa, Nyugat-Európa, Dél-Afrika nyugati régiója, Dél-Afrika északi régiója, Kelet-Afrika, Közép-Franciaország, Észak-Németország, Nyugat-Németország
Ázsia | Dél-India, Közép-India, Nyugat-India, Délkelet-Ázsia, Kelet-Ázsia, Kelet-Japán, Nyugat-Japán, Dél-Korea középső régiója, Dél-Korea déli régiója
Ausztrália    | Kelet-Ausztrália, Délkelet-Ausztrália, Ausztrália középső régiója, Ausztrália 2. középső régiója
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central
Németország    | Közép-Németország, Északkelet-Németország
Kína | Kelet-Kína, Észak-Kína, Észak-Kína2, Kelet-Kína2
Országonkénti vészhelyreállításra fenntartott korlátozott régiók |Nyugat-Svájc fenntartotta Észak-Svájcot, Dél-Franciaország Közép-Franciaország számára fenntartott, Egyesült Arab Emírségek északi ügyfelei számára korlátozott Egyesült Arab Emírségek középső régiója, Délkelet-Kelet-ügyfelek számára Nyugat-Nyugat

>[!NOTE]
>
> - **Dél-Brazília** esetében a következő régiókban replikálhatja és használhatja a feladatátvételt: USA déli középső régiója, USA nyugati középső régiója, USA keleti régiója, USA 2. keleti régiója, USA nyugati régiója, USA 2. nyugati régiója és USA északi középső régiója.
> - Dél-Brazília csak olyan forrás-régióként használható, amelyből a virtuális gépek replikálhatók a Site Recovery. Nem lehet cél-régió. Ezt a földrajzi távolságok késési problémái okozhatják. Vegye figyelembe, hogy ha a feladatátvételt Dél-Brazília mint forrás-régió veszi át egy célterületre, a célterületről a dél-brazíliai feladat-visszavétel támogatott.
> - Olyan régiókban is dolgozhat, amelyekhez megfelelő hozzáféréssel rendelkezik.
> - Ha a régió, amelyben létre szeretné hozni a tárolót, nem mutatja, győződjön meg arról, hogy az előfizetése rendelkezik hozzáféréssel az erőforrások létrehozásához az abban a régióban.
> - Ha a replikáció engedélyezésekor nem látja a földrajzi fürtön belüli régiót, győződjön meg arról, hogy az előfizetése rendelkezik a virtuális gépek létrehozásához szükséges engedélyekkel az adott régióban.



## <a name="cache-storage"></a>Gyorsítótár

Ez a táblázat összefoglalja a replikáció során használt gyorsítótár-Site Recovery támogatását.

**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Általános célú V2-tárfiókok (a gyors és a hűtési szint) | Támogatott | A GPv2 használata nem ajánlott, mert a V2 tranzakciós költségei jelentősen magasabbak, mint a V1 tárfiókok.
Prémium szintű Storage | Nem támogatott | A standard szintű tárfiókok a gyorsítótár-tároláshoz használatosak a költségek optimalizálása érdekében.
Azure Storage-tűzfalak virtuális hálózatokhoz  | Támogatott | Ha tűzfallal engedélyezett gyorsítótár-tárfiókot vagy céltárfiókot használ, győződjön meg arról, hogy [a "Megbízható Microsoft-szolgáltatások" lehetőséget.](../storage/common/storage-network-security.md#exceptions)<br></br>Győződjön meg arról is, hogy a forrás virtuális hálózat legalább egy alhálózata számára engedélyezi a hozzáférést.

Az alábbi táblázat az egy tárfiókba replikálható lemezek számának korlátait sorolja fel.

**Tárfiók típusa**    |    **Adatváltozás = 4 MBps lemezenként**    |    **Adatváltozás = 8 MBps lemezenként**
---    |    ---    |    ---
V1 tárfiók    |    300 lemez    |    150 lemez
V2 tárfiók    |    750 lemez    |    375 lemez

A lemezek átlagos adatváltozásának növekedésével a tárfiókok által támogatott lemezek száma csökken. A fenti táblázat útmutatóként használható a kiépítendő tárfiókok számáról hozott döntésekhez.

Vegye figyelembe, hogy a fenti korlátok csak az Azure-ral és a zóna–dr. dr. forgatókönyvekkel kapcsolatosak. 

## <a name="replicated-machine-operating-systems"></a>Replikált gépek operációs rendszerei

Site Recovery támogatja az ebben a szakaszban felsorolt operációs rendszereket futtató Azure-beli virtuális gépek replikációját. Vegye figyelembe, hogy ha egy már replikált gépet később egy másik fő kernelre frissít (vagy visszaminősít), le kell tiltania a replikációt, és újra engedélyeznie kell a replikációt a frissítés után.

### <a name="windows"></a>Windows


**Operációs rendszer** | **Részletek**
--- | ---
Windows Server 2019 | A Server Core és a Server with Desktop Experience esetén támogatott.
Windows Server 2016  | Támogatott Server Core, Kiszolgáló asztali tapasztalattal.
Windows Server 2012 R2 | Támogatott.
Windows Server 2012 | Támogatott.
Windows Server 2008 R2 with SP1/SP2 | Támogatott.<br/><br/> Az Azure-beli virtuális gépek Mobility szolgáltatás-bővítményének [9.30-as](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) verziójától a Windows karbantartási verem [frissítését (SSU)](https://support.microsoft.com/help/4490628) és [SHA-2 frissítést](https://support.microsoft.com/help/4474419) kell telepítenie a Windows Server 2008 R2 SP1/SP2 rendszert futtató gépekre.  Az SHA-1 2019 szeptembere óta nem támogatott, és ha az SHA-2 kódalá aláírása nincs engedélyezve, az ügynökbővítmény nem a várt módon lesz telepítve/frissítve. További információ az [SHA-2 frissítésről és követelményekről.](https://aka.ms/SHA-2KB)
Windows 10 (x64) | Támogatott.
Windows 8.1 (x64) | Támogatott.
Windows 8 (x64) | Támogatott.
Windows 7 (x64) SP1 és újabb verziók | Az Azure-beli virtuális gépek Mobility szolgáltatás-bővítményének [9.30-as](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) verziójától a Windows karbantartási verem [frissítését (SSU)](https://support.microsoft.com/help/4490628) és [SHA-2 frissítést](https://support.microsoft.com/help/4474419) kell telepítenie a Windows 7 SP1-et futtató gépekre.  Az SHA-1 2019 szeptembere óta nem támogatott, és ha az SHA-2 kódalá aláírása nincs engedélyezve, az ügynökbővítmény nem a várt módon lesz telepítve/frissítve. További információ az [SHA-2 frissítésről és követelményekről.](https://aka.ms/SHA-2KB)



#### <a name="linux"></a>Linux

**Operációs rendszer** | **Részletek**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4564347/), [7.9](https://support.microsoft.com/help/4578241/), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1, [8.2](https://support.microsoft.com/help/4570609/), [8.3](https://support.microsoft.com/help/4597409/)
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10 </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, [7.8](https://support.microsoft.com/help/4564347/), [7.9 pre-GA verzió,](https://support.microsoft.com/help/4578241/)7.9 GA verzió támogatott a 9.37-es gyorsjavítás javításból** </br> 8.0, 8.1, [8.2,](https://support.microsoft.com/en-us/help/4570609) [8.3](https://support.microsoft.com/help/4597409/)
Ubuntu 14.04 LTS Server | Tartalmazza az összes 14.04-es támogatást. *x* verziók; [Támogatott kernelverziók;](#supported-ubuntu-kernel-versions-for-azure-virtual-machines) 
Ubuntu 16.04 LTS Server | Mind a 16.04-es, mind a 16.04-es támogatott. *x* verziók; [Támogatott kernelverzió](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Előfordulhat, hogy a jelszóalapú hitelesítést és bejelentkezést használó Ubuntu-kiszolgálókon, valamint a felhőbeli virtuális gépek konfigurálási cloud-init csomagján le van tiltva a jelszóalapú bejelentkezés feladatátvételkor (a cloudinit konfigurációtól függően). A jelszóalapú bejelentkezés újra engedélyezhető a virtuális gépen a Jelszó alaphelyzetbe állításával a Támogatási > hibaelhárítása > beállítások menüjében (a virtuális gépen a virtuális gépen a Azure Portal.
Ubuntu 18.04 LTS Server | Az összes 18.04-es e-e támogatását tartalmazza. *x* verziók; [Támogatott kernelverzió](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> A jelszóalapú hitelesítést és bejelentkezést használó Ubuntu-kiszolgálókon, valamint a felhőbeli virtuális gépek konfigurálási cloud-init csomagján előfordulhat, hogy a jelszóalapú bejelentkezés le van tiltva feladatátvételkor (a cloudinit konfigurációtól függően). A jelszóalapú bejelentkezés a virtuális gépen úgy engedélyezhető újra, hogy alaphelyzetbe állítani a jelszót a Támogatási > Hibaelhárítási > beállítások menüjében (a virtuális gép virtuális gépének a virtuális gép Azure Portal.
Ubuntu 20.04 LTS-kiszolgáló | Mind a 20.04-es, mind a 20.04-es támogatást tartalmazza. *x* verziók; [Támogatott kernelverzió](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | Mind a 7-hez támogatást nyújt. *x* [verziók támogatott kernelverziók](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | Mind a 8-at támogatja. *x* [verziók támogatott kernelverziók](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | A 9.1 és 9.13 között támogatott. A Debian 9.0 nem támogatott. [Támogatott kernelverziók](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [Támogatott kernelverziók](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5  [(támogatott kernelverziók)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2[(támogatott kernelverziók)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> A gépek SP3-asról SP4-re való frissítése nem támogatott. Ha egy replikált gép frissítve lett, le kell tiltania a replikációt, és újra engedélyeznie kell a replikációt a frissítés után.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7,](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) [7.8](https://support.microsoft.com/help/4573888/), [7.9](https://support.microsoft.com/help/4597409), [8.0](https://support.microsoft.com/help/4573888/), [8.1](https://support.microsoft.com/help/4573888/) (a Red Hat-kompatibilis kernel vagy a törhetetlen Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)<br/><br/>A 9.35 támogatja a 8.1-es (minden UEK kernelen és RedHat kernelen <= 3.10.0-1062.* verzióban futó [8.1-es](https://support.microsoft.com/help/4573888/)verziókat. A többi RedHat-kernel támogatása [a 9.36-os unkban érhető el)](https://support.microsoft.com/help/4578241/)

> [!NOTE]
> Linux-verziók esetén a Azure Site Recovery nem támogatja a testreszabott operációsrendszer-rendszerképeket. Csak azok a készlet kernelei támogatottak, amelyek az elosztási alverzió kiadásának/frissítésének részei.

**Megjegyzés: A legújabb Linux-kernelek a kiadást követő 15 napon belüli támogatásához Azure Site Recovery a mobilitási ügynök legújabb verzióján található gyorsjavítást. Ez a javítás két főverzió-kiadásban van elérhető. A mobilitási ügynök legújabb verziójára való frissítéshez (beleértve a gyorsjavítási javítást) kövesse a cikkben [említett lépéseket.](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Ez a javítás jelenleg az Azure–Azure dr. forgatókönyvben használt mobilitási ügynökök esetében van kiadva.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Az Azure-beli virtuális gépek támogatott Ubuntu kernelverziói

**Kiadás** | **Mobility szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
14.04 LTS | [9.37](https://support.microsoft.com/help/4582666/), [9.38](https://support.microsoft.com/help/4590304/), [9.39](https://support.microsoft.com/help/4597409/), [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.13.0-24-generic to 3.13.0-170-generic,<br/>3.16.0-25-generic to 3.16.0-77-generic,<br/>3.19.0-18-generic to 3.19.0-80-generic,<br/>4.2.0-18-generic to 4.2.0-42-generic,<br/>4.4.0-21-generic to 4.4.0-148-generic,<br/>4.15.0-1023-azure to 4.15.0-1045-azure |
|||
16.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.4.0-21-generic to 4.4.0-201-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-133-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1106-azure <br/> 4.4.0-203-generic, 4.4.0-204-generic, 4.4.0-206-generic, 4.15.0-136-generic, 4.15.0-137-generic, 4.15.0-139-generic, 4.15.0-140-generic, 4.15.0-1108-azure, 4.15.0-1109-azure, 4.15.0-1110-azure, 4.15.0-1111-azure through 9.41 hot fix patch**|
16.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-generic to 4.4.0-197-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-128-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1102-azure </br> 4.15.0-132-generic, 4.4.0-200-generic, 4.15.0-1106-azure, 4.15.0-133-generic, 4.4.0-201-generic through 9.40 hot fix patch**|
16.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-generic to 4.4.0-194-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-123-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1098-azure </br> 4.4.0-197-generic, 4.15.0-126-generic, 4.15.0-128-generic, 4.15.0-1100-azure, 4.15.0-1102-azure through 9.39 hot fix patch**|
16.04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-generic to 4.4.0-190-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-118-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1096-azure </br> 4.4.0-193-generic, 4.15.0-120-generic, 4.15.0-122-generic, 4.15.0-1098-azure through 9.38 hot fix patch**|
16.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-generic to 4.4.0-189-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-115-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1093-azure </br> 4.4.0-190-generic, 4.15.0-117-generic, 4.15.0-118-generic, 4.15.0-1095-azure, 4.15.0-1096-azure through 9.37 hot fix patch**|
|||
18.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.15.0-20-generic to 4.15.0-135-generic </br> 4.18.0-13-generic to 4.18.0-25-generic </br> 5.0.0-15-generic to 5.0.0-65-generic </br> 5.3.0-19-generic to 5.3.0-70-generic </br> 5.4.0-37-generic to 5.4.0-59-generic</br> 5.4.0-60-generic to 5.4.0-65-generic </br> 4.15.0-1009-azure to 4.15.0-1106-azure </br> 4.18.0-1006-azure to 4.18.0-1025-azure </br> 5.0.0-1012-azure to 5.0.0-1036-azure </br> 5.3.0-1007-azure to 5.3.0-1035-azure </br> 5.4.0-1020-azure to 5.4.0-1039-azure </br> 4.15.0-136-generic, 4.15.0-137-generic, 4.15.0-139-generic, 4.15.0-140-generic, 5.3.0-72-generic, 5.4.0-66-generic, 5.4.0-67-generic, 5.4.0-70-generic, 4.15.0-1108-azure, 4.15.0-1111-azure, 5.4.0-1040-azure, 5.4.0-1041 -azure, 5.4.0-1043-azure, 4.15.0-1109-azure, 4.15.0-1110-azure through 9.41 hot fix patch**|
18.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-generic to 4.15.0-129-generic </br> 4.18.0-13-generic to 4.18.0-25-generic </br> 5.0.0-15-generic to 5.0.0-63-generic </br> 5.3.0-19-generic to 5.3.0-69-generic </br> 5.4.0-37-generic to 5.4.0-59-generic</br> 4.15.0-1009-azure to 4.15.0-1103-azure </br> 4.18.0-1006-azure to 4.18.0-1025-azure </br> 5.0.0-1012-azure to 5.0.0-1036-azure </br> 5.3.0-1007-azure to 5.3.0-1035-azure </br> 5.4.0-1020-azure to 5.4.0-1035-azure </br> 4.15.0-1104-azure, 4.15.0-130-generic, 4.15.0-132-generic, 5.4.0-1036-azure, 5.4.0-60-generic, 5.4.0-62-generic, 4.15.0-1106-azure, 4.15.0-134-generic, 4.15.0-135-generic, 5.. 4.0-1039-azure, 5.4.0-64-generic, 5.4.0-65-generic through 9.40 hot fix patch**|
18.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-generic to 4.15.0-123-generic </br> 4.18.0-13-generic to 4.18.0-25-generic </br> 5.0.0-15-generic to 5.0.0-63-generic </br> 5.3.0-19-generic to 5.3.0-69-generic </br> 5.4.0-37-generic to 5.4.0-53-generic</br> 4.15.0-1009-azure to 4.15.0-1099-azure </br> 4.18.0-1006-azure to 4.18.0-1025-azure </br> 5.0.0-1012-azure to 5.0.0-1036-azure </br> 5.3.0-1007-azure to 5.3.0-1035-azure </br> 5.4.0-1020-azure to 5.4.0-1031-azure </br> 4.15.0-124-generic, 5.4.0-54-generic, 5.4.0-1032-azure, 5.4.0-56-generic, 4.15.0-1100-azure, 4.15.0-126-generic, 4.15.0-128-generic, 5.4.0-58-generic, 4.15.0-1102-azure, 5.4.0-1034-azure through 9.39 hot fix patch**|
18.04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-generic to 4.15.0-118-generic </br> 4.18.0-13-generic to 4.18.0-25-generic </br> 5.0.0-15-generic to 5.0.0-61-generic </br> 5.3.0-19-generic to 5.3.0-67-generic </br> 5.4.0-37-generic to 5.4.0-48-generic</br> 4.15.0-1009-azure to 4.15.0-1096-azure </br> 4.18.0-1006-azure to 4.18.0-1025-azure </br> 5.0.0-1012-azure to 5.0.0-1036-azure </br> 5.3.0-1007-azure to 5.3.0-1035-azure </br> 5.4.0-1020-azure to 5.4.0-1026-azure </br> 4.15.0-121-generic, 4.15.0-122-generic, 5.0.0-62-generic, 5.3.0-68-generic, 5.4.0-51-generic, 5.4.0-52-generic, 4.15.0-1099-azure, 5.4.0-1031-azure through 9.38 hot fix**|
18.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-generic to 4.15.0-115-generic </br> 4.18.0-13-generic to 4.18.0-25-generic </br> 5.0.0-15-generic to 5.0.0-60-generic </br> 5.3.0-19-generic to 5.3.0-66-generic </br> 5.4.0-37-generic to 5.4.0-45-generic</br> 4.15.0-1009-azure to 4.15.0-1093-azure </br> 4.18.0-1006-azure to 4.18.0-1025-azure </br> 5.0.0-1012-azure to 5.0.0-1036-azure </br> 5.3.0-1007-azure to 5.3.0-1035-azure </br> 5.4.0-1020-azure to 5.4.0-1023-azure</br> 4.15.0-117-generic, 4.15.0-118-generic, 5.0.0-61-generic, 5.3.0-67-generic, 5.4.0-47-generic, 5.4.0-48-generic, 4.15.0-1095-azure, 4.15.0-1096-azure, 5.4.0-1025-azure, 5.4.0-1026-azure through 9.37 hot fix patch**|
|||
20,04 LTS |[9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 5.4.0-26-generic to 5.4.0-65 </br> -generic 5.4.0-1010-azure to 5.4.0-1039-azure </br> 5.8.0-29-generic to 5.8.0-43-generic </br> 5.4.0-66-generic, 5.4.0-67-generic, 5.4.0-70-generic, 5.8.0-44-generic, 5.8.0-45-generic, 5.8.0-48-generic, 5.4.0-1040-azure, 5.4.0-1041-azure, 5.4.0-1043-azure through 9.41 hot fix**|
20,04 LTS |[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-generic to 5.4.0-59 </br> -generic 5.4.0-1010-azure to 5.4.0-1035-azure </br> 5.8.0-29-generic to 5.8.0-34-generic </br> 5.4.0-1036-azure, 5.4.0-60-generic, 5.4.0-62-generic, 5.8.0-36-generic, 5.8.0-38-generic, 5.4.0-1039-azure, 5.4.0-64-generic, 5.4.0-65-generic, 5.8.0-40-generic, 5.8.0-41-generic through 9.40 hot fix**|
20,04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic to 5.4.0-53 </br> -generic 5.4.0-1010-azure to 5.4.0-1031-azure </br> 5.4.0-54-generic, 5.8.0-29-generic, 5.4.0-1032-azure, 5.4.0-56-generic, 5.8.0-31-generic, 5.8.0-33-generic, 5.4.0-58-generic, 5.4.0-1034-azure through 9.39 gyorsjavítás**
20,04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic to 5.4.0-53 </br> -generic 5.4.0-1010-azure to 5.4.0-1031-azure </br> 5.4.0-54-generic, 5.8.0-29-generic, 5.4.0-1032-azure, 5.4.0-56-generic, 5.8.0-31-generic, 5.8.0-33-generic, 5.4.0-58-generic, 5.4.0-1034-azure through 9.39 gyorsjavítás**
20,04 LTS |[9,38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-generic to 5.4.0-48 </br> -generic 5.4.0-1010-azure to 5.4.0-1026-azure </br> 5.4.0-51-generic, 5.4.0-52-generic, 5.8.0-23-generic, 5.8.0-25-generic, 5.4.0-1031-azure through 9.38 hot fix patch**
20,04 LTS |[9.37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-generic to 5.4.0-45 </br> -generic 5.4.0-1010-azure to 5.4.0-1023-azure </br> 5.4.0-47-generic, 5.4.0-48-generic, 5.4.0-1025-azure, 5.4.0-1026-azure through 9.37 hot fix patch**

**Megjegyzés: A legújabb Linux-kernelek a kiadást követő 15 napon belüli támogatásához Azure Site Recovery a mobilitási ügynök legújabb verziójára telepített gyorsjavítást. Ez a javítás két főverzió-kiadásban van elérhető. A mobilitási ügynök legújabb verziójára való frissítéshez (beleértve a gyorsjavítási javítást) kövesse a cikkben [említett lépéseket.](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Ez a javítás jelenleg az Azure–Azure dr. forgatókönyvben használt mobilitási ügynökök esetében van kiadva.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Az Azure-beli virtuális gépek támogatott Debian kernelverziói

**Kiadás** | **Mobility szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
Debian 7 | [9.37](https://support.microsoft.com/help/4582666/), [9.38](https://support.microsoft.com/help/4590304/), [9.39](https://support.microsoft.com/help/4597409/), [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) , [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.2.0-4-amd64 to 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.37](https://support.microsoft.com/help/4582666/), [9.38](https://support.microsoft.com/help/4590304/), [9.39](https://support.microsoft.com/help/4597409/), [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.16.0-4-amd64 to 3.16.0-11-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.11-amd64 |
|||
Debian 9.1 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.9.0-1-amd64 –4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 to 4.19.0-0.bpo.14-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 - 4.19.0-0.bpo.14-cloud-amd64 </br> 4.9.0-15-amd64, 4.19.0-0.bpo.16-amd64, 4.19.0-0.bpo.16-cloud-amd64-9.41 gyorsjavítási javítás**
Debian 9.1 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64 –4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 - 4.19.0-0.bpo.13-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64– 4.19.0-0.bpo.13-cloud-amd64 
Debian 9.1 | [9.39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64–4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 to 4.19.0-0.bpo.12-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64– 4.19.0-0.bpo.12-cloud-amd64 </br> 4.19.0-0.bpo.13-amd64, 4.19.0-0.bpo.13-cloud-amd64–9.39 gyorsjavítási javítás**</br> 
Debian 9.1 | [9,38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64–4.9.0-13-amd64 </br> 4.19.0-0.bpo.1-amd64 to 4.19.0-0.bpo.11-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 to 4.19.0-0.bpo.11-cloud-amd64 </br> 4.9.0-14-amd64, 4.19.0-0.bpo.12-amd64, 4.19.0-0.bpo.12-cloud-amd64-9.38 hot fix patch**
Debian 9.1 | [9.37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 –4.9.0-13-amd64, 4.19.0-0.bpo.6-amd64 to 4.19.0-0.bpo.10-amd64, 4.19.0-0.bpo.6-cloud-amd64 to 4.19.0-0.bpo.10-cloud-amd64
|||
Debian 10 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.19.0-5-amd64–4.19.0-14-amd64 </br> 4.19.0-6-cloud-amd64 – 4.19.0-14-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64 </br> 4.19.0-10-cloud-amd64, 4.19.0-16-amd64, 4.19.0-16-cloud-amd64-9.41 gyorsjavítás**
Debian 10 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-amd64 –4.19.0-13-amd64 </br> 4.19.0-6-cloud-amd64 – 4.19.0-13-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64

**Megjegyzés: A legújabb Linux-kernelek a kiadást követő 15 napon belüli támogatásához Azure Site Recovery a mobilitási ügynök legújabb verzióján található gyorsjavítást. Ez a javítás két főverzió-kiadásban van elérhető. A mobilitási ügynök legújabb verziójára való frissítéshez (beleértve a gyorsjavítási javítást) kövesse a cikkben [említett lépéseket.](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Ez a javítás jelenleg az Azure–Azure dr. forgatókönyvben használt mobilitási ügynökök esetében van kiadva.

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Támogatott SUSE Linux Enterprise Server Azure-beli virtuális gépek 12 kernelverziója

**Kiadás** | **Mobility szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | Minden [készlet SUSE 12 SP1, SP2, SP3, SP4,SP5 kernel](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-azure to 4.4.180-4.31-azure,</br>4.12.14-6.3-azure to 4.12.14-6.43-azure </br> 4.12.14-16.7-azure to 4.12.14-16.44-azure </br> 4.12.14-16.47-azure–9.41 gyorsjavítási javítás**|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Minden [készlet SUSE 12 SP1, SP2, SP3, SP4,SP5 kernel](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-azure to 4.4.180-4.31-azure,</br>4.12.14-6.3-azure to 4.12.14-6.43-azure </br> 4.12.14-16.7-azure to 4.12.14-16.38-azure </br> 4.12.14-16.41-azure–9.40 gyorsjavítási javítás**|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.39](https://support.microsoft.com/help/4597409/) | Minden [készlet SUSE 12 SP1, SP2, SP3, SP4,SP5 kernel](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-azure to 4.4.180-4.31-azure,</br>4.12.14-6.3-azure to 4.12.14-6.43-azure </br> 4.12.14-16.7-azure to 4.12.14-16.34-azure </br> 4.12.14-16.38-azure–9.39 gyorsjavítási javítás**|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,38](https://support.microsoft.com/help/4590304/) | Minden [készlet SUSE 12 SP1, SP2, SP3, SP4,SP5 kernel](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-azure to 4.4.180-4.31-azure,</br>4.12.14-6.3-azure to 4.12.14-6.43-azure </br> 4.12.14-16.7-azure to 4.12.14-16.28-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.37](https://support.microsoft.com/help/4582666/) | Minden [készleten SUSE 12 SP1, SP2, SP3, SP4,SP5 kernel](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.4.138-4.7-azure to 4.4.180-4.31-azure,</br>4.12.14-6.3-azure to 4.12.14-6.43-azure </br> 4.12.14-16.7-azure to 4.12.14-16.22-azure </br> 4.12.14-16.25-azure, 4.12.14-16.28-azure–9.37 gyorsjavítás**|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Támogatott SUSE Linux Enterprise Server 15 kernelverzió az Azure-beli virtuális gépekhez

**Kiadás** | **Mobility szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | Alapértelmezés szerint minden [készleten SUSE 15, SP1 és SP2 kernel](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.12.14-5.5-azure to 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure to 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure to 5.3.18-18.35-azure </br> 5.3.18-18.38-azure–9.41 gyorsjavítás**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Alapértelmezés szerint minden [készleten SUSE 15, SP1 és SP2 kernel](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.12.14-5.5-azure to 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure to 4.12.14-8.58-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure to 5.3.18-18.29-azure </br> 5.3.18-18.32-azure, 4.12.14-8.58-azure–9.40 gyorsjavítás**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.39](https://support.microsoft.com/help/4597409/)  | Alapértelmezés szerint minden készlet [SUSE 15, SP1 és SP2 kernel](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.12.14-5.5-azure to 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure to 4.12.14-8.47-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure to 5.3.18-18.21-azure </br> 4.12.14-8.52-azure, 5.3.18-18.24-azure, 4.12.14-8.55-azure, 5.3.18-18.29-azure–9.39 gyorsjavítási javítás**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,38](https://support.microsoft.com/help/4590304/)  | Alapértelmezés szerint minden készlet [SUSE 15, SP1 és SP2 kernel](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.12.14-5.5-azure to 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure to 4.12.14-8.44-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure to 5.3.18-18.18-azure </br> 4.12.14-8.47-azure, 5.3.18-18.21-azure–9.38 gyorsjavítás**
SUSE Linux Enterprise Server 15 és 15 SP1 | [9.37](https://support.microsoft.com/help/4582666/)  | Alapértelmezés szerint minden [készleten SUSE 15, SP1 és SP2 kernel](https://www.suse.com/support/kb/doc/?id=000019587) támogatott.</br></br> 4.12.14-5.5-azure to 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure to 4.12.14-8.38-azure </br> 4.12.14-8.41-azure, 4.12.14-8.44-azure–9.37 gyorsjavítás**

**Megjegyzés: A legújabb Linux-kernelek a kiadást követő 15 napon belüli támogatásához Azure Site Recovery a mobilitási ügynök legújabb verziójára telepített gyorsjavítást. Ez a javítás két főverzió-kiadásban van elérhető. A mobilitási ügynök legújabb verziójára való frissítéshez (beleértve a gyorsjavítási javítást) kövesse a cikkben [említett lépéseket.](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Ez a javítás jelenleg az Azure–Azure dr. forgatókönyvben használt mobilitási ügynökök esetében van kiadva.

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikált gépek – Linux fájlrendszer/vendégtároló

* Fájlrendszerek: ext3, ext4, XFS, BTRFS
* Kötetkezelő: LVM2

> [!NOTE]
> A többcsatornás szoftverek nem támogatottak.


## <a name="replicated-machines---compute-settings"></a>Replikált gépek – számítási beállítások

**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Méret | Bármely Azure-beli virtuális gép mérete legalább 2 processzormaggal és 1 GB RAM-mal | Ellenőrizze [az Azure-beli virtuális gépek méretét.](../virtual-machines/sizes.md)
Rendelkezésre állási csoportok | Támogatott | Ha az alapértelmezett beállításokkal engedélyezi az Azure-beli virtuális gépek replikációját, a rendszer automatikusan létrehoz egy rendelkezésre állási készletet a forrás-régió beállításai alapján. Ezeket a beállításokat módosíthatja.
Rendelkezésreállási zónák | Támogatott |
Hybrid Use Benefit (HUB) | Támogatott | Ha a forrás virtuális gépen engedélyezve van a HUB-licenc, a feladatátvételi teszt vagy a feladatátvételi virtuális gép is a HUB licencet használja.
Virtuálisgép-méretezési csoportok | Nem támogatott |
Azure-katalógus rendszerképe – A Microsoft közzétette | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Azure Gallery-rendszerképek – Külső fél által közzétett | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Egyéni rendszerképek – Külső fél által közzétett | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Virtuális gépek migrálva a Site Recovery | Támogatott | Ha egy VMware virtuális gépet vagy fizikai gépet az Site Recovery használatával migráltak az Azure-ba, el kell távolítania a gépen futó Mobility szolgáltatás régebbi verzióját, majd újra kell indítania a gépet, mielőtt replikálja azt egy másik Azure-régióba.
Azure RBAC-szabályzatok | Nem támogatott | A virtuális gépek azure-beli szerepköralapú hozzáférés-vezérlési (Azure RBAC) szabályzatai nem replikálódnak a cél régióban található feladatátvételi virtuális gépre.
Bővítmények | Nem támogatott | A bővítmények nem replikálódnak a feladatátvételi virtuális gépre a célterületen. A feladatátvételt követően manuálisan kell telepíteni.
Közelségi elhelyezési csoportok | Támogatott | A közelségi elhelyezési csoportban található virtuális gépek védelme a Site Recovery.
Címkék  | Támogatott | A forrás virtuális gépeken alkalmazott, felhasználó által létrehozott címkék a feladatátvételi vagy feladatátvételi teszt után át lesznek hozva a cél virtuális gépekre. A rendszer 24 óránként egyszer replikálja a címkéket a virtuális gép(ek)en, amíg a virtuális gép(ek) jelen vannak vagy vannak a cél-régióban.


## <a name="replicated-machines---disk-actions"></a>Replikált gépek – lemezműveletek

**Művelet** | **Részletek**
-- | ---
Lemez átméretezése a replikált virtuális gépen | A forrás virtuális gépen a feladatátvétel előtt támogatott. A replikációt nem szükséges letiltani/újra engedélyezni.<br/><br/> Ha a feladatátvételt követően módosítja a forrás virtuális gépet, a rendszer nem rögzíti a módosításokat.<br/><br/> Ha a feladatátvételt követően módosítja az Azure-beli virtuális gép lemezméretét, a Site Recovery rögzíti a módosításokat, és a feladat-visszavétel az eredeti virtuálisgép-méretre történik.
Lemez hozzáadása replikált virtuális géphez | Támogatott
A védett lemezek offline változásai | A lemezek leválasztása és offline módosítása teljes újraszinkronizációt igényel.

## <a name="replicated-machines---storage"></a>Replikált gépek – tároló

Ez a táblázat összefoglalta az Azure-beli virtuális gép operációsrendszer-lemezének, adatlemezének és ideiglenes lemezének támogatását.

- A teljesítménybeli problémák elkerülése érdekében fontos [](../virtual-machines/disks-scalability-targets.md) megfigyelni a felügyelt lemezekre vonatkozó virtuálisgép-lemezkorlátokat és -célokat.
- Ha az alapértelmezett beállításokkal telepíti a telepítést, a Site Recovery automatikusan létrehozza a lemezeket és a tárfiókokat a forrásbeállítások alapján.
- Ha testre szabja, kövesse az irányelveket.

**Összetevő** | **Támogatás** | **Részletek**
--- | --- | ---
Operációsrendszer-lemez maximális mérete | 2048 GB | [További információ a](../virtual-machines/managed-disks-overview.md) virtuálisgép-lemezekről.
Ideiglenes lemez | Nem támogatott | Az ideiglenes lemez mindig ki van zárva a replikációból.<br/><br/> Ne tárolja az állandó adatokat az ideiglenes lemezen. [További információ](../virtual-machines/managed-disks-overview.md).
Adatlemez maximális mérete | Felügyelt lemezek esetén 32 TB<br></br>4 TB nem tervezett lemezek esetén|
Adatlemez minimális mérete | Nem vonatkozik korlátozás a nemmanaged disksre. Felügyelt lemezek esetén 2 GB |
Adatlemez maximális száma | Legfeljebb 64, egy adott Azure-beli virtuális gép méretének megfelelően | [További információ a](../virtual-machines/sizes.md) virtuálisgép-méretekkel kapcsolatban.
Adatlemezek módosítási sebessége | Prémium szintű tárolás esetén lemezenként legfeljebb 20 MBps. Standard szintű tároló esetén lemezenként legfeljebb 2 MB/s. | Ha a lemez átlagos adatváltozási sebessége folyamatosan magasabb a maximális értéknél, a replikáció nem fog behozni.<br/><br/>  Ha azonban szórványosan túllépi a maximális értéket, a replikáció behozhatja a korlátot, de kissé késleltetett helyreállítási pontokat láthat.
Adatlemez – standard szintű tárfiók | Támogatott |
Adatlemez – prémium szintű tárfiók | Támogatott | Ha egy virtuális gép lemezei prémium és standard szintű tárfiókok között vannak elterjedve, minden lemezhez külön céltárfiókot választhat, hogy ugyanaz a tárolási konfiguráció legyen a célterületen.
Felügyelt lemez – standard | Olyan Azure-régiókban támogatott, Azure Site Recovery támogatottak. |
Felügyelt lemez – prémium | Olyan Azure-régiókban támogatott, Azure Site Recovery támogatottak. |
Standard SSD | Támogatott |
Redundancia | Az LRS és a GRS támogatott.<br/><br/> A ZRS nem támogatott.
A hűtési és a hűtési és a meleg tárolás | Nem támogatott | A virtuálisgép-lemezek nem támogatottak a hűtési és a gyors adatokat tároló tárolókban
Tárolóhelyek | Támogatott |
NVMe-tárolófelület | Nem támogatott
Titkosítás nem használt adatokhoz (SSE) | Támogatott | Az SSE a tárfiókok alapértelmezett beállítása.
Titkosítás az adattitkosítási (CMK) | Támogatott | A felügyelt lemezek szoftveres és HSM-kulcsokat is támogatnak
Double Encryption at rest | Támogatott | További információ a [Windows](../virtual-machines/disk-encryption.md) és Linux támogatott [régióiról](../virtual-machines/disk-encryption.md)
Azure Disk Encryption (ADE) Windows operációs rendszerhez | Felügyelt lemezekkel virtuális gépek esetén támogatott. | A nem támogatott lemezeket használó virtuális gépek nem támogatottak. <br/><br/> A HSM által védett kulcsok nem támogatottak. <br/><br/> Az egyes kötetek egyetlen lemezen való titkosítása nem támogatott. |
Azure Disk Encryption (ADE) Linux operációs rendszerhez | Felügyelt lemezekkel virtuális gépek esetén támogatott. | A nem támogatott lemezeket használó virtuális gépek nem támogatottak. <br/><br/> A HSM által védett kulcsok nem támogatottak. <br/><br/> Az egyes kötetek egyetlen lemezen való titkosítása nem támogatott. <br><br> A replikáció engedélyezésével kapcsolatos ismert probléma. [Részletek](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
SAS-kulcsrotáció | Nem támogatott | Ha a tárfiókok SAS-kulcsa le van váltva, az ügyfélnek le kell tiltania és újra engedélyeznie kell a replikációt. |
Gazdagép gyorsítótárazása | Támogatott
Gyors hozzáadás    | Támogatott | A replikált Azure-beli virtuális gépekhez hozzáadható adatlemezek replikációjának engedélyezése támogatott a felügyelt lemezeket használni képes virtuális gépeken. <br/><br/> Egy Azure-beli virtuális géphez egyszerre csak egy lemez hozzáadható. Több lemez párhuzamos összeadása nem támogatott. |
Lemez gyors eltávolítása    | Nem támogatott | Ha eltávolítja az adatlemezt a virtuális gépen, le kell tiltania a replikációt, és újra engedélyeznie kell a virtuális gép replikációját.
Lemez kizárása | Támogatás: A konfiguráláshoz [a PowerShellt](azure-to-azure-exclude-disks.md) kell használnia. |    Az ideiglenes lemezek alapértelmezés szerint ki vannak zárva.
Tárolóhelyek – Közvetlen  | Összeomlás-konzisztens helyreállítási pontok esetén támogatott. Az alkalmazás-konzisztens helyreállítási pontok nem támogatottak. |
Horizontálisan felskálás fájlkiszolgáló  | Összeomlás-konzisztens helyreállítási pontok esetén támogatott. Az alkalmazás-konzisztens helyreállítási pontok nem támogatottak. |
DRBD | A DRBD-telepítés részét képezi lemezek nem támogatottak. |
LRS | Támogatott |
GRS | Támogatott |
RA-GRS | Támogatott |
ZRS | Nem támogatott |
Cool and Hot Storage | Nem támogatott | A virtuálisgép-lemezek nem támogatottak a hűtési és a meleg tárolási környezetben
Azure Storage-tűzfalak virtuális hálózatokhoz  | Támogatott | Ha korlátozza a virtuális hálózati hozzáférést a tárfiókok számára, engedélyezze [a Megbízható kapcsolatok Microsoft-szolgáltatások.](../storage/common/storage-network-security.md#exceptions)
Általános célú V2-tárfiókok (mind a hot, mind a cool szint) | Támogatott | A tranzakciós költségek jelentősen növekednek az általános célú V1 tárfiókhoz képest
2. generáció (UEFI-rendszerindítás) | Támogatott
NVMe-lemezek | Nem támogatott
Azure megosztott lemezek | Nem támogatott
Biztonságos átvitel lehetőség | Támogatott
Írásgyorsítóval kompatibilis lemezek | Nem támogatott
Címkék  | Támogatott | A rendszer 24 óránként replikálja a felhasználó által létrehozott címkéket.

>[!IMPORTANT]
> A teljesítménybeli problémák elkerülése érdekében győződjön meg arról, hogy követi a virtuálisgép-lemezek skálázhatósági és teljesítménycéljait a [felügyelt lemezekhez.](../virtual-machines/disks-scalability-targets.md) Ha alapértelmezett beállításokat használ, a Site Recovery létrehozza a szükséges lemezeket és tárfiókokat a forrás konfigurációja alapján. Ha testre szabja és kiválasztja a saját beállításait, kövesse a forrás virtuális gépek lemezméret- és teljesítménycéljait.

## <a name="limits-and-data-change-rates"></a>Korlátozások és adatváltozási arányok

Az alábbi táblázat összefoglalja a Site Recovery korlátokat.

- Ezek a korlátok a tesztjeinken alapulnak, de nyilvánvalóan nem fedik le az alkalmazások összes lehetséges I/O-kombinációját.
- A tényleges eredmények az alkalmazás I/O-kombinációitól függően változhatnak.
- A lemez adatváltozása és a virtuális gépek adatváltozása alapján két korlátot kell figyelembe venni.
- A virtuális gépek adatváltozásának jelenlegi korlátja mérettől függetlenül 54 MB/s.

**Tárolási cél** | **Forráslemez átlagos I/O-i** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |    Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |20 MB/s | Lemezenként 1684 GB

## <a name="replicated-machines---networking"></a>Replikált gépek – hálózat
**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Hálózati adapter | Egy adott Azure-beli virtuális gép méretének maximális támogatott száma | A rendszer a virtuális gép feladatátvétel során való létrehozásakor hoz létre NIC-ket.<br/><br/> A feladatátvevő virtuális gépen található NIC-k száma a replikáció engedélyezésekor a forrás virtuális gépen található NIC-k számától függ. Ha a replikáció engedélyezése után hálózati adaptert ad hozzá vagy távolít el, az nem befolyásolja a hálózati adapterek számát a replikált virtuális gépen a feladatátvétel után. <br/><br/> A feladatátvételt követően a NIC-k sorrendje nem garantált, hogy megegyezik az eredetivel. <br/><br/> A célterületen a szervezet elnevezési konvenciói alapján átnevezheti a NIC-ket. A hálózati adapterek a PowerShell használatával való átnanasztása támogatott.
Internetes Load Balancer | Nem támogatott | Az elsődleges régióban nyilvános/internetes terheléselosztást állíthat be. A nyilvános/internetes terheléselosztást azonban nem támogatják a Azure Site Recovery a dr. régióban.
Belső Load Balancer | Támogatott | Társítsa az előre konfigurált terheléselosztást egy Azure Automation szkript használatával egy helyreállítási tervben.
Nyilvános IP-cím | Támogatott | Meglévő nyilvános IP-cím társítása a hálózati adapterhez. Vagy hozzon létre egy nyilvános IP-címet, és társítsa a hálózati adapterhez egy Azure Automation szkript használatával egy helyreállítási tervben.
Hálózati adapteren lévő NSG | Támogatott | Társítsa az NSG-t a hálózati adapterhez egy Azure Automation szkript használatával egy helyreállítási tervben.
NSG az alhálózaton | Támogatott | Társítsa az NSG-t az alhálózathoz egy Azure Automation szkripttel egy helyreállítási tervben.
Fenntartott (statikus) IP-cím | Támogatott | Ha a forrás virtuális gép hálózati adaptere statikus IP-címmel rendelkezik, és a cél alhálózaton ugyanaz az IP-cím érhető el, a rendszer hozzárendeli a feladat-átveszi virtuális géphez.<br/><br/> Ha a cél alhálózat nem ugyanazt az IP-címet tartalmazza, az alhálózatban elérhető IP-címek egyike a virtuális gép számára lesz fenntartva.<br/><br/> Rögzített IP-címet és alhálózatot is megadhat a **Replikált** elemek beállításai Számítási és hálózati  >    >    >  **adapterek résznél.**
Dinamikus IP-cím | Támogatott | Ha a forrás hálózati adaptere dinamikus IP-címzéssel rendelkezik, a virtuális gép hálózati adaptere is alapértelmezés szerint dinamikus.<br/><br/> Szükség esetén módosíthatja ezt egy rögzített IP-címre.
Több IP-cím | Nem támogatott | Ha olyan virtuális gépet ad át, amely több IP-címmel rendelkező hálózati adapterrel rendelkezik, csak a hálózati adapter elsődleges IP-címe marad meg a forrás régióban. Ha több IP-címet szeretne hozzárendelni, [](recovery-plan-overview.md) hozzáadhat virtuális gépeket egy helyreállítási tervhez, és csatolhat egy szkriptet, amely további IP-címeket rendel a tervhez, vagy manuálisan vagy egy szkript használatával is el tudja elvégezni a feladatátvételt.
Traffic Manager     | Támogatott | Előre konfigurálhatja a Traffic Manager, hogy feladatátvétel esetén a forgalom a forrásterület végpontjára, feladatátvétel esetén pedig a célterület végpontjára lesz irányítva.
Azure DNS | Támogatott |
Egyéni DNS    | Támogatott |
Nem hitelesített proxy | Támogatott | [További információ](./azure-to-azure-about-networking.md)
Hitelesített proxy | Nem támogatott | Ha a virtuális gép hitelesített proxyt használ a kimenő kapcsolathoz, nem replikálható a Azure Site Recovery.
Vpn–hely kapcsolat helyszíni kapcsolattal<br/><br/>(ExpressRoute-okkal vagy anélkül)| Támogatott | Győződjön meg arról, hogy az UDR-ek és az NSG-k úgy vannak konfigurálva, hogy a Site Recovery forgalom ne legyen a helyszíni környezetbe irányítva. [További információ](./azure-to-azure-about-networking.md)
Virtuális hálózatok és virtuális hálózatok kapcsolata    | Támogatott | [További információ](./azure-to-azure-about-networking.md)
Virtuális hálózati szolgáltatásvégpontok | Támogatott | Ha korlátozza a virtuális hálózat tárfiókhoz való hozzáférését, győződjön meg arról, hogy a megbízható Microsoft-szolgáltatások számára engedélyezett a tárfiókhoz való hozzáférés.
Gyorsított hálózatkezelés | Támogatott | A gyorsított hálózatépítést engedélyezni kell a forrás virtuális gépen. [További információ](azure-vm-disaster-recovery-with-accelerated-networking.md).
Palo Alto hálózati berendezés | Nem támogatott | A harmadik féltől származó berendezésekre gyakran vonatkoznak korlátozások a szolgáltatóra a virtuális gépen belül. Azure Site Recovery ügynökre, bővítményekre és kimenő kapcsolatra van szükség ahhoz, hogy elérhető legyen. A berendezés azonban nem hagyja, hogy kimenő tevékenységeket konfiguráljon a virtuális gépen belül.
IPv6  | Nem támogatott | Az IPv4-et és az IPv6-ot is tartalmazó vegyes konfigurációk szintén nem támogatottak. Az IPv6-tartomány alhálózatát minden további művelet előtt Site Recovery fel.
Privát kapcsolat hozzáférése Site Recovery szolgáltatáshoz | Támogatott | [További információ](azure-to-azure-how-to-enable-replication-private-endpoints.md)
Címkék  | Támogatott | A rendszer 24 óránként replikálja a felhasználó által létrehozott címkéket a rendszer a NIC-ken.



## <a name="next-steps"></a>Következő lépések

- Olvassa [el az Azure-beli](./azure-to-azure-about-networking.md)  virtuális gépek replikálása hálózati útmutatóját.
- Vészhelyreállítás üzembe helyezése [Azure-beli virtuális gépek replikálása által.](./azure-to-azure-quickstart.md)
