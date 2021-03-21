---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8b827a80059cecadd7b76263f13dd4a0d141fc7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95562195"
---
#### <a name="to-create-a-cloud-appliance"></a>Felhőalapú berendezés létrehozása

1. Az Azure portálon lépjen a **StorSimple-eszközkezelő** szolgáltatáshoz.
2. Lépjen az **Eszközök** panelre. A szolgáltatásösszegző panel parancssorában kattintson a **Felhőalapú berendezés létrehozása** elemre.
    ![StorSimple felhőalapú berendezés létrehozása](./media/storsimple-8000-create-cloud-appliance-u2/sca-create1.png)
3. A **Felhőalapú berendezés létrehozása** párbeszédpanelen adja meg a következő adatokat.
   
    ![StorSimple – 2. Felhőbeli berendezés létrehozása](./media/storsimple-8000-create-cloud-appliance-u2/sca-create2m.png)
   
   1. **Név** – A felhőalapú berendezés egyedi neve.
   2. **Modell** – Válassza ki a felhőalapú berendezés modelljét. A 8010-es eszköz 30 TB Standard szintű, a 8020-as pedig 64 TB Prémium szintű tárhelyet biztosít. Ha biztonsági mentésekből szeretne elemszintű lekérési forgatókönyveket telepíteni, a 8010-es modellt adja meg. A 8020-as modellel nagy teljesítményű, alacsony késleltetésű munkaterheléseket telepíthet, vagy használhatja vészhelyreállítási másodlagos eszközként.
   3. **Verzió** – Válassza ki a felhőalapú berendezés verzióját. A verzió a felhőalapú készülék létrehozásához használt virtuális lemezkép verziójának felel meg. Mivel a felhőalapú berendezés verziója határozza meg, hogy melyik fizikai eszköz használható a feladatátvétel vagy klónozás forrásaként, fontos, hogy a felhőalapú berendezés megfelelő verzióját hozza létre.
   4. **Virtuális hálózat** – Adja meg a felhőalapú berendezéssel használni kívánt virtuális hálózatot. Ha Premium Storage tárolást használ, olyan virtuális hálózatot válasszon, amelyet a Premium szintű Storage-fiók támogat. A nem támogatott virtuális hálózatok szürkén jelennek meg a legördülő listában. Ha nem támogatott virtuális hálózatot választ ki, a rendszer figyelmezteti.
   5. **Alhálózat** – A kiválasztott virtuális hálózat alapján a legördülő lista megjeleníti a társított alhálózatokat. Rendeljen egy alhálózatot a felhőalapú berendezéshez.
   6. **Tárfiók** – Válassza ki, hogy melyik tárfiókban kívánja tárolni a felhőalapú berendezés rendszerképét az üzembe helyezés alatt. Ennek a tárfióknak ugyanabban a régióban kell lennie, ahol a felhőalapú berendezés és a virtuális hálózat is található. A tárfiókot sem fizikai eszköz, sem felhőalapú berendezés nem használhatja adattároláshoz. Alapértelmezés szerint erre a célra létrejön egy új tárfiók. Ha azonban tudja, hogy már van egy tárfiókja, amely alkalmas erre a célra, kiválaszthatja a listából. Ha létrehoz egy prémium szintű felhőalapú berendezést, a legördülő lista csak a Prémium szintű Storage-fiókokat jeleníti meg.
      
      > [!NOTE]
      > A felhőalapú berendezés csak az Azure-tárfiókokkal működik.
    
   7. A jelölőnégyzet bejelölésével jelezze, hogy tudomásul veszi, a felhőalapú berendezésen lévő adatokat egy Microsoft-adatközpont tárolja.
       * Amikor csak fizikai eszközt használ, a titkosítási kulcs az eszközön található, ezért a Microsoft nem tudja feloldani a titkosítást.

       * Amikor felhőalapú berendezést használ, a titkosítási és a visszafejtési kulcs tárolása is a Microsoft Azure-ban történik. További információkért tekintse meg a [felhőalapú berendezések használatára vonatkozó biztonsági szempontokat](../articles/storsimple/storsimple-8000-security.md).
   8. Kattintson a **Létrehozás** elemre a felhőalapú berendezés üzembe helyezéséhez. Az eszköz kiépítése körülbelül 30 percet vesz igénybe. A felhőalapú berendezés sikeres létrehozásáról értesítést kap. Lépjen az Eszközök panelre, ahol az eszközlista frissül, és megjelenik a felhőalapú berendezés. A berendezés állapota **Beállításra kész**.
      
      ![A StorSimple Cloud Appliance beállításra kész](./media/storsimple-8000-create-cloud-appliance-u2/sca-create3.png)