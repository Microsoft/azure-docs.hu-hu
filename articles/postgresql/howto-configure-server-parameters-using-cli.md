---
title: Paraméterek konfigurálása – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja az postgres-paramétereket Azure Database for PostgreSQL – egyetlen kiszolgálón az Azure CLI használatával.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 06/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: cadf21423ac7eb997db5bb42005ee307d9813331
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604192"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Kiszolgáló konfigurációs paramétereinek testreszabása Azure Database for PostgreSQL – egyetlen kiszolgáló az Azure CLI-vel
Az Azure PostgreSQL-kiszolgálóhoz tartozó konfigurációs paramétereket a parancssori felületen (Azure CLI) listázhatja, megjelenítheti és frissítheti. A motor-konfigurációk egy részhalmaza a kiszolgáló szintjén érhető el, és módosítható. 

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Hozzon létre egy Azure Database for PostgreSQL-kiszolgálót és-adatbázist egy [Azure Database for PostgreSQL létrehozása](quickstart-create-server-database-azure-cli.md) után
- Telepítse az [Azure CLI](/cli/azure/install-azure-cli) parancssori felületet a gépére, vagy használja a Azure Portal [Azure Cloud Shell](../cloud-shell/overview.md) a böngészőben.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL kiszolgáló kiszolgáló-konfigurációs paramétereinek listázása
A kiszolgálók és azok értékei összes módosítható paraméterének listázásához futtassa az az [postgres Server Configuration List](/cli/azure/postgres/server/configuration) parancsot.

A kiszolgálói **mydemoserver.postgres.database.Azure.com** tartozó kiszolgálói konfigurációs paramétereket a **myresourcegroup** erőforráscsoport alatt listázhatja.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Kiszolgáló konfigurációs paramétereinek megjelenítése – részletek
A kiszolgálók egy adott konfigurációs paraméterének részleteinek megjelenítéséhez futtassa az az [postgres Server Configuration show](/cli/azure/postgres/server/configuration)  parancsot.

Ez a példa a **log \_ min \_ messages** kiszolgáló konfigurációs paraméterének részleteit jeleníti meg a kiszolgáló **mydemoserver.postgres.database.Azure.com** az erőforráscsoport **myresourcegroup alatt.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Kiszolgáló konfigurációs paramétere értékének módosítása
Egy bizonyos kiszolgáló-konfigurációs paraméter értékét is módosíthatja, amely frissíti a PostgreSQL-kiszolgáló motorjának alapjául szolgáló konfigurációs értéket. A konfiguráció frissítéséhez használja az az [postgres Server Configuration set](/cli/azure/postgres/server/configuration) parancsot. 

A kiszolgáló **mydemoserver.postgres.database.Azure.com** **naplózási \_ percek \_** kiszolgáló-konfigurációs paraméterének frissítése a myresourcegroup erőforráscsoport alatt **.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Ha alaphelyzetbe kívánja állítani egy konfigurációs paraméter értékét, egyszerűen kihagyhatja a választható `--value` paramétert, és a szolgáltatás alkalmazza az alapértelmezett értéket. A fenti példában a következőképpen fog kinézni:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Ez a parancs alaphelyzetbe állítja a **\_ min. percben megadott \_ üzenetek** konfigurációját az alapértelmezett érték **figyelmeztetéssel**. A kiszolgáló-konfigurációval és a megengedett értékekkel kapcsolatos további információkért lásd a PostgreSQL-dokumentáció a [kiszolgálók konfigurációjában](https://www.postgresql.org/docs/9.6/static/runtime-config.html)című témakört.

## <a name="next-steps"></a>Következő lépések
- [További információ a kiszolgálók újraindításáról](howto-restart-server-cli.md)
- A kiszolgálók naplófájljainak konfigurálásához és eléréséhez lásd: [kiszolgálói naplók Azure Database for PostgreSQL](concepts-server-logs.md)
