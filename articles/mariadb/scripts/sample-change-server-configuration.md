---
title: CLI-szkript – Kiszolgálóparaméterek módosítása – Azure Database for MariaDB
description: Ez a CLI-példaszkprogram felsorolja az összes elérhető kiszolgálókonfigurációt és -frissítést a Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 3504f1221c501b997b04d9c81c721aba2903fba6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777058"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Kiszolgáló konfigurációjának felsorolása és frissítése Azure Database for MariaDB Azure CLI használatával
Ez a CLI-példaszklet felsorolja az összes elérhető konfigurációs paramétert,  valamint Azure Database for MariaDB-kiszolgáló engedélyezett értékeit, és a innodb_lock_wait_timeout értékét az alapértelmezetttől különböző értékre állítja.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) | Létrehoz egy MariaDB-kiszolgálót, amely az adatbázisokat tárolja. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_list) | List the configurations of an Azure Database for MariaDB server.list the configurations of an Azure Database for MariaDB server. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_set) | Frissítse egy új Azure Database for MariaDB konfigurációját. |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_show) | Egy új kiszolgáló konfigurációjának Azure Database for MariaDB megjelenítése. |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure).
- További szkriptek: [Azure CLI-minták Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- A kiszolgálóparaméterekkel kapcsolatos további információkért lásd: [Kiszolgálóparaméterek konfigurálása a Azure Database for MariaDB.](../howto-server-parameters.md)
