---
title: Resource Manager-sablonok a Azure Cosmos DB Table API
description: Azure Cosmos DB-Table API létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: e8fb47cc54d520d44085c8d007a4dfe92972d1d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340583"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB Table API erőforrások kezelése Azure Resource Manager sablonok használatával
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Ebből a cikkből megtudhatja, hogyan telepítheti és kezelheti a Azure Cosmos DB-fiókjait, adatbázisait és tárolóit Azure Resource Manager sablonok használatával.

Ez a cikk csak Table API fiókokra mutat példákat más API-típusú fiókokra vonatkozó példák megkereséséhez lásd: Azure Resource Manager-sablonok használata a Azure Cosmos DB API-jával a [Cassandra](./templates-samples-cassandra.md), a [Gremlin](./templates-samples-gremlin.md), a [MongoDB](./templates-samples-mongodb.md)és az [SQL](./manage-with-templates.md) -cikkek számára.

> [!IMPORTANT]
>
> * A fiókok neve 44 karakterből állhat, és az összes kisbetűs értékre van korlátozva.
> * Az átviteli sebesség értékeinek módosításához telepítse újra a sablont a frissített RU/s értékkel.
> * Amikor helyet ad hozzá vagy távolít el egy Azure Cosmos-fiókhoz, a többi tulajdonságot nem módosíthatja egyidejűleg. Ezeket a műveleteket külön kell elvégezni.

Az alábbi Azure Cosmos DB-erőforrások létrehozásához másolja a következő példa sablont egy új JSON-fájlba. Lehetőség van arra is, hogy hozzon létre egy JSON-fájlt, amelyet akkor használhat, ha ugyanazon erőforrás több példányát különböző nevekkel és értékekkel telepíti. A Azure Resource Manager-sablonok számos módon telepíthetők, például a [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), az [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), a [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) és a [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

> [!TIP]
> Ha Table API használatával szeretné engedélyezni a megosztott átviteli sebességet, engedélyezze a fiók szintű átviteli sebességet a Azure Portal.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>Azure Cosmos-fiók az autoscale átviteli sebességgel rendelkező táblázathoz

Ez a sablon egy Azure Cosmos-fiókot hoz létre Table API egy, az automatikusan méretezhető átviteli sebességgel rendelkező táblával. Ez a sablon egykattintásos üzembe helyezésre is használható az Azure Gyorsindítás sablonok katalógusból.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>Azure Cosmos-fiók a szabványos kiosztott átviteli sebességgel rendelkező táblázathoz

Ez a sablon egy Azure Cosmos-fiókot hoz létre a Table APIhoz, és egy táblázatot a standard átviteli sebességgel. Ez a sablon egykattintásos üzembe helyezésre is használható az Azure Gyorsindítás sablonok katalógusból.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

## <a name="next-steps"></a>Következő lépések

Néhány további információforrás:

* [Az Azure Resource Manager dokumentációja](../azure-resource-manager/index.yml)
* [Erőforrás-szolgáltatói séma Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Gyakori Azure Resource Manager telepítési hibák elhárítása](../azure-resource-manager/templates/common-deployment-errors.md)