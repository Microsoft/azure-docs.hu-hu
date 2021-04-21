---
title: 'Az Azure CLI: Egyetlen adatbázis létrehozása'
description: Használja ezt az Azure CLI-példaszk szkriptet egyetlen adatbázis létrehozásához.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 00346accbccd67f542a8496ac097b0236c56a08c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773818"
---
# <a name="use-the-azure-cli-to-create-a-single-database-and-configure-a-firewall-rule"></a>Egyetlen adatbázis létrehozása és tűzfalszabály konfigurálása az Azure CLI használatával

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez az Azure CLI-példaszkret egyetlen adatbázist hoz létre a Azure SQL Database, és konfigurál egy kiszolgálószintű tűzfalszabályt. A szkript sikeres futtatása után az adatbázis az összes Azure-szolgáltatásból és a konfigurált IP-címről elérhető.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Mintahivatkozás

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Leírás |
|---|---|
| [az sql server](/cli/azure/sql/server#az_sql_server_create) | Kiszolgálói parancsok |
| [az sql server firewall](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) | Kiszolgálói tűzfalparancsok. |
| [az sql db](/cli/azure/sql/db#az_sql_db_create) | Adatbázisparancsok. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../az-cli-script-samples-content-guide.md) találhat.
