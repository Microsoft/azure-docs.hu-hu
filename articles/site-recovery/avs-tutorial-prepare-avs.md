---
title: Azure VMware-megoldás előkészítése a Azure Site Recovery vész-helyreállításra
description: Ismerje meg, hogyan készítheti elő az Azure VMware megoldás-kiszolgálókat az Azure-ba való vész-helyreállításra az Azure Site Recovery szolgáltatás használatával.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 8e77ede7b04c95bfd6b6b8f660c8d811e7434c0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395444"
---
# <a name="prepare-azure-vmware-solution-for-disaster-recovery-to-azure-site-recovery"></a>Azure VMware-megoldás előkészítése a Azure Site Recovery vész-helyreállításra

Ez a cikk azt ismerteti, hogyan lehet előkészíteni az Azure VMware megoldás-kiszolgálókat az Azure-ba való vész-helyreállításra az [Azure site Recovery](site-recovery-overview.md) szolgáltatások használatával. 

Ez egy sorozat második oktatóanyaga, amely bemutatja, hogyan állíthatja be a vész-helyreállítást az Azure-ba az Azure VMware-megoldás virtuális gépei számára. Az első oktatóanyagban az Azure VMware-megoldás vész-helyreállításához szükséges [Azure-összetevőket állítjuk be](avs-tutorial-prepare-azure.md) .


Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Készítse elő a fiókot a vCenter-kiszolgálón vagy a vSphere ESXi-gazdagépen a virtuális gépek felderítésének automatizálásához.
> * Készítsen elő egy fiókot a mobilitási szolgáltatás VMware virtuális gépeken való automatikus telepítéséhez.
> * Tekintse át a VMware-kiszolgáló és a virtuális gép követelményeit és támogatását.
> * Felkészülés az Azure-beli virtuális gépekhez való csatlakozásra a feladatátvételt követően.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes utasításokért tekintse át a Site Recovery tartalomjegyzékének útmutató című cikkét.

## <a name="before-you-start"></a>Előkészületek

Győződjön meg arról, hogy előkészítette az Azure-t az [ebben a sorozatban szereplő első oktatóanyagban](avs-tutorial-prepare-azure.md)leírtak szerint.

## <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

Site Recovery az Azure VMware megoldás-kiszolgálókhoz való hozzáférésre van szükség a következőhöz:

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

1. Ellenőrizze az Azure VMware Solution [szoftver verzióját](../azure-vmware/concepts-private-clouds-clusters.md#vmware-software-versions).
2. A [VMware-kiszolgáló követelményeinek](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers)ellenőrzése.
3. Linux rendszerű virtuális gépek esetén [ellenőrizze](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) a fájlrendszerre és tárolásra vonatkozó követelményeket. 
4. A [hálózati](vmware-physical-azure-support-matrix.md#network) és [tárolási](vmware-physical-azure-support-matrix.md#storage) támogatás ellenõrzése. 
5. Ellenőrizze az Azure támogatott [hálózati](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [tárolási](vmware-physical-azure-support-matrix.md#azure-storage) és [számítási](vmware-physical-azure-support-matrix.md#azure-compute) lehetőségeit a feladatátvételt követően.
6. Az Azure-ba replikált Azure-beli VMware-megoldásoknak meg kell felelniük az Azure-beli [virtuális gépek követelményeinek](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
7. A Linux rendszerű virtuális gépeken az eszköz nevének vagy a csatlakoztatási pont nevének egyedinek kell lennie. Győződjön meg arról, hogy nincs két eszköz/csatlakoztatási pont azonos névvel. Vegye figyelembe, hogy a név nem a kis-és nagybetűket megkülönböztető Például két eszköznek a _device1_ és a _device1_ azonos virtuális géphez való elnevezése nem engedélyezett.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

A feladatátvételt követően előfordulhat, hogy az Azure-beli VMware-megoldás hálózatáról szeretne csatlakozni az Azure-beli virtuális gépekhez.

Ha a feladatátvételt követően RDP segítségével szeretne kapcsolódni a Windows virtuális gépekhez, tegye a következőket:

- **Internet-hozzáférés**. A feladatátvétel előtt engedélyezze az RDP-t az Azure VMware megoldás virtuális gépen a feladatátvétel előtt. Ellenőrizze, hogy a **Nyilvános** profilnál felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows-tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.
- **Helyek közötti VPN-elérés**:
    - A feladatátvétel előtt engedélyezze az RDP-t az Azure VMware megoldás virtuális gépén.
    - Az RDP-t engedélyezni kell a **Windows tűzfal**  ->  **engedélyezett alkalmazásaiban és szolgáltatásaiban** a **tartományok és magánhálózatok** számára.
    - Ellenőrizze, hogy az operációs rendszer tárolóhálózati szabályzata **OnlineAll** értékre van-e állítva. [További információk](https://support.microsoft.com/kb/3031135).
- A virtuális gépen nem lehetnek függőben lévő Windows-frissítések a feladatátvétel elindításakor. Ha vannak, akkor nem fog tudni bejelentkezni a virtuális gépre, amíg a frissítés be nem fejeződik.
- A feladatátvételt követően ellenőrizze a **Rendszerindítási diagnosztika** részt a Windows Azure virtuális gépen a virtuális gép képernyőképének megtekintéséhez. Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális gép, majd tekintse át a [hibaelhárítási tippeket](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Ha a feladatátvételt követően SSH segítségével szeretne kapcsolódni a Linux virtuális gépekhez, tegye a következőket:

- Az Azure VMware megoldás virtuális gépen a feladatátvétel előtt győződjön meg arról, hogy a Secure Shell szolgáltatás automatikusan elindul a rendszerindításkor.
- Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.
- A feladatátvételt követően engedélyezze az SSH-porthoz beérkező kapcsolatokat az Azure-beli virtuális gépen a feladatátviteli virtuális gépen lévő hálózati biztonsági csoport szabályaihoz és az Azure-alhálózathoz, amelyhez csatlakozik.
- [Nyilvános IP-cím hozzáadása](./site-recovery-monitor-and-troubleshoot.md) a virtuális gép számára.
- Ellenőrizheti a **Rendszerindítási diagnosztika** részt a virtuális gép képernyőképének megtekintéséhez.


## <a name="failback-requirements"></a>Feladat-visszavételre vonatkozó követelmények
Ha azt tervezi, hogy visszaadja az Azure VMware-megoldás felhőjét, a feladat- [visszavételhez számos előfeltételt](avs-tutorial-reprotect.md#before-you-begin)kell megadnia. Ezeket most is előkészítheti, de nem szükséges. Az Azure-ba történő feladatátvétel után is előkészítheti a feladatokat.




## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Vész-helyreállítás beállítása](avs-tutorial-replication.md)
- Ha több virtuális gépet replikál, [hajtsa végre a kapacitás megtervezését](site-recovery-deployment-planner.md).
