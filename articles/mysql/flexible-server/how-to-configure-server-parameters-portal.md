---
title: Kiszolgáló paramétereinek konfigurálása – Azure Portal – Azure Database for MySQL rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a MySQL-kiszolgáló paramétereit Azure Database for MySQL rugalmas kiszolgálón a Azure Portal használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 618862e12bd62fbe37ef5e621c89babd7942c04b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106955"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Kiszolgáló paramétereinek konfigurálása Azure Database for MySQL-rugalmas kiszolgálón a Azure Portal használatával

> [!IMPORTANT] 
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

A kiszolgálói paraméterek használatával kezelheti Azure Database for MySQL rugalmas kiszolgáló konfigurációját. A kiszolgálói paraméterek az alapértelmezett és ajánlott értékkel vannak konfigurálva a kiszolgáló létrehozásakor.  

Ez a cikk a kiszolgálói paraméterek megtekintését és konfigurálását ismerteti a Azure Portal használatával. A Azure Portal kiszolgálói paraméter a módosítható és nem módosítható kiszolgálói paramétert is megjeleníti. A nem módosítható kiszolgálói paraméterek szürkén jelennek meg.

>[!Note]
> A kiszolgálói paraméterek globálisan frissíthetők a kiszolgáló szintjén, az [Azure CLI](./how-to-configure-server-parameters-cli.md) vagy a [Azure Portal](./how-to-configure-server-parameters-portal.md)használatával.

## <a name="configure-server-parameters"></a>Kiszolgáló paramétereinek konfigurálása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd keresse meg Azure Database for MySQL rugalmas kiszolgálóját.
2. A **Beállítások** szakaszban kattintson a **kiszolgálói paraméterek** elemre a Azure Database for MySQL rugalmas kiszolgáló kiszolgálói paraméterek lapjának megnyitásához.
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Azure Portal kiszolgáló paramétereinek lapja":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. Keresse meg a módosítani kívánt kiszolgálói paramétereket. A **description (Leírás** ) oszlopban tekintse át a cél és az engedélyezett értékek ismeretét.
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="Számbavétel legördülő lista":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. A módosítások mentéséhez kattintson a  **Save (Mentés** ) gombra.
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="Módosítások mentése vagy elvetése":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. A statikus paraméterek azok, amelyek a kiszolgáló újraindítását igénylik érvénybe léptetéséhez. Ha módosítja a statikus paramétert, a rendszer kérni fogja, hogy **indítsa újra** a rendszert, vagy **később indítsa újra**.
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="Újraindítás statikus paraméter esetén mentés":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. Ha új értékeket mentett a paraméterek számára, az **összes visszaállítása az alapértelmezett** értékre lehetőség kiválasztásával bármikor visszaállíthatja az alapértelmezett értékeket.
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="Az összes visszaállítása az alapértelmezett értékre":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>Nem módosítható kiszolgálói paraméterek beállítása

Ha a frissíteni kívánt kiszolgálói paraméter nem módosítható, akkor opcionálisan a paramétert is beállíthatja a kapcsolódási szinten `init_connect` . Ezzel beállítja a kiszolgálóhoz csatlakozó egyes ügyfelek kiszolgálói paramétereit. 

1. A **Beállítások** szakaszban kattintson a **kiszolgálói paraméterek** elemre a Azure Database for MySQL kiszolgáló kiszolgálói paraméterek lapjának megnyitásához.
2. Keresés `init_connect`
3. Adja hozzá a kiszolgáló paramétereit a (z) Value ( `SET parameter_name=YOUR_DESIRED_VALUE` érték) oszlopban a Format: értéknél.

    Megváltoztathatja például a kiszolgáló karakterkészletét a következőre való beállításával: `init_connect``SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Kattintson a **Mentés** gombra a módosítások mentéséhez.

>[!Note]
> Az `init_connect` olyan paraméterek módosítására használható, amelyek nem igényelnek SUPER jogosultságo(ka)t a munkamenet szintjén. A `set session parameter_name=YOUR_DESIRED_VALUE;` parancs végrehajtásával ellenőrizze, hogy megadható-e a paraméter az **használatával. Ha hiba lép fel, és a** Hozzáférés megtagadva; SUPER jogosultság(ok) szükséges(ek)`init_connect` hibaüzenet jelenik meg, akkor a paraméter nem adható meg az init_connect segítségével.

## <a name="working-with-the-time-zone-parameter"></a>Az időzóna-paraméter használata

### <a name="populating-the-time-zone-tables"></a>Az időzóna-táblák feltöltése

A kiszolgálón található időzóna-táblákat úgy töltheti fel, hogy meghívja a `mysql.az_load_timezone` tárolt eljárást egy olyan eszközről, mint a MySQL parancssor vagy a MySQL Workbench.

> [!NOTE]
> Ha a `mysql.az_load_timezone` MySQL Workbenchből futtatja a parancsot, előfordulhat, hogy először ki kell kapcsolnia a biztonságos frissítési módot a használatával `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Indítsa újra a kiszolgálót az időzóna-táblák megfelelő kitöltésének biztosításához.<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

A rendelkezésre álló időzóna-értékek megtekintéséhez futtassa a következő parancsot:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>A globális szintű időzóna beállítása

A globális szintű időzónát a Azure Portal **kiszolgáló paraméterek** lapján lehet beállítani. Az alábbi beállítás a globális időzónát az "USA/csendes-óceáni" értékre állítja be.

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="Időzóna-paraméter beállítása":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>A munkamenet-szint időzónájának beállítása

A munkamenet-szint időzónája beállítható úgy, hogy a `SET time_zone` parancsot egy olyan eszközről futtatja, mint a MySQL parancssor vagy a MySQL Workbench. Az alábbi példa az időzónát az **USA/csendes-óceáni** időzónára állítja be.

```sql
SET time_zone = 'US/Pacific';
```

Tekintse meg a MySQL dokumentációját a [dátum-és Időfüggvényekhez](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Következő lépések

- A [kiszolgálói paraméterek konfigurálása az Azure CLI-ben](./how-to-configure-server-parameters-cli.md)
