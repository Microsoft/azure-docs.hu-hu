---
title: Függvényalkalmazás létrehozása a Azure Cosmos DB – Azure CLI
description: Azure CLI-példaszkript – Egy Azure Cosmos DB-hez kapcsolódó Azure-függvény létrehozása
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 261ede1493b93ccd3cfed19125bbabc3be871698
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786290"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Egy Azure Cosmos DB-hez kapcsolódó Azure-függvény létrehozása

Ez az Azure Functions-példaszkript létrehoz egy függvényalkalmazást, amelyet hozzákapcsol egy Azure Cosmos DB-adatbázishoz. A létrehozott alkalmazásbeállítás, amely a kapcsolatot tartalmazza, egy [Cosmos DB-eseményindítóval vagy -kötéssel](../functions-bindings-cosmosdb.md) használható.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van. 

## <a name="sample-script"></a>Példaszkript

Ez a példa létrehoz egy Azure-függvényalkalmazást, és hozzáadja a Cosmos DB végpontját és hozzáférési kulcsát egy alkalmazásbeállításhoz.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja: A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot egy helyen |
| [az storage accounts create](/cli/azure/storage/account#az_storage_account_create) | Tárfiók létrehozása |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Létrehoz egy függvényalkalmazást a kiszolgáló nélküli [használatra szánt csomagban.](../consumption-plan.md) |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Létrehoz egy Azure Cosmos DB-adatbázist. |
| [az cosmosdb show](/cli/azure/cosmosdb#az_cosmosdb_show)| Lekérte az adatbázisfiók kapcsolatát. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az_cosmosdb_list_keys)| Lekérte az adatbázis kulcsait. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) | Beállítja a kapcsolati sztringet alkalmazásbeállításként a függvényalkalmazásban. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
