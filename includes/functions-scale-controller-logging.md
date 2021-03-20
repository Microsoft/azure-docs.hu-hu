---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 906413d0a6702e6146779f79d628b5cebf383af1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92165765"
---
| | |
|--|--|
|**`<DESTINATION>`**| Az a cél, ahová a rendszer elküldi a naplókat. Az érvényes értékek: `AppInsights` és `Blob` .<br/>A használatakor `AppInsights` Győződjön meg arról, hogy [a Application Insights engedélyezve van a Function alkalmazásban](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration).<br/>A célhely beállításakor a `Blob` rendszer a naplókat az `azure-functions-scale-controller` Alkalmazásbeállítások alapértelmezett Storage-fiókjában nevű blob-tárolóban hozza létre `AzureWebJobsStorage` . |
|**`<VERBOSITY>`** | Meghatározza a naplózási szintet. A támogatott értékek:, `None` `Warning` és `Verbose` .<br/>Ha a értékre `Verbose` van állítva, a skálázási vezérlő a munkavégzők számának minden változása miatt naplózza az okot, valamint az ezekkel a döntésekkel kapcsolatos eseményindítókat is. A részletes naplók közé tartoznak az eseményindító-figyelmeztetések és az eseményindítók által a skálázási vezérlő futtatása előtt és után használt kivonatok. |

> [!TIP]
> Ne feledje, hogy amíg a méretezési vezérlő naplózása engedélyezve van, az hatással van a [Function alkalmazás figyelésének lehetséges költségeire](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits). Érdemes lehet engedélyezni a naplózást, amíg nem gyűjtött elég adatokat a méretezési vezérlő működésének megismeréséhez, majd letiltásához.