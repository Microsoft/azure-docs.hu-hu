---
title: Azure CLI-minták az Azure Cosmos DB-ben | Microsoft Docs
description: Azure CLI-minták – Azure Cosmos DB-fiókok, -adatbázisok, -tárolók, -régiók és -tűzfalak létrehozása és kezelése.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 461207d0c9d27ed645dcac98e6256431bb23f8ad
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51005988"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure CLI-minták az Azure Cosmos DB-hez

A következő táblázat az Azure Cosmos DB-hez elérhető Azure CLI-példaszkriptekre mutató hivatkozásokat tartalmaz. Az Azure Cosmos DB CLI-parancsainak egyes referenciaoldalait az [Azure CLI referenciái](/cli/azure/cosmosdb) között érheti el.

| |  |
|---|---|
|**Azure Cosmos DB-fiók, -adatbázis és -tárolók létrehozása**||
| [SQL API-fiók létrehozása](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Egyetlen Azure Cosmos DB SQL API-fiókot, -adatbázist és -tárolót hoz létre. |
| [MongoDB API-fiók létrehozása](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Egyetlen Azure Cosmos DB MongoDB API-fiókot, -adatbázist és -gyűjteményt hoz létre. |
| [Gremlin API-fiók létrehozása](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Egyetlen Azure Cosmos DB Gremlin API-fiókot, -adatbázist és -grafikont hoz létre. |
| [Cassandra API-fiók létrehozása](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Egyetlen Azure Cosmos DB Cassandra API-fiókot és -adatbázist hoz létre. |
| [Table API-fiók létrehozása](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Egyetlen Azure Cosmos DB Table API-fiókot, -adatbázist és -táblázatot hoz létre. |
|**Az Azure Cosmos DB méretezése**||
| [A tároló átviteli sebességének méretezése](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Módosítja egy tároló kiosztott átviteli sebességét.|
| [Azure Cosmos DB-adatbázisfiók replikálása több régióban és a feladatátvételi prioritások konfigurálása](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Globálisan és meghatározott feladatátvételi prioritás szerint replikál fiókadatokat több régióba.|
|**Az Azure Cosmos DB védelme**||
| [Fiókkulcsok lekérése](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Lekéri a fiók elsődleges és a másodlagos írási mesterkulcsát és elsődleges és másodlagos írásvédett kulcsát.|
| [MongoDB kapcsolati sztring lekérése](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Lekéri azt a kapcsolati sztringet, amely a MongoDB alkalmazás Azure Cosmos DB-fiókhoz való csatlakoztatására szolgál.|
| [Fiókkulcsok ismételt létrehozása](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Újból létrehozza a fiók kulcsait.|
| [Tűzfal létrehozása](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy bemeneti IP-címekre vonatkozó hozzáférés-vezérlési szabályzatot, hogy a fiók hozzáférését a jóváhagyott gépekre és/vagy felhőszolgáltatásokra korlátozza.|
|**Magas rendelkezésre állás, vészhelyreállítás, biztonsági mentés és visszaállítás**||
| [Feladatátvételi szabályzat konfigurálása](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Beállítja azon régiók feladatátvételi prioritását, amelyekbe a fiók replikálva van.|
|**Az Azure Cosmos DB csatlakoztatása erőforrásokhoz**||
| [Webalkalmazás csatlakoztatása az Azure Cosmos DB-hez](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Azure Cosmos DB-adatbázis létrehozása és csatlakozása egy Azure-webalkalmazáshoz.|
|||
