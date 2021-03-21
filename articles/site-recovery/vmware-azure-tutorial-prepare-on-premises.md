---
title: Felkészülés a VMware virtuális gép vész-helyreállítására Azure Site Recovery
description: Ismerje meg, hogyan készíthet elő VMware-kiszolgálókat az Azure-ba irányuló vészhelyreállításhoz az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b8fd34c8f1e3a32a8252074941a49d61aa540207
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86134910"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Helyszíni VMware-kiszolgálók előkészítése az Azure-ba irányuló vészhelyreállításához

Ez a cikk azt ismerteti, hogyan készítse elő a helyszíni VMware-kiszolgálókat az Azure-ba való vész-helyreállításra az [Azure site Recovery](site-recovery-overview.md) -szolgáltatások használatával. 

Ez az oktatóanyag a második rész abban a sorozatban, amely bemutatja, hogyan állíthat be Azure-ba irányuló vészhelyreállítást helyszíni VMware virtuális gépekhez. Az első oktatóanyagban [konfiguráltuk a VMware vészhelyreállításhoz szükséges Azure-összetevőket](tutorial-prepare-azure.md).


Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Készítse elő a fiókot a vCenter-kiszolgálón vagy a vSphere ESXi-gazdagépen a virtuális gépek felderítésének automatizálásához.
> * Készítsen elő egy fiókot a mobilitási szolgáltatás VMware virtuális gépeken való automatikus telepítéséhez.
> * Tekintse át a VMware-kiszolgáló és a virtuális gép követelményeit és támogatását.
> * Felkészülés az Azure-beli virtuális gépekhez való csatlakozásra a feladatátvételt követően.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes utasításokért tekintse át a Site Recovery tartalomjegyzékének útmutató című cikkét.

## <a name="before-you-start"></a>Előkészületek

Győződjön meg arról, hogy előkészítette az Azure-t az [ebben a sorozatban szereplő első oktatóanyagban](tutorial-prepare-azure.md)leírtak szerint.

## <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- A virtuális gépek automatikus felderítése. Szükség van legalább egy csak olvasási jogokat biztosító fiókra.
- Replikáció, feladatátvétel és feladat-visszavétel vezénylése. Szükség van egy fiókra, amely képes műveleteket futtatni, mint például lemezek létrehozása és eltávolítása, valamint virtuális gépek működtetése.

Hozza létre a fiókot az alábbiak szerint:

1. Dedikált fiók használatához hozzon létre egy szerepkört a vCenter-szinten. Adjon meg egy nevet, például: **Azure_Site_Recovery**.
2. Rendelje hozzá a szerepkörhöz az engedélyeket az alábbi táblázatban összefoglaltaknak megfelelően.
3. Hozzon létre egy felhasználót a vCenter-kiszolgálón vagy vSphere-gazdagépen. Rendelje hozzá a szerepkört a felhasználóhoz.

### <a name="vmware-account-permissions"></a>A VMware-fiók engedélyei

**Feladat** | **Szerepkör/Engedélyek** | **Részletek**
--- | --- | ---
**Virtuális gépek felderítése** | Legalább egy csak olvasási jogosultsággal rendelkező felhasználó<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **nincs hozzáférési** szerepkört a **propagálás a gyermek** objektumhoz (vSphere gazdagépek, adattárolók, virtuális gépek és hálózatok).
**Teljes replikáció, feladatátvétel, feladat-visszavétel** |  Hozzon létre egy szerepkört (Azure_Site_Recovery) a szükséges engedélyekkel, majd rendelje hozzá a szerepkört egy VMware-felhasználóhoz vagy -csoporthoz<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = Azure_Site_Recovery<br/><br/> Adattároló -> Terület lefoglalása, adattároló böngészése, alacsony szintű fájlműveletek, fájl eltávolítása, virtuális gépek fájljainak frissítése<br/><br/> Hálózat -> Hálózat hozzárendelése<br/><br/> Erőforrás -> Virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép migrálása, bekapcsolt virtuális gép migrálása<br/><br/> Feladatok -> Feladat létrehozása, feladat frissítése<br/><br/> Virtuális gép -> Konfiguráció<br/><br/> Virtuális gép -> Használat -> Kérdés megválaszolása, eszközkapcsolat, CD-adathordozó konfigurálása, hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuális gép -> Leltár -> Létrehozás, regisztrálás, regisztráció törlése<br/><br/> Virtuális gép -> Üzembe helyezés -> Virtuális gép letöltésének engedélyezése, virtuálisgépfájlok feltöltésének engedélyezése<br/><br/> Virtuális gép -> Pillanatképek -> Pillanatképek eltávolítása | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **nincs hozzáférési** szerepkört a **propagálás a gyermek** objektumhoz (vSphere gazdagépek, adattárolók, virtuális gépek és hálózatok).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatásnak telepítve kell lennie a replikálni kívánt gépeken. A Site Recovery képes ennek a szolgáltatásnak a leküldéses telepítésére, ha engedélyezi a replikációt egy gépen, vagy telepíthet manuálisan, illetve telepítési eszközökkel.

- Ebben az oktatóanyagban leküldéses telepítéssel telepítjük a mobilitási szolgáltatást.
- Ehhez a leküldéses telepítéshez elő kell készítenie egy fiókot, hogy a Site Recovery használhassa azt a virtuális géphez való hozzáféréshez. Ezt a fiókot kell megadnia, amikor beállítja a vészhelyreállítást az Azure-konzolon.

Készítse elő a fiókot az alábbiak szerint:

Készítsen elő egy tartományi vagy helyi fiókot, amely rendelkezik a virtuális gépre való telepítéshez szükséges engedélyekkel.

- **Windows rendszerű virtuális gépek**: Ha Windows rendszerű virtuális gépekre kíván telepíteni, de nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlést a helyi számítógépen. Ehhez adja hozzá a **LocalAccountTokenFilterPolicy** DWORD bejegyzést 1 értékkel a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** helyen található beállításjegyzékhez.
- **Linux rendszerű virtuális gépek**: Ha Linux rendszerű virtuális gépekre kíván telepíteni, készítsen elő egy rendszergazdai fiókot a Linux-forráskiszolgálón.


## <a name="check-vmware-requirements"></a>A VMware követelményeinek ellenőrzése

Győződjön meg róla, hogy a VMware-kiszolgálók és -virtuálisgépek megfelelnek az alábbi követelményeknek.

1. [Ellenőrizze](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) a VMware-kiszolgáló követelményeit.
2. Linux rendszerű virtuális gépek esetén [ellenőrizze](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) a fájlrendszerre és tárolásra vonatkozó követelményeket. 
3. Ellenőrizze a helyszíni [hálózat](vmware-physical-azure-support-matrix.md#network) és [tárolás](vmware-physical-azure-support-matrix.md#storage) támogatását. 
4. Ellenőrizze az Azure támogatott [hálózati](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [tárolási](vmware-physical-azure-support-matrix.md#azure-storage) és [számítási](vmware-physical-azure-support-matrix.md#azure-compute) lehetőségeit a feladatátvételt követően.
5. Az Azure-ba replikált helyszíni virtuális gépeknek meg kell felelniük az [Azure virtuális gépekre vonatkozó feltételeinek](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
6. A Linux rendszerű virtuális gépeken az eszköz nevének vagy a csatlakoztatási pont nevének egyedinek kell lennie. Győződjön meg arról, hogy nincs két eszköz/csatlakoztatási pont azonos névvel. Vegye figyelembe, hogy a név nem a kis-és nagybetűket megkülönböztető Például két eszköznek a _device1_ és a _device1_ azonos virtuális géphez való elnevezése nem engedélyezett.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Előfordulhat, hogy a feladatátvétel után csatlakozni szeretne az Azure-beli virtuális gépekhez a helyszíni hálózatról.

Ha a feladatátvételt követően RDP segítségével szeretne kapcsolódni a Windows virtuális gépekhez, tegye a következőket:

- **Internet-hozzáférés**. Feladatátvétel előtt engedélyezze az RDP-t a helyszíni virtuális gépen. Ellenőrizze, hogy a **Nyilvános** profilnál felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows-tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.
- **Helyek közötti VPN-elérés**:
    - Feladatátvétel előtt engedélyezze az RDP-t a helyszíni gépen.
    - Az RDP-t engedélyezni kell a **Windows tűzfal**  ->  **engedélyezett alkalmazásaiban és szolgáltatásaiban** a **tartományok és magánhálózatok** számára.
    - Ellenőrizze, hogy az operációs rendszer tárolóhálózati szabályzata **OnlineAll** értékre van-e állítva. [További információ](https://support.microsoft.com/kb/3031135).
- A virtuális gépen nem lehetnek függőben lévő Windows-frissítések a feladatátvétel elindításakor. Ha vannak, akkor nem fog tudni bejelentkezni a virtuális gépre, amíg a frissítés be nem fejeződik.
- A feladatátvételt követően ellenőrizze a **Rendszerindítási diagnosztika** részt a Windows Azure virtuális gépen a virtuális gép képernyőképének megtekintéséhez. Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális gép, majd tekintse át a [hibaelhárítási tippeket](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Ha a feladatátvételt követően SSH segítségével szeretne kapcsolódni a Linux virtuális gépekhez, tegye a következőket:

- A helyszíni gépen a feladatátvétel előtt ellenőrizze, beállította-e, hogy a Secure Shell szolgáltatás rendszerindításkor automatikusan elinduljon.
- Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.
- A feladatátvételt követően engedélyezze az SSH-porthoz beérkező kapcsolatokat az Azure-beli virtuális gépen a feladatátviteli virtuális gépen lévő hálózati biztonsági csoport szabályaihoz és az Azure-alhálózathoz, amelyhez csatlakozik.
- [Nyilvános IP-cím hozzáadása](./site-recovery-monitor-and-troubleshoot.md) a virtuális gép számára.
- Ellenőrizheti a **Rendszerindítási diagnosztika** részt a virtuális gép képernyőképének megtekintéséhez.


## <a name="failback-requirements"></a>Feladat-visszavételre vonatkozó követelmények
Ha azt tervezi, hogy visszaadja a feladatokat a helyszíni helynek, a feladat- [visszavételhez számos előfeltételt](vmware-azure-reprotect.md#before-you-begin)kell megadnia. Ezeket most is előkészítheti, de nem szükséges. Az Azure-ba történő feladatátvétel után is előkészítheti a feladatokat.



## <a name="next-steps"></a>Következő lépések

Állítsa be a vész-helyreállítást. Több virtuális gép replikálásakor tervezze meg a kapacitást.
> [!div class="nextstepaction"]
> Vész [-helyreállítás beállítása az Azure-ba VMWare virtuális gépekhez](vmware-azure-tutorial.md) 
>  [Végezze el a kapacitás megtervezését](site-recovery-deployment-planner.md).
