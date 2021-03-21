---
title: SQL API Python-példák Azure Cosmos DB
description: A GitHubon található Python-példákat a Azure Cosmos DB gyakori feladataihoz, beleértve a szifilisz-műveleteket.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 08/11/2020
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: 4a1cd5b1773a58cf385fc6cc5c22be8df7b21e20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018955"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python-példák
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET v2 SDK-példák](sql-api-dotnet-samples.md)
> * [.NET v3 SDK-példák](sql-api-dotnet-v3sdk-samples.md)
> * [Java v4 SDK-példák](sql-api-java-sdk-samples.md)
> * [Spring-adatforrások v3 SDK-példák](sql-api-spring-data-sdk-samples.md)
> * [Node.js példák](sql-api-nodejs-samples.md)
> * [Python-példák](sql-api-python-samples.md)
> * [Azure Kódminta-katalógus](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Az [Azure-documentdb-Python](https://github.com/Azure/azure-documentdb-python) GitHub-adattár tartalmazza a szifiliszi műveleteket és a Azure Cosmos db erőforrásokkal kapcsolatos egyéb gyakori műveleteket. Ez a cikk a következő információkat tartalmazza:

* A például szolgáló Python-projektfájlokban található feladatok hivatkozásai.
* A kapcsolódó API-referenciatartalmak hivatkozásai.

## <a name="prerequisites"></a>Előfeltételek

- Egy Cosmos DB-fiók. A lehetőségek a következők:
    * Azure aktív előfizetésen belül:
        * [Hozzon létre egy ingyenes Azure-fiókot](https://azure.microsoft.com/free) , vagy használja a meglévő előfizetését 
        * [Visual Studio havi kreditek](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Ingyenes Azure Cosmos DB szintű](./optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Azure aktív előfizetés nélkül:
        * [Próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/)ingyen, egy 30 napos tesztelési környezetet.
        * [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) 
- [Python 2,7 vagy 3.5.3 +](https://www.python.org/downloads/), a `python` végrehajtható fájllal `PATH` .
- [Visual Studio Code](https://code.visualstudio.com/).
- A [Python-bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)-hoz.
- [Git](https://www.git-scm.com/downloads). 
- [A Pythonhoz készült SQL API SDK Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="database-examples"></a>Adatbázis-példák

A [database_management.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) a Python-minta bemutatja, hogyan végezheti el a következő feladatokat. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-adatbázisokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](account-databases-containers-items.md) fogalmi cikkének használata.

| Feladat | API-referencia |
| --- | --- |
| [Adatbázis létrehozása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [Adatbázis beolvasása azonosító alapján](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [Adatbázisok lekérdezése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [Fiók adatbázisainak felsorolása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [Adatbázis törlése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>Példák tárolókra

A [container_management.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) a Python-minta bemutatja, hogyan végezheti el a következő feladatokat. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-gyűjteményekről, tekintse meg a következő témakört: [adatbázisok, tárolók és elemek](account-databases-containers-items.md) fogalmi cikkének használata.

| Feladat | API-referencia |
| --- | --- |
| [Tároló lekérdezése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |database.query_containers |
| [Tároló létrehozása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |database.create_container |
| [Egy adatbázis összes tárolójának listázása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |database.list_containers |
| [Tároló beszerzése azonosító alapján](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |database.get_container_client |
| [Tároló kiépített átviteli sebességének kezelése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer, container.replace_throughput|
| [Tároló törlése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |database.delete_container |

## <a name="item-examples"></a>Példák elemekre

A [item_management.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) a Python-minta bemutatja, hogyan végezheti el a következő feladatokat. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-dokumentumokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](account-databases-containers-items.md) fogalmi cikkének használata.

| Feladat | API-referencia |
| --- | --- |
| [Elemek létrehozása egy tárolóban](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [Elemek olvasása azonosító alapján](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [Egy tároló összes elemének olvasása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [Egy tétel lekérdezése az azonosító alapján](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [Elem cseréje](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [Upsert egy elemmel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [Elem törlése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [Tárolóban lévő elemek módosítási csatornájának beolvasása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>Indexelési példák

A [index_management.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) a Python-minta bemutatja, hogyan végezheti el a következő feladatokat. A következő minták futtatása előtt a Azure Cosmos DB indexelésének megismeréséhez lásd: a [házirendek indexelése](index-policy.md), az [indexelési típusok](index-overview.md#index-types)és az [indexelési útvonalak](index-policy.md#include-exclude-paths) fogalmi cikkei.

| Feladat | API-referencia |
| --- | --- |
| [Adott elem kizárása az indexelésből](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Manuális indexelés használata adott elemek indexeléséhez](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | dokumentumok. IndexingDirective. include |
| [Elérési utak kizárása az indexelésből](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |A tulajdonságban kizárandó elérési utak megadása `IndexingPolicy` |
| [Tartományindexek használata sztringeken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Az indexelési szabályzat definiálása a karakterlánc adattípusú tartomány-indexekkel. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Indexátalakítás végrehajtása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |database.replace_container (használja a frissített indexelési házirendet)|
| [Vizsgálatok használata, ha csak a kivonatoló index létezik az elérési úton](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | a `enable_scan_in_query=True` és az `enable_cross_partition_query=True` elemek lekérdezésének beállítása |
