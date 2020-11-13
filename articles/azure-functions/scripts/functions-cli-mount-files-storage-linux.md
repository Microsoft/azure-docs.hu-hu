---
title: Fájlmegosztás csatlakoztatása egy Python-függvény alkalmazásához – Azure CLI
description: Hozzon létre egy kiszolgáló nélküli Python-függvény alkalmazást, és csatlakoztassa egy meglévő fájlmegosztást az Azure CLI használatával.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: bdcaeaca7c063f0532167077bba63f7e52a3d491
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565058"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Fájlmegosztás csatlakoztatása egy Python-függvény alkalmazásához az Azure CLI használatával

Ez a Azure Functions minta parancsfájl létrehoz egy függvény alkalmazást, és létrehoz egy megosztást a Azure Filesban. Csatolja a megosztást, hogy az adatok a függvények számára is elérhetők legyenek.  

>[!NOTE]
>A létrehozott Function alkalmazás a Python 3,7-es verzióján fut. A Azure Functions [a Python 3,6-es és 3,8-es verzióját is támogatja](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy Azure Function-alkalmazást a használati [terv](../functions-scale.md#consumption-plan)használatával.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik. Ez a szkript a következő parancsokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy Azure Storage-fiókot. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Létrehoz egy függvényalkalmazást. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Létrehoz egy Azure Files megosztást a Storage-fiókban. | 
| [az Storage Directory Create](/cli/azure/storage/directory#az-storage-directory-create) | Létrehoz egy könyvtárat a megosztásban. |
| [az WebApp config Storage-Account Add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | A megosztás csatlakoztatása a Function alkalmazáshoz. |
| [az WebApp config Storage-Account List](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Megjeleníti a Function alkalmazáshoz csatlakoztatott fájlmegosztást. | 

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
