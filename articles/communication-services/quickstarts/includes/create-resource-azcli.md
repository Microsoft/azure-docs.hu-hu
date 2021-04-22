---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 69857915620eada94586754a6c934edaf0b294a9
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881357"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/dotnet/)
- Az [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?tabs=azure-cli) 

## <a name="create-azure-communication-resource"></a>Azure Communication-erőforrás létrehozása

A virtuális Azure Communication Services létrehozásához jelentkezzen [be az Azure CLI-be.](/cli/azure/authenticate-azure-cli) Ezt a terminálon keresztül, az paranccsal és a ```az login``` hitelesítő adatok megadatával használhatja. Futtassa a következő parancsot az erőforrás létrehozásához:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Ha egy adott előfizetést szeretne kiválasztani, megadhatja a jelzőt is, és ```--subscription``` megadhatja az előfizetés azonosítóját.
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

A következő Communication Services konfigurálhatja az erőforrást:

* Az erőforráscsoport
* A Communication Services neve
* Az a földrajzi hely, amelyhez az erőforrás társítva lesz

A következő lépésben címkéket rendelhet az erőforráshoz. A címkékkel rendszerezheti az Azure-erőforrásokat. A [címkékkel kapcsolatos](../../../azure-resource-manager/management/tag-resources.md) további információkért tekintse meg az erőforrás-címkézési dokumentációt.

## <a name="manage-your-communication-services-resource"></a>A Communication Services kezelése

Ha címkéket szeretne hozzáadni a Communication Services erőforráshoz, futtassa a következő parancsokat. Egy adott előfizetést is megcélhat.

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

További parancsokkal kapcsolatos információkért lásd: [az communication](/cli/azure/communication).
