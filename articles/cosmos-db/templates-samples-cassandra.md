---
title: Resource Manager-sablonok a Azure Cosmos DB Cassandra API
description: Azure Cosmos DB-Cassandra API létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: eb746e8105717883b029586db2bfce8e31290e93
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340668"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB Cassandra API erőforrások kezelése Azure Resource Manager sablonok használatával
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe és kezelhet Azure Cosmos DB-fiókokat,-webhelyeket és-táblákat Azure Resource Manager-sablonok segítségével.

Ez a cikk csak a Cassandra API fiókokra mutat példákat más API-típusú fiókokra vonatkozó példák megkereséséhez lásd: Azure Resource Manager-sablonok használata az Azure Cosmos DB API-val az [SQL](templates-samples-sql.md), a [Gremlin](templates-samples-gremlin.md), a [MongoDB](templates-samples-mongodb.md)és a [Table](templates-samples-table.md) cikkekhez.

> [!IMPORTANT]
>
> * A fiókok neve 44 karakterből állhat, és az összes kisbetűs értékre van korlátozva.
> * Az átviteli sebesség értékeinek módosításához telepítse újra a sablont a frissített RU/s értékkel.
> * Amikor helyet ad hozzá vagy távolít el egy Azure Cosmos-fiókhoz, a többi tulajdonságot nem módosíthatja egyidejűleg. Ezeket a műveleteket külön kell elvégezni.

Az alábbi Azure Cosmos DB-erőforrások létrehozásához másolja a következő példa sablont egy új JSON-fájlba. Lehetőség van arra is, hogy hozzon létre egy JSON-fájlt, amelyet akkor használhat, ha ugyanazon erőforrás több példányát különböző nevekkel és értékekkel telepíti. A Azure Resource Manager-sablonok számos módon telepíthetők, például a [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), az [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), a [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) és a [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-cassandra-with-autoscale-provisioned-throughput"></a>Azure Cosmos-fiók a Cassandra-hoz az autoscale kiépített átviteli sebességgel

Ez a sablon két régióban hoz létre egy Azure Cosmos-fiókot, amely a konzisztencia és a feladatátvétel lehetőségeit tartalmazza, és egy szóköz és egy tábla van konfigurálva az automatikusan méretezhető átviteli sebességhez. Ez a sablon egykattintásos üzembe helyezésre is használható az Azure Gyorsindítás sablonok katalógusból.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-cassandra-with-standard-provisioned-throughput"></a>Azure Cosmos-fiók a Cassandra standard kiosztott átviteli sebességgel

Ez a sablon két régióban hoz létre egy Azure Cosmos-fiókot, amely a konzisztencia és a feladatátvétel beállításait tartalmazza, és a szabványos átviteli sebességhez a szóköz és a tábla van konfigurálva. Ez a sablon egykattintásos üzembe helyezésre is használható az Azure Gyorsindítás sablonok katalógusból.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="next-steps"></a>Következő lépések

Néhány további információforrás:

* [Az Azure Resource Manager dokumentációja](../azure-resource-manager/index.yml)
* [Erőforrás-szolgáltatói séma Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Gyakori Azure Resource Manager telepítési hibák elhárítása](../azure-resource-manager/templates/common-deployment-errors.md)