---
title: fájl belefoglalása
description: fájl belefoglalása
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 386a39d76bf47da45f07054124886e174104849e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545290"
---
A mennyiségi tárolók törléséhez
 - kötetek törlése a kötet tárolóban. Ha a mennyiségi tárolóhoz társított kötetek tartoznak, először a köteteket offline állapotba kell állítani. Kövesse a [kötet offline állapotba](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline)helyezése című témakör lépéseit. A kötetek offline állapotba helyezése után törölheti őket. 
 - a társított biztonsági mentési szabályzatok és a Felhőbeli Pillanatképek törlése. Ellenőrizze, hogy a mennyiségi tárolóhoz vannak-e társítva biztonsági mentési szabályzatok és Felhőbeli Pillanatképek. Ha igen, [törölje a biztonsági mentési szabályzatokat](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Emellett törli a Felhőbeli pillanatképeket is. 
 
Ha a kötet tárolóban nincsenek társított kötetek, biztonsági mentési szabályzatok és Felhőbeli Pillanatképek, törölheti azt. A kötetek tárolójának törléséhez hajtsa végre az alábbi eljárást.

#### <a name="to-delete-a-volume-container"></a>Mennyiségi tároló törlése

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Jelölje ki, majd kattintson az eszközre, majd lépjen a **beállítások > > mennyiségi tárolók kezelése** elemre.

    ![Mennyiségi tárolók panel](./media/storsimple-8000-delete-volume-container/create-volume-container.png)

2. A mennyiségi tárolók táblázatos listájából válassza ki a törölni kívánt kötet-tárolót, kattintson a jobb gombbal **...** elemre, majd válassza a **Törlés** lehetőséget.

    ![Mennyiségi tároló törlése](./media/storsimple-8000-delete-volume-container/delete-volume-container-01.png)

3. Ha egy mennyiségi tárolóhoz nincsenek társított kötetek, biztonsági mentési szabályzatok és Felhőbeli Pillanatképek, akkor törölhető. Ha a rendszer megerősítést kér, tekintse át, majd jelölje be a mennyiségi tároló törlésének következményeire vonatkozó jelölőnégyzetet. Kattintson a **Törlés** elemre, majd törölje a kötet tárolót.

    ![Törlés megerősítése](./media/storsimple-8000-delete-volume-container/delete-volume-container-02.png)

A kötet-tárolók listája frissült, hogy tükrözze a törölt kötet tárolóját.

![Képernyőfelvétel a mennyiségi tároló lapról. A mennyiségi tárolók táblázatos listája már nem tartalmazza a törölt tárolót.](./media/storsimple-8000-delete-volume-container/delete-volume-container-05.png)
