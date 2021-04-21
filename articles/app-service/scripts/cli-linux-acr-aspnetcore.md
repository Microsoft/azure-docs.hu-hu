---
title: 'CLI: ASP.NET Core-alkalmazás létrehozása az ACR-ről'
description: Megtudhatja, hogyan automatizálhatja a saját alkalmazás üzembe helyezését és felügyeletét az Azure CLI App Service használatával. Ez a minta bemutatja, hogyan hozhat létre Linux ASP.NET Core-alkalmazást az ACR-ről.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/13/2018
ms.author: msangapu
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 037d66b4cc94fba5a0dee2a44b6a9e316dd36f96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787740"
---
# <a name="create-an-aspnet-core-app-in-a-docker-container-in-app-service-from-azure-container-registry"></a>ASP.NET Core-alkalmazás létrehozása egy Docker-tárolóban a App Service Azure Container Registry

Ez a példaszkprogram létrehoz egy erőforráscsoportot, egy Linux-App Service-tervet és egy alkalmazást. Ezután üzembe helyez ASP.NET Core-alkalmazást az Azure Container Registryben található Docker-tárolóval.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Az Azure CLI 2.0.52-es vagy újabb verziójára lesz szüksége. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh "Linux Azure Container Registry")]

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
