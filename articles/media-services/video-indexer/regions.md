---
title: Azok a régiók, amelyekben Video Indexer elérhető – Azure
titleSuffix: Azure Media Services
description: Ez a cikk a Azure Media Services Video Indexer elérhető Azure-régiókkal kapcsolatos információkat tárgyalja.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 6ebdb22f50efbefc695f9752c6e6fc333571828c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530937"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Azure-régiók, amelyekben Video Indexer létezik

Video Indexer API-k olyan **Location** paramétert tartalmaznak, amelyet arra az Azure-régióra kell beállítani, amelyre a hívást át kell irányítani. Ennek egy olyan [Azure-régiónak kell lennie, amelyben video Indexer érhető el](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Helyek

A `location` paraméternek meg kell adni az Azure-régió kódjának nevét az értékeként. Ha az előnézeti módban Video Indexert használ, a értéket kell megtennie `"trial"` értékként. `trial` a paraméter alapértelmezett értéke `location` . Ellenkező esetben a fiókhoz tartozó Azure-régió kódjának beszerzéséhez és a hívás átirányításához használja a Azure Portal, vagy futtasson egy [Azure CLI](/cli/azure) -parancsot.

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyre.
1. Válassza ki a **felhasználói fiókok** elemet az oldal jobb felső sarkában.
1. Keresse meg a fiókja helyét a jobb felső sarokban.  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="Hely":::
    
###  <a name="cli-command"></a>CLI-parancs

```azurecli-interactive
az account list-locations
```

Miután futtatta a fenti sort, megjelenik az összes Azure-régió listája. Navigáljon ahhoz az Azure-régióhoz, amelynek a *DisplayName* a nevét keresi, és használja a *Name* értékét a **Location** paraméterhez.

Az USA 2. nyugati régiójában (az alábbi ábrán látható) például az "westus2" kifejezést fogja használni a **Location** paraméterhez.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Következő lépések

- [Nyelvi modell testreszabása API-k használatával](customize-language-model-with-api.md)
- [A Brands modell testreszabása API-k használatával](customize-brands-model-with-api.md)
- [Személy modell testreszabása API-k használatával](customize-person-model-with-api.md)
