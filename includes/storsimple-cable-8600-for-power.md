---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179155"
---
#### <a name="to-cable-your-device-for-power"></a>Eszköz csatlakoztatása a powerhez
> [!NOTE]
> A StorSimple-eszközön mindkét ház redundáns PCMs tartalmaz. A magas rendelkezésre állás biztosítása érdekében a PCMs a különböző áramforrásokhoz kell telepíteni és csatlakoztatni.
> 
> 

1. Győződjön meg arról, hogy az összes PCMs lévő energiaellátási kapcsolók a kikapcsolt állapotban vannak.
2. Az elsődleges házban kapcsolja össze a tápkábeleket mindkét PCMs. A tápkábelek az alábbi Power kábelezési diagramon piros színnel vannak azonosítva.
3. Győződjön meg arról, hogy az elsődleges kamra két PCMs külön áramforrást használ.
4. Csatlakoztassa a tápkábelt az állvány elosztási egységéhez a Power kábelezési diagramon látható módon.
5. Ismételje meg a 2 – 4. lépést a EBOD ház esetében.
6. Kapcsolja be a EBOD-burkolatot úgy, hogy az egyes PCM-ket a bekapcsolt állapotba helyezi.
7. Ellenőrizze, hogy be van-e kapcsolva a EBOD ház a EBOD vezérlő hátoldalán található zöld LED-ek bekapcsolásával.
8. Kapcsolja be az elsődleges bekerítést úgy, hogy az egyes PCM-kapcsolókat bekapcsolja a be állásba.
9. Ellenőrizze, hogy a rendszer be van-e kapcsolva, és győződjön meg arról, hogy az eszköz vezérlő LED-je bekapcsolva
10. Győződjön meg arról, hogy a EBOD-vezérlő és az vezérlő közötti kapcsolat aktív, és ellenőrizze, hogy a EBOD vezérlő SAS-portja melletti négy LED zöld színű-e.
    
    > [!IMPORTANT]
    > A rendszer magas rendelkezésre állásának biztosítása érdekében javasoljuk, hogy szigorúan tartsa be az alábbi ábrán látható energiaellátási kábelezési sémát.
    > 
    > 
    
    ![A 4U-eszköz csatlakoztatása a powerhez](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Energiaellátási kábelek**
    
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
    | 9 |PDU |

