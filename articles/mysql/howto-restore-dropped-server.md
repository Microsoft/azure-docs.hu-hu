---
title: Eldobott Azure Database for MySQL kiszolgáló visszaállítása
description: Ez a cikk azt ismerteti, hogyan állítható vissza egy eldobott kiszolgáló a Azure Database for MySQL a Azure Portal használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 34dddd8e5f3fb418fc7155630bf82a922e418402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97657090"
---
# <a name="restore-a-dropped-azure-database-for-mysql-server"></a>Eldobott Azure Database for MySQL kiszolgáló visszaállítása

Egy kiszolgáló eldobása esetén az adatbázis-kiszolgáló biztonsági másolata a szolgáltatásban legfeljebb öt napig megőrizhető. Az adatbázis biztonsági mentése csak abban az Azure-előfizetésben érhető el és állítható vissza, ahol a kiszolgáló eredetileg található. Az alábbi javasolt lépésekkel a kiszolgáló törlésének időpontjától számított 5 napon belül helyreállíthatja az elvetett MySQL-kiszolgálói erőforrásokat. A javasolt lépések csak akkor vezetnek eredményre, ha a kiszolgáló biztonsági másolata továbbra is elérhető, és nem lett törölve a rendszerből. 

## <a name="pre-requisites"></a>Előfeltételek
Az eldobott Azure Database for MySQL kiszolgáló visszaállításához a következőket kell tennie:
- Az eredeti kiszolgálót üzemeltető Azure-előfizetés neve
- A kiszolgáló létrehozásának helye

## <a name="steps-to-restore"></a>A visszaállítás lépései

1. Nyissa meg a Azure Portal a figyelő panelén a [tevékenység naplóját](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) . 

2. A tevékenység naplójában kattintson a **szűrő hozzáadása** elemre, és adja meg a következő szűrőket a 

    - **Előfizetés** = a törölt kiszolgálót tároló előfizetés
    - **Erőforrás típusa** = Azure Database for MySQL-kiszolgálók (Microsoft. DBforMySQL/Servers) 
    - **Művelet** = MySQL-kiszolgáló törlése (Microsoft. DBforMySQL/Servers/delete) 
 
     [![Tevékenység napló szűrve a MySQL-kiszolgáló törlése művelethez](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. Kattintson duplán a MySQL-kiszolgáló törlése eseményre, és kattintson a JSON fülre, és jegyezze fel a "resourceId" és a "submissionTimestamp" attribútumokat a JSON-kimenetben. A resourceId formátuma a következő:/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver.
 
 4. Lépjen a [kiszolgáló létrehozása REST API lapra](/rest/api/mysql/servers/create) , és kattintson a "kipróbálás" fülre zöld színnel, és jelentkezzen be az Azure-fiókjával.
 
 5. Adja meg a 3. lépésben rögzített resourceGroupName, serverName (a kiszolgáló neve) subscriptionId, amely a resourceId attribútumból származik, az API-Version pedig előre ki van töltve, ahogy az a képen látható.
 
     [![Kiszolgáló létrehozása REST API használatával](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. Görgessen lejjebb a kérelem törzse szakaszban, és illessze be a következőt az "eldobott kiszolgáló helye", a "submissionTimestamp" és a "resourceId" helyett. A "restorePointInTime" értéknél a "submissionTimestamp" értéket mínusz **15 percben** kell megadni, hogy a parancs ne legyen hiba.
 
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

7. Ha a 201-es vagy a 202-es hibakód jelenik meg, a visszaállítási kérelem sikeresen elküldve. 

8. A kiszolgáló létrehozása az adatbázis méretétől és az eredeti kiszolgálón kiépített számítási erőforrásoktól függően időt is igénybe vehet. A visszaállítási állapot figyelhető a tevékenység naplójában a következő szűréssel: 
   - **Előfizetés** = az előfizetése
   - **Erőforrás típusa** = Azure Database for MySQL-kiszolgálók (Microsoft. DBforMySQL/Servers) 
   - **Művelet** = MySQL-kiszolgáló létrehozása

## <a name="next-steps"></a>Következő lépések
- Ha öt napon belül próbál helyreállítani egy kiszolgálót, és továbbra is hibaüzenetet kap, miután pontosan követte a korábban tárgyalt lépéseket, nyisson meg egy támogatási eseményt segítségért. Ha öt nap elteltével próbál helyreállítani egy eldobott kiszolgálót, a rendszer hibaüzenetet vár, mivel a biztonságimásolat-fájl nem található. Ebben a forgatókönyvben ne nyisson meg támogatási jegyet. A támogatási csapat nem tud segítséget nyújtani, ha a biztonsági mentést törli a rendszerből. 
- A kiszolgálók véletlen törlésének elkerülése érdekében javasoljuk, hogy használjon [erőforrás-zárolást](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222).