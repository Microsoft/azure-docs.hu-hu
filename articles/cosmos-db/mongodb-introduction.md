---
title: Bevezetés a Azure Cosmos DB API-MongoDB
description: Megtudhatja, hogyan tárolhat és lekérdezheti a nagy mennyiségű adatmennyiséget a Azure Cosmos DB használatával a Azure Cosmos DB API-MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 5820592bf06cc9427e12aa0cd79c54dc1f0156e6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657995"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Az [Azure Cosmos DB](introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Az Azure Cosmos DB az [iparág legjobb szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/cosmos-db/) által biztosított [teljes körű, globális terjesztést](distribute-data-globally.md) kínál, valamint [a teljesítmény és a tárterület rugalmas méretezését](partitioning-overview.md) világszerte, az esetek 99%-ában egyszámjegyű ezredmásodperces késéseket és garantált magas rendelkezésre állást. Az Azure Cosmos DB [automatikusan indexeli az adatokat](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy a felhasználónak sémákat és indexeket kellene kezelnie. Egy többmodelles szolgáltatásról van szó, amely támogatja a dokumentum, a kulcs-érték, a gráf és az oszlop típusú adatmodelleket. Azure Cosmos DB a szolgáltatás a Common NoSQL API-k számára implementálja a hálózati protokollokat, beleértve a Cassandra, a MongoDB, a Gremlin és az Azure Table Storage. Ez lehetővé teszi, hogy ismerős NoSQL-ügyfélszoftvereit és eszközeit használja a Cosmos-adatbázissal való kommunikációhoz.

> [!NOTE]
> A [kiszolgáló nélküli kapacitás mód](serverless.md) már elérhető Azure Cosmos db API-MongoDB.

## <a name="wire-protocol-compatibility"></a>Huzal protokoll kompatibilitása

Azure Cosmos DB megvalósítja a MongoDB átviteli protokollt. Ez a megvalósítás lehetővé teszi az átlátható kompatibilitást a natív MongoDB ügyféloldali SDK-k, illesztőprogramok és eszközök használatával. Azure Cosmos DB üzemelteti a MongoDB-adatbázis motorját. A MongoDB által támogatott szolgáltatások részletei itt találhatók: 
- [Azure Cosmos DB API a Mongo DB 4,0-es verziójához](mongodb-feature-support-40.md)
- [Azure Cosmos DB API a Mongo DB 3,6-es verziójához](mongodb-feature-support-36.md)

Alapértelmezés szerint a Azure Cosmos DB API-MongoDB használatával létrehozott új fiókok kompatibilisek az MongoDB Wire protokoll 3,6-es verziójával. Minden olyan MongoDB-ügyfél-illesztőprogram, amely megérti ezt a protokoll-verziót, natív módon csatlakozhat Cosmos DBhoz.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="MongoDB-hez készült Azure Cosmos DB API" border="false":::

## <a name="key-benefits"></a>Főbb előnyök

Az Cosmos DB fő előnye, hogy teljes körűen felügyelt, globálisan elosztott adatbázis-szolgáltatásként szolgál, [itt](introduction.md)olvashat. Emellett a népszerű NoSQL API-k natív módon implementálása révén a Cosmos DB a következő előnyöket nyújtja:

* Az alkalmazást egyszerűen áttelepítheti Cosmos DB az alkalmazás logikájának jelentős részeinek megőrzése mellett.
* Tartsa hordozható alkalmazásait, és folytassa továbbra is a Cloud vendor-agnosztikusot.
* A Cosmos DB által működtetett, közös NoSQL API-kra vonatkozó piacvezető, pénzügyi támogatású SLA-k beszerzése.
* Igény szerint rugalmasan méretezheti a Cosmos-adatbázisok kiépített átviteli sebességét és tárterületét, és csak a szükséges átviteli sebességért és tárterületért kell fizetnie. Ez jelentős költségmegtakarítást eredményez.
* Kulcsrakész, globális eloszlás a többrégiós replikációval.

## <a name="cosmos-dbs-api-for-mongodb"></a>Cosmos DB API-MongoDB

Kövesse a gyors útmutatókat, és hozzon létre egy Azure Cosmos-fiókot, és telepítse át a meglévő MongoDB alkalmazást Azure Cosmos DB használatára, vagy hozzon létre egy újat:

* [Meglévő MongoDB migrálása Node.js webalkalmazásra](create-mongodb-nodejs.md).
* [Webalkalmazás létrehozása a MongoDB és a .NET SDK-hoz készült Azure Cosmos DB API-val](create-mongodb-dotnet.md)
* [A MongoDB és a Java SDK Azure Cosmos DB API-ját használó konzolos alkalmazás létrehozása](create-mongodb-java.md)

## <a name="next-steps"></a>Következő lépések

Íme, pár hivatkozás az első lépések megtételéhez:

* Kövesse a [MongoDB-alkalmazás csatlakoztatása Azure Cosmos db](connect-mongodb-account.md) oktatóanyagot, amelyből megtudhatja, hogyan kérheti le a fiók kapcsolati karakterláncának adatait.
* Kövesse a [Studio 3T használata Azure Cosmos db](mongodb-mongochef.md) oktatóanyagot, amelyből megtudhatja, hogyan hozhat létre kapcsolatot a Cosmos-adatbázis és a MongoDB-alkalmazás között a Studio 3T.
* Kövesse az [MongoDB-adatimportálás Azure Cosmos db](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) oktatóanyagba című témakört, amely az adatait egy Cosmos-adatbázisba importálja.
* Kapcsolódjon egy Cosmos-fiókhoz a [Robo 3T](mongodb-robomongo.md)használatával.
* Ismerje meg, hogyan [konfigurálhatja a globálisan elosztott alkalmazások olvasási beállításait](../cosmos-db/tutorial-global-distribution-mongodb.md).
* Keresse meg a [hibaelhárítási útmutatóban](mongodb-troubleshoot.md) gyakran előforduló hibákat a megoldásokat


<sup>Megjegyzés: Ez a cikk az Azure Cosmos DB egy olyan szolgáltatását ismerteti, amely a MongoDB-adatbázisokkal való, vezetékes protokoll-kompatibilitást biztosít. A Microsoft nem futtat MongoDB-adatbázisokat a szolgáltatás biztosításához. Azure Cosmos DB nem kapcsolódik a MongoDB, Inc. szolgáltatáshoz.</sup>
