---
title: Azure CLI parancsfájl-minta – a Key-Values használata az alkalmazás konfigurációs tárolójában
titleSuffix: Azure App Configuration
description: Alapértékek létrehozása, megtekintése, frissítése és törlése az alkalmazás konfigurációs tárolójából az Azure CLI-parancsfájllal
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 54ad9f389070e1d5d280517a2f6c41e9a0f8f33e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929990"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>A Key-Values használata egy Azure-alkalmazás konfigurációs tárolójában

Ez a példa a következőket mutatja be:
* Új kulcs-érték párok létrehozása
* Az összes létező kulcs-érték pár listázása
* Újonnan létrehozott kulcs értékének frissítése
* Az új kulcs-érték pár törlése

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.
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

Ez a táblázat a minta parancsfájlban használt parancsokat sorolja fel. 

| Parancs | Jegyzetek |
|---|---|
| [az appconfig kV set](/cli/azure/appconfig/kv#az-appconfig-kv-set) | Kulcs-érték párok létrehozása vagy frissítése. |
| [az appconfig kV List](/cli/azure/appconfig/kv#az-appconfig-kv-list) | Egy alkalmazás-konfigurációs tárolóban található kulcs-érték párok listázása. |
| [az appconfig kV delete](/cli/azure/appconfig/kv#az-appconfig-kv-delete) | Kulcs-érték párok törlése. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

Az alkalmazások konfigurációjának további parancssori felületi mintái az [Azure app CONFIGURATION CLI-mintákban](../cli-samples.md)találhatók.
