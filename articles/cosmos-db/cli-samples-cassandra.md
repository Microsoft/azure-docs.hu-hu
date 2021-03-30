---
title: Azure CLI-minták Azure Cosmos DB Cassandra API
description: Azure CLI-minták Azure Cosmos DB Cassandra API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2dbae196b981b792b47e6c12e500398f772d8451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342130"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-cassandra-api"></a>Azure CLI-minták Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A következő táblázat az Azure Cosmos DB-hez elérhető Azure CLI-példaszkriptekre mutató hivatkozásokat tartalmaz. A jobb oldalon található hivatkozásokkal navigáljon az API-specifikus mintákhoz. Az általános minták minden API esetében azonosak. Az összes Azure Cosmos DB CLI-parancsra vonatkozó hivatkozási lapok az [Azure CLI-referenciában](/cli/azure/cosmosdb)érhetők el. A CLI-szkriptek Azure Cosmos DB is megtalálhatók a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Ezek a minták az Azure CLI 2.12.1 vagy újabb verzióját igénylik. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető témakört.

## <a name="common-samples"></a>Gyakori minták

Ezek a minták az összes Azure Cosmos DB API-ra érvényesek

|Feladat | Leírás |
|---|---|
| [Hozzáadási vagy feladatátvételi régiók](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Régió hozzáadása, feladatátvételi prioritás módosítása, manuális feladatátvétel indítása.|
| [A fiókok kulcsai és a kapcsolatok karakterláncai](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Fiókok kulcsai, csak olvasható kulcsok listázása, kulcsok újragenerálása és a kapcsolatok listázása.|
| [Biztonságos IP-tűzfallal](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Hozzon létre egy olyan Cosmos-fiókot, amely konfigurálva van az IP-tűzfallal.|
| [Új fiók biztonságossá tétele szolgáltatási végpontokkal](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Hozzon létre egy Cosmos-fiókot, és gondoskodjon a szolgáltatás-végpontok biztonságáról.|
| [Meglévő fiók biztonságossá tétele szolgáltatási végpontokkal](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Egy Cosmos-fiók frissítése a szolgáltatás-végpontok biztonságossá tételéhez, ha az alhálózat végül konfigurálva van.|
|||

## <a name="cassandra-api-samples"></a>Cassandra API minták

|Feladat | Leírás |
|---|---|
| [Azure Cosmos-fiók, térköz és tábla létrehozása](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot, a térközt és a Cassandra API táblát. |
| [Azure Cosmos-fiók,-térköz és-tábla létrehozása az autoscale paranccsal](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot, egy térközt és egy táblázatot, amely automatikusan méretezhető a Cassandra APIhoz. |
| [Átviteli sebességi műveletek](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Olvashatja, frissítheti és áttelepítheti az autoscale és a standard átviteli sebesség közötti térközt és táblázatot.|
| [Erőforrások zárolásának törlése a törlésből](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal.|
|||
