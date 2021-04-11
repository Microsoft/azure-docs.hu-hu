---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 4cbf179658ddc13aca9bf30934dc28ab0cf5fd9d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081010"
---
| Tulajdonság | Leírás |
|--|--|
|**`<DESTINATION>`**| Az a cél, ahová a rendszer elküldi a naplókat. Az érvényes értékek: `AppInsights` és `Blob` .<br/>A használatakor `AppInsights` Győződjön meg arról, hogy [a Application Insights engedélyezve van a Function alkalmazásban](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration).<br/>A célhely beállításakor a `Blob` rendszer a naplókat az `azure-functions-scale-controller` Alkalmazásbeállítások alapértelmezett Storage-fiókjában nevű blob-tárolóban hozza létre `AzureWebJobsStorage` . |
|**`<VERBOSITY>`** | Meghatározza a naplózási szintet. A támogatott értékek:, `None` `Warning` és `Verbose` .<br/>Ha a értékre `Verbose` van állítva, a skálázási vezérlő a munkavégzők számának minden változása miatt naplózza az okot, valamint az ezekkel a döntésekkel kapcsolatos eseményindítókat is. A részletes naplók közé tartoznak az eseményindító-figyelmeztetések és az eseményindítók által a skálázási vezérlő futtatása előtt és után használt kivonatok. |

> [!TIP]
> Ne feledje, hogy amíg a méretezési vezérlő naplózása engedélyezve van, az hatással van a [Function alkalmazás figyelésének lehetséges költségeire](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits). Érdemes lehet engedélyezni a naplózást, amíg nem gyűjtött elég adatokat a méretezési vezérlő működésének megismeréséhez, majd letiltásához.
