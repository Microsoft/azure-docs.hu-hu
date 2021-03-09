---
title: Azure PowerShell minták Azure Cosmos DB Table API
description: A Azure PowerShell minták beszerzése általános feladatok végrehajtásához Azure Cosmos DB Table API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: a72b329586d25b5eb7014e0fba65e7e6f8d37598
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503334"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-table-api"></a>Azure PowerShell minták Azure Cosmos DB Table API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

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

## <a name="table-api-samples"></a>Table API minták

|Feladat | Leírás |
|---|---|
|[Fiók és tábla létrehozása](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot és-táblát. |
|[Fiók és tábla létrehozása az autoscale paranccsal](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot és egy tábla-méretezést. |
|[Táblázatok listázása és lekérése](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Táblázatok listázása vagy beolvasása. |
|[Átviteli sebességi műveletek](scripts/powershell/table/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy táblázat átviteli műveletei, beleértve a Get, a Update és az áttelepítés az autoscale és a standard átviteli sebesség között. |
|[Erőforrások zárolásának törlése a törlésből](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal. |
|||
