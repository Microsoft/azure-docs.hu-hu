---
title: 'CLI: ASP.NET Core-alkalmazás létrehozása a Dockerből'
description: Megtudhatja, hogyan automatizálhatja a saját alkalmazás üzembe helyezését és felügyeletét az Azure CLI App Service használatával. Ez a minta bemutatja, hogyan hozhat létre ASP.NET Core-alkalmazást a Docker Hub.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: devx-track-dotnet, mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: d7f09d8e4b00a079fb012939e88ff585cbc88f4d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787754"
---
# <a name="create-an-aspnet-core-app-in-a-docker-container-from-docker-hub-using-azure-cli"></a>ASP.NET Core-alkalmazás létrehozása Docker-tárolóban Docker Hub Azure CLI használatával

Ez a példaszkprogram létrehoz egy erőforráscsoportot, egy Linux-App Service-tervet és egy alkalmazást. Ezután üzembe helyez ASP.NET Core-alapú alkalmazást egy Docker-tároló használatával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-docker/deploy-linux-docker.sh?highlight=6 "Linux Docker")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy erőforráscsoport, App Service alkalmazás és az összes kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Létrehoz egy App Service alkalmazást. |
| [`az webapp config container set`](/cli/azure/webapp/config/container#az_webapp_config_container_set) | Beállítja a Docker-tárolót a App Service számára. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
