---
author: baanders
description: fájl belefoglalása az Azure Digital Twinsba – a legújabb IoT-bővítmény beállítása
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 984739a728f6ac5e28eeb561e0d7b6ec0485ca13
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496865"
---
Először futtassa ezt a parancsot a már telepített bővítmények listájának megtekintéséhez.

```azurecli-interactive
az extension list
```

A kimenet a jelenleg meglévő bővítmények tömbje. A `"name"` bővítmények neveinek megtekintéséhez keresse meg az egyes listaelemek mezőjét.

A kimenet használatával határozza meg, hogy a következő parancsok melyike fusson a bővítmény telepítéséhez (több is futtatható).
* Ha a lista a `azure-iot` következőket tartalmazza: már rendelkezik a kiterjesztéssel. A parancs futtatásával győződjön meg arról, hogy rendelkezik a legújabb frissítéssel, és nincs több elérhető frissítés:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Ha a lista **nem** tartalmazza a következőt `azure-iot` : telepítenie kell a bővítményt. Használja a következő parancsot:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Ha a lista tartalmazza a `azure-iot-cli-ext` következőt: Ez a bővítmény örökölt verziója. Egyszerre csak egy bővítményt kell telepíteni, ezért el kell távolítania az örökölt bővítményt. Használja a következő parancsot:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```