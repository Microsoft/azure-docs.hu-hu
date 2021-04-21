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
ms.openlocfilehash: cc44780bd9b42e00ecfb3d140486fec87c767a76
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767247"
---
[!INCLUDE [resource group intro text](resource-group.md)]

A Cloud Shell hozzon létre egy erőforráscsoportot az [`az group create`](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *Nyugat-Európa* helyen. A Linuxon futó, **Alapszintű** App Service-t támogató összes hely megtekintéséhez futtassa az [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice#az_appservice_list_locations) parancsot.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Az erőforráscsoportot és az erőforrásokat általában a közelében található régiókban hozhatja létre. 

A parancs befejeződésekor a JSON-kimenet megjeleníti az erőforráscsoport tulajdonságait.