---
title: 'Az Azure CLI: hozzon létre egy önálló adatbázist'
description: Az Azure CLI példa szkriptjét egyetlen adatbázis létrehozására használhatja.
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
ms.openlocfilehash: dc17ab55cb473036d8c40bd1e533bcc20c4c6259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92742264"
---
# <a name="use-the-azure-cli-to-create-a-single-database-and-configure-a-firewall-rule"></a>Egyetlen adatbázis létrehozása és tűzfalszabály konfigurálása az Azure CLI használatával

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez az Azure CLI-parancsfájl egyetlen adatbázist hoz létre a Azure SQL Databaseban, és egy kiszolgálói szintű tűzfalszabály konfigurálását végzi. A szkript sikeres futtatása után az adatbázis az összes Azure-szolgáltatásból és a konfigurált IP-címről is elérhető.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Minta leírása

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Leírás |
|---|---|
| [az sql server](/cli/azure/sql/server#az-sql-server-create) | Kiszolgálói parancsok |
| [az SQL Server Firewall](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Kiszolgálói tűzfal parancsai. |
| [az SQL db](/cli/azure/sql/db#az-sql-db-create) | Adatbázis-parancsok. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../az-cli-script-samples-content-guide.md) találhat.
