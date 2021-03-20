---
title: ServiceNow-szinkronizálási problémák manuális javítása
description: Állítsa vissza a ServiceNow való kapcsolódást, hogy a riasztások Microsoft Azure újra meghívja a ServiceNow
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/17/2021
ms.openlocfilehash: 664f1522775d96b813b7cd99bdffdb26630497f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037438"
---
# <a name="how-to-manually-fix-sync-problems"></a>Szinkronizálási problémák manuális javítása

Azure Monitor csatlakozhat a harmadik féltől származó IT-szolgáltatói (ITSM-) szolgáltatóhoz. A ServiceNow az egyik ilyen szolgáltató.

Biztonsági okokból előfordulhat, hogy frissítenie kell a ServiceNow-mel való kapcsolathoz használt hitelesítési tokent.
A következő szinkronizálási folyamat használatával aktiválja újra a kapcsolódást, és frissítse a jogkivonatot:

1. Keresse meg a megoldást a legnépszerűbb keresési szalagcímben, majd válassza ki a megfelelő megoldásokat.

    ![A legnépszerűbb keresési szalagcímet megjelenítő képernyőkép, ahol kiválaszthatja a megfelelő megoldásokat.](media/itsmc-resync-servicenow/solution-search-8-bit.png)

1. A megoldás képernyőn válassza az összes kijelölése lehetőséget az előfizetés szűrőben, majd a szűrés "ügyfélszolgálati" értékre.

    ![Képernyőkép, amely bemutatja, hogy hol válassza az összes kijelölése és a szűrés ügyfélszolgálati alapján lehetőséget.](media/itsmc-resync-servicenow/solutions-list-8-bit.png)

1. Válassza ki a ITSM-kapcsolatok megoldását.
1. Válassza a ITSM-kapcsolatok elemet a bal oldali szalagcímben.

    ![Képernyőkép, amely megjeleníti a ITSM-kapcsolatok kiválasztásának helyét.](media/itsmc-resync-servicenow/itsm-connector-8-bit.png)

1. Válassza ki az egyes összekötőket a listából. 
    1. A konfigurálásához kattintson az összekötő nevére
    1. A már nem használt összekötők törlése

    1. A mezők frissítése a partner típusa alapján a [következő definíciók](./itsmc-connections.md) szerint

    1. Kattintson a szinkronizálás elemre

       ![Képernyőfelvétel: a szinkronizálás gomb kiemelése.](media/itsmc-resync-servicenow/resync-8-bit-2.png)

    1. Kattintson a Mentés gombra

        ![Új kapcsolat](media/itsmc-resync-servicenow/save-8-bit.png)

f.    Tekintse át az értesítéseket, és ellenőrizze, hogy elindult-e a folyamat.
