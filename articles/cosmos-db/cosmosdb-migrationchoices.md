---
title: Cosmos DB áttelepítési lehetőségek
description: Ez a dokumentum ismerteti a helyszíni vagy a Felhőbeli adatAzure Cosmos DBre való Migrálás különböző lehetőségeit.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 8721c0eb728f568521e86baecb658dc9c869a7f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93097583"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>A helyszíni vagy a Felhőbeli adatátviteli beállítások Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A különböző adatforrásokból származó adatok betölthetők Azure Cosmos DBba. Mivel Azure Cosmos DB több API-t támogat, a célok lehetnek a meglévő API-k. A következő forgatókönyvek az adatok Azure Cosmos DBba való áttelepíthetők:

* Az adatok áthelyezése egy Azure Cosmos-tárolóból egy másik tárolóba ugyanabba az adatbázisba vagy egy másik adatbázisba.
* Az adatáthelyezés dedikált tárolók között a megosztott adatbázis-tárolók között.
* Adatok áthelyezése egy terület1 található Azure Cosmos-fiókból egy másik Azure Cosmos-fiókba ugyanabban vagy egy másik régióban.
* Adatok áthelyezése egy forrásból, például az Azure Blob Storage-ból, egy JSON-fájlból, egy Oracle-adatbázisból, egy Couchbase, a DynamoDB-ből Azure Cosmos DB.

Ahhoz, hogy támogassa a különböző forrásokból származó áttelepítési útvonalakat a különböző Azure Cosmos DB API-kkal, több megoldás is van, amelyek speciális kezelést biztosítanak az egyes áttelepítési útvonalakhoz. Ez a dokumentum felsorolja az elérhető megoldásokat, valamint ismerteti azok előnyeit és korlátozásait.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>A migrálási eszköz kiválasztását befolyásoló tényezők

Az alábbi tényezők befolyásolják a migrálási eszköz kiválasztását:

* **Online vagy offline migrálás**: Számos migrálási eszköz csak egyszeri migrálást tesz lehetővé. Ez azt jelenti, hogy az adatbázishoz hozzáférő alkalmazások egy időre leállhatnak. Egyes migrálási megoldások élő migrálást is lehetővé tesznek azokban az esetekben, amelyekben be van állítva replikációs folyamat a forrás és a cél között.

* **Adatforrás**: a meglévő adatforrások lehetnek olyan különböző adatforrások, mint például az Oracle DB2, a Datastax Cassanda, a Azure SQL Database, a PostgreSQL stb. Az adattípusok egy meglévő Azure Cosmos DB fiókban is lehetnek, és a Migrálás célja lehet az adatmodell módosítása vagy egy másik partíciós kulccsal rendelkező tárolóban lévő adatparticionálás.

* **Azure Cosmos DB API**: Az Azure Cosmos DB-ben lévő SQL API-hoz számos, az Azure Cosmos DB csapata által fejlesztett eszköz elérhető, amelyek segítséget nyújtanak a különböző migrálási forgatókönyvekhez. A többi API saját, speciális eszközkészlettel rendelkezik, amelyet a közösség fejlesztett ki és tart karban. Mivel az Azure Cosmos DB a vonalprotokoll szintjén támogatja ezeket az API-kat, ezeknek az eszközöknek módosítás nélkül működniük kell az adatok Azure Cosmos DB-be történő migrálása során is. Előfordulhat azonban, hogy a szabályozások egyéni kezelést igényelnek, mivel ez a fogalom az Azure Cosmos DB-re vonatkozik.

* **Az adatok mérete**: A legtöbb migrálási eszköz nagyon jól működik a kisebb adathalmazok esetében. Ha az adathalmaz mérete meghaladja a pár száz gigabájtot, korlátozott számú migrálási eszköz áll rendelkezésre. 

* **A migrálás várható időtartama**: A migrálás úgy is konfigurálható, hogy lassan és fokozatosan, kevesebb átviteli sebességet használva menjen végbe, de használhatja az Azure Cosmos DB-céltárolóra kiosztott teljes átviteli sebességet is, így kevesebb idő alatt fejeződhet be.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API

|Áttelepítés típusa|Megoldás|Támogatott források|Támogatott célok|Megfontolandó szempontok|
|---------|---------|---------|---------|---------|
|Offline|[Adatáttelepítési eszköz](import-data.md)| &bull;JSON-/CSV-fájlok<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;AWS DynamoDB<br/>&bull;Azure-Blob Storage|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB Tables API<br/>&bull;JSON-fájlok |&bull; Egyszerűen beállíthatja és támogathatja a több forrást. <br/>&bull; Nagyméretű adathalmazokhoz nem alkalmas.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON-/CSV-fájlok<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB-hez készült Azure Cosmos DB API<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure-Blob Storage <br/> <br/>Tekintse meg az egyéb támogatott források [Azure Data Factoryét](../data-factory/connector-overview.md) ismertető cikket.|&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB-hez készült Azure Cosmos DB API<br/>&bull;JSON-fájlok <br/><br/> További támogatott célokért tekintse meg a [Azure Data Factory](../data-factory/connector-overview.md) cikket. |&bull; Egyszerűen beállíthatja és támogathatja a több forrást.<br/>&bull; A Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé. <br/>&bull; Nagyméretű adatkészletekhez használható. <br/>&bull; Az ellenőrzőpontok hiánya – ez azt jelenti, hogy ha probléma merül fel az áttelepítés során, újra kell indítania a teljes áttelepítési folyamatot.<br/>&bull; A kézbesítetlen levelek várólistájának hiánya – ez azt jelenti, hogy néhány hibás fájl leállíthatja a teljes áttelepítési folyamatot.|
|Offline|[Azure Cosmos DB Spark-összekötő](spark-connector.md)|Azure Cosmos DB SQL API-t. <br/><br/>Más forrásokat is használhat a Spark-ökoszisztéma további összekötőit használva.| Azure Cosmos DB SQL API-t. <br/><br/>Más célokat a Spark-ökoszisztéma további összekötői is használhatnak.| &bull; A Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé. <br/>&bull; Nagyméretű adatkészletekhez használható. <br/>&bull; Egyéni Spark-telepítőre van szüksége. <br/>&bull; A Spark érzékeny a séma következetlenségére, és ez problémát okozhat az áttelepítés során. |
|Offline|[Egyéni eszköz Cosmos DB tömeges végrehajtó könyvtárral](migrate-cosmosdb-data.md)| A forrás az egyéni kódból függ | Azure Cosmos DB SQL API| &bull; Ellenőrzőpont-és kézbesítetlen levelekre vonatkozó képességeket biztosít, amelyek növelik az áttelepítés rugalmasságát. <br/>&bull; Nagyon nagy adatkészletekhez alkalmas (10 TB +).  <br/>&bull; Ehhez az eszközhöz App Serviceként futó egyéni beállítás szükséges. |
|Online|[Cosmos DB Functions + ChangeFeed API](change-feed-functions.md)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; Egyszerűen beállítható. <br/>&bull; Csak akkor működik, ha a forrás egy Azure Cosmos DB tároló. <br/>&bull; Nagyméretű adathalmazokhoz nem alkalmas. <br/>&bull; A nem rögzíti a törlést a forrás tárolóból. |
|Online|[Egyéni áttelepítési szolgáltatás a ChangeFeed használatával](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; A folyamat nyomon követését teszi lehetővé. <br/>&bull; Csak akkor működik, ha a forrás egy Azure Cosmos DB tároló. <br/>&bull; Nagyobb adatkészletek esetén is működik.<br/>&bull; A felhasználónak be kell állítania egy App Service a változási csatorna processzorának üzemeltetéséhez. <br/>&bull; A nem rögzíti a törlést a forrás tárolóból.|
|Online|[Striim](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> Más támogatott forrásokért tekintse meg a [Striim webhelyet](https://www.striim.com/sources-and-targets/) . |&bull;Azure Cosmos DB SQL API <br/>&bull; Azure Cosmos DB Cassandra API<br/><br/> További támogatott célokért tekintse meg a [Striim webhelyet](https://www.striim.com/sources-and-targets/) . | &bull; Sokféle forrással működik, például Oracle, DB2 SQL Server.<br/>&bull; Egyszerűen hozhat létre ETL-folyamatokat, és irányítópultot biztosít a figyeléshez. <br/>&bull; Nagyobb adatkészleteket támogat. <br/>&bull; Mivel ez egy harmadik féltől származó eszköz, azt a piactéren kell megvásárolni, és telepíteni kell a felhasználó környezetében.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API

|Áttelepítés típusa|Megoldás|Támogatott források|Támogatott célok|Megfontolandó szempontok|
|---------|---------|---------|---------|---------|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|MongoDB-hez készült Azure Cosmos DB API |&bull; A Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé. <br/>&bull; Nagyméretű adatkészletekhez alkalmas, és gondoskodik az élő módosítások replikálásáról. <br/>&bull; Csak más MongoDB-forrásokkal működik.|
|Offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| MongoDB-hez készült Azure Cosmos DB API| &bull; A Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé. <br/>&bull; Nagyméretű adatkészletekhez alkalmas, és gondoskodik az élő módosítások replikálásáról. <br/>&bull; Csak más MongoDB-forrásokkal működik.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON-/CSV-fájlok<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB-hez készült Azure Cosmos DB API <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure-Blob Storage <br/><br/> Tekintse meg az egyéb támogatott források [Azure Data Factoryét](../data-factory/connector-overview.md) ismertető cikket. | &bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB-hez készült Azure Cosmos DB API <br/>&bull; JSON-fájlok <br/><br/> További támogatott célokért tekintse meg a [Azure Data Factory](../data-factory/connector-overview.md) cikket.| &bull; Egyszerűen beállíthatja és támogathatja a több forrást. <br/>&bull; A Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé. <br/>&bull; Nagyméretű adatkészletekhez használható. <br/>&bull; Az ellenőrzőpontok hiánya azt jelenti, hogy az áttelepítés során felmerülő problémákhoz a teljes áttelepítési folyamat újraindítására lenne szükség.<br/>&bull; A kézbesítetlen levelek várólistájának hiánya azt jelenti, hogy néhány hibás fájl leállíthatja a teljes áttelepítési folyamatot. <br/>&bull; Az egyes adatforrások olvasási teljesítményének növeléséhez egyéni kód szükséges.|
|Offline|[Meglévő Mongo-eszközök (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|MongoDB | MongoDB-hez készült Azure Cosmos DB API| &bull; Egyszerűen beállítható és integrálható. <br/>&bull; Egyéni kezelést igényel a szabályozáshoz.|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API

|Áttelepítés típusa|Megoldás|Támogatott források|Támogatott célok|Megfontolandó szempontok|
|---------|---------|---------|---------|---------|
|Offline|[cqlsh MÁSOLÁSi parancs](cassandra-import-data.md#migrate-data-using-cqlsh-copy-command)|CSV-fájlok | Azure Cosmos DB Cassandra API| &bull; Egyszerűen beállítható. <br/>&bull; Nagyméretű adathalmazokhoz nem alkalmas. <br/>&bull; Csak akkor működik, ha a forrás egy Cassandra-tábla.|
|Offline|[Táblázat másolása a Sparktal](cassandra-import-data.md#migrate-data-using-spark) | &bull;Apache Cassandra<br/>&bull;Azure Cosmos DB Cassandra API| Azure Cosmos DB Cassandra API | &bull; A Spark-funkciók használatával integrálással az átalakítást és a betöltést. <br/>&bull; A szabályozások kezeléséhez egyéni újrapróbálkozási szabályzattal rendelkező konfigurációra van szükség.|
|Online|[Striim (Oracle DB/Apache Cassandra-ból)](cosmosdb-cassandra-api-migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> Más támogatott forrásokért tekintse meg a [Striim webhelyet](https://www.striim.com/sources-and-targets/) .|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB Cassandra API <br/><br/> További támogatott célokért tekintse meg a [Striim webhelyet](https://www.striim.com/sources-and-targets/) .| &bull; Sokféle forrással működik, például Oracle, DB2 SQL Server. <br/>&bull; Egyszerűen hozhat létre ETL-folyamatokat, és irányítópultot biztosít a figyeléshez. <br/>&bull; Nagyobb adatkészleteket támogat. <br/>&bull; Mivel ez egy harmadik féltől származó eszköz, azt a piactéren kell megvásárolni, és telepíteni kell a felhasználó környezetében.|
|Online|[Blitzz (Oracle DB/Apache Cassandra-ból)](oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>Más támogatott forrásokért tekintse meg a [Blitzz webhelyet](https://www.blitzz.io/) . |Azure Cosmos DB Cassandra API. <br/><br/>További támogatott célokért tekintse meg a [Blitzz webhelyet](https://www.blitzz.io/) . | &bull; Nagyobb adatkészleteket támogat. <br/>&bull; Mivel ez egy harmadik féltől származó eszköz, azt a piactéren kell megvásárolni, és telepíteni kell a felhasználó környezetében.|

## <a name="other-apis"></a>Egyéb API-k

Az SQL API-t, a Mongo API-t és a Cassandra API-t nem használó API-k esetében az API meglévő ökoszisztémái különböző eszközöket támogatnak. 

**Table API** 

* [Adatáttelepítési eszköz](table-import.md#data-migration-tool)
* [AzCopy](table-import.md#migrate-data-by-using-azcopy)

**Gremlin API**

* [Gráf tömeges végrehajtó könyvtára](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Következő lépések

* További információ: a [.net](bulk-executor-dot-net.md) és a [Java](bulk-executor-java.md)szolgáltatásban a tömeges végrehajtó függvénytárat használó minta alkalmazások kipróbálása. 
* A tömeges végrehajtó függvénytár integrálva van a Cosmos DB Spark-összekötőbe, és további információt a [Azure Cosmos db Spark-összekötő](spark-connector.md) című cikkben talál.  
* A nagyméretű áttelepítéssel kapcsolatos további segítségért lépjen kapcsolatba a Azure Cosmos DB termék csapatával, és nyisson meg egy támogatási jegyet az "általános tanácsadó" probléma típusa és a "nagy (TB +) Migrálás" problémás altípusban.
