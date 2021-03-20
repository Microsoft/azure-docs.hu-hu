---
title: Microsoft Azure StorSimple 8600-eszköz telepítése
description: Útmutató a StorSimple 8600-eszköz kicsomagolásához, rack csatlakoztatásához és kábelhez a szoftver telepítése és konfigurálása előtt.
author: alkohli
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 8d118e71b29b54b034788c37916091ad8f8b996f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91871813"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>A StorSimple 8600-eszköz kicsomagolása, rack-csatlakoztatása és kábele

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple 8600 egy kettős bekerítésű eszköz, amely egy elsődleges és egy EBOD házből áll. Ez az oktatóanyag azt ismerteti, hogyan lehet kicsomagolni, állványra csatlakoztatni és csatlakoztatni a StorSimple 8600 eszköz hardverét a StorSimple szoftver konfigurálása előtt.

## <a name="unpack-your-storsimple-8600-device"></a>A StorSimple 8600-eszköz kicsomagolása
Az alábbi lépések részletesen ismertetik a StorSimple 8600 Storage-eszköz kicsomagolásának módját. Ez az eszköz két mezőbe kerül, egyet az elsődleges ház számára, egy másikat pedig a EBOD ház számára. Ezt a két mezőt ezután egyetlen dobozba helyezi a rendszer.

### <a name="prepare-to-unpack-your-device"></a>Felkészülés az eszköz kicsomagolására
Az eszköz kicsomagolása előtt tekintse át a következő információkat.

![Figyelmeztetés ikon ](./media/storsimple-safety/IC740879.png)![ nagy súly ikon ](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Figyelmeztetés!**

1. Győződjön meg arról, hogy két személy áll rendelkezésre az eszköz súlyozásának kezeléséhez, ha manuálisan végzi a kezelést. Egy teljesen konfigurált ház súlya 32 kg (70 lbs) lehet.
2. Helyezze a csomagot egy sima, vízszintes felületre.

Ezután végezze el az alábbi lépéseket az eszköz kicsomagolásához.

#### <a name="to-unpack-your-device"></a>Az eszköz kicsomagolása
1. Vizsgálja meg a dobozt és a térkitöltő anyagot, hogy nincsenek-e rajta ütődés, vágás, nedvesség vagy más egyértelmű sérülés nyomai. Ha a csomagon jelentős sérülések láthatók, ne nyissa ki. Vegye [fel a kapcsolatot a Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) segítségével, és mérje fel, hogy az eszköz megfelelően működik-e.
2. Nyissa meg a külső mezőt, majd vegye fel az elsődleges és a EBOD-házaknak megfelelő két mezőt. Most már kicsomagolhatja az elsődleges és a EBOD-házakat. Az alábbi ábrán az egyik ház kicsomagolt nézete látható.
   
    ![A tárolóeszköz kicsomagolása](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **A tárolóeszköz kicsomagolt nézete**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Csomagolási mező |
   |   2 |SAS-kábelek (a tartozékok és kábelek tálcájában) |
   |   3 |Alsó hab |
   |   4 |Eszköz |
   |   5 |Felső hab |
   |   6 |Kiegészítő mező |
3. A két mező kicsomagolása után ellenőrizze, hogy rendelkezik-e az alábbiakkal:
   
   * 1 elsődleges bekerítés (az elsődleges ház és a EBOD két külön dobozban található)
   * 1 EBOD ház
   * 4 tápkábel, 2 az egyes dobozokban
   * 2 SAS-kábelek (az elsődleges ház csatlakoztatása a EBOD-ház számára)
   * 1 crossover Ethernet-kábel
   * 2 soros konzolos kábelek
   * 1 soros USB-átalakító a soros hozzáféréshez
   * 4 QSFP-to-SFP + adapterek 10 GbE hálózati adapterrel való használatra
   * 2 rack csatlakoztatási készlet (4 oldalsó sín, csatlakoztatási hardverrel, 2 mindegyik az elsődleges ház és a EBOD ház számára), 1 az egyes mezőkben
   * Bevezetés – dokumentáció
     
     Ha nem kapta meg a fent felsorolt elemek egyikét sem, [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md).  

A következő lépés az eszköz állványra szerelése.

## <a name="rack-mount-your-storsimple-8600-device"></a>StorSimple 8600-eszköz csatlakoztatása
A következő lépésekkel telepítheti a StorSimple 8600 tárolóeszközt standard 19 hüvelykes, elölről és hátulról származó bejegyzésekből álló állványra. Az eszközhöz két ház tartozik: egy elsődleges bekerítés és egy EBOD ház. Mindkettőnek állványra csatlakoztatva kell lennie.

A telepítés több lépésből áll, amelyek mindegyikét a következő eljárásokban tárgyaljuk.

> [!IMPORTANT]
> A StorSimple-eszközöknek rack-csatlakoztatással kell rendelkezniük a megfelelő működéshez.
> 
> 

### <a name="site-preparation"></a>Hely előkészítése
A házakat standard 19 hüvelykes állványra kell telepíteni, amely az első és a hátsó bejegyzésekből áll. A következő eljárással készítheti elő az állvány telepítését.

#### <a name="to-prepare-the-site-for-rack-installation"></a>A hely előkészítése a rack telepítéséhez
1. Győződjön meg arról, hogy az elsődleges és a EBOD-ház biztonságosan működik a sima, stabil és magas szintű munkafelületen (vagy hasonló módon).
2. Győződjön meg arról, hogy a hely, ahol be kívánja állítani a standard szintű AC-áramellátást egy független forrásból vagy egy, a szünetmentes áramforrást (UPS-t) biztosító Power Distribution Unit (PDU) szolgáltatásból.
3. Győződjön meg arról, hogy az állványon egy 4U (2 X 2U) tárolóhely áll rendelkezésre, amelyben csatlakoztatni kívánja a házakat.

![Figyelmeztetés ikon ](./media/storsimple-safety/IC740879.png)![ nagy súly ikon ](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Figyelmeztetés!**

 Ügyeljen arra, hogy a súlyozás kezeléséhez két személy legyen elérhető, ha az eszköz telepítését manuálisan végzi. Egy teljesen konfigurált ház súlya 32 kg (70 lbs) lehet.

### <a name="rack-prerequisites"></a>A rack előfeltételei
A házak standard 19 hüvelykes rack szekrényben való telepítésre lettek tervezve a következőkkel:

* A rack utáni 27,84 cm-es minimális mélység a post értékre
* Az eszköz maximális 32 kg-os súlya
* Maximális ellennyomás 5 Pascal (0,5 mm vízmérőműszer)

### <a name="rack-mounting-rail-kit"></a>Állványra szerelhető vasúti készlet
A rendszer a 19 hüvelykes rack kabinet használva biztosítja a csatlakoztatási sínek készletét. A sínek tesztelték a maximális bekerítési súlyt. Ezek a sínek több ház telepítését is lehetővé teszik az állványon belüli lemezterület elvesztése nélkül. Először telepítse a EBOD burkolatot.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>A EBOD bekerítésének telepítése a Rails-re
1. Ezt a lépést csak akkor hajtsa végre, ha a belső sínek nincsenek telepítve az eszközön. A belső sínek jellemzően a gyárban települnek. Ha a Rails nincs telepítve, akkor telepítse a bal oldali és a jobb oldali vasúti diát a ház vázájának oldalaira. Mindkét oldalon hat metrikus csavart használnak. A tájolással kapcsolatos segítség érdekében a vasúti diaképek az **LH – eleje** és az **RH – elöl** vannak megjelölve, a ház hátulján elhelyezett végpont pedig kúpos végű.
   
    ![Vasúti adatkockák csatlakoztatása a ház vázához](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Vasúti diaképek csatlakoztatása a ház oldalaihoz**
   
   | Címke | Leírás |
   | --- | --- |
   |  1 |M 3x4 gomb – Head csavarok |
   |  2 |Alváz-diák |
2. Csatolja a bal oldali sín és a jobb oldali sín szerelvényeket a rack kabinet függőleges tagjaihoz. A szögletes zárójelek az **LH**, **RH**, és **Ez az oldal felfelé** mutatnak a helyes tájolási útmutatóban.
3. Keresse meg a sín csapjait a sínszerelvény elülső és hátsó végénél. Kiterjesztheti a vasútot úgy, hogy illeszkedjen a rack-bejegyzések között, és beszúrja a PIN-kódokat az első és a hátsó állvány utáni függőleges tag lyukba. Győződjön meg arról, hogy a vasúti szerelvény szintje.
4. A vasúti szerelvényt a megadott metrikai csavarok használatával biztosíthatja az állvány függőleges tagjainak. Használjon egy csavart az elülső és egy hátsó oldalon.
5. Ismételje meg ezeket a lépéseket a másik vasúti szerelvény esetében.
   
     ![Vasúti diaképek összekapcsolása rack szekrénysel](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Vasúti szerelvények csatlakoztatása a rackhez**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Befogó csavar |
   |   2 |Szögletes furatos elülső állvány utáni csavar |
   |   3 |Bal oldali vasúti hely PIN-kódjai |
   |   4 |Befogó csavar |
   |   5 |Bal oldali hátsó vasúti tárolóhely PIN-kód |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>A EBOD bekerítésének csatlakoztatása a rackben
Az imént telepített rack Rails használatával hajtsa végre a következő lépéseket a EBOD bekerítés csatlakoztatásához a rackben.

#### <a name="to-mount-the-ebod-enclosure"></a>A EBOD-ház csatlakoztatása
1. Egy asszisztenssel emelje fel a bekerítést, és igazítsa a rack railshoz.
2. Óvatosan illessze be a burkolatot a railsba, majd a rack szekrénybe küldje el teljesen.
   
    ![Eszköz beszúrása a rackbe](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **A ház csatlakoztatása a rackben**
3. Távolítsa el a bal és a jobb oldali véglapos sapkát a Caps ingyenes kihúzásával. A véglapos sapkák egyszerűen a karimák felületére illeszkednek.
4. A ház biztonságossá tételéhez telepítsen egy megadott Phillips-fej csavart az egyes karimák, balra és jobbra.
5. A karima-sapkákat úgy telepítheti, hogy megnyomja őket a helyükre, és a helyükre pattintja őket.
   
     ![A karima-sapkák telepítése](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **A karima-sapkák telepítése**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Rögzítési csavar |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Az elsődleges ház csatlakoztatása a rackben
Miután befejezte a EBOD-ház csatlakoztatását, az elsődleges ház csatlakoztatását ugyanezen lépéseket követve kell elvégeznie.

> [!NOTE]
> * Lehetséges, hogy van néhány üres tárolóhely az állványban az elsődleges ház és a EBOD ház között.
> * A megadott 2m SAS-kábel használatával csatlakoztassa az elsődleges bekerítést a EBOD-ház számára.
> * A fej egységének relatív elhelyezésére a EBOD egységnek nincsenek korlátai. Ezért az elsődleges ház a felső tárolóhelyen és az alábbi EBOD helyezhető el – vagy fordítva.
> 
> 

A következő lépés az eszköz csatlakoztatása az áramellátáshoz, a hálózathoz és a soros eléréshez.

## <a name="cable-your-storsimple-8600-device"></a>A StorSimple 8600 sorozatú eszköz kábelezése
Az alábbi eljárások azt ismertetik, hogyan lehet a StorSimple 8600-eszközt a Power, a Network és a soros kapcsolatok számára összekapcsolni.

### <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené az eszköz csatlakoztatását, a következőkre lesz szüksége:

* Az elsődleges bekerítés és a EBOD-ház teljes kicsomagolása
* 4 tápkábel (2 mindegyik az elsődleges és a EBOD ház számára), amely az eszközhöz érkezett
* 2 SAS-kábel, amely az eszközzel csatlakozik az EBOD-ház elsődleges házba való csatlakoztatásához
* Hozzáférés 2 hálózati elosztási egységhez (PDU) (ajánlott)
* Hálózati kábelek
* Megadott soros kábelek
* Soros USB-átalakító a számítógépen telepített megfelelő illesztőprogrammal (ha szükséges)
* 4 QSFP-to-SFP + adapterek biztosítva 10 GbE hálózati adapterrel való használatra
* [A StorSimple-eszköz 10 GbE hálózati adapterének támogatott hardverei](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS és Power kábelezés
Az eszköznek van egy elsődleges bekerítése és egy EBOD ház is. Ehhez csatlakoztatni kell az egységeket a soros csatlakozású SCSI (SAS) kapcsolathoz és a teljesítményhez.

Ha első alkalommal állítja be az eszközt, hajtsa végre az SAS-kábelezés lépéseit először, majd hajtsa végre a Power kábelezés lépéseit.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Hálózati kábelezés
Az eszköz aktív-készenléti konfigurációban van: bármikor, az egyik vezérlő modul aktív, és az összes lemezt és hálózati műveletet feldolgozza, miközben a másik vezérlő modul készenléti állapotban van. Ha a vezérlő meghibásodása történik, a készenléti vezérlő azonnal aktiválja és folytatja az összes lemezt és hálózati műveletet.

A redundáns vezérlő feladatátvételének támogatásához a következő lépésekben látható módon csatlakoztassa az eszköz hálózatát.

#### <a name="to-cable-for-network-connection"></a>Hálózati kapcsolatok kábele
1. Az eszköz hat hálózati csatolóval rendelkezik az egyes vezérlőkön: 4 1 Gbps és 2 10 Gbps Ethernet port. Az eszköz hátlapján található adatportok azonosításához tekintse meg az alábbi ábrát.
   
     ![8600-eszköz hátlapja](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Az eszköz hátoldala, amely az adatportokat mutatja**
   
   | Címke | Description |
   | --- | --- |
   |   0, 1, 4, 5 |1 GbE hálózati adapterek |
   |   2, 3 |10 GbE hálózati adapter |
   |   6 |Soros portok |
2. Tekintse meg a hálózati kábelezés következő ábráját. (A minimális hálózati konfigurációt a folytonos kék vonalak mutatják. A magas rendelkezésre állás és a teljesítmény érdekében a további szükséges beállításokat a pontozott vonalak mutatják.)

![A 4U-eszköz hálózati csatlakoztatása](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Az eszköz hálózati kábelezései**

| Címke | Description |
| --- | --- |
| A |Helyi Internet-hozzáféréssel rendelkező LAN |
| B |Vezérlő 0 |
| C |PCM 0 |
| T |Vezérlő 1 |
| E |PCM 1 |
| F |EBOD vezérlő 0 |
| G |1. EBOD-vezérlő |
| H, I |Gazdagépek (például fájlkiszolgálók) |
| 0-5 |Hálózati adapterek |
| 6 |Elsődleges ház |
| 7 |EBOD ház |

Az eszköz kábelezése esetén a minimális konfigurációhoz az alábbiak szükségesek:

* Legalább két hálózati adapter csatlakozik minden vezérlőhöz, egy a felhőhöz való hozzáféréshez, egyet pedig az iSCSI-hez. A 0. számú port automatikusan engedélyezve és konfigurálva van az eszköz soros konzolján keresztül. A 0. ADATOKon kívül egy másik adatportot is konfigurálni kell a klasszikus Azure portálon. Ebben az esetben az adat0 portot az elsődleges helyi hálózathoz (internet-hozzáféréssel rendelkező hálózat) kapcsolja. A többi adatport a kívánt szerepkörtől függően a hálózat SAN/iSCSI LAN-(VLAN-) szegmenséhez is csatlakoztatható.
* Azonos illesztőfelületek az ugyanahhoz a hálózathoz csatlakoztatott vezérlőknél, hogy a rendelkezésre állást a vezérlő feladatátvétele esetén biztosítsa. Ha például a 0. és a 3. adattábla összekapcsolását választja, akkor a másik vezérlőn a megfelelő adatmennyiség 0 és a 3. adatkapcsolattal kell csatlakoznia.

Tartsa szem előtt a magas rendelkezésre állást és a teljesítményt:

* Ha lehetséges, konfiguráljon egy pár hálózati interfészt a felhőalapú hozzáféréshez (1 GbE) és egy másikat az iSCSI-hez (10 GbE ajánlott) minden vezérlőn.
* Ha lehetséges, kapcsolja össze az egyes vezérlők hálózati adaptereit két különböző kapcsolóval, hogy biztosítsa a rendelkezésre állást egy kapcsoló meghibásodása esetén. Az ábrán a 2 10 GbE hálózati adapterek, a 2. adat és a 3. adatok láthatók a két különböző kapcsolóhoz csatlakozó vezérlőkből. További információkért tekintse meg a **hálózati adaptereket** a [StorSimple-eszköz magas rendelkezésre állási követelményeinek](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple)megfelelően.

> [!NOTE]
> Ha SFP + adóvevőt használ a 10 GbE hálózati adapterrel, használja a megadott QSFP-SFP + adaptereket. További információkért keresse fel a [StorSimple-eszköz 10 GbE hálózati adapterének támogatott hardvereit](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Soros port kábelezése
A soros port csatlakoztatásához hajtsa végre az alábbi lépéseket.

#### <a name="to-cable-for-serial-connection"></a>Csatlakoztatás soros kapcsolathoz
1. Az eszköz soros porttal rendelkezik minden olyan vezérlőn, amelyet egy Csavarkulcs ikon azonosít. A soros portok megkereséséhez tekintse meg az eszköz hátoldalán található adatportokat bemutató ábrát.
2. Azonosítsa az aktív vezérlőt az eszköz hátlapján. A villogó kék LED azt jelzi, hogy a vezérlő aktív.
3. Használja a megadott soros kábelt (ha szükséges, az USB-soros átalakítót a laptophoz), és csatlakoztassa a konzolt vagy a számítógépet (a terminál emulációja az eszközhöz) az aktív vezérlő soros portjához.
4. Telepítse a számítógépen a soros USB-illesztőprogramokat (az eszközön szállított).
5. A soros kapcsolat beállítása a következőképpen történik:
   
   * 115 200 Baud
   * 8 adatbitek
   * 1 leállítási bit
   * Nincs paritás
   * A flow vezérlőelem beállítása **none**
6. A konzolon az ENTER billentyű lenyomásával ellenőrizze, hogy működik-e a kapcsolat. Meg kell jelennie A soros konzol menüjének.

> [!NOTE]
> **Fények – felügyelet:** Ha az eszköz egy távoli adatközpontban vagy egy korlátozott hozzáférésű számítógép-helyiségben van telepítve, ügyeljen arra, hogy a soros kapcsolatok mindkét vezérlőhöz mindig a soros konzol kapcsolóhoz vagy hasonló eszközökhöz kapcsolódjanak. Ez lehetővé teszi a sávon kívüli távvezérlési és-támogatási műveleteket hálózati fennakadás vagy váratlan hibák esetén.
> 
> 

Befejezte a kábelezést az eszközön a Power, a hálózati hozzáférés és a soros kapcsolat számára. A következő lépés a szoftver konfigurálása az eszközön.

## <a name="next-steps"></a>Következő lépések
Most már készen áll a helyszíni [StorSimple-eszköz üzembe helyezésére és konfigurálására](storsimple-8000-deployment-walkthrough-u2.md).

