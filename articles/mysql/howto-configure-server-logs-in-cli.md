---
title: Lassú lekérdezési naplók elérése – Azure CLI – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan férhet hozzá a lassú lekérdezési naplókhoz Azure Database for MySQL az Azure CLI használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d5fc2b14a655251e59a9209e078b0534f08baf9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763234"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Lassú lekérdezési naplók konfigurálása és elérése az Azure CLI használatával
A lassú lekérdezési Azure Database for MySQL az Azure CLI, az Azure parancssori segédprogram használatával töltheti le.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató lépésenként a következőre lesz szüksége:
- [Azure Database for MySQL kiszolgáló](quickstart-create-mysql-server-database-using-azure-cli.md)
- Az [Azure CLI](/cli/azure/install-azure-cli) vagy Azure Cloud Shell böngészőben

## <a name="configure-logging"></a>Naplózás konfigurálása
A következő lépésekkel konfigurálhatja a kiszolgálót a MySQL lassú lekérdezési naplójának elérésére:
1. Kapcsolja be a lassú lekérdezésnaplózást úgy, hogy **a lassú \_ lekérdezésnapló \_** paraméterét BE-be van adva.
2. Válassza ki a naplók kimenetének helyét a **naplókimenet \_ használatával.** Ha a helyi tárolóba és a diagnosztikai naplókra is Azure Monitor, válassza a **Fájl lehetőséget.** Ha csak a naplóknak Azure Monitor, válassza a Nincs **lehetőséget.**
3. Állítson be más paramétereket, például **a \_ \_** hosszú lekérdezési időt és **a lassú \_ rendszergazdai \_ utasítások \_ naplózását.**

A paraméterek azure cli-alapú beállítását a kiszolgálóparaméterek konfigurálásában [olvashatja el.](howto-configure-server-parameters-using-cli.md)

A következő CLI-parancs például bekapcsolja a lassú lekérdezési naplót, 10 másodpercre állítja a hosszú lekérdezési időt, majd kikapcsolja a lassú rendszergazdai utasítás naplózását. Végül felsorolja a felülvizsgálat konfigurációs lehetőségeit.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>List logs for Azure Database for MySQL server
Ha **log_output** a "File" (Fájl) beállítás van konfigurálva, a naplók közvetlenül a kiszolgáló helyi tárolójában elérést használhatja. A kiszolgálóhoz elérhető lassú lekérdezési naplófájlok listához futtassa [az az mysql server-logs list parancsot.](/cli/azure/mysql/server-logs#az_mysql_server_logs_list)

A kiszolgálói erőforrások naplófájljainak **listáját mydemoserver.mysql.database.azure.com** **myresourcegroup erőforráscsoportban.** Ezután irányja a naplófájlok listáját egy naplófájlok nevű szövegfájlba a **\_ \_list.txt.**
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Ha **log_output** "File" (Fájl) van konfigurálva, az [az mysql server-logs download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) paranccsal egyes naplófájlokat tölthet le a kiszolgálóról.

Az alábbi példa segítségével töltse le a  **myresourcegroup** erőforráscsoportban mydemoserver.mysql.database.azure.com kiszolgálócsoport adott naplófájlját a helyi környezetbe.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Következő lépések
- Ismerje meg a [lassú lekérdezési naplókat a Azure Database for MySQL.](concepts-server-logs.md)
