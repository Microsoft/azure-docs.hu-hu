---
title: StorSimple Snapshot Manager biztonsági mentési szabályzatok | Microsoft Docs
description: Ismerteti, hogyan használható a StorSimple Snapshot Manager MMC beépülő modul az ütemezett biztonsági mentést vezérlő biztonsági mentési házirendek létrehozásához és kezeléséhez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 745aeb9844f1abad075d34b34735563200ea618b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90054942"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Biztonsági mentési szabályzatok létrehozása és kezelése StorSimple Snapshot Manager használatával
## <a name="overview"></a>Áttekintés
A biztonsági mentési szabályzat létrehoz egy ütemtervet a kötetek adatai helyi vagy Felhőbeli biztonsági mentéséhez. Biztonsági mentési szabályzat létrehozásakor megadhat egy adatmegőrzési szabályt is. (Legfeljebb 64 pillanatképet tárolhat.) A biztonsági mentési szabályzatokkal kapcsolatos további információkért lásd: [biztonsági mentési típusok](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) a [StorSimple 8000 sorozatban: hibrid felhőalapú megoldás](storsimple-overview.md).

Ez az oktatóanyag a következőket ismerteti:

* Biztonsági mentési szabályzat létrehozása
* Biztonsági mentési szabályzat szerkesztése
* Biztonsági mentési szabályzat törlése

## <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása
Új biztonsági mentési szabályzat létrehozásához kövesse az alábbi eljárást.

#### <a name="to-create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson a jobb gombbal a **biztonsági mentési házirendek** elemre, majd kattintson a **biztonsági mentési házirend létrehozása** parancsra.

    ![Biztonsági mentési szabályzat létrehozása](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Megjelenik a **házirend létrehozása** párbeszédpanel.

    ![Házirend létrehozása – Általános lap](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Az **általános** lapon végezze el a következő információkat:

   1. A **név** szövegmezőbe írja be a szabályzat nevét.
   2. A **kötet csoport** szövegmezőbe írja be a házirendhez társított kötet csoport nevét.
   3. Válassza a **helyi pillanatfelvétel** vagy a **Felhőbeli pillanatkép** lehetőséget.
   4. Válassza ki a megőrizni kívánt Pillanatképek számát. Ha az **összes** lehetőséget választja, a 64-es Pillanatképek megmaradnak (a maximumot).
4. Kattintson az **Ütemezés** lapra.

    ![Szabályzat létrehozása – ütemterv lap](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Az **ütemterv** lapon hajtsa végre a következő információkat:

   1. Ha a következő biztonsági mentést szeretné ütemezni, kattintson az **Engedélyezés** jelölőnégyzetre.
   2. A **Beállítások** területen válasszon ki **egy időpontot**, **naponta**, **hetente** vagy **havonta**.
   3. A **Start** szövegmezőben kattintson a naptár ikonra, és válassza ki a kezdő dátumot.
   4. A **Speciális beállítások** területen beállíthatja a választható ismétlési ütemterveket és a befejezési dátumot.
   5. Kattintson az **OK** gombra.

A biztonsági mentési szabályzat létrehozása után az alábbi információk jelennek meg az **eredmények** ablaktáblájában:

* **Name (név** ) – a biztonsági mentési szabályzat neve.
* **Típus** – helyi pillanatfelvétel vagy Felhőbeli pillanatkép.
* **Kötet csoport** – a házirendhez társított kötet csoport.
* **Megőrzés** – a megőrzött Pillanatképek száma; a maximális érték 64.
* **Létrehozva** – a szabályzat létrehozásának dátuma.
* **Engedélyezve** – azt határozza meg, hogy a házirend jelenleg érvényben van-e: az **igaz** érték azt jelzi, hogy az érvényes; A **false (hamis** ) érték azt jelzi, hogy nincs érvényben.

## <a name="edit-a-backup-policy"></a>Biztonsági mentési szabályzat szerkesztése
A meglévő biztonsági mentési szabályzat szerkesztéséhez kövesse az alábbi eljárást.

#### <a name="to-edit-a-backup-policy"></a>Biztonsági mentési szabályzat szerkesztése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson a **biztonsági mentési házirendek** csomópontra. Az **eredmény** ablaktáblán megjelenik az összes biztonsági mentési szabályzat.
3. Kattintson a jobb gombbal a szerkeszteni kívánt szabályzatra, majd kattintson a **Szerkesztés** parancsra.

    ![Biztonsági mentési szabályzat szerkesztése](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Amikor megjelenik a **házirend létrehozása** ablak, írja be a módosításokat, majd kattintson **az OK** gombra.

## <a name="delete-a-backup-policy"></a>Biztonsági mentési szabályzat törlése
A biztonsági mentési szabályzat törléséhez kövesse az alábbi eljárást.

#### <a name="to-delete-a-backup-policy"></a>Biztonsági mentési szabályzat törlése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson a **biztonsági mentési házirendek** csomópontra. Az **eredmény** ablaktáblán megjelenik az összes biztonsági mentési szabályzat.
3. Kattintson a jobb gombbal a törölni kívánt biztonsági mentési házirendre, majd kattintson a **Törlés** parancsra.
4. Amikor megjelenik a megerősítő üzenet, kattintson az **Igen** gombra.

    ![Biztonsági mentési szabályzat törlésének megerősítése](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan kezelheti [a StorSimple-megoldást a StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-admin.md).
* Ismerje meg, hogyan [tekintheti meg és kezelheti a biztonsági mentési feladatokat a StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-manage-backup-jobs.md).
