---
title: Azure Cosmos DB a függvények kötéseit 2. xD és újabb
description: Megtudhatja, hogyan használhatók Azure Cosmos DB eseményindítók és kötések a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: dde3b95c7997a7f742a9d48a964f4275169f6e9c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92106926"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Azure Cosmos DB trigger és kötések Azure Functions 2. x és újabb verziókhoz – áttekintés

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Azure Functions futtatókörnyezet verzióját: "]
> * [1-es verzió](functions-bindings-cosmosdb.md)
> * [2. és újabb verzió](functions-bindings-cosmosdb-v2.md)

Ez a cikk azt ismerteti, hogyan használhatók [Azure Cosmos db](../cosmos-db/serverless-computing-database.md) kötések Azure functions 2. x vagy újabb verziókban. Azure Functions támogatja a Azure Cosmos DB trigger-, bemeneti és kimeneti kötéseit.

| Művelet | Típus |
|---------|---------|
| Függvény futtatása Azure Cosmos DB dokumentum létrehozásakor vagy módosításakor | [Eseményindító](./functions-bindings-cosmosdb-v2-trigger.md) |
| Azure Cosmos DB dokumentum beolvasása | [Bemeneti kötés](./functions-bindings-cosmosdb-v2-input.md) |
| Azure Cosmos DB dokumentum módosításainak mentése  |[Kimeneti kötés](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Ez a hivatkozás [Azure functions 2. x vagy újabb verzióra](functions-versions.md)mutat.  További információ a kötések használatáról az 1. x függvényeknél: [Azure Cosmos db kötések Azure functions 1. x verzióhoz](functions-bindings-cosmosdb.md).
>
> Ez a kötés eredetileg a DocumentDB nevet kapta. A functions 2. x vagy újabb verziójában a trigger, a kötések és a csomag neve Cosmos DB.

## <a name="supported-apis"></a>Támogatott API-k

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Hozzáadás a functions-alkalmazáshoz

### <a name="functions-2x-and-higher"></a>Functions 2.x és újabb

Az trigger és a kötések használata megköveteli, hogy a megfelelő csomagra hivatkozzon. A NuGet csomag a .NET-osztály könyvtáraihoz használatos, míg a kiterjesztési köteg minden más alkalmazás típusához használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A [NuGet-csomag], 3. x verziójának telepítése | |
| C# parancsfájl, Java, JavaScript, Python, PowerShell | A [kiterjesztési csomag] regisztrálása          | Az [Azure Tools bővítmény] használata ajánlott a Visual Studio Code használatával. |
| C#-szkript (csak online – Azure Portal)         | Kötés hozzáadása                            | Ha frissíteni szeretné a meglévő kötési bővítményeket anélkül, hogy újra közzé kellene tennie a Function alkalmazást, tekintse [meg a bővítmények frissítése]című témakört. |

[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[kiterjesztési csomag]: ./functions-bindings-register.md#extension-bundles
[Bővítmények frissítése]: ./functions-bindings-register.md
[Azure-eszközök bővítmény]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A functions 1. x alkalmazások automatikusan hivatkoznak a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomagra, 2. x verzióra.

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása Azure Cosmos DB dokumentum létrehozásakor vagy módosításakor (trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB dokumentum olvasása (bemeneti kötés)](./functions-bindings-cosmosdb-v2-input.md)
- [Azure Cosmos DB dokumentum módosításainak mentése (kimeneti kötés)](./functions-bindings-cosmosdb-v2-output.md)