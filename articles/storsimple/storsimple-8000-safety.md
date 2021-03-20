---
title: Biztonságos telepítés & StorSimple 8000 Series eszköz működik
description: Ismerteti a biztonsági konvenciókat, irányelveket és szempontokat, valamint ismerteti a StorSimple-eszköz biztonságos telepítését és üzemeltetését.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 3adbd292d42fd6fd2a0e149e103c17968a6f084b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94950754"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>StorSimple-eszköz biztonságos telepítése és üzemeltetése
![Figyelmeztető ikon ](./media/storsimple-safety/IC740879.png)
 ![ olvassa el a biztonsági értesítés ikonját ](./media/storsimple-safety/IC740885.png) **, olvassa el a biztonsági és egészségvédelmi információkat**

Olvassa el a cikkben szereplő összes biztonsági és egészségvédelmi információt, amely a Microsoft Azure StorSimple eszközre vonatkozik. A StorSimple-eszközzel szállított összes nyomtatott útmutató későbbi használatra megtartható. Az utasítások követésének és a megfelelő beállításának, használatának és kezelésének elmulasztása növelheti a súlyos sérülés vagy halál kockázatát, illetve az eszköz vagy az eszközök sérülését. Az [útmutató letölthető verziója](https://www.microsoft.com/download/details.aspx?id=44233) szintén elérhető.

## <a name="safety-icon-conventions"></a>Biztonsági ikon konvenciói
Itt láthatja az ikonokat, amelyekkel megtekintheti a Microsoft Azure StorSimple eszköz beállításakor és futtatásakor betartandó biztonsági óvintézkedéseket.

| Ikon | Description |
|:--- |:--- |
| ![Veszélyforrás ikon ](./media/storsimple-safety/IC740879.png) **veszélye!** |Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – halált vagy súlyos sérülést eredményezhet. Ezt a jelet a legszélsőségesebb helyzetekre kell korlátozni. |
| ![Figyelmeztetés ikon ](./media/storsimple-safety/IC740879.png) **Figyelmeztetés!** |Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – halált vagy súlyos sérülést eredményezhet. |
| ![Figyelmeztető ikon ](./media/storsimple-safety/IC740879.png)  figyelmeztetése |Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – kisebb vagy mérsékelt sérülést eredményezhet. |
| ![Értesítés ikonjának ](./media/storsimple-safety/IC740881.png) **megjegyzése:** |A fontosnak tartott információkat jelöli, de nem kapcsolódik a veszélyforráshoz. |
| ![Elektromos áramütés ikon áramütési ](./media/storsimple-safety/IC740882.png) **kockázat** |Magas feszültségű |
| ![Nagy súly ikon ](./media/storsimple-safety/IC740883.png) **nagy súlya** | |
| ![Nincs felhasználó által szervizelhető rész ikonja ](./media/storsimple-safety/IC740879.png) **nem rendelkezik felhasználó által szervizelhető részekkel** |A megfelelő képzés hiányában ne legyen hozzáférés. |
| ![Biztonsági értesítés ikonjának beolvasása –](./media/storsimple-safety/IC740885.png)**első lépések** | |
| ![Tipp kockázati ikon ](./media/storsimple-safety/IC740886.png) **Tipp – kockázat** | |

## <a name="handling-precautions"></a>Óvintézkedések feldolgozása
![Figyelmeztetés ikon ](./media/storsimple-safety/IC740879.png) ![ nagy súly ikon ](./media/storsimple-safety/IC740883.png) **Figyelmeztetés!** 

A sérülés kockázatának csökkentése:

* Egy teljesen konfigurált ház súlya 32 kg (70 lbs) lehet. Ne próbálja meg önállóan feloldani.
* A ház áthelyezése előtt mindig győződjön meg arról, hogy két személy áll rendelkezésre a súlyozás kezelésére. Ügyeljen arra, hogy a súlyozást megkísérlő egyik személy is fenntartsa a sérüléseket.
* Ne szüntesse meg a burkolatot az egység hátsó részén található energiagazdálkodási és hűtési modulok (PCMs) fogantyúi alapján. Ezeket nem a súlyozásra tervezték.

## <a name="connection-precautions"></a>A kapcsolatok óvintézkedései
![Figyelmeztető ikon ](./media/storsimple-safety/IC740879.png) ![ áramütés ikonja ](./media/storsimple-safety/IC740882.png) **Figyelmeztetés!**

A sérülés, az áramütés vagy a halál valószínűségének csökkentése:

* Több AC-forrás esetén a teljes elkülönítés érdekében válassza le az összes energiaellátási teljesítményt.
* Véglegesen kihúzza az egységet, mielőtt áthelyezi, vagy ha úgy gondolja, hogy bármilyen módon megsérült.
* Biztonságos elektromos földi kapcsolódás a tápegységek számára. Győződjön meg arról, hogy a ház üzembe állítása megfelel a nemzeti és helyi követelményeknek, mielőtt alkalmazná a teljesítményt.
* Győződjön meg arról, hogy az energiaellátás mindig le van választva a PCM a zárt rendszerből való eltávolítása előtt.
* Mivel a tápkábel csatlakoztatása a fő leválasztási eszköz, győződjön meg arról, hogy a szoftvercsatorna-egységek a berendezés közelében találhatók, és könnyen elérhetők.

![Figyelmeztető ikon ](./media/storsimple-safety/IC740879.png) ![ áramütés ikonja ](./media/storsimple-safety/IC740882.png) **Figyelmeztetés!**

Az elektromos kapcsolatok túlmelegedésének vagy a tűz kialakulásának valószínűsége:

* A technikai specifikációban részletezett követelmények teljesítéséhez adjon meg egy megfelelő áramforrást, amely elektromos túlterhelés elleni védelemmel rendelkezik.
* Ne használjon hasított szárú-vezetékeket ("Y").
* A megfelelő biztonsági, kibocsátási és termikus követelményeknek való megfelelés érdekében a rendszer nem távolít el a mellékleteket, és az összes öblöt be kell tölteni a beépülő Modulos modulok vagy a meghajtó üresen.
* Győződjön meg arról, hogy a berendezés a gyártó által megadott módon van használatban. Ha ezt a berendezést a gyártó által nem meghatározott módon használják, előfordulhat, hogy a berendezés által biztosított védelem nem megfelelő.

![Értesítés ikonjának ](./media/storsimple-safety/IC740881.png) **megjegyzése:**

A berendezések megfelelő működéséhez és a termékek károsodásának megakadályozásához:

* Az eszköz hátoldalán található RJ45-portok csak Ethernet-kapcsolatok esetén érhetők el. Ezek nem csatlakoztathatók távközlési hálózathoz.
* Ügyeljen arra, hogy az eszközt egy olyan állványon telepítse, amely képes egy előtérben futó hűtési terv befogadására.
* Az összes beépülőmodul-modul és üres lemez a Rendszerház részét képezi. Ezeket csak akkor távolíthatja el, ha a cserét azonnal hozzá lehet adni. A rendszer nem futtatható minden modul vagy üres hely nélkül.

## <a name="rack-system-precautions"></a>Állványrendszer-óvintézkedések
A következő biztonsági követelményeket kell figyelembe venni, amikor csatlakoztatja az eszközt egy rack szekrényben.

![Figyelmeztetés ikon ](./media/storsimple-safety/IC740879.png) ![ Tipp kockázati ikon ](./media/storsimple-safety/IC740886.png) **Figyelmeztetés!**

Egy tippből származó sérülés valószínűségének csökkentése:

* Az állvány kialakításának támogatnia kell a telepített házak teljes súlyozását, és olyan stabilizáló funkciókat kell tartalmaznia, amelyek megakadályozzák a rack kiengedését vagy a telepítés vagy a normál használat során történő leküldését.
* Állvány betöltése esetén töltse ki az állványt az alulról felfelé, majd a felülről lefelé.
* Ne csúsztassa el egyszerre egynél több bekerítést a rackből, hogy elkerülje a rackre való feltöltés veszélyét.

![Figyelmeztető ikon ](./media/storsimple-safety/IC740879.png) ![ áramütés ikonja ](./media/storsimple-safety/IC740882.png) **Figyelmeztetés!**

A sérülés, az áramütés vagy a halál valószínűségének csökkentése:

* Az állványnak biztonságos, elektromos elosztó rendszerrel kell rendelkeznie. Meg kell adnia a bekerítés aktuális védelmét, és nem szabad túlterhelni a telepített házak teljes számával. Meg kell figyelni a névtábla elektromos energiafogyasztásának minősítését.
* Az elektromos elosztó rendszernek megbízható terepet kell biztosítania minden egyes ház számára a rackben.
* Az elektromos elosztó rendszer kialakításának figyelembe kell vennie a teljes, az összes energiaellátási szolgáltatásból származó összes beszivárgást az összes házban. Vegye figyelembe, hogy az egyes házakban az egyes tápegységek az 1,0 mA maximális 264, 60 Hz. A rack a "nagy SZIVÁRGÁSú ÁRAMmal" való címkézést igényelhet. A földi (föld) kapcsolat elengedhetetlen a szolgáltatás összekapcsolásához. "
* Az állványnak, ha a házakkal van konfigurálva, meg kell felelnie az UL 60950-1 és az IEC 60950-1/EN 60950-1 biztonsági követelményeinek.

![Értesítés ikonjának ](./media/storsimple-safety/IC740881.png) **megjegyzése:**

A rack rendszer megfelelő hűtéséhez:

* Győződjön meg arról, hogy az állvány kialakítása figyelembe veszi a 35 Celsius fok (95 fokos Fahrenheit) működési környezeti hőmérsékletének maximális mennyiségét. Tartsa meg a helyet, ahol a rackrendszer bemenő állapotban van, és győződjön meg arról, hogy az adatközpontban elegendő a légáram az AC-nyílásból.
* A rendszer alacsony nyomású, hátsó kimeneti telepítéssel működik (a hátsó ajtók által létrehozott ellennyomás, amely nem haladja meg az 5 Pascal [0,5 mm-es vízmérőt]).

## <a name="power-cooling-module-pcm-precautions"></a>Power hűtési modul (PCM) óvintézkedések

Az eszköz két PCMs való működésre lett tervezve. Minden PCMs van egy tápegység és egy Kéttengelyes ventilátor. Kritikus feltétele esetén a rendszer lehetővé teszi egy energiaellátás meghibásodását, miközben folytatja a normál működést. A két PCMs (és így az energiaellátási szolgáltatásokat) mindig telepíteni kell. Egyetlen PCM nem biztosít redundáns teljesítményt. Ezért az is előfordulhat, hogy akár egy PCM meghibásodása állásidőt vagy adatvesztést eredményezhet.

![Figyelmeztető ikon ](./media/storsimple-safety/IC740879.png) ![ áramütés ikonja ](./media/storsimple-safety/IC740882.png) **Figyelmeztetés!**

A sérülés, az áramütés vagy a halál valószínűségének csökkentése:

* Ne távolítsa el a borítókat a PCM-ből. Áramütést jelent a belső áramütés. A PCM visszaküldéséhez és a csere megszerzéséhez [forduljon Microsoft ügyfélszolgálatahoz](./storsimple-8000-contact-microsoft-support.md).

![Értesítés ikonjának ](./media/storsimple-safety/IC740881.png) **megjegyzése:**

A berendezések megfelelő működéséhez és a termékek károsodásának megakadályozásához:

* A meghibásodott PCM-t 24 órán belül le kell cserélni. Miután eltávolított egy PCM-t a pótláshoz, a pótlást az eltávolítást követő 10 percen belül el kell végezni.
* Ne távolítsa el a PCM-t, hacsak nem lehet azonnal telepíteni a cserét. A bekerítést nem szabad az összes modul nélkül üzemeltetni.

## <a name="electrostatic-discharge-esd-precautions"></a>Elektrosztatikus kisülés (ESD) óvintézkedések

![Értesítés ikonjának ](./media/storsimple-safety/IC740881.png) **megjegyzése:**

Figyelje meg a következő ESD-vel kapcsolatos óvintézkedéseket.

* Győződjön meg arról, hogy telepítette és ellenőrizte a megfelelő antisztatikus csuklót vagy boka szíjat.
* A modulok és összetevők kezelésekor figyelje meg az összes hagyományos ESD-óvintézkedést.
* Ne lépjen kapcsolatba a hátlap-összetevőkkel és a modul-összekötővel.
* A jótállás nem vonatkozik az ESD-károsodásra.

## <a name="battery-disposal-precautions"></a>Akkumulátor-kivezetési óvintézkedések

A tápegység egy speciális akkumulátort használ, amely az ideiglenes, rövid távú áramkimaradások során biztosítja a memória tartalmát. Az akkumulátor a PCM-ben van. Tartsa szem előtt az alábbi információkat az akkumulátorról.

![Figyelmeztetés ikon ](./media/storsimple-safety/IC740879.png) **Figyelmeztetés!**

A rövidnadrág, tűz, robbanás, sérülés vagy halál kockázatának csökkentése:

* A felhasznált elemek a nemzeti/regionális előírásoknak megfelelően vannak használatban.
* A 60 Celsius-fok (140 fokos Fahrenheit) vagy az elégetése után ne szerelje szét, ne törje fel vagy melegítsük fel. Cserélje le a PCM akkumulátort csak a megadott akkumulátorra. Egy másik akkumulátor használata a tűz vagy a robbanás kockázatát jelenthetheti.
* Ha ezek el vannak távolítva az áramellátásból, használja az akkumulátorok védelmi végpontját.

![Értesítés ikonjának ](./media/storsimple-safety/IC740881.png) **megjegyzése:**

Az akkumulátorok légi úton történő szállítása vagy egyéb módon történő átvitele esetén kövesse az IATA lítium akkumulátor-útmutató dokumentumát [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Miután áttekintette ezeket a biztonsági értesítéseket, a következő lépés az eszköz kicsomagolása, összekötése és csatlakoztatása.

## <a name="next-steps"></a>Következő lépések

* Az 8100-es eszközökön válassza a [StorSimple 8100 eszköz telepítése](storsimple-8100-hardware-installation.md)lehetőséget.
* Az 8600-es eszközökön válassza a [StorSimple 8600 eszköz telepítése](storsimple-8600-hardware-installation.md)lehetőséget.