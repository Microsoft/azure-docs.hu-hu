---
title: Integrálás a Apache Kafka összekapcsolásával – Azure Event Hubs | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Kafka az Azure Event Hubs for Kafka használatával.
ms.topic: how-to
ms.date: 01/06/2021
ms.openlocfilehash: f82dcdafa7921f4a994361371536b2f1ace7cbc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935155"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs"></a>Az Apache Kafka Connect-támogatás és az Azure Event Hubs integrálása
[Apache Kafka a kapcsolódás](https://kafka.apache.org/documentation/#connect) egy olyan keretrendszer, amellyel a rendszer bármilyen külső, például MySQL-, HDFS-és fájlrendszerbeli adatokhoz csatlakozhat, illetve exportálhatja azokat. Ez az oktatóanyag végigvezeti a Kafka csatlakozási keretrendszernek a Event Hubssal való használatával.

> [!WARNING]
> A Apache Kafka csatlakozási keretrendszer és az összekötők használata **nem jogosult a terméktámogatásra Microsoft Azureon keresztül**.
>
> Apache Kafka a kapcsolódás feltételezi, hogy a dinamikus konfigurációja a tömörített témakörökben található, máskülönben korlátlan adatmegőrzéssel. Az Azure Event Hubs [nem implementálja a tömörítést közvetítő szolgáltatásként](event-hubs-federation-overview.md#log-projections) , és mindig időalapú adatmegőrzési korlátot alkalmaz a megőrzött eseményekre, és az alapelvet, hogy az Azure Event Hubs valós idejű esemény-adatfolyam-kezelő motor, nem pedig hosszú távú adat-vagy konfigurációs tároló.
>
> Habár a Apache Kafka projekt kényelmes lehet a szerepkörök keverésével, az Azure azt hiszi, hogy az ilyen információk a legmegfelelőbb adatbázisban vagy konfigurációs tárolóban kezelhetők.
>
> Számos Apache Kafka kapcsolódási forgatókönyv működőképes lesz, de a Apache Kafka és az Azure Event Hubs adatmegőrzési modelljei közötti koncepcionális különbségek miatt előfordulhat, hogy bizonyos konfigurációk nem a várt módon működnek. 

Ez az oktatóanyag végigvezeti a Kafka-csatlakozás egy Event hub-vel való integrálásán és az alapszintű FileStreamSource és FileStreamSink-összekötők üzembe helyezésén. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Bár ezeket az összekötőket nem éles környezetben való használatra szánták, egy olyan teljes körű Kafka Connect-forgatókönyvet mutatnak be, amelyben az Azure Event Hubs Kafka-közvetítőként működik.

> [!NOTE]
> Ez a minta a [githubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect)érhető el.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * A Kafka Connect konfigurálása az Event Hubshoz
> * A Kafka Connect futtatása
> * Összekötők létrehozása

## <a name="prerequisites"></a>Előfeltételek
A bemutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Kafka-példány (1.1.1-es verzió, 2.11-es Scala verzió), amely a [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1) webhelyről tölthető le
- Az [Apache Kafkához készült Event Hubsot](./event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket is mindenképpen olvassa át.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása
Az Event Hubs-szolgáltatásokból való küldéshez és fogadáshoz szükség van egy Event Hubs-névtérre. A névtér és az Event hub létrehozásával kapcsolatos utasításokért tekintse meg az [Event hub létrehozása](event-hubs-create.md) című témakört. Szerezze be az Event Hubs kapcsolati sztringjét és teljes tartománynevét (FQDN) későbbi használatra. Útmutatásért lásd az [Event Hubs kapcsolati sztring lekérésével](event-hubs-get-connection-string.md) foglalkozó témakört. 

## <a name="clone-the-example-project"></a>A példaprojekt klónozása
Klónozza az Azure Event Hubs-adattárat, és keresse meg a tutorials/connect almappát: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>A Kafka Connect konfigurálása az Event Hubshoz
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


## <a name="run-kafka-connect"></a>A Kafka Connect futtatása

Ebben a lépésben helyileg el fog indítani egy Kafka Connect-feldolgozót elosztott módban, és az Event Hubsot fogja használni a fürtállapot fenntartásához.

1. Mentse helyileg a fenti `connect-distributed.properties` fájlt.  Ne felejtse el lecserélni a zárójelbe foglalt értékeket.
2. Keresse meg a Kafka-példány helyét a számítógépén.
4. Futtassa az `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` parancsot.  A Connect-feldolgozó REST API akkor áll készen az interakcióra, amikor meglátja az `'INFO Finished starting connectors and tasks'` szöveget. 

> [!NOTE]
> A Kafka-kapcsolat a Kafka AdminClient API-val automatikusan hozza létre a javasolt konfigurációkat tartalmazó témákat, beleértve a tömörítést is. A névtérből az Azure Portalon gyorsan ki lehet deríteni, hogy a Connect-feldolgozó belső témakörei automatikusan jöttek létre.
>
>A Kafka-kapcsolat belső témaköreinek **tömörítést kell használniuk**.  A Event Hubs csapat nem felelős a nem megfelelő konfigurációk kijavításában, ha a belső csatlakozási témakörök helytelenül vannak konfigurálva.

### <a name="create-connectors"></a>Összekötők létrehozása
Ez a szakasz végigvezeti a FileStreamSource és a FileStreamSink összekötő elindításán. 

1. Hozzon létre egy könyvtárat a bemeneti és kimeneti adatfájloknak.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Hozzon létre két fájlt: egyet a kiindulási adatokkal, amelyből a FileStreamSource összekötő olvas, és egy másikat, amelybe a FileStreamSink összekötő ír.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Hozzon létre egy FileStreamSource összekötőt.  Ne felejtse el lecserélni a kapcsos zárójelbe zárt kifejezést a kezdőkönyvtár elérési útjára.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    A fenti parancs futtatása után a `connect-quickstart` eseményközpontnak meg kell jelennie az Event Hubs-példányban.
4. Ellenőrizze a forrásösszekötő állapotát.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    Igény szerint a [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/releases) is ellenőrizheti, hogy beérkeztek-e az események a `connect-quickstart` témakörbe.

5. Hozzon létre egy FileStreamSink összekötőt.  Most se felejtse el lecserélni a kapcsos zárójelbe zárt kifejezést a kezdőkönyvtár elérési útjára.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Ellenőrizze a fogadó összekötő állapotát.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Ellenőrizze, hogy az adatok replikálva lettek-e a fájlok között, és hogy a két fájl adatai azonosak-e.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Felesleges tartalmak törlése
A Kafka Connect olyan eseményközpont-témaköröket hoz létre a konfigurációk, eltolások és állapotok tárolására, amelyek a Connect-fürt leállítása után is megmaradnak. Ha nem kívánja megőrizni ezeket, javasoljuk, hogy törölje a témaköröket. A bemutató során létrehozott `connect-quickstart` eseményközpontot is érdemes törölni.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Kafka-Event Hubsről, tekintse meg a következő cikkeket:  

- [Kafka-közvetítő tükrözése egy eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark csatlakoztatása egy eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink csatlakoztatása egy eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Az Akka Streams csatlakoztatása eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka fejlesztői útmutató az Azure-hoz Event Hubs](apache-kafka-developer-guide.md)
