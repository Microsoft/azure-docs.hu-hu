---
title: A StorSimple 8000 Series eszköz konfigurációjának módosítása | Microsoft Docs
description: Ismerteti, hogyan használható a StorSimple Eszközkezelő szolgáltatás a már telepített StorSimple-eszközök újrakonfigurálásához.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 5eb5c351462279fe5c4f790e052f73201d211cfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961175"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple-eszköz konfigurációjának módosításához

## <a name="overview"></a>Áttekintés

A **Settings (beállítások** ) panel Azure Portal **eszközbeállítások** szakasza tartalmazza az összes olyan eszköz paramétert, amelyet egy StorSimple Eszközkezelő szolgáltatás által felügyelt StorSimple-eszközön konfigurálhat újra. Ez az oktatóanyag azt ismerteti, hogyan használható a **Settings (beállítások** ) panel a következő eszköz szintű feladatok végrehajtásához:

* Eszköz rövid nevének módosítása
* Eszköz időbeállításainak módosítása
* Másodlagos DNS-t rendeljen hozzá
* Hálózati adapterek módosítása
* IP-címek cseréje vagy újbóli társítása

## <a name="modify-device-friendly-name"></a>Eszköz rövid nevének módosítása

A Azure Portal segítségével módosíthatja az eszköz nevét, és hozzárendelheti a választott egyedi felhasználóbarát nevet. Az eszköz **általános beállítások** paneljén módosíthatja az eszköz rövid nevét. A felhasználóbarát név bármilyen karaktert tartalmazhat, és legfeljebb 64 karakter hosszú lehet.

> [!NOTE] 
> Az eszköz nevét csak az eszköz telepítésének befejeződése után módosíthatja a Azure Portal. A minimális eszköz telepítésének befejezése után az eszköz neve nem módosítható.

![Az eszköz neve az általános beállításokban](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

A StorSimple Eszközkezelő szolgáltatáshoz csatlakoztatott StorSimple-eszköz alapértelmezett nevet kap. Az alapértelmezett név általában az eszköz sorozatszámát tükrözi. Például a 15 karakter hosszúságú alapértelmezett eszköznév (például 8600-SHX0991003G44HT) a következőket jelzi:

* **8600**  – az eszköz modelljét jelzi.
* **SHX** – a gyártási helyet jelzi.
* **0991003** – egy adott terméket jelez.
* **G44HT**– az utolsó 5 számjegy növekszik az egyedi sorozatszámok létrehozásához. Lehet, hogy ez nem szekvenciális készlet.

## <a name="modify-device-description"></a>Eszköz leírásának módosítása

Az eszköz leírásának módosításához használja az eszköz **általános beállítások** paneljét.

![Az eszköz leírása az általános beállítások között](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Az eszköz leírása általában segít azonosítani a tulajdonost és az eszköz fizikai helyét. A Leírás mezőben a 256 karakternél rövidebbnek kell szerepelnie.

## <a name="modify-time-settings"></a>Időbeli beállítások módosítása

Az eszköznek szinkronizálnia kell az időt ahhoz, hogy hitelesíteni lehessen a felhőalapú tárolási szolgáltatóval. Használja az eszköz **általános beállítások** paneljét az eszköz időbeállításainak módosításához.

![Az eszköz leírása az általános beállításokban 2](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Válassza ki az időzónát a legördülő listából. Akár két hálózati időprotokoll (NTP) kiszolgálót is megadhat:

 - **Elsődleges NTP-kiszolgáló** – a konfiguráció megadása kötelező, ha a Windows PowerShell StorSimple-bővítménye használatával konfigurálja az eszközt. Az alapértelmezett Windows Server- **Time.Windows.com** az NTP-kiszolgálóként is megadhatja. Az elsődleges NTP-kiszolgáló konfigurációját a Azure Portalon keresztül tekintheti meg, de a Windows PowerShell felületét kell használnia a módosításhoz. Az `Set-HcsNTPClientServerAddress` eszköz elsődleges NTP-kiszolgálójának módosításához használja a parancsmagot. További információkért keresse fel a [set-HcsNTPClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) parancsmag szintaxisát.

- **Másodlagos NTP-kiszolgáló** – a konfiguráció nem kötelező. A portálon másodlagos NTP-kiszolgálót is beállíthat.

Az NTP-kiszolgáló konfigurálásakor győződjön meg arról, hogy a hálózat lehetővé teszi, hogy az NTP-forgalom áthaladjon az adatközpontból az internetre. Nyilvános NTP-kiszolgáló megadásakor meg kell győződnie arról, hogy a hálózati tűzfalak és más biztonsági eszközök úgy vannak konfigurálva, hogy az NTP-forgalom engedélyezze a külső hálózatról érkező és onnan érkező forgalmat. Ha a kétirányú NTP-forgalom nem engedélyezett, belső NTP-kiszolgálót kell használnia (a Windows tartományvezérlő ezt a funkciót biztosítja). Ha az eszköz nem tud szinkronizálni időt, előfordulhat, hogy nem tud kommunikálni a felhőalapú tárolási szolgáltatóval.

A nyilvános NTP-kiszolgálók listájának megtekintéséhez nyissa meg az [NTP-kiszolgálók weblapot](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Mi történik, ha az eszköz egy másik időzónában van telepítve?

Ha az eszköz egy másik időzónában van telepítve, a rendszer megváltoztatja az eszköz időzónáját. Mivel az összes biztonsági mentési szabályzat az eszköz időzónáját használja, a biztonsági mentési szabályzatok automatikusan az új időzónával összhangban lesznek módosítva. Felhasználói beavatkozásra nincs szükség.

## <a name="modify-dns-settings"></a>DNS-beállítások módosítása

A DNS-kiszolgálót akkor kell használni, amikor az eszköz megpróbál kommunikálni a felhőalapú tárolási szolgáltatóval. A konfigurált DNS-beállítások megtekintéséhez és módosításához használja az eszköz **hálózati beállítások** paneljét. 

![DNS-beállítások a hálózati beállításokban](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

A magas rendelkezésre állás érdekében az elsődleges és a másodlagos DNS-kiszolgálókat is konfigurálnia kell az eszköz kezdeti üzembe helyezése során.

**Elsődleges DNS-kiszolgáló** – a Windows PowerShell StorSimple-bővítménye használatával először az elsődleges DNS-kiszolgálót kell megadnia a kezdeti beállítás során. Az elsődleges DNS-kiszolgálót csak a Windows PowerShell felületén keresztül állíthatja be újra. A `Set-HcsDNSClientServerAddress` parancsmag használatával módosítsa az eszköz elsődleges DNS-kiszolgálóját. További információkért keresse fel a [set-HcsDNSClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) parancsmag szintaxisát.

**Másodlagos DNS-kiszolgáló** – a másodlagos DNS-kiszolgáló módosításához használja a `Set-HcsDNSClientServerAddress` StorSimple eszköz eszköz vagy **hálózati beállítások** paneljének Windows PowerShell felületén található parancsmagot a Azure Portal.

A másodlagos DNS-kiszolgáló Azure Portalban való módosításához hajtsa végre az alábbi lépéseket.

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Az eszközök listájából válassza ki az eszközt, és kattintson rá.

2. A **Beállítások** panelen válassza az **eszközbeállítások > hálózat** lehetőséget. Ekkor megnyílik a **hálózati beállítások** panel. Kattintson a **DNS-beállítások** csempére. Módosítsa a másodlagos DNS-kiszolgáló IP-címét.

    ![Másodlagos DNS-kiszolgáló IP-címének módosítása](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. A parancssorban kattintson a **Mentés** elemre, és amikor a rendszer kéri a megerősítést, kattintson **az OK** gombra.

    ![Módosítások mentése és megerősítése](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Hálózati adapterek módosítása

Az eszköz hat eszköz hálózati adapterrel rendelkezik, amelyek közül négy 1 GbE, és ezek közül kettő 10 GbE. Ezek a felületek a 0. adatértékként vannak megjelölve. AZ adat0, az 1., a 4. és az 5. adatértékek 1 GbE, míg a 2. és az adat3 GbE hálózati adapterek.

A **hálózati beállítások** panelen konfigurálhatja a használni kívánt csatolókat.

![Hálózati adapterek konfigurálása hálózati beállítások használatával](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

A magas rendelkezésre állás biztosítása érdekében javasoljuk, hogy legalább két iSCSI-felületet és két felhőalapú felületet rendelkezzen az eszközön. Azt javasoljuk, hogy ne kelljen letiltani a nem használt csatolókat.

Az egyes hálózati adapterek esetében a következő paraméterek jelennek meg:

* **Speed** – nem a felhasználó által konfigurálható paraméter. AZ adat0, az 1., a 4. és az 5. adatértékek mindig 1 GbE, míg a 2. és a 3. adatkapcsolat 10 GbE interfész.
  
  > [!NOTE]
  > A sebesség és a duplex mindig automatikusan egyeztetve van. A Jumbo-keretek nem támogatottak.
  
* **Illesztőfelület állapota** – az illesztőfelület engedélyezhető vagy letiltható. Ha engedélyezve van, az eszköz megpróbálja használni a felületet. Azt javasoljuk, hogy csak a hálózathoz csatlakoztatott és a használatban lévő felületek legyenek engedélyezve. Tiltsa le a nem használt csatolókat.
* **Illesztőfelület típusa** – ez a paraméter lehetővé teszi az iSCSI-forgalom elkülönítését a Felhőbeli tárolók forgalmával. Ez a paraméter a következők egyike lehet:
  
  * **Felhő engedélyezve** – ha engedélyezve van, az eszköz ezt a felületet fogja használni a felhővel való kommunikációhoz.
  * **iSCSI engedélyezve** – ha engedélyezve van, az eszköz ezt a felületet fogja használni az iSCSI-gazdagépkel való kommunikációhoz.
    
    Javasoljuk, hogy az iSCSI-forgalmat elkülönítse a Felhőbeli tárolási forgalomtól. Azt is vegye figyelembe, hogy a gazdagép ugyanazon az alhálózaton belül van, mint az eszköz, nem kell átjárót rendelnie; Ha azonban a gazdagép más alhálózaton található, mint az eszköz, hozzá kell rendelnie egy átjárót.
* **IP-cím** – a hálózati adapterek konfigurálásakor konfigurálnia kell egy virtuális IP-címet (VIP). Ez lehet IPv4 vagy IPv6 vagy mindkettő. Az eszköz hálózati adapterei esetében az IPv4-és az IPv6-cím családok is támogatottak. IPv4 használata esetén a 32 bites IP-címet (*xxx.xxx.xxx.xxx*) pont – decimális jelöléssel kell megadni. IPv6 használata esetén egyszerűen adjon meg egy 4 számjegyű előtagot, és a rendszer az adott előtag alapján automatikusan létrehozza a 128 bites címeket az eszköz hálózati adaptere számára.
* **Alhálózat** – az alhálózati maszkra hivatkozik, és a Windows PowerShell felületén keresztül van konfigurálva.
* **Átjáró** – ez az az alapértelmezett átjáró, amelyet ez a csatoló használ, amikor olyan csomópontokkal próbál kommunikálni, amelyek nem ugyanabban az IP-címtartomány (alhálózat) belül vannak. Az alapértelmezett átjárónak ugyanabban a címtartomány (alhálózat) alatt kell lennie, mint a csatoló IP-címe az alhálózati maszk alapján.
* **Rögzített IP-cím** – ez a mező csak a 0. adatkapcsolat konfigurálásakor érhető el. A frissítések, például az eszköz frissítései vagy hibaelhárítása esetén előfordulhat, hogy közvetlenül kell csatlakoznia az eszköz vezérlőjéhez. A rögzített IP-cím használható az aktív és a passzív vezérlő elérésére az eszközön.

> [!NOTE]
> * A megfelelő működés biztosításához ellenőrizze az interfész sebességét és a duplex kapcsolót arra a kapcsolóra, amelyhez az egyes eszközök csatolói csatlakoznak. A Switch interface-nek vagy a (1000 MB/s) értékkel kell megegyeznie, vagy konfigurálnia kell a teljes kétirányú átvitelt. A lassabb vagy váltakozó kétirányú átviteli sebességű felületek teljesítménybeli problémákat okozhatnak.
> * A fennakadások és a leállás csökkentése érdekében javasoljuk, hogy engedélyezze a portfast minden olyan kapcsoló porton, amelyhez az eszköz iSCSI hálózati adaptere csatlakozni fog. Ez biztosítja, hogy a hálózati kapcsolat gyorsan megoldható feladatátvétel esetén.

### <a name="configure-data-0"></a>0. adatértékek konfigurálása

Alapértelmezés szerint a 0 érték a felhőben van engedélyezve. Az adat0 konfigurálásakor két rögzített IP-címet is konfigurálnia kell, egyet az egyes vezérlőkhöz. Ezek a rögzített IP-címek közvetlenül az eszközökhöz való hozzáféréshez használhatók, és akkor hasznosak, amikor frissítéseket telepít az eszközön, a Garbage-gyűjtemények megfelelő működéséhez, illetve a hibaelhárítási célból a vezérlőhöz való hozzáféréshez.

A rögzített IP-vezérlőket a 0. adatbeállítások panelen állíthatja be újra.

![Hálózati adapter konfigurálása – 0. adatérték](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> A vezérlő rögzített IP-címei az eszköz frissítéseinek karbantartására, valamint a lemezterület-visszanyerési algoritmusok (Garbage Collection) megfelelő működésének kiszolgálására szolgálnak. Ezért a fix IP-knek irányíthatóknak kell lenniük, és csatlakoztatva kell lenniük az internethez.

### <a name="configure-data-1---data-5"></a>1. adatértékek konfigurálása – 5.

Az 1 – 5 adatkapcsolatú hálózati adapterek esetében az összes hálózati beállítást a következő képernyőképen látható módon konfigurálhatja:

![Hálózati adapterek 1. adattípusának konfigurálása – 5. adatkapcsolat](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>IP-címek cseréje vagy újbóli társítása

Jelenleg, ha a vezérlő bármelyik hálózati adaptere egy használatban lévő virtuális IP-címhez van hozzárendelve (ugyanazon eszköz vagy a hálózat egy másik eszköze), akkor a vezérlő feladatátvételt hajt végre. Ha a virtuális IP-címet egy eszköz hálózati adapterére cseréli vagy rendeli hozzá, akkor a megfelelő eljárást kell követnie, mert ismétlődő IP-helyzetet hozhat létre.

A következő lépések végrehajtásával cserélje le vagy rendelje át a virtuális IP-címek bármelyik hálózati adapterhez való hozzárendelését:

#### <a name="to-reassign-ips"></a>IP-címek újbóli társítása

1. Törölje mindkét csatoló IP-címét.
2. Az IP-címek törlése után rendelje hozzá az új IP-címeket a megfelelő interfészekhez.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [konfigurálhatja az MPIO-t a StorSimple-eszközhöz](storsimple-8000-configure-mpio-windows-server.md).
* Ismerje meg, hogyan kezelheti a [StorSimple-eszközt a StorSimple Eszközkezelő szolgáltatással](storsimple-8000-manager-service-administration.md).