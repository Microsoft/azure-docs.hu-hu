---
title: Függvényalkalmazás létrehozása DevOps üzembe helyezéssel – Azure CLI
description: Függvényalkalmazás létrehozása és függvénykód üzembe helyezése az Azure DevOpsból
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f31c6a76412939d179cdd282e5e643ab7e8531b5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786220"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Azure DevOpsból üzembe helyezett függvény létrehozása az Azure-ban

Ez a témakör bemutatja, hogyan használhatja [a](https://azure.microsoft.com/solutions/serverless/) Azure Functions kiszolgáló nélküli függvényalkalmazás létrehozásához a [használat alapján.](../consumption-plan.md) A függvényalkalmazás, amely a függvények tárolója, folyamatosan üzembe helyezhető egy Azure DevOps-adattárból. 

A témakör teljesítéséhez a következőkre lesz szüksége:

* Egy Azure DevOps-adattárra, amely tartalmazza a függvényalkalmazás projektjét, és amelyhez Ön rendszergazdai engedélyekkel rendelkezik.
* Egy [személyes hozzáférési jogkivonatra (PAT)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) az Azure DevOps-adattár eléréséhez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van. 

## <a name="sample-script"></a>Példaszkript

Ez a példa létrehoz egy Azure-függvényalkalmazást, és üzembe helyezi a függvény kódját az Azure DevOpsból.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, tárfiók, függvényalkalmazás és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Létrehozza a tárfiókot, amely a függvényalkalmazáshoz szükséges. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Létrehoz egy függvényalkalmazást a kiszolgáló nélküli [használatra szánt csomagban.](../consumption-plan.md) |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config) | Társít egy függvényalkalmazást egy Git- vagy Mercurial-adattárhoz. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
