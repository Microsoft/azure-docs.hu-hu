---
title: A szolgáltatás paramétereinek konfigurálása az Azure Database for MySQL-hez
description: Ez a cikk ismerteti a szolgáltatás paramétereinek konfigurálása az Azure Database for MySQL-hez az Azure CLI parancssori segédprogram használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: b0d7bbdc3e1dcad6f6cecb57b15e2e5df6b3fd28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525433"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Kiszolgáló konfigurációs paramétereinek testreszabása az Azure CLI-vel
Lista, megjelenítése és konfigurációs paramétereinek frissítése egy Azure Database for MySQL-kiszolgálóhoz az Azure CLI, az Azure parancssori segédprogram használatával. Motor konfigurációk egy részét a kiszolgálói szinten érhető el, és módosíthatók. 

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- [Egy Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Az Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja az Azure Cloud Shellt a böngészőben.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Kiszolgáló konfigurációs paramétereinek listázása az Azure Database for MySQL-kiszolgáló
A kiszolgáló és az értéküket az összes módosíthatóvá paraméter listázásához, futtassa a [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) parancsot.

A kiszolgáló a kiszolgáló konfigurációs paramétereinek listázhatja **mydemoserver.mysql.database.azure.com** erőforráscsoportba tartozó **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Definíciója a listán szereplő paraméterek, tekintse meg a MySQL a referenciaszakasz [kiszolgáló rendszerváltozók](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Kiszolgáló konfigurációs paraméter részletek megjelenítése
Egy adott konfigurációs paraméter a kiszolgáló részleteinek megjelenítéséhez, futtassa a [az mysql server configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) parancsot.

Ez a példa bemutatja a részletek a **lassú\_lekérdezés\_log** kiszolgáló konfigurációs paraméter kiszolgáló **mydemoserver.mysql.database.azure.com** erőforráscsoportba tartozó **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Módosítsa a kiszolgáló konfigurációs paraméter értéke
Egy bizonyos kiszolgáló konfigurációs paraméter értékét, amely frissíti az alapul szolgáló konfigurációs értéket az MySQL server motor módosíthatja is. Frissítse a konfigurációt, használja a [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) parancsot. 

Frissítése az **lassú\_lekérdezés\_log** kiszolgáló konfigurációs paraméter kiszolgáló **mydemoserver.mysql.database.azure.com** erőforráscsoportba tartozó  **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Ha szeretne egy konfigurációs paraméter értékének alaphelyzetbe állítása, hagyja el a választható `--value` paraméterhez, és a szolgáltatás az alapértelmezett érték vonatkozik. A fenti példában akkor jelenne meg:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Ez a kód alaphelyzetbe állítása a **lassú\_lekérdezés\_log** konfigurációját, és az alapértelmezett érték **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Időzóna-paraméter használata

### <a name="populating-the-time-zone-tables"></a>Az időzóna táblák feltöltése

A kiszolgálón az időzóna táblázatok meghívásával lehet adatokkal feltölteni a `az_load_timezone` tárolt eljárás egy eszközt, például a MySQL parancssori vagy a MySQL Workbench segítségével.

> [!NOTE]
> Ha futtatja a `az_load_timezone` parancsot a MySQL Workbench, szükség lehet az első biztonságos frissítési mód kikapcsolása használatával `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Rendelkezésre álló időzóna értékek megtekintéséhez futtassa a következő parancsot:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>A globális szintű időzóna beállítása

A globális szintű időzóna lze nastavit pomocí vlastností a [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) parancsot.

A következő parancsot a frissítések a **idő\_zóna** kiszolgáló konfigurációs paraméter kiszolgáló **mydemoserver.mysql.database.azure.com** erőforráscsoportba tartozó  **myresourcegroup** való **USA / csendes-óceáni térség**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>A munkamenet szintű időzóna beállítása

A munkamenet-szolgáltatói időzóna futtatásával állítható a `SET time_zone` egy eszköz, például a MySQL-parancssor vagy a MySQL Workbench parancsot. Az alábbi példa állítja be az időzónát a **USA / csendes-óceáni térség** időzóna.  

```sql
SET time_zone = 'US/Pacific';
```

A MySQL dokumentációjában [dátum és időpont függvényeinek](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>További lépések

- Konfigurálása [kiszolgálóparaméterek az Azure Portalon](howto-server-parameters.md)