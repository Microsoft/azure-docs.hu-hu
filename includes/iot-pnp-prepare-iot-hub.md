---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: ea5d4ef26fb14e22b871bb4bfa1054cb749d38e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673025"
---
## <a name="prepare-an-iot-hub"></a>IoT hub előkészítése

A cikk lépéseinek elvégzéséhez szüksége lesz egy Azure IoT hub-ra az Azure-előfizetésben. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Ha helyileg használja az Azure CLI-t, először jelentkezzen be az Azure-előfizetésbe a használatával `az login` . Ha ezeket a parancsokat a Azure Cloud Shell futtatja, a rendszer automatikusan bejelentkezett.

Ha helyileg használja az Azure CLI-t, a `az` verziónak a **2.8.0** vagy újabb verziónak kell lennie; a Azure Cloud Shell a legújabb verziót használja. A `az --version` parancs használatával keresse meg a számítógépen telepített verziót.

A következő parancs futtatásával adja hozzá a Microsoft Azure IoT-bővítményt az Azure CLI-hez a példányhoz:

```azurecli-interactive
az extension add --name azure-iot
```

Ha még nem rendelkezik IoT hub-használattal, futtassa az alábbi parancsokat egy erőforráscsoport és egy ingyenes szintű IoT hub létrehozásához az előfizetésében. Cserélje le a helyére `<YourIoTHubName>` egy tetszőleges hub-nevet:

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

A következő parancs futtatásával hozza létre az eszköz identitását az IoT hub-ban. Cserélje le a `<YourIoTHubName>` és a `<YourDeviceID>` helyőrzőt a saját _IoT hub nevére_ és az Ön által választott _eszköz-azonosítóra_ .

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
