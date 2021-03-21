---
title: Azure Cosmos DB Cassandra API használata a Sparkból
description: Ez a cikk a Spark Cosmos DB Cassandra API-integrációjának főoldala.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/01/2019
ms.openlocfilehash: d25e168e342e22af9dc41d31dd7e18530aaa22b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090511"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Csatlakozás az Azure Cosmos DB Cassandra API-hoz a Sparkból
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Ez a cikk többek között a Sparkból való Azure Cosmos DB Cassandra API-integrációról szóló cikkek sorozata. A cikkek kiterjednek a kapcsolatra, az adatdefiníciós nyelv (DDL) műveleteire, az alapszintű adatmanipulációs nyelvre (DML) vonatkozó műveletekre és a Spark Azure Cosmos DB Cassandra API-integrációra. 

## <a name="prerequisites"></a>Előfeltételek
* [Azure Cosmos DB Cassandra API-fiók kiépítése.](create-cassandra-dotnet.md#create-a-database-account)

* A Spark-környezet kiépítése [[Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)  |  [Azure HDInsight – Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) | Mások].

## <a name="dependencies-for-connectivity"></a>Kapcsolat függőségei
* **Spark-összekötő Cassandra-hez:** A Spark-összekötő a Azure Cosmos DB Cassandra APIhoz való kapcsolódásra szolgál.  Azonosítsa és használja a [Maven Central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) -ban található összekötő azon verzióját, amely kompatibilis a Spark-környezet Spark-és Scala-verziójával.

* **Azure Cosmos db segítő könyvtár a Cassandra APIhoz:** A Spark-összekötőn kívül szüksége lesz egy másik, [Azure-Cosmos-Cassandra-Spark-Helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) nevű könyvtárra Azure Cosmos db. Ez a könyvtár tartalmazza az egyéni kapcsolatok gyárát, és újrapróbálkozási házirend-osztályokat tartalmaz.

  Az újrapróbálkozási szabályzat a (z) Azure Cosmos DBban úgy van konfigurálva, hogy kezelje a 429-es HTTP-állapotkódot ("kérelmek aránya – nagy") A Azure Cosmos DB Cassandra API lefordítja ezeket a kivételeket a Cassandra Native protokollon túlterhelt hibákra, és újra tud próbálkozni a visszalépéssel. Mivel a Azure Cosmos DB kiépített átviteli sebességi modellt használ, a kérelmek arányának korlátozása kivételeket eredményez, ha a bejövő és a kimenő forgalom aránya növekszik. Az újrapróbálkozási szabályzat védi a Spark-feladatokat olyan adattüskékkel szemben, amelyek egy pillanatra túllépik a tárolóhoz lefoglalt átviteli sebességet.

  > [!NOTE] 
  > Az újrapróbálkozási szabályzat csak a pillanatnyi tüskékkel tudja védeni a Spark-feladatokat. Ha még nem konfigurálta a számítási feladat futtatásához szükséges RUs-t, akkor az újrapróbálkozási szabályzat nem alkalmazható, és az újrapróbálkozási szabályzat osztálya átadja a kivételt.

* **Azure Cosmos db fiók kapcsolatának részletei:** Az Azure Cassandra API fiók neve, a fiók végpontja és a kulcs.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Spark-összekötő átviteli sebességének konfigurációs paraméterei

A következő táblázat Azure Cosmos DB felsorolja az összekötő által biztosított Cassandra API-specifikus átviteli sebességű konfigurációs paramétereket. Az összes konfigurációs paraméter részletes listáját a Spark Cassandra Connector GitHub-tárházának [konfigurációs referenciát](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) ismertető oldalán tekintheti meg.

| **Tulajdonság neve** | **Alapértelmezett érték** | **Leírás** |
|---------|---------|---------|
| spark.cassandra.output.batCH. size. sorok |  1 |Sorok száma egy kötegben. Állítsa ezt a paramétert 1-re. Ez a paraméter a nagy teljesítményű számítási feladatok nagyobb átviteli sebességének elérésére szolgál. |
| spark.cassandra.connection.connections_per_executor_max  | Nincsenek | Csomópontok maximális száma felhasználónként/végrehajtóként. a 10 * n egy n csomópontos Cassandra-fürtön belüli 10 kapcsolattal egyenlő. Tehát, ha az 5 csomópontos Cassandra-fürthöz egy-egy csomóponton 5 kapcsolatra van szüksége, akkor ezt a konfigurációt 25-re kell állítania. Módosítsa ezt az értéket a párhuzamosság foka vagy a Spark-feladatok által konfigurált végrehajtók száma alapján.   |
| Spark. Cassandra. output. párhuzamos. írások  |  100 | Meghatározza, hogy hány párhuzamos írási művelet hajtható végre a végrehajtón. Mivel a "batch. size. sorok" értéket 1-re állítja, ügyeljen arra, hogy ennek az értéknek a méretezése megfelelő legyen. Módosítsa ezt az értéket a párhuzamosság foka vagy a munkaterheléshez elérni kívánt átviteli sebesség alapján. |
| Spark. Cassandra. párhuzamos. olvasások |  512 | Meghatározza, hogy hány párhuzamos olvasási felmerülhet a végrehajtó. Módosítsa ezt az értéket a párhuzamosság foka vagy a munkaterheléshez elérni kívánt átviteli sebesség alapján.  |
| spark.cassandra.output.throughput_mb_per_sec  | Nincsenek | Meghatározza az összes írási sebességet egy végrehajtón. Ez a paraméter felső korlátként használható a Spark-feladatok átviteli sebességéhez, és a Cosmos-tároló kiépített átviteli sebességén alapul.   |
| spark.cassandra.input.reads_per_sec| Nincsenek   | Meghatározza a végrehajtó összes olvasási sebességét. Ez a paraméter felső korlátként használható a Spark-feladatok átviteli sebességéhez, és a Cosmos-tároló kiépített átviteli sebességén alapul.  |
| spark.cassandra.output.batCH. grouping. Buffer. size |  1000  | Meghatározza a memóriában tárolható kötegek másodpercenkénti számát, mielőtt elküldené a Cassandra API |
| spark.cassandra.connection.keep_alive_ms | 60000 | Meghatározza azt az időtartamot, ameddig a fel nem használt kapcsolatok elérhetők. | 

Módosítsa a paraméterek átviteli sebességét és a párhuzamosság mértékét a Spark-feladatokhoz várható számítási feladatok, valamint a Cosmos DB-fiókhoz kiépített átviteli sebesség alapján.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Csatlakozás Azure Cosmos DB Cassandra API a Sparkból

### <a name="cqlsh"></a>cqlsh
Az alábbi parancsok részletesen ismertetik, hogyan csatlakozhat az Azure CosmosDB Cassandra API a cqlsh-ból.  Ez az ellenőrzéshez hasznos, amikor a Sparkban lévő mintákon futtatja őket.<br>
**Linux/Unix/Mac rendszerből:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Azure Databricks
Az alábbi cikk a fürt kiépítés Azure Databricks, a fürt konfigurálását ismerteti Azure Cosmos DB Cassandra APIhoz való csatlakozáshoz, valamint több, a DDL-műveleteket, a DML-műveleteket és egyebeket magában foglaló jegyzetfüzethez.<BR>
[Azure Cosmos DB Cassandra API használata az Azure databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. Azure-HDInsight-Spark
Az alábbi cikk a HDinsight-Spark szolgáltatásra, a kiépítés, a Azure Cosmos DB Cassandra APIhoz való csatlakozásra, valamint a DDL-műveleteket, a DML-műveletekre és egyebekre kiterjedő több jegyzetfüzetet tartalmaz.<BR>
[Azure Cosmos DB Cassandra API használata az Azure HDInsight-Sparkból](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. a Spark-környezet általánosságban
A fenti szakaszok az Azure Spark-alapú Pásti-szolgáltatásokra jellemzőek, ez a szakasz minden általános Spark-környezetet magában foglal.  Az összekötő függőségeinek, importálásának és a Spark-munkamenet konfigurálásának részletes leírását az alábbiakban találja. A "Next Steps" (a következő lépések) szakasz a DDL-műveletek, a DML-műveletek és sok más kód mintáit tartalmazza.  

#### <a name="connector-dependencies"></a>Összekötő függőségei:

1. Adja hozzá a Maven-koordinátákat a [Spark Cassandra-összekötő](cassandra-spark-generic.md#dependencies-for-connectivity) beszerzéséhez
2. Adja hozzá a Maven-koordinátákat a [Azure Cosmos db Helper könyvtárához](cassandra-spark-generic.md#dependencies-for-connectivity) Cassandra API

#### <a name="imports"></a>Importálja

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Spark-munkamenet konfigurációja:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkek a Spark-integrációt mutatják be Azure Cosmos DB Cassandra APIokkal. 
 
* [DDL-műveletek](cassandra-spark-ddl-ops.md)
* [Műveletek létrehozása/beszúrása](cassandra-spark-create-ops.md)
* [Olvasási műveletek](cassandra-spark-read-ops.md)
* [Upsert-műveletek](cassandra-spark-upsert-ops.md)
* [Törlési műveletek](cassandra-spark-delete-ops.md)
* [Összesítési műveletek](cassandra-spark-aggregation-ops.md)
* [Táblázatos másolási műveletek](cassandra-spark-table-copy-ops.md)