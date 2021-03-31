---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88602352"
---
<!-- Create a resource group -->

Az alábbi parancs használatával hozzon létre egy erőforráscsoportot. Válassza ki azt a földrajzi régiót, amelyet a rendszer a Media Services-fiókhoz tartozó média és metaadat-rekordok tárolására fog használni. Ez a régió fogja használni az adathordozó feldolgozását és továbbítását.

```azurecli
az group create --name amsResourceGroup --location westus2
```
