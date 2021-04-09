---
title: Azure Blob Storage-trigger és-kötések Azure Functionshoz
description: Ismerje meg, hogyan használhatja az Azure Blob Storage-triggert és-kötéseket Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 4ec21086ee94610be1d9cf5da7b64c837b5311a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381528"
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

#### <a name="storage-extension-5x-and-higher"></a>5. x és újabb Storage-bővítmény

A Storage-kötések bővítmény egy új verziója érhető el előzetes verziójú [NuGet-csomagként](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). Ez az előzetes verzió azt mutatja be, hogy [titkos kód helyett identitás használatával tud csatlakozni](./functions-reference.md#configure-an-identity-based-connection). A .NET-alkalmazások esetében az is megváltoztathatja a kötéshez köthető típusokat, és lecserélheti az `WindowsAzure.Storage` `Microsoft.Azure.Storage` [Azure. Storage. Blobok](/dotnet/api/azure.storage.blobs)és az újabb típusok típusait.

> [!NOTE]
> Az előnézeti csomag nem szerepel a kiterjesztési csomagban, és manuálisan kell telepíteni. .NET-alkalmazások esetén adjon hozzá egy hivatkozást a csomaghoz. Az összes többi alkalmazás típusával kapcsolatban lásd: [bővítmények frissítése].

[core tools]: ./functions-run-local.md
[kiterjesztési csomag]: ./functions-bindings-register.md#extension-bundles
[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Bővítmények frissítése]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A functions 1. x alkalmazások automatikusan hivatkoznak a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomagra, 2. x verzióra.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="hostjson-settings"></a>Beállítások host.js

> [!NOTE]
> Ez a szakasz nem vonatkozik a 5.0.0 előtti bővítmény-verziók használatára. Ezen verziók esetében nincsenek globális konfigurációs beállítások a blobokhoz.

Ez a szakasz ismerteti a kötéshez elérhető globális konfigurációs beállításokat a [bővítmény 5.0.0 és újabb verziójának](#storage-extension-5x-and-higher)használatakor. Az alábbi példában szereplő *host.js* csak a kötés 2. x verziójának beállításait tartalmazza. További információ a functions 2. x verziójú függvények globális konfigurációs beállításairól: [host.jsAzure functions](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|Maxanalyticsunits|8 * (az elérhető magok száma)|Az egyes Blobok által aktivált függvények esetében engedélyezett egyidejű hívások egész számának száma. A minimálisan megengedett érték 1.|

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása a blob Storage-beli adatváltozások esetén](./functions-bindings-storage-blob-trigger.md)
- [BLOB Storage-adat olvasása függvény futtatásakor](./functions-bindings-storage-blob-input.md)
- [BLOB Storage-adatok írása függvényből](./functions-bindings-storage-blob-output.md)
