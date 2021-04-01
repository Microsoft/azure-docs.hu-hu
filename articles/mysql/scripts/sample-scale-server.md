---
title: CLI-parancsfájl – méretezési kiszolgáló – Azure Database for MySQL
description: Ez a CLI-példaszkript egy Azure Database for MySQL-kiszolgálót skáláz más teljesítményszintre a metrikák lekérdezése után.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 31cf6d1f3bfcc44887ec939cb2f27df77f12cea3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94539260"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure Database for MySQL-kiszolgáló monitorozása és skálázása az Azure CLI használatával
Ez a CLI-parancsfájl a metrikák lekérdezése után egy Azure Database for MySQL kiszolgáló számítási és tárolási kapacitását méretezi. A számítás vertikális fel-vagy leskálázást végez. A tárterület csak vertikális felskálázásra használható.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 

## <a name="sample-script"></a>Példaszkript
Frissítse a parancsfájlt az előfizetés-azonosítójával.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Létrehoz egy MySQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az MySQL Server Update](/cli/azure/mysql/server#az-mysql-server-update) | Frissíti a MySQL-kiszolgáló tulajdonságait. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Listázza az erőforrások metrikaértékét. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure Database for MySQL számítási és tárolási](../concepts-pricing-tiers.md) feladatokról
- További szkripteket az [Azure Database for MySQL-hez való Azure CLI-példák](../sample-scripts-azure-cli.md) között találhat.
- További információ az [Azure CLI](/cli/azure) -ről
