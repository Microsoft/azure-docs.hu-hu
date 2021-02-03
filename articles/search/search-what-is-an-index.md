---
title: Index létrehozása
titleSuffix: Azure Cognitive Search
description: Bevezeti az Azure Cognitive Search indexelési fogalmait és eszközeit, beleértve a séma-definíciókat és a fizikai adatstruktúrát is.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d9f4ba48a7dc6cdcf6d60e4e9da5f68fcc6b1f28
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509333"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>Keresési indexek létrehozása az Azure-ban Cognitive Search

A keresési index a teljes szöveges és szűrt lekérdezésekhez használt kereshető tartalmat tárolja. Az indexet egy séma határozza meg, és a szolgáltatásba menti, és az adatimportálás második lépésként történik. 

Az indexek *dokumentumokat* tartalmaznak. Elméletileg a dokumentumok az indexben kereshető adategységek. Előfordulhat, hogy egy kiskereskedő rendelkezik egy dokumentummal az egyes termékekhez, a hírekért felelős szervezet pedig minden cikkhez tartalmaz egy dokumentumot, és így tovább. Ezeket a fogalmakat több ismerős adatbázis-megfelelőnek is feltérképezheti: a *keresési index* egy *táblázatnak* felel meg, a *dokumentumok* pedig nagyjából egyenértékűek egy tábla *soraival* .

## <a name="whats-an-index-schema"></a>Mi az index sémája?

Az indexek fizikai szerkezetét a séma határozza meg. A "Fields" gyűjtemény általában az index legnagyobb része, ahol minden mező neve, egy [adattípushoz](/rest/api/searchservice/Supported-data-types)van rendelve, és az attribútum a felhasználási módját meghatározó engedélyezhető viselkedésekkel rendelkezik.

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

A további elemek összecsukása rövid ideig elvégezhető, de a következő hivatkozások részletesen ismertetik: a mutatók, a [pontozási profilok](index-add-scoring-profiles.md), a karakterláncok tokenekre való feldolgozásához használt [elemzők](search-analyzers.md) [, a](index-add-suggesters.md)nyelvi szabályok vagy az analizátor által támogatott egyéb jellemzők, valamint a [CORS](#corsoptions) -beállítások alapján.

## <a name="choose-a-client"></a>Válasszon ügyfelet

A keresési index létrehozásának számos módszere van. Javasoljuk, hogy az Azure Portal vagy REST API-kat a korai fejlesztéshez és a megvalósíthatósági teszteléshez.

A fejlesztés során tervezze meg a gyakori újraépítést. Mivel a fizikai struktúrák a szolgáltatásban jönnek létre, az [indexek eldobása és](search-howto-reindex.md) újbóli létrehozása szükséges ahhoz, hogy a legtöbb módosítást egy meglévő mező határozza meg. Érdemes lehet az adatai egy részhalmazával dolgozni az Újraépítés gyorsabb elvégzése érdekében.

### <a name="permissions"></a>Engedélyek

A keresési indexhez kapcsolódó összes művelet, beleértve a GET kérelmek definícióját, [rendszergazdai API-kulcsot](search-security-api-keys.md) igényel a kérelemben.

### <a name="limits"></a>Korlátok

Az összes [szolgáltatási szintet](search-limits-quotas-capacity.md#index-limits) a létrehozható objektumok száma korlátozza. Ha az ingyenes szinten kísérletezik, akkor egy adott időpontban csak 3 index tartozhat.

### <a name="use-azure-portal-to-create-a-search-index"></a>Keresési index létrehozása a Azure Portal használatával

A portálon két lehetőség áll rendelkezésre a keresési index létrehozásához: [**adatimportálás varázsló**](search-import-data-portal.md) és **index hozzáadása** , amely mezőket biztosít az index sémájának megadásához. A varázsló további műveletekben is létrehoz egy indexelő, egy adatforrás és az adatok betöltése során. Ha ez több, mint amit szeretne, használja az **index hozzáadása** vagy más módszer használatát.

Az alábbi képernyőfelvételen az **index hozzáadása** látható a portálon. Az **adatimportálás** közvetlenül a szomszédban történik.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Index hozzáadása parancs" border="true":::

> [!Tip]
> Az index kialakítása a portálon keresztül kikényszeríti a követelmények és a séma szabályait bizonyos adattípusokra vonatkozóan, például a teljes szöveges keresési képességek letiltását a numerikus mezőkön. Ha egy működőképes indextel rendelkezik, a JSON-t a portálról másolhatja, és hozzáadhatja a megoldáshoz.

### <a name="use-a-rest-client"></a>REST-ügyfél használata

A Poster és a Visual Studio Code (az Azure Cognitive Search bővítménnyel együtt) keresési index-ügyfélként is működhet. Bármelyik eszköz használatával csatlakozhat a keresési szolgáltatáshoz, és elküldheti a [create index (REST)](/rest/api/searchservice/create-index) kérelmeket. Számos oktatóanyag és példa mutatja be a REST-ügyfeleket objektumok létrehozásához. 

Kezdje a következő cikkek bármelyikével, és ismerkedjen meg az egyes ügyfelekkel:

+ [Keresési index létrehozása a REST és a Poster használatával](search-get-started-rest.md)
+ [Ismerkedés a Visual Studio Code és az Azure Cognitive Search](search-get-started-vs-code.md)

A tárgymutató-kérelmek megfogalmazásával kapcsolatos segítségért tekintse meg az [index műveleteit (REST)](/rest/api/searchservice/index-operations) .

### <a name="use-an-sdk"></a>SDK használata

Cognitive Search az Azure SDK-k általánosan elérhető funkciókat implementálnak. Így az SDK-k bármelyikét használhatja keresési index létrehozásához. Mindegyik olyan **SearchIndexClient** biztosít, amely az indexek létrehozásához és frissítéséhez használható metódusokat tartalmaz.

| Azure SDK | Ügyfél | Példák |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [Azure-Search-DotNet-Samples/Gyorsindítás/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample. Java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Indexek](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/indexes) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations.](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="defining-fields"></a>Mezők definiálása

Egy EDM. String típusú mezőt kell kijelölni a dokumentum kulcsaként. Az egyes keresési dokumentumok egyedi azonosítására szolgál. Egy dokumentum a kulcsa alapján kérhető le a Részletek lap feltöltéséhez.  

Ha a bejövő adattípusok hierarchikus jellegűek, a beágyazott struktúrákat a [komplex típus](search-howto-complex-data-types.md) adattípusa szerint kell kijelölni. A beépített mintavételi adatkészlet, a hotelek és az összetett típusok illusztrálása egy olyan címen (több almezőt tartalmaz), amely egy-az-egyhez kapcsolattal rendelkezik az egyes szolgáltatásokkal, valamint a szobák összetett gyűjteménye, ahol több szoba van társítva az egyes szolgáltatásokhoz. 

Az index létrehozása előtt rendeljen hozzá egy elemzőt a karakterlánc mezőihez. Tegye ugyanezt a javaslatokat, ha engedélyezni szeretné az automatikus kiegészítést adott mezőkön.

<a name="index-attributes"></a>

### <a name="attributes"></a>Attribútumok

A mezőtulajdonságok határozzák meg egy mező használati módját, például hogy használva lesz-e teljes szöveges keresésben, kategorizált navigációban, rendezési műveletekben stb. 

A karakterlánc típusú mezők gyakran "kereshető" és "lekérhető" jelöléssel rendelkeznek. A keresési eredmények szűkítéséhez használt mezők közé tartoznak a "rendezhető", a "szűrhető" és a "sokrétű".

|Attribútum|Leírás|  
|---------------|-----------------|  
|kereshető |Kereshető – Teljes szöveges keresésre alkalmas, az indexelés során lexikai elemzés, például szavakra bontás végezhető rajta. Ha egy kereshető mező értékének például a „sunny day” szöveget adja meg, akkor az két különálló tokenre lesz bontva: „sunny” és „day”. További részletek az [A teljes szöveges keresés működése](search-lucene-query-architecture.md) című cikkben.|  
|szűrhető |Szűrhető – A $filter lekérdezések hivatkoznak rá. Az `Edm.String` vagy `Collection(Edm.String)` típusú szűrhető mezők nem lesznek szavakra bontva, ezért összehasonlítások csak pontos egyezésekre végezhetők. Ha egy ilyen mező értékének például a „sunny day” szöveget adja meg, akkor a `$filter=f eq 'sunny'` keresés nem talál egyezést, a `$filter=f eq 'sunny day'` viszont igen. |  
|rendezhető |Rendezhető – A rendszer alapértelmezés szerint érték szerint rendezi a találatokat, de a dokumentumok mezői alapján végzett rendezés is konfigurálható. A típusú mezők `Collection(Edm.String)` nem lehetnek "rendezve". |  
|kategorizálható |Kategorizálható – Általában akkor használják, ha a keresési eredményeket a kategóriánkénti találatok számával együtt kell bemutatni (például szállodák egy adott városban). Ez a lehetőség az `Edm.GeographyPoint` típusú mezők esetén nem használható. A `Edm.String` szűrhető, "rendezhető" vagy "sokrétű" típusú mezők legfeljebb 32 kilobájt hosszúságú lehetnek. Részletek az [Index létrehozása (REST API)](/rest/api/searchservice/create-index) című cikkben.|  
|kulcs |Kulcs – Az indexen belüli dokumentumok egyedi azonosítója. Kulcsmezőként egyetlen `Edm.String` típusú mezőt kell megadni.|  
|visszakereshetőnek |Lekérdezhető – Megadja, hogy a mező visszaadható-e egy keresési eredményben. Ez akkor hasznos, ha egy mező (például *nyereség*) szűrésre, rendezésre vagy értékelésre szolgál, de a végfelhasználó számára nem kell megjelennie. Ennek a tulajdonságnak az értéke `key` tulajdonságú mezők esetén csak `true` lehet.|  

Új mezők bármikor megadhatók, a meglévő meződefiníciók azonban az index élettartamára rögzülnek. A fejlesztők ezért általában egyszerű indexek létrehozására vagy ötletek kipróbálására használják a portált, vagy a portál oldalain néznek utána egy beállításnak. Egy index rendszeres kiegészítése hatékonyabb az index újraépítését megkönnyítő kódalapú megközelítéssel.

> [!NOTE]
> Az indexek létrehozásához használt API-k eltérő alapértelmezett viselkedéssel rendelkeznek. A [REST API](/rest/api/searchservice/Create-Index)-k esetében a legtöbb attribútum alapértelmezés szerint engedélyezve van (például a "kereshető" és a "lekérhető" igaz a karakterlánc mezőknél), és gyakran csak akkor kell beállítania őket, ha ki szeretné kapcsolni őket. A .NET SDK esetében az ellenkezője igaz. A nem kifejezetten beállított bármely tulajdonság esetében az alapértelmezett érték a megfelelő keresési viselkedés letiltása, kivéve, ha kifejezetten engedélyezi azt.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Attribútumok és index mérete (tárolási vonzatok)

Az index méretét a feltöltött dokumentumok mérete határozza meg, valamint az indexelési konfiguráció, például a javaslatok belefoglalása, valamint az attribútumok egyéni mezőkhöz való beállítása. 

Az alábbi képernyőfelvételen az attribútumok különböző kombinációinak eredményeként létrejövő indexek tárolási mintáit mutatjuk be. Az index a **Real Estate Sample indexen** alapul, amelyet egyszerűen létrehozhat az adatimportálás varázsló segítségével. Bár az index sémái nem jelennek meg, az attribútumok az index neve alapján következtethető ki. Például a *Realestate-kereshető* index a "kereshető" attribútummal van kiválasztva, és semmi mást sem, a *Realestate* lekérhető indexben a "lekérhető" attribútum van kiválasztva, semmi más, és így tovább.

![Index mérete az attribútumok kijelölése alapján](./media/search-what-is-an-index/realestate-index-size.png "Index mérete az attribútumok kijelölése alapján")

Bár ezek az index-változatok mesterségesek, az attribútumok a tárolók befolyásolásának széles körű összehasonlítására is hivatkozhatnak. Megnövelhető az index mérete? Nem. Növeli a mezők hozzáadását egy **javaslathoz** az index méretének növelése érdekében? Igen.

A szűrőket és a rendezést támogató indexek a csak teljes szöveges keresést támogató indexek arányosan nagyobbak. Ennek az az oka, hogy a szűrési és rendezési műveletek pontos egyezéseket keresnek, és a Verbatim szöveges karakterláncok jelenlétét igénylik. Ezzel szemben a teljes szöveges lekérdezéseket támogató kereshető mezők fordított indexeket használnak, amelyek olyan jogkivonatokkal vannak feltöltve, amelyek kevesebb helyet foglalnak el, mint a teljes dokumentumok. 

> [!Note]
> A tárolási architektúra az Azure-Cognitive Search megvalósítási részletének minősül, és értesítés nélkül megváltozhat. Nincs garancia arra, hogy a jelenlegi viselkedés továbbra is fennmarad a jövőben.

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>Körülbelül `corsOptions`

Az index sémái közé tartozik egy szakasz a beállításhoz `corsOptions` . Az ügyféloldali JavaScriptek alapértelmezés szerint nem hívhatnak meg API-kat, mivel a böngésző megakadályozza az összes kereszthivatkozási kérelmet. Ha engedélyezni szeretné az adatforrások közötti lekérdezéseket az index számára, engedélyezze a CORS (több eredetű erőforrás-megosztás) a **corsOptions** attribútum beállításával. Biztonsági okokból csak a lekérdezési API-k támogatják a CORS. 

A következő beállítások állíthatók be a CORS:

+ **allowedOrigins** (kötelező): Ez azoknak a forrásoknak a listája, amelyek hozzáférést kapnak az indexhez. Ez azt jelenti, hogy az ezektől az eredettől kiszolgált JavaScript-kódok lehetővé teszik az index lekérdezését (feltéve, hogy a megfelelő API-kulcsot biztosítja). Az egyes eredetek általában az űrlapból származnak, `protocol://<fully-qualified-domain-name>:<port>` bár `<port>` gyakran kimaradnak. További részletekért tekintse meg a több [eredetű erőforrás-megosztás (wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) című témakört.

  Ha engedélyezni szeretné az összes eredet elérését, a `*` **allowedOrigins** tömb egyetlen elemeként adja meg. *Ez az üzemi keresési szolgáltatások esetében nem ajánlott* , de gyakran hasznos a fejlesztéshez és a hibakereséshez.

+ **maxAgeInSeconds** (nem kötelező): a böngészők ezt az értéket használják annak meghatározására, hogy mennyi ideig (másodpercben) kell gyorsítótárazni a CORS elővizsgálati válaszokat. Ennek nem negatív egész számnak kell lennie. Minél nagyobb ez az érték, annál jobb lesz a teljesítmény, de minél hosszabb ideig tart a CORS-szabályzat módosításainak érvénybe léptetése. Ha nincs beállítva, a rendszer az alapértelmezett 5 perces időtartamot használja.

## <a name="next-steps"></a>Következő lépések

A Cognitive Search szinte bármilyen minta vagy bemutató használatával tapasztalatokat szerezhet az indexek létrehozásáról. A tartalomjegyzékből bármelyik rövid útmutatót kiválaszthatja a kezdéshez.

Érdemes megismerni az indexek és az adathalmazok betöltésére szolgáló módszereket is. Az index definíciója és a populáció együtt történik. A következő cikkek további információkat tartalmaznak.

+ [Adatimportálás áttekintése](search-what-is-data-import.md)

+ [Dokumentumok hozzáadása, frissítése vagy törlése (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 