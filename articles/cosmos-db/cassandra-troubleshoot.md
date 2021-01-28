---
title: Azure Cosmos DB Cassandra API gyakori hibáinak elhárítása
description: Ez a dokumentum a Azure Cosmos DB-ben észlelt gyakori problémák elhárításának módszereit tárgyalja Cassandra API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: 75ce307056d24f84e9f72f746eb77b09b89e21b2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927490"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API gyakori problémáinak elhárítása
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A Cassandra API in Azure Cosmos DB egy kompatibilitási réteg, amely a népszerű nyílt forráskódú Apache Cassandra-adatbázishoz nyújt [vezetékes protokoll-támogatást](cassandra-support.md) , és [Azure Cosmos db](./introduction.md)-t működtet. A teljes körűen felügyelt natív szolgáltatásként a Azure Cosmos DB [garantálja a Cassandra API rendelkezésre állását, átviteli sebességét és konzisztenciáját](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) . Ezek a garanciák az Apache Cassandra örökölt implementációjában nem lehetségesek. Cassandra API emellett a nulla karbantartási platform működését és a nulla állásidő javítását is lehetővé teszi. Ennek megfelelően számos háttérbeli művelet különbözik az Apache Cassandra-től, ezért a gyakori hibák elkerüléséhez bizonyos beállítások és megközelítések javasoltak. 

Ez a cikk a Azure Cosmos DB Cassandra APIt használó alkalmazások általános hibáit és megoldásait ismerteti.

## <a name="common-errors-and-solutions"></a>Gyakori hibák és megoldások

| Hiba               |  Leírás             | Megoldás  |
|---------------------|--------------------------|-----------|
| OverloadedException (Java) | A felhasználható kérelmek teljes száma meghaladja a tárterületre vagy a táblára kiépített kérések mennyiségét. Így a kérelmek szabályozása megtörténik. | Érdemes megfontolni a Azure Portal a térközhez vagy a táblához rendelt átviteli sebesség méretezését (lásd [itt](manage-scale-cassandra.md) : méretezési műveletek a Cassandra APIban), vagy egy újrapróbálkozási házirendet is megvalósíthat. A Java esetében lásd: újrapróbálkozási minták a [v3. x illesztőprogramhoz](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) és a [v4. x illesztőprogramhoz](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Lásd még: [Azure Cosmos Cassandra Extensions for Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) |
| OverloadedException (Java) még elegendő átviteli sebességgel | A rendszer úgy tűnik, hogy a kérések mennyisége és/vagy az elfogyasztott kérések egységenkénti ára alapján a kérelmek szabályozása megfelelő átviteli sebesség  | Cassandra API a séma szintű műveletekhez (CREATE TABLE, ALTER TABLE, DROP TABLE) implementálja a rendszer átviteli sebességének költségvetését. Ennek a költségvetésnek elegendőnek kell lennie az éles rendszer sémájának műveleteihez. Ha azonban nagy számú séma-szintű művelet van, akkor lehetséges, hogy túllépi ezt a korlátot. Mivel ez a költségvetés nem a felhasználó által vezérelt, érdemes megfontolni a futtatott séma-műveletek számának csökkentését. Ha a művelet végrehajtása nem oldja meg a problémát, vagy nem valósítható meg a munkaterhelés esetében, [hozzon létre egy Azure-támogatási kérelmet](../azure-portal/supportability/how-to-create-azure-support-request.md).|
| ClosedConnectionException (Java) | A sikeres kapcsolatok utáni üresjárati időtartam után az alkalmazás nem tud csatlakozni| Ezt a hibát az Azure LoadBalancers üresjárati időtúllépése okozhatja, amely 4 percet vesz igénybe. Állítsa be a Keep Alive beállítást az illesztőprogramban (lásd alább), és növelje a Keep-Alive beállításokat az operációs rendszeren, vagy [állítsa be a tétlen időtúllépést Azure Load Balancerban](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). |
| Egyéb átmeneti kapcsolódási hibák (Java) | A kapcsolatok váratlanul elvesznek vagy időtúllépést veszítenek | A Javához készült Apache Cassandra-illesztőprogramok két natív újracsatlakoztatási szabályzatot biztosítanak: `ExponentialReconnectionPolicy` és `ConstantReconnectionPolicy` . A mező alapértelmezett értéke: `ExponentialReconnectionPolicy`. Azure Cosmos DB Cassandra API esetében azonban ajánlott `ConstantReconnectionPolicy` 2 másodperces késleltetés. Tekintse meg a Java v4. x illesztőprogramhoz tartozó [illesztőprogram-dokumentációt](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  , és [itt](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) találja a Java 3. x útmutatót (lásd még az alábbi példákat is).|

Ha a hiba nem szerepel a fentiekben, és a rendszer hibát tapasztal a [Cassandra APIban támogatott művelet](cassandra-support.md)végrehajtásakor, ahol a hiba nem jelenik meg *natív Apache Cassandra használata* esetén, [hozzon létre egy Azure-támogatási kérést](../azure-portal/supportability/how-to-create-azure-support-request.md)

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