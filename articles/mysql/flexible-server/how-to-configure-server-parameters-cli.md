---
title: Kiszolgálói paraméterek konfigurálása – Azure CLI – Azure Database for MySQL rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni a szolgáltatási paramétereket Azure Database for MySQL rugalmas kiszolgálón az Azure CLI parancssori segédprogram használatával.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 58e7c024d6494aee745884997e42b527c51ab237
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94489539"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Kiszolgáló paramétereinek konfigurálása Azure Database for MySQL rugalmas kiszolgálón az Azure CLI használatával

> [!IMPORTANT] 
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Az Azure CLI-vel, az Azure parancssori segédprogrammal megtekintheti, megjelenítheti és frissítheti az Azure Database for MySQL rugalmas kiszolgáló paramétereit. A kiszolgálói paraméterek az alapértelmezett és ajánlott értékkel vannak konfigurálva a kiszolgáló létrehozásakor.  

Ez a cikk bemutatja, hogyan listázhatja, jelenítheti meg és frissítheti a kiszolgálói paramétereket az Azure CLI használatával.

>[!Note]
> A kiszolgálói paraméterek globálisan frissíthetők a kiszolgáló szintjén, az [Azure CLI](./how-to-configure-server-parameters-cli.md) vagy a [Azure Portal](./how-to-configure-server-parameters-portal.md) használatával

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Egy Azure Database for MySQL rugalmas kiszolgáló](quickstart-create-server-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja a Azure Cloud shellt a böngészőben.

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL rugalmas kiszolgáló kiszolgálói paramétereinek listázása
A kiszolgáló összes paraméterének és értékének listázásához futtassa az az [MySQL flexibilis-Server paraméter List](/cli/azure/mysql/flexible-server/parameter) parancsot.

A kiszolgáló **mydemoserver.mysql.database.Azure.com** a **myresourcegroup** erőforráscsoport alatt listázhatja.
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
Az egyes felsorolt paraméterek definícióját a MySQL-referenciával foglalkozó szakaszban találja a [kiszolgálói rendszerváltozók](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)című részben.

## <a name="show-server-parameter-details"></a>Kiszolgáló paraméter részleteinek megjelenítése
A kiszolgáló egy adott paraméterének részleteinek megjelenítéséhez futtassa az az [MySQL flexibilis-Server paraméter show](/cli/azure/mysql/flexible-server/parameter) parancsot.

Ez a példa a **lassú \_ lekérdezési \_ napló** kiszolgálói paraméterének részleteit jeleníti meg a kiszolgáló **mydemoserver.mysql.database.Azure.com** az erőforráscsoport **myresourcegroup alatt.**
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>Kiszolgáló paraméter értékének módosítása
Egy bizonyos kiszolgálói paraméter értékét is módosíthatja, amely frissíti a MySQL-kiszolgáló motorjának alapjául szolgáló konfigurációs értéket. A Server paraméter frissítéséhez használja az az [MySQL flexibilis-Server paraméter set](/cli/azure/mysql/flexible-server/parameter) parancsot. 

A kiszolgáló mydemoserver.mysql.database.azure.com **lassú \_ lekérdezési \_ napló** kiszolgáló paraméterének  frissítése a myresourcegroup erőforráscsoport alatt **.**
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
Ha alaphelyzetbe kívánja állítani egy paraméter értékét, hagyja ki a választható `--value` paramétert, és a szolgáltatás alkalmazza az alapértelmezett értéket. A fenti példában a következőképpen fog kinézni:
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
Ez a kód visszaállítja a **lassú \_ lekérdezési \_ naplót** az alapértelmezett értékre **.** 

## <a name="setting-non-modifiable-server-parameters"></a>Nem módosítható kiszolgálói paraméterek beállítása

Ha a frissíteni kívánt kiszolgálói paraméter nem módosítható, akkor opcionálisan a paramétert is beállíthatja a kapcsolódási szinten `init_connect` . Ezzel beállítja a kiszolgálóhoz csatlakozó egyes ügyfelek kiszolgálói paramétereit. 

Frissítse a kiszolgáló **mydemoserver.mysql.database.Azure.com** **init \_ csatlakozási** kiszolgáló paraméterét az erőforráscsoport **myresourcegroup** az értékek, például a karakterkészlet beállításához.
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
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
> Indítsa újra a kiszolgálót az időzóna-táblák megfelelő kitöltésének biztosításához.<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

A rendelkezésre álló időzóna-értékek megtekintéséhez futtassa a következő parancsot:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>A globális szintű időzóna beállítása

A globális szintű időzónát az az [MySQL flexibilis-Server paraméter set](/cli/azure/mysql/flexible-server/parameter) paranccsal lehet beállítani.

A következő parancs frissíti a kiszolgáló **mydemoserver.mysql.database.Azure.com** **\_ időzóna** -kiszolgáló paraméterét az **USA/csendes-óceáni** **myresourcegroup** .

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>A munkamenet-szint időzónájának beállítása

A munkamenet-szint időzónája beállítható úgy, hogy a `SET time_zone` parancsot egy olyan eszközről futtatja, mint a MySQL parancssor vagy a MySQL Workbench. Az alábbi példa az időzónát az **USA/csendes-óceáni** időzónára állítja be.  

```sql
SET time_zone = 'US/Pacific';
```

Tekintse meg a MySQL dokumentációját a [dátum-és Időfüggvényekhez](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Következő lépések

- [Kiszolgáló paramétereinek konfigurálása Azure Portal](./how-to-configure-server-parameters-portal.md)
