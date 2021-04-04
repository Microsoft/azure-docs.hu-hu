---
title: Kiszolgáló paramétereinek konfigurálása – Azure Portal-Azure Database for MySQL
description: Ez a cikk bemutatja, hogyan konfigurálhatja a MySQL-kiszolgáló paramétereit Azure Database for MySQL a Azure Portal használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 363be8b34f230b812bc24276e1f3925faf0cdc1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540841"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Kiszolgálói paraméterek konfigurálása Azure Database for MySQL a Azure Portal használatával

Azure Database for MySQL támogatja egyes kiszolgálói paraméterek konfigurációját. Ez a cikk azt ismerteti, hogyan konfigurálhatja ezeket a paramétereket a Azure Portal használatával. Nem minden kiszolgáló paraméter módosítható.

>[!Note]
> A kiszolgálóparaméterek a kiszolgáló szintjén frissíthetők globálisan. Használja az [Azure CLI-t](./howto-configure-server-parameters-using-cli.md), a [PowerShellt](./howto-configure-server-parameters-using-powershell.md) vagy az [Azure Portalt](./howto-server-parameters.md).

## <a name="configure-server-parameters"></a>Kiszolgáló paramétereinek konfigurálása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd keresse meg a Azure Database for MySQL-kiszolgálót.
2. A **Beállítások** szakaszban kattintson a **kiszolgálói paraméterek** elemre a Azure Database for MySQL kiszolgáló kiszolgálói paraméterek lapjának megnyitásához.
:::image type="content" source="./media/howto-server-parameters/auzre-portal-server-parameters.png" alt-text="Azure Portal kiszolgáló paramétereinek lapja":::
3. Keresse meg a módosítani kívánt beállításokat. A **description (Leírás** ) oszlopban tekintse át a cél és az engedélyezett értékek ismeretét.
:::image type="content" source="./media/howto-server-parameters/3-toggle_parameter.png" alt-text="Számbavétel legördülő lista":::
4. A módosítások mentéséhez kattintson a  **Save (Mentés** ) gombra.
:::image type="content" source="./media/howto-server-parameters/4-save_parameters.png" alt-text="Módosítások mentése vagy elvetése":::
5. Ha új értékeket mentett a paraméterek számára, az **összes visszaállítása az alapértelmezett** értékre lehetőség kiválasztásával bármikor visszaállíthatja az alapértelmezett értékeket.
:::image type="content" source="./media/howto-server-parameters/5-reset_parameters.png" alt-text="Az összes visszaállítása az alapértelmezett értékre":::

## <a name="setting-parameters-not-listed"></a>Nem felsorolt paraméterek beállítása

Ha a frissíteni kívánt kiszolgálói paraméter nem szerepel a Azure Portalban, akkor opcionálisan a paramétert is megadhatja a kapcsolódási szinten a használatával `init_connect` . Ezzel beállítja a kiszolgálóhoz csatlakozó egyes ügyfelek kiszolgálói paramétereit. 

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
> Indítsa újra a kiszolgálót az időzóna-táblák megfelelő kitöltésének biztosításához. A kiszolgáló újraindításához használja a [Azure Portal](howto-restart-server-portal.md) vagy a [parancssori](howto-restart-server-cli.md)felületet.

A rendelkezésre álló időzóna-értékek megtekintéséhez futtassa a következő parancsot:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>A globális szintű időzóna beállítása

A globális szintű időzónát a Azure Portal **kiszolgáló paraméterek** lapján lehet beállítani. Az alábbi beállítás a globális időzónát az "USA/csendes-óceáni" értékre állítja be.

:::image type="content" source="./media/howto-server-parameters/timezone.png" alt-text="Időzóna-paraméter beállítása":::

### <a name="setting-the-session-level-time-zone"></a>A munkamenet-szint időzónájának beállítása

A munkamenet-szint időzónája beállítható úgy, hogy a `SET time_zone` parancsot egy olyan eszközről futtatja, mint a MySQL parancssor vagy a MySQL Workbench. Az alábbi példa az időzónát az **USA/csendes-óceáni** időzónára állítja be.

```sql
SET time_zone = 'US/Pacific';
```

Tekintse meg a MySQL dokumentációját a [dátum-és Időfüggvényekhez](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Következő lépések

- [Azure Database for MySQLhoz tartozó kapcsolatok kódtárai](concepts-connection-libraries.md).
