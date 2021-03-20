---
title: StorSimple 8000 sorozatú hardver-összetevők cseréje | Microsoft Docs
description: Ismerteti, hogyan lehet biztonságosan lecserélni egy StorSimple-eszköz PCMs, akkumulátorát, vezérlő moduljait, EBOD, lemezmeghajtóit és alvázát.
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
ms.custom: ''
ms.openlocfilehash: 12ab5a9598cc0222f5a3e64985be2e2ea9e7e2fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014856"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Hardver-összetevő cseréje a StorSimple 8000 Series eszközön

## <a name="overview"></a>Áttekintés
A hardveres összetevők cseréjének oktatóanyagai ismertetik az Microsoft Azure StorSimple 8000 sorozatú eszköz hardveres összetevőit, valamint az eltávolításához és lecseréléséhez szükséges lépéseket. Ez a cikk a biztonsági ikonokat ismerteti, és megjeleníti a részletes oktatóanyagokat, és felsorolja a cserélhető összetevőket.

> [!IMPORTANT]
> A StorSimple-összetevők eltávolításának vagy cseréjének megkísérlése előtt ellenőrizze, hogy megtekinti-e a [biztonsági ikon konvencióit](#safety-icon-conventions) és az egyéb [biztonsági óvintézkedéseket](storsimple-8000-safety.md).

### <a name="safety-icon-conventions"></a>Biztonsági ikon konvenciói

Az alábbi táblázat az oktatóanyagokban használt biztonsági ikonokat ismerteti. Ügyeljen rá, hogy az eszközök összetevőinek eltávolításához és lecseréléséhez szükséges lépéseket követve vegye figyelembe ezeket a biztonsági ikonokat.

| Ikon | Szöveg | További információ |
|:--- |:--- |:--- |
| ![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Warning.png) |**VESZÉLY!** |Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – halált vagy súlyos sérülést eredményezhet. Ez a jelzési szó a legszélsőségesebb helyzetekre korlátozódik. |
| ![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Warning.png) |**Figyelmeztetés!** |Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – halált vagy súlyos sérülést eredményezhet. |
| ![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Caution.png) |**Vigyázat!** |Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – kisebb vagy mérsékelt sérülést eredményezhet. |
| ![Értesítés ikon](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**MEGJEGYZČS** |A fontosnak tartott információkat jelöli, de nem kapcsolódik a veszélyforráshoz. |
| ![Áramütés ikon](./media/storsimple-hardware-component-replacement/Electric.png) |**Áramütés veszélye** |Nagy feszültséget jelez. |
| ![Nagy súly ikon](./media/storsimple-hardware-component-replacement/Weight.png) |**Nagy súly** | |
| ![Nincs felhasználó által szervizelhető rész ikon](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Nincsenek felhasználó által szervizelhető részek** |A megfelelő képzés hiányában ne legyen hozzáférés. |
| ![Olvasási utasítások ikonja](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Először olvassa el az összes utasítást** | |
| ![Tipp veszélyességének ikonja](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Tipp kockázati tényező** | |

### <a name="before-you-begin"></a>Előkészületek

Ismerkedjen meg a jelen oktatóanyagban használt eszközével és biztonsági ikonjaival kapcsolatos biztonsági információkkal. A teljes körű információkért lépjen a [biztonságos telepítés és a StorSimple-eszköz üzemeltetéséhez](storsimple-8000-safety.md) . Ügyeljen arra, hogy a StorSimple-eszköz kezelése előtt tekintse át a [biztonsági óvintézkedéseket](storsimple-8000-safety.md#handling-precautions) .

Az összetevők cseréjének megkísérlése előtt vegye figyelembe a következő információkat.

![Figyelmeztető ikon ](./media/storsimple-hardware-component-replacement/Warning.png) ![ áramütés ikonja ](./media/storsimple-hardware-component-replacement/Electric.png) **Figyelmeztetés!**

* A StorSimple-eszköz moduljainak és összetevőinek kezelésekor az elektrosztatikus kisülés vagy a antisztatikus matrac használatával megfelelően kihasználhatja magát.
* Ne érintsen semmilyen áramkört. A megadott fogópontok és útmutatók használata az áramkört esetlegesen fellépő összetevők kezelése során.

![Figyelmeztető ikon figyelmeztetési ikonjának megjegyzése ](./media/storsimple-hardware-component-replacement/Warning.png) ![ ](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **:**

Egy modul cseréjekor **Soha ne hagyjon üres öbölben a ház hátsó részén**. Egy helyettesítő vagy üres modul beszerzése a probléma részének eltávolítása előtt.

## <a name="hardware-component-replacement-procedures"></a>Hardver-összetevők helyettesítési eljárásai

Az StorSimple 8000 sorozatú eszköz több beépülőmodul-modulból áll az elsődleges és/vagy EBOD-házakban. Az 8100 egyetlen elsődleges bekerítéssel rendelkezik, míg a 8600 egy olyan kettős bekerítésű eszköz, amely elsődleges bekerítéssel és egy EBOD ház.

Az eszköz fő hardveres összetevői az alábbi táblázatokban vannak összefoglalva. Kattintson a **helyettesítő eljárás** oszlopban található hivatkozásra a kapcsolódó oktatóanyaghoz való ugráshoz.

| Összetevők | # Jelen | Beépülő modul? | Helyettesítési eljárás |
|:--- |:--- |:--- |:--- |
| Váz |1 |No |[Az alváz cseréje a StorSimple-eszközön](storsimple-8000-chassis-replacement.md) |
| Elsődleges vezérlők |2 |Yes |[Vezérlő modul cseréje a StorSimple-eszközön](storsimple-8000-controller-replacement.md) |
| 764W teljesítmény-és hűtési modulok (PCMs) |2 |Yes |[Az energiaellátási és hűtési rendszer cseréje a StorSimple-eszközön](storsimple-8000-power-cooling-module-replacement.md) |
| Tartalék akkumulátor |2 |Yes |[A tartalék akkumulátor modul cseréje a StorSimple-eszközön](storsimple-8000-battery-replacement.md) |
| Lemezmeghajtók |12 |Yes |[Lemezmeghajtó cseréje a StorSimple-eszközön](storsimple-8000-disk-drive-replacement.md) |

**1. táblázat** Az elsődleges házban található hardver-összetevők

Az elsődleges bekerítés és a EBOD ház különbözik az I/O-modulokban. Emellett a PCMs eltérő teljesítményű. Az elsődleges ház PCMs a 764 W, míg a EBOD-ház a 580 W. Az elsődleges ház PCMs is tartalmaz egy tartalék akkumulátor-modult.

| Összetevők | # Jelen | Beépülő modul? | Helyettesítési eljárás |
|:--- |:--- |:--- |:--- |
| Váz |1 |No |[Az alváz cseréje a StorSimple-eszközön](storsimple-8000-chassis-replacement.md) |
| EBOD-vezérlők |2 |Yes |[EBOD-vezérlő cseréje a StorSimple-eszközön](storsimple-8000-ebod-controller-replacement.md) |
| 580W teljesítmény-és hűtési modulok (PCMs) |2 |Yes |[Az energiaellátási és hűtési rendszer cseréje a StorSimple-eszközön](storsimple-8000-power-cooling-module-replacement.md) |
| Lemezmeghajtók |12 |Yes |[Lemezmeghajtó cseréje a StorSimple-eszközön](storsimple-8000-disk-drive-replacement.md) |

**2. táblázat** A EBOD ház hardveres összetevői

Az eszközön található beépülőmodul-modulok a következő első és hátsó diagramokon vannak kiemelve. Ezen diagramok segítségével meghatározhatja a különböző beépülő modulok helyét, ha cserére van szükség. Az első ábrán a lemezmeghajtók láthatók, a EBOD-ház hátsó diagramjai és az elsődleges ház a beépülő modul moduljait jeleníti meg.

![Lemezmeghajtóval rendelkező eszköz előlapja](./media/storsimple-hardware-component-replacement/IC741028.png)

**1. ábra** Az eszköz előtt

| Címke | Description |
|:--- |:--- |
| 0 - 11 |Lemezmeghajtók (összesen 12) |

Mind az elsődleges, mind a EBOD-ház rendelkezik meghajtó-szolgáltatói modulokkal. A váz tizenkét 3,5 "lemezmeghajtót helyez el 3 – 4 formátumban rendezve.

![Az eszköz elsődleges bekerítési moduljainak hátlapja](./media/storsimple-hardware-component-replacement/IC740994.png)

**2. ábra** Az elsődleges ház hátoldala

| Címke | Leírás |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Vezérlő 0 |
| 4 |Vezérlő 1 |

![Az eszköz EBOD bekerítéséhez tartozó beépülőmodul-modulok hátlapja](./media/storsimple-hardware-component-replacement/IC769599.png)

**3. ábra** A EBOD ház hátoldala

| Címke | Leírás |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD vezérlő 0 |
| 4 |1. EBOD-vezérlő |

## <a name="field-replaceable-units"></a>Mezőre cserélhető egységek

A StorSimple-eszközhöz a következő mezők cserélhető egységei (FRUs) érhetők el:

* Váz (az integrált operatív panelt is beleértve)
* 764 W AC PCM
* 580 W AC PCM
* Merevlemez-meghajtó szolgáltatói modullal
* Vezérlő modul
* EBOD vezérlő modul
* Tartalék akkumulátor modul
* Rack-csatlakoztatási vasúti készlet

A helyettesítő egységek bármelyikének rendeléséhez [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md) .

## <a name="next-steps"></a>Következő lépések

Tekintse át az összes [biztonsági információt](storsimple-8000-safety.md) , mielőtt megpróbálja cserélni a StorSimple hardver-összetevőt.
