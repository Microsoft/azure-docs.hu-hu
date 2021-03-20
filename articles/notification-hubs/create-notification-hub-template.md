---
title: Azure Notification hub létrehozása Azure Resource Manager sablon használatával
description: Ismerje meg, hogyan hozhat létre Azure Notification hub-t egy Azure Resource Manager sablon (ARM-sablon) használatával.
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sethm
ms.date: 08/04/2020
ms.reviewer: thsomasu
ms.lastreviewed: 05/15/2020
ms.openlocfilehash: 2165351bee4ee260e768c10b23e26bf095779cad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88684643"
---
# <a name="quickstart-create-a-notification-hub-using-an-arm-template"></a>Gyors útmutató: értesítési központ létrehozása ARM-sablonnal

Az Azure Notification Hubs egy könnyen használható és méretezhető leküldéses motort biztosít, amely lehetővé teszi, hogy bármilyen háttérből (Felhőbeli vagy helyszíni) bármilyen platformra (iOS, Android, Windows, Kindle stb.) küldjön értesítéseket. A szolgáltatással kapcsolatos további információkért lásd: [Mi az az Azure Notification Hubs](notification-hubs-push-notification-overview.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ez a rövid útmutató egy Azure Resource Manager sablont (ARM-sablont) használ egy Azure Notification Hubs-névtér létrehozásához, valamint egy **MyHub** nevű értesítési központot a névtéren belül.

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-notification-hub/) közül származik.

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json":::

* [Microsoft. NotificationHubs/névterek](/azure/templates/microsoft.notificationhubs/namespaces)
* [Microsoft. NotificationHubs/névterek/notificationHubs](/azure/templates/microsoft.notificationhubs/namespaces/notificationhubs)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon egy Notification Hubs névtér nevét adja meg paraméterként. A sablon Ezután létrehoz egy névteret a névvel és egy **MyHub** nevű értesítési központtal az adott névtéren belül.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Használhatja a Azure Portal az üzembe helyezett erőforrások vizsgálatára, vagy az Azure CLI vagy a Azure PowerShell parancsfájl használatával listázhatja az üzembe helyezett Notification Hubs névteret és hubot:

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzNotificationHub -Namespace "nhtestns123" -ResourceGroup "ContosoNotificationsGroup"
Get-AzNotificationHubsNamespace -Namespace "nhtestns123"
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
az notification-hub show --resource-group ContosoNotificationsGroup --namespace-name nhtestns123 --name MyHub
az notification-hub namespace show --resource-group ContosoNotificationsGroup --name nhtestns123
```

---
The output looks similar to: null
':::image type="content" source="media/create-notification-hub-template/verify-deploy.png" alt-text="Verify deployment"::': null
---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Következő lépések

A sablonok létrehozásának folyamatát ismertető, lépésenkénti oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
