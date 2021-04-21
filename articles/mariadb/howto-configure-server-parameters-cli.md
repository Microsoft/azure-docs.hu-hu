---
title: Kiszolgálóparaméterek konfigurálása – Azure CLI – Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a szolgáltatás paramétereit a Azure Database for MariaDB az Azure CLI parancssori segédprogrammal.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a3ebcf5a381efceb5e7de503caf88f4ffd3b504e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774736"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-the-azure-cli"></a>Kiszolgálóparaméterek konfigurálása Azure Database for MariaDB Azure CLI használatával
Az Azure CLI, az Azure parancssori segédprogram használatával listába lehet sorolni, meg lehet mutatni és frissíteni lehet egy Azure Database for MariaDB-kiszolgáló konfigurációs paramétereit. A motorkonfigurációk egy része kiszolgálószinten van elérhető, és módosítható.

>[!Note]
> A kiszolgálóparaméterek a kiszolgáló szintjén frissíthetők globálisan. Használja az [Azure CLI-t](./howto-configure-server-parameters-cli.md), a [PowerShellt](./howto-configure-server-parameters-using-powershell.md) vagy az [Azure Portalt](./howto-server-parameters.md).

## <a name="prerequisites"></a>Előfeltételek
Az útmutató lépésenként a következőre lesz szüksége:
- [Egy Azure Database for MariaDB-kiszolgáló](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Az Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogramja, vagy használja Azure Cloud Shell a böngészőben.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>List server configuration parameters for Azure Database for MariaDB server
A kiszolgálón található összes módosítható paraméter és azok értékeinek listához futtassa az [az mariadb server configuration list parancsot.](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_list)

A kiszolgálócsoport kiszolgáló-konfigurációs paramétereit a **myresourcegroup** **mydemoserver.mariadb.database.azure.com** listába sorolhatja.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Az egyes felsorolt paraméterek definícióját a Server System Variables (Kiszolgálórendszer változói) című részben, a MariaDB-referenciában [láthatja.](https://mariadb.com/kb/en/library/server-system-variables/)

## <a name="show-server-configuration-parameter-details"></a>A kiszolgáló konfigurációs paraméterének részleteinek megjelenítése
Egy kiszolgáló adott konfigurációs paraméterének részleteinek megjelenítése az [az mariadb server configuration show paranccsal.](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_show)

Ez a példa a myresourcegroup erőforráscsoportban mydemoserver.mariadb.database.azure.com kiszolgálókiszolgáló lassú **lekérdezési naplókiszolgálójának konfigurációs paraméterét mutatja be.** **\_ \_** 
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Kiszolgáló-konfigurációs paraméter értékének módosítása
Egy adott kiszolgáló-konfigurációs paraméter értékét is módosíthatja, amely frissíti a MariaDB-kiszolgálómotor mögöttes konfigurációs értékét. A konfiguráció frissítéséhez használja az [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_set) parancsot. 

A lassú **lekérdezési naplókiszolgáló \_ \_** konfigurációs  paraméterének frissítéséhez mydemoserver.mariadb.database.azure.com **myresourcegroup erőforráscsoportban.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Ha alaphelyzetbe szeretné állítani egy konfigurációs paraméter értékét, ne használja a választható paramétert, és a szolgáltatás `--value` az alapértelmezett értéket alkalmazza. A fenti példában így nézne ki:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Ez a kód visszaállítja **a lassú \_ lekérdezési napló \_ konfigurációját** az alapértelmezett **OFF** értékre. 

## <a name="setting-parameters-not-listed"></a>A listán nem szereplő beállítási paraméterek
Ha a frissíteni kívánt kiszolgálóparaméter nem szerepel a Azure Portal, a paramétert kapcsolatszinten is beállíthatja a `init_connect` használatával. Ez beállítja a kiszolgálóhoz csatlakozó összes ügyfél kiszolgálóparaméterét. 

Frissítse az **init \_ connect** kiszolgáló konfigurációs paraméterét **a** mydemoserver.mariadb.database.azure.com erőforráscsoportban a **myresourcegroup** erőforráscsoportban, hogy olyan értékeket állítson be, mint például a karakterkészlet.
```azurecli-interactive
az mariadb server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>Az időzóna paraméter használata

### <a name="populating-the-time-zone-tables"></a>Az időzóna-táblák adatokban való megugrása

A kiszolgálón lévő időzóna-táblák a tárolt eljárásnak a MariaDB parancssorból vagy a MariaDB Workbenchhez hasonló eszközből való hívásával `mysql.az_load_timezone` töltve adatokat is kiadhatóak.

> [!NOTE]
> Ha a Parancsot a `mysql.az_load_timezone` MariaDB Workbenchből futtatja, előfordulhat, hogy először ki kell kapcsolnia a csökkentett frissítési módot a `SET SQL_SAFE_UPDATES=0;` használatával.

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

A globális szintű időzóna az [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_set) paranccsal beállítható.

A következő parancs a **myresourcegroup** erőforráscsoportban mydemoserver.mariadb.database.azure.com kiszolgálókiszolgáló konfigurációs paraméterét az **USA/Csendes-óceáni régióra frissíti.** **\_** 

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>A munkamenetszintű időzóna beállítása

A munkamenetszintű időzóna a parancs futtatásával beállítható egy olyan eszközről, mint a MariaDB parancssora vagy a `SET time_zone` MariaDB Workbench. Az alábbi példa az USA/Csendes-óceáni időzóna **időzónát** állítja be.  

```sql
SET time_zone = 'US/Pacific';
```

Tekintse meg a Dátum és idő függvények [MariaDB-dokumentációját.](https://mariadb.com/kb/en/library/date-time-functions/)

## <a name="next-steps"></a>Következő lépések

- [Kiszolgálóparaméterek konfigurálása a Azure Portal](howto-server-parameters.md)
