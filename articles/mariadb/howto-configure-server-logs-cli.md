---
title: Lassú lekérdezési naplók elérése – Azure CLI – Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan érheti el a lassú naplókat Azure Database for MariaDB az Azure CLI parancssori segédprogram használatával.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cea1571a48afe00350dde247c3c10e222a446247
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662389"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Az Azure Database for Maria DB lassú lekérdezési naplók konfigurálása és elérése az Azure CLI használatával

Az Azure CLI-vel, az Azure parancssori segédprogrammal töltheti le az Azure Database for MariaDB lassú lekérdezési naplókat.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for MariaDB kiszolgáló](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Az [Azure CLI](/cli/azure/install-azure-cli) vagy Azure Cloud Shell a böngészőben

## <a name="configure-logging"></a>Naplózás konfigurálása
A kiszolgálót úgy is beállíthatja, hogy a következő lépések végrehajtásával hozzáférhessen a MySQL lassú lekérdezési naplóhoz:
1. Kapcsolja be a lassú lekérdezés naplózását úgy, hogy a **lassú \_ lekérdezési \_ napló** paraméterét bekapcsolja értékre.
2. Válassza ki, hová szeretné kiadni a naplókat a naplók **\_ kimenetének** használatához. Ha a naplókat a helyi tárolóba és Azure Monitor diagnosztikai naplókba kívánja küldeni, válassza a **fájl** elemet. Naplók csak Azure Monitor naplókba való küldéséhez válassza a **nincs** lehetőséget
3. Módosítsa a többi paramétert, például a **hosszú \_ lekérdezési \_ időt** és a **\_ lassú \_ rendszergazdai \_ utasítások naplózását**.

Ha meg szeretné tudni, hogyan állíthatja be a paraméterek értékét az Azure CLI-n keresztül, tekintse meg a [kiszolgáló paramétereinek konfigurálása](howto-configure-server-parameters-cli.md)című témakört.

A következő CLI-parancs például bekapcsolja a lassú lekérdezési naplót, beállítja a hosszú lekérdezési időt 10 másodpercre, majd kikapcsolja a lassú rendszergazdai utasítás naplózását. Végül felsorolja az Áttekintés konfigurációs beállításait.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Azure Database for MariaDB-kiszolgáló naplófájljainak listázása
Ha **log_output** "file"-ra van konfigurálva, közvetlenül a kiszolgáló helyi tárolójából érheti el a naplókat. A kiszolgáló rendelkezésre álló lassú lekérdezési naplófájljainak listázásához futtassa az az [MariaDB Server-logs List](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) parancsot.

A kiszolgálói **mydemoserver.MariaDB.database.Azure.com** tartozó naplófájlokat az erőforráscsoport **myresourcegroup** lehet kilistázni. Ezután irányítsa a naplófájlok listáját a naplófájlok **\_ \_list.txt** nevű szövegfájlba.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Ha **log_output** "file"-ra van konfigurálva, akkor az az [MariaDB Server-logs Download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) paranccsal letöltheti az egyes naplófájlokat a kiszolgálóról.

A következő példa használatával letöltheti a kiszolgáló **mydemoserver.MariaDB.database.Azure.com** tartozó naplófájlt a helyi környezet **myresourcegroup** .
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Következő lépések
- További információ [a Azure Database for MariaDB lassú lekérdezési naplóiról](concepts-server-logs.md).
