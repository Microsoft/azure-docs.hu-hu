---
title: Lekérdezés létrehozása
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan hozhat létre lekérdezési kéréseket a Cognitive Searchban, mely eszközök és API-k használhatók a teszteléshez és a kódoláshoz, és hogy a lekérdezési döntések hogyan kezdődnek az indexek
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: b013c66feefade077c85194ba3b1ff04ff4c4aa5
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99536832"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Lekérdezések létrehozása az Azure-ban Cognitive Search

Ha első alkalommal hoz létre lekérdezést, ez a cikk a lekérdezések beállításának megközelítéseit és módszereit ismerteti. Emellett egy lekérdezési kérelmet is bevezet, és bemutatja, hogyan befolyásolhatják a mezők attribútumai és a nyelvi elemzők a lekérdezési eredményeket.

## <a name="whats-a-query-request"></a>Mi a lekérdezési kérelem?

A lekérdezés csak olvasható kérelem egyetlen keresési index docs-gyűjteményéből. Meghatározza a "queryType" és a lekérdezési kifejezést, azonban a "Search" paramétert. A lekérdezés kifejezés tartalmazhat keresési kifejezéseket, idézőjeleket tartalmazó kifejezést és operátorokat.

A lekérdezésben a "Count" érték is megadható az indexben található egyezések számának visszaadásához, a "kiválasztás" lehetőséggel pedig kiválaszthatja, hogy mely mezőket adja vissza a rendszer a keresési eredmények között, és a "OrderBy" kifejezéssel A következő példa egy lekérdezési kérelem általános gondolatát mutatja be az elérhető paraméterek egy részhalmazának bemutatásával. A lekérdezések összeállításával kapcsolatos további információkért lásd: [lekérdezési típusok és összeállítások](search-query-overview.md) és [keresési dokumentumok (REST)](/rest/api/searchservice/search-documents).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
    "search": "`New York` +restaurant",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "count": "true",
    "orderby": "Rating desc"
}
```

## <a name="choose-a-client"></a>Válasszon ügyfelet

Szüksége lesz egy olyan eszközre, mint a Azure Portal vagy a Poster, vagy olyan kód, amely API-kat használó lekérdezési ügyfelet hoz létre. Javasoljuk, hogy az Azure Portal vagy REST API-kat a korai fejlesztéshez és a megvalósíthatósági teszteléshez.

### <a name="permissions"></a>Engedélyek

Bármely művelet, beleértve a lekérdezési kérelmeket, egy [felügyeleti API-kulcs](search-security-api-keys.md)alatt fog működni, de a lekérdezési kérések igény szerint egy [lekérdezési API-kulcsot](search-security-api-keys.md#create-query-keys)is használhatnak. Erősen ajánlott a lekérdezési API-kulcsok használata. Szolgáltatásként akár 50-et is létrehozhat, és különböző kulcsokat rendelhet különböző alkalmazásokhoz.

Azure Portal az eszközökhöz, a varázslókhoz és az objektumokhoz való hozzáféréshez a közreműködő szerepkörben vagy a szolgáltatáson felül kell tartoznia. 

### <a name="use-azure-portal-to-query-an-index"></a>Index lekérdezése Azure Portal használatával

A [Search Explorer (portál)](search-explorer.md) a Azure Portal lekérdezési felülete, amely az alapul szolgáló keresési szolgáltatás indexekkel kapcsolatos lekérdezéseket futtat. Belsőleg a portálon [keresési dokumentumok](/rest/api/searchservice/search-documents) is érkeznek, de nem hívhatják meg az automatikus kiegészítést, a javaslatokat vagy a dokumentumok keresését. 

Bármelyik indexet és REST API verziót kiválaszthatja, beleértve az előnézet is. A lekérdezési karakterláncok egyszerű vagy teljes szintaxist használhatnak az összes lekérdezési paraméter (Filter, select, searchFields stb.) támogatásával. A portálon, amikor megnyit egy indexet, az egymás melletti lapfüleken található JSON-definícióval együtt használhatja a keresési Explorert a mezők attribútumaihoz való könnyű hozzáférés érdekében. Vizsgálja meg, hogy mely mezők kereshetők, rendezhető, szűrhető és sokrétűek a lekérdezések tesztelése során.

### <a name="use-a-rest-client"></a>REST-ügyfél használata

A Poster és a Visual Studio Code (az Azure Cognitive Search bővítménnyel) is működhet lekérdezési ügyfélként. Bármelyik eszköz használatával kapcsolódhat a keresési szolgáltatáshoz, és elküldheti a [keresési dokumentumok (REST)](/rest/api/searchservice/search-documents) kéréseit. Számos oktatóanyag és példa mutatja be a REST-ügyfeleket az indexelés lekérdezéséhez. 

Kezdje a fenti cikkek bármelyikével, hogy megismerje az egyes ügyfeleket (a lekérdezésekre vonatkozó utasításokat is beleértve):

+ [Keresési index létrehozása a REST és a Poster használatával](search-get-started-rest.md)
+ [Ismerkedés a Visual Studio Code és az Azure Cognitive Search](search-get-started-vs-code.md)

Minden kérelem önálló, ezért minden kérelemnél meg kell adnia a végpontot, az index nevét és az API-verziót. A kérelem fejlécében a többi tulajdonság, a Content-Type és az API-kulcs is át lesz adva. További információkért lásd: [dokumentumok keresése (REST)](/rest/api/searchservice/search-documents) a lekérdezési kérelmek megfogalmazásával kapcsolatos segítségért.

### <a name="use-an-sdk"></a>SDK használata

Cognitive Search az Azure SDK-k általánosan elérhető funkciókat implementálnak. Így az SDK-k bármelyikét felhasználhatja az indexek lekérdezéséhez. Mindegyik olyan **SearchClient** biztosít, amely egy indextel való interakcióra, a keresési dokumentumokból származó indexek betöltésére, a lekérdezési kérelmek összeállítására szolgáló metódusokkal rendelkezik.

| Azure SDK | Ügyfél | Példák |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample. Java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [readonlyQuery.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query. ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>Válasszon egy lekérdezési típust: Simple | teljes

Ha a lekérdezés teljes szöveges keresést végez, a rendszer egy lekérdezés-elemzővel dolgozza fel a keresési kifejezésként és kifejezésként átadott szöveget. Az Azure Cognitive Search két lekérdezés-elemzőt kínál. 

+ Az egyszerű elemző megérti az [egyszerű lekérdezési szintaxist](query-simple-syntax.md). Ez az elemző lett kiválasztva, mivel a gyorsasága és hatékonysága az ingyenes szöveges lekérdezésekben nem megfelelő. A szintaxis támogatja a gyakori keresési operátorokat (és, vagy nem) a kifejezésre és kifejezésre való keresésekhez, valamint az előtag ( `*` ) kereséshez (mint a "Sea *" a Seattle és a Seaside esetében). Általános javaslat, hogy először az egyszerű elemzőt próbálja ki, majd a teljes elemzőre váltson, ha az alkalmazásra vonatkozó követelmények nagyobb teljesítményű lekérdezéseket hívnak meg.

+ A kérelemhez való hozzáadáskor engedélyezett [teljes Lucene-lekérdezési szintaxis](query-Lucene-syntax.md#bkmk_syntax) `queryType=full` az [Apache Lucene-elemző](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)alapján történik.

A teljes szintaxis és az egyszerű szintaxis átfedésben van azzal a mértéktel, amely egyszerre támogatja ugyanazt az előtagot és logikai műveleteket, de a teljes szintaxis több operátort is biztosít. Teljes mértékben több operátor is létezik a logikai kifejezésekhez, és további operátorok találhatók a speciális lekérdezésekhez, például a intelligens kereséshez, a helyettesítő karakteres kereséshez, a közelségi kereséshez és a reguláris kifejezésekhez.

## <a name="choose-query-methods"></a>Lekérdezési módszerek kiválasztása

A keresés alapvetően egy felhasználó által vezérelt gyakorlat, amelyben a feltételek vagy kifejezések gyűjtése egy keresőmező alapján történik, vagy az adott oldalon lévő események elemre kattintva. A következő táblázat összefoglalja azokat a mechanizmusokat, amelyekkel összegyűjtheti a felhasználói adatokat, valamint a várt keresési élményt.

| Bevitel | Élmény |
|-------|---------|
| [Keresési módszer](/rest/api/searchservice/search-documents) | A felhasználók kifejezéseket és kifejezéseket is begépelnek egy keresési mezőbe, operátorral vagy anélkül, és a Keresés gombra kattintanak a kérelem elküldéséhez. A Keresés az ugyanazon kérelemben található szűrőkkel is használható, de nem automatikus kiegészítéssel vagy javaslatokkal. |
| [Automatikus kiegészítési módszer](/rest/api/searchservice/autocomplete) | A felhasználók néhány karaktert begépelnek, és a rendszer minden új karakter beírása után kezdeményezi a lekérdezéseket. A válasz egy befejezett sztring az indexből. Ha a megadott karakterlánc érvényes, a felhasználó a Keresés gombra kattintva küldi el a lekérdezést a szolgáltatásnak. |
| [Javaslatok metódusa](/rest/api/searchservice/suggestions) | Az automatikus kiegészítéshez hasonlóan a felhasználók csak néhány karaktert és növekményes lekérdezéseket hoznak létre. A válasz a megfelelő dokumentumok legördülő listája, amelyet jellemzően néhány egyedi vagy leíró mező képvisel. Ha bármelyik kijelölés érvényes, a felhasználó rákattint az egyikre, és a rendszer visszaadja a megfelelő dokumentumot. |
| [Jellemzőalapú navigáció](/rest/api/searchservice/search-documents#query-parameters) | Egy oldalon a keresés hatókörét szűkítő, kattintható navigációs hivatkozások vagy zsemlemorzsa látható. Egy csiszolt navigációs struktúra dinamikusan áll a kezdeti lekérdezés alapján. Például `search=*` egy olyan csiszolt navigációs fa feltöltéséhez, amely minden lehetséges kategóriából tevődik össze. Egy lekérdezési válaszban egy sokoldalú navigációs struktúra jön létre, de a következő lekérdezés kiírására szolgáló mechanizmus is. n REST API hivatkozás a `facets` keresési dokumentumok művelet lekérdezési paramétereként van dokumentálva, de a paraméter nélkül is használható `search` .|
| [Szűrési módszer](/rest/api/searchservice/search-documents#query-parameters) | A szűrők a dimenziók használatával szűkítik az eredményeket. Az oldal mögött egy szűrőt is alkalmazhat, például az oldal inicializálásához nyelvspecifikus mezőkkel. REST API-hivatkozásban a a `$filter` keresési dokumentumok művelet lekérdezési paramétereként van dokumentálva, de a paraméter nélkül is használható `search` .|

## <a name="know-your-field-attributes"></a>A mező attribútumainak megismerése

Ha korábban áttekintette a [lekérdezési típusokat és az összeállítást](search-query-overview.md), akkor előfordulhat, hogy a lekérdezési kérelem paraméterei attól függnek, hogy a mezők hogyan legyenek indexelve az indexben. A lekérdezésekben, szűrési vagy rendezési sorrendben való használatra például egy mezőnek *kereshetőnek*, *szűrhetőnek* *és rendezhető* kell lennie. Hasonlóképpen, a találatok között csak a *beolvasható* mezők láthatók. A `search` `filter` kérelemben szereplő, és paraméter megadásakor `orderby` ügyeljen arra, hogy a nem várt eredmények elkerülése érdekében ellenőrizze az attribútumokat.

Az alábbi, a [Hotel minta indexét](search-get-started-portal.md)tartalmazó képernyőképen csak az utolsó két mező ("" lastrenovationdate "és" minősítés " `"$orderby"` ) használható egyetlen záradékban.

![A szállodai minta index-definíciója](./media/search-query-overview/hotel-sample-index-definition.png "A szállodai minta index-definíciója")

A Mezőtulajdonságok leírását lásd: [create index (REST API)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>A jogkivonatok ismerete

Az indexelés során a keresőmotor egy elemzőt használ a karakterláncok szöveges elemzéséhez, és maximalizálja a lekérdezési időponthoz való megfelelés lehetőségét. A karakterláncok minimálisan kisebbek, de morfológiai elemzéshez is lehetnek, és leállíthatók a Word eltávolítása. A nagyobb sztringeket vagy összetett szavakat általában szóközzel, kötőjelekkel vagy kötőjelekkel, valamint különálló tokenként indexelve kell elosztani. 

Az a pont, amellyel elkerülheti, hogy mit gondol az index, és hogy mi valójában, eltérő lehet. Ha a lekérdezések nem adják vissza a várt eredményeket, megvizsgálhatja az analizátor által létrehozott jogkivonatokat az [elemzés szövege alapján (REST API)](/rest/api/searchservice/test-analyzer). További információ a jogkivonatok létrehozása és a lekérdezések hatásáról: [részleges kifejezéses keresés és minták speciális karakterekkel](search-query-partial-matching.md).

## <a name="next-steps"></a>Következő lépések

Most, hogy jobban megértette, hogyan működik a lekérdezési kérelmek, próbálja ki a következő rövid útmutatókat gyakorlati tapasztalatokra.

+ [Keresési ablak](search-explorer.md)
+ [A lekérdezés a REST-ben](search-get-started-rest.md)
+ [Lekérdezés a .NET-ben](search-get-started-dotnet.md)
+ [Lekérdezés a Pythonban](search-get-started-python.md)
+ [Lekérdezés a JavaScriptben](search-get-started-javascript.md)