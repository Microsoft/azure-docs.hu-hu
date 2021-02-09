---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 647226091c3e15a19bf8262c23e62d95018488b3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831203"
---
Ha azt tervezi, hogy folytatja a további IoT Plug and Play cikkekkel, megtarthatja és újra felhasználhatja a cikkben használt erőforrásokat. Ellenkező esetben törölheti a cikkben létrehozott erőforrásokat a további díjak elkerülése érdekében.

A hubot és a regisztrált eszközt egyszerre is törölheti, ha törli a teljes erőforráscsoportot az alábbi Azure CLI-paranccsal. Ne használja ezt a parancsot, ha ezek az erőforrások megosztanak egy erőforráscsoportot a megőrizni kívánt egyéb erőforrásokkal.

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```

Ha csak az IoT hubot szeretné törölni, futtassa az alábbi parancsot az Azure CLI használatával:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Ha csak az IoT hub-ban regisztrált eszköz identitását szeretné törölni, futtassa az alábbi parancsot az Azure CLI használatával:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Előfordulhat, hogy el kívánja távolítani a klónozott minta fájlokat a fejlesztői gépről.
