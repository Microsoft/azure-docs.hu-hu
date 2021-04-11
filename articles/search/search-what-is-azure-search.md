---
title: Bevezetés az Azure Cognitive Search használatába
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search egy teljes körűen felügyelt felhőalapú keresési szolgáltatás a Microsofttól. Ismerje meg a használati eseteket, a fejlesztési munkafolyamatot, a Microsoft keresési termékeinek összehasonlítását és az első lépéseket.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/09/2021
ms.custom: contperf-fy21q1
ms.openlocfilehash: e17d08d09814c135af3e0b4fc299b6e6f42326d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549889"
---
# <a name="what-is-azure-cognitive-search"></a>Mi az az Azure Cognitive Search?

Az Azure Cognitive Search ([korábbi nevén "Azure Search"](whats-new.md#new-service-name)) egy felhőalapú keresési szolgáltatás, amely lehetővé teszi, hogy a fejlesztők API-kat és eszközöket kínáljanak a webes, mobil-és Nagyvállalati alkalmazások privát, heterogén tartalmainak gazdag keresési élményének kialakításához. 

A keresés alapja bármely olyan alkalmazás, amely tartalmat felhasznál a felhasználók számára, olyan gyakori forgatókönyvekkel, mint például a katalógus vagy a dokumentumok keresése, az e-kereskedelmi webhelyek keresése vagy az adatelemzés. A Cognitive Search API-k és architektúrája egyszerűsíti a kifinomult információk beolvasásának feladatát bármely megoldáshoz.

A keresési szolgáltatás a következő összetevőket tartalmazhatja:

+ Keresőmotor a teljes szöveges kereséshez
+ A felhasználó tulajdonában lévő indexelt tartalom állandó tárolása
+ A tartalom indexeléséhez és lekérdezéséhez szükséges API-k
+ Opcionális [AI-alapú dúsítások](cognitive-search-concept-intro.md), kereshető tartalmak létrehozása képekből, nyers strukturálatlan szöveg, alkalmazásfájlok
+ Opcionális integráció más Azure-szolgáltatásokkal az adatkezeléshez, gépi tanuláshoz/AI, monitorozáshoz és biztonsághoz
+ A [szemantikai keresés (előzetes verzió)](semantic-search-overview.md) opcionális megvalósítása a jobb relevancia szempontjából

A keresési szolgáltatás építészeti szempontból a nem indexelt adatokat tartalmazó külső adattárak között található, és az ügyfélalkalmazás, amely lekérdezési kérelmeket küld egy keresési indexnek, és kezeli a választ.

![Azure Cognitive Search-architektúra](media/search-what-is-azure-search/azure-search-diagram.svg "Azure Cognitive Search-architektúra")

Külsőleg a keresés integrálható más Azure-szolgáltatásokkal olyan *Indexelő* formájában, amely automatizálja az adatok betöltését/lekérését az Azure-adatforrásokból, valamint olyan *szakértelmével* , amelyek a Cognitive Services, például a képek és a szöveges elemzések, vagy a Azure Machine learning vagy a Azure FUNCTIONSban létrehozott egyéni AI-k használatával.

## <a name="inside-a-search-service"></a>Keresési szolgáltatáson belül

A keresési szolgáltatásban a két elsődleges munkaterhelés az *indexelés* és a *lekérdezés*. 

+ Az [indexelés](search-what-is-an-index.md) olyan beviteli folyamat, amely betölti a tartalmat a keresési szolgáltatásba, és kereshetővé teszi. Belsőleg a bejövő szövegeket a rendszer tokenekre dolgozza fel, és a gyors vizsgálatok érdekében fordított indexekben tárolja őket. A JSON-dokumentumok formájában bármilyen szöveget fel lehet tölteni.

  Továbbá, ha a tartalom vegyes fájlokat tartalmaz, lehetősége van a *mesterséges intelligenciát* is felvenni a [kognitív képességek](cognitive-search-working-with-skillsets.md)segítségével. Az AI-bővítés kinyerheti az alkalmazásfájlokba ágyazott szöveget, és a tartalom elemzésével a nem szöveges fájlokból is kikövetkeztetheti a szöveget és a struktúrát. 

  Az elemzést biztosító készségek előre definiálva vannak a Microsofttól, illetve a létrehozott egyéni készségektől. A későbbi elemzések és átalakítások olyan új információkat és struktúrákat eredményeznek, amelyek korábban nem léteztek, így nagy mennyiségű keresési és tudás-adatbányászati forgatókönyvet biztosítanak.

+ A [lekérdezés](search-query-overview.md) akkor fordulhat elő, ha az index kereshető szöveggel van feltöltve, amikor az ügyfélalkalmazás lekérdezési kérelmeket küld egy keresési szolgáltatásnak, és kezeli a válaszokat. Az összes lekérdezés végrehajtása a szolgáltatásban létrehozott, saját és tárolt keresési index fölé esik. Az ügyfélalkalmazás esetében a keresési élmény az Azure Cognitive Search API-jai használatával van definiálva, és a következők lehetnek: relevancia finomhangolása, automatikus kiegészítés, szinonimák egyeztetése, zavaros megfeleltetés, minta egyeztetés, szűrés és rendezés.

A funkciókat egy egyszerű [REST API-n](/rest/api/searchservice/) vagy [.NET SDK-n](search-howto-dotnet-sdk.md) keresztül tudja elérni, mely elfedi az információk kiolvasásának mögöttes komplexitását. A szolgáltatás felügyeletéhez és a tartalomkezeléshez használható Azure Portal is használhatja, az indexek és a szakértelmével prototípusának és lekérdezésének eszközeivel. Mivel a szolgáltatás a felhőben fut, az infrastruktúrát és a rendelkezésre állást a Microsoft felügyeli.

## <a name="why-use-cognitive-search"></a>Miért érdemes Cognitive Search

Az Azure Cognitive Search kiválóan alkalmas a következő alkalmazási forgatókönyvekhez:

+ Heterogén tartalom konszolidálása privát, felhasználó által definiált keresési indexbe.

+ Könnyen megvalósítható a kereséssel kapcsolatos funkciók: a relevancia finomhangolása, a sokoldalú navigálás, a szűrők (beleértve a Geo-térbeli keresést is), a szinonimák leképezése és az automatikus kiegészítés.

+ Az Azure Blob Storage-ban vagy Cosmos DBban tárolt, nagyméretű, nem megkülönböztethető szöveg-és képfájlok, illetve alkalmazások fájljainak átalakítása kereshető JSON-dokumentumba. Ez a külső feldolgozást kiegészítő [kognitív ismereteken](cognitive-search-concept-intro.md) keresztül érhető el az indexben.

+ Nyelvi vagy egyéni szöveges elemzés hozzáadása. Ha nem angol nyelvű tartalommal rendelkezik, az Azure Cognitive Search a Lucene-elemzőket és a Microsoft természetes nyelvi processzorait is támogatja. Az elemzőket úgy is konfigurálhatja, hogy a nyers tartalom speciális feldolgozását, például a Mellékjelek kiszűrését, illetve a mintázatok felismerését és a karakterláncokban való megőrzését.

További információ az egyes funkciókról: [Az Azure Cognitive Search szolgáltatásai](search-features-list.md)

## <a name="how-to-get-started"></a>Első lépések

Az alapvető keresési funkciók teljes körű feltárása négy lépésben érhető el:

1. [**Hozzon létre egy keresési szolgáltatást**](search-create-service-portal.md) a megosztott ingyenes szinten, vagy egy [számlázható szintet](https://azure.microsoft.com/pricing/details/search/) a kizárólag a szolgáltatás által használt dedikált erőforrások számára. Az összes rövid útmutató és oktatóanyag egy megosztott szolgáltatáson is elvégezhető.

1. [**Keresési index létrehozása**](search-what-is-an-index.md) a portál, a [REST API](/rest/api/searchservice/create-index), a [.net SDK](search-howto-dotnet-sdk.md)vagy más SDK használatával. Az index séma határozza meg a kereshető tartalom szerkezetét.

1. [**Tartalmat tölthet fel**](search-what-is-data-import.md) a ["push" modellel](tutorial-optimize-indexing-push-api.md) a JSON-dokumentumok bármilyen forrásból való leküldéséhez, vagy a ["pull" modell (indexelő)](search-indexer-overview.md) használatával, ha a forrásadatok az Azure-on vannak.

1. [**Egy index lekérdezése**](search-query-overview.md) a portálon, a [REST API](search-get-started-rest.md), a [.net SDK](/dotnet/api/azure.search.documents.searchclient.search)-ban vagy más SDK-ban található [keresési tallózó](search-explorer.md) használatával.

A kezdeti feltáráshoz kezdje az [**adatimportálás varázslóval**](search-get-started-portal.md) , valamint egy beépített Azure-adatforrást egy index létrehozásához, betöltéséhez és lekérdezéséhez percek alatt.

Az összetett vagy egyéni megoldásokkal kapcsolatos segítségért [**vegye fel a kapcsolatot egy partnerrel**](resource-partners-knowledge-mining.md) Cognitive Search technológiában.

## <a name="compare-search-options"></a>Keresési beállítások összehasonlítása

Az ügyfelek gyakran kérdezik le, hogyan hasonlítanak össze az Azure Cognitive Search más keresési megoldásokkal. Az alábbi táblázat összefoglalja a fő eltéréseket.

| Más szolgáltatás | Fő eltérések |
|-------------|-----------------|
| Microsoft Search | A [Microsoft Search](/microsoftsearch/overview-microsoft-search) olyan Microsoft 365 hitelesített felhasználók számára készült, akiknek a SharePoint-tartalommal kell lekérdezni a tartalmat. A szolgáltatás használatra kész, a rendszergazdák által engedélyezett és konfigurált, a Microsofttól és más forrásokból származó összekötők segítségével fogadhatja el a külső tartalmakat. Ha ez leírja a forgatókönyvet, a Microsoft Search with Microsoft 365 vonzó lehetőség a megismerésére.<br/><br/>Ezzel szemben az Azure Cognitive Search lekérdezéseket hajt végre a definiált indexeken keresztül, és a saját adatokkal és dokumentumokkal tölti fel azokat, amelyek gyakran különböző forrásokból származnak. Az Azure Cognitive Search [Indexelő](search-indexer-overview.md)képességekkel rendelkezik néhány Azure-adatforráshoz, de az index sémának megfelelő JSON-dokumentumok egyetlen, konszolidált kereshető erőforrásba küldhetők. Az indexelési folyamat testreszabható úgy is, hogy a gépi tanulási és a lexikális elemzőket is tartalmazza. Mivel Cognitive Search a nagyobb megoldások beépülő moduljának részeként van kialakítva, bármilyen platformon integrálhatja a keresést szinte bármilyen alkalmazásba.|
|Bing | A [Bing Web Search API](../cognitive-services/bing-web-search/index.yml) a Bing.com szolgáltatás indexeiben végez keresést az Ön által megadott kifejezés alapján. Ezek az indexek a nyilvános webhelyeken elérhető HTML, XML és más formátumú tartalmakból állnak. Ugyanerre az alapra épül a [Bing Custom Search](/azure/cognitive-services/bing-custom-search/), amely ugyanezt a webes tartalmakhoz használható bejárási technológiát nyújtja, de egyéni webhelyekre alkalmazva.<br/><br/>A Cognitive Searchban megadhatja és feltöltheti az indexet. Az [Indexelő](search-indexer-overview.md) segítségével az Azure-adatforrások adatai bejárhatók, vagy bármely index-alapú JSON-dokumentum leküldése a keresési szolgáltatásba. |
|Adatbázis-keresés | Számos adatbázis-platform tartalmaz beépített keresőket. Az SQL Server egy [teljes szöveges keresési funkciót](/sql/relational-databases/search/full-text-search) tartalmaz. A Cosmos DB és a hasonló technológiák lekérdezhető indexekkel rendelkeznek. A keresési és tárolási funkciókat egyesítő termékek kiértékelésekor kihívást jelenthet a választás. Számos megoldás a következőket használja: az adatbázis-kezelőt a Storage szolgáltatáshoz, valamint az Azure Cognitive Search a speciális keresési funkciókhoz.<br/><br/>Az adatbázis-kezelői kereséssel összehasonlítva az Azure Cognitive Search a tartalmakat heterogén forrásokból tárolja, és speciális szöveges feldolgozási funkciókat kínál, mint például a nyelvi támogató szöveges feldolgozás (eredő, morfológiai elemzéshez, Word Forms) a [56 nyelven](/rest/api/searchservice/language-support). Emellett támogatja a hibásan írt szavak automatikus javítását, a [szinonimákat](/rest/api/searchservice/synonym-map-operations), a [javaslatokat](/rest/api/searchservice/suggestions), a [pontozási vezérlőket](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), az [aspektusokat](./search-filters-facets.md) és az [egyéni lexikális elemzéseket](/rest/api/searchservice/custom-analyzers-in-azure-search). Az Azure Cognitive Search [teljes szöveges keresőmotorja](search-lucene-query-architecture.md) az Apache Lucene-ra épül, amely az információk lekérésének iparági szabványa. Bár az Azure Cognitive Search invert index formájában őrzi meg az adatmennyiséget, nem helyettesíti a valódi adattárolást, ezért nem javasoljuk, hogy ezt a kapacitásban használja. További információért tekintse meg [ezt a fórumbejegyzést](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Az erőforrás-kihasználás is egy lényeges szempont ebben a kategóriában. Az indexelés és bizonyos lekérdezési műveletek általában nagy számítási terheléssel járnak. A keresések kihelyezése a DBMS-ből egy felhőbeli dedikált megoldásba segít megőrizni a rendszererőforrásokat tranzakciófeldolgozás céljából. Emellett a külső keresési megoldás használata megkönnyíti a lekérdezések mennyiségéhez igazított skálázást is.|
|Dedikált keresési megoldás | Ha úgy döntött, hogy egy teljes körű, dedikált keresési megoldást használ, utolsó lépésként kategorikusan össze kell hasonlítania a helyszíni megoldásokat és egy felhőalapú szolgáltatást. Számos keresési technológia ad lehetőséget az indexelés és a lekérdezési folyamat szabályozására, fejlettebb lekérdezési és szűrési szintaxis használatára, a rangsorolás és a relevancia vezérlésére, valamint önállóan irányított és intelligens keresés végrehajtására. <br/><br/>A felhőszolgáltatás a megfelelő választás, ha kulcsrakész, minimális fenntartási és karbantartási költségű, rugalmasan méretezhető megoldást keres. <br/><br/>A felhőmegoldásokon belül számos szolgáltató nyújt alapvető funkcionalitást teljes szöveges kereséssel, földrajzi kereséssel, valamint a keresési bemenetek bizonyos fokú félreérthetőségének kezelési képességével. Az ideális megoldást jellemzően az határozza meg, hogy milyen [speciális szolgáltatásokra](search-features-list.md) van szüksége, illetve hogy mennyire egyszerű az API-k és az eszközök használata, valamint a felügyelet. |

A felhőalapú szolgáltatók között az Azure Cognitive Search a teljes szöveges keresési feladatokhoz, amelyek az Azure-ban található tartalom-és adatbázis-szolgáltatásokon alapulnak, elsősorban az információk lekérésére és a tartalom navigálására támaszkodó alkalmazások esetében. 

A legfőbb előnyök közé tartoznak az alábbiak:

+ Azure-adatintegráció (webbejárók) az indexelő rétegben
+ Az Azure Private link integrációja az internetre vonatkozó biztonsági követelmények támogatásához
+ Integráció AI-feldolgozással a kereshető tartalomtípusok szövegének kereshetővé tételéhez.
+ Nyelvi és egyéni elemzési funkciók, teljes szöveges keresési elemzők 56 nyelvhez
+ [Kritikus funkciók](search-features-list.md): gazdag lekérdezési nyelv, relevancia-hangolás, aspektus, automatikus kiegészítés, szinonimák, Geo-keresés és eredmény-összeállítás.
+ Az Azure ismert méretezhetősége, megbízhatósága és világszínvonalú rendelkezésre állása

Ügyfeleink körében az Azure-Cognitive Search számos funkcióját kihasználhatja, például online katalógusokat, üzletági programokat és dokumentum-felderítési alkalmazásokat.

## <a name="watch-this-video"></a>Videó megtekintése

Ebben a 15 perces videóban a program Manager Luis Cabrera bevezeti az Azure Cognitive Search.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]