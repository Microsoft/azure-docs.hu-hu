---
title: Migrálás a CouchBase-ből Azure Cosmos DB SQL API-ba
description: Részletes útmutató a CouchBase-ről Azure Cosmos DB SQL API-ra való áttelepítéshez
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.custom: devx-track-java
ms.openlocfilehash: a15c6b5919f428b28daab86fea9c3b6473d19162
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606198"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migrálás a CouchBase-ből Azure Cosmos DB SQL API-ba
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB egy méretezhető, globálisan elosztott, teljes körűen felügyelt adatbázis. Garantáltan alacsony késésű hozzáférést biztosít az adataihoz. Ha többet szeretne megtudni a Azure Cosmos DBről, tekintse meg az [áttekintő](introduction.md) cikket. Ez a cikk útmutatást nyújt a Couchbase-hez kapcsolódó Java-alkalmazások áttelepítéséhez Azure Cosmos DB-ben egy SQL API-fiókhoz.

## <a name="differences-in-nomenclature"></a>Különbségek a nómenklatúrában

Az alábbi főbb funkciók a Couchbase képest eltérő módon működnek Azure Cosmos DBban:

| Couchbase | Azure Cosmos DB |
|--|--|
| Couchbase-kiszolgáló | Fiók |
| Gyűjtő | Adatbázis |
| Gyűjtő | Tároló/gyűjtemény |
| JSON-dokumentum | Tétel/dokumentum |

## <a name="key-differences"></a>Fő eltérések

* Azure Cosmos DB rendelkezik egy "ID" mezővel a dokumentumban, míg a Couchbase a gyűjtő részeként rendelkezik AZONOSÍTÓval. Az "ID" mező a partíción belül egyedi.

* A skálázási vagy horizontális Felskálázási módszerrel Azure Cosmos DB a skálákat. Ez azt jelenti, hogy az adatmegosztás több szegmensre vagy partícióra történik. Ezek a partíciók/szegmensek a megadott Partition Key tulajdonság alapján jönnek létre. Kiválaszthatja a partíciós kulcsot az olvasási és írási műveletek optimalizálására, illetve a megfelelő írási/olvasási műveletekre is. További információért lásd a [particionálással](./partitioning-overview.md) foglalkozó cikket.

* Azure Cosmos DB nem szükséges, hogy a legfelső szintű hierarchia szerepeljen a gyűjteményben, mert a gyűjtemény neve már létezik. Ez a funkció sokkal egyszerűbbé teszi a JSON-struktúrát. Az alábbi példa az adatmodellben a Couchbase és a Azure Cosmos DB közötti különbségeket mutatja:

   **Couchbase**: dokumentum azonosítója = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos db**: Tekintse meg az "azonosító" kifejezést a dokumentumban az alább látható módon

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Java SDK-támogatás

Azure Cosmos DB a következő SDK-kat támogatja a különböző Java-keretrendszerek támogatásához:

* Aszinkron SDK
* Spring boot SDK

A következő szakaszok ismertetik, hogy mikor kell használni ezeket az SDK-kat. Vegyünk például egy olyan példát, amelyben háromféle számítási feladat létezik:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase & Spring adatalapú egyéni lekérdezések

Ha az áttelepíteni kívánt munkaterhelés a Spring boot-alapú SDK-ra épül, akkor a következő lépéseket hajthatja végre:

1. Szülő hozzáadása a POM.xml fájlhoz:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Tulajdonságok hozzáadása a POM.xml fájlhoz:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Függőségek hozzáadása a POM.xml fájlhoz:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Adja hozzá az alkalmazás tulajdonságait az erőforrások területen, és adja meg a következőket. Ügyeljen rá, hogy cserélje le az URL-címet, a kulcsot és az adatbázis neve paramétert:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Adja meg a gyűjtemény nevét a modellben. További megjegyzéseket is megadhat. Például azonosító, partíciós kulcs, amely explicit módon megjelöli őket:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

A következő kódrészletek a szifilisz-műveletekhez használhatók:

### <a name="insert-and-update-operations"></a>Műveletek beszúrása és frissítése

Ahol a *_repo* a tárház objektuma, a *doc* pedig a POJO osztály objektuma. `.save`A használatával beszúrhat vagy upsert (ha megtalálható a megadott azonosítójú dokumentum). A következő kódrészlet bemutatja, hogyan szúrhat be vagy frissíthet egy doc-objektumot:

```_repo.save(doc);```

### <a name="delete-operation"></a>Művelet törlése

Vegye figyelembe a következő kódrészletet, ahol a doc objektum azonosító és partíciós kulcs kötelező az objektum megkereséséhez és törléséhez:

```_repo.delete(doc);```

### <a name="read-operation"></a>Olvasási művelet

A dokumentumot a partíciós kulcs megadásával vagy anélkül is elolvashatja. Ha nem adja meg a partíciós kulcsot, akkor a rendszer több partíciós lekérdezésként kezeli. Vegye figyelembe a következő mintakód-mintákat, az első az azonosító és a partíció kulcsa mező használatával hajtja végre a műveletet. A második példa egy normál mezőt használ & a Partition Key mező meghatározása nélkül.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

Így már használhatja az alkalmazást Azure Cosmos DB használatával. Az ebben a dokumentációban ismertetett példában a [CouchbaseToCosmosDB-SpringCosmos GitHub-](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/SpringCosmos) tárházban elérhető kód minta szerepel.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase & N1QL-lekérdezések használatával

A N1QL lekérdezések a lekérdezéseknek a Couchbase való definiálásának módja.

|N1QL-lekérdezés | Azure CosmosDB-lekérdezés|
|-------------------|-------------------|
|Válassza a META ( `TravelDocument` ). ID azonosítót, `TravelDocument` . * elemet, `TravelDocument` ahol `_type` = "com. xx. xx. xx. xxx. xxx. xxxx" és az ország = "India", a vízumok pedig minden m. type = = "Multi-Entry" és m. Country in ["India", Bhután "] Order by ` Validity` desc limit 25 eltolás 0 | Válassza ki a c. id, a c értéket a c: c. ország = "India", ahol c._type = "com. xx. xx. xx. xxx. xxx. xxxx" és c. ország = "India" és m. type = "Multi-Entry" és m. Country IN ("India", "Bhután") ORDER BY c |

A N1QL-lekérdezésekben a következő változások láthatók:

* Nem kell használnia a META kulcsszót, vagy az első szintű dokumentumra kell hivatkoznia. Ehelyett saját hivatkozást hozhat létre a tárolóhoz. Ebben a példában a "c"-ként tekintjük meg (ez bármi lehet). Ez a hivatkozás az összes első szintű mező előtagjaként szolgál. Fr példa: c.id, c. ország stb.

* Az "ANY" helyett most már csatlakozhat az aldokumentumhoz, és egy dedikált aliassal (például "m") hivatkozhat. Miután létrehozott egy aliast az aldokumentumhoz, aliast kell használnia. Például: m. Country.

* Az ELTOLÁSi folyamat különbözik Azure Cosmos DB lekérdezésben, először meg kell adnia az ELTOLÁSt, majd a KORLÁTot. Javasoljuk, hogy ne használja a Spring adatsdk-t, ha maximálisan definiált egyéni lekérdezéseket használ, mivel az ügyfél oldalán szükségtelen terhelést okozhat, miközben a lekérdezés átadása Azure Cosmos DB. Ehelyett egy közvetlen aszinkron Java SDK-t használunk, amely ebben az esetben sokkal hatékonyabban használható.

### <a name="read-operation"></a>Olvasási művelet

Használja az aszinkron Java SDK-t a következő lépésekkel:

1. Konfigurálja a következő függőséget a POM.xml fájlra:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Az alábbi példában látható módon hozzon létre egy Azure Cosmos DBhoz tartozó kapcsolatok objektumát a `ConnectionBuilder` metódus használatával. Ügyeljen arra, hogy a deklarációt a beanbe helyezze, hogy a következő kód csak egyszer legyen végrehajtva:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. A lekérdezés végrehajtásához a következő kódrészletet kell futtatnia:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Most a fenti módszer segítségével több lekérdezést is átadhat, és problémamentesen végrehajthatja a végrehajtást. Ha egy nagyméretű lekérdezés végrehajtására van szükség, amely több lekérdezésre is bontható, akkor az előző helyett próbálja meg a következő kódrészletet:

```java
for(SqlQuerySpec query:queries)
{
   objFlux= container.queryItems(query, fo);
   objFlux .publishOn(Schedulers.elastic())
         .subscribe(feedResponse->
            {
               if(feedResponse.results().size()>0)
               {
                  _docs.addAll(feedResponse.results());
               }
            
            },
            Throwable::printStackTrace,latch::countDown);
   lstFlux.add(objFlux);
}
                  
      Flux.merge(lstFlux);
      latch.await();
}
```

Az előző kóddal párhuzamosan futtathat lekérdezéseket, és növelheti az elosztott végrehajtást az optimalizáláshoz. Ezen túl az INSERT és a Update művelet is futtatható:

### <a name="insert-operation"></a>Művelet beszúrása

A dokumentum beszúrásához futtassa a következő kódot:

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Ezután fizessen elő a Mono-ra:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.statusCode()!=successStatus)
              {
                 throw new RuntimeException(resourceResponse.toString());
              }
           },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert művelet

A Upsert művelet végrehajtásához meg kell adnia a frissíteni kívánt dokumentumot. A teljes dokumentum beolvasásához használja az olvasási művelet fejléc alatt említett kódrészletet, majd módosítsa a kötelező mező (ka) t. A következő kódrészlet upsert a dokumentumot:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Ezután fizessen elő a Mono-ra. Tekintse meg a Mono előfizetési kódrészletet a beszúrási műveletben.

### <a name="delete-operation"></a>Művelet törlése

A következő kódrészlet végrehajtja a törlési műveletet:

```java
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Ezután fizessen elő a Mono-ra, tekintse meg a Mono előfizetés kódrészletet a beszúrási műveletben. A teljes mintakód a [CouchbaseToCosmosDB-AsyncInSpring GitHub-](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncInSpring) tárházban érhető el.

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase kulcs/érték párokként

Ez egy egyszerű számítási feladat, amelyben lekérdezések helyett keresési műveleteket hajthat végre. Használja a következő lépéseket a kulcs/érték párokhoz:

1. Ügyeljen arra, hogy a "/ID" elsődleges kulcs legyen, amely biztosítja, hogy a keresési művelet közvetlenül az adott partíción legyen végrehajtva. Hozzon létre egy gyűjteményt, és válassza a "/ID" lehetőséget partíciós kulcsként.

1. Az indexelést teljesen ki kell kapcsolni. Mivel keresési műveleteket hajt végre, nincs olyan pont, amely az indexelési terhelést hordozza. Az indexelés kikapcsolásához jelentkezzen be Azure Portal, goto Azure Cosmos DB-fiókkal. Nyissa meg a **adatkezelőt**, válassza ki az **adatbázist** és a **tárolót**. Nyissa meg a **méretezési & beállítások** lapot, és válassza ki az  **indexelési házirendet**. Az indexelési szabályzat jelenleg a következőhöz hasonlít:
    
   ```json
   {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ]
    }
   ````

   Cserélje le a fenti indexelési házirendet a következő szabályzatra:

   ```json
   {
    "indexingMode": "none",
    "automatic": false,
    "includedPaths": [],
    "excludedPaths": []
    }
   ```

1. A kapcsolódási objektum létrehozásához használja az alábbi kódrészletet. A kapcsolódási objektum ( @Bean vagy statikusra kell helyezni):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Most a következő módon hajthatja végre a szifilisz-műveleteket:

### <a name="read-operation"></a>Olvasási művelet

Az elem olvasásához használja a következő kódrészletet:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
      
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.item()!=null)
           {
              doc= resourceResponse.properties().toObject(UserModel.class);
           }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Művelet beszúrása

Egy elem beszúrásához hajtsa végre a következő kódot:

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Ezután fizessen elő a Mono-ra:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
      .subscribe(resourceResponse->
      {
         if(resourceResponse.statusCode()!=successStatus)
            {
               throw new RuntimeException(resourceResponse.toString());
            }
         },
      Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert művelet

Egy elem értékének frissítéséhez tekintse meg az alábbi kódrészletet:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Ezután fizessen elő a Mono-ra, tekintse meg a Mono előfizetés kódrészletet a beszúrási műveletben.

### <a name="delete-operation"></a>Művelet törlése

A törlési művelet végrehajtásához használja a következő kódrészletet:

```java
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Ezután fizessen elő a Mono-ra, tekintse meg a Mono előfizetés kódrészletet a beszúrási műveletben. A teljes mintakód a [CouchbaseToCosmosDB-AsyncKeyValue GitHub-](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncKeyValue) tárházban érhető el.

## <a name="data-migration"></a>Adatok áttelepítése

Az adatmigrálás kétféleképpen végezhető el.

* **Azure Data Factory használata:** Ez az ajánlott módszer az adatáttelepítésre. Konfigurálja a forrást Couchbase és fogadóként Azure Cosmos DB SQL API-ként a részletes lépésekért tekintse meg az Azure [Cosmos DB Data Factory-összekötőt](../data-factory/connector-azure-cosmos-db.md) ismertető cikket.

* **Használja a Azure Cosmos db adatimportálási eszközt:** Ez a beállítás ajánlott a kisebb mennyiségű adattal rendelkező virtuális gépek használatával történő áttelepítésre. A részletes lépésekért lásd az [adatimportálót](./import-data.md) ismertető cikket.

## <a name="next-steps"></a>Következő lépések

* A teljesítmény teszteléséhez lásd: [teljesítmény-és méretezési tesztelés Azure Cosmos db](./performance-testing.md) cikkel.
* A kód optimalizálásához tekintse meg [Azure Cosmos db cikk teljesítményével kapcsolatos tippeket](./performance-tips-async-java.md) .
* Ismerkedjen meg a Java aszinkron v3 SDK-val, az [SDK-referenciával](https://github.com/Azure/azure-cosmosdb-java/tree/v3) .
