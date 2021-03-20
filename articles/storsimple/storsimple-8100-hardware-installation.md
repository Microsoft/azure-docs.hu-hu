---
title: Microsoft Azure StorSimple 8100-eszköz telepítése
description: Útmutató a StorSimple 8100-eszköz kicsomagolásához, rack csatlakoztatásához és kábelhez a szoftver telepítése és konfigurálása előtt.
author: alkohli
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 113b72ddf7e5d508c8a0b577d4004d4fbd83e8e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84699107"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>A StorSimple 8100-eszköz kicsomagolása, rack-csatlakoztatása és kábele

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple 8100 egyetlen ház, állványra csatlakoztatott eszköz. Ez az oktatóanyag azt ismerteti, hogyan lehet a StorSimple-eszköz konfigurálása és telepítése előtt kicsomagolni, állványra csatlakoztatni és csatlakoztatni a StorSimple 8100 eszköz hardverét.

## <a name="unpack-your-storsimple-8100-device"></a>A StorSimple 8100-eszköz kicsomagolása
Az alábbi lépések részletesen ismertetik a StorSimple 8100 Storage-eszköz kicsomagolásának módját. Az eszköz egyetlen dobozban érkezik.

### <a name="prepare-to-unpack-your-device"></a>Felkészülés az eszköz kicsomagolására
Az eszköz kicsomagolása előtt tekintse át a következő információkat.

![Figyelmeztetés ikon ](./media/storsimple-safety/IC740879.png)![ nagy súly ikon ](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **Figyelmeztetés!**

1. Győződjön meg arról, hogy két személy áll rendelkezésre a ház súlyának kezeléséhez, ha manuálisan végzi a kezelést. Egy teljesen konfigurált ház súlya 32 kg (70 lbs) lehet.
2. Helyezze a csomagot egy sima, vízszintes felületre.

Ezután végezze el az alábbi lépéseket az eszköz kicsomagolásához.

#### <a name="to-unpack-your-device"></a>Az eszköz kicsomagolása
1. Vizsgálja meg a dobozt és a térkitöltő anyagot, hogy nincsenek-e rajta ütődés, vágás, nedvesség vagy más egyértelmű sérülés nyomai. Ha a csomagon jelentős sérülések láthatók, ne nyissa ki. Vegye [fel a kapcsolatot a Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) segítségével, és mérje fel, hogy az eszköz megfelelően működik-e.
2. Bontsa ki a dobozt. Az alábbi képen a StorSimple-eszköz kicsomagolt nézete látható.
   
     ![A tárolóeszköz kicsomagolása](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **A tárolóeszköz kicsomagolt nézete**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Csomagolási mező |
   |   2 |Alsó hab |
   |   3 |Eszköz |
   |   4 |Felső hab |
   |   5 |Kiegészítő mező |
3. A csomag felbontása után ellenőrizze, hogy megvannak-e a következők:
   
   * 1 egyetlen bekerítés eszköz
   * 2 tápkábel
   * 1 crossover Ethernet-kábel
   * 2 soros konzolos kábelek
   * 1 soros USB-átalakító a soros hozzáféréshez
   * 1 illetéktelen hozzáférést igazoló T10 csavarhúzó
   * 4 QSFP-to-SFP + adapterek 10 GbE hálózati adapterrel való használatra
   * 1 rack-Mount csomag (2 oldalsó sín csatlakoztatási hardverrel)
   * Első lépések dokumentáció
     
     Ha nem kapta meg a fent felsorolt elemek egyikét sem, [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md).

A következő lépés az eszköz állványra szerelése.

## <a name="rack-mount-your-storsimple-8100-device"></a>StorSimple 8100-eszköz csatlakoztatása
A következő lépésekkel telepítheti a StorSimple 8100 tárolóeszközt standard 19 hüvelykes, elölről és hátulról származó bejegyzésekből álló állványra. A StorSimple 8100-eszköz egyetlen elsődleges bekerítéssel rendelkezik.

A telepítés több lépésből áll, amelyek mindegyikét a következő eljárásokban tárgyaljuk.

> [!IMPORTANT]
> A StorSimple-eszközöknek rack-csatlakoztatással kell rendelkezniük a megfelelő működéshez.
> 
> 

### <a name="prepare-the-site"></a>A hely előkészítése
Az eszközt olyan standard 19 hüvelykes állványra kell telepíteni, amely elöl és hátul is van. A következő eljárással készítheti elő az állvány telepítését.

#### <a name="to-prepare-the-site-for-rack-installation"></a>A hely előkészítése a rack telepítéséhez
1. Győződjön meg arról, hogy az eszköz egy sima, stabil és vízszintes munkafelületen (vagy ahhoz hasonlón) helyezkedik el.
2. Győződjön meg arról, hogy a hely, ahol be kívánja állítani a standard szintű AC-áramellátást egy független forrásból vagy egy, a szünetmentes áramforrást (UPS-t) biztosító Power Distribution Unit (PDU) szolgáltatásból.
3. Győződjön meg arról, hogy az állványon egy 2U-tárolóhely áll rendelkezésre, amelyhez csatlakoztatni kívánja az eszközt.

![Figyelmeztetés ikon ](./media/storsimple-safety/IC740879.png)![ nagy súly ikon ](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **Figyelmeztetés!**

Ügyeljen arra, hogy a súlyozás kezeléséhez két személy legyen elérhető, ha az eszköz telepítését manuálisan végzi. Egy teljesen konfigurált ház súlya 32 kg (70 lbs) lehet.

### <a name="rack-prerequisites"></a>A rack előfeltételei
Az 8100-es ház a következőhöz készült standard 19 hüvelykes rack szekrényben telepíthető:

* A beosztás utáni, a post értékre 27,84 cm-es minimális mélység.
* Az eszköz maximális 32 kg-os súlya
* Maximális ellennyomás 5 Pascal (0,5 mm vízmérőműszer).

### <a name="rack-mounting-rail-kit"></a>Állványra szerelhető vasúti készlet
A 19 hüvelykes rack kabinet használva a csatlakoztatási sínek készlete is elérhető. A sínek tesztelték a maximális bekerítési súlyt. Ezek a sínek lehetővé teszik több ház telepítését anélkül, hogy helyet szabadítanak fel az állványon belül.

#### <a name="to-install-the-device-on-the-rails"></a>Az eszköz telepítése a Rails-re
1. Ezt a lépést csak akkor hajtsa végre, ha a belső sínek nincsenek telepítve az eszközön. A belső sínek jellemzően a gyárban települnek. Ha a Rails nincs telepítve, akkor telepítse a bal oldali és a jobb oldali vasúti diát a ház vázájának oldalaira. Mindkét oldalon hat metrikus csavart használnak. A tájolással kapcsolatos segítség érdekében a vasúti diaképek az **LH – eleje** és az **RH – elöl** vannak megjelölve, a ház hátulján elhelyezett végpont pedig kúpos végű.<br/>
   
    ![Vasúti adatkockák csatlakoztatása a ház vázához](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Belső vasúti diaképek csatlakoztatása a ház oldalaihoz**
   
    Címke | Leírás
    ----- | -----------
    1     | M 3x4 gomb – Head csavarok
    2     | Alváz-diák

2. A külső bal oldali sín és a külső jobb oldali vasút szerelvényeit csatlakoztassa a rack kabinet függőleges tagjaihoz. A szögletes zárójelek az **LH**, az **RH**, és **Ez az oldal felfelé** mutatnak a megfelelő tájolási útmutatóban.
3. Keresse meg a sín csapjait a sínszerelvény elülső és hátsó végénél. Kiterjesztheti a vasútot úgy, hogy illeszkedjen a rack-bejegyzések között, és beszúrja a PIN-kód behelyezését az első és a hátsó állványba a függőleges tag Győződjön meg arról, hogy a vasúti szerelvény szintje.
4. A megadott metrikai csavarok közül kettővel biztonságossá teheti a vasúti szerelvényt az állvány függőleges tagjai számára. Használjon egy csavart az elülső és egy hátsó oldalon.
5. Ismételje meg ezeket a lépéseket a másik vasúti szerelvény esetében.<br/>
   
     ![Vasúti diaképek összekapcsolása rack szekrénysel](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Külső sín szerelvények csatlakoztatása a rackhez**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Befogó csavar |
   |   2 |Szögletes furatos elülső állvány utáni csavar |
   |   3 |Bal oldali sín elülső helye PIN-kód |
   |   4 |Befogó csavar |
   |   5 |Bal oldali sín – hátsó hely PIN-kód |

### <a name="mounting-the-device-in-the-rack"></a>Az eszköz csatlakoztatása a rackben
Az imént telepített rack Rails használatával hajtsa végre az alábbi lépéseket az eszköz állványra csatlakoztatásához.

#### <a name="to-mount-the-device"></a>Az eszköz csatlakoztatása
1. Egy asszisztenssel emelje fel a bekerítést, és igazítsa a rack railshoz.
2. Óvatosan helyezze be az eszközt a railsba, majd az eszközt teljes egészében küldje el a rack-szekrénybe.<br/>
   
    ![Eszköz beszúrása a rackbe](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Az eszköz csatlakoztatása a rackben**
3. Távolítsa el a bal és a jobb oldali véglapos sapkát a Caps ingyenes kihúzásával. A véglapos sapkák egyszerűen a karimák felületére illeszkednek.
4. Gondoskodjon róla, hogy a szekrényben egy megadott Phillips-fej csavart telepítsen az egyes karimák, balra és jobbra.
5. A karima-sapkákat úgy telepítheti, hogy megnyomja őket a helyükre, és a helyükre pattintja őket.<br/>
   
     ![A karima-sapkák telepítése](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **A karima-sapkák telepítése**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Rögzítési csavar |

A következő lépés az eszköz csatlakoztatása az áramellátáshoz, a hálózathoz és a soros eléréshez.

## <a name="cable-your-storsimple-8100-device"></a>A StorSimple 8100 sorozatú eszköz kábelezése
Az alábbi eljárások azt ismertetik, hogyan lehet a StorSimple 8100-eszközt a Power, a Network és a soros kapcsolatok számára összekapcsolni.

### <a name="prerequisites"></a>Előfeltételek
Az eszköz kábelezésének megkezdéséhez az alábbiakra lesz szüksége:

* A tárolóeszköz, amely teljesen ki van csomagolva és rackben csatlakoztatva van.
* 2 az eszközzel ellátott hálózati kábelek
* Hozzáférés 2 hálózati elosztási egységhez (ajánlott).
* Hálózati kábelek
* Megadott soros kábelek
* Soros USB-átalakító a számítógépen telepített megfelelő illesztőprogrammal (ha szükséges)
* 4 QSFP-to-SFP + adapterek biztosítva 10 GbE hálózati adapterrel való használatra
* [A StorSimple-eszköz 10 GbE hálózati adapterének támogatott hardverei](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Energiaellátási kábelek
Az eszköz redundáns energiaellátási és hűtési modulokat (PCMs) tartalmaz. A magas rendelkezésre állás biztosítása érdekében mindkét PCMs telepíteni és csatlakoztatni kell a különböző áramforrásokhoz.

A következő lépések végrehajtásával csatlakoztassa az eszközt a powerhez.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Hálózati kábelezés
Az eszköz aktív-készenléti konfiguráció: bármikor, az egyik vezérlő modul aktív, és az összes lemezt és hálózati műveletet dolgozza fel, míg a másik vezérlő modul készenléti állapotban van. Ha egy vezérlő meghibásodik, a készenléti vezérlő azonnal aktiválódik, és folytatja az összes lemezt és hálózati műveletet.

A redundáns vezérlő feladatátvételének támogatásához csatlakoztassa az eszközt a következő lépésekben leírtaknak megfelelően.

#### <a name="to-cable-for-network-connection"></a>Hálózati kapcsolatok kábele
1. Az eszköz hat hálózati csatolóval rendelkezik a következő vezérlőkön: 4 1 GB/s, 2 10 Gbps Ethernet port. Azonosítsa a különböző adatportokat az eszköz hátlapján.
   
    ![8100-eszköz hátlapja](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Az eszköz hátoldala, amely az adatportokat mutatja**
   
   | Címke | Description |
   | --- | --- |
   |   0, 1, 4, 5 |1 GbE hálózati adapterek |
   |   2, 3 |10 GbE hálózati adapter |
   |   6 |Soros portok |
2. Tekintse meg a hálózati kábelezés következő ábráját. (A minimális hálózati konfigurációt a folytonos kék vonalak mutatják. A magas rendelkezésre álláshoz és a teljesítményhez szükséges további konfigurációt pontozott vonalak mutatják.)

    ![A 2U-eszköz hálózati csatlakoztatása](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Az eszköz hálózati kábelezései**

   |Címke | Description |
   |----- | ----------- |
   | A    | Helyi Internet-hozzáféréssel rendelkező LAN |
   | B    | Vezérlő 0 |
   | C    | PCM 0 |
   | T    | Vezérlő 1 |
   | E    | PCM 1 |
   | F, G | Hosts |
   | 0-5  | Hálózati adapterek |



Az eszköz kábelezése esetén a minimális konfigurációhoz az alábbiak szükségesek:

* Legalább két hálózati adapter csatlakozik minden vezérlőhöz, egy a felhőhöz való hozzáféréshez, egyet pedig az iSCSI-hez. A 0. számú port automatikusan engedélyezve és konfigurálva van az eszköz soros konzolján keresztül. A 0. ADATOKon kívül egy másik adatportot is konfigurálni kell a klasszikus Azure portálon. Ebben az esetben az adat0 portot az elsődleges helyi hálózathoz (internet-hozzáféréssel rendelkező hálózat) kapcsolja. A többi adatport a kívánt szerepkörtől függően a hálózat SAN/iSCSI LAN-(VLAN-) szegmenséhez is csatlakoztatható.
* Azonos illesztőfelületek az ugyanahhoz a hálózathoz csatlakoztatott vezérlőknél, hogy a rendelkezésre állást a vezérlő feladatátvétele esetén biztosítsa. Ha például a 0. és a 3. adattábla összekapcsolását választja, akkor a másik vezérlőn a megfelelő adatmennyiség 0 és a 3. adatkapcsolattal kell csatlakoznia.

Tartsa szem előtt a magas rendelkezésre állást és a teljesítményt:

* Ha lehetséges, konfiguráljon egy pár hálózati interfészt a felhőalapú hozzáféréshez (1 GbE) és egy másikat az iSCSI-hez (10 GbE ajánlott) minden vezérlőn.
* Ha lehetséges, kapcsolja össze az egyes vezérlők hálózati adaptereit két különböző kapcsolóval, hogy biztosítsa a rendelkezésre állást egy kapcsoló meghibásodása esetén. Az ábrán a 2 10 GbE hálózati adapterek, a 2. adat és a 3. adatok láthatók a két különböző kapcsolóhoz csatlakozó vezérlőkből.

További információkért tekintse meg a **hálózati adaptereket** a [StorSimple-eszköz magas rendelkezésre állási követelményeinek](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple)megfelelően.

> [!NOTE]
> Ha SFP + adóvevőt használ a 10 GbE hálózati adapterrel, használja a megadott QSFP-SFP + adaptereket. További információkért keresse fel a [StorSimple-eszköz 10 GbE hálózati adapterének támogatott hardvereit](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Soros port kábelezése
A soros port csatlakoztatásához hajtsa végre az alábbi lépéseket.

#### <a name="to-cable-for-serial-connection"></a>Csatlakoztatás soros kapcsolathoz
1. Az eszköz soros porttal rendelkezik minden olyan vezérlőn, amelyet egy Csavarkulcs ikon azonosít. Az eszköz hátlapján található soros portok megkereséséhez tekintse meg a [hálózati kábelezés](#network-cabling) szakasz ábráját.
2. Azonosítsa az aktív vezérlőt az eszköz hátlapján. A villogó kék LED azt jelzi, hogy a vezérlő aktív.
3. A megadott soros kábelek (szükség esetén a laptop USB-soros átalakítója) használata, valamint a konzol vagy számítógép csatlakoztatása (a terminál emulációja az eszközhöz) az aktív vezérlő soros portjára.
4. Telepítse a számítógépen a soros USB-illesztőprogramokat (az eszközön szállított).
5. A soros kapcsolat beállítása a következőképpen történik: 115 200 Baud, 8 adatbitek, 1 leállítási bit, nincs paritás, és a flow vezérlő nincs értékre állítva.
6. A konzolon az ENTER billentyű lenyomásával ellenőrizze, hogy működik-e a kapcsolat. Meg kell jelennie A soros konzol menüjének.

> [!NOTE]
> **Lights-Out felügyelet**: Ha az eszközt egy távoli adatközpontba vagy egy korlátozott hozzáféréssel rendelkező számítógépbe helyezi, győződjön meg arról, hogy a soros kapcsolatok mindkét vezérlőhöz mindig csatlakoztatva vannak egy soros konzol kapcsolóhoz vagy hasonló eszközökhöz. Ez lehetővé teszi a sávon kívüli távvezérlési és-támogatási műveleteket, ha hálózati fennakadások vagy váratlan hibák történtek.
> 
> 

Az eszköz most már a Power, a hálózati hozzáférés és a soros kapcsolat számára is kábeles. A következő lépés a szoftver konfigurálása és az eszköz üzembe helyezése.

## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan [helyezheti üzembe és konfigurálhatja helyszíni StorSimple-eszközét](storsimple-8000-deployment-walkthrough-u2.md).

