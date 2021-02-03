---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493945"
---
A Visual Studio Code lehetővé teszi function.jskötések hozzáadását a fájlokhoz a következő kényelmes készlettel. 

Kötés hozzáadásához nyissa meg a Command raklap (F1) parancsot, és írja be a **Azure functions: kötés hozzáadása...** lehetőséget, válassza ki a függvényt az új kötéshez, majd kövesse az utasításokat, amelyek a függvényhez hozzáadott kötés típusától függően változnak. 

A következő példa arra kéri, hogy adjon meg egy új tárolási kimeneti kötést:

| Adatkérés | Érték | Leírás |
| -------- | ----- | ----------- |
| **Kötési irány kiválasztása** | `out` | A kötés kimeneti kötés. |
| **Kötés kijelölése iránysal** | `Azure Queue Storage` | A kötés egy Azure Storage-várólista kötése. |
| **A kódban a kötés azonosítására használt név** | `msg` | A kódban hivatkozott kötési paramétert azonosító név. |
| **Az az üzenetsor, amelybe az üzenet el lesz küldve** | `outqueue` | Annak a sornak a neve, amelyet a kötés ír. Ha a *queueName* nem létezik, a kötés létrehozza az első használatkor. |
| **Válassza a beállítás elemet a "local.settings.json"** | `MyStorageConnection` | A Storage-fiókhoz tartozó kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve. A `AzureWebJobsStorage` beállítás tartalmazza a Function alkalmazással létrehozott Storage-fiókhoz tartozó kapcsolatok karakterláncát. |

Azt is megteheti, hogy a jobb gombbal kattint (CTRL + kattintás macOS rendszeren) közvetlenül a **function.js** fájlhoz a Function mappában, válassza a **kötés hozzáadása** lehetőséget, és kövesse ugyanezen utasításokat.

Ebben a példában a következő kötést adja hozzá a `bindings` tömbhöz a function.jsfájljában:

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```