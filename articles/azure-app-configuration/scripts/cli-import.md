---
title: Azure CLI-példaszktár – Importálás App Configuration tárolóba
titleSuffix: Azure App Configuration
description: Azure CLI-szkript használata – Konfiguráció importálása a Azure App Configuration
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5a80acd1261211fe3efaefa6a4b6025a321e2d65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774178"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importálás Azure App Configuration tárolóba

Ez a példaszkprogram importálja a kulcs-érték beállításokat egy Azure App Configuration tárolóba.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ehhez az oktatóanyaghoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --format json --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja a App Configuration importálására. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az appconfig kv import](/cli/azure/appconfig/kv#az_appconfig_kv_import) | Importál egy App Configuration-erőforrásba. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-ről az [Azure CLI dokumentációjában talál további információt.](/cli/azure)

További App Configuration CLI-szkriptmintákat az Azure App Configuration [CLI-minták között talál.](../cli-samples.md)
