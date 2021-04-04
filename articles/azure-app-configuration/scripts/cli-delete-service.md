---
title: Azure CLI-parancsfájl minta – Azure-alkalmazás konfigurációs tárolójának törlése
titleSuffix: Azure App Configuration
description: Azure-alkalmazás konfigurációs tárolójának törlése egy minta Azure CLI-parancsfájl használatával. Lásd a parancsfájlban használt parancsokra mutató hivatkozásokat.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49d6a85faa55de5dbf50377998dbe2fc829d9f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929786"
---
# <a name="delete-an-azure-app-configuration-store"></a>Azure-alkalmazás konfigurációs tárolójának törlése

Ez a minta parancsfájl törli az Azure-alkalmazások konfigurációjának egy példányát.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#/bin/bash

# Delete an App Configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja az alkalmazás konfigurációs tárolójának törléséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az appconfig delete](/cli/azure/appconfig#az-appconfig-delete) | Egy alkalmazás-konfigurációs tároló erőforrásának törlése. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

Az alkalmazások konfigurációjának további parancssori felületi mintái az [Azure app CONFIGURATION CLI-mintákban](../cli-samples.md)találhatók.
