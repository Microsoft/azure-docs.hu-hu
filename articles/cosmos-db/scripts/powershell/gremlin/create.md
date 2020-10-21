---
title: PowerShell-szkript Azure Cosmos DB Gremlin API-adatbázis és-gráf létrehozásához
description: Azure PowerShell script-Azure Cosmos DB Gremlin API-adatbázis és-gráf létrehozása
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: d326ebc3bede88fbe3e95485125ff2126d3a6f8f
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280666"
---
# <a name="create-a-database-and-graph-for-azure-cosmos-db---gremlin-api"></a>Adatbázis és gráf létrehozása a Azure Cosmos DB-Gremlin API-hoz

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-create.ps1 "Create a database and graph for Gremlin API")]

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
| [Új – AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Létrehoz egy Cosmos DB fiókot. |
| [Új – AzCosmosDBGremlinDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbgremlindatabase) | Létrehoz egy Gremlin API-adatbázist. |
| [Új – AzCosmosDBGremlinConflictResolutionPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbgremlinconflictresolutionpolicy) | Létrehoz egy Gremlin API írási ütközési feloldási szabályzatot. |
| [Új – AzCosmosDBGremlinGraph](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbgremlingraph) | Létrehoz egy Gremlin API-gráfot. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).
