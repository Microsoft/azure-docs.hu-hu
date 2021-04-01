---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: cURL
ms.openlocfilehash: ca7f0dab2302386a9cb7d21ebb3224d24afe5e19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88608802"
---
<!--Create a media services asset cURL-->

A következő Azure cURL-parancs létrehoz egy új Media Services eszközt. Cserélje le az értékeket `subscriptionID` , a `resourceGroup` és `amsAccountName` a értékeket a jelenleg használatban lévő értékekre. Adja meg az eszköz nevét az itt beállítottak szerint `assetName` .

```cURL
curl -X PUT 'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' -H 'Accept: application/json' -H 'Content-Type: application/json' -d '{"properties": {"description": "",}}'
```
