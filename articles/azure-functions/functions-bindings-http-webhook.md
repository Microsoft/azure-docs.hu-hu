---
title: Az Azure Functions HTTP-triggerei és kötései
description: Megtudhatja, hogyan használhatja a HTTP-eseményindítókat és-kötéseket Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 5236a3be9f8908d886274764dfc0c0da5b3565bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92104478"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Azure Functions HTTP-eseményindítók és-kötések áttekintése

A Azure Functions HTTP-kérések használatával hívhatók meg kiszolgáló nélküli API-k létrehozásához és a [webhookok](https://en.wikipedia.org/wiki/Webhook)megválaszolásához.

| Művelet | Típus |
|---------|---------|
| Függvény futtatása HTTP-kérelemből | [Eseményindító](./functions-bindings-http-webhook-trigger.md) |
| HTTP-válasz visszaadása függvényből |[Kimeneti kötés](./functions-bindings-http-webhook-output.md) |

A cikkben szereplő kód a .NET Core szintaxisra vonatkozik, amelyet a functions 2. x vagy újabb verziójában használ. Az 1. x szintaxissal kapcsolatos további információkért tekintse meg az [1. x függvények sablonjait](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Hozzáadás a functions-alkalmazáshoz

### <a name="functions-2x-and-higher"></a>Functions 2.x és újabb

Az trigger és a kötések használata megköveteli, hogy a megfelelő csomagra hivatkozzon. A NuGet csomag a .NET-osztály könyvtáraihoz használatos, míg a kiterjesztési köteg minden más alkalmazás típusához használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A [NuGet-csomag], 3. x verziójának telepítése | |
| C# parancsfájl, Java, JavaScript, Python, PowerShell | A [kiterjesztési csomag] regisztrálása          | Az [Azure Tools bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) használata ajánlott a Visual Studio Code használatával. |
| C#-szkript (csak online – Azure Portal)         | Kötés hozzáadása                            | Ha frissíteni szeretné a meglévő kötési bővítményeket anélkül, hogy újra közzé kellene tennie a Function alkalmazást, tekintse [meg a bővítmények frissítése]című témakört. |

[core tools]: ./functions-run-local.md
[kiterjesztési csomag]: ./functions-bindings-register.md#extension-bundles
[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Bővítmények frissítése]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A functions 1. x alkalmazások automatikusan hivatkoznak a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomagra, 2. x verzióra.

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása HTTP-kérelemből](./functions-bindings-http-webhook-trigger.md)
- [HTTP-válasz visszaadása függvényből](./functions-bindings-http-webhook-output.md)