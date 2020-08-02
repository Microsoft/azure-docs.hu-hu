---
title: Azure CLI-szkript – Kiszolgálói naplók letöltése az Azure Database for PostgreSQL-ben
description: Ez az Azure CLI-példaszkript bemutatja, hogyan engedélyezheti és töltheti le egy Azure Database for PostgreSQL-kiszolgáló naplóit.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 11940cce5ea4bf6265c451ee765c385a18b956b9
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496475"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Azure Database for PostgreSQL-kiszolgáló lassú lekérdezési naplóinak engedélyezése és letöltése az Azure CLI használatával
Ez az Azure CLI-példaszkript engedélyezi és letölti egy Azure Database for PostgreSQL-kiszolgáló lassú lekérdezési naplóit.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát. Cserélje le &lt; a &gt; parancsok log_file_name a `az monitor` saját kiszolgálói naplófájljának nevére.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az postgres server create](/cli/azure/postgres/server) | Létrehoz egy PostgreSQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Felsorolja egy kiszolgáló konfigurációs értékeit. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Frissíti egy kiszolgáló konfigurációját. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs) | Felsorolja egy kiszolgáló naplófájljait. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs) | Letölti a naplófájlokat. |
| [az group delete](/cli/azure/group) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure).
- További szkripteket [az Azure Database for PostgreSQL-hez készült Azure CLI-példák](../sample-scripts-azure-cli.md) között találhat.
- [Kiszolgálónaplók konfigurálása és elérése az Azure Portalon](../howto-configure-server-logs-in-portal.md)
