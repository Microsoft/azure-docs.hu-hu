---
title: Azure CLI-példaszktár – Kulcsértékek használata az App Configuration Store-ban
titleSuffix: Azure App Configuration
description: Azure CLI-szkript használata kulcsértékek létrehozásához, megtekintéséhez, frissítéséhez és törléséhez App Configuration tárolóból
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: bc329feffbf22d63774ab0f3779d62d3ccf53adb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774122"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Kulcsértékek Azure App Configuration tárolóban

Ez a példaszk szkript a következőt mutatja be:
* Új kulcs-érték pár létrehozása
* Az összes meglévő kulcs-érték pár felsorolása
* Újonnan létrehozott kulcs értékének frissítése
* Az új kulcs-érték pár törlése

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.
## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a táblázat a mintaszk szkriptben használt parancsokat sorolja fel. 

| Parancs | Jegyzetek |
|---|---|
| [az appconfig kv set](/cli/azure/appconfig/kv#az_appconfig_kv_set) | Kulcs-érték pár létrehozása vagy frissítése. |
| [az appconfig kv list](/cli/azure/appconfig/kv#az_appconfig_kv_list) | List key-value párok egy App Configuration tárolóban. |
| [az appconfig kv delete](/cli/azure/appconfig/kv#az_appconfig_kv_delete) | Kulcs-érték pár törlése. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Configuration CLI-mintákat a cli Azure App Configuration minták [között talál.](../cli-samples.md)
