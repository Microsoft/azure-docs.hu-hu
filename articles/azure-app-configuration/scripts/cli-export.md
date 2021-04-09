---
title: Azure CLI-parancsfájl mintája – exportálás Azure-alkalmazás konfigurációs tárolójából
titleSuffix: Azure App Configuration
description: Az Azure CLI-szkript használata a konfiguráció exportálására az Azure-alkalmazások konfigurációjától
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: be3fac17ba99194a76b27c78040a76cce43405a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96931112"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Exportálás Azure app Configuration Store-ból

Ez a minta parancsfájl exportálja a kulcs-értékeket egy Azure-alkalmazás konfigurációs tárolójából.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja az alkalmazás-konfigurációs tárolóból való exportáláshoz. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az appconfig kV export](/cli/azure/appconfig/kv#az-appconfig-kv-export) | Exportálás az alkalmazás konfigurációs tárolójának erőforrásaiból. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

Az alkalmazások konfigurációjának további parancssori felületi mintái az [Azure app CONFIGURATION CLI-mintákban](../cli-samples.md)találhatók.
