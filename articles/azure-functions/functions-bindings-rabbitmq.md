---
title: Azure Functions Azure RabbitMQ-kötések
description: Ismerje meg, hogyan küldhet Azure RabbitMQ-eseményindítókat és-kötéseket Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 2a480f25821f5022295b18ca24abfd2c0fb8a50c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746524"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>Azure Functions RabbitMQ-kötések – áttekintés

> [!NOTE]
> A RabbitMQ-kötések csak a **prémium és a dedikált** csomagok esetében teljes mértékben támogatottak. A felhasználás nem támogatott.

A Azure Functions [triggerek és kötések](./functions-triggers-bindings.md)használatával integrálható a [RabbitMQ](https://www.rabbitmq.com/) . A Azure Functions RabbitMQ bővítmény lehetővé teszi üzenetek küldését és fogadását a RabbitMQ API és a functions használatával.

| Művelet | Típus |
|---------|---------|
| Függvény futtatása, ha egy RabbitMQ-üzenet a várólistán keresztül érkezik | [Eseményindító](./functions-bindings-rabbitmq-trigger.md) |
| RabbitMQ üzenetek küldése |[Kimeneti kötés](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>Hozzáadás a functions-alkalmazáshoz

A bővítmény fejlesztésének megkezdéséhez győződjön meg arról, hogy először [állít be egy RabbitMQ-végpontot](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint). Ha többet szeretne megtudni a RabbitMQ, tekintse meg az [első lépéseket ismertető oldalt](https://www.rabbitmq.com/getstarted.html).

### <a name="functions-3x-and-higher"></a>3. x és újabb függvények

Az trigger és a kötések használata megköveteli, hogy a megfelelő csomagra hivatkozzon. A NuGet csomag a .NET-osztály könyvtáraihoz használatos, míg a kiterjesztési köteg minden más alkalmazás típusához használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A 4. x verziójú [NuGet-csomag]telepítése | |
| C# parancsfájl, Java, JavaScript, Python, PowerShell | A [kiterjesztési csomag] regisztrálása          | Az [Azure Tools bővítmény] használata ajánlott a Visual Studio Code használatával. |
| C#-szkript (csak online – Azure Portal)         | Kötés hozzáadása                            | Ha frissíteni szeretné a meglévő kötési bővítményeket anélkül, hogy újra közzé kellene tennie a Function alkalmazást, tekintse [meg a bővítmények frissítése]című témakört. |

[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[kiterjesztési csomag]: ./functions-bindings-register.md#extension-bundles
[Bővítmények frissítése]: ./functions-bindings-register.md
[Azure-eszközök bővítmény]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>1. x és 2. x függvények

A RabbitMQ kötési bővítmények nem támogatottak az 1. x és 2. x függvényeknél. Használja a 3. x és újabb függvényeket.

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása RabbitMQ-üzenet létrehozásakor (trigger)](./functions-bindings-rabbitmq-trigger.md)
- [RabbitMQ üzenetek küldése Azure Functionsból (kimeneti kötés)](./functions-bindings-rabbitmq-output.md)