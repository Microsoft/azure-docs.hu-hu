---
title: 'Az Azure CLI: adatbázis áthelyezése rugalmas készletek között'
description: Egy Azure CLI-parancsfájl használatával két rugalmas készletet hozhat létre, és SQL Database egy-egy rugalmas készletből egy másikba helyezheti át az adatbázist.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 9fcb23750137c8286efbc0e1fb7446e324c572dd
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747335"
---
# <a name="use-the-azure-cli-to-move-a-database-in-sql-database-in-a-sql-elastic-pool"></a>Az adatbázis áthelyezése az Azure CLI használatával SQL Database egy rugalmas SQL-készletben

Ez az Azure CLI-parancsfájl két rugalmas készletet hoz létre, és egy készletezett adatbázist helyez át SQL Database egy rugalmas SQL-készletből egy másik SQL rugalmas készletbe, majd áthelyezi a készletezett adatbázist a rugalmas SQL-készletből, hogy egyetlen adatbázis legyen a SQL Database.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Minta leírása

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Leírás |
|---|---|
| [az sql server](/cli/azure/sql/server) | Kiszolgálói parancsok. |
| [az SQL rugalmas-készletek](/cli/azure/sql/elastic-pool) | Rugalmas készlet parancsai. |
| [az SQL db](/cli/azure/sql/db) | Adatbázis-parancsok. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../az-cli-script-samples-content-guide.md) találhat.
