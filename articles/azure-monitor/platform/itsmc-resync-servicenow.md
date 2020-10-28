---
title: ServiceNow-szinkronizálási problémák manuális javítása
description: Állítsa vissza a ServiceNow való kapcsolódást, hogy a riasztások Microsoft Azure újra meghívja a ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 2a9a8f3fe7422468ff2c8886dff7415322cf70f0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676878"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>ServiceNow-szinkronizálási problémák manuális javítása

Azure Monitor csatlakozhat a harmadik féltől származó IT-szolgáltatói (ITSM-) szolgáltatóhoz. A ServiceNow az egyik ilyen szolgáltató.

Biztonsági okokból előfordulhat, hogy frissítenie kell a ServiceNow-mel való kapcsolathoz használt hitelesítési tokent.
A következő szinkronizálási folyamat használatával aktiválja újra a kapcsolódást, és frissítse a jogkivonatot:


1. Keresse meg a megoldást a legnépszerűbb keresési szalagcímben, majd válassza ki a megfelelő megoldásokat.

    ![A legnépszerűbb keresési szalagcímet megjelenítő képernyőkép, ahol kiválaszthatja a megfelelő megoldásokat.](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. A megoldás képernyőn válassza az összes kijelölése lehetőséget az előfizetés szűrőben, majd a szűrés "ügyfélszolgálati" értékre.

    ![Képernyőkép, amely bemutatja, hogy hol válassza az összes kijelölése és a szűrés ügyfélszolgálati alapján lehetőséget.](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Válassza ki a ITSM-kapcsolatok megoldását.
1. Válassza a ITSM-kapcsolatok elemet a bal oldali szalagcímben.

    ![Képernyőkép, amely megjeleníti a ITSM-kapcsolatok kiválasztásának helyét.](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Válassza ki az egyes összekötőket a listából. 
    1. A konfigurálásához kattintson az összekötő nevére
    1. A már nem használt összekötők törlése

    1. A mezők frissítése a partner típusa alapján a [következő definíciók](./itsmc-connections.md) szerint

    1. Kattintson a szinkronizálás elemre

       ![Képernyőfelvétel: a szinkronizálás gomb kiemelése.](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Kattintson a Mentés gombra

        ![Új kapcsolat](media/itsmc-resync-servicenow/save-8bit.png)

f.    Tekintse át az értesítéseket, és ellenőrizze, hogy a folyamat sikeresen befejeződött-e 

## <a name="next-steps"></a>Következő lépések

További információ az [IT Service Management-kapcsolatokról](itsmc-connections.md)
