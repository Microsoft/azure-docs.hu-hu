---
title: Az Azure-beli SAP HANA operációs rendszerének frissítése (nagyméretű példányok) | Microsoft Docs
description: Operációs rendszer verziófrissítésének elvégzése SAP HANA Azure-ban (nagyméretű példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f77c16f16ddac01329a8315893021767a4120295
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179238"
---
# <a name="operating-system-upgrade"></a>Operációs rendszer frissítése
Ez a dokumentum ismerteti az operációs rendszer frissítésének részleteit a HANA nagyméretű példányain.

>[!NOTE]
>Az operációs rendszer verziófrissítése az ügyfél felelőssége, a Microsoft üzemeltetési támogatási szolgálata végigvezeti Önt a frissítés során megtekinthető kulcsfontosságú területeken. A frissítés megtervezése előtt tekintse meg az operációs rendszer gyártóját is.

A HLI egység kiépítés során a Microsoft Operations csapata telepíti az operációs rendszert.
Az idő múlásával meg kell őriznie az operációs rendszert (például: javítás, hangolás, frissítés stb.) a HLI egységen.

Mielőtt megkezdené az operációs rendszer jelentős módosításait (például a SP1 verzióról SP2-re), forduljon a Microsoft Operations csapathoz egy támogatási jegy megnyitásával.

Belefoglalás a jegybe:

* A HLI-előfizetés azonosítója.
* A kiszolgáló neve.
* Az alkalmazni kívánt javítási szint.
* A módosítás megtervezésének dátuma. 

Javasoljuk, hogy a kívánt frissítés előtt legalább egy héttel nyissa meg ezt a jegyet, amely lehetővé teszi, hogy a opration csapata tudja a szükséges belső vezérlőprogram-verziót.

A különböző Linux-verziókkal rendelkező különböző SAP HANA verziók támogatási mátrixa: [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

## <a name="known-issues"></a>Ismert problémák

A frissítés során néhány gyakori ismert probléma a következő:
- A II. típusú SKU-ban az operációs rendszer frissítése után törlődnek a szoftver Foundation szoftverei (a strukturális alapok). Az operációs rendszer frissítése után újra kell telepítenie a kompatibilis strukturális rendszereket.
- Az Ethernet-kártya illesztőprogramjai (ENIC és FNIC) visszaállnak a régebbi verzióra. A frissítés után újra kell telepítenie az illesztőprogramok kompatibilis verzióját.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA nagyméretű példány (I) ajánlott konfiguráció

Az operációs rendszer konfigurációja a javítások, a Rendszerfrissítések és az ügyfelek által végzett módosítások miatt idővel eltérhet az ajánlott beállításoktól. A Microsoft emellett a meglévő rendszerekhez szükséges frissítéseket is azonosítja, így biztosítva, hogy optimálisan legyenek konfigurálva a legjobb teljesítményhez és rugalmassághoz. A következő utasítások a hálózati teljesítményre, a rendszer stabilitására és az optimális HANA-teljesítményre vonatkozó javaslatokat ismertetik.

### <a name="compatible-enicfnic-driver-versions"></a>Kompatibilis eNIC-/fNIC-illesztőprogram-verziók
  A megfelelő hálózati teljesítmény és a rendszer stabilitásának biztosítása érdekében javasoljuk, hogy a eNIC és a fNIC illesztőprogramok operációs rendszerre vonatkozó megfelelő verzióját a következő kompatibilitási táblázatban látható módon telepítse. A kiszolgálók a kompatibilis verziókkal rendelkező ügyfeleknek érkeznek. Bizonyos esetekben az operációs rendszer/kernel javítása során az illesztőprogramok visszaállíthatók az alapértelmezett illesztőprogram-verzióra. Ellenőrizze, hogy az illesztőprogram megfelelő verziója fut-e az operációs rendszer/kernel javítási műveleteinek futtatása után.
       
      
  |  Operációs rendszer szállítója    |  Operációs rendszer csomagjának verziója     |  Belső vezérlőprogram verziója  |  eNIC-illesztőprogram |  fNIC-illesztőprogram | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3 h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3 h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3 i           |  4.0.0.6     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3 i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3 i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP5            |   3.2.3 i           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7,2               |   3.1.3 h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7,6               |   3.2.3 i           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7,6               |   b. 4.1.1.           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Az illesztőprogram verziófrissítésére és a régi rpm-csomagok tisztítására vonatkozó parancsok

#### <a name="command-to-check-existing-installed-drivers"></a>A meglévő telepített illesztőprogramok vizsgálatára szolgáló parancs
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>Meglévő eNIC/fNIC rpm törlése
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>Az ajánlott eNIC-/fNIC-illesztőprogram-csomagok telepítése
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>A telepítés megerősítésére szolgáló parancsok
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>A eNIC/fNIC illesztőprogramok telepítésének lépései az operációs rendszer frissítése során

* Operációs rendszer verziójának frissítése
* Régi rpm-csomagok eltávolítása
* Kompatibilis eNIC-/fNIC-illesztőprogramok telepítése telepített operációsrendszer-verzióként
* Rendszer újraindítása
* Újraindítás után keresse meg a eNIC/fNIC verzióját.


### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB-frissítési hiba
Az Azure HANA nagyméretű példányain (I. típus) lévő SAP nem indítható állapotban lehet a frissítés után. Az alábbi eljárás javítja ezt a problémát.
#### <a name="execution-steps"></a>Végrehajtási lépések


*   `multipath -ll`Parancs végrehajtása.
*   Szerezze be a logikai egység AZONOSÍTÓját, amelynek mérete körülbelül 50G, vagy használja a parancsot: `fdisk -l | grep mapper`
*   `/etc/default/grub_installdevice`Fájl frissítése a sorral `/dev/mapper/<LUN ID>` . Példa:/dev/Mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>A LUN-azonosító a kiszolgálóról a kiszolgálóra változik.


### <a name="disable-edac"></a>EDAC letiltása 
   A hibák észlelése és javítása (EDAC) modul segítséget nyújt a memóriahiba észlelésében és kijavításában. A SAP HANA on Azure Large Instances (I. típus) mögöttes hardver azonban már ugyanazt a funkciót is végrehajtja. Ha ugyanaz a funkció engedélyezve van a hardver-és operációs rendszer (OS) szintjén, az ütközéseket okozhat, és a kiszolgáló alkalmi, nem tervezett leállásához vezethet. Ezért javasoljuk, hogy tiltsa le a modult az operációs rendszerből.

#### <a name="execution-steps"></a>Végrehajtási lépések

* Ellenőrizze, hogy a EDAC modul engedélyezve van-e. Ha az alábbi parancs kimenetet ad vissza, az azt jelenti, hogy a modul engedélyezve van. 
```
lsmod | grep -i edac 
```
* Tiltsa le a modulokat a következő sorok hozzáfűzésével a fájlhoz: `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
A módosítások érvénybe lépéséhez újraindítás szükséges. Futtassa `lsmod` a parancsot, és ellenőrizze, hogy a modul nem található-e a kimenetben.

### <a name="kernel-parameters"></a>Kernel-paraméterek
   Győződjön meg arról, hogy a, a, a és a megfelelő beállítás `transparent_hugepage` `numa_balancing` `processor.max_cstate` `ignore_ce` `intel_idle.max_cstate` van alkalmazva.

* intel_idle intel_idle.max_cstate = 1
* processor.max_cstate = 1
* transparent_hugepage = soha
* numa_balancing = letiltás
* MCE = ignore_ce

#### <a name="execution-steps"></a>Végrehajtási lépések

* Adja hozzá ezeket a paramétereket a `GRB_CMDLINE_LINUX` fájl sorához `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Hozzon létre egy új grub-fájlt.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Rendszer újraindítása.


## <a name="next-steps"></a>Következő lépések
- Tekintse át a [biztonsági mentést és a visszaállítást](hana-overview-high-availability-disaster-recovery.md) az operációs rendszer biztonsági mentése I SKU osztályban.
- Tekintse át az [operációs rendszer biztonsági másolatát](os-backup-type-ii-skus.md) , amely a 2. típusú SKU osztályhoz tartozó 3. típusú bélyegzőket használja.
