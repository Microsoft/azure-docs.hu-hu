---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 22a9cf3338f422341928a77f2bf14c497aa2ba31
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563776"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/dotnet/).
- Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?tabs=azure-cli) telepítése 

## <a name="create-azure-communication-resource"></a>Azure kommunikációs erőforrás létrehozása

Azure kommunikációs szolgáltatások erőforrásának létrehozásához [Jelentkezzen be az Azure CLI-be](/cli/azure/authenticate-azure-cli). Ezt megteheti a terminálon keresztül a ```az login``` paranccsal, és megadhatja a hitelesítő adatait. Futtassa az alábbi parancsot az erőforrás létrehozásához:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Ha ki szeretne választani egy adott előfizetést, megadhatja a ```--subscription``` jelzőt is, és megadhatja az előfizetés azonosítóját.
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

A kommunikációs szolgáltatások erőforrását a következő beállításokkal állíthatja be:

* Az erőforráscsoport
* A kommunikációs szolgáltatások erőforrásának neve
* Az a földrajzi hely, amelyhez az erőforrás társítva lesz

A következő lépésben címkéket rendelhet hozzá az erőforráshoz. Az Azure-erőforrások rendszerezéséhez címkéket is használhat. A címkékkel kapcsolatos további információkért tekintse meg az [erőforrás-címkézési dokumentációt](../../../azure-resource-manager/management/tag-resources.md) .

## <a name="manage-your-communication-services-resource"></a>A kommunikációs szolgáltatások erőforrásának kezelése

Ha címkéket szeretne hozzáadni a kommunikációs szolgáltatások erőforrásához, futtassa a következő parancsokat. Egy adott előfizetést is megcélozhat.

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

További információ a további parancsokról: [az Communication](/cli/azure/ext/communication/communication).
