---
title: Adatok migrálása az Apache Cassandra-ból Azure Cosmos DB Cassandra API a Databricks (Spark) használatával
description: Megtudhatja, hogyan telepítheti át az Apache Cassandra Database-ből származó adatok Azure Cosmos DB Cassandra API a Azure Databricks és a Spark használatával.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: caf9cbb0ca017ee00c5061d94e0d37703194943d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573368"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Adatok migrálása a Cassandra-ből Azure Cosmos DB Cassandra API-fiókba a Azure Databricks használatával
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A Azure Cosmos DB Cassandra API az Apache Cassandra-on futó nagyvállalati számítási feladatok széles választékát váltják ki, többek között: 

* **A felügyelet és a figyelés nem jelent terhelést:** Kiküszöböli a több operációs rendszer-, JVM-és YAML-fájlon, valamint azok interakcióján keresztüli különböző beállítások felügyeletének és figyelésének a terhelését.

* **Jelentős költségmegtakarítás:** A költségeket megtakaríthatja Azure Cosmos DBával, amely magában foglalja a virtuális gépek, a sávszélesség és a kapcsolódó licencek költségeit is. Emellett nem szükséges az adatközpontok, a kiszolgálók, az SSD-tárolók, a Hálózatkezelés és a villamosenergia-költségek kezelése. 

* **Meglévő kód és eszközök használatának lehetősége:** A Azure Cosmos DB a meglévő Cassandra SDK-k és eszközök segítségével biztosítja a vezetékes protokollok szintjének kompatibilitását. A kompatibilitásnak köszönhetően meglévő kódbázisát minimális változtatásokkal az Azure Cosmos DB Cassandra API-val is használhatja.

Az adatbázis-számítási feladatok egyik platformról a másikra való áttelepíthetők többféleképpen. A [Azure Databricks](https://azure.microsoft.com/services/databricks/) egy olyan platform, amely [Apache Spark](https://spark.apache.org/) szolgáltatásként szolgál, amely lehetővé teszik az offline Migrálás nagy léptékű elvégzését. Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek az adatok natív Apache Cassandra-beli alapterületekről/táblázatokból való átírásához, hogy a Azure Databricks használatával Azure Cosmos DB Cassandra API.

## <a name="prerequisites"></a>Előfeltételek

* [Azure Cosmos DB Cassandra API fiók kiépítése](create-cassandra-dotnet.md#create-a-database-account)

* [Tekintse át Azure Cosmos DB Cassandra APIhoz való csatlakozás alapjait](cassandra-spark-generic.md)

* A kompatibilitás érdekében tekintse át a [Azure Cosmos DB Cassandra API támogatott funkcióit](cassandra-support.md) .

* Győződjön meg arról, hogy már létrehozott üres tárhelyet és táblákat a cél Azure Cosmos DB Cassandra API fiókban

* [Cqlsh vagy üzemeltetett rendszerhéj használata az ellenőrzéshez](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Azure Databricks-fürt kiépítése

[Azure Databricks-fürt](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)kiépítésére vonatkozó utasításokat követve hozhat létre. Javasoljuk, hogy válassza ki a Databricks Runtime 7,5-es verzióját, amely támogatja a Spark 3,0-et:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks futtatókörnyezet":::


## <a name="add-dependencies"></a>Függőségek hozzáadása

A natív és a Cosmos DB Cassandra-végpontokhoz való csatlakozáshoz hozzá kell adnia a Apache Spark Cassandra Connector-függvénytárat a fürthöz. A fürtben válassza a könyvtárak lehetőséget > telepítse a New-> mavent. Hozzáadás `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` a Maven koordinátáihoz:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks keresési csomagok":::

Válassza a telepítés lehetőséget, és győződjön meg arról, hogy a telepítés befejeződése után újraindítja a fürtöt. 

> [!NOTE]
> Győződjön meg arról, hogy a Cassandra Connector függvénytárának telepítése után újra kell indítania a Databricks-fürtöt.

## <a name="create-scala-notebook-for-migration"></a>Scala-jegyzetfüzet létrehozása áttelepítéshez

Hozzon létre egy Scala notebookot a Databricks-ben a következő kóddal. Cserélje le a forrás-és cél Cassandra-konfigurációkat a megfelelő hitelesítő adatokkal, valamint a forrás-és cél-alapterületekre és táblákra, majd futtassa a parancsot:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> A és a `spark.cassandra.output.batch.size.rows` `spark.cassandra.output.concurrent.writes` , valamint a Spark-fürtben dolgozó szakemberek száma fontos konfigurációk, amelyek a [korlátozás](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)elkerüléséhez szükségesek, ami akkor fordul elő, ha a kérések Azure Cosmos db túllépik a kiépített átviteli sebességet/([kérési egységeket](./request-units.md)). Előfordulhat, hogy módosítania kell ezeket a beállításokat a Spark-fürtben lévő végrehajtók számától függően, és az egyes rekordok méretének (és így RU-nek) a megcélzott táblákba való beírása is lehetséges.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="rate-limiting-429-error"></a>Mérték korlátozása (429 hiba)
`request rate is large`A fenti beállításoknak a minimális értékekre való csökkentése ellenére előfordulhat, hogy a 429-es hibakód vagy a hiba szövege nem jelenik meg. Az alábbiakban néhány ilyen forgatókönyvet ismertetünk:

- **A táblához lefoglalt átviteli sebesség kevesebb, mint 6000 [kérelem egység](./request-units.md)**. Még a minimális beállítások is lehetővé teszik, hogy a Spark az írásokat az 6000-es számú kérelmek száma alapján hajtsa végre. Ha kiépített egy táblát egy olyan alaptérben, amely megosztott átviteli sebességgel rendelkezik, lehetséges, hogy ez a tábla kevesebb, mint 6000 RUs érhető el futásidőben. Győződjön meg arról, hogy az áttelepíteni kívánt tábla legalább 6000 RUs számára elérhető az áttelepítés futtatásakor, és szükség esetén a dedikált kérési egységek kiosztása a táblához. 
- **Túlzott adattorzítás nagy mennyiségű adattal**. Ha nagy mennyiségű adattal rendelkezik (azaz táblázatos sorokkal) egy adott táblába való átálláshoz, de jelentős mértékben elferdíti az adatokat (például az ugyanahhoz a partíciós kulcs értékére írt rekordokat), akkor továbbra is használhatja a díjszabást, még akkor is, ha nagy mennyiségű [kérési egység](./request-units.md) van kiépítve a táblában. Ennek az az oka, hogy a kérelmek egységei a fizikai partíciók között egyenlően oszlanak meg, és a nagy adatváltozás miatt a kérések szűk keresztmetszetet jelenthetnek egy adott partícióra vonatkozóan, ami a korlátozást eredményezi. Ebben az esetben javasoljuk, hogy csökkentse a Spark minimális átviteli sebességét, hogy elkerülje a mérték korlátozását, és kényszerítse az áttelepítés lassú futását. Ez a forgatókönyv sokkal gyakoribb lehet a hivatkozás-vagy vezérlési táblázatok áttelepítésekor, ahol a hozzáférés ritkább, de a ferdeség magas lehet. Ha azonban bármilyen más típusú táblázatban jelentős döntés van jelen, érdemes lehet áttekintenie az adatmodellt is, hogy elkerülje a számítási feladatok gyors particionálását az állandó állapotú műveletek során. 



## <a name="next-steps"></a>Következő lépések

* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md) 
* [A partíciós kulcs ajánlott eljárásai](partitioning-overview.md#choose-partitionkey)
* [Ru/s becslése a Azure Cosmos db Capacity Planner cikkei alapján](estimate-ru-with-capacity-planner.md)
* [Rugalmas skálázás Azure Cosmos DB Cassandra API](manage-scale-cassandra.md)
