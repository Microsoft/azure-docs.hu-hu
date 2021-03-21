---
title: A tömeges importálási és frissítési műveletek végrehajtásához használja a Azure Cosmos DB csoportos futtatású Java-kódtárat.
description: Azure Cosmos DB dokumentumok tömeges importálása és frissítése a tömeges végrehajtó Java-kódtár használatával
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 08/26/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: d0ccaf15df62b566b5a52ee7a4031ba7e270a4ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218111"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Műveletek tömeges végrehajtása Azure Cosmos DB-adatokon a tömeges végrehajtási Java-kódtárral
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez az oktatóanyag útmutatást nyújt a Azure Cosmos DB tömeges végrehajtó Java-könyvtárának használatáról az importáláshoz és a Azure Cosmos DB dokumentumok frissítéséhez. Ha többet szeretne megtudni a tömeges végrehajtó függvénytárról, valamint arról, hogy miként segíti a nagy átviteli sebesség és tárterület kihasználását, tekintse meg a [tömeges végrehajtó függvénytár áttekintését](bulk-executor-overview.md) ismertető cikket. Ebben az oktatóanyagban egy olyan Java-alkalmazást hoz létre, amely véletlenszerű dokumentumokat hoz létre, és tömegesen importálnak egy Azure Cosmos-tárolóba. Az importálás után tömegesen frissíti a dokumentumok egyes tulajdonságait. 

Jelenleg a tömeges végrehajtó függvénytárat csak Azure Cosmos DB SQL API és Gremlin API-fiókok támogatják. Ez a cikk azt ismerteti, hogyan használható a tömeges végrehajtó Java-függvénytár SQL API-fiókokkal. A tömeges végrehajtó .NET-kódtár Gremlin API-val történő használatáról további információt a [tömeges műveletek végrehajtása Azure Cosmos db GREMLIN API-ban](bulk-executor-graph-dotnet.md)című témakörben talál. A fent ismertetett tömeges végrehajtó könyvtár csak a [Azure Cosmos db Java Sync SDK v2](sql-api-sdk-java.md) esetén érhető el, és ez a Java tömeges támogatásának jelenleg javasolt megoldása. Jelenleg nem érhető el a 3. x, 4. x vagy más SDK-verzióhoz.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.  

* Az Azure-előfizetések nélkül, díjmentesen és kötelezettségvállalásokon keresztül [Azure Cosmos db ingyen kipróbálhatja](https://azure.microsoft.com/try/cosmosdb/) . Vagy használhatja a [Azure Cosmos db emulátort](./local-emulator.md) a `https://localhost:8081` végponttal. Az elsődleges kulcs a [Kérelmek hitelesítése](local-emulator.md#authenticate-requests) című részben található.  

* [Java Development Kit (JDK) 1.7 +](/java/azure/jdk/)  
  - Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.  

  - Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.

* [Maven](https://maven.apache.org/download.cgi) bináris archívum [letöltése](https://maven.apache.org/install.html) és [telepítése](https://maven.apache.org/)  
  
  - Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.

* Hozzon létre egy Azure Cosmos DB SQL API-fiókot a Java rövid útmutató című cikk [adatbázis-fiók létrehozása](create-sql-api-java.md#create-a-database-account) című szakaszában ismertetett lépések segítségével.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most váltson a Code-ra, és töltsön le egy minta Java-alkalmazást a GitHubról. Ez az alkalmazás csoportos műveleteket hajt végre Azure Cosmos DB adatokon. Az alkalmazás klónozásához nyisson meg egy parancssort, Navigáljon arra a könyvtárra, ahová az alkalmazást másolni szeretné, majd futtassa a következő parancsot:

```bash
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

A klónozott adattár a "\azure-cosmosdb-bulkexecutor-Java-Getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor" mappához képest két mintát tartalmaz: "BulkImport" és "bulkupdate". A "BulkImport" alkalmazás véletlenszerűen létrehozott dokumentumokat hoz létre, és azokat Azure Cosmos DBba importálja. A "bulkupdate" alkalmazás néhány dokumentumot frissít Azure Cosmos DBban. A következő részekben áttekintjük a kódot mindegyik minta alkalmazásban. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Az Azure Cosmos DBbe való tömeges importálás

1. A Azure Cosmos DB kapcsolódási karakterláncai argumentumként vannak beolvasva, és a CmdLineConfiguration. Java fájlban meghatározott változókhoz vannak rendelve.  

2. A DocumentClient objektum a következő utasításokkal lett inicializálva:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. A DocumentBulkExecutor objektum a várakozási idő és a szabályozott kérelmek magas újrapróbálkozási értékeivel lett inicializálva. Ezután a 0 értékre vannak állítva, hogy átadja a zsúfoltság-vezérlést a DocumentBulkExecutor élettartama érdekében.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. Hívja meg a beosztott API-t, amely véletlenszerű dokumentumokat hoz létre tömeges importálásra egy Azure Cosmos-tárolóba. A parancssori konfigurációkat a CmdLineConfiguration. Java fájlon belül is konfigurálhatja.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   A tömeges importálási API JSON-szerializált dokumentumok gyűjteményét fogadja el, és a következő szintaxissal rendelkezik, további részletekért tekintse meg az [API dokumentációját](/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   Az inportál metódus a következő paramétereket fogadja el:
 
   |**Paraméter**  |**Leírás**  |
   |---------|---------|
   |isUpsert    |   A dokumentumok upsert engedélyezésének jelzője. Ha a megadott AZONOSÍTÓJÚ dokumentum már létezik, frissül.  |
   |disableAutomaticIdGeneration     |   Az azonosító automatikus generálásának letiltására szolgáló jelző. Alapértelmezés szerint igaz értékre van állítva.   |
   |maxConcurrencyPerPartitionRange    |  A partíciós kulcs tartományának maximális párhuzamossági foka. Az alapértelmezett érték 20.  |

   **Tömeges importálási válasz objektumának definíciója** A tömeges importálási API-hívás eredménye a következő Get metódusokat tartalmazza:

   |**Paraméter**  |**Leírás**  |
   |---------|---------|
   |int getNumberOfDocumentsImported ()  |   A tömeges importálási API-híváshoz megadott dokumentumokból sikeresen importált dokumentumok teljes száma.      |
   |dupla getTotalRequestUnitsConsumed ()   |  A tömeges importálási API-hívás által felhasznált összes kérelmek egysége (RU).       |
   |Időtartam getTotalTimeTaken ()   |    A tömeges importálási API hívása által a végrehajtás befejezésére tett teljes idő.     |
   |GetErrors listázása \<Exception> () |  Lekéri a hibák listáját, ha nem sikerült beszúrni a tömeges importálási API-híváshoz megadott kötegből kiolvasott dokumentumokat.       |
   |GetBadInputDocuments listázása \<Object> ()  |    Azon helytelen formátumú dokumentumok listája, amelyeket nem sikerült importálni a tömeges importálási API-hívásban. A felhasználónak ki kell javítania a visszaadott dokumentumokat, és újra kell próbálkoznia az importálás A helytelen formátumú dokumentumok közé tartoznak azok a dokumentumok, amelyek azonosító értéke nem sztring (null vagy bármely más adattípus érvénytelennek tekintendő).     |

5. Miután megtörtént a tömeges importálás alkalmazása, hozza létre a parancssori eszközt a forrásból a "MVN tiszta csomag" parancs használatával. Ez a parancs egy jar-fájlt hoz létre a célmappában:  

   ```bash
   mvn clean package
   ```

6. A cél függőségeinek létrehozása után a tömeges importáló alkalmazást a következő parancs használatával hívhatja meg:  

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB's endpoint>*  -masterKey *<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   A tömeges importáló létrehoz egy új adatbázist és egy gyűjteményt, amely a App.config fájlban megadott adatbázis-névvel, gyűjtemény nevével és átviteli értékkel rendelkezik. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>A Azure Cosmos DBban tárolt adatmennyiségek tömeges frissítése

A meglévő dokumentumokat a BulkUpdateAsync API használatával frissítheti. Ebben a példában a Name (név) mezőt egy új értékre állítja be, és eltávolítja a Leírás mezőt a meglévő dokumentumokból. A támogatott mező-frissítési műveletek teljes készletét lásd: [API-dokumentáció](/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Meghatározza a frissítési elemeket a megfelelő mező-frissítési műveletekkel együtt. Ebben a példában a SetUpdateOperation segítségével frissíti a Name (név) mezőt és a UnsetUpdateOperation, hogy eltávolítsa a Leírás mezőt az összes dokumentumból. Más műveleteket is végrehajthat, például egy adott értékkel növelheti a dokumentum mező értékét, leküldheti a konkrét értékeket egy tömb mezőjébe, vagy eltávolíthat egy adott értéket egy tömb mezőből. Ha többet szeretne megtudni a tömeges frissítési API által nyújtott különböző módszerekről, tekintse meg az [API dokumentációját](/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                        
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Hívja meg a updateAll API-t, amely véletlenszerű dokumentumokat hoz létre, majd tömegesen importálja őket egy Azure Cosmos-tárolóba. Beállíthatja, hogy a parancssori konfigurációk a CmdLineConfiguration. Java fájlban legyenek átadva.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   A tömeges frissítési API a frissítendő elemek gyűjteményét fogadja el. Minden frissítési elem meghatározza az AZONOSÍTÓval és a partíciós kulcs értékével azonosított dokumentum-frissítési műveletek listáját. További részletekért tekintse meg az [API dokumentációját](/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   A updateAll metódus a következő paramétereket fogadja el:

   |**Paraméter** |**Leírás** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  A partíciós kulcs tartományának maximális párhuzamossági foka. Az alapértelmezett érték 20.  |
 
   **Tömeges importálási válasz objektumának definíciója** A tömeges importálási API-hívás eredménye a következő Get metódusokat tartalmazza:

   |**Paraméter** |**Leírás**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated ()  |   Azon dokumentumok teljes száma, amelyek sikeresen frissítve lettek a tömeges frissítési API-híváshoz megadott dokumentumokból.      |
   |dupla getTotalRequestUnitsConsumed () |  A tömeges frissítési API-hívás által felhasznált összes kérési egység (RU).       |
   |Időtartam getTotalTimeTaken ()  |   A tömeges frissítési API hívása által a végrehajtás befejezésére tett teljes idő.      |
   |GetErrors listázása \<Exception> ()   |       A frissítési művelettel kapcsolatos működési vagy hálózati problémák listájának beolvasása.      |
   |GetFailedUpdates listázása \<BulkUpdateFailure> ()   |       A frissítések listájának beolvasása, amely nem hajtható végre, valamint a hibákhoz vezető konkrét kivételek.|

3. Miután elvégezte a tömeges frissítés alkalmazását, hozza létre a parancssori eszközt a forrásból a "MVN tiszta csomag" parancs használatával. Ez a parancs egy jar-fájlt hoz létre a célmappában:  

   ```bash
   mvn clean package
   ```

4. A cél függőségeinek létrehozása után a tömeges frissítési alkalmazást a következő parancs használatával hívhatja meg:

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB's endpoint>* -masterKey **<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Teljesítménnyel kapcsolatos tippek 

Tömeges végrehajtó könyvtár használata esetén vegye figyelembe a következő szempontokat a jobb teljesítmény érdekében:

* A legjobb teljesítmény érdekében az alkalmazást egy olyan Azure-beli virtuális gépről futtassa, amely ugyanabban a régióban található, mint a Cosmos DB fiók írási régiója.  
* A magasabb átviteli sebesség elérése érdekében:  

   * A nagy számú dokumentum kezelésére szolgáló memória-probléma elkerülése érdekében állítsa a JVM halom méretét elég nagy számra. Javasolt halom mérete: Max (3GB, 3 * sizeof (az összes dokumentum, amely egy kötegben tömeges importálási API-ként lett átadva)).  
   * Van egy előfeldolgozási idő, amely miatt nagyobb átviteli sebességre lesz szüksége, amikor nagy mennyiségű dokumentummal rendelkező tömeges műveleteket végez. Tehát ha az 10 000 000-es dokumentumokat kívánja importálni, a tömeges importálás 10 alkalommal 10 nagy mennyiségű, 1 000 000-es méretű dokumentumot használ, mint a tömeges importálás 100-es idő100 pontját az egyes 100 000 méretekhez tartozó dokumentumok esetében.  

* Ajánlott egyetlen DocumentBulkExecutor objektumot létrehozni a teljes alkalmazáshoz egyetlen virtuális gépen belül, amely egy adott Azure Cosmos-tárolónak felel meg.  

* Mivel egyetlen tömeges művelet API-végrehajtása nagy mennyiségű adatrészletet használ az ügyfélszámítógép CPU-és hálózati IO-jával. Ez úgy történik, hogy belsőleg több feladatot indít el, így elkerülhető, hogy az alkalmazás folyamatában több egyidejű feladat ne legyen végrehajtva a tömeges működésű API-hívások végrehajtása során. Ha egyetlen virtuális gépen futó egyetlen tömeges művelet API-hívása nem tudja felhasználni a teljes tároló átviteli sebességét (ha a tároló átviteli sebessége > 1 000 000 RU/s), érdemes lehet külön virtuális gépeket létrehozni a tömeges művelet API-hívások egyidejű végrehajtásához.

    
## <a name="next-steps"></a>Következő lépések
* További információ a Maven csomag részleteiről és a tömeges végrehajtó Java-függvénytár kibocsátási megjegyzéséről:[tömeges végrehajtó SDK – részletek](sql-api-sdk-bulk-executor-java.md).