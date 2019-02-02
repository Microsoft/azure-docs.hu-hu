---
title: Mi az Azure Search szolgáltatás – Azure Search
description: Az Azure Search egy teljes körűen felügyelt felhőalapú keresési szolgáltatás a Microsoft egy. Tekintse át a szolgáltatások leírása, a fejlesztési munkafolyamat, hogyan hasonlítja össze Azure Search keresési más Microsoft-terméket, és hogyan kezdheti el.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 02/01/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 36d65e1ffab79c8f0866d60f4a133798d25e9dea
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562793"
---
# <a name="what-is-azure-search"></a>Mi az az Azure Search?
Az Azure Search egy felhőalapú keresőszolgáltatás, amely olyan API-kat és eszközöket nyújt a fejlesztőknek, amelyek segítségével fejlett keresési funkciókat adhatnak hozzá a privát, heterogén tartalmak keresése érdekében a webes, mobilos és vállalati alkalmazásokhoz. A lekérdezések végrehajtása felhasználó által meghatározott indexen keresztül történik.

+ Többféle tartalomtípusba tartozó és többféle platformról származó adatokból létrehozhat egy olyan keresési gyűjteményt, amely csak a saját adatait tartalmazza. 
+ A mesterséges intelligencia által vezérelt indexelés segítségével képfájlokból szöveget és jellegzetességeket, nyers szövegből pedig entitásokat és kulcskifejezéseket nyerhet ki.
+ Intuitív kereséseket hozhat létre jellemzőalapú navigáció, valamint szűrők, szinonimák, automatikus kitöltés és szövegelemzés használatával az „úgy értette, hogy…?” típusú, automatikusan javított keresőkifejezésekhez.
+ Hozzáadhat földrajzi keresést a „keresés a közelben” használatához, nyelvi elemzőket a nem angol nyelvű szöveges keresésekhez, illetve pontozási logikát a keresések rangsorolásához.

A funkciókat egy egyszerű [REST API-n](/rest/api/searchservice/) vagy [.NET SDK-n](search-howto-dotnet-sdk.md) keresztül tudja elérni, mely elfedi az információk kiolvasásának mögöttes komplexitását. Az API-k mellett az Azure Portal is nyújt támogatást a felügyelethez és a tartalomkezeléshez, például eszközöket kínál prototípus-készítéshez és az indexek lekérdezéséhez. Mivel a szolgáltatás a felhőben fut, az infrastruktúrát és a rendelkezésre állást a Microsoft felügyeli.

<a name="feature-drilldown"></a>

## <a name="feature-summary"></a>Szolgáltatások ismertetése

| Kategória | Szolgáltatások |
|----------|----------|
|Teljes szöveges keresés és szövegelemzés | A [teljes szöveges keresés](search-lucene-query-architecture.md) a keresésalapú alkalmazások leggyakrabban igénybe vett funkciója. A lekérdezéseket a támogatott szintaxisok segítségével állíthatja össze. <br/><br/>Az [**egyszerű lekérdezési szintaxis**](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) logikai operátorokat, kifejezéskeresési operátorokat, utótag-operátorokat és prioritási operátorokat tartalmaz.<br/><br/>A [**Lucene lekérdezési szintaxis**](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) tartalmazza az egyszerű szintaxis összes operátorát, illetve támogatja emellett az intelligens keresést, a közelségi keresést, a kifejezés-kiemelést és a reguláris kifejezéseket.|
|Kognitív keresés (előzetes verzió) | A képek és szövegek elemzéséhez használt, [mesterséges intelligencia által vezérelt algoritmusok](cognitive-search-concept-intro.md) indexelési folyamatokra is alkalmazhatók a szöveges információ nyers tartalomból való kinyerése céljából. A [beépített képességek](cognitive-search-predefined-skills.md) között szerepel például az optikai karakterfelismerés (beolvasott JPEG-fájlok kereshetővé tétele), az entitásfelismerés (cég vagy intézmény, név vagy hely azonosítása), valamint a kulcskifejezések felismerése. [Egyéni képességeket is kódolhat](cognitive-search-create-custom-skill-example.md), amelyeket a folyamathoz lehet kapcsolni. |
| Adatintegráció | Az Azure Search indexei bármilyen forrásból fogadnak el adatokat, feltéve hogy azok JSON adatstruktúraként vannak formázva. <br/><br/> Opcionálisan az Azure-ban támogatott adatforrásokhoz használhat [**indexelőket**](search-indexer-overview.md), amelyek automatikusan bejárják az [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md) vagy [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) szolgáltatásokat, és kereshető tartalmakat keresnek az elsődleges adattárakban. Az Azure Blob-indexelők képesek *dokumentumfeltörést* végezni [szövegek kinyeréséhez a leggyakoribb fájlformátumokból](search-howto-indexing-azure-blob-storage.md), ideértve a Microsoft Office-, a PDF- és a HTML-dokumentumokat. |
| Nyelvi elemzés | Az elemzők olyan összetevők, amelyekkel a rendszer szövegfeldolgozást végez az indexelési és keresési műveletek során. Két típusukat különböztetjük meg. <br/><br/>Az [**egyéni lexikai elemzőket**](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) a rendszer a fonetikus egyeztetést és reguláris kifejezéseket használó összetett keresési lekérdezésekhez alkalmazza. <br/><br/>A Lucene-tól vagy a Microsofttól származó [**nyelvi elemzők**](https://docs.microsoft.com/rest/api/searchservice/language-support) a nyelvspecifikus funkcionalitást kezelik, ideértve például az igeidőt, a nemet, a szabálytalan többesszámú főneveket, a szóösszetételek lebontását és a szavakra bontást (a szóközt nem alkalmazó nyelveknél). |
| Földrajzi keresés | Az Azure Search feldolgozza, szűri és megjeleníti a földrajzi helyeket. Ez lehetővé teszi a felhasználóknak, hogy az adatokat a keresési eredménynek egy fizikai helyhez való közelsége alapján tárják fel. További információért [nézze meg ezt a videót](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) vagy [tekintse át ezt a mintát](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). |
| Felhasználói élményt javító szolgáltatások | Az [**Automatikus kitöltés (előzetes verzió)**](search-autocomplete-tutorial.md) engedélyezésével gépelés közbeni javaslatokat nyújthat a keresősávban. <br/><br/>A [**Keresési javaslatok**](https://docs.microsoft.com/rest/api/searchservice/suggesters) részben bevitt szövegeket is feldolgoz a keresősávban, de eredményként nem lekérdezési kifejezéseket, hanem tényleges dokumentumokat ad ki az indexben. <br/><br/>A [**Szinonimák**](search-synonyms.md) egyenértékű, a lekérdezés hatókörét implicit módon bővítő kifejezéseket társít a kereséshez, hogy ne a felhasználónak kelljen megadnia az alternatívákat. <br/><br/>A [**jellemzőalapú navigáció**](search-faceted-navigation.md) egy lekérdezési paraméterrel engedélyezhető. Az Azure Search ekkor visszaad egy jellemzőalapú navigációs struktúrát, melyet használhat kategórialista mögötti kódként önállóan irányított szűrés céljából (például katalóguselemek árkategória vagy márka szerinti szűréséhez). <br/><br/> A [**szűrők**](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) segítségével építhet be jellemzőalapú navigációt az alkalmazása felhasználói felületébe, továbbfejlesztheti a lekérdezésírást, valamint szűrhet felhasználó vagy fejlesztő által megadott feltételek alapján. Szűrőket OData-szintaxissal tud létrehozni.<br/><br/> A [**találatok kiemelése**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) szövegformázást alkalmaz a keresési találatok egyező kulcsszavaira. Megadhatja, hogy mely mezők adjanak vissza kiemelt részeket.<br/><br/>A [**rendezés**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) több mezőhöz is elérhető az indexsémán keresztül, majd a lekérdezéskor be- és kikapcsolható egyetlen keresési paraméter használatával.<br/><br/> A [**lapozás**](search-pagination-page-layout.md) és a keresési találatok leszabályozása egyaránt könnyen igénybe vehető az Azure Search által nyújtott részletes vezérlési funkcionalitás részeként.  
| Relevancia | Az [**egyszerű pontozás**](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) az Azure Search használatának egyik legfőbb előnye. A pontozási profilok segítségével a relevancia magukban a dokumentumokban lévő értékeknek a függvényeként modellezhető. Beállíthatja például, hogy az újabb termékek vagy az akciós termékek magasabb prioritással jelenjenek meg a keresési eredményben. Emellett hozhat létre pontozási profilokat címkékkel, ha a külön nyomon követett és tárolt felhasználói keresési beállítások alapján személyre szabott pontozást szeretne nyújtani. |
| Figyelés és jelentéskészítés | A rendszer [**keresésforgalmi adatok**](search-traffic-analytics.md) gyűjtésével és elemzésével betekintést nyújt a felhasználók által a keresőmezőbe beírt kifejezésekbe. <br/><br/>Emellett a rendszer alapértelmezés szerint gyűjti a lekérdezések másodpercenkénti számának, a késésnek és a leszabályozásnak a metrikáit, melyek megtekinthetők jelentésként a portállapokon. Könnyedén figyelheti még az indexek és a dokumentumok számát is, hogy szükség esetén méretezhesse a kapacitást. További információt a [szolgáltatás felügyeletét tárgyaló cikkben](search-manage.md) találhat. |
| Eszközök prototípus-készítéshez és vizsgálatokhoz | A portálon az [**Adatok importálása varázsló**](search-import-data-portal.md) segítségével konfigurálhatja az indexelőket, az indextervezővel új indexeket tervezhet, a [**Keresési ablakban**](search-explorer.md) pedig keresési kifejezéseket tesztelhet a pontozási profilok finomításához. Emellett bármelyik indexet megnyithatja a séma megtekintéséhez. |
| Infrastruktúra | A **nagy rendelkezésre állású platform** rendkívül megbízható keresési szolgáltatást szavatol. Megfelelően méretezve az [Azure Search szolgáltatás 99,9%-os rendelkezésre állást nyújt](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> **Teljes körűen felügyelt és méretezhető** megoldásként az Azure Search egyáltalán nem igényel infrastruktúra-felügyeletet. A szolgáltatást két dimenzió méretezésével szabhatja az igényeihez: konfigurálhat nagyobb méretű dokumentumtárat vagy magasabb lekérdezés-feldolgozási kapacitást, vagy mindkettőt.

## <a name="how-to-use-azure-search"></a>Az Azure Search használata
### <a name="step-1-provision-service"></a>1. lépés: A szolgáltatás üzembe helyezése
Az Azure Search szolgáltatásokat az [Azure Portalon](https://portal.azure.com/) vagy az [Azure Resource Management API](/rest/api/searchmanagement/) segítségével helyezheti üzembe. Választhatja az ingyenes, más előfizetőkkel megosztott szolgáltatást, vagy választhat [fizetett csomagot](https://azure.microsoft.com/pricing/details/search/), mely dedikált erőforrásokat nyújt csak az Ön szolgáltatása számára. A fizetett csomagok esetében a szolgáltatást két dimenzióban méretezheti: 

- Replikák hozzáadásával növelheti az adott idő alatt feldolgozható lekérdezések számát.   
- Partíciók hozzáadásával növelheti a dokumentumtároláshoz elérhető tárterület méretét. 

A dokumentumtárolás és a lekérdezésfeldolgozási teljesítmény külön-külön való szabályozásával a szolgáltatást az egyedi környezete igényeihez szabhatja.

### <a name="step-2-create-index"></a>2. lépés: Index létrehozása
A kereshető tartalmak feltöltése előtt definiálnia kell egy Azure Search-indexet. Az index egy adatbázistáblához hasonló struktúra, mely tárolja az adatait, és képes keresési lekérdezések fogadására. Definiálhatja az indexsémát olyan leképezéssel, amely a keresendő dokumentumok struktúráját tükrözi, az adatbázisokban lévő mezőkhöz hasonlóan.

Hozhat létre sémát az Azure Portalon, illetve programozottan is a [.NET SDK](search-howto-dotnet-sdk.md) vagy a [REST API](/rest/api/searchservice/) használatával.

### <a name="step-3-load-data"></a>3. lépés: Adatok betöltése
Az index definiálása után készen áll a tartalmak feltöltésére. Választhat a küldéses és a lekéréses modell között.

A lekéréses modell beszerzi az adatokat a külső adatforrásokból. Ezt olyan *indexelők* segítségével végzi, amelyek leegyszerűsítik és automatizálják az adatbetöltés bizonyos részleteit, például az adatokhoz való kapcsolódást, illetve az adatok olvasását vagy szerializálását. Érhetők el [indexelők](/rest/api/searchservice/Indexer-operations) az Azure Cosmos DB, az Azure SQL Database és az Azure Blob Storage szolgáltatáshoz, valamint az Azure virtuális gépeken futó SQL Server-kiszolgálókhoz. Az indexelőket konfigurálhatja igény szerinti vagy ütemezett adatfrissítésre.

A küldéses modell az SDK-n vagy a REST API-kon keresztül vehető igénybe úgy, hogy elküldi a frissített dokumentumokat egy indexnek. Gyakorlatilag bármilyen adatkészletből küldhet adatokat JSON formátumban. Az adatok betöltéséről a [Dokumentumok hozzáadása, frissítése vagy törlése](/rest/api/searchservice/addupdate-or-delete-documents) és [A .NET SDK használata](search-howto-dotnet-sdk.md) című cikkben találhat útmutatást.

### <a name="step-4-search"></a>4. lépés: Keresés
Az index feltöltése után [bocsáthat ki keresési lekérdezéseket](/rest/api/searchservice/Search-Documents) a szolgáltatásvégpontjának egyszerű HTTP-kérések REST API-n vagy .NET SDK-n keresztüli küldésével.

## <a name="how-it-compares"></a>Összehasonlítás más keresési megoldásokkal

Ügyfeleink gyakran kérdezik, hogy az Azure Search miben tér el a többi keresési megoldástól. Az alábbi táblázat összefoglalja a fő eltéréseket.

| Más szolgáltatás | Fő eltérések |
|-------------|-----------------|
|Bing | A [Bing Web Search API](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) a Bing.com szolgáltatás indexeiben végez keresést az Ön által megadott kifejezés alapján. Ezek az indexek a nyilvános webhelyeken elérhető HTML, XML és más formátumú tartalmakból állnak. Ugyanerre az alapra épül a [Bing Custom Search](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/), amely ugyanezt a webes tartalmakhoz használható bejárási technológiát nyújtja, de egyéni webhelyekre alkalmazva.<br/><br/>Az Azure Search Ön által definiált indexekben végez keresést, melyek az Ön tulajdonában lévő adatokat tartalmazzák, gyakran sokféle különböző forrásból. Az Azure Search rendelkezik bejárási képességgel bizonyos adatforrásokhoz az [indexelőkön](search-indexer-overview.md) keresztül, de küldhet a szolgáltatásba bármilyen, az indexsémájához igazodó JSON-dokumentumot, ezzel egyetlen összevont kereshető erőforrást létrehozva. |
|Adatbázis-keresés | Számos adatbázis-platform tartalmaz beépített keresőket. Az SQL Server egy [teljes szöveges keresési funkciót](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) tartalmaz. A Cosmos DB és a hasonló technológiák lekérdezhető indexekkel rendelkeznek. A keresési és tárolási funkciókat egyesítő termékek kiértékelésekor kihívást jelenthet a választás. Számos megoldás is használja: Az adatbázis-kezelő tárolási vagy az Azure Search szolgáltatást a speciális keresési funkciókat.<br/><br/>Az adatbázis-kezelő search, Azure Search heterogén forrásokból tárol adatokat, és specializált nyelvi együttműködő szövegfeldolgozást (származtató, morfológiai, word űrlap) a feldolgozás funkciókat kínál [56 nyelvhez](https://docs.microsoft.com/rest/api/searchservice/language-support). Emellett támogatja a hibásan írt szavak automatikus javítását, a [szinonimákat](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), a [javaslatokat](https://docs.microsoft.com/rest/api/searchservice/suggestions), a [pontozási vezérlőket](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), az [aspektusokat](https://docs.microsoft.com/azure/search/search-filters-facets) és az [egyéni lexikális elemzéseket](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). Az Azure Search [teljes szöveges keresőmotorja](search-lucene-query-architecture.md) az iparági szabványnak számító Apache Lucene rendszert használja az információk kiolvasásához. Noha az Azure Search megtartja az adatokat egy fordított index formájában, ez aligha helyettesíti a valódi adattárolást. További információért tekintse meg [ezt a fórumbejegyzést](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Az erőforrás-kihasználás is egy lényeges szempont ebben a kategóriában. Az indexelés és bizonyos lekérdezési műveletek általában nagy számítási terheléssel járnak. A keresések kihelyezése a DBMS-ből egy felhőbeli dedikált megoldásba segít megőrizni a rendszererőforrásokat tranzakciófeldolgozás céljából. Emellett a külső keresési megoldás használata megkönnyíti a lekérdezések mennyiségéhez igazított skálázást is.|
|Dedikált keresési megoldás | Ha úgy döntött, hogy egy teljes körű, dedikált keresési megoldást használ, utolsó lépésként kategorikusan össze kell hasonlítania a helyszíni megoldásokat és egy felhőalapú szolgáltatást. Számos keresési technológia ad lehetőséget az indexelés és a lekérdezési folyamat szabályozására, fejlettebb lekérdezési és szűrési szintaxis használatára, a rangsorolás és a relevancia vezérlésére, valamint önállóan irányított és intelligens keresés végrehajtására. <br/><br/>Egy felhőalapú szolgáltatás használata a megfelelő választás, ha azt szeretné, hogy minimális fenntartási és karbantartási és állítható méretezési csoport egy kulcsrakész megoldást. <br/><br/>A felhőmegoldásokon belül számos szolgáltató nyújt alapvető funkcionalitást teljes szöveges kereséssel, földrajzi kereséssel, valamint a keresési bemenetek bizonyos fokú félreérthetőségének kezelési képességével. Az ideális megoldást jellemzően az határozza meg, hogy milyen [speciális szolgáltatásokra](#feature-drilldown) van szüksége, illetve hogy mennyire egyszerű az API-k és az eszközök használata, valamint a felügyelet. |

A felhőszolgáltatók közül az Azure Search nyújtja a legerősebb teljesítményt a tartalomtárakban és Azure-adatbázisokban végzett teljes szöveges kereséshez, és így az olyan alkalmazásokhoz is, amelyek elsősorban keresésre támaszkodnak az adatbeolvasás és a tartalomnavigáció megvalósításához. 

A legfőbb előnyök közé tartoznak az alábbiak:

+ Azure-adatintegráció (webbejárók) az indexelő rétegben
+ Azure Portal központi felügyelethez
+ Az Azure ismert méretezhetősége, megbízhatósága és világszínvonalú rendelkezésre állása
+ Nyelvi és egyéni elemzési funkciók, teljes szöveges keresési elemzők 56 nyelvhez
+ [Keresésközpontú alkalmazásokban gyakran használt funkciók](#feature-drilldown): pontozás, jellemzőkezelés, javaslatok, szinonimák, földrajzi keresés és egyebek.

> [!Note]
> A rendszer teljes mértékben támogatja a nem Azure adatforrásokat is, de ezek nem indexelőkkel, hanem több kódot igénylő küldéses metódussal használhatók. A rendelkezésre álló API-k segítségével bármilyen JSON-dokumentumgyűjteményt elküldhet az Azure Search-indexek egyikének.

Az ügyfeleink közül számos az Azure Search-képességek legszélesebb körét kihasználja például online katalógusokkal, üzletági alkalmazásokkal és dokumentumfeltárási alkalmazásokkal.

## <a name="rest-api--net-sdk"></a>REST API | .NET SDK

Jóllehet sok feladat elvégezhető a portálon keresztül, az Azure Search szolgáltatást elsősorban olyan fejlesztőknek terveztük, akik a keresési funkcionalitást a meglévő alkalmazásaikba szeretnék integrálni. Ehhez az alábbi programozási felületek vehetők igénybe.

|Platform |Leírás |
|-----|------------|
|[REST](/rest/api/searchservice/) | HTTP-parancsok, melyek használhatók bármilyen programozási platformról vagy nyelven, ideértve például a Xamarin, a Java és a JavaScript platformot|
|[.NET SDK](search-howto-dotnet-sdk.md) | .NET-burkoló a REST API-hoz, mely hatékony fejlesztői munkát tesz lehetővé C# és más felügyelt kódú nyelveken, .NET-keretrendszerre épülő megoldások létrehozásához |

## <a name="free-trial"></a>Ingyenes próbaidőszak
Az Azure-előfizetők [üzembe helyezhetik a szolgáltatás ingyenes verzióját](search-create-service-portal.md).

Ha nincs még előfizetése, [ingyen létrehozhat egy Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). A fiók létrehozásakor krediteket kap a fizetős Azure-szolgáltatások kipróbálásához. A kreditek felhasználása után megtarthatja a fiókját, és tovább használhatja azt az [ingyenes Azure-szolgáltatásokkal](https://azure.microsoft.com/free/). A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez.

Lehetőségként [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Az MSDN-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat. 

## <a name="how-to-get-started"></a>Első lépések

1. Hozzon létre egy [ingyenes szolgáltatást](search-create-service-portal.md). Az ingyenes szolgáltatás az összes rövid útmutató és oktatóanyag elvégzését lehetővé teszi.

2. Végezze el a [beépített eszközök indexelésre és lekérdezésre történő használatát ismertető oktatóanyagot](search-get-started-portal.md). Ennek keretében fontos fogalmakat sajátíthat el, és megismerkedhet a portál által szolgáltatott információkkal.

3. A .NET vagy a REST API használatával folytathatja a kódolást:

  + [A .NET SDK használata](search-howto-dotnet-sdk.md) – Bemutatja a felügyelt kódú megoldások fejlesztésének fő munkafolyamatát.  
  + [A REST API használata](https://github.com/Azure-Samples/search-rest-api-getting-started) – Ismerteti ugyanezeket a lépéseket a REST API használatával. Ez a rövid útmutató segítségével REST API-k hívása a Postman vagy a Fiddler: [Ismerje meg az Azure Search REST API-k](search-fiddler.md).

## <a name="watch-this-video"></a>Videó megtekintése

A keresőmotor az a technológia, amelyet a mobilalkalmazások, a webhelyek és a vállalati adattárak általában használnak az információk lekéréséhez. Az Azure Search olyan eszközöket nyújt, amelyekkel a nagyméretű kereskedelmi webhelyek keresési szolgáltatásaihoz hasonló funkcionalitást tud létrehozni.

Ebben a 9 perces videóban megtudhatja Liam Cavanagh programmenedzsertől, hogy egy keresőmotor integrálása milyen előnyöket nyújt az alkalmazásának. Rövid bemutatókkal szemlélteti az Azure Search fő funkcionalitását és a jellemző munkafolyamatokat. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ A 0–3. percben megismerheti a fő funkciókat és azok használati eseteit.
+ A 3–4. percben megismerheti a szolgáltatás üzembe helyezését. 
+ A 4–6. percben megismerheti az indexek Adatok importálása varázslóval való létrehozását a beépített adatkészlet használatával.
+ A 6–9. percben megismerheti a Keresési ablakot és a lekérdezések végrehajtását.
