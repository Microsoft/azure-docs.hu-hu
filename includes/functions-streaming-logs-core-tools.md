---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881323"
---
#### <a name="built-in-log-streaming"></a>Beépített naplóstreamelés

Ezzel `logstream` a lehetőséggel megkezdheti az Azure-ban futó adott függvényalkalmazás streamelési naplóinak fogadását, ahogy az a következő példában is:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Élő metrikastream

A funkcióalkalmazás [élő mérőszámfolyam-közvetítését](../articles/azure-monitor/app/live-stream.md) egy új böngészőablakban `--browser` is megtekintheti, ha a következő példában is szerepel:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
