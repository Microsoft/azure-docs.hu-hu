---
title: Function-alkalmazás létrehozása Azure Cosmos DB-Azure CLI-vel
description: Azure CLI-példaszkript – Egy Azure Cosmos DB-hez kapcsolódó Azure-függvény létrehozása
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9ec4d3cb9d47608aa98075ba98aacfde51f341cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934424"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Egy Azure Cosmos DB-hez kapcsolódó Azure-függvény létrehozása

Ez az Azure Functions-példaszkript létrehoz egy függvényalkalmazást, amelyet hozzákapcsol egy Azure Cosmos DB-adatbázishoz. A létrehozott alkalmazásbeállítás, amely a kapcsolatot tartalmazza, egy [Cosmos DB-eseményindítóval vagy -kötéssel](../functions-bindings-cosmosdb.md) használható.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 

## <a name="sample-script"></a>Példaszkript

Ez a példa létrehoz egy Azure-függvényalkalmazást, és hozzáadja a Cosmos DB végpontját és hozzáférési kulcsát egy alkalmazásbeállításhoz.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja: A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot egy helyen |
| [az storage accounts create](/cli/azure/storage/account#az-storage-account-create) | Tárfiók létrehozása |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Egy Function-alkalmazást hoz létre a kiszolgáló nélküli [felhasználási tervben](../consumption-plan.md). |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Létrehoz egy Azure Cosmos DB-adatbázist. |
| [az cosmosdb show](/cli/azure/cosmosdb#az-cosmosdb-show)| Az adatbázis-fiókkal létesített kapcsolatok beolvasása. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys)| Lekéri az adatbázis kulcsait. |
| [az functionapp config appSettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | A függvény alkalmazásban beállíthatja a kapcsolatok karakterláncát. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.




