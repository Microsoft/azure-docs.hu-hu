---
title: Naplók kezelése – Azure CLI-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni és elérni a kiszolgálói naplókat (. naplófájlokat) Azure Database for PostgreSQL egyetlen kiszolgálón az Azure CLI használatával.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 71dac0b6ea2202e712280607a73e860ae68bdb73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005588"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>A kiszolgálói naplók konfigurálása és elérése az Azure CLI használatával
A PostgreSQL-kiszolgáló naplófájljait a parancssori felület (Azure CLI) használatával töltheti le. A tranzakciós naplókhoz való hozzáférés azonban nem támogatott. 

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for PostgreSQL kiszolgáló](quickstart-create-server-database-azure-cli.md)
- Az [Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogram vagy Azure Cloud Shell a böngészőben

## <a name="configure-logging"></a>Naplózás konfigurálása
Beállíthatja, hogy a kiszolgáló hozzáférjen a lekérdezési naplókhoz és a hibák naplóihoz. A naplókhoz automatikus vákuum, kapcsolatok és ellenőrzőpont-információk is tartozhatnak.
1. A naplózás bekapcsolása.
2. A lekérdezések naplózásának engedélyezéséhez frissítse a **log \_ utasítás** és a **napló \_ minimális \_ időtartama \_ utasítást**.
3. A megőrzési időtartam frissítése.

További információ: a [kiszolgáló konfigurációs paramétereinek testreszabása](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Naplók listázása
A kiszolgálóhoz elérhető naplófájlok listázásához futtassa az az [postgres Server-logs List](/cli/azure/postgres/server-logs) parancsot.

A kiszolgálói **mydemoserver.postgres.database.Azure.com** tartozó naplófájlokat az erőforráscsoport **myresourcegroup** lehet kilistázni. Ezután irányítsa a naplófájlok listáját a naplófájlok **\_ \_list.txt** nevű szövegfájlba.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Naplók helyi letöltése a kiszolgálóról
Az az [postgres Server-logs Download](/cli/azure/postgres/server-logs) paranccsal letöltheti a kiszolgáló egyes naplófájljait. 

A következő példa használatával letöltheti a kiszolgáló **mydemoserver.postgres.database.Azure.com** tartozó naplófájlt a helyi környezet **myresourcegroup** .
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Következő lépések
- A kiszolgálók naplóival kapcsolatos további tudnivalókért tekintse meg [a Azure Database for PostgreSQL kiszolgáló naplófájljait](concepts-server-logs.md).
- A kiszolgálói paraméterekkel kapcsolatos további információkért lásd: [kiszolgáló konfigurációs paramétereinek testreszabása az Azure CLI használatával](howto-configure-server-parameters-using-cli.md).
