---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-hoz – a Cloud Shell és a IoT-bővítmény beállítása
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: d4d9efd99a60c93dbfef2d6f45971781d71e83fb
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105059"
---
Az Azure Digital Twins használatának megkezdéséhez nyissa meg [Azure Cloud Shell](https://shell.azure.com) ablakban az első teendő, hogy jelentkezzen be, és állítsa be a rendszerhéj környezetét az előfizetéséhez ehhez a munkamenethez. Futtassa ezeket a parancsokat a Cloud Shellban:

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> A fenti parancsban szereplő azonosító helyett az előfizetés nevét is használhatja. 

Ha első alkalommal használta ezt az előfizetést az Azure digitális Twins szolgáltatásban, futtassa ezt a parancsot az Azure Digital Twins-névtérben való regisztráláshoz. (Ha nem biztos abban, hogy a múltban is elvégezte a futtatást, akkor is.)

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

Ezután adja hozzá az [**Azure CLI-hez készült Microsoft Azure IoT-bővítményt**](/cli/azure/service-page/azure%20iot) a Cloud Shellhoz, hogy engedélyezze a parancsokat az Azure Digital Twins és más IoT-szolgáltatások használatával való interakcióhoz. A parancs futtatásával ellenőrizze, hogy a bővítmény legújabb verziója van-e:

```azurecli-interactive
az extension add --upgrade -n azure-iot
```

Most már készen áll az Azure Digital Twins-vel való együttműködésre a Cloud Shell.

Ezt bármikor ellenőrizheti, ha `az dt -h` az elérhető legfelső szintű Azure digitális Twins-parancsok listáját szeretné megtekinteni.