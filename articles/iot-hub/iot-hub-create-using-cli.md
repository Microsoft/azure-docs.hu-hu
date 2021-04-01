---
title: IoT Hub létrehozása az Azure CLI használatával | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy erőforráscsoportot az Azure CLI-parancsokkal, majd hogyan hozhat létre IoT hubot az erőforráscsoporthoz. Azt is megtudhatja, hogyan távolíthatja el a hubot.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e9757b94330f889869080a0e961ab2728cee86f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659930"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>IoT hub létrehozása az Azure CLI használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk bemutatja, hogyan hozhat létre egy IoT hubot az Azure CLI használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az Azure CLI használatával hozzon létre egy erőforráscsoportot, majd vegyen fel egy IoT Hubot.

1. IoT hub létrehozásakor létre kell hoznia egy erőforráscsoportot. Használhat meglévő erőforráscsoportot, vagy futtathatja a következő [parancsot erőforráscsoport létrehozásához](/cli/azure/resource):
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > Az előző példában az erőforráscsoport az USA nyugati régiójában jön létre. Az elérhető helyszínek listáját a következő parancs futtatásával tekintheti meg: 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. Futtassa az erőforráscsoporthoz tartozó alábbi [IoT Hub létrehozási parancsot](/cli/azure/iot/hub#az-iot-hub-create) egy globálisan egyedi IoT Hub-név használatával:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Az előző parancs egy IoT hubot hoz létre az S1 díjszabási szinten, amelynek számlázása folyamatban van. További információ: [Azure IoT hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>IoT Hub eltávolítása

Az Azure CLI-vel [törölhet egy adott erőforrást](/cli/azure/resource), például egy IoT hubot, vagy törölhet egy erőforráscsoportot és annak összes erőforrását, beleértve az IoT-hubokat is.

Az [IoT hub törléséhez](/cli/azure/iot/hub#az-iot-hub-delete)futtassa a következő parancsot:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Egy erőforráscsoport és az összes erőforrás [törléséhez](/cli/azure/group#az-group-delete) futtassa a következő parancsot:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az IoT hub használatáról, tekintse meg a következő cikkeket:

* [IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [A Azure Portal használata a IoT Hub kezeléséhez](iot-hub-create-through-portal.md)