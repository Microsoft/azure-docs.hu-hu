---
title: StorSimple 8600 EBOD vezérlő cseréje | Microsoft Docs
description: Elmagyarázza, hogyan távolíthat el és cserélhet le egy vagy mindkét EBOD-vezérlőt egy StorSimple 8600-eszközön.
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
ms.openlocfilehash: bdafb4288bf818928aba43d09f15d4dd75092b24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014839"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>EBOD-vezérlő cseréje a StorSimple-eszközön

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan lehet lecserélni egy hibás EBOD-vezérlő modult a Microsoft Azure StorSimple eszközön. Egy EBOD vezérlő modul lecseréléséhez a következőket kell tennie:

* A hibás EBOD-vezérlő eltávolítása
* Új EBOD-vezérlő telepítése

Mielőtt elkezdené, vegye figyelembe a következő információkat:

* Üres EBOD-modulokat kell beszúrni az összes fel nem használt tárolóhelybe. A ház nem fog megfelelően kihűlni, ha a tárolóhely nyitva van.
* A EBOD vezérlő könnyen cserélhető, és eltávolítható vagy lecserélhető. Ne távolítsa el a meghibásodott modult, amíg nincs csere. A helyettesítési folyamat elindításakor 10 percen belül be kell fejeznie.

> [!IMPORTANT]
> A StorSimple-összetevők eltávolításának vagy cseréjének megkísérlése előtt ellenőrizze, hogy megtekinti-e a [biztonsági ikon konvencióit](storsimple-8000-safety.md#safety-icon-conventions) és az egyéb [biztonsági óvintézkedéseket](storsimple-8000-safety.md).

## <a name="remove-an-ebod-controller"></a>EBOD-vezérlő eltávolítása
Mielőtt lecseréli a sikertelen EBOD vezérlő modult a StorSimple-eszközön, győződjön meg arról, hogy a másik EBOD vezérlő modul aktív és fut. A következő eljárás és táblázat ismerteti, hogyan távolíthatja el a EBOD vezérlő modult.

#### <a name="to-remove-an-ebod-module"></a>EBOD-modul eltávolítása
1. Nyissa meg az Azure Portalt.
2. Nyissa meg az eszközt, és navigáljon a **Beállítások**  >  **hardver állapota** elemhez, és ellenőrizze, hogy az aktív EBOD vezérlő moduljának állapota zöld-e, és hogy a sikertelen EBOD vezérlő modulhoz tartozó LED piros színű-e.
3. Keresse meg a sikertelen EBOD vezérlő modult az eszköz hátoldalán.
4. Távolítsa el azokat a kábeleket, amelyek a EBOD-vezérlő modult csatlakoztatják a vezérlőhöz, mielőtt a EBOD-modult a rendszeren kívülre tenné.
5. Jegyezze fel a vezérlőhöz csatlakoztatott EBOD vezérlő modul pontos SAS-portját. A EBOD modul cseréje után vissza kell állítania a rendszer erre a konfigurációra.
   
   > [!NOTE]
   > Ez általában az A port, amely a következő ábrán **gazdagépként** van megjelölve.
   
    ![EBOD-vezérlő hátlapja](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **1. ábra** A EBOD modul hátoldala
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Hiba LED |
   | 2 |Power LED |
   | 3 |SAS-összekötők |
   | 4 |SAS-LED-EK |
   | 5 |Csak gyári portok használata |
   | 6 |A port (a gazdagép) |
   | 7 |B port (a gazdagépen) |
   | 8 |C port (csak gyári használatra) |

## <a name="install-a-new-ebod-controller"></a>Új EBOD-vezérlő telepítése
A következő eljárás és táblázat ismerteti, hogyan telepíthet egy EBOD vezérlő modult a StorSimple-eszközön.

#### <a name="to-install-an-ebod-controller"></a>EBOD-vezérlő telepítése
1. Győződjön meg róla, hogy a EBOD-eszköz kárt okoz, különösen az illesztőfelület-összekötőn. Ne telepítse az új EBOD-vezérlőt, ha bármelyik PIN-kód be van hajlítva.
2. Ha a zárolások nyitott helyzetben vannak, csúsztassa a modult a házba, amíg a zárak bekerülnek.
   
    ![EBOD-vezérlő telepítése](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **2. ábra**  A EBOD vezérlő moduljának telepítése
3. Zárjuk be a zárat. A zárolás bevonásakor egy kattintással kell megjelennie.
   
    ![EBOD zárolásának felszabadítása](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **3. ábra**  A EBOD modul zárolásának bezárása
4. A kábelek újracsatlakoztatása. A csere előtt található pontos konfigurációt használja. A kábelek csatlakoztatásával kapcsolatos részletekért tekintse meg a következő diagramot és táblázatot.
   
    ![A 4U-eszköz csatlakoztatása a powerhez](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **4. ábra**. Kábelek újracsatlakoztatása
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Elsődleges ház |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Vezérlő 0 |
   | 5 |Vezérlő 1 |
   | 6 |EBOD vezérlő 0 |
   | 7 |1. EBOD-vezérlő |
   | 8 |EBOD ház |
   | 9 |Energiaellátási egységek |

## <a name="next-steps"></a>Következő lépések
További információ a [StorSimple hardveres összetevők cseréjéről](storsimple-8000-hardware-component-replacement.md).

