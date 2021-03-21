---
title: Az akkumulátor cseréje Microsoft Azure StorSimple 8000 sorozatú eszközön
description: Ismerteti, hogyan lehet eltávolítani, lecserélni és karbantartani a Backup akkumulátor-modult a StorSimple-eszközön.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 17a6a07d117557cdee23634bd211a14cf90d49e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023771"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>A tartalék akkumulátor modul cseréje a StorSimple-eszközön

## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple eszköz elsődleges bekerítési teljesítmény-és hűtési moduljának (PCM) további akkumulátor-csomaggal rendelkezik. Ez a csomag lehetővé teszi, hogy a StorSimple-eszköz képes legyen az adatmentésre, ha áramkimaradás történt az elsődleges bekerítésnél. Ezt az akkumulátort a *Backup akkumulátor moduljának* nevezzük. A Backup akkumulátor modul csak a StorSimple-eszköz elsődleges bekerítéséhez létezik (a EBOD-ház nem tartalmaz Backup akkumulátor-modult).

Ez az oktatóanyag a következőket ismerteti:

* A biztonsági mentési akkumulátor modul eltávolítása
* Új biztonsági mentési akkumulátor modul telepítése
* A biztonsági mentési akkumulátor moduljának karbantartása

> [!IMPORTANT]
> A Backup akkumulátor-modul eltávolítása és cseréje előtt tekintse át a biztonsági tudnivalókat a [StorSimple hardveres összetevők cseréjének bemutatása](storsimple-8000-hardware-component-replacement.md)című témakörben.


## <a name="remove-the-backup-battery-module"></a>A biztonsági mentési akkumulátor modul eltávolítása
A StorSimple-eszköz Backup akkumulátor-modulja egy mezőre cserélhető egység. A PCM-ben való telepítése előtt az akkumulátor-modult az eredeti csomagolásban kell tárolni. A biztonsági mentési akkumulátor eltávolításához hajtsa végre az alábbi lépéseket.

#### <a name="to-remove-the-backup-battery-module"></a>A Backup akkumulátor moduljának eltávolítása
1. A Azure Portal lépjen a StorSimple Eszközkezelő szolgáltatás paneljére. Nyissa meg az **eszközök** elemet, majd válassza ki az eszközt az eszközök listájából. Navigáljon a  >  **hardver állapotának** figyeléséhez. A **megosztott összetevők** területen tekintse meg az akkumulátor állapotát.
2. Azonosítsa azt a PCM-t, amelyben az akkumulátor meghiúsult. Az 1. ábrán az StorSimple-eszköz hátoldala látható.
   
    ![Az eszköz elsődleges bekerítési moduljainak hátlapja](./media/storsimple-battery-replacement/IC740994.png)
   
    **1. ábra** Az elsődleges eszköz hátoldala, amely a PCM és a vezérlő modulokat mutatja
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Vezérlő 0 |
   | 4 |Vezérlő 1 |
   
    Ahogy azt a 3. ábra is mutatja, az **akkumulátor meghibásodásának** megfelelő PCM 0 LED-es figyelési mutatót kell megvilágítani.
   
    ![Az eszköz PCM monitorozási jelző LED-ek hátlapja](./media/storsimple-battery-replacement/IC740992.png)
   
    **2. ábra** A monitorozási kijelző LED-je a PCM hátoldalán látható
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |AC áramszünet |
   | 2 |Ventilátor meghibásodása |
   | 3 |Akkumulátor meghibásodása |
   | 4 |PCM OK |
   | 5 |EGYENÁRAMÚ áramszünet |
   | 6 |Akkumulátor állapota Kifogástalan |
3. A PCM hibás elemmel való eltávolításához kövesse a [PCM eltávolítása](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm)című témakör lépéseit.
4. Ha a PCM el lett távolítva, a következő ábrán látható módon emelje le és forgassa el az akkumulátor-modul fogantyúját, és húzza fel az akkumulátor eltávolításához.
   
    ![Akkumulátor eltávolítása a PCM-ből](./media/storsimple-battery-replacement/IC741019.png)
   
    **3. ábra** Az akkumulátor eltávolítása a PCM-ből
5. Helyezze a modult a kihelyezhető egység csomagolása mezőbe.
6. A megfelelő karbantartás és felügyelet érdekében visszaküldi a hibás egységet a Microsoftnak.

## <a name="install-a-new-backup-battery-module"></a>Új biztonsági mentési akkumulátor modul telepítése
A következő lépésekkel telepítheti a helyettesítő akkumulátor-modult a PCM-ben a StorSimple-eszköz elsődleges borításában.

#### <a name="to-install-the-battery-module"></a>Az akkumulátor moduljának telepítése
1. Helyezze a Backup akkumulátor modulját a PCM megfelelő tájolására.
2. Tartsa lenyomva az akkumulátor-modul leíróját, hogy az összekötőt helyezze el.
3. Cserélje le a PCM-t az elsődleges házban a StorSimple- [eszköz energiagazdálkodási és hűtési moduljának cseréje](storsimple-8000-power-cooling-module-replacement.md)című rész útmutatását követve.
4. A csere befejezése után lépjen az eszközre, majd lépjen   >  a Azure Portal a **hardver állapotának** figyelése elemre. Ellenőrizze az akkumulátor állapotát, és győződjön meg arról, hogy a telepítés sikeres volt. A zöld állapot azt jelzi, hogy az akkumulátor kifogástalan állapotban van.

## <a name="maintain-the-backup-battery-module"></a>A biztonsági mentési akkumulátor moduljának karbantartása
A StorSimple-eszközön a Backup akkumulátor modul a vezérlő áramellátását is biztosítja az áramkimaradás esetén. Lehetővé teszi a StorSimple-eszköz számára a kritikus fontosságú adatmentést az ellenőrzött módon történő leállítás előtt. A PCMs két, teljes mértékben felszámított akkumulátort képes kezelni két egymást követő veszteséges eseményt.

A Azure Portal a **monitor** panelen a **hardver állapota** jelzi, hogy az akkumulátor meghibásodik-e, vagy közeledik-e az élettartam. Az akkumulátor állapotát a **PCM 0 elemnél** vagy a **PCM 1** elemnél, a **megosztott összetevők** területen kell megadni. Ez a panel a teljes életciklusra kiterjedően **lecsökkentett** állapotot mutat, és **nem tudta** elérni az élettartam végét.

> [!NOTE]
> Az akkumulátor **nem** tud jelentést készíteni, ha egyszerűen fel kell számolni.


Ha a **csökkentett teljesítményű** állapot jelenik meg, a következő műveletet javasoljuk:

* Előfordulhat, hogy a rendszer egy közelmúltbeli áramkimaradást észlelt, vagy az akkumulátorok rendszeres karbantartás alatt állnak. A folytatás előtt 12 órával figyelje a rendszerét.
  
  * Ha az **állapot továbbra** is 12 órányi folyamatos kapcsolódás után, a vezérlőkkel és a PCMs fut, akkor az akkumulátort le kell cserélni. Kérjük, [lépjen kapcsolatba Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) a tartalék akkumulátor-modul cseréjéhez.
  * Ha az állapot 12 óra elteltével válik aktívvá, az akkumulátor működőképes lesz, és csak karbantartási díj szükséges.
* Ha nem fordult elő áramkimaradás, és a PCM be van kapcsolva, és csatlakoztatva van a hálózati adapterhez, az akkumulátort le kell cserélni. A biztonsági mentési akkumulátor-modul megrendeléséhez [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md) .

> [!IMPORTANT]
> A meghibásodott akkumulátor a nemzeti és regionális előírásoknak megfelelően.

## <a name="next-steps"></a>Következő lépések
További információ a [StorSimple hardveres összetevők cseréjéről](storsimple-8000-hardware-component-replacement.md).

