---
title: Adatok migrálása a PostgreSQL-ből Azure Cosmos DB Cassandra API-fiókba a Apache Kafka használatával
description: Ismerje meg, hogy a Kafka-kapcsolódás használatával hogyan szinkronizálhat adatokat a PostgreSQL-ről Azure Cosmos DB Cassandra API valós időben.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 0038219ee8c1721ff5ab2be76231d33d2bd9064d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98203065"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Adatok migrálása a PostgreSQL-ből Azure Cosmos DB Cassandra API-fiókba a Apache Kafka használatával
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A Azure Cosmos DB Cassandra API az Apache Cassandra-on futó nagyvállalati munkaterhelések számára nagyszerű választás, többek között a következők miatt:

* **Jelentős költségmegtakarítás:** A költségeket megtakaríthatja Azure Cosmos DBával, amely magában foglalja a virtuális gépek, a sávszélesség és az esetlegesen érvényes Oracle-licencek költségeit is. Emellett nem szükséges az adatközpontok, a kiszolgálók, az SSD-tárolók, a Hálózatkezelés és a villamosenergia-költségek kezelése.

* **Jobb méretezhetőség és rendelkezésre állás:** Kiküszöböli az egyes meghibásodási pontokat, az alkalmazások jobb méretezhetőségét és rendelkezésre állását.

* **A felügyelet és a figyelés nem jelent terhelést:** Teljes körűen felügyelt felhőalapú szolgáltatásként Azure Cosmos DB eltávolítja a rengeteg beállítás felügyeletének és figyelésének terhét.

A [Kafka-kapcsolat](https://kafka.apache.org/documentation/#connect) egy olyan platform, amellyel a [Apache Kafka](https://kafka.apache.org/) és más rendszerek között méretezhető és megbízható módon továbbíthatja az adatátvitelt. Többek között a Polcos összekötőket is támogatja, ami azt jelenti, hogy nincs szükség egyéni kódra a külső rendszerek Apache Kafka-nal való integrálásához.

Ez a cikk bemutatja, hogyan használható a Kafka-összekötők egy olyan adatfolyamat beállításához, amely folyamatosan szinkronizálja a rekordokat egy olyan rokon adatbázisból, mint például a [PostgreSQL](https://www.postgresql.org/) [Azure Cosmos db Cassandra API](cassandra-introduction.md).

## <a name="overview"></a>Áttekintés

A jelen cikkben bemutatott teljes körű folyamat áttekintését itt tekintheti át.

A PostgreSQL-táblázatban lévő adatApache Kafka a [Debezium PostgreSQL-összekötő](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html)használatával küldi el a rendszer, amely egy Kafka csatlakozási **forrás** -összekötő. A PostgreSQL-tábla rekordjainak beszúrása, frissítése vagy törlése eseményként lesz rögzítve, `change data` és elküldve a Kafka-témakör (ek) nek. A [DataStax Apache Kafka-összekötő](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) (Kafka **összekapcsolási összekötője** ) a folyamat második részét képezi. Szinkronizálja a Kafka-témakör adatváltozási eseményeit Azure Cosmos DB Cassandra API táblákat.

> [!NOTE]
> A DataStax Apache Kafka-összekötő bizonyos funkcióinak használatával több táblázatba küldhet adatküldést. Ebben a példában az összekötő segít megőrizni a változási adatrekordokat két Cassandra-táblára, amelyek támogatják a különböző lekérdezési követelményeket.

## <a name="prerequisites"></a>Előfeltételek

* [Azure Cosmos DB Cassandra API fiók kiépítése](create-cassandra-dotnet.md#create-a-database-account)
* [Cqlsh vagy üzemeltetett rendszerhéj használata az ellenőrzéshez](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 vagy újabb
* [Docker](https://www.docker.com/) (nem kötelező)

## <a name="base-setup"></a>Alapkonfiguráció

### <a name="set-up-postgresql-database-if-you-havent-already"></a>Ha még nem tette meg, állítsa be a PostgreSQL-adatbázist. 

Ez lehet egy meglévő helyszíni adatbázis, vagy [letöltheti és telepítheti az egyiket](https://www.postgresql.org/download/) a helyi gépen. [Docker-tárolót](https://hub.docker.com/_/postgres)is használhat.

Tároló elindítása:

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

Kapcsolódjon a PostgreSQL-példányhoz az [`psql`](https://www.postgresql.org/docs/current/app-psql.html) ügyfél használatával:

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

Hozzon létre egy táblázatot a mintavételi adatok tárolásához:

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>A Azure Portal használatával hozza létre a Cassandra Space és a bemutató alkalmazáshoz szükséges táblákat.

> [!NOTE]
> Használja ugyanazt a szóköz és táblanév nevet az alábbiak szerint

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>Telepítő Apache Kafka 

Ez a cikk egy helyi fürtöt használ, de bármilyen más lehetőséget is választhat. [Töltse le a Kafka](https://kafka.apache.org/downloads)-t, csomagolja ki, indítsa el a Zookeeper és a Kafka-fürtöt.

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>Összekötők beállítása

Telepítse a Debezium PostgreSQL-t és a DataStax Apache Kafka-összekötőt. Töltse le a Debezium PostgreSQL Connector beépülő modul archívumát. Ha például az összekötő verziójának 1.3.0 szeretné letölteni (a legújabbat az írás időpontjában), használja [ezt a hivatkozást](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.3.0.Final-plugin.tar.gz). Töltse le a DataStax Apache Kafka-összekötőt [erről a hivatkozásról](https://downloads.datastax.com/#akc).

Bontsa ki az összekötő-archívumokat, és másolja a JAR-fájlokat a [Kafka kapcsolódási beépülő modul. Path](https://kafka.apache.org/documentation/#connectconfigs)fájlba.


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> További részletekért tekintse meg a [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) és a [DataStax](https://docs.datastax.com/en/kafka/doc/) dokumentációját.

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>A Kafka-kapcsolat konfigurálása és az adatfolyamat elindítása

### <a name="start-kafka-connect-cluster"></a>A Kafka csatlakozási fürt elindítása

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>A PostgreSQL Connector-példány elindítása

Mentse az összekötő konfigurációját (JSON) egy fájlba, például `pg-source-config.json`

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

A PostgreSQL Connector-példány elindítása:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> A törléséhez a következőt használhatja: `curl -X DELETE http://localhost:8083/connectors/pg-orders-source`


### <a name="insert-data"></a>Adat beszúrása

A `orders_info` tábla tartalmazza a rendelés részleteit, például a megrendelés azonosítóját, az ügyfél azonosítóját, a várost stb. Az alábbi SQL használatával töltse fel a táblázatot véletlenszerű adatokkal.

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

10 rekordot kell beszúrnia a táblába. Ügyeljen rá, hogy az alább felsorolt rekordok számát a követelmények szerint frissítse `generate_series(1, 10)` , például a rekordok beszúrásához használja a következőt: `100``generate_series(1, 100)`

A megerősítéshez:

```bash
select * from retail.orders_info;
```

Az adatváltozás-rögzítési események keresése a Kafka-témakörben

> [!NOTE]
> Vegye figyelembe, hogy a témakör neve az `myserver.retail.orders_info` [összekötői konvenciónak](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names) megfelelően

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

Az adatváltozási eseményeket JSON formátumban kell látnia.

### <a name="start-datastax-apache-kafka-connector-instance"></a>DataStax Apache Kafka-összekötő példányának elindítása

Mentse az összekötő konfigurációját (JSON) egy fájlba, `cassandra-sink-config.json` és frissítse a tulajdonságokat a környezete szerint.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

Az összekötő-példány elindítása:

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Az összekötőnek a műveletnek kell megjelennie, és a PostgreSQL-ről a végpontok közötti folyamat Azure Cosmos DB működőképes lesz.

### <a name="query-azure-cosmos-db"></a>Az Azure Cosmos DB lekérdezése

Keresse meg a Cassandra-táblákat Azure Cosmos DB. Íme néhány a következő lekérdezésekkel:

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

Továbbra is további adatokat szúrhat be a PostgreSQL-be, és ellenőrizheti, hogy a rekordok szinkronizálva vannak-e Azure Cosmos DB.

## <a name="next-steps"></a>Következő lépések

* [Apache Kafka-és Azure Cosmos DB-Cassandra API integrálása a Kafka-kapcsolattal](cassandra-kafka-connect.md)
* [Az Azure Event Hubs (előzetes verzió) Apache Kafka-csatlakozási funkciójának integrálása az adatváltozások rögzítésének Debezium](../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Adatok migrálása az Oracle-ből Azure Cosmos DB Cassandra API a Blitzz használatával](oracle-migrate-cosmos-db-blitzz.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md) 
* [A partíciós kulcs ajánlott eljárásai](partitioning-overview.md#choose-partitionkey)
* [Ru/s becslése a Azure Cosmos db Capacity Planner cikkei alapján](estimate-ru-with-capacity-planner.md)

