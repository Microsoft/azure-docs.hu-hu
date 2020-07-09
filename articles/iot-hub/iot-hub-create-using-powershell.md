---
title: Azure-IoT Hub létrehozása PowerShell-parancsmag használatával | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy erőforráscsoportot a PowerShell-parancsmagok használatával, majd hogyan hozhat létre IoT hubot az erőforráscsoporthoz. Azt is megtudhatja, hogyan távolíthatja el a hubot.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 9c49f7ac744ee516aefc1571d50264132035ba8a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "73890602"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>IoT hub létrehozása a New-AzIotHub parancsmag használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction (Bevezetés)

Az Azure IoT-hubok létrehozásához és kezeléséhez Azure PowerShell parancsmagokat is használhat. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre IoT hubot a PowerShell használatával.

A útmutató végrehajtásához Azure-előfizetésre van szükség. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Csatlakozás az Azure-előfizetéshez

Ha a Cloud Shell használja, már be van jelentkezve az előfizetésbe. Ha ehelyett a PowerShellt helyileg futtatja, a következő parancs beírásával jelentkezzen be az Azure-előfizetésbe:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az IoT hub üzembe helyezéséhez erőforráscsoport szükséges. Használhat meglévő erőforráscsoportot, vagy létrehozhat egy újat.

Az IoT hub-hoz tartozó erőforráscsoport létrehozásához használja a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) parancsot. Ez a példa létrehoz egy **MyIoTRG1** nevű ERŐFORRÁSCSOPORTOT az **USA keleti** régiójában:

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha az előző lépésben létrehozott erőforráscsoporthoz IoT hubot szeretne létrehozni, használja a [New-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) parancsot. Ez a példa egy **MyTestIoTHub** nevű **S1** HUBOT hoz létre az **USA keleti** régiójában:

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Az IoT hub nevének globálisan egyedinek kell lennie.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Az előfizetésben található összes IoT-hub a [Get-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) paranccsal listázható:

```azurepowershell-interactive
Get-AzIotHub
```

Ez a példa az előző lépésben létrehozott S1 standard IoT Hub mutatja.

Az IoT hub a [Remove-AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) paranccsal törölhető:

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Azt is megteheti, hogy eltávolít egy erőforráscsoportot és a benne található összes erőforrást a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup) parancs használatával:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>További lépések

Most már üzembe helyezett egy IoT hubot egy PowerShell-parancsmag használatával, ha további részleteket szeretne felfedezni, tekintse meg a következő cikkeket:

* [PowerShell-parancsmagok az IoT hub használatával végzett munkához](https://docs.microsoft.com/powershell/module/az.iothub/).

* [IoT hub erőforrás-szolgáltató REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Ha többet szeretne megtudni a IoT Hub fejlesztéséről, tekintse meg a következő cikkeket:

* [A C SDK bemutatása](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
