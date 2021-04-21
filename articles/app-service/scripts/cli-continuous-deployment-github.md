---
title: 'CLI: Folyamatos üzembe helyezés a GitHubról'
description: Megtudhatja, hogyan automatizálhatja a saját alkalmazás üzembe helyezését és felügyeletét az Azure CLI App Service használatával. Ez a minta bemutatja, hogyan hozhat létre alkalmazást a CI/CD használatával a GitHubról.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/02/2019
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: b67d05951a4fc1a0ed2d625b2406ca5d0bc0fe34
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762298"
---
# <a name="create-an-app-service-app-with-continuous-deployment-from-github-using-cli"></a>Alkalmazás létrehozása App Service GitHubról a CLI használatával való folyamatos üzembe helyezéssel

Ez a példaszk szkript létrehoz egy alkalmazást a App Service kapcsolódó erőforrásokkal együtt, majd beállítja a folyamatos üzembe helyezést egy GitHub-adattárból. A GitHub folyamatos üzembe helyezés nélküli üzembe helyezése: Alkalmazás létrehozása és kód [üzembe helyezése a GitHubról.](cli-deploy-github.md) A példához a következők szükségesek:

* Egy GitHub-adattár az alkalmazáskóddal, amelyhez rendszergazdai jogosultság szükséges. Az automatikus buildek lekért struktúra a Prepare your repository (Az adattár [előkészítése) tábla alapján struktúratálja az adattárat.](../deploy-continuous-deployment.md#prepare-your-repository)
* Egy [személyes hozzáférési jogkivonat (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) a GitHub-fiókjához.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "Create an app with continuous deployment from GitHub")]

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
