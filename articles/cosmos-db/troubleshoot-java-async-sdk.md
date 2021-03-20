---
title: Azure Cosmos DB aszinkron Java SDK v2 diagnosztizálása és megoldása
description: Használjon olyan szolgáltatásokat, mint az ügyféloldali naplózás és más külső eszközök az aszinkron Java SDK v2-ben Azure Cosmos DB problémák azonosításához, diagnosztizálásához és hibaelhárításához.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: b39a74bd06f942cf21d201c8cef48bc6dfc57d46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96548086"
---
# <a name="troubleshoot-issues-when-you-use-the-azure-cosmos-db-async-java-sdk-v2-with-sql-api-accounts"></a>A Azure Cosmos DB aszinkron Java SDK v2 és az SQL API-fiókok használata esetén felmerülő problémák elhárítása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Ez *nem* a legújabb Java SDK a Azure Cosmos db! Frissítse a projektet [Azure Cosmos db Java SDK v4](sql-api-sdk-java-v4.md) -re, majd olvassa el a Azure Cosmos db Java SDK v4 [hibaelhárítási útmutatót](troubleshoot-java-sdk-v4-sql.md). A frissítéshez kövesse az [áttelepítés Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -útmutató és a [reaktor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) útmutató című témakör utasításait. 
>
> Ez a cikk csak Azure Cosmos DB aszinkron Java SDK v2-re vonatkozó hibaelhárítást ismerteti. További információért tekintse meg a Azure Cosmos DB aszinkron Java SDK v2 [kibocsátási megjegyzéseit](sql-api-sdk-async-java.md), a [Maven-tárházat](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) és a [teljesítménnyel kapcsolatos tippeket](performance-tips-async-java.md) .
>

Ez a cikk általános problémákról, megkerülő megoldásokról, diagnosztikai lépésekről és eszközökről tartalmaz Azure Cosmos DB SQL API-fiókkal rendelkező [Java ASZINKRON SDK](sql-api-sdk-async-java.md) -val.
A Java Async SDK ügyféloldali logikai leképezést biztosít az Azure Cosmos DB SQL API eléréséhez. Ez a cikk azokat az eszközöket és módszereket ismerteti, amelyek segítenek megoldani a problémákat.

Kezdje a következő listával:

* Tekintse meg a jelen cikk [gyakori problémák és megoldások] című szakaszát.
* Tekintse meg az SDK-t, amely elérhető [nyílt forráskódú a githubon](https://github.com/Azure/azure-cosmosdb-java). A [probléma szakasza](https://github.com/Azure/azure-cosmosdb-java/issues) aktívan van figyelve. Ellenőrizze, hogy a megkerülő megoldással kapcsolatos bármilyen hasonló probléma már be van-e jelölve.
* Tekintse át a [teljesítménnyel kapcsolatos tippeket](performance-tips-async-java.md), és kövesse a javasolt eljárásokat.
* Ha nem talál megoldást, olvassa el a cikk további részeit. Ezután egy [GitHub-problémát kell megadnia](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Gyakori hibák és áthidaló megoldásaik

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Hálózati problémák, nettó olvasási időtúllépési hiba, alacsony átviteli sebesség, nagy késés

#### <a name="general-suggestions"></a>Általános javaslatok
* Győződjön meg arról, hogy az alkalmazás ugyanazon a régión fut, mint a Azure Cosmos DB-fiókja. 
* Keresse meg a CPU-használatot azon a gazdagépen, amelyen az alkalmazás fut. Ha a CPU-használat 90 százalék vagy több, akkor futtassa az alkalmazást egy magasabb konfigurációval rendelkező gazdagépen. Vagy több gépen is terjesztheti a terhelést.

#### <a name="connection-throttling"></a>Kapcsolatok szabályozása
A kapcsolatok szabályozása akkor fordulhat elő, ha a gazdagép vagy az [Azure SNAT (Pat) portjának kimerülése]vagy a [Csatlakozás korlátja] .

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>A gazdagépre vonatkozó kapcsolatonként
Bizonyos linuxos rendszerek (például a Red Hat) felső korláttal rendelkeznek a megnyitott fájlok teljes számánál. A Linux rendszerű szoftvercsatornák fájlokként vannak megvalósítva, így ez a szám a kapcsolatok teljes számát is korlátozza.
Futtassa az alábbi parancsot.

```bash
ulimit -a
```
A "nincs fájl" néven azonosított maximálisan engedélyezett megnyitott fájlok száma legalább dupla a kapcsolatok készletének mérete. További információ: teljesítménnyel kapcsolatos [tippek](performance-tips-async-java.md).

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Az Azure SNAT (PAT) portjának kimerülése

Ha az alkalmazás nyilvános IP-cím nélküli Azure-Virtual Machines van telepítve, alapértelmezés szerint az [Azure SNAT-portok](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) kapcsolatot létesít a virtuális gépen kívüli végpontokkal. A virtuális gépről a Azure Cosmos DB végpont számára engedélyezett kapcsolatok számát az [Azure SNAT konfigurációja](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)korlátozza.

 Az Azure SNAT-portok használata csak akkor történik meg, ha a virtuális gép magánhálózati IP-címmel rendelkezik, és a virtuális gép egy folyamata megpróbál csatlakozni egy nyilvános IP-címhez. Az Azure SNAT-korlátozás elkerülése érdekében két Áthidaló megoldás létezik:

* Adja hozzá Azure Cosmos DB szolgáltatási végpontját az Azure Virtual Machines Virtual Network alhálózatához. További információ: [Azure Virtual Network Service-végpontok](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Ha a szolgáltatási végpont engedélyezve van, a rendszer a kérelmeket már nem küldi el a nyilvános IP-címről Azure Cosmos DB. Ehelyett a rendszer elküldi a virtuális hálózatot és az alhálózati identitást. Ez a változás akkor okozhat tűzfalat, ha csak a nyilvános IP-címek engedélyezettek. Ha tűzfalat használ, a szolgáltatás végpontjának engedélyezésekor [Virtual Network ACL](/previous-versions/azure/virtual-network/virtual-networks-acl)-EK használatával adjon hozzá egy alhálózatot a tűzfalhoz.
* Rendeljen hozzá egy nyilvános IP-címet az Azure-beli virtuális géphez.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Nem érhető el a szolgáltatás – tűzfal
``ConnectTimeoutException`` azt jelzi, hogy az SDK nem tudja elérni a szolgáltatást.
A közvetlen mód használata esetén a következőhöz hasonló hibaüzenet jelenhet meg:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Ha az alkalmazás gépén fut egy tűzfal, nyissa meg a 10 000 – 20 000 portot, amelyet a közvetlen mód használ.
Kövesse a [gazdagépen a kapcsolatok korlátját](#connection-limit-on-host)is.

#### <a name="http-proxy"></a>HTTP-proxy

Ha HTTP-proxyt használ, győződjön meg arról, hogy az képes támogatni az SDK-ban konfigurált kapcsolatok számát `ConnectionPolicy` .
Ellenkező esetben a csatlakoztatási problémákkal szembesül.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Érvénytelen kódolási minta: a nettó i/o-szál blokkolása

Az SDK a [nettó](https://netty.io/) IO-függvénytárat használja a Azure Cosmos db való kommunikációhoz. Az SDK-ban aszinkron API-k szerepelnek, és a nem blokkoló i/o API-kat használ. Az SDK i/o-műveletek végrehajtása az IO-beli nettó szálakon történik. Az IO-beli virtuális gépek száma úgy van konfigurálva, hogy megegyezzen az alkalmazáshoz tartozó CPU-magok számával. 

A nettó i/o-szálak csak nem blokkoló, a nettó i/o-működéshez használhatók. Az SDK visszaadja az API meghívásának eredményét az egyik nettó i/o-szálon az alkalmazás kódjára. Ha az alkalmazás hosszú élettartamú műveletet hajt végre, miután az eredményeket kapott a nettó szálon, előfordulhat, hogy az SDK-ban nincs elég IO-szál a belső i/o-művelet elvégzéséhez. Az ilyen alkalmazások kódolása alacsony átviteli sebességet, nagy késést és `io.netty.handler.timeout.ReadTimeoutException` hibákat eredményezhet. A megkerülő megoldás a szál átállítása, ha tudja, hogy a művelet időt vesz igénybe.

Tekintse meg például a következő kódrészletet. Előfordulhat, hogy hosszú távú munkát végez, amely több mint néhány ezredmásodpercet vesz igénybe a nettó szálon. Ha igen, akkor végül egy olyan állapotba kerülhet, amelyben nem szerepelnek a nettó i/o-szálak az IO-munka feldolgozásához. Ennek eredményeképpen ReadTimeoutException-hibát kap.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-readtimeout"></a>Aszinkron Java SDK v2 (Maven com. microsoft. Azure:: Azure-cosmosdb)

```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
A megkerülő megoldással módosíthatja a szálat, amelyen időt vesz igénybe. Definiálja az ütemező egyedi példányát az alkalmazáshoz.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-scheduler"></a>Aszinkron Java SDK v2 (Maven com. microsoft. Azure:: Azure-cosmosdb)

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
```
Előfordulhat, hogy olyan munkát kell végeznie, amely időt vesz igénybe, például számítási feldolgozóként vagy az IO blokkolása. Ebben az esetben a szálat a által az API-val megadott feldolgozónak állítsa be `customScheduler` `.observeOn(customScheduler)` .

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-applycustomscheduler"></a>Aszinkron Java SDK v2 (Maven com. microsoft. Azure:: Azure-cosmosdb)

```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
A használatával `observeOn(customScheduler)` felszabadítja a nettó i/o-szálat, és átválthat az egyéni ütemező által biztosított saját egyéni szálra. Ez a módosítás megoldja a problémát. Többé nem jelenik meg a `io.netty.handler.timeout.ReadTimeoutException` hiba.

### <a name="connection-pool-exhausted-issue"></a>A csatlakozókábel kimerült hibája

`PoolExhaustedException` ügyféloldali hiba. Ez a hiba azt jelzi, hogy az alkalmazás számítási feladatának értéke magasabb, mint amit az SDK-kapcsolatfájl képes kiszolgálni. Növelje a kapcsolatok készletének méretét, vagy ossza meg több alkalmazás terhelését.

### <a name="request-rate-too-large"></a>Túl nagy a kérelmek aránya
Ez a hiba kiszolgálóoldali hiba. Azt jelzi, hogy a kiosztott átviteli sebességet használták. Próbálkozzon újra később. Ha ez a hiba gyakran előfordul, vegye figyelembe a gyűjtemény átviteli sebességének növekedését.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Sikertelen csatlakozás Azure Cosmos DB emulátorhoz

Az Azure Cosmos DB Emulator HTTPS-tanúsítványa önaláírt. Ahhoz, hogy az SDK működjön az emulátorral, importálja az Emulator-tanúsítványt egy Java-TrustStore. További információ: [Azure Cosmos db Emulator-tanúsítványok exportálása](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Függőségi ütközések problémái

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

A fenti kivétel azt sugallja, hogy a RxJava lib egy régebbi verziójával (például 1.2.2) függ. Az SDK azon RxJava-1.3.8 támaszkodik, amelyeken a RxJava korábbi verzióiban nem érhető el API-k. 

Az ilyen problémák megoldásához meg kell határozni, hogy az RxJava-1.2.2 milyen más függőséget eredményez, és kizárja a RxJava-1.2.2-re irányuló tranzitív függőséget, és lehetővé teszi a CosmosDB SDK számára az újabb verzió használatát.

Annak azonosításához, hogy melyik függvénytár hozza a RxJava-1.2.2-t, futtassa a következő parancsot a Project pom.xml fájl mellett:
```bash
mvn dependency:tree
```
További információt a [Maven függőségi fa útmutatójában](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html)talál.

Miután azonosította a RxJava-1.2.2-t, a rendszer tranzitív függőségi viszonyban áll a projekt más függőségeivel, módosíthatja a lib-fájlban lévő függőséget a Pom-fájlban, és kizárhatja a RxJava tranzitív függőségét:

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

További információ: a [tranzitív függőségi útmutató kizárása](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Ügyféloldali SDK naplózásának engedélyezése

A Java aszinkron SDK SLF4j használ a naplózási homlokzatként, amely támogatja az olyan népszerű naplózási keretrendszerekbe való bejelentkezést, mint a log4j és a logback.

Ha például naplózási keretrendszerként szeretné használni a log4j-t, adja hozzá a következő libs-t a Java-osztályútvonal.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Adjon hozzá egy log4j-konfigurációt is.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

További információkért lásd a [sfl4j naplózási kézikönyvét](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Operációs rendszer hálózati statisztikája
A netstat parancs futtatásával megtudhatja, hogy hány kapcsolat van olyan állapotban, mint a `ESTABLISHED` és a `CLOSE_WAIT` .

Linux rendszeren a következő parancsot futtathatja.
```bash
netstat -nap
```
Az eredmény szűrése, hogy csak az Azure Cosmos DB végponthoz tartozó kapcsolatok legyenek.

Az Azure Cosmos DB végponthoz tartozó kapcsolatok száma az `ESTABLISHED` állapotban nem lehet nagyobb, mint a konfigurált kapcsolati készlet mérete.

Az Azure Cosmos DB végponttal létesített kapcsolatok lehetnek az `CLOSE_WAIT` állapotban. Lehet, hogy több mint 1 000. A magas érték azt jelzi, hogy a kapcsolatok létrehozása és leválasztása gyorsan megtörténik. Ez a helyzet valószínűleg problémákat okoz. További információt a [gyakori problémák és a megkerülő megoldások] című szakaszban talál.

 <!--Anchors-->
[Gyakori hibák és áthidaló megoldásaik]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[A gazdagépre vonatkozó kapcsolatonként]: #connection-limit-on-host
[Az Azure SNAT (PAT) portjának kimerülése]: #snat
