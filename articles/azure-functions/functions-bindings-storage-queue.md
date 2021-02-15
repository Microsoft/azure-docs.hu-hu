---
title: Azure üzenetsor-tárolási trigger és kötések Azure Functions – áttekintés
description: Ismerje meg, hogyan használható az Azure üzenetsor-tároló-trigger és a kimeneti kötés a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: a1b9d03da29b7c89055303fa97fc38c2ef734b23
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381477"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Azure üzenetsor-tárolási trigger és kötések Azure Functions – áttekintés

Azure Functions futtatható új Azure üzenetsor-tároló üzenetekkel, és egy függvényen belül is írhat üzenetsor-üzeneteket.

| Művelet | Típus |
|---------|---------|
| Függvény futtatása üzenetsor-tárolási adatváltozásként | [Eseményindító](./functions-bindings-storage-queue-trigger.md) |
| Írási várólista tárolási üzenetei |[Kimeneti kötés](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Hozzáadás a functions-alkalmazáshoz

### <a name="functions-2x-and-higher"></a>Functions 2.x és újabb

Az trigger és a kötések használata megköveteli, hogy a megfelelő csomagra hivatkozzon. A NuGet csomag a .NET-osztály könyvtáraihoz használatos, míg a kiterjesztési köteg minden más alkalmazás típusához használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A [NuGet-csomag], 3. x verziójának telepítése | |
| C# parancsfájl, Java, JavaScript, Python, PowerShell | A [kiterjesztési csomag] regisztrálása          | Az [Azure Tools bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) használata ajánlott a Visual Studio Code használatával. |
| C#-szkript (csak online – Azure Portal)         | Kötés hozzáadása                            | Ha frissíteni szeretné a meglévő kötési bővítményeket anélkül, hogy újra közzé kellene tennie a Function alkalmazást, tekintse [meg a bővítmények frissítése]című témakört. |

#### <a name="storage-extension-5x-and-higher"></a>5. x és újabb Storage-bővítmény

A Storage-kötések bővítmény egy új verziója érhető el előzetes verziójú [NuGet-csomagként](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). Ez az előzetes verzió azt mutatja be, hogy [titkos kód helyett identitás használatával tud csatlakozni](./functions-reference.md#configure-an-identity-based-connection). A .NET-alkalmazások esetében is megváltoztatja a kötéshez köthető típusokat, és lecseréli az `WindowsAzure.Storage` `Microsoft.Azure.Storage` [Azure. Storage. Queues](/dotnet/api/azure.storage.queues)és újabb típusaiból származó típusokat.

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

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Beállítások host.js

Ez a szakasz a kötéshez elérhető globális konfigurációs beállításokat ismerteti 2. x vagy újabb verziókban. Az alábbi példában szereplő *host.js* csak a kötés 2. x verziójának beállításait tartalmazza. További információ a 2. x verzióban található globális konfigurációs beállításokról és azon kívül: [host.jsAzure functions](functions-host-json.md).

> [!NOTE]
> Az 1. x függvények host.jsának hivatkozását lásd:host.jsaz [ Azure functions 1. x](functions-host-json-v1.md)esetében.

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Description |
|---------|---------|---------|
|maxPollingInterval|00:00:01|A várólista-lekérdezések közötti maximális időköz. Minimum 00:00:00.100 (100 MS), és legfeljebb 00:01:00 (1 perc) értékkel növekszik.  A 2. x és újabb függvények esetében az adattípus a `TimeSpan` , az 1. x verzióban pedig ezredmásodperc van.|
|visibilityTimeout|00:00:00|Az újrapróbálkozások között eltelt idő az üzenet feldolgozásakor. |
|batchSize|16|Azoknak a üzenetsor-üzeneteknek a száma, amelyeket a függvények futtatókörnyezete egyszerre kér le, és párhuzamosan dolgozza fel a folyamatokat. A feldolgozás alatt álló szám lekérése után `newBatchThreshold` a futtatókörnyezet egy másik köteget kap, és elindítja az üzenetek feldolgozását. Így a függvények által feldolgozott egyidejű üzenetek maximális száma `batchSize` plusz `newBatchThreshold` . Ez a korlát külön vonatkozik az egyes üzenetsor-vezérelt függvényekre. <br><br>Ha el szeretné kerülni az egy várólistán fogadott üzenetek párhuzamos végrehajtását, beállíthatja `batchSize` az 1 értékre. Ez a beállítás azonban kiküszöböli a párhuzamosságot, ha a Function alkalmazás csak egyetlen virtuális gépen fut (VM). Ha a Function alkalmazás több virtuális gépre is kiterjed, minden egyes virtuális gép futtathatja az egyes üzenetsor által aktivált függvények egy példányát.<br><br>A maximális `batchSize` érték 32. |
|maxDequeueCount|5|Azon alkalmak száma, amelyekkel az üzenetek feldolgozására kerül sor, mielőtt a rendszer áthelyezi azt a Megmérgező várólistára.|
|newBatchThreshold|batchSize/2|Ha az egyidejűleg feldolgozható üzenetek száma leállítja ezt a számot, a futtatókörnyezet egy másik köteget kérdez le.|
|messageEncoding|base64| Ez a beállítás csak a [bővítmény 5.0.0 és újabb verzióiban](#storage-extension-5x-and-higher)érhető el. Az üzenetek kódolási formátumát jelöli. Az érvényes értékek: `base64` és `none` .|

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása üzenetsor-tárolási adatváltozásként (trigger)](./functions-bindings-storage-queue-trigger.md)
- [Írási várólista tárolási üzenetei (kimeneti kötés)](./functions-bindings-storage-queue-output.md)
