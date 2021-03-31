---
title: VMware virtuális gépek/fizikai kiszolgálók vész-helyreállítása másodlagos helyre Azure Site Recovery
description: Megtudhatja, hogyan állíthatja be a VMware virtuális gépek, illetve a Windows-és Linux-alapú fizikai kiszolgálók vész-helyreállítását egy másodlagos helyre Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 71d230c9fea25edfbf0ca4ea40f15b69779ad060
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84711901"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Helyszíni VMware-alapú virtuális gépek vagy fizikai kiszolgálók vészhelyreállításának beállítása másodlagos helyre

Az inmage Scout a [Azure site Recovery](site-recovery-overview.md) valós idejű replikációt tesz lehetővé a helyszíni VMware-helyek között. Az inmage Scout a Azure Site Recovery szolgáltatás-előfizetések részét képezi.

## <a name="end-of-support-announcement"></a>Támogatás megszűnésének bejelentése

A helyszíni VMware-vagy fizikai adatközpontok közötti replikálás Azure Site Recoveryi forgatókönyve támogatja a támogatás végét.

-   A forgatókönyv nem konfigurálható a Recovery Services-tárolóban, és az inmage Scout szoftver nem tölthető le a tárból. 2018 A meglévő telepítések támogatottak lesznek. 
-   December 31 2020-én a forgatókönyv nem támogatott.
- A támogatás befejezéséig a meglévő partnerek új ügyfeleket hozhatnak be a forgatókönyvbe.

A 2018-es és a 2019-es verzióban két frissítés jelenik meg: 

-   7. frissítés: megjavítja a hálózati konfigurációt és a megfelelőségi problémákat, és biztosítja a TLS 1,2-támogatást.
-   8. frissítés: támogatást nyújt a Linux operációs rendszerek RHEL/CentOS 7.3/7.4/7.5 és SUSE 12 rendszerhez

A 8. frissítés után a rendszer nem szabadít fel további frissítéseket. A 8. frissítésben hozzáadott operációs rendszerek esetében korlátozott a gyorsjavítások támogatása, a hibajavítások pedig a legjobb erőfeszítés alapján.

A Azure Site Recovery továbbra is innovációra törekszik azáltal, hogy a VMware és a Hyper-V ügyfeleinek zökkenőmentes és legjobb minőségű DRaaS megoldást biztosít az Azure-ban vész-helyreállítási webhelyként. A Microsoft azt javasolja, hogy a meglévő inmage/ASR Scout-ügyfelek a Azure Site Recovery VMware – Azure forgatókönyvet használják az üzletmenet folytonossági igényei szerint. Azure Site Recovery VMware-ről Azure-ra forgatókönyve egy nagyvállalati szintű DR-megoldás VMware-alkalmazásokhoz, amely RPO és RTO biztosít, támogatja a több virtuális gépre kiterjedő alkalmazások replikálását és helyreállítását, a zökkenőmentes bevezetést, az átfogó monitorozást és a jelentős TCO előnyeit.

### <a name="scenario-migration"></a>Forgatókönyv – Migrálás
Alternatív megoldásként javasoljuk, hogy a helyszíni VMware virtuális gépek és a fizikai gépek vész-helyreállítását az Azure-ba replikálva állítsa be. Ezt a következőképpen teheti meg:

1.  Tekintse át az alábbi gyors összehasonlítást. A helyszíni gépek replikálásához ellenőriznie kell, hogy megfelelnek-e az Azure-ba történő replikáció [követelményeinek](./vmware-physical-azure-support-matrix.md#replicated-machines) . Ha VMware virtuális gépeket replikál, javasoljuk, hogy tekintse át a [kapacitás-tervezési irányelveket](./site-recovery-plan-capacity-vmware.md), és futtassa az [Deployment Planner eszközt](./site-recovery-deployment-planner.md) az identitási kapacitásra vonatkozó követelményekkel, és ellenőrizze a megfelelőséget.
2.  A Deployment Planner futtatása után beállíthatja a replikációt: o a VMware virtuális gépekhez, a következő oktatóanyagok segítségével [előkészítheti az Azure](./tutorial-prepare-azure.md)-t, előkészítheti [a helyszíni VMware-környezetet](./vmware-azure-tutorial-prepare-on-premises.md), és beállíthatja a vész- [helyreállítást](./vmware-azure-tutorial-prepare-on-premises.md).
o fizikai gépek esetén kövesse ezt az [oktatóanyagot](./physical-azure-disaster-recovery.md).
3.  Miután a gépek az Azure-ba replikálódnak, a vész- [helyreállítási gyakorlat](./site-recovery-test-failover-to-azure.md) futtatásával gondoskodhat arról, hogy minden a várt módon működjön.

### <a name="quick-comparison"></a>Gyors összehasonlítás

**Szolgáltatás** | **Replikálás az Azure-ba** |**Replikálás a VMware-adatközpontok között**
--|--|--
**Szükséges összetevők** |Mobilitási szolgáltatás replikált gépeken. Helyszíni konfigurációs kiszolgáló, Process Server, fő célkiszolgáló. Ideiglenes folyamat-kiszolgáló az Azure-ban a feladat-visszavételhez.|Mobilitási szolgáltatás, a Process Server, a konfigurációs kiszolgáló és a fő cél
**Konfigurálás és előkészítés** |Recovery Services tároló a Azure Portal | A vContinuum használata 
**Replikált** |Lemez (Windows és Linux) |Volume-Windows<br> Disk-Linux
**Megosztott lemezes fürt** |Nem támogatott|Támogatott
**Adatforgalomra vonatkozó korlátok (átlag)** |10 MB/s adatlemez<br> 25MB/s-adatmennyiség virtuális gépenként<br> [További információ](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | Lemezenként > 10 MB/s adatmennyiség  <br> Virtuális gépenként > 25 MB/s adatmennyiség
**Figyelés** |Azure Portal|CX-ból (konfigurációs kiszolgáló)
**Támogatási mátrix** | [A részletekért kattintson ide](./vmware-physical-azure-support-matrix.md)|[Az ASR-felderítővel kompatibilis mátrix letöltése](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez:

- [Tekintse át](vmware-physical-secondary-support-matrix.md) az összes összetevő támogatási követelményeit.
- Győződjön meg róla, hogy a replikálni kívánt gépek megfelelnek a [replikált gépek támogatásának](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Összetevők frissítéseinek letöltése és telepítése

 Tekintse át és telepítse a legújabb [frissítéseket](#updates). A frissítéseket a következő sorrendben kell telepíteni a kiszolgálókra:

1. RX-kiszolgáló (ha van ilyen)
2. Konfigurációs kiszolgálók
3. Kiszolgálók feldolgozása
4. Fő célkiszolgáló
5. vContinuum-kiszolgálók
6. Forráskiszolgáló (Windows és Linux rendszerű kiszolgálók)

Telepítse a frissítéseket a következőképpen:

> [!NOTE]
>Az összes felderítő összetevő fájljának frissítési verziója nem lehet azonos az Update. zip fájlban. A régebbi verzió azt jelzi, hogy az összetevő nem módosult a frissítés korábbi frissítése óta.

Töltse le az [Update](https://aka.ms/asr-scout-update7) . zip fájlt, valamint a [MySQL és a PHP frissítési](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) konfigurációs fájljait. Az Update. zip fájl a következő összetevők összes alap bináris fájljait és kumulatív frissítését tartalmazza: 
- InMage_ScoutCloud_RX_8 InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015. tar. gz
- RX_8 RX_8.0.7.0_GA_Update_7_2965621_28Dec18. tar. gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8 InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release. tar. gz
  1. Bontsa ki a. zip-fájlokat.
  2. **Rx-kiszolgáló**: másolja a **RX_8.0.7.0_GA_Update_7_2965621_28Dec18. tar. gz** -t az RX-kiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/install** parancsot.
  3. **Konfigurációs kiszolgáló és folyamat kiszolgálója**: **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** másolása a konfigurációs kiszolgálóra és a Process Serverre. Kattintson rá duplán a futtatásához.<br>
  4. **Windows fő célkiszolgáló**: az egyesített ügynök frissítéséhez másolja **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** a kiszolgálóra. Kattintson rá duplán a futtatásához. Ugyanez a fájl is használható a friss telepítéshez. Ugyanez az egyesített ügynök frissítése is alkalmazható a forráskiszolgálón.
  A frissítést nem kell alkalmazni a **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exere**  előkészített fő célhelyen, mivel ez új GA-telepítő a legújabb módosításokkal.
  5. **vContinuum-kiszolgáló**: **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** másolása a kiszolgálóra.  Győződjön meg arról, hogy bezárta a vContinuum varázslót. A futtatásához kattintson duplán a fájlra.
  6. **Linuxos fő célkiszolgáló**: az egyesített ügynök frissítéséhez másolja a **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz** -t a Linux fő célkiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/install** parancsot.
  7. **Windows-forráskiszolgáló**: az egyesített ügynök frissítéséhez másolja **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** a forráskiszolgálóról. A futtatásához kattintson duplán a fájlra. 
  8. **Linuxos forráskiszolgáló**: az egyesített ügynök frissítéséhez másolja az egyesített ügynök fájljának megfelelő verzióját a Linux-kiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/install** parancsot.  Példa: a RHEL 6,7 64 bites kiszolgáló esetében másolja a **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz** -t a kiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/install** parancsot.
  9. A konfigurációs kiszolgáló, a Process Server és az RX kiszolgáló és a fenti telepítők frissítése után a PHP-és MySQL-kódtárakat manuálisan kell frissíteni a [gyors telepítési útmutató](https://aka.ms/asr-scout-quick-install-guide)7,4. szakaszában említett lépések végrehajtásával.

## <a name="enable-replication"></a>A replikáció engedélyezése

1. Állítsa be a replikációt a forrás és a cél VMware-helyek között.
2. A telepítéssel, a védelemmel és a helyreállítással kapcsolatos további információkért tekintse meg a következő dokumentumokat:

   * [Kibocsátási megjegyzések](https://aka.ms/asr-scout-release-notes)
   * [Kompatibilitási mátrix](https://aka.ms/asr-scout-cm)
   * [Felhasználói útmutató](https://aka.ms/asr-scout-user-guide)
   * [RX felhasználói útmutató](https://aka.ms/asr-scout-rx-user-guide)
   * [Gyors telepítési útmutató](https://aka.ms/asr-scout-quick-install-guide)
   * [MYSQL-és PHP-kódtárak frissítése](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Frissítések

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 7. frissítés 
Frissítve: 2018. december 31-ig a [Scout Update 7](https://aka.ms/asr-scout-update7)letöltése.
A Scout Update 7 egy teljes telepítő, amely a friss telepítéshez, valamint a korábbi frissítésekhez tartozó meglévő ügynökök/MT frissítéséhez is használható (az 1. frissítéstől a 6. frissítésig). Az 1. frissítés összes javítását tartalmazza a 6. frissítéshez, valamint az alább ismertetett új javításokat és fejlesztéseket.
 
#### <a name="new-features"></a>Új funkciók
* PCI-megfelelőség
* TLS 2.0-támogatás

#### <a name="bug-and-security-fixes"></a>Hibák és biztonsági javítások
* Kijavítva: a Windows-fürt/önálló gépek helytelen IP-konfigurációval rendelkeznek a helyreállítás/DR-részletezés alapján.
* Kijavítva: a V2V-fürtön időnként sikertelen volt a lemez hozzáadása művelet.
* Kijavítva: a vContinuum varázsló elakad a helyreállítási fázis során, ha a fő cél a Windows Server 2016
* Kijavítva: a MySQL biztonsági problémák enyhítésére a MySQL és a verzió 5.7.23 frissítése

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>A PHP és a MySQL manuális frissítése CS, PS és RX rendszeren
A PHP Scripting platformot frissíteni kell a konfigurációs kiszolgáló, a Process Server és az RX-kiszolgáló 7.2.10 verzióra.
A MySQL adatbázis-kezelő rendszert a konfigurációs kiszolgáló, a Process Server és az RX-kiszolgáló 5.7.23 verzióra kell frissíteni.
A PHP-és MySQL-verziók frissítéséhez kövesse a [gyors telepítési útmutatóban](https://aka.ms/asr-scout-quick-install-guide) megadott utasításokat.

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 6. frissítés 
Frissítve: október 12., 2017

A [Scout 6 frissítésének](https://aka.ms/asr-scout-update6)letöltése.

A Scout Update 6 egy összesítő frissítés. Az 1. frissítés összes javítását tartalmazza az 5. frissítéshez, valamint az alább ismertetett új javításokat és fejlesztéseket. 

#### <a name="new-platform-support"></a>Új platform-támogatás
* Támogatás lett hozzáadva a forrás Windows Server 2016-hez
* A támogatás a következő Linux operációs rendszerekhez lett hozzáadva:
    - Red Hat Enterprise Linux (RHEL) 6,9
    - CentOS 6,9
    - Oracle Linux 5,11
    - Oracle Linux 6,8
* Támogatás lett hozzáadva a VMware Center 6,5-hez

Telepítse a frissítéseket a következőképpen:

> [!NOTE]
>Az összes felderítő összetevő fájljának frissítési verziója nem lehet azonos az Update. zip fájlban. A régebbi verzió azt jelzi, hogy az összetevő nem módosult a frissítés korábbi frissítése óta.

Töltse le az [Update](https://aka.ms/asr-scout-update6) . zip fájlt. A fájl a következő összetevőket tartalmazza: 
- RX_8 RX_8.0.4.0_GA_Update_4_8725872_16Sep16. tar. gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA Update4 BITS a RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_ \<Linux OS> _8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz
  1. Bontsa ki a. zip-fájlokat.
  2. **Rx-kiszolgáló**: másolja a **RX_8.0.4.0_GA_Update_4_8725872_16Sep16. tar. gz** -t az RX-kiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/install** parancsot.
  3. **Konfigurációs kiszolgáló és folyamat kiszolgálója**: **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** másolása a konfigurációs kiszolgálóra és a Process Serverre. Kattintson rá duplán a futtatásához.<br>
  4. **Windows fő célkiszolgáló**: az egyesített ügynök frissítéséhez másolja **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** a kiszolgálóra. Kattintson rá duplán a futtatásához. Ugyanez az egyesített ügynök frissítése is alkalmazható a forráskiszolgálón. Ha a forrás nem frissült a 4. frissítésre, frissítse az egyesített ügynököt.
  A frissítést nem kell alkalmazni a **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exere**  előkészített fő célhelyen, mivel ez új GA-telepítő a legújabb módosításokkal.
  5. **vContinuum-kiszolgáló**: **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** másolása a kiszolgálóra.  Győződjön meg arról, hogy bezárta a vContinuum varázslót. A futtatásához kattintson duplán a fájlra.
  A frissítést nem kell alkalmazni a **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exere** előkészített fő célhelyen, mivel ez új GA-telepítő a legújabb módosításokkal.
  6. **Linuxos fő célkiszolgáló**: az egyesített ügynök frissítéséhez másolja **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz-** t a fő célkiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/install** parancsot.
  7. **Windows-forráskiszolgáló**: az egyesített ügynök frissítéséhez másolja **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** a forráskiszolgálóról. A futtatásához kattintson duplán a fájlra. 
  Nem kell telepítenie az 5. frissítés ügynököt a forráskiszolgálón, ha már frissítve lett a 4-es frissítésre vagy a forrás-ügynökre a legújabb alapszintű telepítő **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Linuxos forráskiszolgáló**: az egyesített ügynök frissítéséhez másolja az egyesített ügynök fájljának megfelelő verzióját a Linux-kiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/install** parancsot.  Példa: RHEL 6,7 64-bites kiszolgáló esetén másolja **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz-** t a kiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/install** parancsot.


> [!NOTE]
> * A Windows rendszerhez készült alapszintű egyesített ügynök (UA) telepítője frissült a Windows Server 2016 támogatásához. Az új telepítő **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** az alap Scout GA-csomaggal (**InMage_Scout_Standard_8.0,1 GA-Oct17.zip**) van csomagolva. Ugyanazt a telepítőt fogja használni az összes támogatott Windows-verzióhoz. 
> * A Windows Server 2016-es verziójának támogatásához az alapszintű Windows vContinuum & fő cél telepítőjének frissítése megtörtént. Az új telepítő **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** az alap Scout GA-csomaggal (**InMage_Scout_Standard_8.0,1 GA-Oct17.zip**) van csomagolva. Ugyanez a telepítő fogja használni a Windows 2016 Master Target és a Windows 2012R2 fő cél telepítését.
> * Az ASR-felderítő nem támogatja a Windows Server 2016-et a fizikai kiszolgálón. Csak a Windows Server 2016 VMware virtuális gépeket támogatja. 
>

#### <a name="bug-fixes-and-enhancements"></a>Hibajavítások és fejlesztések
- A feladat-visszavételi védelem meghiúsul, ha a Linux rendszerű virtuális gép a replikálni kívánt lemezek listáját üresen hagyja a konfiguráció végén.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Update 5
A Scout Update 5 összesítő frissítés. Az 1. frissítés összes javítását tartalmazza a 4. frissítéshez, valamint az alább ismertetett új javításokat.
- A Site Recovery felderítő frissítés 4-es verziójának javításai kifejezetten a fő cél-és vContinuum-összetevőkhöz szükségesek.
- Ha a forráskiszolgáló, a fő cél, a konfiguráció, a folyamat és az RX-kiszolgáló már futtatja a 4-es frissítést, akkor csak a fő célkiszolgálón alkalmazza. 

#### <a name="new-platform-support"></a>Új platform-támogatás
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)
* A SLES 11 SP4 64 bites  **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release. tar. gz** az alap Scout GA csomaggal (**InMage_Scout_Standard_8.0,1 GA.zip**) van csomagolva. Töltse le a GA-csomagot a portálról a tár létrehozása című témakörben leírtak szerint.


#### <a name="bug-fixes-and-enhancements"></a>Hibajavítások és fejlesztések

* Javítások a Windows-fürt fokozottan támogatott megbízhatóságához:
    * Javítva – a P2V MSCS-fürt egyes lemezei a helyreállítás után NYERSek lesznek.
    * A rögzített P2V MSCS-fürt helyreállítása nem sikerül, mert nem egyezik a lemez sorrendje.
    * Javítva – a lemezek hozzáadására szolgáló MSCS művelet meghiúsul, ha a lemez mérete nem egyezik.
    * Kijavítva – a MSCS-fürt RDM LUN-hozzárendeléssel való készültségének ellenőrzése sikertelen a méret ellenőrzése során.
    * Rögzített – az egycsomópontos fürt védelme nem sikerül, mert egy SCSI-eltérési probléma történt. 
    * A P2V Windows-fürtjének javított újbóli védelme sikertelen, ha a cél fürtözött lemezek jelen vannak. 
    
* Kijavítva: a feladat-visszavétel védelme során, ha a kijelölt fő célkiszolgáló nem ugyanazon az ESXi-kiszolgálón található, mint a védett forrásszámítógép (a továbbítási védelem során), akkor a vContinuum a feladat-visszavételi helyreállítás során nem a megfelelő fő célkiszolgáló megadását választja, és a helyreállítási művelet meghiúsul.

> [!NOTE]
> * A P2V-fürtök csak azokra a fizikai MSCS-fürtökre vonatkoznak, amelyek újonnan védettek az 5. Site Recovery felderítő frissítéssel. Ha a fürt javításait a védett P2V MSCS-fürtökön a régebbi frissítésekkel szeretné telepíteni, kövesse a [site Recovery Scout kibocsátási megjegyzésének](https://aka.ms/asr-scout-release-notes)12. szakaszában említett frissítési lépéseket.
> * Ha az ismételt védelem időpontjában ugyanazok a lemezek lesznek aktívak az egyes fürtcsomópontok esetében, mint amikor eredetileg védettek voltak, akkor a fizikai MSCS-fürtök újbóli védelme csak a meglévő céllemez újrafelhasználását követően végezhető el. Ha nem, akkor használja a [site Recovery Scout kibocsátási megjegyzésének](https://aka.ms/asr-scout-release-notes)12. szakaszának manuális lépéseit, hogy a cél oldali lemezeket a megfelelő adattár-útvonalra helyezze át újra a védelem során. Ha a MSCS-fürtöt a frissítés lépéseinek követése nélkül P2V módban szeretné újra védetté tenni, akkor egy új lemezt hoz létre a cél ESXi-kiszolgálón. Manuálisan kell törölnie a régi lemezeket az adattárból.
> * Ha a forrás-vagy SLES11 (bármely szervizcsomaggal rendelkező) kiszolgálót szabályosan újraindították, akkor manuálisan meg kell jelölnie a SLES11 a **legfelső szintű** lemez replikációs párokat. Nincs értesítés a CX felületéről. Ha nem jelöli meg a legfelső szintű lemezt az újraszinkronizáláshoz, észreveheti az adatok integritásával kapcsolatos problémákat.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 4. frissítés
A Scout Update 4 egy összesítő frissítés. Az 1. frissítés összes javítását tartalmazza a 3. frissítéshez, valamint az alább ismertetett új javításokat.

#### <a name="new-platform-support"></a>Új platform-támogatás

* Támogatás hozzáadva a vCenter/vSphere 6,0, 6,1 és 6,2 rendszerhez
* A támogatott Linux operációs rendszerek támogatása:
  * Red Hat Enterprise Linux (RHEL) 7,0, 7,1 és 7,2
  * CentOS 7,0, 7,1 és 7,2
  * Red Hat Enterprise Linux (RHEL) 6,8
  * CentOS 6,8

> [!NOTE]
> A RHEL/CentOS 7 64 bit  **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release. tar. gz** az alapszintű Scout GA csomag **InMage_Scout_Standard_8.0,1 GA.zip** van csomagolva. Töltse le a Scout GA-csomagot a portálról a tár létrehozása című témakörben leírtak szerint.

#### <a name="bug-fixes-and-enhancements"></a>Hibajavítások és fejlesztések

* Továbbfejlesztett leállítási kezelés a következő Linux operációs rendszerekhez és klónokhoz a nemkívánatos újraszinkronizálási problémák megelőzése érdekében:
    * Red Hat Enterprise Linux (RHEL) 6. x
    * Oracle Linux (OL) 6. x
* A Linux esetében az egyesített ügynök telepítési könyvtárában lévő összes mappa-hozzáférési engedély már csak a helyi felhasználóra korlátozódik.
* Windows rendszeren a közös elosztott konzisztencia-könyvjelzők kiadása során felmerülő időtúllépési hiba javítása a nagy terhelésű elosztott alkalmazásokban, például a SQL Server és a megosztási pont fürtökön.
* Egy naplóval kapcsolatos javítás a konfigurációs kiszolgáló alaptelepítőben.
* A VMware vCLI 6,0 letöltési hivatkozása hozzá lett adva a Windows fő célként megadott alaptelepítőhöz.
* További ellenőrzéseket és naplókat adtak hozzá a hálózati konfiguráció változásaihoz a feladatátvétel és a vész-helyreállítási gyakorlat során.
* Olyan probléma javítása, amely miatt a rendszer nem jelentett adatmegőrzési adatokat a konfigurációs kiszolgálónak.  
* Fizikai fürtök esetén olyan probléma javítását eredményezi, amely miatt a kötet átméretezése sikertelen volt a vContinuum varázslóban, a forrás kötetének csökkentésekor.
* Egy, a következő hibával meghiúsult fürt védelmére vonatkozó probléma: "nem sikerült megtalálni a lemez aláírását", ha a fürtlemez PRDM lemez.
* Egy cxps-átviteli kiszolgáló összeomlása, amelyet a tartományon kívüli kivétel okoz.
* A kiszolgáló neve és az IP-cím oszlopok mostantól átméretezhetők a vContinuum varázsló **leküldéses telepítés** lapján.
* Az RX API továbbfejlesztései:
  * A legújabb elérhető általános konzisztencia-pontok mostantól elérhetők (csak a garantált címkék).
  * A kapacitás és a szabad terület adatai megjelennek az összes védett eszközön.
  * A forrás-kiszolgáló Scout-illesztőprogramjának állapota elérhető.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** alapcsomag a következőket tartalmazhatja:
>     * Egy frissített konfigurációs kiszolgáló alaptelepítője (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Egy Windows fő célként megadott alap telepítője (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * Az összes új telepítéshez használja az új konfigurációs kiszolgálót és a Windows fő célként szolgáló GA BITS szolgáltatást.
> * A 4. frissítés közvetlenül alkalmazható a 8.0.1 GA-on.
> * A konfigurációs kiszolgáló és az RX-frissítések nem állíthatók vissza az alkalmazása után.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 3. frissítés

Az összes Site Recovery kumulatív frissítés. A 3. frissítés az 1. és a 2. frissítés összes javítását tartalmazza. A 3. frissítés közvetlenül alkalmazható a 8.0.1 GA szolgáltatásban. A konfigurációs kiszolgáló és az RX-frissítések nem állíthatók vissza az alkalmazása után.

#### <a name="bug-fixes-and-enhancements"></a>Hibajavítások és fejlesztések
A 3. frissítés a következő problémákat javítja:

* A konfigurációs kiszolgáló és az RX nincs regisztrálva a tárolóban, amikor a proxy mögött vannak.
* Az a szám, ameddig a helyreállítási pont célkitűzése (RPO) nem érhető el, nem frissül az állapotjelentés.
* A konfigurációs kiszolgáló nem szinkronizál az RX-sel, ha az ESX-hardver adatai vagy a hálózati adatok nem tartalmaznak UTF-8 karaktereket.
* A Windows Server 2008 R2 rendszerű tartományvezérlők nem indulnak el a helyreállítás után.
* Az offline szinkronizálás nem a várt módon működik.
* A virtuális gép feladatátvétele után a replikációs pár törlése hosszú ideig nem halad előre a konfigurációs kiszolgáló konzolján. A felhasználók nem tudják befejezni a feladat-visszavételi vagy folytatási műveletet.
* A konzisztencia-feladatokhoz tartozó összes pillanatkép-művelet optimalizálva lett, így csökkenthető az alkalmazások leválasztása, például SQL Server ügyfelek.
* A konzisztencia-eszköz (VACP.exe) teljesítménye javult. A pillanatképek Windows rendszeren való létrehozásához szükséges memóriahasználat csökkentve.
* A leküldéses telepítési szolgáltatás összeomlik, ha a jelszó hosszabb, mint 16 karakter.
* a vContinuum nem vizsgálja és kéri az új vCenter hitelesítő adatokat, amikor a hitelesítő adatok módosulnak.
* Linux rendszeren a fő cél gyorsítótár-kezelő (cachemgr) nem tölti le a fájlokat a folyamat-kiszolgálóról. Ez a replikálási pár szabályozását eredményezi.
* Ha a fizikai feladatátvevő fürt (MSCS) lemezének megrendelése nem azonos az összes csomóponton, a replikálás nem a fürt egyes köteteihez van beállítva. A javítás kihasználása érdekében a fürtöt újra kell védelemmel ellátni.  
* Az SMTP-funkciók nem a várt módon működnek, az RX a Scout 7,1-ből a Scout 8.0.1-be való frissítésekor.
* További statisztikák lettek hozzáadva a naplóban a visszaállítási művelethez, hogy nyomon kövessék a befejezéshez szükséges időt.
* A Linux operációs rendszerek támogatása a forráskiszolgálón:
  * Red Hat Enterprise Linux (RHEL) 6 7. frissítés
  * CentOS 6 7. frissítés
* A konfigurációs kiszolgáló és az RX-konzol mostantól a pároshoz tartozó értesítéseket jeleníti meg, amelyek a bitkép üzemmódba kerülnek.
* A következő biztonsági javítások lettek hozzáadva az RX-ben:
    * Hitelesítés engedélyezése a paraméter-módosításon keresztül: korlátozott hozzáférés a nem megfelelő felhasználókhoz.
    * Helyek közötti kérelmek hamisítása: a Page-token koncepciója implementálva lett, és minden oldal esetében véletlenszerűen jön létre. Ez azt jelenti, hogy ugyanahhoz a felhasználóhoz csak egyetlen bejelentkezési példány van, és az oldal frissítése nem működik. Ehelyett a rendszer átirányítja az irányítópultra.
    * Rosszindulatú fájl feltöltése: a fájlok meghatározott kiterjesztésekre korlátozódnak: z, AIFF, ASF, AVI, BMP, CSV, doc, docx, Fla, FLV, GIF, GZ, gzip, JPEG, jpg, log, Mid, MOV, MP3, MP4, MPC, MPEG, MPG, ODS, ODT, PDF, PNG, ppt, PPTX, PXD, QT, RAM, rar, RM, RMI, RMVB, RTF, SDC, sitd, SWF, sxc, sxw, Tar, TGZ, TIF, TIFF, txt, VSD, WAV, WMA, WMV, xls , xlsx, XML és zip.
    * Állandó helyközi parancsfájlkezelés: a bemeneti érvényesítések hozzá lettek adva.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 2. frissítés (03Dec15 frissítése)

A 2. frissítésben szereplő javítások a következők:

* **Konfigurációs kiszolgáló**: olyan problémák, amelyek meggátolták a 31 napos ingyenes mérési funkció működését a várt módon, amikor a konfigurációs kiszolgáló regisztrálva van Azure site Recovery-tárolóban.
* **Egyesített ügynök**: javítsa ki az 1. frissítés egyik hibáját, amelynek eredményeként a frissítés nem lett telepítve a fő célkiszolgálón, a 8,0-es verzióról a 8.0.1-re történő frissítés során.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 1. frissítés
Az 1. frissítés a következő hibajavításokat és új funkciókat tartalmazza:

* a kiszolgálónkénti példányok által biztosított ingyenes védelem 31 nap. Ez lehetővé teszi a funkciók tesztelését, vagy egy próba-koncepció beállítását.
* A kiszolgálón lévő összes művelet, beleértve a feladatátvételt és a feladat-visszavételt, az első 31 napra ingyenes. Az idő akkor kezdődik, amikor a kiszolgálót először Site Recovery Scout védi. A 32nd naptól kezdve minden védett kiszolgáló a Site Recovery védelemhez használt standard szintű példány díjszabása alapján történik az ügyfél által birtokolt webhelyre.
* A jelenleg felszámított védett kiszolgálók száma bármikor elérhető az **irányítópulton** a tárolóban.
* Támogatás lett hozzáadva a vSphere Command-Line Interface (vCLI) 5,5 2. frissítéséhez.
* A következő linuxos operációs rendszerek támogatása lett hozzáadva a forráskiszolgálón:
    * RHEL 6, 6. frissítés
    * RHEL 5, 11. frissítés
    * CentOS 6, 6. frissítés
    * CentOS 5 frissítés 11
* Hibajavítások a következő problémák megoldásához:
  * A tár regisztrációja sikertelen a konfigurációs kiszolgáló vagy az RX-kiszolgáló esetében.
  * A fürtözött kötetek nem a várt módon jelennek meg, amikor a fürtözött virtuális gépek újra vannak védve, ahogy folytatják.
  * A feladat-visszavétel meghiúsul, ha a fő célkiszolgáló egy másik ESXi-kiszolgálón található a helyszíni éles virtuális gépekről.
  * A konfigurációs fájl engedélyei a 8.0.1-re való frissítéskor változnak. Ez a változás hatással van a védelemre és a műveletekre.
  * Az újraszinkronizálási küszöb nem a várt módon van kikényszerítve, ami inkonzisztens replikációs működést okoz.
  * A RPO beállítások nem jelennek meg megfelelően a konfigurációs kiszolgáló konzolján. A tömörítetlen adatérték helytelenül jeleníti meg a tömörített értéket.
  * Az eltávolítási művelet nem a várt módon törlődik a vContinuum varázslóban, és a replikálás nem törlődik a konfigurációs kiszolgáló konzolról.
  * A vContinuum varázslóban a lemez automatikusan nem lesz kiválasztva, ha a MSCS virtuális gépek védelme során a lemez nézetben a **részletek** elemre kattint.
  * A fizikai – virtuális (P2V) forgatókönyvben a szükséges HP-szolgáltatások (például a CIMnotify és a CqMgHost) nem helyezhetők át manuálisra a virtuális gép helyreállításában. Ez a probléma további rendszerindítási időt eredményez.
  * A Linux rendszerű virtuális gépek védelme meghiúsul, ha a fő célkiszolgálón több mint 26 lemez van.

