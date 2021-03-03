---
title: Azure Cosmos DB Cassandra API gyakori hibáinak elhárítása
description: Ez a dokumentum a Azure Cosmos DB-ben észlelt gyakori problémák elhárításának módszereit tárgyalja Cassandra API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: f9b6e586879b8697660ced7aa6f1e75083e3ee29
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658571"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API gyakori problémáinak elhárítása
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A Cassandra API in Azure Cosmos DB egy kompatibilitási réteg, amely a népszerű nyílt forráskódú Apache Cassandra-adatbázishoz nyújt [vezetékes protokoll-támogatást](cassandra-support.md) , és [Azure Cosmos db](./introduction.md)-t működtet. A teljes körűen felügyelt natív szolgáltatásként a Azure Cosmos DB [garantálja a Cassandra API rendelkezésre állását, átviteli sebességét és konzisztenciáját](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) . Ezek a garanciák az Apache Cassandra örökölt implementációjában nem lehetségesek. Cassandra API emellett a nulla karbantartási platform működését és a nulla állásidő javítását is lehetővé teszi. Ennek megfelelően számos háttérbeli művelet különbözik az Apache Cassandra-től, ezért a gyakori hibák elkerüléséhez bizonyos beállítások és megközelítések javasoltak. 

Ez a cikk a Azure Cosmos DB Cassandra APIt használó alkalmazások általános hibáit és megoldásait ismerteti. Ha a hiba nem szerepel a lenti listában, és egy [támogatott művelet Cassandra APIban](cassandra-support.md)való végrehajtása során hiba lépett fel, ahol a hiba nem jelenik meg *natív Apache Cassandra használata* esetén, [hozzon létre egy Azure-támogatási kérelmet](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="nonodeavailableexception"></a>NoNodeAvailableException
Ez egy legfelső szintű burkoló kivétel, amely nagy számú lehetséges okot és belső kivételt okoz, amelyek közül számos lehet ügyfélhez kapcsolódó. 
### <a name="solution"></a>Megoldás
Néhány népszerű ok és megoldás a következő: 
- Az Azure-LoadBalancers üresjárati időkorlátja: Ez a következőképpen is megnyilvánulhat `ClosedConnectionException` . Ennek megoldásához állítsa az életben tartási beállítás beállítást az illesztőprogramban (lásd [alább](#enable-keep-alive-for-java-driver)), és növelje a Keep-Alive beállításokat az operációs rendszeren, vagy [állítsa be az üresjárati időtúllépést Azure Load Balancerban](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). 
- **Ügyfélalkalmazás erőforrás-kimerülése:** győződjön meg arról, hogy az ügyfélszámítógépek elegendő erőforrással rendelkeznek a kérelem végrehajtásához. 

## <a name="cannot-connect-to-host"></a>Nem lehet csatlakozni a gazdagéphez
A következő hibaüzenet jelenhet meg: `Cannot connect to any host, scheduling retry in 600000 milliseconds` . 

### <a name="solution"></a>Megoldás
Ez az ügyféloldali SNAT lehet. A probléma kizárásához kövesse a SNAT címen található lépéseket a [Kimenő kapcsolatok esetében](../load-balancer/load-balancer-outbound-connections.md) . Ez egy üresjárati időtúllépési probléma is lehet, ha az Azure Load Balancer alapértelmezés szerint 4 perc üresjárati időkorláttal rendelkezik. Tekintse meg a dokumentációt a terheléselosztó [üresjárati időkorlátjában](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). Engedélyezze a TCP-t – tartsa életben az illesztőprogram-beállításokat (lásd [alább](#enable-keep-alive-for-java-driver)), és állítsa az `keepAlive` operációs rendszer intervallumát 4 percnél kevesebbre.

 

## <a name="overloadedexception-java"></a>OverloadedException (Java)
A felhasználható kérelmek teljes száma meghaladja a tárterületre vagy a táblára kiépített kérések mennyiségét. Így a kérelmek szabályozása megtörténik.
### <a name="solution"></a>Megoldás
Érdemes megfontolni a Azure Portal a térközhez vagy a táblához rendelt átviteli sebesség méretezését (lásd [itt](manage-scale-cassandra.md) : méretezési műveletek a Cassandra APIban), vagy egy újrapróbálkozási házirendet is megvalósíthat. A Java esetében lásd: újrapróbálkozási minták a [v3. x illesztőprogramhoz](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) és a [v4. x illesztőprogramhoz](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Lásd még: [Az Azure Cosmos Cassandra Extensions for Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-even-with-sufficient-throughput"></a>OverloadedException még elegendő átviteli sebességgel 
A rendszer úgy tűnik, hogy a kérések mennyisége és/vagy az elfogyasztott kérések egységnyi ára alapján a kérelmek szabályozása és a kérések mennyiségének kiszámításához elegendő átviteli sebesség A vártnál több lehetséges oka van:
- **Séma szintű műveletek:** Cassandra API a séma szintű műveletekhez (CREATE TABLE, ALTER TABLE, DROP TABLE) implementálja a rendszer átviteli sebességének költségvetését. Ennek a költségvetésnek elegendőnek kell lennie az éles rendszer sémájának műveleteihez. Ha azonban nagy számú séma-szintű művelet van, akkor lehetséges, hogy túllépi ezt a korlátot. Mivel ez a költségkeret nem felhasználó által vezérelt, érdemes megfontolni a futtatott séma-műveletek számának csökkentését. Ha a művelet végrehajtása nem oldja meg a problémát, vagy nem valósítható meg a munkaterhelés esetében, [hozzon létre egy Azure-támogatási kérelmet](../azure-portal/supportability/how-to-create-azure-support-request.md).
- **Adatok eldöntése:** ha az átviteli sebesség Cassandra APIban van kiépítve, egyenlően oszlik meg a fizikai partíciók között, és mindegyik fizikai partíciónak van felső korlátja. Ha nagy mennyiségű adatot szúr be vagy kérdez le egy adott partícióból, lehetséges, hogy az adott táblára vonatkozóan nagy mennyiségű teljes átviteli sebesség (kérelmek egysége) kiépítés miatt korlátozott. Tekintse át az adatmodellt, és győződjön meg arról, hogy nem rendelkezik túlzott mértékű döntéssel, ami okozhatja a gyors partíciót. 

## <a name="intermittent-connectivity-errors-java"></a>Átmeneti kapcsolódási hibák (Java) 
A kapcsolatok váratlanul elvesznek vagy időtúllépésnek számítanak.

### <a name="solution"></a>Megoldás 
A Javához készült Apache Cassandra-illesztőprogramok két natív újracsatlakoztatási szabályzatot biztosítanak: `ExponentialReconnectionPolicy` és `ConstantReconnectionPolicy` . A mező alapértelmezett értéke: `ExponentialReconnectionPolicy`. Azure Cosmos DB Cassandra API esetében azonban ajánlott `ConstantReconnectionPolicy` 2 másodperces késleltetés. Tekintse meg a Java v4. x illesztőprogramhoz tartozó [illesztőprogram-dokumentációt](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  , és [itt](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) a Java 3. x útmutatást lásd: a [ReconnectionPolicy konfigurálása a Java-illesztőprogramokhoz az](#configuring-reconnectionpolicy-for-java-driver) alábbi példákban.

## <a name="error-with-load-balancing-policy"></a>Hiba a terheléselosztási házirenddel

Ha végrehajtotta a terheléselosztási házirendet a Java Datastax-illesztőprogram v3. x verziójában, az alábbihoz hasonló kóddal:

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

Ha az érték `withLocalDc()` nem felel meg a kapcsolattartási pont adatközpontjának, akkor előfordulhat, hogy a következő hibát tapasztalja: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` . 

### <a name="solution"></a>Megoldás 
A [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java) megvalósítása (Előfordulhat, hogy a működéséhez frissítenie kell a datastax alverziót):

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="count-fails-on-large-table"></a>A Count sikertelen a nagyméretű táblában
Ha nagy mennyiségű `select count(*) from table` sorhoz futtat vagy hasonló, a kiszolgáló időtúllépést mutat.

### <a name="solution"></a>Megoldás 
Ha helyi CQLSH-ügyfelet használ, próbálja meg módosítani a `--connect-timeout` vagy a `--request-timeout` beállításokat (további részletek [itt](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)találhatók). Ha ez nem elegendő, és a szám továbbra is időtúllépést okoz, akkor a Azure Cosmos DB háttérbeli telemetria rekordjainak számát a Azure Portal metrikák lapján, a metrika kiválasztásával `document count` , majd az adatbázishoz vagy a gyűjteményhez tartozó szűrő hozzáadásával (a Azure Cosmos db egy analóg táblája) adja meg. Ezután az eredményül kapott gráf fölé helyezheti azt az időpontot, amikor a rekordok számát meg szeretné jeleníteni.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="metrikák nézet":::


## <a name="configuring-reconnectionpolicy-for-java-driver"></a>A ReconnectionPolicy konfigurálása Java-illesztőprogramhoz

### <a name="version-3x"></a>3. x verzió

A Java-illesztőprogram 3. x verziójának esetében konfigurálja az újrakapcsolódási házirendet a fürt objektum létrehozásakor:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>4. x verzió

A Java-illesztőprogram 4-es. x verziójához konfigurálja az újracsatlakoztatási házirendet a következő fájl beállításainak felülbírálásával `reference.conf` :

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-java-driver"></a>Keep-Alive engedélyezése a Java-illesztőprogramhoz

### <a name="version-3x"></a>3. x verzió

A Java-illesztőprogram 3. x verziójában állítsa a Keep-Alive beállítást a fürt objektum létrehozásakor, és győződjön meg arról, hogy a Keep-Alive [engedélyezve van az operációs rendszeren](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>4. x verzió

A Java-illesztőprogram 4-es. x verziójához állítsa be a Keep-Alive beállítást a beállítások felülbírálása beállítással, `reference.conf` és győződjön meg arról, hogy a Keep-Alive [engedélyezve van az operációs rendszeren](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg Azure Cosmos DB Cassandra API [támogatott szolgáltatásaival](cassandra-support.md) .
- Megtudhatja, hogyan [telepíthet át natív Apache cassandraről Azure Cosmos DBra Cassandra API](cassandra-migrate-cosmos-db-databricks.md)
