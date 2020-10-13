---
title: Azure CLI-minták a Azure Cosmos DBhoz
description: Azure CLI-minták a Azure Cosmos DBhoz
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 58460dffad3d44090644a544f4082b7727ece3f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840336"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure CLI-minták az Azure Cosmos DB-hez

A következő táblázat az Azure Cosmos DB-hez elérhető Azure CLI-példaszkriptekre mutató hivatkozásokat tartalmaz. A jobb oldalon található hivatkozásokkal navigáljon az API-specifikus mintákhoz. Az általános minták minden API esetében azonosak. Az összes Azure Cosmos DB CLI-parancsra vonatkozó hivatkozási lapok az [Azure CLI-referenciában](/cli/azure/cosmosdb)érhetők el. A CLI-szkriptek Azure Cosmos DB is megtalálhatók a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Ezek a minták az Azure CLI 2.9.1 vagy újabb verzióját igénylik. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető témakört.

## <a name="common-samples"></a>Gyakori minták

Ezek a minták az összes Azure Cosmos DB API-ra érvényesek

|Feladat | Leírás |
|---|---|
| [Hozzáadási vagy feladatátvételi régiók](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Régió hozzáadása, feladatátvételi prioritás módosítása, manuális feladatátvétel indítása.|
| [A fiókok kulcsai és a kapcsolatok karakterláncai](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Fiókok kulcsai, csak olvasható kulcsok listázása, kulcsok újragenerálása és a kapcsolatok listázása.|
| [Biztonságos IP-tűzfallal](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Hozzon létre egy olyan Cosmos-fiókot, amely konfigurálva van az IP-tűzfallal.|
| [Új fiók biztonságossá tétele szolgáltatási végpontokkal](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Hozzon létre egy Cosmos-fiókot, és gondoskodjon a szolgáltatás-végpontok biztonságáról.|
| [Meglévő fiók biztonságossá tétele szolgáltatási végpontokkal](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Egy Cosmos-fiók frissítése a szolgáltatás-végpontok biztonságossá tételéhez, ha az alhálózat végül konfigurálva van.|
|||

## <a name="core-sql-api-samples"></a>Core (SQL) API-minták

|Feladat | Leírás |
|---|---|
| [Azure Cosmos-fiók,-adatbázis és-tároló létrehozása](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot, adatbázist és tárolót a Core (SQL) API-hoz. |
| [Azure Cosmos-fiók,-adatbázis és-tároló létrehozása az autoscale](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot, adatbázist és tárolót az autoscale for Core (SQL) API-val. |
| [Átviteli sebességi műveletek](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Olvashatja, frissítheti és áttelepítheti az alapszintű és a standard szintű átviteli sebességet egy adatbázison és tárolón.|
| [Erőforrások zárolásának törlése a törlésből](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal.|
|||

## <a name="mongodb-api-samples"></a>MongoDB API-minták

|Feladat | Leírás |
|---|---|
| [Azure Cosmos-fiók, adatbázis és gyűjtemény létrehozása](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot, adatbázist és gyűjteményt a MongoDB API-hoz. |
| [Hozzon létre egy Azure Cosmos-fiókot, az adatbázist az autoscale és két gyűjtemény közös átviteli sebességgel](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot, az adatbázist az autoscale és két gyűjtemény közös átviteli sebességgel a MongoDB API-hoz. |
| [Átviteli sebességi műveletek](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Olvashatja, frissítheti és áttelepítheti az alapszintű és a standard szintű átviteli sebességet egy adatbázison és gyűjteményen.|
| [Erőforrások zárolásának törlése a törlésből](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal.|
|||

## <a name="cassandra-api-samples"></a>Cassandra API minták

|Feladat | Leírás |
|---|---|
| [Azure Cosmos-fiók, térköz és tábla létrehozása](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot, a térközt és a Cassandra API táblát. |
| [Azure Cosmos-fiók,-térköz és-tábla létrehozása az autoscale paranccsal](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot, egy térközt és egy táblázatot, amely automatikusan méretezhető a Cassandra APIhoz. |
| [Átviteli sebességi műveletek](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Olvashatja, frissítheti és áttelepítheti az autoscale és a standard átviteli sebesség közötti térközt és táblázatot.|
| [Erőforrások zárolásának törlése a törlésből](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal.|
|||

## <a name="gremlin-api-samples"></a>Gremlin API-minták

|Feladat | Leírás |
|---|---|
| [Azure Cosmos-fiók, adatbázis és gráf létrehozása](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot, adatbázist és gráfot a Gremlin API-hoz. |
| [Azure Cosmos-fiók, adatbázis és gráf létrehozása az autoscalevel](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot, adatbázist és gráfot a Gremlin API-hoz. |
| [Átviteli sebességi műveletek](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Olvashatja, frissítheti és áttelepítheti az autoscale és a standard átviteli sebességét egy adatbázison és gráfon.|
| [Erőforrások zárolásának törlése a törlésből](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal.|
|||

## <a name="table-api-samples"></a>Table API minták

|Feladat | Leírás |
|---|---|
| [Azure Cosmos-fiók és-tábla létrehozása](scripts/cli/table/create.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot és táblát a Table APIhoz. |
| [Azure Cosmos-fiók és-tábla létrehozása az autoscale paranccsal](scripts/cli/table/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot és táblát, amely automatikusan méretezhető a Table APIhoz. |
| [Átviteli sebességi műveletek](scripts/cli/table/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Olvashatja, frissítheti és áttelepítheti az alapszintű és a standard szintű adatátviteli sebességet egy táblán.|
| [Erőforrások zárolásának törlése a törlésből](scripts/cli/table/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal.|
|||
