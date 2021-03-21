---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 05d136093bd509e8c23ce8622423216326b0f1f2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102623398"
---
## <a name="add-to-your-functions-app"></a>Hozzáadás a functions-alkalmazáshoz

### <a name="functions-2x-and-higher"></a>Functions 2.x és újabb

Az trigger és a kötések használata megköveteli, hogy a megfelelő csomagra hivatkozzon. A NuGet csomag a .NET-osztály könyvtáraihoz használatos, míg a kiterjesztési köteg minden más alkalmazás típusához használatos.

| Nyelv                                        | Hozzáadás...                                   | Megjegyzések 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | A [NuGet-csomag], 3. x verziójának telepítése | |
| C# parancsfájl, Java, JavaScript, Python, PowerShell | A [kiterjesztési csomag] regisztrálása          | Az [Azure Tools bővítmény] használata ajánlott a Visual Studio Code használatával. |
| C#-szkript (csak online – Azure Portal)         | Kötés hozzáadása                            | Ha frissíteni szeretné a meglévő kötési bővítményeket anélkül, hogy újra közzé kellene tennie a Function alkalmazást, tekintse [meg a bővítmények frissítése]című témakört. |

[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[kiterjesztési csomag]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Bővítmények frissítése]: ../articles/azure-functions/functions-bindings-register.md
[Azure-eszközök bővítmény]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Event Hubs 5. x vagy újabb bővítmény

A Event Hubs kötések bővítmény új verziója érhető el előzetes verziójú NuGet- [csomagként](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/5.0.0-beta.1). Ez az előzetes verzió azt mutatja be, hogy [titkos kód helyett identitás használatával tud csatlakozni](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection). A .NET-alkalmazások esetében az azt is megváltoztatja, hogy milyen típusú kötések hozhatók létre, a típusait az `Microsoft.Azure.EventHubs` [Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs)újabb típusaiból cserélje le.

> [!NOTE]
> Az előnézeti csomag nem szerepel a kiterjesztési csomagban, és manuálisan kell telepíteni. .NET-alkalmazások esetén adjon hozzá egy hivatkozást a csomaghoz. Az összes többi alkalmazás típusával kapcsolatban lásd: [bővítmények frissítése].

[core tools]: ./functions-run-local.md
[kiterjesztési csomag]: ./functions-bindings-register.md#extension-bundles
[NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[Bővítmények frissítése]: ./functions-bindings-register.md
[Azure-eszközök bővítmény]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

A functions 1. x alkalmazások automatikusan hivatkoznak a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomagra, 2. x verzióra.

## <a name="hostjson-settings"></a>Beállítások host.js
<a name="host-json"></a>

A [host.js](../articles/azure-functions/functions-host-json.md#eventhub) fájl olyan beállításokat tartalmaz, amelyek a Event Hubs trigger viselkedését vezérlik. A konfiguráció a Azure Functions verziójától függően eltérő.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]