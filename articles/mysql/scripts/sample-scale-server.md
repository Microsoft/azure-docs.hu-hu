---
title: CLI-szkript – Kiszolgáló méretezése – Azure Database for MySQL
description: Ez a CLI-példaszkript egy Azure Database for MySQL-kiszolgálót skáláz más teljesítményszintre a metrikák lekérdezése után.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: e7dea1cb99ea8ce941b16739bdbd258843877e89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791642"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure Database for MySQL-kiszolgáló monitorozása és skálázása az Azure CLI használatával
Ez a CLI-példaszkprogram egyetlen kiszolgáló számítási és tárolási Azure Database for MySQL skáláz a metrikák lekérdezése után. A számítás felfelé vagy lefelé is skálázható. A tárterület csak felskálzhat.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van. 

## <a name="sample-script"></a>Példaszkript
Frissítse a szkriptet az előfizetés azonosítójával.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Létrehoz egy MySQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az mysql server update](/cli/azure/mysql/server#az_mysql_server_update) | Frissíti a MySQL-kiszolgáló tulajdonságait. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Listázza az erőforrások metrikaértékét. |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések
- További információ a [számítási Azure Database for MySQL tárolásról](../concepts-pricing-tiers.md)
- További szkripteket az [Azure Database for MySQL-hez való Azure CLI-példák](../sample-scripts-azure-cli.md) között találhat.
- További információ az [Azure CLI-ről](/cli/azure)
