---
title: Tároló létrehozása Azure Cosmos DB Table API
description: Megtudhatja, hogyan hozhat létre tárolót Azure Cosmos DB-Table API a Azure Portal, a .NET, a Java, a Python, a Node.js és más SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: fde3145e7bd7f4e53ae7a0c44f02e066c28ec785
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101578"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>Tároló létrehozása Azure Cosmos DB Table API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Ez a cikk ismerteti a tárolók létrehozásának különböző módszereit Azure Cosmos DB Table API. Bemutatja, hogyan hozhat létre egy tárolót a Azure Portal, az Azure CLI, a PowerShell vagy a támogatott SDK-k használatával. Ez a cikk bemutatja, hogyan hozhat létre tárolót, hogyan adhatja meg a partíciós kulcsot, és hogyan lehet kiépíteni az átviteli sebességet.

Ez a cikk ismerteti a tárolók létrehozásának különböző módszereit Azure Cosmos DB Table API. Ha más API-t használ, tekintse meg a tároló létrehozásához a [MongoDB](how-to-create-container-mongodb.md), [CASSANDRA API](how-to-create-container-cassandra.md), [Gremlin API](how-to-create-container-gremlin.md)és [SQL API](how-to-create-container.md) -cikkek API-ját.

> [!NOTE]
> Tárolók létrehozásakor ügyeljen arra, hogy ne hozzon létre két tárolót ugyanazzal a névvel, de a különböző burkolattal. Ennek az az oka, hogy az Azure-platform egyes részei nem megkülönböztetik a kis-és nagybetűket, így az ilyen nevű tárolók telemetria és műveleteinek összekeveredését és ütközését okozhatják.

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>Létrehozás az Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-table-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tábla** lehetőséget. Ezután adja meg a következő adatokat:

   * Adja meg a tábla AZONOSÍTÓját.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Válassza az **OK** lehetőséget.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Képernyőkép a Table APIről, tábla hozzáadása párbeszédpanel":::

> [!Note]
> A Table API esetében a rendszer minden új sor hozzáadásakor megad egy partíciókulcsot.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Létrehozás az Azure CLI használatával

[Hozzon létre egy Table API táblát az Azure CLI-vel](./scripts/cli/table/create.md). Az összes Azure Cosmos DB API-val kapcsolatos összes Azure CLI-minta listáját lásd: [Azure CLI-minták a Azure Cosmos DBhoz](cli-samples.md).

## <a name="create-using-powershell"></a>Létrehozás a PowerShell használatával

[Hozzon létre egy Table API táblát a PowerShell használatával](./scripts/powershell/table/create.md). Az összes Azure Cosmos DB API-val kapcsolatos PowerShell-minta listázásához lásd: [PowerShell-minták](powershell-samples.md)

## <a name="next-steps"></a>Következő lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Azure Cosmos-fiók használata](./account-databases-containers-items.md)