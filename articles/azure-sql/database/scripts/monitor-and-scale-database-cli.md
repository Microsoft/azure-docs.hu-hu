---
title: 'Azure CLI: egyetlen adatbázis monitorozása és méretezése Azure SQL Database'
description: Egy Azure CLI-parancsfájl használatával figyelheti és méretezheti a Azure SQL Database egyetlen adatbázisát.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: c4df9ecc025bbffb63730273be06f54cf46f613c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319396"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Egyetlen adatbázis figyelése és méretezése az Azure CLI használatával Azure SQL Database

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez az Azure CLI-parancsfájl például az adatbázis méretére vonatkozó információk lekérése után egy különálló adatbázist Azure SQL Database egy másik számítási méretre.

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> Az az [SQL db op List](/cli/azure/sql/db/op?#az-sql-db-op-list) paranccsal kérheti le az adatbázison végrehajtott műveletek listáját, és az [az SQL db op Cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) paranccsal szakíthat meg egy frissítési műveletet az adatbázison.

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Minta leírása

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Script | Leírás |
|---|---|
| [az sql server](/cli/azure/sql/server) | Kiszolgálói parancsok. |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | Megjeleníti egy adatbázis méretkihasználtsági adatait. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További CLI-parancsfájlok is megtalálhatók az [Azure CLI-minták parancsfájljaiban](../az-cli-script-samples-content-guide.md).
