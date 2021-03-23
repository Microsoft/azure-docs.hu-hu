---
title: Adatok migrálása az Apache Cassandra-ből a Azure Cosmos DB Cassandra APIba a Databricks (Spark) használatával
description: Megtudhatja, hogyan telepíthet át egy Apache Cassandra-adatbázis adatait a Azure Cosmos DB Cassandra APIba Azure Databricks és Spark használatával.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: caedefbf3887205b68bcd5de5e7cd5f1f7d7f53c
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801009"
---
# <a name="migrate-data-from-cassandra-to-an-azure-cosmos-db-cassandra-api-account-by-using-azure-databricks"></a>Adatok migrálása a Cassandra-ből egy Azure Cosmos DB Cassandra API-fiókba Azure Databricks használatával
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A Azure Cosmos DB Cassandra API az Apache Cassandra-on futó nagyvállalati munkaterhelések számára nagyszerű választás, számos okból:

* **A felügyelet és a figyelés nem jelent terhelést:** Kiküszöböli az operációs rendszer-, JVM-és YAML-fájlok és azok interakciójának felügyeletének és figyelésének terhelését.

* **Jelentős költségmegtakarítás:** Megtakaríthatja a költségeket a Azure Cosmos DBával, amely magában foglalja a virtuális gépek, a sávszélesség és a kapcsolódó licencek költségeit is. Nem szükséges az adatközpontok, a kiszolgálók, az SSD-tárolók, a Hálózatkezelés és a villamosenergia-költségek kezelése.

* **Meglévő kód és eszközök használatának lehetősége:** A Azure Cosmos DB a meglévő Cassandra SDK-k és eszközök segítségével biztosítja a vezetékes protokoll szintű kompatibilitást. Ez a kompatibilitási funkció biztosítja, hogy a meglévő kód a Azure Cosmos DB Cassandra API egyszerű módosításokkal használható legyen.

Az adatbázisok számítási feladatait többféleképpen is áttelepítheti az egyik platformról a másikra. A [Azure Databricks](https://azure.microsoft.com/services/databricks/) a [Apache Spark](https://spark.apache.org/) szolgáltatásként nyújtott platform, amely lehetővé teszik az offline Migrálás nagy léptékű elvégzését. Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek az adatok natív Apache Cassandra-beli alapterületekről és táblázatokból való átmásolásához a Azure Cosmos DB Cassandra API Azure Databricks használatával.

## <a name="prerequisites"></a>Előfeltételek

* [Azure Cosmos DB Cassandra API-fiók kiépítése](create-cassandra-dotnet.md#create-a-database-account).

* [Tekintse át Azure Cosmos DB Cassandra APIhoz való csatlakozás alapjait](cassandra-spark-generic.md).

* A kompatibilitás érdekében tekintse át a [Azure Cosmos DB Cassandra API támogatott funkcióit](cassandra-support.md) .

* Győződjön meg arról, hogy már létrehozott üres tárhelyeket és táblákat a cél Azure Cosmos DB Cassandra API fiókban.

* [Az érvényesítéshez használjon cqlsh vagy üzemeltetett rendszerhéjt](cassandra-support.md#hosted-cql-shell-preview).

## <a name="provision-an-azure-databricks-cluster"></a>Azure Databricks-fürt kiépítése

[Azure Databricks-fürt](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)kiépítésére vonatkozó utasításokat követve hozhat létre. Javasoljuk, hogy válassza ki a Databricks Runtime 7,5-es verzióját, amely támogatja a Spark 3,0-et.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="A Databricks futásidejű verziójának megtalálását bemutató képernyőkép.":::

## <a name="add-dependencies"></a>Függőségek hozzáadása

A natív és a Azure Cosmos DB Cassandra-végpontokhoz való csatlakozáshoz hozzá kell adnia a Apache Spark Cassandra Connector-függvénytárat a fürthöz. A fürtben válassza a **könyvtárak**  >  **új**  >  **Maven** telepítése lehetőséget, majd adja hozzá `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` a Maven-koordinátákat.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Képernyőkép, amely a Maven-csomagok keresését mutatja a Databricks-ben.":::

Ha a telepítés befejeződött, válassza a **telepítés** lehetőséget, majd indítsa újra a fürtöt.

> [!NOTE]
> Győződjön meg arról, hogy a Cassandra Connector függvénytárának telepítése után újra kell indítania a Databricks-fürtöt.

## <a name="create-scala-notebook-for-migration"></a>Scala-jegyzetfüzet létrehozása áttelepítéshez

Scala-jegyzetfüzet létrehozása a Databricks-ben. Cserélje le a forrás-és a cél Cassandra-konfigurációkat a megfelelő hitelesítő adatokkal, valamint a forrás-és cél-alapterületekre és táblákra. Ezután futtassa a következő kódot:

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
> A `spark.cassandra.output.batch.size.rows` Spark-fürtben a és az `spark.cassandra.output.concurrent.writes` értékek és a feldolgozók száma fontos konfigurációt igényel a [mérték korlátozása](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)érdekében. A díjszabás akkor fordul elő, amikor a Azure Cosmos DB kérelmek túllépik a kiosztott átviteli sebességet vagy a [kérési egységeket](./request-units.md) (RUs). Előfordulhat, hogy módosítania kell ezeket a beállításokat a Spark-fürtben lévő végrehajtók számától függően, és lehetséges, hogy a cél táblákba írt rekordok mérete (és az RU-díj is).

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="rate-limiting-429-error"></a>Mérték korlátozása (429 hiba)

Előfordulhat, hogy a 429-es hibakód vagy a "kérések aránya nagy" szöveg jelenik meg, még akkor is, ha a beállításokat a minimális értékekre csökkenti. A következő forgatókönyvek a díjszabási korlátozásokat okozhatják:

* **A táblához lefoglalt átviteli sebesség kevesebb, mint 6 000 [kérelem egység](./request-units.md)**. Még a minimális beállításokon is megadható, hogy a Spark körülbelül 6 000-os kérési egységgel vagy más számmal tud írni. Ha megosztott átviteli sebességgel rendelkező táblán telepített egy táblázatot, lehetséges, hogy ez a tábla a futtatókörnyezetnél kevesebb, mint 6 000 RUs-t tartalmaz.

    Győződjön meg arról, hogy az áttelepíteni kívánt tábla legalább 6 000 RUs elérhető az áttelepítés futtatásakor. Ha szükséges, a táblához rendelje hozzá a dedikált kérési egységeket.

* **Túlzott adattorzítás nagy mennyiségű adattal**. Ha nagy mennyiségű adattal szeretne áttérni egy adott táblába, de jelentős mértékben elferdíti az adatokat (azaz a rekordok nagy száma azonos a partíciós kulcs értékével), akkor a díjszabás akkor is megmarad, ha több, a táblában kiosztott [kérési egységgel](./request-units.md) rendelkezik. A kérelmek egységei a fizikai partíciók között egyenlően oszlanak meg, a nagy mennyiségű adatdöntés pedig a kérések egyetlen partícióhoz való szűk keresztmetszetét eredményezheti.

    Ebben az esetben csökkentse a Spark minimális átviteli sebességét, és kényszerítse az áttelepítés lassú futását. Ez a forgatókönyv gyakoribb lehet a hivatkozás-vagy vezérlési táblázatok áttelepítésekor, ahol a hozzáférés kevésbé gyakori, és a döntés magas lehet. Ha azonban bármilyen más típusú táblázatban jelentős döntés van jelen, érdemes lehet áttekintenie az adatmodellt, hogy elkerülje a számítási feladatokhoz szükséges gyors particionálási problémákat az állandó állapotú műveletek során.

## <a name="next-steps"></a>Következő lépések

* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [A partíciós kulcs ajánlott eljárásai](partitioning-overview.md#choose-partitionkey)
* [RU/s becslése a Azure Cosmos DB Capacity Planner használatával](estimate-ru-with-capacity-planner.md)
* [Rugalmas skálázás Azure Cosmos DB Cassandra API](manage-scale-cassandra.md)
