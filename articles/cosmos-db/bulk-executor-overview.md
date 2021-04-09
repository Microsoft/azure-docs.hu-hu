---
title: Az Azure Cosmos DB tömeges végrehajtói kódtárának áttekintése
description: Tömeges műveleteket hajthat végre Azure Cosmos DB a tömeges importálási és tömeges frissítési API-k használatával, amelyet a tömeges végrehajtó függvénytár kínál.
author: tknandu
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 211fc85f97069fcf3251048a074d625e777f8e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100473"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Az Azure Cosmos DB tömeges végrehajtói kódtárának áttekintése
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
 
Az Azure Cosmos DB egy gyors, rugalmas, globálisan elosztott adatbázis-szolgáltatás, amelyet rugalmas horizontális felskálázásra terveztek a következők támogatása érdekében: 

* Nagy olvasási és írási átviteli sebesség (több millió művelet másodpercenként).  
* Nagy mennyiségű (több száz terabájt, vagy még annál is több) tranzakciós és műveleti adat tárolása kiszámítható, ezredmásodperces késéssel.  

A tömeges végrehajtói kódtár segít kihasználni ezt a hatalmas átviteli sebességet és tárterületet. A tömeges végrehajtói kódtár tömeges importálási és tömeges frissítési API-k segítségével teszi lehetővé a tömeges műveletek végrehajtást az Azure Cosmos DB-ben. A tömeges végrehajtási kódtár funkcióiról a következő szakaszokban talál további információt. 

> [!NOTE] 
> Jelenleg a tömeges végrehajtó függvénytár támogatja az importálási és frissítési műveleteket, és ezt a kódtárat csak Azure Cosmos DB SQL API és a Gremlin API-fiókok támogatják.

> [!IMPORTANT]
> A tömeges végrehajtó függvénytár jelenleg nem támogatott a [kiszolgáló](serverless.md) nélküli fiókoknál. A .NET-ben javasolt az SDK v3 verziójában elérhető [tömeges támogatás](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/) használata.
 
## <a name="key-features-of-the-bulk-executor-library"></a>A tömeges végrehajtó könyvtár legfontosabb funkciói  
 
* Jelentősen csökkenti az ügyféloldali számítási erőforrásokat, amelyek a tárolóhoz lefoglalt átviteli sebesség telítettségét igénylik. Egy többszálú alkalmazás, amely a tömeges importálási API használatával ír be adatot, 10 szor nagyobb írási sebességet tesz elérhetővé egy többszálas alkalmazáshoz képest, amely az adatok párhuzamos írását is lehetővé teszi, miközben az ügyfélszámítógép CPU-ját telítettnek kell lennie.  

* Elvonta az alkalmazás logikájának írásával járó unalmas feladatokat a kérések arányának korlátozása, a kérelmek időtúllépése és egyéb átmeneti kivételek kezelése érdekében, a könyvtárban lévő hatékony kezeléssel.  

* Egyszerűsített mechanizmust biztosít a tömeges műveleteket végző alkalmazások számára a vertikális felskálázáshoz. Egy Azure-beli virtuális gépen futó egyetlen tömeges végrehajtó példány több mint 500 kb/s-nál nagyobb mennyiségű, a magasabb átviteli sebességet pedig további példányok hozzáadásával is elérheti az egyes ügyfél virtuális gépeken.  
 
* A kibővíthető architektúra használatával több mint egy terabájtos adatmennyiséget is importálhat egy órán belül.  

* Az Azure Cosmos-tárolókban lévő meglévő adatmennyiséget javítások formájában is frissítheti. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Hogyan működik a tömeges végrehajtó? 

Ha a dokumentumok importálására vagy frissítésére szolgáló tömeges művelet az entitások egy kötegével aktiválódik, a rendszer először a Azure Cosmos DB partíciós tartományának megfelelő gyűjtőbe rendezi azokat. A partíciós kulcs tartományának megfelelő gyűjtőn belül a rendszer lebontja a mini-batchs szolgáltatást, és minden egyes mini Batch a kiszolgálón véglegesített adattartalomként működik. A tömeges végrehajtó függvénytár a következő mini-kötegek egyidejű végrehajtásához a partíciós kulcs-tartományokon belül és azok között is beépített optimalizációkat tartalmaz. Az alábbi ábra azt szemlélteti, hogy a tömeges végrehajtó hogyan hajtja végre az adatok különböző partíciós kulcsokban való feldolgozását:  

:::image type="content" source="./media/bulk-executor-overview/bulk-executor-architecture.png" alt-text="Tömeges végrehajtó architektúrája" :::

A tömeges végrehajtó könyvtára gondoskodik a gyűjteményhez lefoglalt átviteli sebesség maximális kihasználásáról. Egy [AIMD stílusú torlódás-vezérlési mechanizmust](https://tools.ietf.org/html/rfc5681) használ minden Azure Cosmos db partíciós kulcs tartományához, így hatékonyan kezelheti a díjszabást és az időtúllépéseket. 

## <a name="next-steps"></a>Következő lépések 
  
* További információ: a [.net](bulk-executor-dot-net.md) és a [Java](bulk-executor-java.md)szolgáltatásban a tömeges végrehajtó függvénytárat használó minta alkalmazások kipróbálása.  
* Tekintse meg a tömeges végrehajtó SDK-információkat, valamint a [.net](sql-api-sdk-bulk-executor-dot-net.md) és a [Java](sql-api-sdk-bulk-executor-java.md)kiadási megjegyzéseit.
* A tömeges végrehajtó függvénytár integrálva van a Cosmos DB Spark-összekötőbe, és további információt a [Azure Cosmos db Spark-összekötő](spark-connector.md) című cikkben talál.  
* A tömeges végrehajtó függvénytár a [Azure Cosmos db Connector](../data-factory/connector-azure-cosmos-db.md) új verziójába is integrálva van, Azure Data Factory az adatmásoláshoz.
