---
title: Resource Manager-sablonok a MongoDB Azure Cosmos DB API-hoz
description: Azure Resource Manager-sablonok használatával Azure Cosmos DB API-t hozhat létre és konfigurálhat a MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 7664d48bad153b34e7557e9faaf4c8aa0d4215ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340624"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB MongoDB API-erőforrások kezelése Azure Resource Manager-sablonok használatával
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ebből a cikkből megtudhatja, hogyan telepítheti és kezelheti a MongoDB API-k, adatbázisok és gyűjtemények Azure Cosmos DB-fiókjait Azure Resource Manager sablonok használatával.

Ez a cikk példákat tartalmaz a Azure Cosmos DB API-MongoDB csak az API-típusok más típusú fiókjainak megkereséséhez lásd: a Azure Resource Manager-sablonok használata a Azure Cosmos DB API-jával a [Cassandra](templates-samples-cassandra.md), a [Gremlin](templates-samples-gremlin.md), az [SQL](templates-samples-sql.md)és a [Table](templates-samples-table.md) cikkekhez.

> [!IMPORTANT]
>
> * A fiókok neve 44 karakterből állhat, és az összes kisbetűs értékre van korlátozva.
> * Az átviteli sebesség értékeinek módosításához telepítse újra a sablont a frissített RU/s értékkel.
> * Amikor helyet ad hozzá vagy távolít el egy Azure Cosmos-fiókhoz, a többi tulajdonságot nem módosíthatja egyidejűleg. Ezeket a műveleteket külön kell elvégezni.

Az alábbi Azure Cosmos DB-erőforrások létrehozásához másolja a következő példa sablont egy új JSON-fájlba. Lehetőség van arra is, hogy hozzon létre egy JSON-fájlt, amelyet akkor használhat, ha ugyanazon erőforrás több példányát különböző nevekkel és értékekkel telepíti. A Azure Resource Manager-sablonok számos módon telepíthetők, például a [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), az [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), a [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) és a [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>Azure Cosmos-fiók a MongoDB-hez az autoscale kiépített átviteli sebességgel

Ez a sablon létrehoz egy Azure Cosmos-fiókot a MongoDB API-hoz (3,2 vagy 3,6) két olyan gyűjteménysel, amelyek az adatbázis szintjén osztoznak az automatikusan skálázható átviteli sebességgel. Ez a sablon egykattintásos üzembe helyezésre is használható az Azure Gyorsindítás sablonok katalógusból.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>Azure Cosmos-fiók a MongoDB standard kiosztott átviteli sebességgel

Ez a sablon egy Azure Cosmos-fiókot hoz létre a MongoDB API-hoz (3,2 vagy 3,6) két olyan gyűjteménysel, amelyek az adatbázis szintjén osztoznak a 400 RU/s szabvány (manuális) átviteli sebességgel. Ez a sablon egykattintásos üzembe helyezésre is használható az Azure Gyorsindítás sablonok katalógusból.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>Következő lépések

Néhány további információforrás:

* [Az Azure Resource Manager dokumentációja](../azure-resource-manager/index.yml)
* [Erőforrás-szolgáltatói séma Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Gyakori Azure Resource Manager telepítési hibák elhárítása](../azure-resource-manager/templates/common-deployment-errors.md)