---
title: CLI-szkript – kiszolgáló létrehozása – Azure Database for MySQL
description: Ez a CLI-mintaszkript létrehoz egy Azure Database for MySQL-kiszolgálót, és konfigurál egy kiszolgálószintű tűzfalszabályt.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 12/02/2019
ms.openlocfilehash: a210d2d11c9d9fccd000a6dffbf1f8730bda0c8b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74765794"
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>MySQL-kiszolgáló létrehozása és tűzfalszabály konfigurálása az Azure CLI-vel
Ez a CLI-mintaszkript létrehoz egy Azure Database for MySQL-kiszolgálót, és konfigurál egy kiszolgálószintű tűzfalszabályt. A szkript sikeres futtatása után a MySQL-kiszolgáló elérhetővé válik a konfigurált IP-címről és az összes Azure-szolgáltatás számára.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

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
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Létrehoz egy MySQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) | Létrehoz egy tűzfalszabályt, amely engedélyezi a kiszolgáló és az azon lévő adatbázisok elérését a megadott IP-címtartományból. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure).
- További szkripteket az [Azure Database for MySQL-hez való Azure CLI-példák](../sample-scripts-azure-cli.md) között találhat.
