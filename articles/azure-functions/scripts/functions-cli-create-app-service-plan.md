---
title: Függvényalkalmazás létrehozása egy App Service-tervben – Azure CLI
description: Azure CLI-szkriptminta – Függvényalkalmazás létrehozása egy App Service-csomagban
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 67efb5cc146b10b7e8194b7de4f6e0f0d6c08de9
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565449"
---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Függvényalkalmazás létrehozása egy App Service-csomagban

Ez az Azure Functions-példaszkript egy függvényalkalmazást hoz létre, amely az Ön függvényeinek tárolójaként szolgál. A létrehozott függvényalkalmazás egy dedikált App Service-csomagot használ, ami azt jelenti, hogy a kiszolgáló-erőforrások mindig működnek.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 

## <a name="sample-script"></a>Példaszkript

Ez a szkript egy Azure-függvényalkalmazást hoz létre egy dedikált [App Service-csomag](../functions-scale.md#app-service-plan) használatával.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik. Ez a szkript a következő parancsokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy Azure Storage-fiókot. |
| [az functionapp Plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) | Létrehoz egy prémium csomagot. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Létrehoz egy függvényalkalmazást az App Service-csomagban. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
