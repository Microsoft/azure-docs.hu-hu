---
title: 'Azure Cosmos DB: Java-példák az SQL API-hoz | Microsoft Docs'
description: A GitHub tartalmaz az Azure Cosmos DB általános feladataihoz, többek között a CRUD-műveletekhez kötődő Java-példákat.
keywords: NoSQL-példa
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: sample
ms.date: 02/08/2018
ms.author: sngun
ms.openlocfilehash: 00098b02e5f007cfad1076eb3759c424dd9a5d3e
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333870"
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB: Java-példák az SQL API-hoz

> [!div class="op_single_selector"]
> * [.NET-példák](sql-api-dotnet-samples.md)
> * [Java-példák](sql-api-java-samples.md)
> * [Aszinkron Java-példák](sql-api-async-java-samples.md)
> * [Node.js-példák](sql-api-nodejs-samples.md)
> * [Python-példák](sql-api-python-samples.md)
> * [Azure-kódminták katalógusa](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Az [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java) GitHub-adattárban megtalálhatók a legújabb mintaalkalmazások, amelyek CRUD műveleteket és más általános műveleteket végeznek az Azure Cosmos DB erőforrásokon. Ez a cikk a következő információkat tartalmazza:

* Az egyes Java-projektfájlokban lévő feladatok hivatkozásai. 
* A kapcsolódó API-referenciatartalmak hivatkozásai.

**Előfeltételek**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Aktiválhatja Visual Studio-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetés minden hónapban biztosít Önnek jóváírást, amelyekkel fizetős Azure-szolgáltatásokat használhat.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

A mintaalkalmazás futtatásához a következőkre lesz szüksége:

* Java fejlesztői készlet 7-as verziója
* Microsoft Azure DocumentDB Java SDK

Igény szerint a Maven segítségével is beszerezheti a legújabb Microsoft Azure DocumentDB Java SDK bináris fájlokat a projekthez. A Maven automatikusan hozzáadja az összes szükséges függőséget. Egyéb esetben közvetlenül letöltheti a pom.xml fájlban szereplő függőségeket, és az elérési úthoz adhatja őket.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**A mintaalkalmazások futtatása**

A mintaadattár klónozása:
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

A mintákat az Eclipse használatával vagy a parancssorból futtathatja a Maven segítségével.

Futtatás az Eclipse-ből:
* Töltse be a fő szülő projekt pom.xml fájlját az Eclipse-be; automatikusan be kell töltenie a documentdb-examples modult.
* A minták futtatásához érvényes Azure Cosmos DB-végpontra van szüksége. A végpontok beolvasása az `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java` mappából történik.
* A végpont hitelesítő adatait VM-argumentumként illesztheti be az Eclipse JUnit futtatási konfigurációba, vagy a végpont hitelesítő adatait az AccountCredentials.java fájlban helyezheti el.
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* Most már JUnit tesztként futtathatja a mintákat az Eclipse-ben.

Futtatás a parancssorból:
* A minták futtatásának másik módja a Maven használata:
* Futtassa a Mavent, és illessze be az Azure Cosmos DB-végpont hitelesítő adatait:
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > Minden minta önálló; magát állítja be, és törli önmagát. A minták több hívást intéznek a [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection) felé. Minden ilyen hívás esetén az előfizetésére 1 órányi használat terhelődik a létrehozott gyűjtemény teljesítményszintjének megfelelően. 
   > 
   > 

## <a name="database-examples"></a>Adatbázispéldák
A [DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) fájl mutatja a következő feladatok elvégzési módját:

| Tevékenység | API-referencia |
| --- | --- |
| [Adatbázis létrehozása és olvasása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource.setid) |
| [Adatbázis létrehozása és törlése](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletedatabase) |
| [Adatbázis létrehozása és lekérdezése](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydatabases) |

## <a name="collection-examples"></a>Gyűjteménypéldák
A [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) fájl mutatja a következő feladatok elvégzési módját:

| Tevékenység | API-referencia |
| --- | --- |
| [Egypartíciós gyűjtemény létrehozása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Egyéni többpartíciós gyűjtemény létrehozása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_collection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._partition_key_definition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._request_options) |
| [Gyűjtemény törlése](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletecollection) |

## <a name="document-examples"></a>Dokumentumpéldák
A [DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) fájl mutatja a következő feladatok elvégzési módját:

| Tevékenység | API-referencia |
| --- | --- |
| [Dokumentum létrehozása, olvasása és törlése](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletedocument) |
| [Dokumentum létrehozása programozható dokumentumdefinícióval](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [Dokumentum](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource.setid) |

## <a name="indexing-examples"></a>Indexelési példák
A [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) fájl mutatja a következő feladatok elvégzési módját:

| Tevékenység | API-referencia |
| --- | --- |
| [Index létrehozása és indexelési szabályzat beállítása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy) |

További információ az indexelésről: [Azure Cosmos DB indexelési szabályzat](index-policy.md).

## <a name="query-examples"></a>Lekérdezéspéldák
A [DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) fájl mutatja a következő feladatok elvégzési módját:

| Tevékenység | API-referencia |
| --- | --- |
| [Partíciók közötti egyszerű dokumentumlekérdezés végrehajtása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setenablecrosspartitionquery) |
| [Lekérdezés szerinti rendezés](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse<T>.getQueryIterator](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_response.getqueryiterator) |

További információ a lekérdezések írásáról: [SQL-lekérdezés az Azure Cosmos DB-ben](how-to-sql-query.md).

## <a name="offer-examples"></a>Ajánlatpéldák
Az [OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) fájl bemutatja, hogyan végezheti el a következő feladatokat:

| Tevékenység | API-referencia |
| --- | --- |
| [Gyűjtemény létrehozása és a teljesítmény beállítása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection)<br>[RequestOptions.setOfferThroughput ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._request_options.setofferthroughput) |
| [Gyűjtemény olvasása a társított ajánlat megkereséséhez](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.queryoffers) |

## <a name="partition-key-examples"></a>Partíciókulcs-példák
A [SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) fájl mutatja a következő feladatok elvégzési módját:

| Tevékenység | API-referencia |
| --- | --- |
| [Egypartíciós gyűjtemény létrehozása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Egypartíciós gyűjtemény teljesítményajánlatának módosítása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer) |

## <a name="stored-procedure-examples"></a>Tárolt eljárások példái
A [StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) fájl bemutatja, hogyan végezheti el a következő feladatokat:

| Tevékenység | API-referencia |
| --- | --- |
| [Tárolt eljárás létrehozása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._stored_procedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createstoredprocedure) |
| [Tárolt eljárás futtatása argumentumokkal](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
| [Tárolt eljárás futtatása objektumargumentummal](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
