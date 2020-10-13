---
title: Paraméterek konfigurálása – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan konfigurálható a postgres-paraméterek Azure Database for PostgreSQL-rugalmas kiszolgálón az Azure CLI használatával.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43b14858cc99cac41e277b03171fd4cac4d6eafa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940737"
---
# <a name="customize-server-parameters-for-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>Kiszolgáló paramétereinek testreszabása Azure Database for PostgreSQL-rugalmas kiszolgálóhoz az Azure CLI használatával

Az Azure PostgreSQL-kiszolgálóhoz tartozó konfigurációs paramétereket a parancssori felületen (Azure CLI) listázhatja, megjelenítheti és frissítheti. A motor paramétereinek egy részhalmaza a kiszolgáló szintjén van kitéve, és módosítható. 

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Hozzon létre egy Azure Database for PostgreSQL-kiszolgálót és-adatbázist egy [Azure Database for PostgreSQL létrehozása](quickstart-create-server-cli.md) után
- Telepítse az [Azure CLI](/cli/azure/install-azure-cli) parancssori felületet a gépére, vagy használja a Azure Portal [Azure Cloud Shell](../../cloud-shell/overview.md) a böngészőben.

## <a name="list-server-parameters-for-a-flexible-server"></a>Egy rugalmas kiszolgáló kiszolgálói paramétereinek listázása

A kiszolgálók és azok értékei összes módosítható paraméterének listázásához futtassa az az [postgres flexibilis-Server paraméter List](/cli/azure/postgres/flexible-server/parameter) parancsot.

A kiszolgáló **mydemoserver.postgres.database.Azure.com** a **myresourcegroup**erőforráscsoport alatt listázhatja.

```azurecli-interactive
az postgres flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="show-server-parameter-details"></a>Kiszolgáló paraméter részleteinek megjelenítése

A kiszolgálók egy adott paraméterének részleteinek megjelenítéséhez futtassa az az [postgres flexibilis-Server paraméter show](/cli/azure/postgres/flexible-server/parameter)  parancsot.

Ebben a példában a **log \_ min \_ messages** Server paraméter részleteit jeleníti meg a kiszolgáló **mydemoserver.postgres.database.Azure.com** az erőforráscsoport **myresourcegroup alatt.**

```azurecli-interactive
az postgres flexible-server parameter show --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="modify-server-parameter-value"></a>Kiszolgáló paraméter értékének módosítása

Egy bizonyos kiszolgálói paraméter értékét is módosíthatja, amely frissíti a PostgreSQL-kiszolgáló motorjának alapjául szolgáló konfigurációs értéket. A paraméter frissítéséhez használja az az [postgres flexibilis-Server paraméter set](/cli/azure/postgres/flexible-server/parameter) parancsot. 

A kiszolgáló **mydemoserver.postgres.database.Azure.com** tartozó ** \_ percek \_ naplózása** kiszolgáló paraméterének frissítése a myresourcegroup erőforráscsoport alatt **.**

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --value INFO --resource-group myresourcegroup --server-name mydemoserver
```

Ha egy paraméter értékét alaphelyzetbe szeretné állítani, egyszerűen válassza ki a választható `--value` paramétert, és a szolgáltatás alkalmazza az alapértelmezett értéket. A fenti példában a következőképpen fog kinézni:

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

Ez a parancs alaphelyzetbe állítja a percenkénti ** \_ \_ üzenetek naplózása** paramétert az alapértelmezett érték **figyelmeztetéssel**. A kiszolgálói paraméterekkel és a megengedett értékekkel kapcsolatos további információkért lásd: PostgreSQL-dokumentáció a [Paraméterek beállításához](https://www.postgresql.org/docs/12/config-setting.html).

## <a name="next-steps"></a>Következő lépések

- A kiszolgálók naplófájljainak konfigurálásához és eléréséhez lásd: [kiszolgálói naplók Azure Database for PostgreSQL](concepts-logging.md)
