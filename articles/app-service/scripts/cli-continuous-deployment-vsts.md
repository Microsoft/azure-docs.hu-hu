---
title: Folyamatos üzembe helyezés Azure-adattárakból
description: Megtudhatja, hogyan automatizálhatja az alkalmazás üzembe helyezését és felügyeletét az Azure CLI App Service használatával. Ez a minta bemutatja, hogyan állíthatja be a CI/CD-t az Azure-adattárakból.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: cfc9ffff21db92eef4a3a544cb042394078a4e6d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788024"
---
# <a name="create-an-app-service-app-with-continuous-deployment-using-azure-cli"></a>Folyamatos üzembe helyezést App Service alkalmazás létrehozása az Azure CLI használatával

Ez a példaszkvóta létrehoz egy alkalmazást a App Service kapcsolódó erőforrásokkal együtt, majd beállítja a folyamatos üzembe helyezést egy Azure DevOps-adattárból. A példához a következők szükségesek:

* Egy Azure DevOps-adattár alkalmazáskóddal, amelyhez rendszergazdai jogosultsággal rendelkezik.
* Egy [személyes hozzáférési jogkivonat (PAT)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) az Azure DevOps-szervezethez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ehhez az oktatóanyaghoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Create an app with continuous deployment from Azure DevOps")]

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
