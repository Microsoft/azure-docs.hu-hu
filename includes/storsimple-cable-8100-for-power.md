---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db2104020e9478b1fedf68e1c9467f75e16044e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179156"
---
#### <a name="to-cable-for-power"></a>A tápkábelhez
1. Győződjön meg arról, hogy az egyes energiagazdálkodási és hűtési modulok (PCMs-EK) energiaellátási kapcsolói a kikapcsolt állapotban vannak.
2. Kapcsolja össze a tápkábeleket az elsődleges házban található összes PCMs.
3. Csatlakoztassa a tápkábeleket a rack Power Distribution Units (PDU) szolgáltatáshoz az alábbi ábrán látható módon. Győződjön meg arról, hogy a két PCMs külön áramforrást használ.
   
   > [!IMPORTANT]
   > A rendszer magas rendelkezésre állásának biztosítása érdekében javasoljuk, hogy szigorúan tartsa be az alábbi ábrán látható energiaellátási kábelezési sémát. 
   > 
   > 
   
    ![A 2U-eszköz csatlakoztatása a powerhez](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **8100-eszközön lévő hálózati kábelek**
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |Vezérlő 1 |
   | 3 |Vezérlő 0 |
   | 4 |PCM 1 |
   | 5 |PDU |
4. A rendszer bekapcsolásához fordítsa a Power switcheket mindkét PCMs a ON pozícióra.

