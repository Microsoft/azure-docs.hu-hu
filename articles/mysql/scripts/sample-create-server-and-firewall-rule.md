---
title: CLI-szkript – Kiszolgáló létrehozása – Azure Database for MySQL
description: Ez a CLI-mintaszkript létrehoz egy Azure Database for MySQL-kiszolgálót, és konfigurál egy kiszolgálószintű tűzfalszabályt.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 12/02/2019
ms.openlocfilehash: 5859af08a8343c0640cdae293d001a1f143f4629
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763259"
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>MySQL-kiszolgáló létrehozása és tűzfalszabály konfigurálása az Azure CLI-vel
Ez a CLI-mintaszkript létrehoz egy Azure Database for MySQL-kiszolgálót, és konfigurál egy kiszolgálószintű tűzfalszabályt. A szkript sikeres futtatása után a MySQL-kiszolgáló elérhetővé válik a konfigurált IP-címről és az összes Azure-szolgáltatás számára.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van. 

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/create-mysql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for MySQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/delete-mysql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Létrehoz egy MySQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) | Létrehoz egy tűzfalszabályt, amely engedélyezi a kiszolgáló és az azon lévő adatbázisok elérését a megadott IP-címtartományból. |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure).
- További szkripteket az [Azure Database for MySQL-hez való Azure CLI-példák](../sample-scripts-azure-cli.md) között találhat.
