---
title: Azure CLI-példaszktár – Azure App Configuration létrehozása
titleSuffix: Azure App Configuration
description: Hozzon létre Azure App Configuration tárolót egy Azure CLI-példaszkret használatával. A referenciacikk a szkriptben használt parancsokra mutató hivatkozásokat tartalmaz.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: sample
ms.date: 01/24/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4a341e9772c67da7781a5cec92f286c037c55ce9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768902"
---
# <a name="create-an-azure-app-configuration-store"></a>Új Azure App Configuration létrehozása

Ez a példaszkprogram egy új példányt hoz Azure App Configuration egy új erőforráscsoportban.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ehhez az oktatóanyaghoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
#resource name must be lowercase
myAppConfigStoreName=${appConfigName,,}
myResourceGroupName=$appConfigName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure AppConfig Service resource and query the hostName
appConfigHostname=$(az appconfig create \
  --name $myAppConfigStoreName \
  --location eastus \
  --resource-group $myResourceGroupName \
  --query endpoint \
  --sku free \
  -o tsv
  )

# Get the AppConfig connection string 
appConfigConnectionString=$(az appconfig credential list \
--resource-group $myResourceGroupName \
--name $myAppConfigStoreName \
--query "[?name=='Secondary Read Only'] .connectionString" -o tsv)

# Echo the connection string for use in your application
echo "$appConfigConnectionString"
```

Jegyezze fel az új erőforráscsoporthoz létrehozott tényleges nevet. Ezt az erőforráscsoport-nevet fogja használni, amikor törölni szeretné a csoport összes erőforrását.

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy új erőforráscsoport és egy App Configuration létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az appconfig create](/cli/azure/appconfig#az_appconfig_create) | Létrehoz egy App Configuration-erőforrást. |
| [az appconfig credential list](/cli/azure/appconfig/credential#az_appconfig_credential_list) | List access keys for an App Configuration store .list.list access keys for an App Configuration store. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Configuration CLI-szkriptmintákat az Azure App Configuration [CLI-minták között talál.](../cli-samples.md)
