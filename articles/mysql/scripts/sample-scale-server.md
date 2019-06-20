---
title: Azure CLI-szkript – Azure Database for MySQL-kiszolgáló skálázása
description: Ez a CLI-példaszkript egy Azure Database for MySQL-kiszolgálót skáláz más teljesítményszintre a metrikák lekérdezése után.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 04/05/2018
ms.openlocfilehash: 63bd92aeea0ad85872c1165eab374e90de078d44
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275126"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure Database for MySQL-kiszolgáló monitorozása és skálázása az Azure CLI használatával
Ez a CLI-példaszkript egyetlen Azure Database for MySQL-kiszolgálót skáláz más teljesítményszintre a metrikák lekérdezése után.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát. A használt előfizetés-Azonosítót cserélje le a `az monitor` parancsok a saját előfizetés-azonosítójára.  
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh?highlight=15-16 "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Létrehoz egy MySQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Listázza az erőforrások metrikaértékét. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure CLI-vel kapcsolatos további információkért tekintse meg: [Az Azure CLI dokumentációját](/cli/azure).
- További szkripteket: [Az Azure CLI-minták az Azure Database for MySQL-hez](../sample-scripts-azure-cli.md)
- További információkat a skálázásról a [szolgáltatásszintekkel](../concepts-service-tiers.md) és a [számítási és tárolási egységekkel](../concepts-compute-unit-and-storage.md) foglalkozó témakörben talál.
