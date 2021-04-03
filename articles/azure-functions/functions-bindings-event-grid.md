---
title: Azure Functions-kötések Azure Event Grid
description: Ismerje meg, hogyan kezelheti Event Grid eseményeit Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 115ff1b59ecbe3f4fdb089c2bd61da955fba3984
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92104546"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Functions-kötések Azure Event Grid

Ez a hivatkozás a Azure Functions [Event Grid](../event-grid/overview.md) eseményeinek kezelését ismerteti. Az Event Grid üzenetek HTTP-végponton való kezelésével kapcsolatos részletekért lásd: [események fogadása http-végpontra](../event-grid/receive-events.md).

Event Grid egy olyan Azure-szolgáltatás, amely HTTP-kéréseket küld, hogy értesítse a *kiadói* eseményekről. A közzétevő az eseményt kezdeményező szolgáltatás vagy erőforrás. Egy Azure Blob Storage-fiók például közzétevő, [a blob feltöltése vagy törlése pedig egy esemény](../storage/blobs/storage-blob-event-overview.md). Egyes [Azure-szolgáltatások beépített támogatást biztosítanak az események Event Grid való közzétételéhez](../event-grid/overview.md#event-sources).

Az *eseménykezelők fogadják* és dolgozzák fel az eseményeket. A Azure Functions számos olyan [Azure-szolgáltatás egyike, amelyek beépített támogatást biztosítanak a Event Grid események kezeléséhez](../event-grid/overview.md#event-handlers). Ebben a leírásban megtudhatja, hogyan használhat egy Event Grid eseményindítót egy függvény meghívásához, ha a rendszer egy eseményt fogad Event Gridtól, és a kimeneti kötés használatával küldi az eseményeket egy [Event Grid egyéni témakörnek](../event-grid/post-to-custom-topic.md).

Ha szeretné, használhat egy HTTP-triggert Event Grid események kezelésére; Lásd: [események fogadása http-végpontra](../event-grid/receive-events.md). Jelenleg nem használhat Event Grid eseményindítót egy Azure Functions alkalmazáshoz, ha az esemény a [CloudEvents-sémában](../event-grid/cloudevents-schema.md#azure-functions)kerül kézbesítésre. Ehelyett használjon HTTP-triggert.

| Művelet | Típus |
|---------|---------|
| Függvény futtatása egy Event Grid esemény elküldésekor | [Eseményindító](./functions-bindings-event-grid-trigger.md) |
| Event Grid esemény küldése |[Kimeneti kötés](./functions-bindings-event-grid-output.md) |

A hivatkozásban szereplő kód a .NET Core szintaxisra vonatkozik, amely a functions 2. x vagy újabb verziójában használatos. Az 1. x szintaxissal kapcsolatos további információkért tekintse meg az [1. x függvények sablonjait](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Hozzáadás a functions-alkalmazáshoz

### <a name="functions-2x-and-higher"></a>Functions 2.x és újabb

Az trigger és a kötések használata megköveteli, hogy a megfelelő csomagra hivatkozzon. A NuGet csomag a .NET-osztály könyvtáraihoz használatos, míg a kiterjesztési köteg minden más alkalmazás típusához használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A [NuGet-csomag], 2. x verziójának telepítése | |
| C# parancsfájl, Java, JavaScript, Python, PowerShell | A [kiterjesztési csomag] regisztrálása          | Az [Azure Tools bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) használata ajánlott a Visual Studio Code használatával. |
| C#-szkript (csak online – Azure Portal)         | Kötés hozzáadása                            | Ha frissíteni szeretné a meglévő kötési bővítményeket anélkül, hogy újra közzé kellene tennie a Function alkalmazást, tekintse [meg a bővítmények frissítése]című témakört. |

[core tools]: ./functions-run-local.md
[kiterjesztési csomag]: ./functions-bindings-register.md#extension-bundles
[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Bővítmények frissítése]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A functions 1. x alkalmazások automatikusan hivatkoznak a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomagra, 2. x verzióra.

## <a name="next-steps"></a>Következő lépések
* [Függvény futtatása egy Event Grid esemény elküldésekor](./functions-bindings-event-grid-trigger.md)
* [Event Grid esemény elküldése](./functions-bindings-event-grid-trigger.md)