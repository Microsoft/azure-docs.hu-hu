---
title: Fájlmegosztás csatlakoztatása Python-függvényalkalmazáshoz – Azure CLI
description: Kiszolgáló nélküli Python-függvényalkalmazás létrehozása és meglévő fájlmegosztás csatlakoztatása az Azure CLI használatával.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d0037cea24b1989c4f7a4d2ddd6bf3f8f7e812b3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762280"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Fájlmegosztás csatlakoztatása Python-függvényalkalmazáshoz az Azure CLI használatával

Ez Azure Functions szkriptminta létrehoz egy függvényalkalmazást, és létrehoz egy megosztást a Azure Files. Csatlakoztatja a megosztást, hogy a függvények hozzáférnek az adatokhoz.  

>[!NOTE]
>A létrehozott függvényalkalmazás a Python 3.7-es verzióján fut. Azure Functions a [Python 3.6-os és 3.8-as verzióit is támogatja.](../functions-reference-python.md#python-version)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van. 

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy függvényalkalmazást a Azure Functions használat [alapján.](../consumption-plan.md)

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create a function app on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik. Ez a szkript a következő parancsokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Létrehoz egy Azure Storage-fiókot. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Létrehoz egy függvényalkalmazást. |
| [az storage share create](/cli/azure/storage/share#az_storage_share_create) | Létrehoz egy Azure Files-megosztást a tárfiókban. | 
| [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create) | Létrehoz egy könyvtárat a megosztásban. |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) | Csatlakoztatja a megosztást a függvényalkalmazáshoz. |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_list) | Megjeleníti a függvényalkalmazáshoz csatlakoztatott fájlmegosztásokat. | 

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
