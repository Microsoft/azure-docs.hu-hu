---
title: Felhők és régiók a Azure Media Services v3 számára
description: Ez a cikk a végpontokhoz és a-kódokhoz használt URL-címekről nyújt útmutatást a régiók számára.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: 74f30ebb766ea34603c0a30455837c47222967c0
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954327"
---
# <a name="regional-code-names-and-endpoints"></a>Regionális kódnevek és végpontok

### <a name="region-code-name"></a>Régiókód neve

Ha a **Location** paramétert egy parancs vagy kérelem használja, meg kell adnia a területi kód nevét a **hely** értékeként. Ha szeretné lekérni annak a régiónak a nevét, amelyhez a fiókja van, és a hívást át kell irányítani, a következő sort futtathatja az Azure CLI-ben.

```azurecli-interactive
az account list-locations
```

Miután futtatta a fenti sort, megjelenik az összes Azure-régió listája. Navigáljon ahhoz az Azure-régióhoz, amelynek a *DisplayName* a nevét keresi, és használja a *Name* értékét a **Location** paraméterhez.

Az USA 2. nyugati régiójában (az alábbi ábrán látható) például az "westus2" kifejezést fogja használni a **Location** paraméterhez.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Végpontok  

A következő végpontokat fontos tudni, hogy mikor csatlakozhat Media Services-fiókokhoz a különböző nemzeti Azure-felhőkből.

### <a name="global-azure"></a>Globális Azure

| Szolgáltatás | Végpont |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Hitelesítés | `https://login.microsoftonline.com/` |
| Jogkivonat célközönsége | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Szolgáltatás | Végpont |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Hitelesítés | `https://login.microsoftonline.us/` |
| Jogkivonat célközönsége | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure Germany

> [!NOTE]
> Az Azure Germany-végpontok csak a németországi szuverén felhőkre érvényesek.

| Szolgáltatás | Végpont |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Hitelesítés | `https://login.microsoftonline.de/` |
| Jogkivonat célközönsége | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

| Szolgáltatás | Végpont |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Hitelesítés | `https://login.chinacloudapi.cn/` |
| Jogkivonat célközönsége |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Lásd még

* [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/)
* [Regionális kódnevek és végpontok](azure-regions-code-names.md)
* [Azure földrajzi területek](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure-beli helyszínek](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Következő lépések

[Media Services v3 – áttekintés](media-services-overview.md)
