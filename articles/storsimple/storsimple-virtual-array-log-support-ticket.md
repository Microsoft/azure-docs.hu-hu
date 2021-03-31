---
title: Támogatási jegy naplózása a StorSimple Eszközkezelő használatával | Microsoft Docs
description: Ismerteti a StorSimple Eszközkezelő diagnosztizálására szolgáló képességet, és ismerteti, hogyan használható a StorSimple virtuális tömb hibaelhárítására.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a0c394df-957b-49b3-a283-38824f8847fd
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 57cd9b55f529bde3f6bcb0a25a95c70333f6d8dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005775"
---
# <a name="use-the-storsimple-device-manager-service-to-log-a-support-request-for-the-storsimple-virtual-array"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple virtuális tömb Support request naplózására

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő lehetővé teszi **egy új támogatási kérelem naplózását** a szolgáltatás összegzési paneljén belül. Ez a cikk azt ismerteti, hogyan regisztrálhat egy új támogatási kérést, és hogyan kezelheti az életciklusát a portálon belülről.

## <a name="new-support-request"></a>Új támogatási kérelem

A [támogatási csomagtól](https://azure.microsoft.com/support/plans/)függően támogatási jegyek hozhatók létre a StorSimple virtuális tömbben, közvetlenül a StorSimple Eszközkezelő szolgáltatás összefoglalási paneljéről.

#### <a name="to-log-a-new-request"></a>Új kérelem naplózása

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A szolgáltatás összegzése panel beállításainál válassza a **támogatás + hibaelhárítás** szakaszt, majd kattintson az **új támogatási kérelem** elemre.
   
    ![A képernyőképen az új támogatási kérelem alapjai panelje látható.](./media/storsimple-virtual-array-log-support-ticket/log-support-ticket1.png)

2. Az **alapvető beállítások** panelen tegye a következőket:

    1. A **probléma típusa** legördülő listából válassza a **technikai** lehetőséget. 
    
    2. Az aktuális **előfizetés**, a **szolgáltatás** típusa és az **erőforrás** (StorSimple Eszközkezelő szolgáltatás) automatikusan ki van választva. 

    3. Adja meg egy vagy több, a szolgáltatáshoz regisztrált eszközt, amely problémákba ütközik.

    4. Válassza ki a megfelelő **támogatási csomagot** , ha az előfizetéshez több csomag is tartozik. A technikai támogatás engedélyezéséhez fizetős támogatási csomag szükséges.

3. A **2. lépésben** válassza ki a **súlyosságot** , és adja meg, hogy a probléma a tömbhöz vagy a StorSimple Eszközkezelő szolgáltatáshoz kapcsolódik-e. Válassza ki a probléma **kategóriáját** is, és adja meg a probléma további **részleteit** .
   
    ![Képernyőfelvétel: a probléma panel, amelyen megadhatja a támogatási kérelemhez tartozó probléma típusát, kategóriáját és részleteit.](./media/storsimple-virtual-array-log-support-ticket/log-support-ticket2.png)

4. A **3. lépésben** adja meg a kapcsolattartási adatait. A Microsoft ügyfélszolgálata ezeket az információkat a további információk, a diagnosztika és a megoldás elérésére fogja használni.
   
    ![Képernyőfelvétel: a kapcsolattartási adatok ablaktábla.](./media/storsimple-virtual-array-log-support-ticket/log-support-ticket3.png)

## <a name="manage-a-support-request"></a>Támogatási kérelem kezelése

A támogatási jegy létrehozása után a jegyet a teljes életciklusán keresztül kezelheti a portálon.

#### <a name="to-manage-your-support-requests"></a>A támogatási kérések kezelése

A Súgó és támogatás lapon keresse meg a **tallózás > Súgó + támogatás** menüpontot.

![Támogatási kérelmek kezelése](./media/storsimple-virtual-array-log-support-ticket/manage-support-tickets.png)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [diagnosztizálhatja és megoldhatja a StorSimple virtuális tömbhöz kapcsolódó problémákat](storsimple-virtual-array-diagnose-problems.md)

