---
title: Webalkalmazás létrehozása SignalR Service GitHub-hitelesítéssel
description: Azure CLI-példaszkript – SignalR szolgáltatást és GitHub-hitelesítést használó webalkalmazás létrehozása
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/22/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c46ac350c1bf7f7593f334a9a7fa5df1af7b19d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787322"
---
# <a name="create-a-web-app-that-uses-signalr-service-and-github-authentication"></a>SignalR szolgáltatást és GitHub-hitelesítést használó webalkalmazás létrehozása

Ez a példaszkript létrehoz egy új Azure SignalR szolgáltatási erőforrást, amellyel valós idejű tartalomfrissítések küldhetők le az ügyfeleknek. A szkript emellett hozzáad egy új webalkalmazást és App Service-csomagot is a SignalR szolgáltatási erőforrást használó ASP.NET Core-webalkalmazás futtatására. A webalkalmazás úgy van konfigurálva, hogy csatlakozzon az új SignalR szolgáltatási erőforráshoz, és [GitHub-hitelesítést](https://developer.github.com/v3/guides/basics-of-authentication/) használjon a hitelesítéshez. A webalkalmazás úgy van konfigurálva, hogy egy helyi Git-adattárat használjon üzembehelyezési forrásként.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="sample-script"></a>Példaszkript

Ez a szkript az Azure CLI *signalr* bővítményét használja. A példaszkript használata előtt a következő parancsot végrehajtva telepítse a *signalr* bővítményt az Azure CLI-hez:

```azurecli-interactive
#!/bin/bash

#========================================================================
#=== Update these values based on your desired deployment username    ===
#=== and password.                                                    ===
#========================================================================
deploymentUser=<Replace with your desired username>
deploymentUserPassword=<Replace with your desired password>

#========================================================================
#=== Update these values based on your GitHub OAuth App registration. ===
#========================================================================
GitHubClientId=<Replace with your GitHub OAuth app Client ID>
GitHubClientSecret=<Replace with your GitHub OAuth app Client Secret>


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
  --settings "Azure:SignalR:ConnectionString=$primaryConnectionString" 

#Add app settings to use with GitHub authentication
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "GitHubClientId=$GitHubClientId" 
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "GitHubClientSecret=$GitHubClientSecret" 

# Add the desired deployment user name and password
az webapp deployment user set --user-name $deploymentUser --password $deploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $myWebAppName --resource-group $myResourceGroupName \
  --query [url] -o tsv
```

Jegyezze fel az új erőforráscsoporthoz létrehozott tényleges nevet. Ez a kimenetben látható. Ezt az erőforráscsoport-nevet fogja használni, amikor törölni szeretné a csoport összes erőforrását.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik. Ez a szkript a következő parancsokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az signalr create](/cli/azure/signalr#az_signalr_create) | Létrehoz egy Azure SignalR szolgáltatási erőforrást. |
| [az signalr key list](/cli/azure/signalr/key#az_signalr_key_list) | Felsorolja az alkalmazás által a tartalomfrissítések SignalRrel történő valós idejű leküldése során használt kulcsokat. |
| [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) | Létrehoz egy webalkalmazások futtatására szolgáló Azure App Service-csomagot. |
| [az webapp create](/cli/azure/webapp#az_webapp_create) | Létrehoz egy Azure-webalkalmazást az App Service futtatási csomaggal. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Új alkalmazásbeállításokat ad a webalkalmazáshoz. Ezeket az alkalmazásbeállításokat használja a rendszer a SignalR kapcsolati sztringje és a GitHub OAuth titkos alkalmazáskulcsok tárolásához. |
| [az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) | Frissíti az üzembehelyezési hitelesítő adatokat. |
| [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_local_git) | Beszerez egy URL-címet egy Git-adattárvégponthoz, amely klónozáshoz és leküldéshez használható webalkalmazás üzembe helyezésekor. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure SignalR szolgáltatási CLI-példaszkripteket az [Azure SignalR szolgáltatás dokumentációjában](../signalr-reference-cli.md) találhat.
