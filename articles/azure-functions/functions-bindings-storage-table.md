---
title: Azure Functions Azure Table Storage-kötések
description: Ismerje meg, hogyan használhatók az Azure Table Storage-kötések Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4f2b890dc60cd50b5fcaefabe8d418268b738c20
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92096725"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Functions Azure Table Storage-kötések

A Azure Functions [triggerekkel és kötésekkel](./functions-triggers-bindings.md)integrálható az [Azure Storage](../storage/index.yml) -ba. A Table Storage szolgáltatással való integráció lehetővé teszi a Table Storage-adatok olvasását és írására szolgáló függvények létrehozását.

| Művelet | Típus |
|---------|---------|
| Táblázat tárolási adatának olvasása függvényben | [Bemeneti kötés](./functions-bindings-storage-table-input.md) |
| Táblázatos tárolói adat írására szolgáló függvény engedélyezése |[Kimeneti kötés](./functions-bindings-storage-table-output.md) |

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A Table Storage-kötések a [Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet csomag 3. x verziójában érhetők el. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

A Table Storage-kötések a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet csomagban, 2. x verzióban vannak megadva. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Következő lépések

- [Táblázat tárolási adatának olvasása függvény futtatásakor](./functions-bindings-storage-table-input.md)
- [Táblázatos tárolási adatok írása függvényből](./functions-bindings-storage-table-output.md)
