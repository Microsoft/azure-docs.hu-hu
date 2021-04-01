---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93425040"
---
Futtassa a következő parancsot a közel valós idejű adatfolyam- [naplók](../articles/azure-functions/functions-run-local.md#enable-streaming-logs)megtekintéséhez:

```console
func azure functionapp logstream <APP_NAME> 
```

Egy külön terminál-ablakban vagy a böngészőben hívja meg újra a távoli függvényt. Az Azure-ban a funkció végrehajtásának részletes naplója látható a terminálon. 