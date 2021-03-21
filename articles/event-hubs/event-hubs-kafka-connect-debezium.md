---
title: Az Azure Event Hubs integrációja Apache Kafka az adatváltozások rögzítése Debezium
description: Ez a cikk tájékoztatást nyújt arról, hogyan használható a Debezium az Azure Event Hubs for Kafka szolgáltatással.
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 01/06/2021
ms.openlocfilehash: 0ad1df23e71e652f7d380ffbabb542b81954e038
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935172"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-with-debezium-for-change-data-capture"></a>Az Azure Event Hubs Apache Kafka-csatlakozási támogatásának integrálása az adatváltozások rögzítésére szolgáló Debezium

Az **adatváltozások rögzítése (CDC)** a létrehozási, frissítési és törlési műveletekre reagáló, az adatbázistáblák sorainak változásainak nyomon követésére szolgáló módszer. A [Debezium](https://debezium.io/) egy elosztott platform, amely a különböző adatbázisokban elérhető adatváltozás-rögzítési funkciókra épül (például a [PostgreSQL logikai dekódolása](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)). Olyan [Kafka csatlakozási összekötőket](https://debezium.io/documentation/reference/1.2/connectors/index.html) biztosít, amelyek az adatbázistábla (ok) sorban álló változásaira koppintanak, és a [Apache Kafkaba](https://kafka.apache.org/)küldendő esemény-adatfolyamokra konvertálják őket.

> [!WARNING]
> A Apache Kafka csatlakozási keretrendszer, valamint a Debezium platform és az összekötők használata **nem jogosult a terméktámogatásra Microsoft Azureon keresztül**.
>
> Apache Kafka a kapcsolódás feltételezi, hogy a dinamikus konfigurációja a tömörített témakörökben található, máskülönben korlátlan adatmegőrzéssel. Az Azure Event Hubs [nem implementálja a tömörítést közvetítő szolgáltatásként](event-hubs-federation-overview.md#log-projections) , és mindig időalapú adatmegőrzési korlátot alkalmaz a megőrzött eseményekre, és az alapelvet, hogy az Azure Event Hubs valós idejű esemény-adatfolyam-kezelő motor, nem pedig hosszú távú adat-vagy konfigurációs tároló.
>
> Habár a Apache Kafka projekt kényelmes lehet a szerepkörök keverésével, az Azure azt hiszi, hogy az ilyen információk a legmegfelelőbb adatbázisban vagy konfigurációs tárolóban kezelhetők.
>
> Számos Apache Kafka kapcsolódási forgatókönyv működőképes lesz, de a Apache Kafka és az Azure Event Hubs adatmegőrzési modelljei közötti koncepcionális különbségek miatt előfordulhat, hogy bizonyos konfigurációk nem a várt módon működnek. 

Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be az Azure-beli adatváltozás-rögzítési rendszert az Azure [Event Hubs](./event-hubs-about.md?WT.mc_id=devto-blog-abhishgu) (a Kafka esetében), [Az Azure db for PostgreSQL és a](../postgresql/overview.md) Debezium használatával. A [Debezium PostgreSQL-összekötővel](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html) továbbítja a PostgreSQL-ből származó adatbázis-módosításokat az Azure-beli Kafka-témakörökbe Event Hubs

> [!NOTE]
> Ez a cikk az *engedélyezési* feltételekre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * Azure Database for PostgreSQL beállítása és konfigurálása
> * A Kafka-csatlakozás konfigurálása és futtatása a Debezium PostgreSQL-összekötővel
> * Az adatváltozások rögzítésének tesztelése
> * Választható Adatváltozási események felhasználása `FileStreamSink` összekötővel

## <a name="pre-requisites"></a>Előfeltételek
A lépés elvégzéséhez a következőket kell tennie:

- Egy Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).
- Linux/MacOS
- Kafka-példány (1.1.1-es verzió, 2.11-es Scala verzió), amely a [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1) webhelyről tölthető le
- Az [Apache Kafkához készült Event Hubsot](./event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket is mindenképpen olvassa át.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása
Az Event Hubs-szolgáltatásokból való küldéshez és fogadáshoz szükség van egy Event Hubs-névtérre. A névtér és az Event hub létrehozásával kapcsolatos utasításokért tekintse meg az [Event hub létrehozása](event-hubs-create.md) című témakört. Szerezze be az Event Hubs kapcsolati sztringjét és teljes tartománynevét (FQDN) későbbi használatra. Útmutatásért lásd az [Event Hubs kapcsolati sztring lekérésével](event-hubs-get-connection-string.md) foglalkozó témakört. 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>Azure Database for PostgreSQL beállítása és konfigurálása
A [Azure Database for PostgreSQL](../postgresql/overview.md) a nyílt forráskódú PostgreSQL adatbázismotor közösségi verzióján alapuló, a következő két telepítési lehetőségben elérhető: egyetlen kiszolgáló és nagy kapacitású (Citus). Az [alábbi utasításokat követve](../postgresql/quickstart-create-server-database-portal.md) hozzon létre egy Azure Database for PostgreSQL-kiszolgálót a Azure Portal használatával. 

## <a name="setup-and-run-kafka-connect"></a>A Kafka-kapcsolat beállítása és futtatása
Ez a szakasz a következő témaköröket tartalmazza:

- Debezium-összekötő telepítése
- A Kafka-kapcsolat konfigurálása Event Hubshoz
- A Kafka csatlakozási fürt elindítása a Debezium-összekötővel

### <a name="download-and-setup-debezium-connector"></a>Debezium-összekötő letöltése és beállítása
Az összekötő letöltéséhez és beállításához kövesse az [Debezium dokumentációjának](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) legfrissebb utasításait.

- Töltse le az összekötő beépülő moduljának archívumát. Az összekötő verziójának letöltéséhez például `1.2.0` használja a következő hivatkozást: https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- Bontsa ki a JAR-fájlokat, és másolja őket a [Kafka kapcsolódási beépülő modul. Path](https://kafka.apache.org/documentation/#connectconfigs)fájlba.


### <a name="configure-kafka-connect-for-event-hubs"></a>A Kafka Connect konfigurálása az Event Hubshoz
A Kafka Connect teljesítményének a Kafkából az Event Hubsba való átirányításához minimális újrakonfigurálásra van szükség.  Az alábbi `connect-distributed.properties` példa bemutatja, hogyan konfigurálhatja a Connectet úgy, hogy az Event Hubs Kafka-végpontjával hitelesítsen és kommunikáljon:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

> [!IMPORTANT]
> Cserélje le a helyére `{YOUR.EVENTHUBS.CONNECTION.STRING}` a Event Hubs névtérhez tartozó kapcsolatok karakterláncát. A kapcsolatok karakterláncának beolvasásával kapcsolatos utasításokért lásd: [Event Hubs-kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md). Íme egy példa a konfigurációra: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-kafka-connect"></a>A Kafka Connect futtatása
Ebben a lépésben helyileg el fog indítani egy Kafka Connect-feldolgozót elosztott módban, és az Event Hubsot fogja használni a fürtállapot fenntartásához.

1. Mentse helyileg a fenti `connect-distributed.properties` fájlt.  Ne felejtse el lecserélni a zárójelbe foglalt értékeket.
2. Keresse meg a Kafka-példány helyét a számítógépén.
3. Futtassa `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` a parancsot, és várja meg, amíg a fürt elindul.

> [!NOTE]
> A Kafka-kapcsolat a Kafka AdminClient API-val automatikusan hozza létre a javasolt konfigurációkat tartalmazó témákat, beleértve a tömörítést is. A névtérből az Azure Portalon gyorsan ki lehet deríteni, hogy a Connect-feldolgozó belső témakörei automatikusan jöttek létre.
>
> A Kafka-kapcsolat belső témaköreinek **tömörítést kell használniuk**.  A Event Hubs csapat nem felelős a nem megfelelő konfigurációk kijavításában, ha a belső csatlakozási témakörök helytelenül vannak konfigurálva.

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>A Debezium PostgreSQL forrás-összekötő konfigurálása és elindítása

Hozzon létre egy konfigurációs fájlt ( `pg-source-connector.json` ) a PostgreSQL forrás-összekötőhöz – az értékeket cserélje le az Azure PostgreSQL-példányra.

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> `database.server.name` az attribútum egy logikai név, amely azonosítja és megadja a megfigyelt PostgreSQL adatbázis-kiszolgáló/fürt névterét. Részletes információkért lásd a [Debezium dokumentációját](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name) .

Az összekötő egy példányának létrehozásához használja a Kafka kapcsolódási REST API végpontját:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

Az összekötő állapotának ellenõrzése:

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>Az adatváltozások rögzítésének tesztelése
Az adatváltozások rögzítésének működés közbeni megtekintéséhez az Azure PostgreSQL-adatbázisban létre kell hoznia/frissítenie/törölnie kell a rekordokat.

Először kapcsolódjon az Azure PostgreSQL-adatbázishoz (az alábbi példa a [psql](https://www.postgresql.org/docs/12/app-psql.html)-t használja)

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**Tábla létrehozása és rekordok beszúrása**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

Az összekötőnek mostantól a művelethez kell tartoznia, és adatváltozási eseményeket kell küldenie egy Event Hubs témakörre az alábbi na, e `my-server.public.todos` értékkel, feltételezve, hogy a `my-server` `database.server.name` és az `public.todos` a tábla, amelynek a nyomon követését végzi ( `table.whitelist` konfigurációként).

**Event Hubs témakör bejelölése**

Tekintse át a témakör tartalmát, és győződjön meg róla, hogy minden a várt módon működik-e. Az alábbi példa a [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) szolgáltatást használja, de az [itt felsorolt lehetőségek bármelyikével létrehozhat egy fogyasztót](apache-kafka-developer-guide.md) is

Hozzon létre egy nevű fájlt `kafkacat.conf` a következő tartalommal:

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> Frissítés `metadata.broker.list` és `sasl.password` attribútumok `kafkacat.conf` Event Hubsi információként. 

Egy másik terminálon indítson el egy fogyasztót:

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

A táblázatba felvett sorokra adott válaszként meg kell jelennie a PostgreSQL-ben generált adatváltozási eseményeket jelképező JSON-adattartalomnak `todos` . Itt látható a hasznos adatok részlete:


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

Az esemény a és a `payload` `schema` (rövid ideig elhagyva) értékből áll. `payload`Figyelje meg, hogy a létrehozási művelet ( `"op": "c"` ) látható- `"before": null` e – azt jelenti, hogy az egy újonnan létrehozott `INSERT` sor, amely a sorban lévő oszlopok értékeit adja meg a PostgreSQL- `after` példány metaadatait, amelyekből `source` az eseményt felvettek stb.

Ugyanezt a frissítési vagy törlési művelettel is kipróbálhatja, és betekintheti az adatok módosítása eseményeket. Például a feladat állapotának (feltéve, hogy `configure and install connector` ) frissítéséhez `id` `3` :

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a>Választható Az FileStreamSink-összekötő telepítése
Most, hogy az összes `todos` tábla módosult Event Hubs témakörben, a FileStreamSink-összekötőt fogjuk használni (amely alapértelmezés szerint elérhető a Kafka-csatlakozásban) az események felhasználásához.

Hozzon létre egy konfigurációs fájlt ( `file-sink-connector.json` ) az összekötőhöz – cserélje le az `file` attribútumot a fájlrendszerre.

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

Az összekötő létrehozásához és az állapotának vizsgálatához:

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

Adatbázisrekordok beszúrása/frissítése/törlése és a beállított kimeneti fogadó fájlban lévő rekordok figyelése:

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>Felesleges tartalmak törlése
A Kafka Connect olyan eseményközpont-témaköröket hoz létre a konfigurációk, eltolások és állapotok tárolására, amelyek a Connect-fürt leállítása után is megmaradnak. Ha nem kívánja megőrizni ezeket, javasoljuk, hogy törölje a témaköröket. Érdemes törölni az Event hub-t is `my-server.public.todos` , amelyet a folyamat során hozott létre.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Kafka-Event Hubsről, tekintse meg a következő cikkeket:  

- [Kafka-közvetítő tükrözése egy eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark csatlakoztatása egy eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink csatlakoztatása egy eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Az Akka Streams csatlakoztatása eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka fejlesztői útmutató az Azure-hoz Event Hubs](apache-kafka-developer-guide.md)