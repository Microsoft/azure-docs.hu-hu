---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 23ce17844a0113f63931c6ece7d36bfefedc2de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100552589"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>StorSimple biztonsági mentési házirend hozzáadása

1. A StorSimple-eszközben kattintson a **Biztonsági mentési szabályzat** lehetőségre.

2. A **Biztonsági mentési szabályzat** panelen kattintson a **+ Szabályzat hozzáadása** gombra a parancssávon.
   
    ![Biztonsági mentési szabályzat hozzáadása](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. Hajtsa végre a következő lépéseket a **Biztonsági mentési szabályzat létrehozása** panelen:
   
   1. Az **Eszköz kiválasztása** mezőt a rendszer automatikusan kitölti a kiválasztott eszköz alapján.
   
   2. Adja meg azt a biztonsági mentési **szabályzat nevét** , amely 3 – 150 karakterből áll. A szabályzat nem nevezhető át a létrehozása után.
       
   3. Ha köteteket szeretne hozzárendelni ehhez a biztonsági mentési szabályzathoz, válassza a **Kötetek hozzáadása** lehetőséget, majd a kötetek táblázatos listájában a jelölőnégyzet(ek)re kattintva rendeljen hozzá egy vagy több kötetet a biztonsági mentési szabályzathoz.

       ![Biztonsági mentési házirend hozzáadása 2](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. Ha ütemezést szeretne meghatározni a biztonsági mentési szabályzathoz, kattintson az **Első ütemezés** lehetőségre, majd módosítsa a következő paramétereket:<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![Biztonsági mentési szabályzat hozzáadása 3](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. A **Pillanatkép típusa** területen válassza a **Felhő** vagy a **Helyi** lehetőséget.

       2. Jelezze a biztonsági mentések gyakoriságát (adjon meg egy számot, majd válassza a **nap** vagy **hét** lehetőséget a legördülő listából.

       3. Adja meg a megőrzés ütemezését.

       4. Adja meg a biztonsági házirend indítási időpontját és dátumát.

       5. Kattintson az **OK** gombra az ütemezés meghatározásához.

   5. Biztonsági mentési szabályzat létrehozásához kattintson a **Létrehozás** gombra.
   
   6. A biztonsági mentési szabályzat létrehozásáról értesítést kap. Az újonnan felvett szabályzat megjelenik a **Biztonsági mentési szabályzat** panel táblázatos nézetében.

       ![Biztonsági mentési szabályzat hozzáadása 5](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
