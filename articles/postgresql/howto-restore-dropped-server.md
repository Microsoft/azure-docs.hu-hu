---
title: Eldobott Azure Database for PostgreSQL kiszolgáló visszaállítása
description: Ez a cikk azt ismerteti, hogyan állítható vissza egy eldobott kiszolgáló a Azure Database for PostgreSQL a Azure Portal használatával.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 591f01004cfba247112f702625ab05ddc0aaede3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652925"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Eldobott Azure Database for PostgreSQL kiszolgáló visszaállítása

Egy kiszolgáló eldobása esetén az adatbázis-kiszolgáló biztonsági másolata a szolgáltatásban legfeljebb öt napig megőrizhető. Az adatbázis biztonsági mentése csak abban az Azure-előfizetésben érhető el és állítható vissza, ahol a kiszolgáló eredetileg található. A következő javasolt lépéseket követve állíthatja vissza az eldobott PostgreSQL-kiszolgáló erőforrását a kiszolgáló törlésének időpontjától számított 5 napon belül. A javasolt lépések csak akkor vezetnek eredményre, ha a kiszolgáló biztonsági másolata továbbra is elérhető, és nem lett törölve a rendszerből. 

## <a name="pre-requisites"></a>Előfeltételek
Az eldobott Azure Database for PostgreSQL kiszolgáló visszaállításához a következőket kell tennie:
- Az eredeti kiszolgálót üzemeltető Azure-előfizetés neve
- A kiszolgáló létrehozásának helye

## <a name="steps-to-restore"></a>A visszaállítás lépései

1. Tallózással keresse meg a [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). Válassza ki a **Azure monitor** szolgáltatást, majd válassza a **műveletnapló** elemet.

2. A tevékenység naplójában kattintson a **szűrő hozzáadása** elemre, és adja meg a következő szűrőket a következőhöz:

    - **Előfizetés** = a törölt kiszolgálót tároló előfizetés
    - **Erőforrás típusa** = Azure Database for PostgreSQL-kiszolgálók (Microsoft. DBforPostgreSQL/Servers)
    - **Művelet** = PostgreSQL-kiszolgáló törlése (Microsoft. DBforPostgreSQL/Servers/delete)
 
    ![A PostgreSQL-kiszolgálói művelet törlésére szolgáló tevékenység naplója szűrve](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Válassza a **PostgreSQL-kiszolgáló törlése** eseményt, majd válassza a **JSON fület**. Másolja a `resourceId` és `submissionTimestamp` attribútumokat a JSON-kimenetbe. A resourceId formátuma a következő: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. Tallózással keresse meg a PostgreSQL- [kiszolgáló létrehozása REST API lapot](/rest/api/PostgreSQL/servers/create) , és válassza a zöld színnel kiemelt **kipróbálás** lapot. Jelentkezzen be az Azure-fiókjával.

 5. Adja meg a **resourceGroupName**, **serverName** (a kiszolgáló nevét), a **SUBSCRIPTIONID** tulajdonságokat a resourceId attribútum JSON-értéke alapján, amelyet a 3. lépésben rögzített. Az API-Version tulajdonság előre ki van töltve, és az a következő képen látható módon maradhat: is.

    ![Kiszolgáló létrehozása REST API használatával](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
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

    Ha például az aktuális idő 2020-11-02T23:59:59.0000000 Z, javasoljuk, hogy legalább 15 perccel korábbi visszaállítási pontot a 2020-11-02T23:44:59.0000000 Z időpontban.

    > [!Important]
    > A kiszolgáló eldobása után a rendszer öt nappal túllépte az időkorlátot. Öt nap elteltével hiba várható, mivel a biztonságimásolat-fájl nem található.
    
7. Ha a 201-es vagy a 202-es hibakód jelenik meg, a visszaállítási kérelem sikeresen elküldve. 

    A kiszolgáló létrehozása az adatbázis méretétől és az eredeti kiszolgálón kiépített számítási erőforrásoktól függően időt is igénybe vehet. A visszaállítási állapot figyelhető a tevékenység naplójában a következő szűréssel: 
   - **Előfizetés** = az előfizetése
   - **Erőforrás típusa** = Azure Database for PostgreSQL-kiszolgálók (Microsoft. DBforPostgreSQL/Servers) 
   - **Művelet** = PostgreSQL-kiszolgáló létrehozása

## <a name="next-steps"></a>Következő lépések
- Ha öt napon belül próbál helyreállítani egy kiszolgálót, és továbbra is hibaüzenetet kap, miután pontosan követte a korábban tárgyalt lépéseket, nyisson meg egy támogatási eseményt segítségért. Ha öt nap elteltével próbál helyreállítani egy eldobott kiszolgálót, a rendszer hibaüzenetet vár, mivel a biztonságimásolat-fájl nem található. Ebben a forgatókönyvben ne nyisson meg támogatási jegyet. A támogatási csapat nem tud segítséget nyújtani, ha a biztonsági mentést törli a rendszerből. 
- A kiszolgálók véletlen törlésének elkerülése érdekében javasoljuk, hogy használjon [erőforrás-zárolást](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222).
