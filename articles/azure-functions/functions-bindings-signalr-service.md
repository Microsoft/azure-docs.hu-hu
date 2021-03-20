---
title: Azure Functions szignáló szolgáltatás kötései
description: Megtudhatja, hogyan használhatja a Signaler szolgáltatás kötéseit Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 9e60fd9a20720d75f96a0b78ee783bd5509a8f90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97763489"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR-szolgáltatáskötések az Azure Functionshöz

Ez a cikk azt ismerteti, hogyan lehet valós idejű üzeneteket hitelesíteni és elküldeni az [Azure signaler szolgáltatáshoz](https://azure.microsoft.com/services/signalr-service/) csatlakozó ügyfeleknek a signaler szolgáltatás kötéseinek használatával Azure Functionsban. Az Azure Functions támogatja a SignalR-szolgáltatás bemeneti és kimeneti kötéseit.

| Művelet | Típus |
|---------|---------|
| A Signaler szolgáltatás üzeneteinek kezelése | [Trigger kötése](./functions-bindings-signalr-service-trigger.md) |
| A szolgáltatási végpont URL-címének és hozzáférési jogkivonatának visszaküldése | [Bemeneti kötés](./functions-bindings-signalr-service-input.md) |
| Szignáló szolgáltatás üzeneteinek küldése |[Kimeneti kötés](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Hozzáadás a functions-alkalmazáshoz

### <a name="functions-2x-and-higher"></a>Functions 2.x és újabb

Az trigger és a kötések használata megköveteli, hogy a megfelelő csomagra hivatkozzon. A NuGet csomag a .NET-osztály könyvtáraihoz használatos, míg a kiterjesztési köteg minden más alkalmazás típusához használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A [NuGet-csomag], 3. x verziójának telepítése | |
| C# parancsfájl, Java, JavaScript, Python, PowerShell | A [kiterjesztési csomag] regisztrálása          | Az [Azure Tools bővítmény] használata ajánlott a Visual Studio Code használatával. |
| C#-szkript (csak online – Azure Portal)         | Kötés hozzáadása                            | Ha frissíteni szeretné a meglévő kötési bővítményeket anélkül, hogy újra közzé kellene tennie a Function alkalmazást, tekintse [meg a bővítmények frissítése]című témakört. |

[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[kiterjesztési csomag]: ./functions-bindings-register.md#extension-bundles
[Bővítmények frissítése]: ./functions-bindings-register.md
[Azure-eszközök bővítmény]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

A Signaler Azure Functions szolgáltatás konfigurálásával és használatával kapcsolatos további részletekért tekintse meg a [Azure functions fejlesztés és konfigurálás az Azure signaler szolgáltatással](../azure-signalr/signalr-concept-serverless-development-config.md)című témakört.

### <a name="annotations-library-java-only"></a>Jegyzetek könyvtára (csak Java)

Ahhoz, hogy használni lehessen a Signaler szolgáltatás megjegyzéseit a Java-függvényekben, hozzá kell adnia egy függőséget az *Azure-functions-Java-Library-signaler* összetevőhöz (1,0-es vagy újabb verzió) a *pom.xml* fájlhoz.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Következő lépések

- [A Signaler szolgáltatás üzeneteinek kezelése (trigger kötése)](./functions-bindings-signalr-service-trigger.md)
- [A szolgáltatási végpont URL-címének és hozzáférési jogkivonatának visszaadása (bemeneti kötés)](./functions-bindings-signalr-service-input.md)
- [Szignáló szolgáltatás üzeneteinek küldése (kimeneti kötés)](./functions-bindings-signalr-service-output.md)