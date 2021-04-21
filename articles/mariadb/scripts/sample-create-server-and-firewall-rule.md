---
title: CLI-szkript – Kiszolgáló létrehozása – Azure Database for MariaDB
description: Ez a CLI-példaszk Azure Database for MariaDB egy kiszolgálót, és konfigurál egy kiszolgálószintű tűzfalszabályt.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 11/28/2018
ms.openlocfilehash: 1fc02555364beb2288772ae1fbfd0b66a7d96c50
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785684"
---
# <a name="create-a-mariadb-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>MariaDB-kiszolgáló létrehozása és tűzfalszabály konfigurálása az Azure CLI használatával
Ez a CLI-példaszk Azure Database for MariaDB egy kiszolgálót, és konfigurál egy kiszolgálószintű tűzfalszabályt. A szkript sikeres futtatása után a MariaDB-kiszolgáló elérhetővé lesz az összes Azure-szolgáltatás és a konfigurált IP-cím számára.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/create-mariadb-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for mariadb, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/delete-mariadb.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) | Létrehoz egy MariaDB-kiszolgálót, amely az adatbázisokat tárolja. |
| [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_create) | Létrehoz egy tűzfalszabályt, amely engedélyezi a kiszolgáló és az azon lévő adatbázisok elérését a megadott IP-címtartományból. |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure).
- További szkriptek: [Azure CLI-minták Azure Database for MariaDB](../sample-scripts-azure-cli.md)
