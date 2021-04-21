---
title: Alkalmazások IoT Central Azure CLI-ről | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet IoT Central alkalmazást a CLI használatával. Az alkalmazást a CLI használatával megtekintheti, módosíthatja és eltávolíthatja.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: c30781cb83436e15a217a1d43c0e39facae9f52d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770411"
---
# <a name="manage-iot-central-from-azure-cli"></a>Alkalmazások IoT Central Azure CLI-ről

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Ahelyett, hogy új alkalmazásokat IoT Central az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén, az Azure [CLI-t](/cli/azure/) használhatja az alkalmazások kezeléséhez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ha a CLI-parancsokat egy másik Azure-előfizetésben kell futtatnia, tekintse meg az [aktív előfizetés módosítása részt.](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)

## <a name="create-an-application"></a>Alkalmazás létrehozása

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Az [az iot central app create paranccsal](/cli/azure/iot/central/app#az_iot_central_app_create) hozzon létre egy IoT Central az Azure-előfizetésében. Például:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Ezek a parancsok először létrehoznak egy erőforráscsoportot az USA keleti régiójában az alkalmazás számára. Az alábbi táblázat az az **iot central app create** paranccsal használt paramétereket ismerteti:

| Paraméter         | Leírás |
| ----------------- | ----------- |
| resource-group    | Az alkalmazást tartalmazó erőforráscsoport. Ennek az erőforráscsoportnak már léteznie kell az előfizetésében. |
| location          | Alapértelmezés szerint ez a parancs az erőforráscsoport helyét használja. Jelenleg az Ausztrália, **IoT Central,** Európa,  **Ázsia és a Csendes-óceáni térség,** **Egyesült Államok,** az Egyesült Királyság és Japán földrajzi  helyen hozhat létre. |
| name              | Az alkalmazás neve a Azure Portal. |
| Aldomain         | Az alkalmazás URL-címének altartománya. A példában az alkalmazás URL-címe `https://mysubdomain.azureiotcentral.com` . |
| Sku               | Jelenleg az **ST1** és az **ST2 is használható.** Lásd [Azure IoT Central díjszabást.](https://azure.microsoft.com/pricing/details/iot-central/) |
| sablon          | A használni kívánt alkalmazássablon. További információt az alábbi táblázat tartalmaz. |
| megjelenített név      | Az alkalmazásnak a felhasználói felületen megjelenő neve. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Saját alkalmazások megtekintése

Használja az [az iot central app list parancsot](/cli/azure/iot/central/app#az_iot_central_app_list) a IoT Central és a metaadatok megtekintéséhez.

## <a name="modify-an-application"></a>Alkalmazás módosítása

Az [az iot central app update paranccsal](/cli/azure/iot/central/app#az_iot_central_app_update) frissítheti egy IoT Central metaadatait. Például az alkalmazás megjelenített nevének módosítása:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Alkalmazás eltávolítása

Az [az iot central app delete paranccsal](/cli/azure/iot/central/app#az_iot_central_app_delete) törölhet egy IoT Central alkalmazást. Például:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan kezelheti a Azure IoT Central az Azure CLI-ről, a következő javasolt lépés:

> [!div class="nextstepaction"]
> [Alkalmazás felügyelése](howto-administer.md)
