---
title: Teljesítménnyel kapcsolatos tippek Azure Cosmos DB aszinkron Java SDK v2-hez
description: Az ügyfél-konfigurációs beállítások megismerése az Azure Cosmos Database teljesítményének az aszinkron Java SDK v2-hez való javításához
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: bd009ae4909c8cb016a31323294df3a359eb7c51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033663"
---
# <a name="performance-tips-for-azure-cosmos-db-async-java-sdk-v2"></a>Teljesítménnyel kapcsolatos tippek Azure Cosmos DB aszinkron Java SDK v2-hez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)


> [!IMPORTANT]  
> Ez *nem* a legújabb Java SDK a Azure Cosmos db! Frissítse a projektet [Azure Cosmos db Java SDK v4](sql-api-sdk-java-v4.md) -re, majd olvassa el a Azure Cosmos db Java SDK v4 [Performance tippek útmutatót](performance-tips-java-sdk-v4-sql.md). A frissítéshez kövesse az [áttelepítés Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -útmutató és a [reaktor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) útmutató című témakör utasításait. 
> 
> A jelen cikkben található teljesítménnyel kapcsolatos tippek csak Azure Cosmos DB aszinkron Java SDK v2-re vonatkoznak. További információkért tekintse meg a Azure Cosmos DB aszinkron Java SDK v2 [kibocsátási megjegyzéseit](sql-api-sdk-async-java.md), a [Maven-tárházat](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)és a Azure Cosmos db aszinkron Java SDK v2 [hibaelhárítási útmutatót](troubleshoot-java-async-sdk.md) .
>

A Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen méretezhető a garantált késés és az átviteli sebesség tekintetében. Nem kell megváltoztatnia a jelentős architektúrát, vagy összetett kódot kell írnia az adatbázis méretezéséhez Azure Cosmos DB. A fel-és leskálázás olyan egyszerű, mint egyetlen API-hívás vagy egy SDK-metódus hívása. Mivel azonban a Azure Cosmos DB hálózati hívásokkal érhetők el, ügyféloldali optimalizálási lehetőségek érhetők el, ha a [Azure Cosmos db aszinkron Java SDK v2](sql-api-sdk-async-java.md)-t használja a maximális teljesítmény eléréséhez.

Tehát ha a "Hogyan javíthatom az adatbázis teljesítményét?" című témakört kérdezi le? vegye figyelembe a következő lehetőségeket:

## <a name="networking"></a>Hálózatkezelés

* **Csatlakoztatási mód: közvetlen mód használata**
    
  Az ügyfél Azure Cosmos DBhoz való csatlakozásának módja fontos hatással van a teljesítményre, különösen az ügyféloldali késések tekintetében. A *ConnectionMode* az ügyfél *ConnectionPolicy* konfigurálásához rendelkezésre álló kulcsfontosságú konfigurációs beállítás. Azure Cosmos DB aszinkron Java SDK v2 esetén a két elérhető ConnectionModes a következők:  
      
  * [Átjáró (alapértelmezett)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
  * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)
  
  Az átjáró üzemmód minden SDK platformon támogatott, és alapértelmezés szerint a konfigurált beállítás. Ha az alkalmazásai szigorú tűzfal-korlátozásokkal rendelkező vállalati hálózaton belül futnak, az átjáró mód a legjobb választás, mivel a szabványos HTTPS-portot és egyetlen végpontot használ.   A teljesítmény-kompromisszum azonban az, hogy az átjáró üzemmód egy további hálózati ugrást is magában foglal minden alkalommal, amikor az összes adat beolvasása vagy írása Azure Cosmos DB. Emiatt a közvetlen mód jobb teljesítményt nyújt kevesebb hálózati ugrás miatt.
  
  A *ConnectionMode* a *DocumentClient* -példány építése során, a *ConnectionPolicy* paraméterrel konfigurálható.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-connectionpolicy"></a>Aszinkron Java SDK v2 (Maven com. microsoft. Azure:: Azure-cosmosdb)

```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.Direct);
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
```

* **Az ügyfeleket ugyanabban az Azure-régióban helyezze el a jobb teljesítmény érdekében**

  Ha lehetséges, helyezzen minden olyan alkalmazást, amely a Azure Cosmos DBt hívja meg ugyanabban a régióban, mint az Azure Cosmos Database. A hozzávetőleges összehasonlításhoz az azonos régióban lévő Azure Cosmos DB a 1-2 MS-on belül fejeződik be, de az USA nyugati és keleti partja közötti késés >50 MS. Ez a késés valószínűleg a kérelemtől függ attól függően, hogy a kérés milyen útvonalon halad át az ügyféltől az Azure Datacenter-határig. A lehető legalacsonyabb késést úgy érheti el, hogy a hívó alkalmazás ugyanabban az Azure-régióban található, mint a kiépített Azure Cosmos DB végpont. Az elérhető régiók listáját az [Azure-régiók](https://azure.microsoft.com/regions/#services)című részben tekintheti meg.

  :::image type="content" source="./media/performance-tips/same-region.png" alt-text="A Azure Cosmos DB-kapcsolatok házirendjének ábrája" border="false":::

## <a name="sdk-usage"></a>SDK-használat

* **A legújabb SDK telepítése**

  A Azure Cosmos DB SDK-kat folyamatosan fejlesztjük a legjobb teljesítmény érdekében. Tekintse meg az Azure Cosmos DB aszinkron Java SDK v2 [kibocsátási megjegyzések](sql-api-sdk-async-java.md) oldalait a legújabb SDK-és felülvizsgálati eredmények meghatározásához.

* **Egyedi Azure Cosmos DB-ügyfél használata az alkalmazás élettartama során**

  Minden AsyncDocumentClient-példány szál-biztonságos, és hatékonyan kezeli a kapcsolatok kezelését és a címek gyorsítótárazását. A hatékony AsyncDocumentClient és a jobb teljesítmény érdekében ajánlott a AsyncDocumentClient/alkalmazástartomány egyetlen példányát használni az alkalmazás élettartamára.

* **ConnectionPolicy finomhangolása**

  Alapértelmezés szerint a közvetlen üzemmódú Cosmos DB kérelmek TCP protokollon keresztül történnek a Azure Cosmos DB aszinkron Java SDK v2 használata esetén. Belsőleg az SDK egy speciális közvetlen üzemmódú architektúrát használ a hálózati erőforrások dinamikus kezeléséhez és a legjobb teljesítmény eléréséhez.

  A Azure Cosmos DB aszinkron Java SDK v2-ben a legjobb választás az adatbázis teljesítményének növelése a legtöbb számítási feladattal. 

  * ***Közvetlen üzemmód áttekintése***

  :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="A közvetlen módú architektúra ábrája" border="false":::
  
  A közvetlen módban alkalmazott ügyféloldali architektúra előre jelezhető hálózati kihasználtságot és többszörös hozzáférést biztosít Azure Cosmos DB replikához. A fenti ábrán látható, hogy a Direct Mode hogyan irányítja az ügyfelek kérelmeit a Cosmos DB háttérbeli replikára. A közvetlen módú architektúra legfeljebb 10 **csatornát** foglal le az ügyféloldali replikán. A csatornák egy TCP-kapcsolatok, amely előtt egy kérelem-puffer található, amely 30 kérelem mélyét képezi. A replikához tartozó csatornák dinamikusan vannak lefoglalva a replika **szolgáltatási végpontja** által igényelt módon. Amikor a felhasználó közvetlen módban bocsát ki egy kérést, a **TransportClient** a megfelelő szolgáltatási végpontra irányítja a kérést a partíciós kulcs alapján. A kérelmek **várólistájának** pufferei a szolgáltatási végpont előtt érkeznek.

  * ***ConnectionPolicy-konfigurációs beállítások közvetlen módban***

    Első lépésként használja az alábbi ajánlott konfigurációs beállításokat. Ha az adott témakörben problémákba ütközik, forduljon a [Azure Cosmos db csapatához](mailto:CosmosDBPerformanceSupport@service.microsoft.com) .

    Ha a Azure Cosmos DBt hivatkozási adatbázisként használja (azaz az adatbázist sok pont olvasási művelethez és kevés írási művelethez használja), akkor elfogadható lehet a *idleEndpointTimeout* 0 értékre (azaz nincs időkorlát) beállítani.


    | Konfigurációs beállítás       | Alapértelmezett    |
    | :------------------:       | :-----:    |
    | bufferPageSize             | 8192       |
    | connectionTimeout          | "PT1M"     |
    | idleChannelTimeout         | "PT0S"     |
    | idleEndpointTimeout        | "PT1M10S"  |
    | maxBufferCapacity          | 8388608    |
    | maxChannelsPerEndpoint     | 10         |
    | maxRequestsPerChannel      | 30         |
    | receiveHangDetectionTime   | "PT1M5S"   |
    | requestExpiryInterval      | "PT5S"     |
    | requestTimeout             | "PT1M"     |
    | requestTimerResolution     | "PT 0.5 S"   |
    | sendHangDetectionTime      | "PT10S"    |
    | shutdownTimeout            | "PT15S"    |

* ***Programozási tippek közvetlen üzemmódhoz***

  Az SDK-problémák megoldásához tekintse át az Azure Cosmos DB aszinkron Java SDK v2 [hibaelhárítási](troubleshoot-java-async-sdk.md) cikkét.
  
  Néhány fontos programozási tipp közvetlen mód használata esetén:
  
  * **Használjon többszálú működést az alkalmazásban a hatékony TCP-adatátvitel** érdekében – a kérések elvégzése után az alkalmazásnak elő kell fizetnie, hogy egy másik szálon fogadja az adatait. Ezzel nem kényszeríti a nem szándékolt "váltakozó kétirányú" műveletet, és az azt követő kérések blokkolva lesznek az előző kérelem válaszára.
  
  * Nagy **számítási igényű munkaterhelések elvégzése dedikált szálon** – az előző tipphez hasonló okokból, például az összetett adatfeldolgozáshoz a legjobban egy külön szálban kell elhelyezni. Egy másik adattárból származó adatokra lekérő kérelem (például ha a szál Azure Cosmos DB-és Spark-adattárakat is használ) nagyobb késést tapasztalhat, és azt javasoljuk, hogy egy további szálat indítson el, amely a másik adattártól érkező választ vár.
  
    * A Azure Cosmos DB aszinkron Java SDK v2-ben található alapul szolgáló hálózati IO-t a rendszer a nettó értékkel kezeli, ezekkel a [tippekkel elkerülhetők a nettó IO-szálakat blokkoló kódolási minták](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread).
  
  * **Adatmodellezés** – a Azure Cosmos db SLA azt feltételezi, hogy a dokumentum mérete kisebb, mint 1KB. Ha optimalizálni szeretné az adatmodellt és a programozást, a kisebb méretű dokumentumok méretének csökkentése általában a késés csökkenését eredményezi. Ha a 1KB-nál nagyobb méretű dokumentumok tárolására és lekérésére lesz szüksége, az ajánlott módszer az Azure Blob Storageban lévő adatokra mutató dokumentumok használata.

* **A particionált gyűjtemények párhuzamos lekérdezésének finomhangolása**

  Azure Cosmos DB aszinkron Java SDK v2 támogatja a párhuzamos lekérdezéseket, amelyek lehetővé teszik a particionált gyűjtemények párhuzamos lekérdezését. További információ: az SDK-k használatához kapcsolódó [kód-minták](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) . A párhuzamos lekérdezések úgy vannak kialakítva, hogy a lekérdezési késést és az adatátvitelt a soros munkatársaik

  * ***SetMaxDegreeOfParallelism finomhangolása\:***
    
    A párhuzamos lekérdezések több partíció párhuzamos lekérdezésével működnek. Az egyedi particionált gyűjteményekből származó adatok azonban a lekérdezéssel kapcsolatos sorosan kerülnek beolvasásra. Ezért a setMaxDegreeOfParallelism használatával állítsa be a legtöbb teljesítményű lekérdezés elérésének maximális esélyét biztosító partíciók számát, ha az összes többi rendszerfeltétel változatlan marad. Ha nem ismeri a partíciók számát, a setMaxDegreeOfParallelism használatával magas számot állíthat be, a rendszer pedig a minimális párhuzamosságot (a partíciók számát, a felhasználó által megadott bemenetet) adja meg.

    Fontos megjegyezni, hogy a párhuzamos lekérdezések a legjobb előnyöket nyújtják, ha az adatforgalom egyenletesen oszlik el az összes partíció között a lekérdezés tekintetében. Ha a particionált gyűjtemény úgy van particionálva, hogy a lekérdezés által visszaadott összes adat többsége néhány partíción (egy partíció a legrosszabb esetben) van, akkor a lekérdezés teljesítményét a partíciók szűk keresztmetszete okozhatja.

  * ***SetMaxBufferedItemCount finomhangolása\:***
    
    A párhuzamos lekérdezés úgy lett kialakítva, hogy előzetesen beolvassa az eredményeket, miközben az ügyfél az aktuális eredményt dolgozza fel. Az előzetes beolvasás a lekérdezés teljes késésének javulását segíti elő. a setMaxBufferedItemCount korlátozza az előre beolvasott eredmények számát. A setMaxBufferedItemCount beállítása a visszaadott eredmények várt számához (vagy egy magasabb szám) lehetővé teszi a lekérdezés számára, hogy a lehető legtöbbet fogadja az előzetes lekéréstől.

    Az előzetes lekérés ugyanúgy működik, mint a Maxanalyticsunits, és egyetlen puffer van az összes partícióból származó adatokhoz.

* **Leállítási megvalósítása getRetryAfterInMilliseconds időközönként**

  A teljesítmény tesztelése során növelje a terhelést, amíg a kérelmek kis száma le nem kerül a szabályozásba. Ha szabályozva van, az ügyfélalkalmazás leállítási kell lennie a kiszolgáló által megadott újrapróbálkozási időköznek. A leállítási tiszteletben tartásával biztosítható, hogy az újrapróbálkozások között minimálisan mennyi időt kell várnia.

* **Az ügyfél felskálázása – munkaterhelés**

  Ha magas átviteli sebességű (>50 000 RU/s) tesztelést végez, előfordulhat, hogy az ügyfélalkalmazás szűk keresztmetszetet okoz a processzor vagy a hálózat kihasználtsága miatt. Ha eléri ezt a pontot, továbbra is folytathatja a Azure Cosmos DB-fiók leküldését az ügyfélalkalmazások több kiszolgálón való skálázásával.

* **Név alapú címzés használata**

  Használjon név-alapú címzést, ahol a hivatkozások formátuma a `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` SelfLinks helyett ( \_ saját), amelynek formátuma a `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` hivatkozás létrehozásához használt összes erőforrás ResourceId: beolvasásának elkerülése. Továbbá, mivel ezek az erőforrások újra létre lettek (valószínűleg ugyanazzal a névvel), előfordulhat, hogy a gyorsítótárazás nem segít.

* **A jobb teljesítmény érdekében a lekérdezések/olvasási hírcsatornák méretének finomhangolása**

  A dokumentumok tömeges beolvasása a hírcsatornák olvasási funkciójának használatával (például readDocuments) vagy SQL-lekérdezés kiállításakor az eredményeket szegmentált módon adja vissza, ha az eredményhalmaz túl nagy. Alapértelmezés szerint a rendszer az eredményeket 100 elemből vagy 1 MB-ra adja vissza, amely a határértékek közül az első.

  Ha csökkenteni szeretné az összes vonatkozó eredmény beolvasásához szükséges hálózati kör alakú utak számát, az [x-MS-Max-Item-](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) Request fejléc használatával növelheti az oldal méretét a legfeljebb 1000 értékre. Olyan esetekben, ahol csak néhány eredményt kell megjelenítenie, például ha a felhasználói felület vagy az Application API csak 10 eredményt ad vissza, az olvasási és lekérdezési teljesítmény csökkentése érdekében csökkentheti az oldal méretét is 10 értékre.

  Az oldalméret a setMaxItemCount metódussal is megadható.

* **A megfelelő ütemező használata (az Event loop IO-szálak kilopásának elkerülése)**

  A Azure Cosmos DB aszinkron Java SDK v2 a nem blokkoló IO- [t használja.](https://netty.io/) Az SDK az i/o-műveletekhez rögzített számú i/o-t használ (a gép számos CPU-magja). Az API által visszaadott megfigyelhető érték a megosztott i/o-eseményekhez tartozó többoldalas szálakat eredményezi. Ezért fontos, hogy ne tiltsa le a megosztott IO-esemény hurkos szálait. Ha CPU-igényes munkahelyi vagy letiltási műveletet hajt végre az IO-esemény hurkos szál szálán, a holtpontot okozhat, vagy jelentősen csökkentheti az SDK átviteli sebességét.

  Például az alábbi kód egy CPU-igényes munkát hajt végre az Event loop IO-szálon:

  **Aszinkron Java SDK v2 (Maven com. microsoft. Azure:: Azure-cosmosdb)**

  ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
  ```

  Ha az eredmény megérkezik, ha a CPU-igényes munkát szeretné elvégezni az eredményen, kerülje az Event loop IO-szálon végzett műveletét. Ehelyett saját ütemező használatával biztosíthatja a saját szálát a munka futtatásához.

  **Aszinkron Java SDK v2 (Maven com. microsoft. Azure:: Azure-cosmosdb)**

  ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
  ```

  A munkája típusától függően a megfelelő meglévő RxJava ütemező használatával kell használnia a munkát. Itt olvashat [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html) .

  További információkért tekintse meg a [GitHub oldalt](https://github.com/Azure/azure-cosmosdb-java) Azure Cosmos db ASZINKRON Java SDK v2-hez.

* **A nettó naplózás letiltása**

    A további CPU-költségek elkerülése érdekében a többfunkciós kódtár naplózása beszédes, és ki kell kapcsolni (a bejelentkezés a konfigurációban nem lehet elég). Ha nem hibakeresési módban van, tiltsa le a nettó naplózást. Tehát ha a log4j használatával távolítja el a további CPU-költségeket a (z) ``org.apache.log4j.Category.callAppenders()`` rendszerből, adja hozzá a következő sort a kód számára:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **Operációs rendszer által megnyitott fájlok erőforrás-korlátja**
 
    Bizonyos linuxos rendszerek (például a Red Hat) felső korláttal rendelkeznek a megnyitott fájlok számától, így a kapcsolatok teljes száma. A jelenlegi korlátok megtekintéséhez futtassa a következő parancsot:

    ```bash
    ulimit -a
    ```

    A megnyitott fájlok (nem fájl) számának elég nagynak kell lennie ahhoz, hogy elegendő hely legyen a konfigurált és az operációs rendszer által megnyitott fájlok számára. Ez módosítható úgy, hogy nagyobb a kapcsolatok készletének mérete.

    Nyissa meg a Limits. conf fájlt:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    A következő sorok hozzáadása/módosítása:

    ```
    * - nofile 100000
    ```

## <a name="indexing-policy"></a>Indexelési szabályzat
 
* **Nem használt útvonalak kizárása az indexelésből a gyorsabb írás érdekében**

    Azure Cosmos DB indexelési házirendje lehetővé teszi annak meghatározását, hogy mely dokumentum-elérési utakat kell belefoglalni vagy kizárni az indexelésből az indexelési útvonalak (setIncludedPaths és setExcludedPaths) használatával. Az indexelési útvonalak használata javíthatja az írási teljesítményt és az alacsonyabb indexű tárolást olyan helyzetekben, amikor a lekérdezési mintákat előre ismerték, mivel az indexelési költségek közvetlenül az indexelt egyedi útvonalak számával vannak összekapcsolva. Az alábbi kód például azt mutatja be, hogyan zárható ki a dokumentumok (más néven részfa) teljes szakasza az indexelésből a "*" helyettesítő karakter használatával.

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-indexing"></a>Aszinkron Java SDK v2 (Maven com. microsoft. Azure:: Azure-cosmosdb)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    További információ: [Azure Cosmos db indexelési házirendek](./index-policy.md).

## <a name="throughput"></a><a id="measure-rus"></a>Teljesítmény

* **Az alacsonyabb kérelmek egységének mérése és finomhangolása/második használat**

    A Azure Cosmos DB az adatbázis-műveletek gazdag készletét kínálja, beleértve a UDF, tárolt eljárásokkal és triggerekkel kapcsolatos, és az adatbázis-gyűjteményen belüli összes műveletet. Az egyes ilyen műveletekhez kapcsolódó költségek a művelet végrehajtásához szükséges CPU, IO és memória függvényében változnak. A hardveres erőforrások gondolkodása és kezelése helyett a kérések egysége (RU) egyetlen mértékként használható a különböző adatbázis-műveletek végrehajtásához és az alkalmazásokra vonatkozó kérések kiszolgálásához szükséges erőforrásokhoz.

    Az átviteli sebesség az egyes tárolók számára beállított [kérelmek egységeinek](request-units.md) száma alapján lett kiépítve. A kérelem egységének felhasználását a rendszer másodpercenkénti arányban értékeli. Azok az alkalmazások, amelyek túllépik a tárolók kiépített kérelmi egységének sebességét, csak a tároló számára kiépített szint alá csökkennek. Ha az alkalmazás magasabb adatátviteli kapacitást igényel, akkor a további kérelmek kiszámításával növelheti a teljesítményt.

    A lekérdezés bonyolultsága befolyásolja, hogy hány kérelem-egységet használ a művelet. A predikátumok száma, a predikátumok természete, a UDF száma és a forrásadatok készletének mérete egyaránt befolyásolja a lekérdezési műveletek költségeit.

    Bármilyen művelet (létrehozás, frissítés vagy törlés) mértékének méréséhez vizsgálja meg az [x-MS-Request-Charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) fejlécet a műveletek által felhasznált kérelmek mennyiségének méréséhez. A ResourceResponse vagy a FeedResponse egyenértékű RequestCharge tulajdonságát is megtekintheti \<T> \<T> .

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-requestcharge"></a>Aszinkron Java SDK v2 (Maven com. microsoft. Azure:: Azure-cosmosdb)

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Az ebben a fejlécben visszaadott kérelem díja a kiépített átviteli sebesség töredéke. Ha például 2000 RU/s van kiépítve, és ha az előző lekérdezés a 1000 1KB-dokumentumokat adja vissza, a művelet díja 1000. Mint ilyen, egy másodpercen belül a kiszolgáló csak két ilyen kérést értékel ki a későbbi kérelmek korlátozása előtt. További információ: a [kérelmek egységei](request-units.md) és a [kérési egység kalkulátora](https://www.documentdb.com/capacityplanner).

* **A kezelői sebesség korlátozása/kérelmek arányának aránya túl nagy**

    Ha egy ügyfél megpróbál meghaladni egy fiók fenntartott átviteli sebességét, nincs teljesítmény-romlás a kiszolgálón, és a lefoglalt szinten túl nem használható az átviteli kapacitás. A kiszolgáló megelőző jelleggel a kérést a RequestRateTooLarge (429-es HTTP-állapotkód), és visszaküldi az [x-MS-újrapróbálkozás-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) fejlécet, jelezve azt az időtartamot (ezredmásodpercben), ameddig a felhasználónak meg kell várnia a kérés ismételt megkísérlése előtt.

   ```xml
   HTTP Status 429,
   Status Line: RequestRateTooLarge
   x-ms-retry-after-ms :100
   ```

    Az SDK-k minden implicit módon elkapják ezt a választ, tiszteletben tartsák a kiszolgáló által megadott újrapróbálkozás utáni újrapróbálkozást, majd próbálja megismételni a kérelmet. Ha a fiókját több ügyfél egyidejűleg nem éri el, a következő újrapróbálkozás sikeres lesz.

    Ha több ügyfél halmozottan működik, és a kérések aránya meghaladja a kérelmek arányát, akkor előfordulhat, hogy az ügyfél az újrapróbálkozások alapértelmezett száma jelenleg 9 belső értékre van állítva. Ebben az esetben az ügyfél egy 429-as állapotkódot tartalmazó DocumentClientException hoz az alkalmazáshoz. Az újrapróbálkozások alapértelmezett száma módosítható a ConnectionPolicy-példány setRetryOptions használatával. Alapértelmezés szerint a 429-as kódú DocumentClientException a rendszer a 30 másodperces kumulatív várakozási idő után adja vissza, ha a kérés továbbra is a kérelmek arányán felül működik. Ez akkor is előfordul, ha a jelenlegi újrapróbálkozások száma kisebb, mint az újrapróbálkozások maximális száma, legyen az alapértelmezett 9-es vagy felhasználó által definiált érték.

    Míg az automatikus újrapróbálkozási viselkedés segíti a legtöbb alkalmazás rugalmasságának és használhatóságának javítását, akkor előfordulhat, hogy a teljesítményre vonatkozó teljesítménymutatók végrehajtásakor a rendszer hasznosnak bizonyul, különösen a késés mérése során. Az ügyfél által megfigyelt késés megszegi, ha a kísérlet megkeresi a kiszolgáló szabályozását, és az ügyfél-SDK-t csendes újrapróbálkozás okozta. A teljesítmény-kísérletek során felmerülő késések elkerülése érdekében mérje fel az egyes műveletek által visszaadott díjat, és győződjön meg arról, hogy a kérelmek a fenntartott kérelmek arányában működnek. További információt a [kérelmek egységei](request-units.md)című témakörben talál.

* **Tervezzen kisebb dokumentumokat a nagyobb átviteli sebesség érdekében**

    Egy adott műveletre vonatkozó kérelem díja (a kérelmek feldolgozási díja) közvetlenül összefügg a dokumentum méretével. A nagyméretű dokumentumokon végzett műveletek többek között a kis méretű dokumentumok műveleteinél nagyobb mértékben járnak.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az alkalmazás méretezési és nagy teljesítményű kialakításáról, tekintse meg [a particionálás és skálázás Azure Cosmos DBban](partitioning-overview.md)című témakört.