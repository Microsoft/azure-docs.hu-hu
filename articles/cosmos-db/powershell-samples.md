---
title: Az Azure PowerShell-minták az Azure Cosmos DB használatával
description: Azure PowerShell-minták – Szkriptek, amelyek segítenek az Azure Cosmos DB-fiókok létrehozásában és felügyeletében.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: 3498ac6a2a4aaa1682d7b5bc5aae5383866d5bcd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926158"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure PowerShell-minták az Azure Cosmos DB-ben

A következő táblázat az Azure Cosmos DB-hez elérhető Azure PowerShell-példaszkriptekre mutató hivatkozásokat tartalmaz. Jelenleg csak az Azure Cosmos DB-fiók kezelhető a PowerShell-lel, más erőforrások, például adatbázisok és tárolók nem kezelhetők a PowerShell használatával.

| |  |
|---|---|
|**Azure Cosmos DB-fiók létrehozása**||
|[Fiók létrehozása és konfigurálása egy Cosmos SQL API-val](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egyetlen Azure Cosmos DB-fiókot hoz létre az SQL API-val történő használathoz. |
|[Hozzon létre, és a egy Cosmos-fiók beállítása az Azure Cosmos DB API a mongodb-hez](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy egyetlen Cosmos-fiókot az Azure Cosmos DB API a mongodb-hez. |
|[Fiók létrehozása és konfigurálása egy Cosmos Gremlin API-val](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egyetlen Azure Cosmos DB-fiókot hoz létre a Gremlin API-val történő használathoz. |
|[Fiók létrehozása és konfigurálása egy Cosmos Cassandra API-val](scripts/create-and-configure-cassandra-database.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egyetlen Azure Cosmos DB-fiókot hoz létre a Cassandra API-val történő használathoz. |
|[Fiók létrehozása és konfigurálása egy Cosmos tábla API-val](scripts/create-table-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egyetlen Azure Cosmos DB-fiókot hoz létre a Table API-val történő használathoz. |
|**Az Azure Cosmos DB méretezése**||
|[Azure Cosmos DB-fiók replikálása több régióban és a feladatátvételi prioritások konfigurálása](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Globálisan és meghatározott feladatátvételi prioritás szerint replikál fiókadatokat több régióba.|
|**Az Azure Cosmos DB védelme**||
| [Fiókkulcsok lekérése](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Lekéri a fiók elsődleges és a másodlagos írási mesterkulcsát és elsődleges és másodlagos írásvédett kulcsát.|
| [MongoDB kapcsolati sztring lekérése](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Lekéri azt a kapcsolati sztringet, amely a MongoDB alkalmazás Azure Cosmos DB-fiókhoz való csatlakoztatására szolgál.|
|[Fiókkulcsok ismételt létrehozása](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Újból létrehozza a fiók mester- vagy írásvédett kulcsát.|
|[Tűzfal létrehozása](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy bemeneti IP-címekre vonatkozó hozzáférés-vezérlési szabályzatot, hogy a fiók hozzáférését a jóváhagyott gépekre és/vagy felhőszolgáltatásokra korlátozza.|
|**Magas rendelkezésre állás, vészhelyreállítás, biztonsági mentés és visszaállítás**||
|[Feladatátvételi szabályzat konfigurálása](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Beállítja azon régiók feladatátvételi prioritását, amelyekbe a fiók replikálva van.|
|||
