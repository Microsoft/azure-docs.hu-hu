---
title: Tárterület automatikus nőjön – Azure CLI – Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan engedélyezheti a tárterület automatikus növekedését az Azure CLI használatával a Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f2ae7a890fc1dab29b6cc99e4cc88087dbc6e052
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366181"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Tárterület automatikus Azure Database for MariaDB az Azure CLI használatával
Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MariaDB-kiszolgáló tárolóját úgy, hogy az ne befolyásolja a számítási feladatokat.

A [tárhelykorlátot](concepts-pricing-tiers.md#reaching-the-storage-limit)el érő kiszolgáló csak olvashatóra van állítva. Ha a tárterület automatikus mérete engedélyezve van, akkor a 100 GB-nál kisebb kiépítésű tárterülettel rendelkező kiszolgálók esetén a kiépített tárterület mérete 5 GB-kal nő, amint az ingyenes tárterület a kiépített tárterület 1 GB-nál nagyobb vagy 10%-a alá csökken. A 100 GB-nál nagyobb kiépített tárterülettel rendelkező kiszolgálók esetén a kiépített tárterület 5%-kal nő, ha a szabad tárterület a kiépített tárterület 10 GB-nál kisebb. Az itt megadott maximális tárolási [korlátok érvényesek.](concepts-pricing-tiers.md#storage)

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez:

- Szüksége lesz egy [Azure Database for MariaDB kiszolgálóra.](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="enable-mariadb-server-storage-auto-grow"></a>MariaDB-kiszolgáló tárterületének automatikus növekedésének engedélyezése

Engedélyezze a kiszolgáló automatikus tárolókapacitását egy meglévő kiszolgálón a következő paranccsal:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Engedélyezze a kiszolgáló automatikus tárolókapacitását új kiszolgáló létrehozásakor a következő paranccsal:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Következő lépések

Ismerje [meg, hogyan hozhat létre metrikákra vonatkozó riasztásokat.](howto-alert-metric.md)
