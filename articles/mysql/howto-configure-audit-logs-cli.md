---
title: Hozzáférés a naplókhoz – Azure CLI – Azure Database for MySQL
description: Ez a cikk bemutatja, hogyan konfigurálhatja és érheti el a Azure Database for MySQL naplóit az Azure CLI-vel.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: db7ffabae785a589bacf349356079f6046039f9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541997"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Eseménynaplók konfigurálása és elérése az Azure CLI-ben

Az [Azure Database for MySQL naplókat](concepts-audit-logs.md) az Azure CLI-ből is konfigurálhatja.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépései:

- Szüksége van egy [Azure Database for MySQL-kiszolgálóra](quickstart-create-mysql-server-database-using-azure-portal.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

>[!IMPORTANT]
> Azt javasoljuk, hogy csak azokat az eseményeket és felhasználókat naplózza, amelyek szükségesek a naplózáshoz, hogy a kiszolgáló teljesítménye ne legyen nagy hatással.

A naplózást a következő lépésekkel engedélyezheti és konfigurálhatja:

1. A naplók bekapcsolásához állítsa a **audit_logs_enabled** paramétert "on" értékre. 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Válassza ki a naplózni kívánt [események típusát](concepts-audit-logs.md#configure-audit-logging) a **audit_log_events** paraméter frissítésével.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. A **audit_log_exclude_users** paraméter frissítésével adja hozzá a kizárni kívánt MySQL-felhasználókat a naplózásból. Adja meg a felhasználókat a MySQL-Felhasználónév megadásával.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Adja hozzá a naplózáshoz hozzáadni kívánt MySQL-felhasználókat a **audit_log_include_users** paraméter frissítésével. Adja meg a felhasználókat a MySQL-Felhasználónév megadásával.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Következő lépések
- További információ a Azure Database for MySQL [naplózási naplóiról](concepts-audit-logs.md)
- Ismerje meg, hogyan konfigurálhatja a naplókat a [Azure Portal](howto-configure-audit-logs-portal.md)
