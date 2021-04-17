---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 26ae372b6e431247d2038445daafcb3c997a232d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512875"
---
<!--Create a media services account -->

Az alábbi Azure CLI-parancs egy új Media Services-fiókot hoz létre. Lecserélheti a következő értékeket: (egyeznie kell a tárfiókhoz megadott értékkel), és (egyeznie kell az erőforráscsoporthoz megadott `amsaccount` `storageaccountforams` `amsResourceGroup` értékkel).  

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```
