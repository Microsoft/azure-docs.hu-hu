---
title: Azure PowerShell minták a Azure Cosmos DB Gremlin API-hoz
description: Azure PowerShell minták beszerzése általános feladatok végrehajtásához Azure Cosmos DB Gremlin API-ban
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: a0bd1c732a23ca123c391521f5895eb4dcb8c879
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503385"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-gremlin-api"></a>Azure PowerShell minták a Azure Cosmos DB Gremlin API-hoz
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Az alábbi táblázat a Azure Cosmos DB leggyakrabban használt Azure PowerShell parancsfájljaira mutató hivatkozásokat tartalmaz. A jobb oldalon található hivatkozásokkal navigáljon az API-specifikus mintákhoz. Az általános minták minden API esetében azonosak. Az összes Azure Cosmos DB PowerShell-parancsmagra vonatkozó hivatkozási lapok a [Azure PowerShell-hivatkozásban](/powershell/module/az.cosmosdb)érhetők el. A `Az.CosmosDB` modul már része a `Az` modulnak. [Töltse le és telepítse](/powershell/azure/install-az-ps) az az modul legújabb verzióját az Azure Cosmos db-parancsmagok beszerzéséhez. A [PowerShell-Galéria](https://www.powershellgallery.com/packages/Az/5.4.0)a legújabb verziót is letöltheti. Ezeket a PowerShell-mintákat a GitHub-adattárból is letölthetik Cosmos DBhoz, [Cosmos db PowerShell-mintákat a githubon](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Gyakori minták

|Feladat | Leírás |
|---|---|
|[Fiók frissítése](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB fiók alapértelmezett konzisztencia-szintjének frissítése. |
|[Fiók régiói frissítése](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB fiók régióinak frissítése. |
|[Feladatátvételi prioritás módosítása vagy feladatátvételi trigger](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók regionális feladatátvételi prioritásának módosítása vagy manuális feladatátvétel indítása. |
|[Fiók kulcsai vagy a kapcsolatok karakterláncai](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elsődleges és másodlagos kulcsok, a kapcsolatok karakterláncának beolvasása vagy egy Azure Cosmos DB fiók kulcsának újragenerálása. |
|[Cosmos-fiók létrehozása IP-tűzfallal](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos DB fiókot, amelyen engedélyezve van az IP-tűzfal. |
|||

## <a name="gremlin-api-samples"></a>Gremlin API-minták

|Feladat | Leírás |
|---|---|
|[Fiók, adatbázis és gráf létrehozása](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot, adatbázist és gráfot. |
|[Hozzon létre egy fiókot, adatbázist és gráfot az autoscale](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot, egy adatbázist és egy gráfot az autoscaletel. |
|[Adatbázisok vagy diagramok listázása és lekérése](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy gráf listázása vagy beolvasása. |
|[Átviteli sebességi műveletek](scripts/powershell/gremlin/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatátviteli műveletek egy adatbázishoz vagy gráfhoz, beleértve az autoscale és a standard átviteli sebesség közötti Get, Update és Migrálás folyamatát. |
|[Erőforrások zárolásának törlése a törlésből](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal. |
|||
