---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 520a32ed44951a711dcb1d0975fb452829530c4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244478"
---
[!INCLUDE [resource group intro text](resource-group.md)]

A Cloud Shell hozzon létre egy erőforráscsoportot a [`az group create`](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *Nyugat-Európa* helyen. Az **Ingyenes** szintű App Service-t támogató összes hely megtekintéséhez futtassa az [`az appservice list-locations --sku FREE`](/cli/azure/appservice) parancsot.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Az erőforráscsoportot és az erőforrásokat általában a közelében található régiókban hozhatja létre. 

A parancs befejeződésekor a JSON-kimenet megjeleníti az erőforráscsoport tulajdonságait.