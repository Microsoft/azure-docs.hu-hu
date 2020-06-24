---
title: Kiszolgáló paramétereinek konfigurálása – Azure Portal-Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni a MariaDB Server-paramétereket a Azure Database for MariaDB a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/11/2020
ms.openlocfilehash: ae9878ba17f05b2d843e18c921c87c44b9396827
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84738311"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-the-azure-portal"></a>Kiszolgálói paraméterek konfigurálása Azure Database for MariaDB a Azure Portal használatával

Azure Database for MariaDB támogatja egyes kiszolgálói paraméterek konfigurációját. Ez a cikk azt ismerteti, hogyan konfigurálhatja ezeket a paramétereket a Azure Portal használatával. Nem minden kiszolgáló paraméter módosítható.

## <a name="configure-server-parameters"></a>Kiszolgáló paramétereinek konfigurálása

1. Jelentkezzen be a Azure Portalba, majd keresse meg a Azure Database for MariaDB-kiszolgálót.
2. A **Beállítások** szakaszban kattintson a **kiszolgálói paraméterek** elemre a Azure Database for MariaDB kiszolgáló kiszolgálói paraméterek lapjának megnyitásához.
![Azure Portal kiszolgáló paramétereinek lapja](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Keresse meg a módosítani kívánt beállításokat. A **description (Leírás** ) oszlopban tekintse át a cél és az engedélyezett értékek ismeretét.
![Számbavétel legördülő lista](./media/howto-server-parameters/3-toggle_parameter.png)
4. A módosítások mentéséhez kattintson a **Save (Mentés** ) gombra.
![Módosítások mentése vagy elvetése](./media/howto-server-parameters/4-save_parameters.png)
5. Ha új értékeket mentett a paraméterek számára, az **összes visszaállítása az alapértelmezett**értékre lehetőség kiválasztásával bármikor visszaállíthatja az alapértelmezett értékeket.
![Az összes visszaállítása az alapértelmezett értékre](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="setting-parameters-not-listed"></a>Nem felsorolt paraméterek beállítása

Ha a frissíteni kívánt kiszolgálói paraméter nem szerepel a Azure Portalban, akkor opcionálisan a paramétert is megadhatja a kapcsolódási szinten a használatával `init_connect` . Ezzel beállítja a kiszolgálóhoz csatlakozó egyes ügyfelek kiszolgálói paramétereit. 

1. A **Beállítások** szakaszban kattintson a **kiszolgálói paraméterek** elemre a Azure Database for MariaDB kiszolgáló kiszolgálói paraméterek lapjának megnyitásához.
2. Keresés`init_connect`
3. Adja hozzá a kiszolgáló paramétereit a (z) Value ( `SET parameter_name=YOUR_DESIRED_VALUE` érték) oszlopban a Format: értéknél.

    Megváltoztathatja például a kiszolgáló karakterkészletét a következőre való beállításával: `init_connect``SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Kattintson a **Mentés** gombra a módosítások mentéséhez.

## <a name="working-with-the-time-zone-parameter"></a>Az időzóna-paraméter használata

### <a name="populating-the-time-zone-tables"></a>Az időzóna-táblák feltöltése

A kiszolgálón található időzóna-táblákat úgy töltheti fel, hogy meghívja a `mysql.az_load_timezone` tárolt eljárást egy olyan eszközről, mint a MySQL parancssor vagy a MySQL Workbench.

> [!NOTE]
> Ha a `mysql.az_load_timezone` MySQL Workbenchből futtatja a parancsot, előfordulhat, hogy először ki kell kapcsolnia a biztonságos frissítési módot a használatával `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Indítsa újra a kiszolgálót az időzóna-táblák megfelelő kitöltésének biztosításához. A kiszolgáló újraindításához használja a [Azure Portal](howto-restart-server-portal.md) vagy a [parancssori](howto-restart-server-cli.md)felületet.
A rendelkezésre álló időzóna-értékek megtekintéséhez futtassa a következő parancsot:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>A globális szintű időzóna beállítása

A globális szintű időzónát a Azure Portal **kiszolgáló paraméterek** lapján lehet beállítani. Az alábbi beállítás a globális időzónát az "USA/csendes-óceáni" értékre állítja be.

![Időzóna-paraméter beállítása](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>A munkamenet-szint időzónájának beállítása

A munkamenet-szint időzónája beállítható úgy, hogy a `SET time_zone` parancsot egy olyan eszközről futtatja, mint a MySQL parancssor vagy a MySQL Workbench. Az alábbi példa az időzónát az **USA/csendes-óceáni** időzónára állítja be.

```sql
SET time_zone = 'US/Pacific';
```

A [dátum-és Időfüggvényekhez](https://mariadb.com/kb/en/library/convert_tz/)tekintse meg a MariaDB dokumentációját.

## <a name="next-steps"></a>További lépések

- További információ a [kiszolgálói paraméterekről](concepts-server-parameters.md)