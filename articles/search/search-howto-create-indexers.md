---
title: Indexelő létrehozása
titleSuffix: Azure Cognitive Search
description: Az indexelő tulajdonságainak beállítása az adatforrások és a célhelyek meghatározásához. A futásidejű viselkedések módosításához paramétereket adhat meg.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: ade5880a6b06f448df23eb77d81201a521f1d240
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430045"
---
# <a name="create-a-search-indexer"></a>Keresési indexelő létrehozása

A keresési indexelő egy automatizált munkafolyamatot biztosít a dokumentumok és tartalmak külső adatforrásból történő átviteléhez a keresési szolgáltatás keresési indexéhez. Az eredetileg megtervezett módon Kinyeri a szöveget és a metaadatokat egy Azure-adatforrásból, szerializálja a dokumentumokat a JSON-be, és átadja az eredményül kapott dokumentumokat a keresőmotornak az indexeléshez. Azóta kiterjesztették a [mesterséges intelligencia](cognitive-search-concept-intro.md) -bővítés támogatására a tartalom feldolgozásához. 

Az indexelő használata jelentősen csökkenti az íráshoz szükséges kód mennyiségét és összetettségét. Ebből a cikkből megtudhatja, hogyan hozhat létre egy indexelő a forrás-specifikus indexelő és [szakértelmével](cognitive-search-working-with-skillsets.md)fejlettebb munkájának előkészítéséhez.

## <a name="whats-an-indexer-definition"></a>Mi az indexelő definíciója?

Az indexelő olyan szöveges indexeléshez használatos, amely alfanumerikus tartalmat olvas be a forrás mezőiből index mezőkbe, vagy olyan AI-alapú feldolgozásra, amely elemzi a nem differenciált szöveget a struktúra számára, vagy elemzi a képeket a szövegre és az információkra, továbbá hozzáadja a tartalmat egy indexhez. A következő index-definíciók jellemzőek arra, amit bármelyik forgatókönyvhöz létre lehet hozni.

### <a name="indexers-for-text-content"></a>Szöveges tartalom indexelése

Az indexelő eredeti célja, hogy leegyszerűsítse az index betöltésének összetett folyamatát azáltal, hogy olyan mechanizmust biztosít, amely lehetővé teszi a szöveg és a numerikus tartalom összekapcsolását egy adatforrásból, a tartalmat JSON-dokumentumként szerializálja, és ezeket a dokumentumokat kikapcsolja a keresőmotorban az indexeléshez. Ez még mindig elsődleges használati eset, és ehhez a művelethez létre kell hoznia egy indexelő az alábbi példában megadott tulajdonságokkal.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

A **`name`** , a **`dataSourceName`** és a **`targetIndexName`**  Tulajdonságok szükségesek, és attól függően, hogy miként hozza létre az indexelő, az indexelő futtatása előtt a szolgáltatásban már léteznie kell az adatforrás és az index is. 

A **`parameters`** tulajdonság módosítja a futási idő viselkedését, például azt, hogy hány hibát kell elfogadnia, mielőtt a teljes feladatot elmulasztja. A paraméterek emellett a forrás-specifikus viselkedést is megadhatják. Ha például a forrás blob Storage, beállíthat egy olyan paramétert, amely a következő fájlkiterjesztések alapján szűr: `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }` .

A **`field mappings`** tulajdonság a forrás – cél mezők explicit módon történő leképezésére szolgál, ha ezek a mezők név vagy típus szerint eltérőek. Más tulajdonságok (nem láthatók), az [Ütemezés megadására](search-howto-schedule-indexers.md), a indexelő letiltott állapotú létrehozására, illetve a REST-adatok kiegészítő titkosításának [titkosítási kulcsának](search-security-manage-encryption-keys.md) megadására szolgálnak.

### <a name="indexers-for-ai-indexing"></a>Az AI-indexelés indexelő

Mivel az indexelő az a mechanizmus, amellyel a Search szolgáltatás kimenő kérelmeket küld, az indexelő kibővült a mesterséges intelligenciák támogatására, az infrastruktúra és objektumok hozzáadására a használati eset megvalósításához.

A fenti tulajdonságok és paraméterek az AI-bővítést végrehajtó indexelő adatokra vonatkoznak. A következő tulajdonságok jellemzőek az AI-dúsításra: **`skillSets`** , **`outputFieldMappings`** , **`cache`** (csak előnézet és REST). 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

Az AI-bővítés a jelen cikk hatókörén kívül esik. További információt a következő cikkekben talál: [AI-gazdagodás](cognitive-search-concept-intro.md), [szakértelmével az Azure Cognitive Searchban](cognitive-search-working-with-skillsets.md), és [készségkészlet létrehozása (REST)](/rest/api/searchservice/create-skillset).

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>Válasszon egy indexelő ügyfelet, és hozza létre az Indexelő

Ha készen áll arra, hogy egy távoli keresési szolgáltatáson hozzon létre egy indexelő szolgáltatást, szüksége lesz egy olyan eszközre, mint Azure Portal vagy Poster, vagy egy indexelő ügyfelet létrehozó kód. Javasoljuk, hogy az Azure Portal vagy REST API-kat a korai fejlesztéshez és a megvalósíthatósági teszteléshez.

### <a name="permissions"></a>Engedélyek

Az indexelő eszközökhöz kapcsolódó összes művelet, beleértve az állapotra vagy a definícióra vonatkozó GET-kéréseket, [rendszergazdai API-kulcsot](search-security-api-keys.md) kell megkövetelni a kérelemben.

### <a name="limits"></a>Korlátok

Az összes [szolgáltatási szintet](search-limits-quotas-capacity.md#indexer-limits) a létrehozható objektumok száma korlátozza. Ha az ingyenes szinten kísérletezik, akkor csak az egyes típusok 3 objektuma lehet, és az indexelő feldolgozásának 2 perce (nem tartalmazza a készségkészlet-feldolgozást).

### <a name="use-azure-portal-to-create-an-indexer"></a>Indexelő létrehozása a Azure Portal használatával

A portál két lehetőséget kínál az indexelő létrehozásához: az [**adat importálása varázsló**](search-import-data-portal.md) és az **új indexelő** , amely mezőket biztosít az indexelő definíciójának meghatározásához. A varázsló egyedi, hogy létrehozza az összes szükséges elemet. Más megközelítésekhez az szükséges, hogy az adatforrás és az index előre definiálva legyen.

A következő képernyőképen látható, hogy hol találhatók ezek a funkciók a portálon. 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="Hotels indexelő" border="true":::

### <a name="use-a-rest-client"></a>REST-ügyfél használata

A Poster és a Visual Studio Code (az Azure Cognitive Search bővítménnyel együtt) indexelő ügyfélként is működhet. Bármelyik eszköz használatával kapcsolódhat a keresési szolgáltatáshoz, és elküldheti az indexelő és egyéb objektumokat létrehozó kérelmeket. Számos oktatóanyag és példa mutatja be a REST-ügyfeleket objektumok létrehozásához. 

Kezdje a következő cikkek bármelyikével, és ismerkedjen meg az egyes ügyfelekkel:

+ [Keresési index létrehozása a REST és a Poster használatával](search-get-started-rest.md)
+ [Ismerkedés a Visual Studio Code és az Azure Cognitive Search](search-get-started-vs-code.md)

Az indexelő kérelmek megfogalmazásával kapcsolatos segítségért tekintse meg az [Indexelő műveleteit (REST)](/rest/api/searchservice/Indexer-operations) .

### <a name="use-an-sdk"></a>SDK használata

Cognitive Search az Azure SDK-k általánosan elérhető funkciókat implementálnak. Ilyen esetben az SDK-k bármelyikét használhatja az indexelő szolgáltatással kapcsolatos objektumok létrehozásához. Mindegyik olyan **SearchIndexerClient** valósít meg, amely az indexelő és a kapcsolódó objektumok, például a szakértelmével létrehozására szolgáló metódusokat biztosít.

| Azure SDK | Ügyfél | Példák |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [Dotnethowtoindexers elemre](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample. Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [Indexelők](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>Az indexelő futtatása

Az indexelő automatikusan elindul, amikor létrehozza az Indexelő szolgáltatást a szolgáltatásban. Ez az igazság pillanata, ahol megtudhatja, hogy vannak-e adatforrás-kapcsolódási hibák, mező-hozzárendelési problémák vagy készségkészlet problémák. 

Az indexelő több módon is futtatható:

+ A definíció hozzáadásához vagy módosításához, illetve az indexelő futtatásához küldjön HTTP-kérést az [Indexelő létrehozásához](/rest/api/searchservice/create-indexer) vagy az [Indexelő frissítéséhez](/rest/api/searchservice/update-indexer) .

+ HTTP-kérelem küldése az [Indexelő futtatásához](/rest/api/searchservice/run-indexer) , hogy a definíció módosítása nélkül hajtson végre egy indexelő.

+ Futtasson egy programot, amely meghívja a SearchIndexerClient metódusokat a létrehozás, a frissítés vagy a Futtatás számára.

> [!NOTE]
> Ha el szeretné kerülni, hogy az indexelő azonnal fusson a létrehozáskor, vegye **`disabled=true`** fel az indexelési definícióba.

Azt is megteheti, hogy az indexelő [Ütemezés](search-howto-schedule-indexers.md) szerint rendszeres időközönként meghívja a feldolgozást. 

Az ütemezett feldolgozás általában egybeesik a módosított tartalom növekményes indexelésének szükségességével. Az észlelési logika a forrás-platformokba beépített képesség. Az indexelő automatikusan észleli a blob-tároló módosításait. A változások észlelésének más adatforrásokban való kihasználásával kapcsolatos útmutatásért tekintse meg az indexelő docs az egyes adatforrásokhoz:

+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Azure-Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="know-your-data"></a>Az adatai ismerete

Az indexelő egy táblázatos sort vár, amelyben minden egyes sor teljes vagy részleges keresési dokumentumnak lesz az indexben. Gyakran előfordul, hogy van egy-az-egyhez típusú levelezés egy sor és az eredményül kapott keresési dokumentum között, ahol a sorban lévő összes mező teljes mértékben feltölti az egyes dokumentumokat. Az indexelő segítségével azonban csak egy dokumentum egy részét hozhatja létre, például ha több indexelő vagy megközelítést használ az index kiépítéséhez. 

Ha a viszonyítási adatokat egy sorba állítja, létre kell hoznia egy SQL-nézetet, vagy olyan lekérdezést kell létrehoznia, amely ugyanabban a sorban a szülő-és alárendelt rekordokat adja vissza. Például a beépített szállodák minta adatkészlete egy SQL-adatbázis, amelynek 50 rekordja van (egy az egyes szállodákhoz), a kapcsolódó táblázatban lévő szobák rekordjaihoz csatolva. Az a lekérdezés, amely a kollektív adatokat egy sorba állítja, az összes szállodai rekordban beágyazza az összes helyiség-információt JSON-dokumentumba. A beágyazott helyiség adatait egy olyan lekérdezés hozza létre, amely a **for JSON Auto** záradékot használja. A technikával kapcsolatos további információkért tekintse meg [a beágyazott JSON-t visszaadó lekérdezést](index-sql-relational-data.md#define-a-query-that-returns-embedded-json). Ez csak egy példa; más módszereket is megtalálhat, amelyek ugyanazt a hatást fogják eredményezni.

Az összeolvasztott adatok mellett fontos, hogy csak kereshető adatok legyenek lekérdezve. A kereshető adatértékek alfanumerikusak. Cognitive Search nem tud bármilyen formátumú bináris adaton keresni, bár a képfájlok szöveges leírását kinyerheti és kikövetkeztetheti (lásd: [AI](cognitive-search-concept-intro.md)-bővítés) kereshető tartalom létrehozásához. Hasonlóképpen, az AI-bővítés használatával a nagyméretű szövegeket természetes nyelvi modellekkel elemezheti, hogy megkeresse a szerkezetet vagy a kapcsolódó információkat, és új tartalmat adjon hozzá a keresési dokumentumokhoz.

Mivel az indexelő nem javítja az adatproblémákat, szükség lehet az adattisztítás más formáira vagy a manipulációra. További információkért tekintse meg az [Azure Database termék](/azure/?product=databases)termékdokumentációját.

## <a name="know-your-index"></a>Az index megismerése

EMLÉKEZTETVE arra, hogy az indexelő a keresési dokumentumokat a keresőmotorban adják át indexelésre. Az indexelő olyan tulajdonságokkal rendelkeznek, amelyek a végrehajtási viselkedést határozzák meg, az index sémája olyan tulajdonságokkal rendelkezik, amelyek a karakterláncok indexelését befolyásolják (csak a karakterláncok elemzése és a jogkivonatos). Az elemzői hozzárendeléstől függően az indexelt karakterláncok eltérőek lehetnek az Ön által átadott adatoktól. Kiértékelheti az elemzők hatásait az elemzés [szövege (REST)](/rest/api/searchservice/test-analyzer)használatával. További információ az elemzők használatáról: [elemzők a szöveg feldolgozásához](search-analyzers.md).

Az indexelő az indexekkel való interakciója szempontjából az indexelő csak a mezők nevét és típusát ellenőrzi. Nincs olyan ellenőrzési lépés, amely biztosítja, hogy a bejövő tartalom helyes legyen az index megfelelő keresési mezőjénél. Ellenőrzési lépésként futtathat lekérdezéseket a teljes dokumentumokat vagy a kijelölt mezőket visszaadó, feltöltött indexhez. Az indexek tartalmának lekérdezésével kapcsolatos további információkért lásd: [alapszintű lekérdezés létrehozása](search-query-create.md).

## <a name="next-steps"></a>Következő lépések

+ [Indexelők ütemezése](search-howto-schedule-indexers.md)
+ [Mező-hozzárendelések definiálása](search-indexer-field-mappings.md)
+ [Indexelő állapotának figyelése](search-howto-monitor-indexers.md)
+ [Csatlakozás felügyelt identitásokkal](search-howto-managed-identities-data-sources.md)