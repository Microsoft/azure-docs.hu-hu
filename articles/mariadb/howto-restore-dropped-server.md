---
title: Törölt Azure Database for MariaDB kiszolgáló visszaállítása
description: Ez a cikk azt ismerteti, hogyan lehet visszaállítani egy törölt kiszolgálót Azure Database for MariaDB a Azure Portal használatával.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 4/12/2021
ms.openlocfilehash: 7cf2ce61239c7f2320f7b789acbb8f340e6de471
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315550"
---
# <a name="restore-a-deleted-azure-database-for-mariadb-server"></a>Törölt Azure Database for MariaDB kiszolgáló visszaállítása

A kiszolgálók törlését követően az adatbázis-kiszolgáló biztonsági másolata akár öt napig is megtartható a szolgáltatásban. Az adatbázis biztonsági mentése csak abban az Azure-előfizetésben érhető el és állítható vissza, ahol a kiszolgáló eredetileg található. A következő javasolt lépéseket követve állíthatja helyre a törölt MariaDB-kiszolgáló erőforrását a kiszolgáló törlésének idejétől számított 5 napon belül. A javasolt lépések csak akkor vezetnek eredményre, ha a kiszolgáló biztonsági másolata továbbra is elérhető, és nem lett törölve a rendszerből. 

## <a name="pre-requisites"></a>Előfeltételek
Törölt Azure Database for MariaDB-kiszolgáló visszaállításához a következőkre lesz szüksége:
- Az eredeti kiszolgálót üzemeltető Azure-előfizetés neve
- A kiszolgáló létrehozásának helye

## <a name="steps-to-restore"></a>A visszaállítás lépései

1. Nyissa meg a Azure Portal a figyelő panelén a [tevékenység naplóját](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) . 

2. A tevékenység naplójában kattintson a **szűrő hozzáadása** elemre, és adja meg a következő szűrőket a 

    - **Előfizetés** = a törölt kiszolgálót tároló előfizetés
    - **Erőforrás típusa** = Azure Database for MariaDB-kiszolgálók (Microsoft. DBForMariaDB/Servers) 
    - **Operation** = MariaDB-kiszolgáló törlése (Microsoft. DBForMariaDB/Servers/delete) 
 
     [![A MariaDB-kiszolgáló törlésére szolgáló tevékenység naplója szűrve](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. Kattintson duplán az MariaDB-kiszolgáló törlése eseményre, és kattintson a JSON fülre, és jegyezze fel a "resourceId" és a "submissionTimestamp" attribútumokat a JSON-kimenetben. A resourceId formátuma a következő:/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBForMariaDB/servers/deletedserver.
 
 4. Lépjen a [kiszolgáló létrehozása REST API lapra](/rest/api/mariadb/servers/create) , és kattintson a "kipróbálás" fülre zöld színnel, és jelentkezzen be az Azure-fiókjával.
 
 5. Adja meg a 3. lépésben rögzített resourceGroupName, serverName (a kiszolgáló neve) subscriptionId, amely a resourceId attribútumból származik, az API-Version pedig előre ki van töltve, ahogy az a képen látható.
 
     [![Kiszolgáló létrehozása REST API használatával](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. Görgessen lejjebb a kérelem törzse szakaszban, és illessze be a következőt:
     
    ```json
    {
        "location": "Dropped Server Location",  
        "properties": 
            {
                "restorePointInTime": "submissionTimestamp - 15 minutes",
                "createMode": "PointInTimeRestore",
                "sourceServerId": "resourceId"
            }
    }
    ```

7. Cserélje le a következő értékeket a fenti kérelem törzsében:
   * "Eldobott kiszolgáló helye" azon az Azure-régióban, ahol a törölt kiszolgáló eredetileg létrejött
   * "submissionTimestamp" és "resourceId" a 3. lépésben rögzített értékekkel. 
   * A "restorePointInTime" értéknél a "submissionTimestamp" értéket mínusz **15 percben** kell megadni, hogy a parancs ne legyen hiba.

8. Ha a 201-es vagy a 202-es hibakód jelenik meg, a visszaállítási kérelem sikeresen elküldve. 

9. A kiszolgáló létrehozása az adatbázis méretétől és az eredeti kiszolgálón kiépített számítási erőforrásoktól függően időt is igénybe vehet. A visszaállítási állapot figyelhető a tevékenység naplójában a következő szűréssel: 
   - **Előfizetés** = az előfizetése
   - **Erőforrás típusa** = Azure Database for MariaDB-kiszolgálók (Microsoft. DBForMariaDB/Servers) 
   - **Művelet** = MariaDB-kiszolgáló létrehozása

## <a name="next-steps"></a>Következő lépések
- Ha öt napon belül próbál helyreállítani egy kiszolgálót, és továbbra is hibaüzenetet kap, miután pontosan követte a korábban tárgyalt lépéseket, nyisson meg egy támogatási eseményt segítségért. Ha öt nap elteltével próbálkozik a törölt kiszolgáló visszaállításával, a rendszer hibaüzenetet vár, mivel a biztonságimásolat-fájl nem található. Ebben a forgatókönyvben ne nyisson meg támogatási jegyet. A támogatási csapat nem tud segítséget nyújtani, ha a biztonsági mentést törli a rendszerből. 
- A kiszolgálók véletlen törlésének elkerülése érdekében javasoljuk, hogy használjon [erőforrás-zárolást](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222).
