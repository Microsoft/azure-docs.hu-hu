---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93425040"
---
Futtassa a következő parancsot a közel valós idejű adatfolyam- [naplók](../articles/azure-functions/functions-run-local.md#enable-streaming-logs)megtekintéséhez:

```console
func azure functionapp logstream <APP_NAME> 
```

Egy külön terminál-ablakban vagy a böngészőben hívja meg újra a távoli függvényt. Az Azure-ban a funkció végrehajtásának részletes naplója látható a terminálon. 