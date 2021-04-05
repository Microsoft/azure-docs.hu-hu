---
title: Kiszolgálói paraméterek konfigurálása – Azure CLI – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni a szolgáltatás paramétereit Azure Database for MySQL az Azure CLI parancssori segédprogram használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a5a84d93400e713f66545387fd146148ee735c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541538"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-cli"></a>Kiszolgálói paraméterek konfigurálása Azure Database for MySQL az Azure CLI használatával
Az Azure CLI, az Azure parancssori segédprogram használatával listázhatja, megjelenítheti és frissítheti az Azure Database for MySQL-kiszolgáló konfigurációs paramétereit. A motor konfigurációjának egy részhalmaza a kiszolgáló szintjén érhető el, és módosítható. 

>[!Note]
> A kiszolgálói paraméterek globálisan frissíthetők a kiszolgáló szintjén, az [Azure CLI](./howto-configure-server-parameters-using-cli.md), a [PowerShell](./howto-configure-server-parameters-using-powershell.md)vagy a [Azure Portal](./howto-server-parameters.md) használatával

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Egy Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja a Azure Cloud shellt a böngészőben.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Azure Database for MySQL kiszolgáló kiszolgáló-konfigurációs paramétereinek listázása
A kiszolgálók és azok értékei összes módosítható paraméterének listázásához futtassa az az [MySQL Server Configuration List](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) parancsot.

A kiszolgálói **mydemoserver.mysql.database.Azure.com** tartozó kiszolgálói konfigurációs paramétereket a **myresourcegroup** erőforráscsoport alatt listázhatja.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Az egyes felsorolt paraméterek definícióját a MySQL-referenciával foglalkozó szakaszban találja a [kiszolgálói rendszerváltozók](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)című részben.

## <a name="show-server-configuration-parameter-details"></a>Kiszolgáló konfigurációs paramétereinek megjelenítése – részletek
A kiszolgálók egy adott konfigurációs paraméterének részleteinek megjelenítéséhez futtassa az az [MySQL Server Configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) parancsot.

Ez a példa a **lassú \_ lekérdezési \_ napló** kiszolgálójának konfigurációs paraméterének részleteit jeleníti meg a kiszolgáló **mydemoserver.mysql.database.Azure.com** az erőforráscsoport **myresourcegroup alatt.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Kiszolgáló-konfigurációs paraméter értékének módosítása
Egy bizonyos kiszolgáló-konfigurációs paraméter értékét is módosíthatja, amely frissíti a MySQL-kiszolgáló motorjának alapjául szolgáló konfigurációs értéket. A konfiguráció frissítéséhez használja az az [MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) parancsot. 

A **lassú \_ lekérdezési \_ napló** kiszolgáló-konfigurációs paraméterének frissítése a kiszolgáló **mydemoserver.mysql.database.Azure.com** az erőforráscsoport **myresourcegroup területen.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Ha alaphelyzetbe kívánja állítani egy konfigurációs paraméter értékét, hagyja ki a választható `--value` paramétert, és a szolgáltatás alkalmazza az alapértelmezett értéket. A fenti példában a következőképpen fog kinézni:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Ez a kód visszaállítja a **lassú \_ lekérdezési \_ napló** konfigurációját az alapértelmezett értékre. 

## <a name="setting-parameters-not-listed"></a>Nem felsorolt paraméterek beállítása
Ha a frissíteni kívánt kiszolgálói paraméter nem szerepel a Azure Portalban, akkor opcionálisan a paramétert is megadhatja a kapcsolódási szinten a használatával `init_connect` . Ezzel beállítja a kiszolgálóhoz csatlakozó egyes ügyfelek kiszolgálói paramétereit. 

Frissítse az **init \_ csatlakozási** kiszolgáló konfigurációs paraméterét a kiszolgáló **mydemoserver.mysql.database.Azure.com** az erőforráscsoport **myresourcegroup** alatt az értékek, például a karakterkészlet megadásához.
```azurecli-interactive
az mysql server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

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

A globális szintű időzónát az az [MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) paranccsal lehet beállítani.

A következő parancs frissíti a kiszolgálói **mydemoserver.mysql.database.Azure.com** **\_ időzóna** -kiszolgáló konfigurációs paraméterét az **USA/csendes-óceáni erőforrás-** csoport **myresourcegroup** .

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>A munkamenet-szint időzónájának beállítása

A munkamenet-szint időzónája beállítható úgy, hogy a `SET time_zone` parancsot egy olyan eszközről futtatja, mint a MySQL parancssor vagy a MySQL Workbench. Az alábbi példa az időzónát az **USA/csendes-óceáni** időzónára állítja be.  

```sql
SET time_zone = 'US/Pacific';
```

Tekintse meg a MySQL dokumentációját a [dátum-és Időfüggvényekhez](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Következő lépések

- [Kiszolgáló paramétereinek konfigurálása Azure Portal](howto-server-parameters.md)
