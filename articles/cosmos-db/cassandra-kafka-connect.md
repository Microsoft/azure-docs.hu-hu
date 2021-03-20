---
title: Apache Kafka-és Azure Cosmos DB-Cassandra API integrálása a Kafka-kapcsolattal
description: Ismerje meg, hogyan töltheti be a Kafka adatait Azure Cosmos DB Cassandra API a DataStax Apache Kafka Connector használatával
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 25972ba2bb30c39838c4822a42af292e8d8b1dba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803629"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>Adatok beolvasása Apache Kafkaból Azure Cosmos DB Cassandra API a Kafka-kapcsolat használatával
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A meglévő Cassandra-alkalmazások könnyen dolgozhatnak a [Azure Cosmos DB Cassandra API](cassandra-introduction.md) a [CQLv4-illesztőprogram kompatibilitása](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)miatt. Ezt a képességet úgy használhatja ki, hogy integrálja a streaming platformokat, például a [Apache Kafka](https://kafka.apache.org/) és az adatAzure Cosmos DBba.

Apache Kafkaban (témakörökben) lévő adatmennyiség csak más alkalmazások általi felhasználás vagy más rendszerbe való betöltés esetén hasznos. A [Kafka producer/fogyasztói](https://kafka.apache.org/documentation/#api) API-k segítségével létrehozhat egy megoldást, amely az [Ön által választott nyelven és ügyfél-SDK-](https://cwiki.apache.org/confluence/display/KAFKA/Clients)val lehetséges. A Kafka-kapcsolat alternatív megoldást kínál. Ez egy platform, amely a Apache Kafka és más rendszerek közötti adatátvitelt méretezhető és megbízható módon biztosítja. Mivel a Kafka-csatlakozás támogatja az olyan polcos összekötőket, mint a Cassandra, nem kell egyéni kódot írnia a Kafka Azure Cosmos DB Cassandra API való integrálásához. 

Ebben a cikkben a nyílt forráskódú [DataStax Apache Kafka-összekötőt](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html)fogjuk használni, amely a Kafka-kapcsolati keretrendszeren belül működik, hogy egy Kafka-témakörből származó rekordokat egy vagy több Cassandra-tábla soraiba foglalja. A példa egy újrafelhasználható telepítőt biztosít a Docker-összeállítás használatával. Ez elég kényelmes, mivel lehetővé teszi az összes szükséges összetevő helyi indítását egyetlen paranccsal. Ezek az összetevők többek között a Kafka, a Zookeeper, a Kafka-összekötő és a Sample adatgenerátor-alkalmazás.

Itt látható az összetevők és a szolgáltatási definíciók részletezése – a GitHub-tárház teljes `docker-compose` fájljára [](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml)hivatkozhat.

- A Kafka és a Zookeeper [debezium](https://hub.docker.com/r/debezium/kafka/) -lemezképeket használ.
- A Docker-tárolóként való futtatáshoz a DataStax Apache Kafka-összekötő egy meglévő Docker-rendszerkép- [debezium/Connector-Base-](https://github.com/debezium/docker-images/tree/master/connect-base/1.2)ra épül. Ez a rendszerkép tartalmazza a Kafka és a Kafka-kapcsolati kódtárak telepítését, így könnyen használhatók egyéni összekötők hozzáadásához. Tekintse át a [Docker](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile).
- A `data-generator` szolgáltatás a Kafka-témakörben véletlenszerűen generált (JSON) adathalmazokat tartalmaz `weather-data` . A kódot és a GitHub-tárházat a következő címen tekintheti meg `Dockerfile` : [](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/)

## <a name="prerequisites"></a>Előfeltételek

* [Azure Cosmos DB Cassandra API fiók kiépítése](create-cassandra-dotnet.md#create-a-database-account)

* [Cqlsh vagy üzemeltetett rendszerhéj használata az ellenőrzéshez](cassandra-support.md#hosted-cql-shell-preview)

* A [Docker](https://docs.docker.com/get-docker/) és a [Docker-összeállítás](https://docs.docker.com/compose/install) telepítése

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>Lemezterület létrehozása, táblák és az integrációs folyamat elindítása

A Azure Portal használatával hozza létre a Cassandra Space és a bemutató alkalmazáshoz szükséges táblákat.

> [!NOTE]
> Használja ugyanazt a szóköz és táblanév nevet az alábbiak szerint

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

A GitHub-tárház klónozása:

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

Az összes szolgáltatás elindítása:

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> A tárolók letöltése és elindítása eltarthat egy ideig: ez csak egy egyszeri folyamat.

Annak ellenőrzése, hogy az összes tároló elindult-e:

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

Az adatgenerátor-alkalmazás elkezdi bepumpálni az adatgyűjtést a `weather-data` Kafka-témakörbe. Azt is megteheti, hogy a gyors józan ész ellenőrzése megerősítve. Betekintés a Kafka-kapcsolat feldolgozóját futtató Docker-tárolóba:


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

Miután bejelentkezett a tároló-rendszerhéjba, csak indítsa el a szokásos Kafka-konzol felhasználói folyamatát, és látnia kell, hogy milyen időjárási információk (JSON formátumban) áramlanak.

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Cassandra mosogató-összekötő beállítása

Másolja az alábbi JSON-tartalmat egy fájlba (nevezze el `cassandra-sink-config.json` ). A telepítés során frissítenie kell, és a szakasz további részében útmutatást talál a jelen témakörben.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

Itt látható az attribútumok összegzése:

**Alapszintű kapcsolat**

- `contactPoints`: adja meg Cosmos DB Cassandra kapcsolódási pontját
- `loadBalancing.localDc`: adja meg Cosmos DB-fiók régióját, például: Délkelet-Ázsia
- `auth.username`: adja meg a felhasználónevet
- `auth.password`: adja meg a jelszót
- `port`: adja meg a port értékét (ez `10350` nem `9042` . hagyja meg a következőt)

**SSL-konfiguráció**

Azure Cosmos DB a [biztonságos kapcsolatot](database-security.md) kényszeríti az SSL-kapcsolaton keresztül, és a Kafka csatlakozási összekötő támogatja az SSL-t is.

- `ssl.keystore.path`: a JDK-tároló elérési útja a tárolóban – `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`: JDK-tároló (alapértelmezett) jelszó
- `ssl.hostnameValidation`: Bekapcsoljuk a saját csomópont állomásneve érvényesítését
- `ssl.provider`: `JDK` SSL-szolgáltatóként van használatban

**Általános paraméterek**

- `key.converter`: A karakterlánc-átalakítót használjuk. `org.apache.kafka.connect.storage.StringConverter`
- `value.converter`: mivel a Kafka-témakörökben szereplő információ JSON, a `org.apache.kafka.connect.json.JsonConverter`
- `value.converter.schemas.enable`: Mivel a JSON-adattartalomhoz nem tartozik séma társítva (a bemutató alkalmazáshoz), meg kell utasítania a Kafka-kapcsolódást, hogy ne keressen sémát úgy, hogy ezt az attribútumot állítsa be `false` . Ha ezt nem teszi meg, a rendszer hibákat fog eredményezni.

### <a name="install-the-connector"></a>Az összekötő telepítése

Telepítse az összekötőt a Kafka-kapcsolat REST-végpontjának használatával:

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Az állapot ellenõrzése:

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

Ha minden jól megy, az összekötőnek el kell kezdenie a varázsát. Hitelesítenie kell Azure Cosmos DB és el kell kezdenie az adatok betöltését a Kafka-témakörből ( `weather-data` ) a Cassandra Tables – `weather.data_by_state` és `weather.data_by_station`

Mostantól a táblákban is lekérdezheti az adatlekérdezéseket. Lépjen a Azure Portalre, és hozza létre a Azure Cosmos DB-fiókjához tartozó üzemeltetett CQL-rendszerhéjt.

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="CQLSH megnyitása":::

## <a name="query-data-from-azure-cosmos-db"></a>Adatok lekérdezése Azure Cosmos DB

Keresse meg a `data_by_state` és a `data_by_station` táblákat. Íme néhány példa a kezdéshez szükséges lekérdezésekre:

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md) 
* [A partíciós kulcs ajánlott eljárásai](partitioning-overview.md#choose-partitionkey)
* [Ru/s becslése a Azure Cosmos db Capacity Planner cikkei alapján](estimate-ru-with-capacity-planner.md)
