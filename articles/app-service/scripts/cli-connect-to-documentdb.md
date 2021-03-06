---
title: 'CLI: Alkalmazás csatlakoztatása Cosmos DB'
description: Megtudhatja, hogyan automatizálhatja a saját alkalmazás üzembe helyezését és felügyeletét az Azure CLI App Service használatával. Ez a példa bemutatja, hogyan csatlakoztathat egy alkalmazást a MongoDB-hez (Cosmos DB).
author: msangapu-msft
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: f76f036e2101fb1dbb4483ba417f5cf10f3e37f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782387"
---
# <a name="connect-an-app-service-app-to-cosmos-db-using-cli"></a>Alkalmazás App Service csatlakoztatása Cosmos DB CLI használatával

Ez a példaszk Azure Cosmos DB fiókot hoz létre a mongoDB-hez Azure Cosmos DB API és egy App Service használatával. Ezután az alkalmazásbeállítások használatával egy MongoDB kapcsolati sztringet kapcsolódik a webalkalmazáshoz.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy erőforráscsoport, App Service alkalmazás, Cosmos DB és az összes kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Létrehoz egy App Service alkalmazást. |
| [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create) | Létrehoz egy Cosmos DB-fiókot. |
| [`az cosmosdb list-connection-strings`](/cli/azure/cosmosdb#az_cosmosdb_list_connection_strings) | Listázza a megadott Cosmos DB-fiókhoz tartozó kapcsolati sztringekat. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Létrehoz vagy frissíti egy alkalmazásbeállítást egy App Service alkalmazáshoz. Az alkalmazásbeállítások környezeti változókként érhetők el az alkalmazás számára. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
