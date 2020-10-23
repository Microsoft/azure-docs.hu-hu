---
title: Bevezetés az Azure Cognitive Search használatába
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search egy teljes körűen felügyelt felhőalapú keresési szolgáltatás a Microsofttól. Ismerje meg a használati eseteket, a fejlesztési munkafolyamatot, a Microsoft keresési termékeinek összehasonlítását és az első lépéseket.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 10/22/2020
ms.custom: contperfq1
ms.openlocfilehash: d045014cd92df084e4dd409012fdb668d312de1a
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442320"
---
# <a name="what-is-azure-cognitive-search"></a>Mi az az Azure Cognitive Search?

Az Azure Cognitive Search ([korábbi nevén "Azure Search"](whats-new.md#new-service-name)) egy felhőalapú keresési szolgáltatás, amely lehetővé teszi, hogy a fejlesztők API-kat és eszközöket kínáljanak a webes, mobil-és Nagyvállalati alkalmazások privát, heterogén tartalmainak gazdag keresési élményének kialakításához.

Cognitive Search szolgáltatás létrehozásakor olyan keresőmotort kap, amely indexelési és lekérdezés-végrehajtást végez, valamint a létrehozott és kezelt indexek állandó tárolóját, valamint egy lekérdezési nyelvet, amellyel egyszerű és összetett lekérdezések készíthetők. A Search szolgáltatás más Azure-szolgáltatásokkal is integrálható *Indexelő* formájában, amely automatizálja az adatok betöltését/lekérését az Azure-adatforrásokból, valamint olyan *szakértelmével* , amelyek a Cognitive Services, például a képek és a szöveges elemzések, vagy a Azure Machine learning vagy a Azure FUNCTIONSben létrehozott egyéni AI

![Azure Cognitive Search-architektúra](media/search-what-is-azure-search/azure-search-diagram.svg "Azure Cognitive Search-architektúra")

A keresési szolgáltatás építészeti szempontból a nem indexelt adatokat tartalmazó külső adattárak között, valamint egy olyan ügyfélalkalmazás között ül, amely lekérdezési kérelmeket küld egy keresési indexnek, és kezeli a választ.  Az index séma meghatározza a kereshető tartalom szerkezetét. 

A keresési szolgáltatás két elsődleges munkaterhelése az *indexelés* és a *lekérdezés*.

+ Az indexelés szöveget helyez el a keresési szolgáltatásba, és kereshetővé teszi. Belsőleg a bejövő szövegeket a rendszer tokenekre dolgozza fel, és a gyors vizsgálatok érdekében fordított indexekben tárolja őket. Az indexelés során lehetősége van arra, hogy a Microsofttól vagy az Ön által létrehozott egyéni szakemberektől előre definiált *kognitív képességeket*is felvehet. A későbbi elemzések és átalakítások olyan új információkat és struktúrákat eredményeznek, amelyek korábban nem léteztek, így nagy mennyiségű keresési és tudás-adatbányászati forgatókönyvet biztosítanak.

+ Ha egy indexet kereshető adatokkal tölt fel, az ügyfélalkalmazás lekérdezési kérelmeket küld egy keresési szolgáltatásnak, és kezeli a válaszokat. Az összes lekérdezés végrehajtása a szolgáltatásban létrehozott, saját és tárolt keresési index fölé esik. Az ügyfélalkalmazás esetében a keresési élmény az Azure Cognitive Search API-jai használatával van definiálva, és a következők lehetnek: relevancia finomhangolása, automatikus kiegészítés, szinonimák egyeztetése, zavaros megfeleltetés, minta egyeztetés, szűrés és rendezés.

A funkciókat egy egyszerű [REST API-n](/rest/api/searchservice/) vagy [.NET SDK-n](search-howto-dotnet-sdk.md) keresztül tudja elérni, mely elfedi az információk kiolvasásának mögöttes komplexitását. A szolgáltatás felügyeletéhez és a tartalomkezeléshez használható Azure Portal is használhatja, az indexek és a szakértelmével prototípusának és lekérdezésének eszközeivel. Mivel a szolgáltatás a felhőben fut, az infrastruktúrát és a rendelkezésre állást a Microsoft felügyeli.

## <a name="when-to-use-cognitive-search"></a>Mikor kell használni a Cognitive Search

Az Azure Cognitive Search kiválóan alkalmas a következő alkalmazási forgatókönyvekhez:

+ Heterogén tartalomtípusok összevonása egy privát, felhasználó által definiált keresési indexbe. A keresési indexet bármilyen forrásból származó JSON-dokumentumokból töltheti fel. Az Azure által támogatott források esetében indexelő *segítségével* automatizálhatja az indexelést. Az index sémájának szabályozása és az ütemterv frissítése a Cognitive Search használatának egyik fő oka.

+ A kereséssel kapcsolatos szolgáltatások egyszerű implementálása. A keresési API-k leegyszerűsítik a lekérdezések építését, a sokoldalú navigációt, a szűrőket (beleértve a Geo-térbeli keresést), a szinonimák leképezését, az automatikus kiegészítést A beépített funkciók használatával a kereskedelmi webkeresőmotorokhoz hasonló keresési élményekhez is kielégítheti a végfelhasználói elvárásokat.

+ A nyers tartalom nagy, nem megkülönböztethető szöveg-vagy képfájl, illetve az Azure Blob Storage-ban vagy Cosmos DBban tárolt alkalmazásfájlok. Az indexelés során a [kognitív képességek](cognitive-search-concept-intro.md) alkalmazásával azonosíthatja és kinyerheti a szöveget, létrehozhat struktúrát, vagy létrehozhat új adatokat, például lefordított szöveget vagy entitásokat.

+ A tartalomnak nyelvi vagy egyéni szöveges elemzésre van szüksége. Ha nem angol nyelvű tartalommal rendelkezik, az Azure Cognitive Search a Lucene-elemzőket és a Microsoft természetes nyelvi processzorait is támogatja. Az elemzőket úgy is konfigurálhatja, hogy a nyers tartalom speciális feldolgozását, például a Mellékjelek kiszűrését, illetve a mintázatok felismerését és a karakterláncokban való megőrzését.

További információ az egyes funkciókról: [Az Azure Cognitive Search szolgáltatásai](search-features-list.md)

## <a name="how-to-use-cognitive-search"></a>A Cognitive Search használata

### <a name="step-1-provision-service"></a>1. lépés: A szolgáltatás üzembe helyezése

[Létrehozhat egy ingyenes szolgáltatást](search-create-service-portal.md) más előfizetőkkel, vagy egy [fizetős](https://azure.microsoft.com/pricing/details/search/) csomagot, amely kizárólag a szolgáltatás által használt erőforrásokat rendeli. Az ingyenes szolgáltatás az összes rövid útmutató és oktatóanyag elvégzését lehetővé teszi.

A fizetős szinteken a szolgáltatás két dimenzióban is méretezhető, így a termelési követelmények alapján kalibrálhatja a szolgáltatást:

+ Replikák hozzáadásával növelheti kapacitását a nagy mennyiségű lekérdezési terhelés kezelésére
+ Partíciók hozzáadása a növekvő tárterülethez további dokumentumok

### <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása

Definiáljon egy indexelési sémát, amely a keresendő dokumentumok szerkezetét tükrözi, hasonlóan az adatbázis mezőihez. A keresési index egy speciális adatstruktúra, amely gyors lekérdezés-végrehajtásra van optimalizálva.

Gyakori, hogy [létrehozza az index sémát a Azure Portalban](search-what-is-an-index.md), vagy programozott módon használja a [.net SDK](search-howto-dotnet-sdk.md) -t vagy [Rest APIt](/rest/api/searchservice/).

> [!TIP]
> A gyors üzembe helyezési útmutató [: az adatimportálás varázsló](search-get-started-portal.md) használatával percek alatt létrehozhatja, betöltheti és kérdezheti le az indexeket.

### <a name="step-3-load-data"></a>3. lépés: Adatok betöltése

Az index definiálása után készen áll a tartalmak feltöltésére. Választhat a küldéses és a lekéréses modell között.

A leküldéses modell a JSON-dokumentumokat egy [SDK](search-howto-dotnet-sdk.md) -ból vagy [Rest](/rest/api/searchservice/addupdate-or-delete-documents)-ból API-kat használó indexbe küldi. A külső adatkészlet gyakorlatilag bármilyen adatforrás lehet, feltéve, hogy a dokumentumok JSON formátumúak.

A lekéréses modell a forrásokból származó adatokat lekéri az Azure-ból, és egy keresési indexbe küldi azt. A lekéréses modellt olyan [*Indexelő*](/rest/api/searchservice/Indexer-operations) eszközön keresztül valósítja meg, amely egyszerűsíti és automatizálja az adatfeldolgozás szempontjait, például az adatfeldolgozáshoz való csatlakozást, az olvasást és a szerializálást. A támogatott adatforrások közé tartozik az Azure Cosmos DB, az Azure SQL és az Azure Storage.

### <a name="step-4-send-queries-and-handle-responses"></a>4. lépés: lekérdezések küldése és a válaszok kezelése

Az indexek feltöltése után a [keresési lekérdezéseket](search-query-overview.md) a szolgáltatás végpontján [REST API](/rest/api/searchservice/Search-Documents) vagy a [.net SDK](/dotnet/api/microsoft.azure.search.idocumentsoperations)-val rendelkező egyszerű HTTP-kérelmek használatával teheti ki.

Lépjen az [első keresőalkalmazás létrehozásához](tutorial-csharp-create-first-app.md) , majd terjesszen fel egy olyan weblapot, amely összegyűjti a felhasználói adatokat, és kezeli az eredményeket. Az [interaktív Rest](search-get-started-postman.md) -hívásokhoz vagy a Azure Portal beépített [keresési Explorerrel](search-explorer.md) is használhatja a meglévő indexek lekérdezéséhez.

## <a name="how-it-compares"></a>Összehasonlítás más keresési megoldásokkal

Az ügyfelek gyakran kérdezik le, hogyan hasonlítanak össze az Azure Cognitive Search más keresési megoldásokkal. Az alábbi táblázat összefoglalja a fő eltéréseket.

| Más szolgáltatás | Fő eltérések |
|-------------|-----------------|
| Microsoft Search | A [Microsoft Search](https://docs.microsoft.com/microsoftsearch/overview-microsoft-search) olyan Microsoft 365 hitelesített felhasználók számára készült, akiknek a SharePoint-tartalommal kell lekérdezni a tartalmat. A szolgáltatás használatra kész, a rendszergazdák által engedélyezett és konfigurált, a Microsofttól és más forrásokból származó összekötők segítségével fogadhatja el a külső tartalmakat. Ha ez leírja a forgatókönyvet, a Microsoft Search with Microsoft 365 vonzó lehetőség a megismerésére.<br/><br/>Ezzel szemben az Azure Cognitive Search lekérdezéseket hajt végre a definiált indexeken keresztül, és a saját adatokkal és dokumentumokkal tölti fel azokat, amelyek gyakran különböző forrásokból származnak. Az Azure Cognitive Search [Indexelő](search-indexer-overview.md)képességekkel rendelkezik néhány Azure-adatforráshoz, de az index sémának megfelelő JSON-dokumentumok egyetlen, konszolidált kereshető erőforrásba küldhetők. Az indexelési folyamat testreszabható úgy is, hogy a gépi tanulási és a lexikális elemzőket is tartalmazza. Mivel Cognitive Search a nagyobb megoldások beépülő moduljának részeként van kialakítva, bármilyen platformon integrálhatja a keresést szinte bármilyen alkalmazásba.|
|Bing | A [Bing Web Search API](../cognitive-services/bing-web-search/index.yml) a Bing.com szolgáltatás indexeiben végez keresést az Ön által megadott kifejezés alapján. Ezek az indexek a nyilvános webhelyeken elérhető HTML, XML és más formátumú tartalmakból állnak. Ugyanerre az alapra épül a [Bing Custom Search](/azure/cognitive-services/bing-custom-search/), amely ugyanezt a webes tartalmakhoz használható bejárási technológiát nyújtja, de egyéni webhelyekre alkalmazva.<br/><br/>A Cognitive Searchban megadhatja és feltöltheti az indexet. Az [Indexelő](search-indexer-overview.md) segítségével az Azure-adatforrások adatai bejárhatók, vagy bármely index-alapú JSON-dokumentum leküldése a keresési szolgáltatásba. |
|Adatbázis-keresés | Számos adatbázis-platform tartalmaz beépített keresőket. Az SQL Server egy [teljes szöveges keresési funkciót](/sql/relational-databases/search/full-text-search) tartalmaz. A Cosmos DB és a hasonló technológiák lekérdezhető indexekkel rendelkeznek. A keresési és tárolási funkciókat egyesítő termékek kiértékelésekor kihívást jelenthet a választás. Számos megoldás a következőket használja: az adatbázis-kezelőt a Storage szolgáltatáshoz, valamint az Azure Cognitive Search a speciális keresési funkciókhoz.<br/><br/>Az adatbázis-kezelői kereséssel összehasonlítva az Azure Cognitive Search a tartalmakat heterogén forrásokból tárolja, és speciális szöveges feldolgozási funkciókat kínál, mint például a nyelvi támogató szöveges feldolgozás (eredő, morfológiai elemzéshez, Word Forms) a [56 nyelven](/rest/api/searchservice/language-support). Emellett támogatja a hibásan írt szavak automatikus javítását, a [szinonimákat](/rest/api/searchservice/synonym-map-operations), a [javaslatokat](/rest/api/searchservice/suggestions), a [pontozási vezérlőket](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), az [aspektusokat](./search-filters-facets.md) és az [egyéni lexikális elemzéseket](/rest/api/searchservice/custom-analyzers-in-azure-search). Az Azure Cognitive Search [teljes szöveges keresőmotorja](search-lucene-query-architecture.md) az Apache Lucene-ra épül, amely az információk lekérésének iparági szabványa. Bár az Azure Cognitive Search invert index formájában őrzi meg az adatmennyiséget, nem helyettesíti a valódi adattárolást, ezért nem javasoljuk, hogy ezt a kapacitásban használja. További információért tekintse meg [ezt a fórumbejegyzést](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Az erőforrás-kihasználás is egy lényeges szempont ebben a kategóriában. Az indexelés és bizonyos lekérdezési műveletek általában nagy számítási terheléssel járnak. A keresések kihelyezése a DBMS-ből egy felhőbeli dedikált megoldásba segít megőrizni a rendszererőforrásokat tranzakciófeldolgozás céljából. Emellett a külső keresési megoldás használata megkönnyíti a lekérdezések mennyiségéhez igazított skálázást is.|
|Dedikált keresési megoldás | Ha úgy döntött, hogy egy teljes körű, dedikált keresési megoldást használ, utolsó lépésként kategorikusan össze kell hasonlítania a helyszíni megoldásokat és egy felhőalapú szolgáltatást. Számos keresési technológia ad lehetőséget az indexelés és a lekérdezési folyamat szabályozására, fejlettebb lekérdezési és szűrési szintaxis használatára, a rangsorolás és a relevancia vezérlésére, valamint önállóan irányított és intelligens keresés végrehajtására. <br/><br/>A felhőszolgáltatás a megfelelő választás, ha kulcsrakész, minimális fenntartási és karbantartási költségű, rugalmasan méretezhető megoldást keres. <br/><br/>A felhőmegoldásokon belül számos szolgáltató nyújt alapvető funkcionalitást teljes szöveges kereséssel, földrajzi kereséssel, valamint a keresési bemenetek bizonyos fokú félreérthetőségének kezelési képességével. Az ideális megoldást jellemzően az határozza meg, hogy milyen [speciális szolgáltatásokra](search-features-list.md) van szüksége, illetve hogy mennyire egyszerű az API-k és az eszközök használata, valamint a felügyelet. |

A felhőalapú szolgáltatók között az Azure Cognitive Search a teljes szöveges keresési feladatokhoz, amelyek az Azure-ban található tartalom-és adatbázis-szolgáltatásokon alapulnak, elsősorban az információk lekérésére és a tartalom navigálására támaszkodó alkalmazások esetében. 

A legfőbb előnyök közé tartoznak az alábbiak:

+ Azure-adatintegráció (webbejárók) az indexelő rétegben
+ Azure Portal központi felügyelethez
+ Az Azure ismert méretezhetősége, megbízhatósága és világszínvonalú rendelkezésre állása
+ A nyers adatok mesterséges feldolgozásával kereshetővé válik, beleértve a képekből származó szöveget, vagy nem strukturált tartalomban talál mintákat.
+ Nyelvi és egyéni elemzési funkciók, teljes szöveges keresési elemzők 56 nyelvhez
+ [Keresésközpontú alkalmazásokban gyakran használt funkciók](search-features-list.md): pontozás, jellemzőkezelés, javaslatok, szinonimák, földrajzi keresés és egyebek.

Ügyfeleink körében az Azure-Cognitive Search számos funkcióját kihasználhatja, például online katalógusokat, üzletági programokat és dokumentum-felderítési alkalmazásokat.

## <a name="watch-this-video"></a>Videó megtekintése

Ebben a 15 perces videóban a program Manager Luis Cabrera bevezeti az Azure Cognitive Search.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]