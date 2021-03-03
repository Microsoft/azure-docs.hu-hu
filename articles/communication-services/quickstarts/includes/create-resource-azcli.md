---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: 32d869e6285e76b57a7b8e462e8110a43de82dd9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656575"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Azure kommunikációs erőforrás létrehozása

Azure kommunikációs szolgáltatások erőforrásának létrehozásához [Jelentkezzen be az Azure CLI-be](/cli/azure/authenticate-azure-cli), majd futtassa a következő parancsot:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

A kommunikációs szolgáltatások erőforrását a következő beállításokkal állíthatja be:

* Az erőforráscsoport
* A kommunikációs szolgáltatások erőforrásának neve
* Az a földrajzi hely, amelyhez az erőforrás társítva lesz

A következő lépésben címkéket rendelhet hozzá az erőforráshoz. Az Azure-erőforrások rendszerezéséhez címkéket is használhat. A címkékkel kapcsolatos további információkért tekintse meg az [erőforrás-címkézési dokumentációt](../../../azure-resource-manager/management/tag-resources.md) .

## <a name="manage-your-communication-services-resource"></a>A kommunikációs szolgáltatások erőforrásának kezelése

Ha címkéket szeretne hozzáadni a kommunikációs szolgáltatások erőforrásához, futtassa a következő parancsokat:

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

További információ a további parancsokról: [az Communication](/cli/azure/ext/communication/communication).