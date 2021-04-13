---
title: Eldobott kiszolgáló Azure Database for PostgreSQL visszaállítása
description: Ez a cikk bemutatja, hogyan állíthatók vissza az eldobott kiszolgálók Azure Database for PostgreSQL a Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 5b5bb9fd6e3d34fc4a6b0ae90a2cd76fc84e9ce1
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366521"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Eldobott kiszolgáló Azure Database for PostgreSQL visszaállítása

Egy kiszolgáló elejtését az adatbázis-kiszolgáló biztonsági másolata legfeljebb öt napig őrizheti meg a szolgáltatásban. Az adatbázis biztonsági másolata csak abból az Azure-előfizetésből érhető el és állítható vissza, amelyben a kiszolgáló eredetileg található. Az alábbi javasolt lépésekkel helyreállíthatja az eldobott PostgreSQL-kiszolgálói erőforrásokat a kiszolgáló törlésétől számított 5 napon belül. A javasolt lépések csak akkor vezetnek eredményre, ha a kiszolgáló biztonsági másolata továbbra is elérhető, és nem lett törölve a rendszerből. 

## <a name="pre-requisites"></a>Előfeltételek
Az eldobott Azure Database for PostgreSQL helyreállításához a következőkre lesz szüksége:
- Az eredeti kiszolgálót üzemeltető Azure-előfizetés neve
- A kiszolgáló létrehozási helye

## <a name="steps-to-restore"></a>A visszaállítás lépései

1. Keresse meg a [Azure Portal.](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) Válassza ki **a Azure Monitor** szolgáltatást, majd válassza a **Tevékenységnapló lehetőséget.**

2. A Tevékenységnaplóban kattintson a **Szűrő hozzáadása** lehetőségre az itt látható módon, és állítsa be a következő szűrőket az alábbiakhoz

    - **Előfizetés** = A törölt kiszolgálót üzemeltető előfizetés
    - **Erőforrástípus** = Azure Database for PostgreSQL kiszolgálók (Microsoft.DBforPostgreSQL/servers)
    - **Művelet** = PostgreSQL-kiszolgáló törlése (Microsoft.DBforPostgreSQL/servers/delete)
 
    ![PostgreSQL-kiszolgáló törlésére szűrt tevékenységnapló](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Válassza a **PostgreSQL-kiszolgáló törlése eseményt,** majd a **JSON lapot.** Másolja ki a `resourceId` `submissionTimestamp` JSON-kimenetben a és a attribútumot. A resourceId formátuma a következő: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. Keresse meg a PostgreSQL [Create Server REST API Page](/rest/api/PostgreSQL/servers/create) (Kiszolgáló létrehozása a PostgreSQL-kiszolgáló létrehozása) lapot, és válassza a Try It **(Kipróbálom)** lapot zöld színnel kiemelve. Jelentkezzen be Azure-fiókjával.

 5. Adja meg a **resourceGroupName**, **serverName** (törölt kiszolgáló neve), **subscriptionId** tulajdonságokat az előző 3. lépésben rögzített resourceId attribútum JSON-értéke alapján. Az API-version tulajdonság előre ki van töltve, és a jelenlegi adatokat is meghagyhatja, ahogyan az alábbi képen látható.

    ![Kiszolgáló létrehozása REST API](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. Görgessen le alább a Kérelem törzse szakaszban, és illessze be a következőt az "Eldobott kiszolgáló helye" (például CentralUS, EastUS stb.), a "submissionTimestamp" és a "resourceId" helyére. A "restorePointInTime" értékeként adja meg a "submissionTimestamp" **értéket 15** perccel, hogy a parancs ne hibásodjön meg.
    
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

    Ha például az aktuális időpont 2020-11-02T23:59:59.0000000Z, akkor legalább 15 perccel a 2020-11-02T23:44:59.0000000Z időpont előtt javasoljuk a visszaállítási pontot.

    > [!Important]
    > A kiszolgáló elejtését követően öt napos időkorlát van meg. Öt nap után hiba várható, mivel a biztonsági mentési fájl nem található.
    
7. Ha 201-es vagy 202-es válaszkódot lát, a visszaállítási kérés sikeresen el lett elküldve. 

    A kiszolgáló létrehozása az eredeti kiszolgálón kiépített adatbázis méretétől és számítási erőforrásaitól függően sok időt is eltelhet. A visszaállítási állapot a Tevékenységnaplóból a következő szűrésével monitorozással figyelhető meg: 
   - **Előfizetés** = Az Ön előfizetése
   - **Erőforrástípus** = Azure Database for PostgreSQL kiszolgálók (Microsoft.DBforPostgreSQL/servers) 
   - **Művelet** = PostgreSQL-kiszolgáló létrehozása frissítése

## <a name="next-steps"></a>Következő lépések
- Ha öt napon belül megpróbál visszaállítani egy kiszolgálót, és a korábban tárgyalt lépések pontos lépéseit követő hibaüzenetet kap, nyisson egy támogatási incidenst segítségért. Ha öt nap után próbál visszaállítani egy eldobott kiszolgálót, a rendszer hibát jelez, mivel a biztonságimásolat-fájl nem található. Ebben a forgatókönyvben ne nyisson támogatási jegyet. A támogatási csapat nem tud segítséget nyújtani, ha a biztonsági másolatot törlik a rendszerből. 
- A kiszolgálók véletlen törlésének megelőzése érdekében erősen ajánlott az [erőforrás-zárolás használata.](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222)
