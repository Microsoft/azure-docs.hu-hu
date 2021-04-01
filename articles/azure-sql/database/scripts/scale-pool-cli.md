---
title: 'Az Azure CLI: rugalmas készlet méretezése'
description: Egy rugalmas készlet méretezése Azure SQL Databaseban egy Azure CLI-parancsfájl használatával.
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
ms.openlocfilehash: 30d462dec8c65252afbde0c4d2eee171891a64ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747310"
---
# <a name="use-the-azure-cli-to-scale-an-elastic-pool-in-azure-sql-database"></a>Rugalmas készlet méretezése az Azure CLI használatával Azure SQL Database

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez az Azure CLI-parancsfájl rugalmas készleteket hoz létre Azure SQL Database, áthelyezi a készletezett adatbázisokat, és módosítja a rugalmas készlet számítási méreteit.

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2,0-es vagy újabb verziójának kell futnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

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
| [az SQL db](/cli/azure/sql/db) | Adatbázis-parancsok. |
| [az SQL rugalmas-készletek](/cli/azure/sql/elastic-pool) | Rugalmas készlet parancsai. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../az-cli-script-samples-content-guide.md) találhat.
