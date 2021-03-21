---
title: Azure CLI-parancsfájl minta – importálás alkalmazás-konfigurációs tárolóba
titleSuffix: Azure App Configuration
description: Az Azure CLI parancsfájl-importálási konfigurációjának használata az Azure-alkalmazások konfigurációjához
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: f7d68024dd86a1eb2a0311cecf607d41a7067deb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173568"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importálás Azure-alkalmazás konfigurációs tárolójába

Ez a minta parancsfájl a kulcs-érték beállításokat egy Azure-alkalmazás konfigurációs tárolójába importálja.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --format json --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy alkalmazás-konfigurációs tárolóba való importáláshoz. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az appconfig kV import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importálás az alkalmazás konfigurációs tárolójának erőforrásaiba. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információkért tekintse meg az [Azure CLI dokumentációját](/cli/azure).

Az alkalmazások konfigurációjának további parancssori felületi mintái az [Azure app CONFIGURATION CLI-mintákban](../cli-samples.md)találhatók.
