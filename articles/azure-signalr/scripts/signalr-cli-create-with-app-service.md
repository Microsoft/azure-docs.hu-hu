---
title: Szignáló szolgáltatás létrehozása App Service az Azure CLI használatával
description: Használja az Azure CLI-t a Signal Service App Service használatával történő létrehozásához. Az Azure Signaler szolgáltatás összes CLI-parancsának megismerése.
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 673a3583b1ec80fb3ad61ec35a1786a59939f6e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94563324"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>SignalR szolgáltatási erőforrás létrehozása App Service-szel

Ez a példaszkript létrehoz egy új Azure SignalR szolgáltatási erőforrást, amellyel valós idejű tartalomfrissítések küldhetők le az ügyfeleknek. A szkript emellett hozzáad egy új webalkalmazást és App Service-csomagot is a SignalR szolgáltatási erőforrást használó ASP.NET Core-webalkalmazás futtatására. A webalkalmazás egy *AzureSignalRConnectionString* nevű alkalmazásbeállítással lesz konfigurálva az új SignalR szolgáltatási erőforráshoz való csatlakozáshoz.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a szkript az Azure CLI *signalr* bővítményét használja. A példaszkript használata előtt a következő parancsot végrehajtva telepítse a *signalr* bővítményt az Azure CLI-hez:

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate unique names for the SignalR service, resource group, 
# app service, and app service plan
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"
myWebAppName=SignalRTestWebApp$randomNum
myAppSvcPlanName=$myAppSvcName"Plan"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Create an App Service plan.
az appservice plan create --name $myAppSvcPlanName --resource-group $myResourceGroupName --sku FREE

# Create the Web App
az webapp create --name $myWebAppName --resource-group $myResourceGroupName --plan $myAppSvcPlanName  

# Get the SignalR primary connection string
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "AzureSignalRConnectionString=$primaryConnectionString"
```

Jegyezze fel az új erőforráscsoporthoz létrehozott tényleges nevet. Ez a kimenetben látható. Ezt az erőforráscsoport-nevet fogja használni, amikor törölni szeretné a csoport összes erőforrását.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik. Ez a szkript a következő parancsokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Létrehoz egy Azure SignalR szolgáltatási erőforrást. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Felsorolja az alkalmazás által a tartalomfrissítések SignalRrel történő valós idejű leküldése során használt kulcsokat. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Létrehoz egy webalkalmazások futtatására szolgáló Azure App Service-csomagot. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Létrehoz egy Azure-webalkalmazást az App Service futtatási csomaggal. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Hozzáad egy új alkalmazásbeállítást a webalkalmazáshoz. Ez az alkalmazásbeállítás a SignalR kapcsolati sztring tárolására szolgál. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure SignalR szolgáltatási CLI-példaszkripteket az [Azure SignalR szolgáltatás dokumentációjában](../signalr-reference-cli.md) találhat.
