---
title: Hozzon létre IoT Hub Azure CLI-| Microsoft Docs
description: Megtudhatja, hogyan hozhat létre erőforráscsoportot az Azure CLI-parancsokkal, majd hogyan hozhat létre egy IoT Hubot az erőforráscsoportban. Azt is megtudhatja, hogyan távolíthatja el a központot.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: eff0085a4a739e0831b25b1bd28cd234fdbcde3d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766442"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>IoT Hub létrehozása az Azure CLI használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk bemutatja, hogyan hozhat létre IoT Hubot az Azure CLI használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az Azure CLI használatával hozzon létre egy erőforráscsoportot, majd vegyen fel egy IoT Hubot.

1. IoT Hub létrehozásakor azt egy erőforráscsoportban kell létrehoznia. Használhat meglévő erőforráscsoportot, vagy futtathatja a következő [parancsot erőforráscsoport létrehozásához](/cli/azure/resource):
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > Az előző példában az erőforráscsoport az USA nyugati régiójában jön létre. Az elérhető helyek listáját az alábbi parancs futtatásával tudja megtekinteni: 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. Futtassa az erőforráscsoporthoz tartozó alábbi [IoT Hub létrehozási parancsot](/cli/azure/iot/hub#az_iot_hub_create) egy globálisan egyedi IoT Hub-név használatával:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Az előző parancs egy IoT Hubot hoz létre az S1 tarifacsomagban, amely után fizetni kell. További információért tekintse meg a [díjszabást Azure IoT Hub.](https://azure.microsoft.com/pricing/details/iot-hub/)

## <a name="remove-an-iot-hub"></a>Új IoT Hub

Az Azure CLI használatával [törölhet](/cli/azure/resource)egy adott erőforrást, például egy IoT Hubot, vagy törölhet egy erőforráscsoportot és annak összes erőforrását, beleértve az IoT Hubokat is.

Az [IoT Hub törléséhez futtassa](/cli/azure/iot/hub#az_iot_hub_delete)a következő parancsot:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Egy [erőforráscsoport és annak](/cli/azure/group#az_group_delete) összes erőforrásának törléséhez futtassa a következő parancsot:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Következő lépések

Az IoT Hubok használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [IoT Hub útmutató](iot-hub-devguide.md)
* [A Azure Portal használata a IoT Hub](iot-hub-create-through-portal.md)