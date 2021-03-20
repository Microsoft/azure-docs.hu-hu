---
title: 'Azure Cosmos DB: .NET-példák az SQL API-hoz'
description: C# .NET-példák keresése a GitHubon a Azure Cosmos DB SQL API-t használó gyakori feladatokhoz, beleértve a szifilisz-műveleteket is.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/23/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 2e7bfd3735a57ddfa5d6100e18cf7a5676c1b9b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019040"
---
# <a name="azure-cosmos-db-net-examples-for-the-sql-api"></a>Azure Cosmos DB: .NET-példák az SQL API-hoz
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET v2 SDK-példák](sql-api-dotnet-samples.md)
> * [.NET v3 SDK-példák](sql-api-dotnet-v3sdk-samples.md)
> * [Java v4 SDK-példák](sql-api-java-sdk-samples.md)
> * [Spring-adatforrások v3 SDK-példák](sql-api-spring-data-sdk-samples.md)
> * [Node.js példák](sql-api-nodejs-samples.md)
> * [Python-példák](sql-api-python-samples.md)
> * [Azure Kódminta-katalógus](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Az [Azure-Cosmos-DotNet-v2 GitHub-](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples) tárház tartalmazza a legújabb .net-példákat a szifilisz és egyéb gyakori műveletek végrehajtásához Azure Cosmos db erőforrásokon. Ez a cikk a következő információkat tartalmazza:

* Az egyes C#-projektfájlokban lévő feladatok hivatkozásai. 
* A kapcsolódó API-referenciatartalmak hivatkozásai.

A .NET SDK 3,0-as (előzetes verziójú) kódjának mintáit lásd: az [Azure-Cosmos-DotNet-v3 GitHub-](https://github.com/Azure/azure-cosmos-dotnet-v3) tárház legújabb mintái. 

## <a name="prerequisites"></a>Előfeltételek

A Visual Studio 2019 és az Azure fejlesztői munkafolyamata telepítve van
- Letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition verziót](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során. 

A [Microsoft.Azure.DocUmentDB NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 

Azure-előfizetés vagy ingyenes Cosmos DB próbaverziós fiók
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [Aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): a Visual Studio-előfizetése havi kreditet biztosít, amelyet a fizetős Azure-szolgáltatásokhoz is használhat.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> A minták önállóak, és a [CreateDocumentCollectionAsync ()](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync)metódusok többszöri meghívásával állíthatók be és tisztíthatók. Minden előfordulás egy órányi használatot számláz az előfizetésben a gyűjtemény teljesítményi rétegében. 
> 

## <a name="database-examples"></a>Adatbázis-példák
A minta *DatabaseManagement* -projekt [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L75-L91) metódusa a következő feladatok elvégzését mutatja be. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-adatbázisokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek használata](account-databases-containers-items.md). 

| Feladat | API-referencia |
| --- | --- |
| [Adatbázis létrehozása](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L77) |[DocumentClient. Createdatabaseifnotexistasync metódusának](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) |
| [Adatbázis beolvasása azonosító alapján](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L79) |[DocumentClient.ReadDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabaseasync) |
| [Az összes adatbázis olvasása](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L83) |[DocumentClient.ReadDatabaseFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabasefeedasync) |
| [Adatbázis törlése](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L89) |[DocumentClient.DeleteDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedatabaseasync) |

## <a name="collection-examples"></a>Gyűjtemény-példák
A minta *CollectionManagement* -projekt [RunCollectionDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L86-L104) metódusa a következő feladatok elvégzését mutatja be. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-gyűjteményekről, tekintse meg a következő témakört: [adatbázisok, tárolók és elemek használata](account-databases-containers-items.md). 

| Feladat | API-referencia |
| --- | --- |
| [Gyűjtemény létrehozása](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L109) |[DocumentClient. Createdocumentcollectionifnotexistsasync metódusával](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync) 
| [Gyűjtemény konfigurált teljesítményének módosítása](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L190) |[DocumentClient.ReplaceOfferAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync) |
| [Gyűjtemény beolvasása azonosító alapján](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L202) |[DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) |
| [Egy adatbázisban lévő összes gyűjtemény olvasása](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L215) |[DocumentClient.ReadDocumentCollectionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionfeedasync) |
| [Gyűjtemény törlése](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L228) |[DocumentClient.DeleteDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentcollectionasync) |

## <a name="document-examples"></a>Dokumentumpéldák
A minta *DocumentManagement* -projekt [RunDocumentsDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L109-L118) metódusa a következő feladatok elvégzését mutatja be. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-dokumentumokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek használata](account-databases-containers-items.md). 

| Feladat | API-referencia |
| --- | --- |
| [Dokumentum létrehozása](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L154) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) |
| [Dokumentum olvasása ID (azonosító) alapján](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L168) |[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync) |
| [Egy gyűjteményben lévő összes dokumentum beolvasása](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L190) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Dokumentumok lekérdezése](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L18-L222) |[DocumentClient.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dokumentum cseréje](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L240-L242) |[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Dokumentum frissítése és beszúrása](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L254) |[DocumentClient.UpsertDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.upsertdocumentasync) |
| [Dokumentum törlése](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L275-L277) |[DocumentClient.DeleteDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentasync) |
| [.NET dinamikus objektumok használata](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L349-L397) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync)<br>[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync)<br>[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Dokumentum cseréje feltételes ETag-ellenőrzéssel](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L405-L501) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |
| [Dokumentum olvasása csak a dokumentum módosítása esetén](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L454-L500) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |

## <a name="indexing-examples"></a>Indexelési példák
A minta *IndexManagement* -projekt [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L93-L115) metódusa a következő feladatok elvégzését mutatja be. A következő minták futtatása előtt a Azure Cosmos DB indexelésének megismeréséhez tekintse meg az [index házirendek](index-policy.md), az [indexelési típusok](index-overview.md#index-types)és az [index elérési útjai](index-policy.md#include-exclude-paths)című témakört. 

| Feladat | API-referencia |
| --- | --- |
| [Dokumentum kizárása az indexből](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L123-L162) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.documents.indexingdirective) |
| [Lusta indexelés használata](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L174-L192) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.documents.indexingpolicy.indexingmode) |
| [Adott dokumentum-útvonalak kizárása az indexből](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L202-L263) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.excludedpaths) |
| [Tartományvizsgálati művelet kikényszerítése kivonatolt indexelt elérési útvonalon](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L305-L340) |[FeedOptions.EnableScanInQuery](/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery) |
| [Tartományindexek használata sztringeken](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L265-L316) |[IndexingPolicy.IncludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.includedpaths)<br>[RangeIndex](/dotnet/api/microsoft.azure.documents.rangeindex) |
| [Indexátalakítás végrehajtása](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L318-L370) |[ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync) |

## <a name="geospatial-examples"></a>Térinformatikai példák
A minta *térinformatikai* projekt [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L94-L139) metódusa a következő feladatok elvégzését mutatja be.  A GeoJSON és térinformatikai adatainak a következő minták futtatása előtti megismeréséhez tekintse meg a [térinformatikai és GeoJSON-hely adatainak használata](./sql-query-geospatial-intro.md)című témakört. 

| Feladat | API-referencia |
| --- | --- |
| [Térinformatikai indexelés engedélyezése új gyűjteményen](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L48) |[IndexingPolicy](/dotnet/api/microsoft.azure.documents.indexingpolicy) <br> [IndexKind.Spatial](/dotnet/api/microsoft.azure.documents.indexkind) <br>[DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Dokumentumok beszúrása GeoJSON-pontokkal](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L104-L114) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) </br> [DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Pontok megkeresése adott távolságon belül](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L158-L199) |[ST_DISTANCE](sql-query-st-distance.md) </br> [GeometryOperationExtensions.Distance](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) |
| [Pontok megkeresése egy sokszögön belül](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L204-L227) |[ST_WITHIN](sql-query-st-within.md) </br> [GeometryOperationExtensions. belül](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) </br>[Sokszög](/dotnet/api/microsoft.azure.documents.spatial.polygon) |
| [Térinformatikai indexelés engedélyezése meglévő gyűjteményen](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L385-L391) |[DocumentClient.ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync)<br>[DocumentCollection.IndexingPolicy](/dotnet/api/microsoft.azure.documents.documentcollection.indexingpolicy) |
| [Pont- és sokszögadatok érvényesítése](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L290-L326) |[ST_ISVALID](sql-query-st-isvalid.md)<br>[ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md)<br>[GeometryOperationExtensions.IsValid](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvalid)<br>[GeometryOperationExtensions.IsValidDetailed](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvaliddetailed) |

## <a name="query-examples"></a>Lekérdezéspéldák
A [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L64-L129) metódusa azt mutatja  be, hogyan végezheti el a következő feladatokat az SQL-lekérdezési nyelvtan, a LINQ-szolgáltató lekérdezéssel és a lambda használatával. Ha többet szeretne megtudni a Azure Cosmos DB SQL-lekérdezési referenciáról az alábbi minták futtatása előtt, tekintse meg a [Azure Cosmos db SQL-lekérdezési példákat](./sql-query-getting-started.md). 

| Feladat | API-referencia |
| --- | --- |
| [Az összes dokumentum lekérdezése](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L131-L147) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Egyenlőség lekérdezése == használatával](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L186-L198) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Egyenlőtlenség lekérdezése != és NOT használatával](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L288-L332) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Lekérdezés tartományi operátorokkal, például a >, <, >=, <= használatával](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L334-L355) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Lekérdezés tartományi operátorokkal sztringekben](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L355-L370) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Lekérdezés RENDELÉSsel](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L422-L446) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Lekérdezés összesítő függvényekkel](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L448-L496) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Aldokumentumok használata](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L498-L524) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Lekérdezés dokumentumon belüli illesztésekkel](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L526-L540) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Lekérdezés string, Math és Array operátorral](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L636-L673) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Lekérdezés paraméteres SQL-lel az SqlQuerySpec használatával](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L149-L184) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100))<br>[SqlQuerySpec](/dotnet/api/microsoft.azure.documents.sqlqueryspec) |
| [Lekérdezés explicit lapozással](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L675-L734) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Particionált gyűjtemények lekérdezése párhuzamosan](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L736-L807) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Lekérdezés az ORDER BY particionált gyűjtemények esetén](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L809-L882) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |

## <a name="change-feed-examples"></a>Változáscsatornák példái 
A minta *ChangeFeed* -projekt [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L54-L97) metódusa a következő feladatok elvégzését mutatja be. Ha a következő minták futtatása előtt szeretné megismerni a Azure Cosmos DB a hírcsatornát, tekintse meg az [olvasási Azure Cosmos db módosítási hírcsatorna](read-change-feed.md) és a [hírcsatorna-feldolgozó módosítása](change-feed-processor.md)című témakört. 

| Feladat | API-referencia |
| --- | --- |
| [Változáscsatorna olvasása](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L132) |[DocumentClient.CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery) | 
| [Partíciókulcsok tartományainak olvasása](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L118) |[DocumentClient.ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync) | 

A [ChangeFeedMigrationTool](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/ChangeFeedMigrationTool)módosítása, amely bemutatja, hogyan használható a hírcsatorna-feldolgozó függvénytárának módosítása egy másik Cosmos-tárolóba való replikáláshoz.   

## <a name="server-side-programming-examples"></a>Kiszolgálóoldali programozási példák
A minta *ServerSideScripts* -projekt [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ServerSideScripts/Program.cs#L58-L91) metódusa a következő feladatok elvégzését mutatja be. Ha többet szeretne megtudni a Azure Cosmos DB kiszolgálóoldali programozásáról a következő minták futtatása előtt, tekintse meg a [tárolt eljárások, eseményindítók és felhasználó által definiált függvények](stored-procedures-triggers-udfs.md)című témakört. 

| Feladat | API-referencia |
| --- | --- |
| [Tárolt eljárás létrehozása](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L110) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Tárolt eljárás végrehajtása](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L125) |[DocumentClient.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.executestoredprocedureasync) |
| [Dokumentumcsatorna olvasása tárolt eljáráshoz](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L198) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Tárolt eljárás létrehozása ORDER BY alapján](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L223) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Trigger előtti elem létrehozása](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L273) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Trigger utáni elem létrehozása](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L341) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Felhasználó által definiált függvény (UDF) létrehozása](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L421) |[DocumentClient.CreateUserDefinedFunctionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserdefinedfunctionasync) |

## <a name="user-management-examples"></a>Felhasználókezelés példái
A minta *UserManagement* -projekt [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/UserManagement/Program.cs#L55-L129) metódusa a következő feladatok elvégzését mutatja be:

| Feladat | API-referencia |
| --- | --- |
| [Felhasználó létrehozása](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L93) |[DocumentClient.CreateUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserasync) |
| [Engedélyek beállítása gyűjteményen vagy dokumentumban](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L97) |[DocumentClient.CreatePermissionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createpermissionasync) |
| [Felhasználó engedélylistájának lekérése](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L241) |[DocumentClient.ReadUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readuserasync)<br>[DocumentClient.ReadPermissionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpermissionfeedasync) |
