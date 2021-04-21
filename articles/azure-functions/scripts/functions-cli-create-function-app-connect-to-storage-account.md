---
title: Függvényalkalmazás létrehozása csatlakoztatott tárolóval – Azure CLI
description: Azure CLI-példaszkript – Egy Azure Storage-hoz kapcsolódó Azure-függvény létrehozása
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 539c3a7dd95045b2e569dbb339be0e5a0c845902
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786282"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Függvényalkalmazás létrehozása tárfiók-kapcsolattal 

Ez az Azure Functions-példaszkript létrehoz egy függvényalkalmazást, amelyet hozzákapcsol egy Azure Storage-fiókhoz. A létrehozott alkalmazásbeállítás, amely a kapcsolatot tartalmazza, egy [tárolási eseményindítóval vagy kötéssel](../functions-bindings-storage-blob.md) együtt használható. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a példa létrehoz egy Azure-függvényalkalmazást, és hozzáadja a tároló kapcsolati sztringjét egy alkalmazásbeállításhoz.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot egy helyen. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Tárfiók létrehozása. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Létrehoz egy függvényalkalmazást a kiszolgáló nélküli [használatra szánt csomagban.](../consumption-plan.md) |
| [az storage account show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string) | Lekéri a fiók kapcsolati sztringjét. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) | Beállítja a kapcsolati sztringet alkalmazásbeállításként a függvényalkalmazásban. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
