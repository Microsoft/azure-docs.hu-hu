---
title: PowerShell-szkript egy Azure Cosmos DB fiókban kikapcsolt indexeléssel rendelkező tároló létrehozásához
description: Azure PowerShell szkript minta – egy Azure Cosmos DB-fiókban kikapcsolt indexeléssel rendelkező tároló létrehozása
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: b287bc851e560261d56ccda5d73d5319619146ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684039"
---
# <a name="create-a-container-with-indexing-turned-off-in-an-azure-cosmos-db-account-using-powershell"></a>Egy Azure Cosmos DB fiókban kikapcsolt indexeléssel rendelkező tároló létrehozása a PowerShell használatával
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Ehhez a mintához Azure PowerShell az 5.4.0 vagy újabb verzió szükséges. `Get-Module -ListAvailable Az`A Futtatás gombra kattintva megtekintheti, hogy mely verziók vannak telepítve.
Ha telepítenie kell a-t, olvassa el a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című témakört.

Futtassa a [AzAccount-](/powershell/module/az.accounts/connect-azaccount) t az Azure-ba való bejelentkezéshez.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-container-create-index-none.ps1 "Create a container with indexing turned off in an Azure Cosmos DB account")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
|**Azure Cosmos DB**| |
| [Új – AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Létrehoz egy Cosmos DB fiókot. |
| [Új – AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Létrehoz egy Cosmos DB SQL Database. |
| [Új – AzCosmosDBSqlIndexingPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Létrehoz egy, a New-AzCosmosDBSqlContainer paraméterként használt PSSqlIndexingPolicy objektumot. |
| [Új – AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Létrehoz egy Cosmos DB SQL-tárolót. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).