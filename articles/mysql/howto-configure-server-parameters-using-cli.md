---
title: Kiszolgálóparaméterek konfigurálása – Azure CLI – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a szolgáltatás paramétereit a Azure Database for MySQL az Azure CLI parancssori segédprogrammal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 94dd7f72f6411934587c76850a05d6885e9f6862
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763216"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-cli"></a>Kiszolgálóparaméterek konfigurálása Azure Database for MySQL Azure CLI használatával
Az Azure CLI, az Azure parancssori segédprogram használatával listathatja, mutathatja és frissítheti egy Azure Database for MySQL-kiszolgáló konfigurációs paramétereit. A motorkonfigurációk egy része kiszolgálószinten van elérhető, és módosítható. 

>[!Note]
> A kiszolgálóparaméterek globálisan frissíthetők kiszolgálószinten, az [Azure CLI,](./howto-configure-server-parameters-using-cli.md) [a PowerShell](./howto-configure-server-parameters-using-powershell.md)vagy a [Azure Portal](./howto-server-parameters.md)

## <a name="prerequisites"></a>Előfeltételek
Az útmutató lépésenként a következőre lesz szüksége:
- [Egy Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Az Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogramja, vagy használja Azure Cloud Shell a böngészőben.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>List server configuration parameters for Azure Database for MySQL server
A kiszolgálón található összes módosítható paraméter és azok értékeinek listához futtassa [az az mysql server configuration list parancsot.](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list)

A kiszolgálócsoport kiszolgáló-konfigurációs paramétereit a **myresourcegroup** **mydemoserver.mysql.database.azure.com** listába sorolhatja.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Az egyes felsorolt paraméterek definíciójért tekintse meg a MySQL-referencia szakaszt a [kiszolgáló rendszerváltozóiról.](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)

## <a name="show-server-configuration-parameter-details"></a>A kiszolgáló konfigurációs paraméterének részleteinek megjelenítése
Egy adott kiszolgáló adott konfigurációs paraméterének részleteinek megjelenítése az [az mysql server configuration show paranccsal.](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show)

Ez a példa a myresourcegroup erőforráscsoportban mydemoserver.mysql.database.azure.com kiszolgálókiszolgáló lassú **lekérdezési naplókiszolgálójának konfigurációs paraméterét mutatja be.** **\_ \_** 
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Kiszolgáló-konfigurációs paraméter értékének módosítása
Egy adott kiszolgáló-konfigurációs paraméter értékét is módosíthatja, amely frissíti a MySQL-kiszolgálómotor mögöttes konfigurációs értékét. A konfiguráció frissítéséhez használja [az az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) parancsot. 

A lassú **lekérdezési naplókiszolgáló \_ \_** konfigurációs  paraméterének frissítéséhez a mydemoserver.mysql.database.azure.com **a myresourcegroup erőforráscsoportban.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Ha alaphelyzetbe szeretné állítani egy konfigurációs paraméter értékét, ne használja a választható paramétert, és a szolgáltatás `--value` az alapértelmezett értéket alkalmazza. A fenti példában így nézne ki:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Ez a kód visszaállítja **a lassú \_ lekérdezési napló \_ konfigurációját** az alapértelmezett **OFF** értékre. 

## <a name="setting-parameters-not-listed"></a>A listán nem szereplő beállítási paraméterek
Ha a frissíteni kívánt kiszolgálóparaméter nem szerepel a Azure Portal, a paramétert kapcsolatszinten is beállíthatja a `init_connect` használatával. Ez beállítja a kiszolgálóhoz csatlakozó összes ügyfél kiszolgálóparaméterét. 

Frissítse a **myresourcegroup** erőforráscsoportban található mydemoserver.mysql.database.azure.com **init \_** connect kiszolgáló konfigurációs paraméterét, hogy olyan értékeket állítson be, mint például a karakterkészlet. 
```azurecli-interactive
az mysql server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>Az időzóna paraméter használata

### <a name="populating-the-time-zone-tables"></a>Az időzóna-táblák adatokban való megugrása

A kiszolgálón lévő időzóna-táblák a tárolt eljárásnak a MySQL parancssorból vagy a MySQL Workbenchhez hasonló eszközből való hívását `mysql.az_load_timezone` követően adatokat kaphatnak.

> [!NOTE]
> Ha a MySQL Workbenchből futtatja a parancsot, előfordulhat, hogy először ki kell kapcsolnia a csökkentett frissítési `mysql.az_load_timezone` módot a `SET SQL_SAFE_UPDATES=0;` használatával.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Az időzóna-táblák megfelelő feltöltéséhez indítsa újra a kiszolgálót. A kiszolgáló újraindításához [](howto-restart-server-portal.md) használja a Azure Portal [cli-t.](howto-restart-server-cli.md)

Az elérhető időzónaértékek megtekintéséhez futtassa a következő parancsot:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>A globális szintű időzóna beállítása

A globális szintű időzóna az [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) paranccsal beállítható.

A következő parancs a **myresourcegroup** erőforráscsoportban mydemoserver.mysql.database.azure.com kiszolgálókiszolgáló konfigurációs paraméterét az **USA/Csendes-óceáni régióra frissíti.** **\_** 

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>A munkamenetszintű időzóna beállítása

A munkamenetszintű időzóna úgy beállítható, hogy a parancsot egy olyan eszközről futtatja, mint a MySQL parancssor vagy a `SET time_zone` MySQL Workbench. Az alábbi példa az USA/Csendes-óceáni időzóna **időzónát** állítja be.  

```sql
SET time_zone = 'US/Pacific';
```

Tekintse meg a Dátum és idő függvények [MySQL-dokumentációját.](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)


## <a name="next-steps"></a>Következő lépések

- [Kiszolgálóparaméterek konfigurálása a Azure Portal](howto-server-parameters.md)
