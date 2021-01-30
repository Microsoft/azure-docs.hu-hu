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
ms.openlocfilehash: 0483030312493dde9a50ab9000fbe29f19bfaff4
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99064163"
---
# <a name="create-a-search-indexer"></a>Keresési indexelő létrehozása

A keresési indexelő egy automatizált munkafolyamatot biztosít a dokumentumok és tartalmak külső adatforrásból történő átviteléhez a keresési szolgáltatás keresési indexéhez. Az eredetileg tervezett módon Kinyeri a szöveget és a metaadatokat az Azure-adatforrásokból, szerializálja a dokumentumokat a JSON-be, és átadja az eredményül kapott dokumentumokat egy keresőmotornak az indexeléshez. Azóta kiterjesztették a [mesterséges intelligencia](cognitive-search-concept-intro.md) -bővítés támogatására a tartalom feldolgozásához. 

Az indexelő használata jelentősen csökkenti az íráshoz szükséges kód mennyiségét és összetettségét. Ez a cikk az indexelő szerkezetét és felépítését, a forrás-specifikus indexelő és a [szakértelmével](cognitive-search-working-with-skillsets.md)megvizsgálása előtt helyezi üzembe.

## <a name="whats-an-indexer-definition"></a>Mi az indexelő definíciója?

Az indexelő olyan szöveges indexeléshez használatos, amely a forrás mezőkből származó szöveget a tárgymutató mezőibe vagy az olyan mesterséges feldolgozásra használja, amely elemzi a nem differenciált szöveget a struktúra számára, vagy elemzi a képeket a szövegre és az információkra. A következő index-definíciók jellemzőek arra, amit bármelyik forgatókönyvhöz létre lehet hozni.

### <a name="indexers-for-text-content"></a>Szöveges tartalom indexelése

Az indexelő eredeti célja, hogy leegyszerűsítse az index betöltésének összetett folyamatát azáltal, hogy olyan mechanizmust biztosít, amely lehetővé teszi a szöveg és a numerikus tartalom összekapcsolását egy adatforrásból, a tartalmat JSON-dokumentumként szerializálja, és ezeket a dokumentumokat kikapcsolja a keresőmotorban az indexeléshez. Ez még mindig elsődleges használati eset, és ehhez a művelethez létre kell hoznia egy indexelő az ebben a szakaszban meghatározott tulajdonságokkal.

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
A **`name`** , a **`dataSourceName`** és a **`targetIndexName`**  Tulajdonságok szükségesek, és attól függően, hogy az indexelő Hogyan jön létre, az indexelő futtatása előtt az adatforrás és az index is léteznie kell. 

A **`parameters`** tulajdonság a futási idő viselkedését tájékoztatja, például azt, hogy hány hibát kell elfogadnia a teljes feladat végrehajtása előtt. A paraméterek emellett a forrás-specifikus viselkedést is megadhatják. Ha például a forrás blob Storage, beállíthat egy olyan paramétert, amely a következő fájlkiterjesztések alapján szűr: `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }` .

A **`field mappings`** tulajdonság a forrás – cél mezők explicit módon történő leképezésére szolgál, ha ezek a mezők név vagy típus szerint eltérőek. Más tulajdonságok (nem láthatók), az ütemezés megadására, a indexelő letiltott állapotú létrehozására, illetve a REST-adatok kiegészítő titkosításának titkosítási kulcsának megadására szolgálnak.

### <a name="indexers-for-ai-indexing"></a>Az AI-indexelés indexelő

Mivel az indexelő az a mechanizmus, amellyel a Search szolgáltatás kimenő kérelmeket küld, az indexelő kiterjeszthetők a mesterséges intelligencia-bővítés támogatására, a használati esethez szükséges lépések és objektumok hozzáadásával.

A fenti tulajdonságok és paraméterek az AI-bővítést végző indexelő adatokra vonatkoznak, és három olyan tulajdonság hozzáadásával, amelyek a mesterséges intelligenciával kapcsolatos alkoholtartalom-növeléshez tartoznak: **`skillSets`** , **`outputFieldMappings`** , **`cache`** (csak előnézet és REST). 

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

Az AI-bővítés a jelen cikk hatókörén kívül esik. További információ [: szakértelmével használata az Azure Cognitive Search-ban](cognitive-search-working-with-skillsets.md) vagy [KÉSZSÉGKÉSZLET létrehozása (REST)](/rest/api/searchservice/create-skillset).

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>Válasszon egy indexelő ügyfelet, és hozza létre az Indexelő

Ha készen áll arra, hogy egy távoli keresési szolgáltatáson hozzon létre egy indexelő szolgáltatást, szüksége lesz egy olyan eszközre, mint Azure Portal vagy Poster, vagy egy indexelő ügyfelet létrehozó kód. Javasoljuk, hogy az Azure Portal vagy REST API-kat a korai fejlesztéshez és a megvalósíthatósági teszteléshez.

### <a name="permissions"></a>Engedélyek

Az indexelő eszközökhöz kapcsolódó összes művelet, beleértve az állapotra vagy a definícióra vonatkozó GET-kéréseket, [rendszergazdai API-kulcsot](search-security-api-keys.md) kell megkövetelni a kérelemben.

### <a name="limits"></a>Korlátok

Az összes [szolgáltatási szintet](search-limits-quotas-capacity.md#indexer-limits) a létrehozható objektumok száma korlátozza. Ha az ingyenes szinten kísérletezik, akkor csak az egyes típusok 3 objektuma lehet, és az indexelő feldolgozásának 2 perce (nem tartalmazza a készségkészlet-feldolgozást).

### <a name="use-azure-portal-to-create-an-indexer"></a>Indexelő létrehozása a Azure Portal használatával

A portál két lehetőséget kínál az indexelő létrehozására: az [**adatimportálás**](search-import-data-portal.md) és az **új indexelő** , amely mezőket biztosít az indexelő definíciójának megadásához. A varázsló egyedi, hogy létrehozza az összes szükséges elemet. Más megközelítésekhez az szükséges, hogy az adatforrás és az index előre definiálva legyen.

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

Az indexelő automatikusan elindul, amikor létrehozza az Indexelő szolgáltatást a szolgáltatásban. Ez az igazság pillanata, ahol megtudhatja, hogy vannak-e adatforrás-kapcsolódási hibák, mező-hozzárendelési problémák vagy készségkészlet problémák. Az [Indexelő létrehozásához](/rest/api/searchservice/create-indexer) vagy az [Indexelő frissítéséhez](/rest/api/searchservice/update-indexer) egy interaktív HTTP-kérelem fog futni. Egy olyan program futtatása, amely meghívja a SearchIndexerClient metódusokat, egy indexelő is futni fog.

Ha el szeretné kerülni, hogy az indexelő azonnal fusson a létrehozáskor, vegye **`disabled=true`** fel az indexelési definícióba.

Az indexelő létezése után igény szerint futtathatja a [Run indexelő (REST)](/rest/api/searchservice/run-indexer) vagy egy egyenértékű SDK-metódus használatával. Vagy állítsa be az indexelő [ütemezését](search-howto-schedule-indexers.md) , hogy rendszeres időközönként meghívja a feldolgozást. 

Az ütemezett feldolgozás általában egybeesik a módosított tartalom növekményes indexelésének szükségességével. Az észlelési logika a forrás-platformokba beépített képesség. Az indexelő automatikusan észleli a blob-tároló módosításait. A változások észlelésének más adatforrásokban való kihasználásával kapcsolatos útmutatásért tekintse meg az indexelő docs az egyes adatforrásokhoz:

+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Azure-Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="know-your-data"></a>Az adatai ismerete

Az indexelő egy táblázatos sort vár, amelyben minden egyes sor teljes vagy részleges keresési dokumentumnak lesz az indexben. Gyakran előfordul, hogy egy sor és az eredményül kapott keresési dokumentum között egy teljes körű levelezés látható, amelyben az összes mező fel van sorakozva. Az indexelő segítségével azonban csak egy dokumentum egy részét hozhatja létre, például ha több indexelő vagy megközelítést használ az index kiépítéséhez. 

Ha a viszonyítási adatokat egy sorba állítja, előfordulhat, hogy létre kell hoznia egy SQL-nézetet, vagy olyan lekérdezést kell létrehoznia, amely ugyanabban a sorban a szülő-és alárendelt rekordokat adja vissza. Például a beépített szállodák minta adatkészlete egy SQL-adatbázis, amelynek 50 rekordja van (egy az egyes szállodákhoz), a kapcsolódó táblázatban lévő szobák rekordjaihoz csatolva. Az a lekérdezés, amely a kollektív adatokat egy sorba állítja, az összes szállodai rekordban beágyazza az összes helyiség-információt JSON-dokumentumba. A beágyazott helyiség adatait egy olyan lekérdezés hozza létre, amely a **for JSON Auto** záradékot használja. A technikával kapcsolatos további információkért tekintse meg [a beágyazott JSON-t visszaadó lekérdezést](index-sql-relational-data.md#define-a-query-that-returns-embedded-json). Ez csak egy példa; más módszereket is megtalálhat, amelyek ugyanazt a hatást fogják eredményezni.

Az összeolvasztott adatok mellett fontos, hogy csak kereshető adatok legyenek lekérdezve. A kereshető adatértékek alfanumerikusak. Cognitive Search nem tud bármilyen formátumú bináris adaton keresni, bár a képfájlok szöveges leírását kinyerheti és kikövetkeztetheti (lásd: [AI](cognitive-search-concept-intro.md)-bővítés) kereshető tartalom létrehozásához. Hasonlóképpen, az AI-bővítés használatával a nagyméretű szövegeket természetes nyelvi modellekkel elemezheti, hogy megkeresse a szerkezetet vagy a kapcsolódó információkat, és új tartalmat adjon hozzá a keresési dokumentumokhoz.

Mivel az indexelő nem javítja az adatproblémákat, szükség lehet az adattisztítás más formáira vagy a manipulációra. További információkért tekintse meg az [Azure Database termék](/azure/?product=databases)termékdokumentációját.

## <a name="know-your-index"></a>Az index megismerése

EMLÉKEZTETVE arra, hogy az indexelő a keresési dokumentumokat a keresőmotorban adják át indexelésre. Az indexelő olyan tulajdonságokkal rendelkeznek, amelyek a végrehajtási viselkedést határozzák meg, az index sémája olyan tulajdonságokkal rendelkezik, amelyek a karakterláncok indexelését befolyásolják (csak a karakterláncok elemzése és a jogkivonatos). Az elemzői hozzárendeléstől függően az indexelt karakterláncok eltérőek lehetnek az Ön által átadott adatoktól. Kiértékelheti az elemzők hatásait az elemzés [szövege (REST)](/rest/api/searchservice/test-analyzer)használatával. További információ az elemzők használatáról: [elemzők a szöveg feldolgozásához](search-analyzers.md).

Az indexelő csak a mezők nevét és típusát jelöli. Nincs olyan ellenőrzési lépés, amely biztosítja, hogy a bejövő tartalom helyes legyen az index megfelelő keresési mezőjénél. Ellenőrzési lépésként futtathat lekérdezéseket a teljes dokumentumokat vagy a kijelölt mezőket visszaadó, feltöltött indexhez. Az indexek tartalmának lekérdezésével kapcsolatos további információkért lásd: [alapszintű lekérdezés létrehozása](search-query-create.md).

## <a name="next-steps"></a>Következő lépések

+ [Indexelők ütemezése](search-howto-schedule-indexers.md)
+ [Mező-hozzárendelések definiálása](search-indexer-field-mappings.md)
+ [Indexelő állapotának figyelése](search-howto-monitor-indexers.md)
+ [Csatlakozás felügyelt identitásokkal](search-howto-managed-identities-data-sources.md)