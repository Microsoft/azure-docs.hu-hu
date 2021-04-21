---
title: Lassú lekérdezési naplók elérése – Azure CLI – Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan férhet hozzá a lassú Azure Database for MariaDB az Azure CLI parancssori segédprogrammal.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 42382076b6c14989eb153725e991c8ef047ad15b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774790"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Az Azure Database for Maria DB lassú lekérdezési naplóinak konfigurálása és elérése az Azure CLI használatával

A lassú Azure Database for MariaDB naplókat az Azure CLI, az Azure parancssori segédprogram használatával töltheti le.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató lépésenként a következőre lesz szüksége:
- [Azure Database for MariaDB kiszolgáló](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Az [Azure CLI vagy](/cli/azure/install-azure-cli) Azure Cloud Shell böngészőben

## <a name="configure-logging"></a>Naplózás konfigurálása
A következő lépésekkel konfigurálhatja a kiszolgálót a MySQL lassú lekérdezési naplójának elérésére:
1. Kapcsolja be a lassú lekérdezésnaplózást a **lassú \_ \_ lekérdezésnapló paraméter** BE beállításával.
2. Válassza ki a naplók kimenetének helyét a **naplókimenet \_ használatával.** Ha a helyi tárolóba és a diagnosztikai naplókba is Azure Monitor, válassza a **Fájl lehetőséget.** Ha csak a naplóknak Azure Monitor, válassza a Nincs **lehetőséget.**
3. Állítson be más paramétereket, például a hosszú lekérdezési időt és **a lassú \_ \_ naplózási rendszergazdai \_ utasításokat.** **\_ \_**

A paraméterek értékének Azure CLI-alapú beállítását lásd: [Kiszolgálóparaméterek konfigurálása.](howto-configure-server-parameters-cli.md)

A következő CLI-parancs például bekapcsolja a lassú lekérdezési naplót, 10 másodpercre állítja a hosszú lekérdezési időt, majd kikapcsolja a lassú rendszergazdai utasítás naplózását. Végül felsorolja a felülvizsgálat konfigurációs lehetőségeit.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>List logs for Azure Database for MariaDB server
Ha **log_output** "File" (Fájl) beállítás van konfigurálva, a naplók közvetlenül a kiszolgáló helyi tárolójában elérést használhatja. A kiszolgálóhoz elérhető lassú lekérdezési naplófájlok listához futtassa az [az mariadb server-logs list parancsot.](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_list)

A kiszolgálói erőforrások naplófájljainak **listáját mydemoserver.mariadb.database.azure.com** **myresourcegroup erőforráscsoportban.** Ezután irányja a naplófájlok listáját egy naplófájlok nevű szövegfájlba a **\_ \_list.txt.**
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Ha **log_output** "File" (Fájl) beállítás van konfigurálva, az [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_download) paranccsal egyes naplófájlokat tölthet le a kiszolgálóról.

Az alábbi példa segítségével töltse le a  **myresourcegroup** mydemoserver.mariadb.database.azure.com erőforráscsoportban található kiszolgálóspecifikus naplófájlt a helyi környezetbe.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Következő lépések
- Ismerje meg a [lassú lekérdezési naplókat a Azure Database for MariaDB.](concepts-server-logs.md)
