---
title: Azure Blob Storage-trigger és-kötések Azure Functionshoz
description: Ismerje meg, hogyan használhatja az Azure Blob Storage-triggert és-kötéseket Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: e56d1add36d4296526348d12d7c0b6eb03108f27
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104359"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Blob Storage-kötések Azure Functions – áttekintés

A Azure Functions [triggerekkel és kötésekkel](./functions-triggers-bindings.md)integrálható az [Azure Storage](../storage/index.yml) -ba. A blob Storage-val való integráció lehetővé teszi olyan függvények összeállítását, amelyek reagálnak a blob-adatok változásaira, valamint az olvasási és írási értékekre.

| Művelet | Típus |
|---------|---------|
| Függvény futtatása blob Storage-beli adatváltozásként | [Eseményindító](./functions-bindings-storage-blob-trigger.md) |
| BLOB Storage-beli adat olvasása függvényben | [Bemeneti kötés](./functions-bindings-storage-blob-input.md) |
| BLOB Storage-adat írására szolgáló függvény engedélyezése |[Kimeneti kötés](./functions-bindings-storage-blob-output.md) |

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
[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Bővítmények frissítése]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A functions 1. x alkalmazások automatikusan hivatkoznak a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomagra, 2. x verzióra.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>További lépések

- [Függvény futtatása a blob Storage-beli adatváltozások esetén](./functions-bindings-storage-blob-trigger.md)
- [BLOB Storage-adat olvasása függvény futtatásakor](./functions-bindings-storage-blob-input.md)
- [BLOB Storage-adatok írása függvényből](./functions-bindings-storage-blob-output.md)