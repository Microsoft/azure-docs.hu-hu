---
title: A Azure Cosmos DB gyakori hibáinak elhárítása Cassandra API
description: Ez a cikk a Azure Cosmos DB Cassandra API gyakori problémáit és azok hibaelhárítását ismerteti.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: e81ab2539527c9d5c5cf2c6bff77fd19887103cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967353"
---
# <a name="troubleshoot-common-issues-in-the-azure-cosmos-db-cassandra-api"></a>A Azure Cosmos DB gyakori problémáinak elhárítása Cassandra API

[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A Cassandra API a [Azure Cosmos db](./introduction.md) egy kompatibilitási réteg, amely a nyílt forráskódú Apache Cassandra-adatbázishoz nyújt [vezetékes protokollt](cassandra-support.md) .

Ez a cikk a Azure Cosmos DB Cassandra APIt használó alkalmazások általános hibáit és megoldásait ismerteti. Ha a hiba nem szerepel a felsorolásban, és hibát tapasztal, amikor egy [támogatott műveletet hajt végre a Cassandra-ben](cassandra-support.md), de a hiba nem jelenik meg natív Apache Cassandra használata esetén, [hozzon létre egy Azure-támogatási kérelmet](../azure-portal/supportability/how-to-create-azure-support-request.md).

>[!NOTE]
>A teljes körűen felügyelt Felhőbeli natív szolgáltatásként a Azure Cosmos DB garantálja a Cassandra API [rendelkezésre állását, átviteli sebességét és konzisztenciáját](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) . A Cassandra API a nulla karbantartási platform működését és a nulla állásidő javítását is lehetővé teszi.
>
>Ezek a garanciák nem lehetségesek az Apache Cassandra korábbi implementációjában, így sok Cassandra API háttérbeli művelet eltér az Apache Cassandra-től. A gyakori hibák elkerülése érdekében bizonyos beállítások és megközelítések javasoltak.

## <a name="nonodeavailableexception"></a>NoNodeAvailableException

Ez a hiba egy legfelső szintű burkoló kivétel, amely nagy számú lehetséges okot és belső kivételt okoz, amelyek közül számos lehet ügyfélhez kapcsolódó.

Gyakori okok és megoldások:

- **Az Azure LoadBalancers üresjárati időkorlátja**: Ez a probléma a következőképpen is megnyilvánulhat: `ClosedConnectionException` . A probléma megoldásához állítsa a Keep-Alive beállítást az illesztőprogramban (lásd: [Keep-Alive engedélyezése a Java-illesztőprogramhoz](#enable-keep-alive-for-the-java-driver)), és növelje a Keep-Alive beállításokat az operációs rendszeren, vagy [állítsa be az üresjárat időkorlátját Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal).

- **Ügyfélalkalmazás erőforrás-kimerülése**: Győződjön meg arról, hogy az ügyfélszámítógépek elegendő erőforrással rendelkeznek a kérelem végrehajtásához.

## <a name="cant-connect-to-a-host"></a>Nem lehet csatlakozni a gazdagéphez

Ez a következő hibaüzenet jelenhet meg: "nem lehet csatlakozni a gazdagéphez, az újrapróbálkozások száma 600000 ezredmásodpercben."

Ezt a hibát a forrás hálózati címfordítás (SNAT) kimerülése okozhatja az ügyféloldali oldalon. A probléma kizárásához kövesse a SNAT-on a [Kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md) című szakasz lépéseit.

A hiba akkor is előfordulhat, ha az Azure Load Balancer alapértelmezés szerint négy perc üresjárati időkorláttal rendelkezik. Lásd: a [terheléselosztó üresjárati időkorlátja](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). [Engedélyezze a Keep-Alive értéket a Java-illesztőprogram számára](#enable-keep-alive-for-the-java-driver) , és állítsa be az `keepAlive` operációs rendszer intervallumát négy percnél kevesebbre.

## <a name="overloadedexception-java"></a>OverloadedException (Java)

A kérelmek szabályozása megtörténik, mert a felhasznált kérelmek teljes száma nagyobb, mint a kulcstartón vagy táblában kiépített kérelmek száma.

Érdemes lehet a Azure Portalhoz rendelt átviteli sebesség méretezését (lásd: [Azure Cosmos DB Cassandra API fiók rugalmas skálázása](manage-scale-cassandra.md)) vagy újrapróbálkozási szabályzat megvalósítása.

A Java esetében lásd: újrapróbálkozási minták a [v3. x illesztőprogramhoz](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) és a [v4. x illesztőprogramhoz](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Lásd még: [Az Azure Cosmos Cassandra Extensions for Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-despite-sufficient-throughput"></a>OverloadedException a megfelelő átviteli sebesség ellenére

A rendszer úgy tűnik, hogy a kérelmek szabályozása akkor is lehetséges, ha elegendő átviteli sebesség van kiépítve a kérelem mennyiségi vagy felhasznált kérelmi egységének díjaként. Két lehetséges ok:

- **Séma szintű műveletek**: a Cassandra API a séma szintű műveletekhez (CREATE TABLE, ALTER TABLE, drop Table) implementálja a rendszer átviteli sebességének költségvetését. Ennek a költségvetésnek elegendőnek kell lennie az éles rendszer sémájának műveleteihez. Ha azonban nagy számú séma-szintű művelet van, akkor túllépheti ezt a korlátot.

  Mivel a költségvetés nem felhasználó által vezérelt, érdemes csökkenteni a futtatott séma-műveletek számát. Ha ez a művelet nem oldja meg a problémát, vagy nem valósítható meg a munkaterhelés esetében, [hozzon létre egy Azure-támogatási kérelmet](../azure-portal/supportability/how-to-create-azure-support-request.md).

- **Adatok eldöntése**: Ha az átviteli sebesség a Cassandra APIban van kiépítve, akkor egyenlően oszlik meg a fizikai partíciók között, és mindegyik fizikai partíciónak van felső korlátja. Ha nagy mennyiségű adatot szúr be vagy kérdez le egy adott partícióból, akkor is lehet, hogy az adott tábla nagy mennyiségű teljes átviteli sebességét (kérelmek egységét) is kiépíti.

  Tekintse át az adatmodellt, és győződjön meg arról, hogy nem rendelkezik túlzott mértékű döntéssel, ami gyors partíciót eredményezhet.

## <a name="intermittent-connectivity-errors-java"></a>Átmeneti kapcsolódási hibák (Java)

A kapcsolatok váratlanul elvesznek vagy időtúllépésnek számítanak.

A Javához készült Apache Cassandra-illesztőprogramok két natív újracsatlakoztatási szabályzatot biztosítanak: `ExponentialReconnectionPolicy` és `ConstantReconnectionPolicy` . A mező alapértelmezett értéke: `ExponentialReconnectionPolicy`. Azure Cosmos DB Cassandra API esetében azonban `ConstantReconnectionPolicy` két másodperces késleltetést ajánlunk.

Tekintse meg a Java [4. x illesztőprogram dokumentációját](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/), a [Java 3. x illesztőprogram dokumentációját](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/), vagy [konfigurálja a ReconnectionPolicy a Java-illesztőprogramok](#configure-reconnectionpolicy-for-the-java-driver) példái között.

## <a name="error-with-load-balancing-policy"></a>Hiba a terheléselosztási házirenddel

Lehet, hogy implementálta a terheléselosztási házirendet a Java DataStax-illesztőprogram v3. x verziójában, a következőhöz hasonló kóddal:

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

Ha az érték `withLocalDc()` nem felel meg a kapcsolattartási pont adatközpontjának, időszakos hibát tapasztalhat: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` .

A [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java)implementálása. A működéséhez előfordulhat, hogy a következő kóddal kell frissítenie a DataStax:

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="the-count-fails-on-a-large-table"></a>Egy nagyméretű tábla száma meghiúsul

Ha nagy mennyiségű `select count(*) from table` sorhoz futtat vagy hasonló, a kiszolgáló időtúllépést tapasztal.

Ha helyi CQLSH-ügyfelet használ, módosítsa a `--connect-timeout` vagy a `--request-timeout` beállításokat. Lásd [: cqlsh: a CQL-rendszerhéj](https://cassandra.apache.org/doc/latest/tools/cqlsh.html).

Ha a szám továbbra is időtúllépés miatt lejár, a Azure Cosmos DB háttér-telemetria származó rekordok számát a Azure Portal metrikák lapján, a metrika kiválasztásával, `document count` majd az adatbázishoz vagy a gyűjteményhez tartozó szűrő hozzáadásával (az Azure Cosmos dB-ben lévő tábla analóg elemével) érheti el. Ezután az eredményül kapott gráf fölé helyezheti azt az időpontot, amikor a rekordok számát meg szeretné jeleníteni.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="metrikák nézet":::

## <a name="configure-reconnectionpolicy-for-the-java-driver"></a>A Java-illesztőprogram ReconnectionPolicy konfigurálása

### <a name="version-3x"></a>3. x verzió

A Java-illesztőprogram 3. x verziójának esetében konfigurálja az újrakapcsolódási házirendet, amikor létrehoz egy fürtözött objektumot:

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

## <a name="enable-keep-alive-for-the-java-driver"></a>Keep-Alive engedélyezése a Java-illesztőprogramhoz

### <a name="version-3x"></a>3. x verzió

A Java-illesztőprogram 3. x verziójában állítsa a Keep-Alive beállítást a fürt létrehozásakor, majd győződjön meg arról, hogy a Keep-Alive [engedélyezve van az operációs rendszeren](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

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

A Java-illesztőprogram 4-es. x verziójához állítsa be a Keep-Alive beállítást a (z) beállításainak felülbírálásával `reference.conf` , majd győződjön meg arról, hogy a Keep-Alive [engedélyezve van az operációs rendszeren](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg a Azure Cosmos DB Cassandra API [támogatott szolgáltatásaival](cassandra-support.md) .
- Megtudhatja, hogyan [telepíthet át natív Apache Cassandra-ről Azure Cosmos DB Cassandra APIre](cassandra-migrate-cosmos-db-databricks.md).
