---
title: Function-alkalmazás létrehozása prémium csomagból – Azure CLI
description: Function-alkalmazás létrehozása az Azure-ban skálázható prémium csomaggal az Azure CLI használatával
ms.service: azure-functions
ms.topic: sample
ms.date: 11/23/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 714dd8d886f567bcdf07c93803662ef4b2c635f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94565162"
---
# <a name="create-a-function-app-in-a-premium-plan---azure-cli"></a>Function-alkalmazás létrehozása prémium csomagból – Azure CLI

Ez az Azure Functions-példaszkript egy függvényalkalmazást hoz létre, amely az Ön függvényeinek tárolójaként szolgál. A létrehozott Function alkalmazás [méretezhető prémium csomagot](../functions-premium-plan.md)használ.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a szkript egy [Prémium csomag](../functions-premium-plan.md)használatával hoz létre egy Function alkalmazást.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-premium-plan/create-function-app-premium-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik. Ez a szkript a következő parancsokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy Azure Storage-fiókot. |
| [az functionapp Plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) | Egy prémium szintű csomagot hoz létre egy [adott SKU](../functions-premium-plan.md#available-instance-skus)-ban. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Létrehoz egy függvényalkalmazást az App Service-csomagban. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
