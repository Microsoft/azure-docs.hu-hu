---
title: Tárterület automatikus növekedése – Azure CLI – Azure Database for MySQL
description: Ez a cikk bemutatja, hogyan engedélyezheti az automatikus növekedés tárolását a Azure Database for MySQL Azure CLI használatával.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2e2a2dadfb8bcaedb9a3210ebcab7cce6c30a8a0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546925"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Azure Database for MySQL tárterület automatikus növelése az Azure CLI használatával
Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MySQL-kiszolgáló tárterületét úgy, hogy az a munkaterhelés befolyásolása nélkül is növekszik.

A [tárolási korlátot elérő](./concepts-pricing-tiers.md#reaching-the-storage-limit)kiszolgáló csak olvasható értékre van állítva. Ha a Storage automatikus növekedése engedélyezve van, akkor a 100 GB-nál kevesebb kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5 GB-kal nő, amint az ingyenes tárterület a kiépített tárterület nagyobb 1 GB-os vagy 10%-ában kisebb. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5%-kal nő, ha a szabad tárterület mérete a kiosztott tárterület méretének 5%-a alá esik. Az [itt](./concepts-pricing-tiers.md#storage) megadott maximális tárolási korlátozások érvényesek.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ehhez az útmutatóhoz az Azure CLI 2,0-es vagy újabb verzióját kell használnia. A verzió megerősítéséhez az Azure CLI parancssorában adja meg a következőt: `az --version` . Telepítéshez vagy frissítéshez: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="enable-mysql-server-storage-auto-grow"></a>A MySQL-kiszolgáló tárterületének automatikus növekedésének engedélyezése

A kiszolgáló automatikus növekedésének engedélyezése egy meglévő kiszolgálón a következő paranccsal:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

A kiszolgáló automatikus növekedésének engedélyezése a tárolóban új kiszolgáló létrehozásakor a következő paranccsal:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Következő lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-on-metric.md).