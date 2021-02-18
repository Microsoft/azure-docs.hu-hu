---
title: Azure Cosmos DB Java SDK v4-es verziójának diagnosztizálása és megoldása
description: A Java SDK v4-ben a Azure Cosmos DB problémák azonosításához, diagnosztizálásához és hibaelhárításához használhatók olyan funkciók, mint az ügyféloldali naplózás és más külső eszközök.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.custom: devx-track-java
ms.openlocfilehash: cba8b97adb40ca2c277268188ff6ad541c7e9676
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596474"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>A Azure Cosmos DB Java SDK v4 SQL API-fiókokkal való használatakor felmerülő problémák elhárítása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Ez a cikk csak Azure Cosmos DB Java SDK v4-re vonatkozó hibaelhárítást ismerteti. További információért tekintse meg a Azure Cosmos DB Java SDK v4 [kibocsátási megjegyzéseit](sql-api-sdk-java-v4.md), a [Maven-tárházat](https://mvnrepository.com/artifact/com.azure/azure-cosmos)és a [teljesítménnyel kapcsolatos tippeket](performance-tips-java-sdk-v4-sql.md) . Ha jelenleg a v4-nél régebbi verziót használ, tekintse meg a következőt: [migrálás Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -re – útmutató a v4-re való frissítéshez.
>

Ez a cikk általános problémákról, megkerülő megoldásokról, diagnosztikai lépésekről és eszközökről tartalmaz Azure Cosmos DB Java SDK v4 Azure Cosmos DB SQL API-fiókokkal való használatakor.
Azure Cosmos DB Java SDK v4 ügyféloldali logikai ábrázolást biztosít a Azure Cosmos DB SQL API eléréséhez. Ez a cikk azokat az eszközöket és módszereket ismerteti, amelyek segítenek megoldani a problémákat.

Kezdje a következő listával:

* Tekintse meg a jelen cikk [gyakori problémák és megoldások] című szakaszát.
* Tekintse meg a Java SDK-t a Azure Cosmos DB központi tárházban, amely [nyílt forráskódú elérhető a githubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos). A [probléma szakasza](https://github.com/Azure/azure-sdk-for-java/issues) aktívan van figyelve. Ellenőrizze, hogy a megkerülő megoldással kapcsolatos bármilyen hasonló probléma már be van-e jelölve. Az egyik hasznos tipp, ha a *Cosmos: v4-Item* címkével szűri a problémákat.
* Tekintse át a Azure Cosmos DB Java SDK v4 [teljesítményével kapcsolatos tippeket](performance-tips-java-sdk-v4-sql.md) , és kövesse a javasolt eljárásokat.
* Ha nem talál megoldást, olvassa el a cikk további részeit. Ezután egy [GitHub-problémát kell megadnia](https://github.com/Azure/azure-sdk-for-java/issues). Ha van lehetőség címkék hozzáadására a GitHub-problémákhoz, vegyen fel egy *Cosmos: v4-Item* címkét.

### <a name="retry-logic"></a>Újrapróbálkozási logika <a id="retry-logics"></a>
A Cosmos DB SDK bármely I/O-hiba esetén megpróbálja újra végrehajtani a sikertelen műveletet, ha az SDK-ban kivitelezhető az újrapróbálkozás. Ha bármilyen hiba esetén újra próbálkozik, az írási hibák konkrét kezelése/újrapróbálása kötelező. Javasoljuk, hogy a legújabb SDK-t használja, mivel az újrapróbálkozási logikát folyamatosan fejleszti.

1. Az i/o-hibák olvasása és lekérése az SDK-val újra próbálkozik a végfelhasználók nélkül.
2. Az írások (létrehozás, Upsert, csere, törlés) nem idempotens, ezért az SDK nem mindig vakon próbálkozik a sikertelen írási műveletekkel. Szükség van arra, hogy a felhasználó alkalmazás-logikája kezelni tudja a hibát, és próbálkozzon újra.
3. [Hibaelhárítási SDK-elérhetőség](troubleshoot-sdk-availability.md) – a többrégiós Cosmos db-fiókok újrapróbálkozásait ismerteti.

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Gyakori hibák és áthidaló megoldásaik

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Hálózati problémák, nettó olvasási időtúllépési hiba, alacsony átviteli sebesség, nagy késés

#### <a name="general-suggestions"></a>Általános javaslatok
A legjobb teljesítmény érdekében:
* Győződjön meg arról, hogy az alkalmazás ugyanazon a régión fut, mint a Azure Cosmos DB-fiókja. 
* Keresse meg a CPU-használatot azon a gazdagépen, amelyen az alkalmazás fut. Ha a CPU-használat 50 százalék vagy több, akkor futtassa az alkalmazást egy magasabb konfigurációval rendelkező gazdagépen. Vagy több gépen is terjesztheti a terhelést.
    * Ha az alkalmazást az Azure Kubernetes szolgáltatáson futtatja, akkor a [Azure monitor segítségével figyelheti a CPU-kihasználtságot](../azure-monitor/containers/container-insights-analyze.md).

#### <a name="connection-throttling"></a>Kapcsolatok szabályozása
A kapcsolatok szabályozása akkor fordulhat elő, ha a gazdagép vagy az [Azure SNAT (Pat) portjának kimerülése]vagy a [Csatlakozás korlátja] .

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>A gazdagépre vonatkozó kapcsolatonként
Bizonyos linuxos rendszerek (például a Red Hat) felső korláttal rendelkeznek a megnyitott fájlok teljes számánál. A Linux rendszerű szoftvercsatornák fájlokként vannak megvalósítva, így ez a szám a kapcsolatok teljes számát is korlátozza.
Futtassa az alábbi parancsot.

```bash
ulimit -a
```
A "nincs fájl" néven azonosított maximálisan engedélyezett megnyitott fájlok száma legalább dupla a kapcsolatok készletének mérete. További információ: Azure Cosmos DB Java SDK v4 [teljesítményével kapcsolatos tippek](performance-tips-java-sdk-v4-sql.md).

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

Az SDK a [nettó](https://netty.io/) IO-függvénytárat használja a Azure Cosmos db való kommunikációhoz. Az SDK egy aszinkron API-val rendelkezik, és nem blokkolja az i/o-API-kat. Az SDK i/o-műveletek végrehajtása az IO-beli nettó szálakon történik. Az IO-beli virtuális gépek száma úgy van konfigurálva, hogy megegyezzen az alkalmazáshoz tartozó CPU-magok számával. 

A nettó i/o-szálak csak nem blokkoló, a nettó i/o-működéshez használhatók. Az SDK visszaadja az API meghívásának eredményét az egyik nettó i/o-szálon az alkalmazás kódjára. Ha az alkalmazás hosszú élettartamú műveletet hajt végre, miután az eredményeket kapott a nettó szálon, előfordulhat, hogy az SDK-ban nincs elég IO-szál a belső i/o-művelet elvégzéséhez. Az ilyen alkalmazások kódolása alacsony átviteli sebességet, nagy késést és `io.netty.handler.timeout.ReadTimeoutException` hibákat eredményezhet. A megkerülő megoldás a szál átállítása, ha tudja, hogy a művelet időt vesz igénybe.

Tekintse meg például az alábbi kódrészletet, amely elemeket ad hozzá egy tárolóhoz ( [itt](create-sql-api-java.md) találja az adatbázis és a tároló beállításához szükséges útmutatást.) Előfordulhat, hogy hosszú távú munkát végez, amely több mint néhány ezredmásodpercet vesz igénybe a nettó szálon. Ha igen, akkor végül egy olyan állapotba kerülhet, amelyben nem szerepelnek a nettó i/o-szálak az IO-munka feldolgozásához. Ennek eredményeképpen ReadTimeoutException-hibát kap.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootNeedsSchedulerAsync)]

A megkerülő megoldással módosíthatja a szálat, amelyen időt vesz igénybe. Definiálja az ütemező egyedi példányát az alkalmazáshoz.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootCustomSchedulerAsync)]

Előfordulhat, hogy olyan munkát kell végeznie, amely időt vesz igénybe, például számítási feldolgozóként vagy az IO blokkolása. Ebben az esetben a szálat a által az API-val megadott feldolgozónak állítsa be `customScheduler` `.publishOn(customScheduler)` .

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootPublishOnSchedulerAsync)]

A használatával `publishOn(customScheduler)` felszabadítja a nettó i/o-szálat, és átválthat az egyéni ütemező által biztosított saját egyéni szálra. Ez a módosítás megoldja a problémát. Többé nem jelenik meg a `io.netty.handler.timeout.ReadTimeoutException` hiba.

### <a name="request-rate-too-large"></a>Túl nagy a kérelmek aránya
Ez a hiba kiszolgálóoldali hiba. Azt jelzi, hogy a kiosztott átviteli sebességet használták. Próbálkozzon újra később. Ha ez a hiba gyakran előfordul, vegye figyelembe a gyűjtemény átviteli sebességének növekedését.

* **Leállítási megvalósítása getRetryAfterInMilliseconds időközönként**

    A teljesítmény tesztelése során növelje a terhelést, amíg a kérelmek kis száma le nem kerül a szabályozásba. Ha szabályozva van, az ügyfélalkalmazás leállítási kell lennie a kiszolgáló által megadott újrapróbálkozási időköznek. A leállítási tiszteletben tartásával biztosítható, hogy az újrapróbálkozások között minimálisan mennyi időt kell várnia.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Sikertelen csatlakozás Azure Cosmos DB emulátorhoz

Az Azure Cosmos DB Emulator HTTPS-tanúsítványa önaláírt. Ahhoz, hogy az SDK működjön az emulátorral, importálja az Emulator-tanúsítványt egy Java-TrustStore. További információ: [Azure Cosmos db Emulator-tanúsítványok exportálása](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Függőségi ütközések problémái

A Azure Cosmos DB Java SDK számos függőséggel rendelkezik; Általánosságban elmondható, hogy ha a projekt függőségi fájában szerepel egy olyan összetevő egy régebbi verziója, amely Azure Cosmos DB Java SDK-ra függ, ez az alkalmazás futtatásakor nem várt hibákat eredményezhet. Ha hibakeresést végez, amiért az alkalmazás váratlanul kivételt jelez, érdemes megtekinteni, hogy a függőségi fa nem véletlenül húz-e véletlenül egy vagy több Azure Cosmos DB Java SDK-függőség egy régebbi verzióját.

A probléma megoldásához meg kell határozni, hogy a projekt függőségei közül melyik hozza a régi verziót, és zárja ki a régebbi verzió tranzitív függőségét, és engedélyezze Azure Cosmos DB Java SDK-t az újabb verzióban.

A következő parancs futtatásával megállapíthatja, hogy a projekt függőségei közül melyik Azure Cosmos DB a Java SDK-t, majd futtassa az alábbi parancsot a projekt pom.xml fájlján:
```bash
mvn dependency:tree
```
További információt a [Maven függőségi fa útmutatójában](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html)talál.

Ha már tudja, hogy a projektnek melyik függősége egy régebbi verziótól függ, módosíthatja az adott lib-fájl függőségét a Pom-fájlban, és kizárhatja a tranzitív függőséget az alábbi példát követve (amely azt feltételezi, hogy a *reaktor-Core* az elavult függőség):

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

További információ: a [tranzitív függőségi útmutató kizárása](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Ügyféloldali SDK naplózásának engedélyezése

Azure Cosmos DB Java SDK v4 a SLF4j használja a naplózási homlokzatként, amely támogatja az olyan népszerű naplózási keretrendszerekbe való bejelentkezést, mint például a log4j és a logback.

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

# Set root logger level to INFO and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.azure.cosmos=INFO
#log4j.category.io.netty=OFF
#log4j.category.io.projectreactor=OFF
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

Windows rendszeren ugyanezt a parancsot különböző argumentum-jelzők használatával futtathatja:
```bash
netstat -abn
```

Az eredmény szűrése, hogy csak az Azure Cosmos DB végponthoz tartozó kapcsolatok legyenek.

Az Azure Cosmos DB végponthoz tartozó kapcsolatok száma az `ESTABLISHED` állapotban nem lehet nagyobb, mint a konfigurált kapcsolati készlet mérete.

Az Azure Cosmos DB végponttal létesített kapcsolatok lehetnek az `CLOSE_WAIT` állapotban. Lehet, hogy több mint 1 000. A magas érték azt jelzi, hogy a kapcsolatok létrehozása és leválasztása gyorsan megtörténik. Ez a helyzet valószínűleg problémákat okoz. További információt a [gyakori problémák és a megkerülő megoldások] című szakaszban talál.

 <!--Anchors-->
[Gyakori hibák és áthidaló megoldásaik]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[A gazdagépre vonatkozó kapcsolatonként]: #connection-limit-on-host
[Az Azure SNAT (PAT) portjának kimerülése]: #snat
