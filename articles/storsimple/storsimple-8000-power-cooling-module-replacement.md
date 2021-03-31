---
title: Cserélje le a PCM-t a StorSimple 8000 Series eszközön | Microsoft Docs
description: A cikk azt ismerteti, Hogyan távolítható el és cserélhető le a StorSimple-eszközön lévő energiagazdálkodási és hűtési modul (PCM)
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 045cec85174a88d1d608a4adc679461008852768
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514595"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Az energiaellátási és hűtési rendszer cseréje a StorSimple-eszközön
## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple eszköz energiagazdálkodási és hűtési modulja az elsődleges és EBOD-házakon keresztül vezérelt energiaellátási és hűtési ventilátorokat tartalmaz. Az egyes házakhoz csak egy PCM-modell van hitelesítve. Az elsődleges ház 764 W-os PCM-re van hitelesítve, a EBOD-ház pedig 580 W PCM minősítéssel rendelkezik. Bár az elsődleges bekerítés és a EBOD-ház PCMs eltérnek, a helyettesítési eljárás azonos.

Ez az oktatóanyag a következőket ismerteti:

* PCM eltávolítása
* Helyettesítő PCM telepítése

> [!IMPORTANT]
> A PCM eltávolítása és cseréje előtt tekintse át a [StorSimple hardver-összetevő cseréje](storsimple-8000-hardware-component-replacement.md)biztonsági információit.


## <a name="before-you-replace-a-pcm"></a>A PCM cseréje előtt
A PCM cseréje előtt vegye figyelembe a következő fontos problémákat:

* Ha a PCM tápegysége meghibásodik, hagyja meg a hibás modult, de távolítsa el a tápkábelt. A ventilátor továbbra is megkapja a beérkező áramellátást, és továbbra is megfelelő hűtést biztosít. Ha a ventilátor meghibásodik, a PCM-et azonnal le kell cserélni.
* A PCM eltávolítása előtt bontsa ki a Power elemet a PCM-ből a főkapcsoló kikapcsolásával (ahol van), vagy fizikailag távolítsa el a tápkábelt. Ez figyelmeztetést ad a rendszernek, hogy a leállítási küszöb a közeljövőben.
* Ellenőrizze, hogy a másik PCM működik-e a folyamatos rendszerművelethez a hibás PCM cseréje előtt. A hibás PCM-t a lehető leghamarabb egy teljesen működőképes PCM-re kell cserélni.
* A PCM-modul cseréje csak néhány percet vesz igénybe, de a meghibásodott PCM eltávolítása után 10 percen belül el kell végezni a túlmelegedés elkerülését.
* Vegye figyelembe, hogy a gyárból szállított 764 W PCM-modulok nem tartalmazzák a Backup akkumulátor-modult. A csere végrehajtása előtt el kell távolítania az akkumulátort a hibás PCM-ből, majd be kell szúrnia a helyettesítő modulba. További információkért lásd: [biztonsági mentési akkumulátor-modul eltávolítása és beillesztése](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>PCM eltávolítása
Kövesse ezeket az utasításokat, ha készen áll egy energiagazdálkodási és hűtési modul (PCM) eltávolítására a Microsoft Azure StorSimple eszközről.

> [!NOTE]
> A PCM eltávolítása előtt ellenőrizze, hogy megfelelő-e a pótlás (764 W az elsődleges bekerítéshez, vagy 580 W a EBOD ház esetében).

#### <a name="to-remove-a-pcm"></a>A PCM eltávolítása
1. A klasszikus Azure portálon kattintson a **beállítások > Monitor > hardver állapota** elemre. A **megosztott összetevők** alatt lévő PCM-összetevők állapotának megadásával azonosíthatja, hogy melyik PCM sikertelen:
   
   * Ha a PCM 0 áramellátása nem sikerült, akkor a **PCM 0-** as tápegység állapota piros lesz.
   * Ha a PCM 1 áramellátása meghiúsult, akkor a **PCM 1-es** számú tápegység állapota piros lesz.
   * Ha a PCM 1 ventilátora nem sikerült, a PCM 0 esetében a PCM 0 vagy a **hűtés 1** **hűtési** állapota piros lesz.
2. Keresse meg a meghibásodott PCM-t az elsődleges ház hátoldalán. Ha 8600 modellt futtat, akkor azonosítsa az elsődleges bekerítést úgy, hogy megtekinti a rendszeregység-azonosító sorszámát, amely az előlap LED-képernyőjén látható. Az elsődleges házban megjelenő alapértelmezett azonosító a **00**, míg a EBOD-házban megjelenő alapértelmezett egység azonosítója **01**. A következő diagram és táblázat ismerteti a LED megjelenítésének előlapját.
   
    ![A System ID az első OPS panelen](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **1. ábra** Az eszköz előlapja  
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Némítás gomb |
   | 2 |Rendszerteljesítmény |
   | 3 |Modul hibája |
   | 4 |Logikai hiba |
   | 5 |Egység AZONOSÍTÓjának megjelenítése |
3. Az elsődleges ház hátulján lévő monitorozási jelző LED-ek a hibás PCM azonosítására is használhatók. Tekintse meg a következő diagramot és táblázatot, amelyből megismerheti, hogyan használhatja a LED-eket a hibás PCM eléréséhez. Ha például a **ventilátornak** megfelelő LED nem világít, a ventilátor nem sikerült. Hasonlóképpen, ha az **AC** -hez tartozó LED nem világít, az áramellátás nem sikerült. 
   
    ![Az eszköz PCM monitorozási jelző LED-ek hátlapja](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **2. ábra** A PCM hátoldala kijelző LED-ekkel
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |AC áramszünet |
   | 2 |Ventilátor meghibásodása |
   | 3 |Akkumulátor meghibásodása |
   | 4 |PCM OK |
   | 5 |EGYENÁRAMÚ áramszünet |
   | 6 |Akkumulátor állapota Kifogástalan |
4. A meghibásodott PCM-modul megkereséséhez tekintse meg a StorSimple eszköz hátoldalának következő ábráját. A PCM 0 a bal oldalon, a PCM 1 pedig a jobb oldalon található. Az alábbi táblázat ismerteti a modulokat.
   
     ![Az eszköz elsődleges bekerítési moduljainak hátlapja](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **3. ábra** Eszköz hátoldala beépülő modulokkal 
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Vezérlő 0 |
   | 4 |Vezérlő 1 |
5. Kapcsolja ki a hibás PCM-t, és válassza le az áramellátási kábelt. Most már eltávolíthatja a PCM-t.
6. Fogadja el a kilincset és a PCM-fogantyú oldalát a hüvelykujj és a mutatóujj között, és a fogantyú megnyitásához nyomja össze őket.
   
    ![PCM-leíró megnyitása](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **4. ábra** A PCM-leíró megnyitása
7. A fogantyú megfogása és a PCM eltávolítása.
   
    ![Eszköz PCM eltávolítása](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **5. ábra** A PCM eltávolítása

## <a name="install-a-replacement-pcm"></a>Helyettesítő PCM telepítése
Az alábbi útmutatást követve telepítheti a PCM-t a StorSimple-eszközön. Győződjön meg arról, hogy a helyettesítő PCM telepítése előtt beszúrta a Backup akkumulátor-modult (csak 764 W PCMs vonatkozik). További információkért lásd: [biztonsági mentési akkumulátor-modul eltávolítása és beillesztése](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>A PCM telepítése
1. Ellenőrizze, hogy rendelkezik-e a megfelelő helyettesítő PCM-vel ehhez a bekerítéshez. Az elsődleges bekerítéshez 764 W PCM szükséges, és a EBOD bekerítéshez 580 W PCM szükséges. Ne kísérelje meg az 580 W PCM használatát az elsődleges házban, vagy a 764 W PCM-et a EBOD házban. Az alábbi képen látható, hogy hol azonosíthatók ezek az információk a PCM-re erősített címkén.
   
    ![Eszköz PCM-címkéje](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **6. ábra** PCM-címke
2. Keressen sérülést a ház számára, különös figyelmet fordítva az összekötőre. 
   
   > [!NOTE]
   > **Ne telepítse a modult, ha bármely összekötő PIN-kód behajlítva van.**
   > 
   > 
3. A nyitott pozícióban lévő PCM-fogantyúval csúsztassa a modult a házba.
   
    ![Az eszköz PCM telepítése](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **7. ábra** A PCM telepítése
4. Manuálisan zárjuk be a PCM-leírót. Egy kattintással kell hallania, hogy a leíró zárolása bekapcsolódjon.
   
   > [!NOTE]
   > Annak érdekében, hogy az összekötő-PIN-kódok bekapcsolódjanak, a kilincset a zárolás feloldása nélkül is finoman kihasználhatja. Ha a PCM kicsúszik, az azt jelenti, hogy a zárolás bezárult az összekötők bekapcsolása előtt.
   
5. Csatlakoztatja a tápkábeleket az áramforráshoz és a PCM-hez.
6. Gondoskodjon a törzs tehermentesítési bála védelméről.
7. Kapcsolja be a PCM-t.
8. Ellenőrizze, hogy a helyettesítés sikeres volt-e: a StorSimple Eszközkezelő szolgáltatás Azure Portaljában navigáljon az eszközhöz, majd a **beállítások > figyelje > hardver állapota lehetőséget**. A **megosztott összetevők** alatt a PCM állapotának zöldnek kell lennie.
   
   > [!NOTE]
   > Eltarthat néhány percig, amíg a helyettesítő PCM teljesen inicializálható.

## <a name="next-steps"></a>Következő lépések
További információ a [StorSimple hardveres összetevők cseréjéről](storsimple-8000-hardware-component-replacement.md).

