---
title: CLI-példa – feladatátvevő csoport – Azure SQL Database rugalmas készlet
description: Azure CLI-példa parancsfájl egy Azure SQL Database rugalmas készlet létrehozásához, egy feladatátvételi csoporthoz való hozzáadásához és a feladatátvételi teszthez.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 07/16/2019
ms.openlocfilehash: a5814bfe3bd6ec2d97a068ea8ce71fa7ffea8ec0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323583"
---
# <a name="use-cli-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Azure SQL Database rugalmas készlet hozzáadása feladatátvételi csoporthoz a CLI használatával

Ez az Azure CLI-parancsfájl egy önálló adatbázist hoz létre, amely egy rugalmas készletbe kerül, létrehoz egy feladatátvételi csoportot, és teszteli a feladatátvételi teszteket.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-cli.sh "Add elastic pool to a failover group")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Minta leírása

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Leírás |
|---|---|
| [az SQL rugalmas készlet](/cli/azure/sql/elastic-pool) | Rugalmas készlet parancsai. |
| [az SQL feladatátvétel-csoport ](/cli/azure/sql/failover-group) | Feladatátvételi csoport parancsai. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure/overview).

További SQL Database Azure CLI-szkriptek a [Azure SQL Database Azure CLI-szkriptekben](../../azure-sql/database/az-cli-script-samples-content-guide.md)találhatók.
