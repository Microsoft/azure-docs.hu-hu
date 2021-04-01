---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 9e5b344f15a92e7ac40182f8fc7ae3ca667f63a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88608810"
---
<!--Create a media services asset REST-->

A következő Azure REST-parancs egy új Media Services eszközt hoz létre. Cserélje le az értékeket `subscriptionID` , a `resourceGroup` és `amsAccountName` a értékeket a jelenleg használatban lévő értékekre. Adja meg az eszköz nevét az itt beállítottak szerint `assetName` .

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```