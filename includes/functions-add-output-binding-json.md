---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 9b3859d854b3900cc30eda6a95b8425da6763e59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94341022"
---
A kötési attribútumok közvetlenül a fájl function.jsvannak definiálva. A kötési típustól függően szükség lehet további tulajdonságokra. A [várólista kimeneti konfigurációja](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) leírja az Azure Storage-várólista kötéséhez szükséges mezőket. A bővítmény megkönnyíti a kötések hozzáadását a fájl function.js. 

Kötés létrehozásához kattintson a jobb gombbal (CTRL + kattintás macOS-re) a `function.json` HttpTrigger mappában található fájlra, majd válassza a **kötés hozzáadása...** lehetőséget. Kövesse az utasításokat a következő kötési tulajdonságok definiálásához az új kötéshez:

| Adatkérés | Érték | Leírás |
| -------- | ----- | ----------- |
| **Kötési irány kiválasztása** | `out` | A kötés kimeneti kötés. |
| **Kötés kijelölése iránysal...** | `Azure Queue Storage` | A kötés egy Azure Storage-várólista kötése. |
| **A kódban a kötés azonosítására használt név** | `msg` | A kódban hivatkozott kötési paramétert azonosító név. |
| **Az az üzenetsor, amelybe az üzenet el lesz küldve** | `outqueue` | Annak a sornak a neve, amelyet a kötés ír. Ha a *queueName* nem létezik, a kötés létrehozza az első használatkor. |
| **Válassza a beállítás elemet a "local.setting.json"** | `AzureWebJobsStorage` | A Storage-fiókhoz tartozó kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve. A `AzureWebJobsStorage` beállítás tartalmazza a Function alkalmazással létrehozott Storage-fiókhoz tartozó kapcsolatok karakterláncát. |

A rendszer egy kötést ad hozzá a `bindings` function.jsa tömbhöz, amelynek a következőhöz hasonlóan kell kinéznie:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::
