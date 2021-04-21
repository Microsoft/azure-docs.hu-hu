---
title: 'CLI: Alkalmazás üzembe helyezése a GitHubról'
description: Megtudhatja, hogyan automatizálhatja az alkalmazás üzembe helyezését és felügyeletét az Azure CLI App Service használatával. Ez a minta bemutatja, hogyan helyezhet üzembe egy alkalmazást a GitHubról.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 655c98e5249b66b114dfbe1d88cf951001d07af2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787867"
---
# <a name="create-an-app-service-app-with-deployment-from-github-using-azure-cli"></a>Alkalmazásalkalmazás App Service GitHubról az Azure CLI használatával való üzembe helyezéssel

Ez a példaszkprogram létrehoz egy alkalmazást a App Service kapcsolódó erőforrásokkal együtt. Ezután üzembe helyezheti az alkalmazáskódot egy nyilvános GitHub-adattárból (folyamatos üzembe helyezés nélkül). A GitHub folyamatos üzembe helyezéssel való üzembe helyezéséről lásd: Alkalmazás létrehozása folyamatos üzembe helyezéssel [a GitHubról.](cli-continuous-deployment-github.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ehhez az oktatóanyaghoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "Create an app with deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése 

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Létrehoz egy App Service alkalmazást. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Társít egy App Service egy Git- vagy Mercurial-adattárhoz. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
