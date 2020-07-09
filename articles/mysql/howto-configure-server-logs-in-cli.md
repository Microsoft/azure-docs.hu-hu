---
title: Lassú lekérdezési naplók elérése – Azure CLI – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan érheti el a lassú lekérdezési naplókat Azure Database for MySQL az Azure CLI használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.openlocfilehash: 356d4e916695c9ac8bcece2867a23d7f7f53d299
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86101665"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Lassú lekérdezési naplók konfigurálása és elérése az Azure CLI használatával
Az Azure CLI-vel, az Azure parancssori segédprogrammal töltheti le az Azure Database for MySQL lassú lekérdezési naplókat.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for MySQL kiszolgáló](quickstart-create-mysql-server-database-using-azure-cli.md)
- Az [Azure CLI](/cli/azure/install-azure-cli) vagy Azure Cloud Shell a böngészőben

## <a name="configure-logging"></a>Naplózás konfigurálása
A kiszolgálót úgy is beállíthatja, hogy a következő lépések végrehajtásával hozzáférhessen a MySQL lassú lekérdezési naplóhoz:
1. Kapcsolja be a lassú lekérdezés naplózását úgy, hogy a **lassú \_ lekérdezési \_ napló** paraméterét bekapcsolja értékre.
2. Válassza ki, hová szeretné kiadni a naplókat a naplók ** \_ kimenetének**használatához. Ha a naplókat a helyi tárolóba és Azure Monitor diagnosztikai naplókba kívánja küldeni, válassza a **fájl**elemet. Naplók csak Azure Monitor naplókba való küldéséhez válassza a **nincs** lehetőséget
3. Módosítsa a többi paramétert, például a **hosszú \_ lekérdezési \_ időt** és a ** \_ lassú \_ rendszergazdai \_ utasítások naplózását**.

Ha meg szeretné tudni, hogyan állíthatja be a paraméterek értékét az Azure CLI-n keresztül, tekintse meg a [kiszolgáló paramétereinek konfigurálása](howto-configure-server-parameters-using-cli.md)című témakört.

A következő CLI-parancs például bekapcsolja a lassú lekérdezési naplót, beállítja a hosszú lekérdezési időt 10 másodpercre, majd kikapcsolja a lassú rendszergazdai utasítás naplózását. Végül felsorolja az Áttekintés konfigurációs beállításait.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Azure Database for MySQL-kiszolgáló naplófájljainak listázása
Ha **log_output** "file"-ra van konfigurálva, közvetlenül a kiszolgáló helyi tárolójából érheti el a naplókat. A kiszolgáló rendelkezésre álló lassú lekérdezési naplófájljainak listázásához futtassa az az [MySQL Server-logs List](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) parancsot.

A kiszolgálói **mydemoserver.mysql.database.Azure.com** tartozó naplófájlokat az erőforráscsoport **myresourcegroup**lehet kilistázni. Ezután irányítsa a naplófájlok listáját a naplófájlok ** \_ \_list.txt**nevű szövegfájlba.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Ha **log_output** "file"-ra van konfigurálva, akkor az az [MySQL Server-logs Download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) paranccsal letöltheti az egyes naplófájlokat a kiszolgálóról.

A következő példa használatával letöltheti a kiszolgáló **mydemoserver.mysql.database.Azure.com** tartozó naplófájlt a helyi környezet **myresourcegroup** .
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Következő lépések
- További információ [a Azure Database for MySQL lassú lekérdezési naplóiról](concepts-server-logs.md).
