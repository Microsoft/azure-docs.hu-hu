---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 71f18cf8448060385ea38be9b2719b1ed545c5d2
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545299"
---
> [!NOTE] 
> A mennyiségi tárolóhoz társított titkosítási beállítások és a Storage-fiók hitelesítő adatai nem módosíthatók a létrehozása után.

#### <a name="to-modify-a-volume-container"></a>Mennyiségi tároló módosítása

1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, majd navigáljon a **felügyeleti > mennyiségi tárolók** elemre.

2. A mennyiségi tárolók táblázatos listájából válassza ki a módosítani kívánt kötet-tárolót. Az **eszközök** lapon válassza ki az eszközt, majd kattintson rá duplán, majd kattintson a **mennyiségi tárolók** fülre.

3. A mennyiségi tárolók táblázatos listájában válassza ki a módosítani kívánt kötetet. A megnyíló panelen kattintson a parancssorban a **módosítás** elemre.

    ![Mennyiségi tároló módosítása](./media/storsimple-8000-modify-volume-container/modify-volume-container-01.png)

4. A **mennyiségi tároló módosítása** panelen hajtsa végre a következő lépéseket:
   
   1. A kötet-tárolóhoz társított név, titkosítási kulcs és Storage-fiók nem módosítható a megadott beállítások megadása után. Módosítsa a társított sávszélesség-beállítást.<!--STEPS NEED WORK. Updated screen doesn't show alternative to Unlimited or subsequent steps if they customize bandwidth. Can we talk them through this (briefly)?-->
      
       ![Sávszélesség-beállítás módosítása](./media/storsimple-8000-modify-volume-container/modify-volume-container-02.png)<!--New graphic based on: modify-volume-container-bw-setting.png-->

   1.  Kattintson az **OK** gombra.<!--If they choose Custom, do they still click OK, or are there more steps?-->

5. A **mennyiségi tároló módosítása** párbeszédpanel következő lapján:<!--This step happens only if they choose Custom bandwidth? Are the steps similar to those in "Add volume container," step 3f, above?"-->
   
   1. A legördülő listában válasszon ki egy meglévő sávszélesség-sablont.
   1. Tekintse át a megadott sávszélesség-sablon ütemezett beállításait.
   1. Kattintson a **Mentés** gombra, és erősítse meg a módosításokat.
      
       ![Módosítások megerősítése](./media/storsimple-8000-modify-volume-container/modify-volume-container-03.png)

      A **kötetek tárolói** panelje frissül, hogy tükrözze a módosításokat.
