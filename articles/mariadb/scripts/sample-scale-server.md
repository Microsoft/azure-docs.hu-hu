---
title: CLI-szkript – Kiszolgáló méretezése – Azure Database for MariaDB
description: Ez a CLI-példaszk Azure Database for MariaDB kiszolgálót más teljesítményszintre skáláz a metrikák lekérdezése után.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 3a099f1a3ab16c33baa8037e727d26f107a593a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785540"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Virtuáliskiszolgálók monitorba Azure Database for MariaDB és méretezése az Azure CLI használatával
Ez a CLI-példaszk szkript egyetlen kiszolgáló számítási és tárolási Azure Database for MariaDB a metrikák lekérdezése után. A számítás felfelé vagy lefelé is skálázható. A tárterület csak felskálásos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van. 

## <a name="sample-script"></a>Példaszkript
Frissítse a szkriptet az előfizetés azonosítójával.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) | Létrehoz egy MariaDB-kiszolgálót, amely az adatbázisokat tárolja. |
| [az mariadb server update](/cli/azure/mariadb/server#az_mariadb_server_update) | Frissíti a MariaDB-kiszolgáló tulajdonságait. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Listázza az erőforrások metrikaértékét. |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure Database for MariaDB és tárolásról](../concepts-pricing-tiers.md)
- További szkriptek: [Azure CLI-minták Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- További információ az [Azure CLI-ről](/cli/azure)
