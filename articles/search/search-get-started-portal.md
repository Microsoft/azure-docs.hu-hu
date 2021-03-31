---
title: Keresési index létrehozása a Azure Portalban
titleSuffix: Azure Cognitive Search
description: Hozza létre, töltse be és kérdezze le az első keresési indexet a Azure Portal adatimportálás varázslójával. Ez a rövid útmutató egy fiktív szállodai adathalmazt használ a mintaadatok számára.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1be165bfe7cca44e8a928933c3c8fe926ad7d4c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694835"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Gyors útmutató: Azure Cognitive Search index létrehozása a Azure Portal

Hozza létre első keresési indexét az **adatimportálás** varázslóval, valamint egy beépített minta adatforrást, amely fiktív szállodai adataiból áll. A varázsló végigvezeti a keresési index (Hotels-Sample-index) létrehozásán, hogy percek alatt írjon érdekes lekérdezéseket. 

Bár ebben a rövid útmutatóban nem fogja használni a beállításokat, a varázsló tartalmaz egy oldalt az AI-bővítéshez, így szöveget és struktúrát tud kinyerni a képfájlokból és a strukturálatlan szövegből. Az AI-bővítést is tartalmazó hasonló bemutatóért lásd [: gyors útmutató: kognitív Készségkészlet létrehozása](cognitive-search-quickstart-blob.md).

## <a name="prerequisites"></a>Előfeltételek

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).

+ Egy Azure Cognitive Search szolgáltatás (bármilyen szintű, bármely régió). [Hozzon létre egy szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

### <a name="check-for-space"></a>Szabad terület ellenőrzése

Sok ügyfél az ingyenes szolgáltatással kezdi. Az ingyenes csomag három indexre, három adatforrásra és három indexelő korlátozódik. Mielőtt hozzákezdene, ellenőrizze, hogy elegendő hellyel rendelkezik-e további elemek számára. Az oktatóanyagban minden objektumból egyet hozhat majd létre.

A szolgáltatás áttekintése oldalon megtudhatja, hogy hány index, indexelő és adatforrás van. 

:::image type="content" source="media/search-get-started-portal/tiles-indexers-datasources.png" alt-text="Indexek, indexelő és adatforrások listája":::

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a> Index létrehozása és az adatok betöltése

A keresési lekérdezések egy [*index*](search-what-is-an-index.md) alapján ismétlődnek, amely kereshető adatokat, metaadatokat és további szerkezeteket tartalmaz, amelyek bizonyos keresési viselkedések optimalizálásához használhatók.

Ebben az oktatóanyagban egy beépített minta-adathalmazt használunk, amely az [*Indexer*](search-indexer-overview.md) használatával bejárható az [ **adatimportálás** varázsló](search-import-data-portal.md)segítségével. Az indexelők adatforrás-specifikus webbejárók, amelyek metaadatokat és tartalmakat képesek olvasni a támogatott Azure-adatforrásokból. Az indexelő általában programozott módon van használatban, de a portálon az **adatimportálás** varázsló segítségével érheti el azokat. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>1. lépés – az adatimportálás varázsló elindítása és adatforrás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.

1. [Keresse meg a keresési szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) , és az Áttekintés lapon kattintson az **adatok importálása** gombra a parancssorban a keresési index létrehozásához és feltöltéséhez.

   :::image type="content" source="media/search-get-started-portal/import-data-cmd.png" alt-text="Adatok importálása parancs":::

1. A varázslóban kattintson a **Kapcsolódás** az  >  **adatmintákhoz**  >  **Hotels-Sample** elemre. Ez az adatforrás beépített. Ha saját adatforrást hozott létre, meg kell adnia a nevet, a típust és a kapcsolódási adatokat. Létrehozását követően „meglévő adatforrássá” válik, amely más importálási műveletek során ismét felhasználható.

   :::image type="content" source="media/search-get-started-portal/import-datasource-sample.png" alt-text="Minta adatkészlet kiválasztása":::

1. Folytassa a következő oldallal.

### <a name="step-2---skip-the-enrich-content-page"></a>2. lépés – a "tartalom gazdagítása" oldal kihagyása

A varázsló támogatja egy AI-bővítési [folyamat](cognitive-search-concept-intro.md) létrehozását a Cognitive Services AI-algoritmusok indexelésbe való integrálásához. 

Most kihagyjuk ezt a lépést, és közvetlenül a cél- **index testreszabásához**.

   :::image type="content" source="media/search-get-started-portal/skip-cog-skill-step.png" alt-text="A kognitív képességek lépés kihagyása":::

> [!TIP]
> Egy rövid [útmutatóban vagy oktatóanyagban](cognitive-search-tutorial-blob.md) [egy AI](cognitive-search-quickstart-blob.md) -indexelési példát is megadhat.

### <a name="step-3---configure-index"></a>3. lépés – index konfigurálása

A beépített Hotels-minta index esetében az alapértelmezett index séma van definiálva. Néhány speciális szűrési példa kivételével a dokumentációban és a mintaként szolgáló, a Hotel-Samples indexet célozó lekérdezések az index definícióján fognak futni:

:::image type="content" source="media/search-get-started-portal/hotelsindex.png" alt-text="Generált szállodák indexe":::

A kód alapú gyakorlatban általában az index létrehozása az adatgyűjtés előtt fejeződik be. Az adatok importálása varázsló ezeket a lépéseket a feltérképezhető adatforrások alapszintű indexének létrehozásával tömöríti. Az indexhez szükség van legalább egy névre és egy mezőgyűjteményre, amely mezők közül az egyiket a dokumentum kulcsaként kell megjelölni. Ez az egyes dokumentumok egyedi azonosítására szolgál. Emellett nyelvi elemzőket vagy javaslatokat is megadhat, ha automatikus kiegészítést vagy javasolt lekérdezéseket szeretne használni.

A mezők adattípusokkal és attribútumokkal rendelkeznek. A fent látható jelölőnégyzetek *indexattribútumok*, amelyek a mező használati módját szabályozzák.

+ **Lekérhető**: azt jelenti, hogy a mező a keresési eredmények listájában jelenik meg. Az egyes mezők kikapcsolási korlátként való megjelöléséhez törölje ezt a jelölőnégyzetet, például a csak szűrési kifejezésekben használt mezőknél.
+ A **kulcs** az egyedi dokumentum azonosítója. Mindig sztring, és kötelező megadni.
+ **Szűrhető**, **rendezhető** és rendszerezhető annak meghatározása, hogy a mezők szűrő, rendezés **vagy csiszolt** navigációs szerkezetben vannak-e használva.
+ **Kereshető**: azt jelenti, hogy a mező szerepel a teljes szöveges keresésben. A sztringek kereshetők. A numerikus és logikai mezőket gyakran nem kereshetőként jelölik meg.

A tárolási követelmények nem változnak a kijelölés eredményeként. Ha például több mezőben állítja be a lekérdezhető **attribútumot** , a tárolási követelmények nem lépnek fel.

Alapértelmezés szerint a varázsló átvizsgálja a adatforrást egyedi azonosítókat keresve, amelyeket felhasználhat a kulcsmező alapjaként. A *karakterláncok* **beolvasható** és **kereshetők**. *Az egész számok* lekérhető **,** **szűrhető**, **rendezhető** és **sokrétű** lehet.

1. Fogadja el az alapértelmezett beállításokat.

   Ha a varázslót Másodszor futtatja egy meglévő szálláshely-adatforrás használatával, az index nem lesz alapértelmezett attribútumokkal konfigurálva. A jövőbeli importálások attribútumait manuálisan kell kiválasztani. 

1. Folytassa a következő oldallal.

### <a name="step-4---configure-indexer"></a>4. lépés – indexelő konfigurálása

Továbbra is az **Adatok importálása** varázslóban maradva kattintson az **Indexelő** > **Név** lehetőségre, és gépelje be az indexelő nevét.

Ez az objektum egy végrehajtható folyamatot határoz meg. Azt is megteheti, hogy ismétlődő ütemtervet használ, de most az alapértelmezett beállítással futtathatja az indexelő azonnal.

Kattintson a **Submit (Küldés** ) gombra az indexelő létrehozásához és egyidejű futtatásához.

  :::image type="content" source="media/search-get-started-portal/hotels-indexer.png" alt-text="Hotels indexelő":::

## <a name="monitor-progress"></a>Figyelési folyamat

A varázslónak el kell végeznie az indexelő lista listáját, ahol nyomon követheti a folyamat előrehaladását. Az önnavigációhoz lépjen az Áttekintés lapra, és kattintson az **Indexelő** fülre.

Több percet is igénybe vehet, amíg a portál frissíti az oldalt, de az újonnan létrehozott indexelő megjelenik a listában, amelynek állapota "folyamatban" vagy sikeres, valamint az indexelt dokumentumok száma.

   :::image type="content" source="media/search-get-started-portal/indexers-inprogress.png" alt-text="Indexelő állapotüzenete":::

## <a name="view-the-index"></a>Az index megtekintése

A szolgáltatás áttekintése lap az Azure Cognitive Search szolgáltatásban létrehozott erőforrásokra mutató hivatkozásokat tartalmaz.  Az imént létrehozott index megtekintéséhez kattintson az **indexek** elemre a hivatkozások listájában. 

Várjon, amíg frissül a portál oldala. Néhány perc elteltével az indexnek a dokumentumok száma és a tárterület mérete alapján kell megjelennie.

   :::image type="content" source="media/search-get-started-portal/indexes-list.png" alt-text="Indexek listája a szolgáltatás irányítópultján":::

Ebből a listából rákattinthat az imént létrehozott *Hotels-Sample* indexre, és megtekintheti az index sémát. és opcionálisan hozzáadhat új mezőket. 

A **mezők** lap az index sémát jeleníti meg. Ha lekérdezéseket ír, és ellenőriznie kell, hogy egy mező szűrhető vagy rendezhető, ezen a lapon láthatók az attribútumok.

Görgessen a lista aljára, és adjon meg egy új mezőt. Habár mindig létrehozhat egy új mezőt, a legtöbb esetben nem módosíthatja a meglévő mezőket. A meglévő mezők fizikai ábrázolással rendelkeznek a keresési szolgáltatásban, ezért nem módosíthatók, még a kódban sem. Egy meglévő mező alapvető módosításához hozzon létre egy új indexet, és vidd az eredetit.

   :::image type="content" source="media/search-get-started-portal/sample-index-def.png" alt-text="példa indexdefinícióra":::

Egyéb szerkezetek, például pontozási profilok és CORS-paraméterek, bármikor hozzáadhatók.

Annak érdekében, hogy jól átlássa az indextervezés során szerkeszthető és nem szerkeszthető elemeket, szánjon egy percet az indexdefiníciós lehetőségek áttekintésére. A kiszürkített lehetőségek azt jelzik, hogy az érték nem módosítható vagy törölhető. 

## <a name="query-using-search-explorer"></a><a name="query-index"></a> Lekérdezés a keresési Explorerrel

Továbblépve most már rendelkezünk egy keresési indexszel, amely készen áll a lekérdezésre a beépített [**Keresési ablak**](search-explorer.md) lekérdezési lappal. Ez egy keresőmezőt biztosít, amellyel tesztelheti a tetszőleges lekérdezési sztringeket.

A **Search Explorer** csak [REST API kérelmek](/rest/api/searchservice/search-documents)kezelésére alkalmas, de az [egyszerű lekérdezési szintaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search) és a [teljes Lucene lekérdezési elemző](/rest/api/searchservice/lucene-query-syntax-in-azure-search)szintaxisát is elfogadja, valamint a [keresési dokumentumban REST API](/rest/api/searchservice/search-documents#bkmk_examples) műveletekben elérhető összes keresési paramétert.

> [!TIP]
> Az alábbi lépéseket a 6m08s az [Azure Cognitive Search áttekintő videójában](https://channel9.msdn.com/Events/Connect/2016/138)mutatjuk be.
>

1. A parancssávon kattintson a **Keresési ablak** elemre.

   :::image type="content" source="media/search-get-started-portal/search-explorer-cmd.png" alt-text="Keresési ablak parancs":::

1. Az **index** legördülő menüben válassza a  *Hotels-Sample-index* lehetőséget. Kattintson az **API-verzió** legördülő menüre, és ellenőrizze, hogy mely REST API-k érhetők el. Az alábbi lekérdezésekhez használja az általánosan elérhető verziót (2020-06-30).

   :::image type="content" source="media/search-get-started-portal/search-explorer-changeindex.png" alt-text="Index és API-parancsok":::

1. A keresősáv alatt illessze be az alábbi lekérdezési karakterláncokat, és kattintson a **Keresés** gombra.

   :::image type="content" source="media/search-get-started-portal/search-explorer-query-string-example.png" alt-text="Lekérdezési karakterlánc és Keresés gomb":::

## <a name="example-queries"></a>Példa a lekérdezésekre

Megadhatja a kifejezéseket és kifejezéseket, hasonlóan a Bing vagy a Google Search szolgáltatáshoz, vagy teljesen meghatározott lekérdezési kifejezésekhez. Az eredményeket a rendszer részletes JSON-dokumentumként adja vissza.

### <a name="simple-query-with-top-n-results"></a>Egyszerű lekérdezés az első N eredménnyel

#### <a name="example-string-query-searchspa"></a>Példa (karakterlánc-lekérdezés): `search=spa`

+ A **keresési** paraméter segítségével megadhatja a teljes szöveges keresés kulcsszavas keresését. ebben az esetben a rendszer a dokumentum bármely kereshető mezőjében visszaküldi a *Spa* -t tartalmazó adatokat.

+ A **Keresési ablak** JSON-formátumban adja vissza az eredményeket, amely részletes és nehezen olvasható lehet, ha a dokumentumok sűrű szerkezettel rendelkeznek. Ez szándékos; a teljes dokumentum láthatósága a fejlesztés szempontjából fontos, különösen a tesztelés során. A jobb felhasználói élmény érdekében olyan kódot kell írnia, amely [a keresési eredmények kezelésével](search-pagination-page-layout.md) kiemeli a fontosabb elemeket.

+ A dokumentumokban minden mező „lekérdezhetőként” van jelölve az indexben. Az index attribútumainak megtekintéséhez a portálon kattintson a *Hotels-Sample* elemre az **indexek** listájában.

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Példa (paraméteres lekérdezés): `search=spa&$count=true&$top=10`

+ A **&** szimbólum a keresési paraméterek hozzáfűzésére szolgál, amelyek bármilyen sorrendben megadhatók.

+ A **$Count = True** paraméter a visszaadott dokumentumok teljes számát adja vissza. Ez az érték a keresési eredmények elejénél található. A szűrőlekérdezések ellenőrzéséhez megfigyelheti a **$count=true** paraméter által jelentett módosításokat. A kisebb darabszámok azt jelzik, hogy a szűrő működik.

+ A **$Top = 10** érték a legmagasabb rangsorolt 10 dokumentumot adja vissza a teljes összegből. Alapértelmezés szerint az Azure Cognitive Search az első 50 legjobb egyezést adja vissza. A **$top** paraméter használatával növelheti vagy csökkentheti a mennyiséget.

### <a name="filter-the-query"></a><a name="filter-query"></a> A lekérdezés szűrése

A **$filter** paraméter hozzáfűzésekor a szűrők megjelennek a keresési kérésekben. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Példa (szűrt): `search=beach&$filter=Rating gt 4`

+ A **$filter** paraméter olyan eredményeket ad vissza, amelyek megfelelnek a megadott feltételeknek. Ebben az esetben a 4-nél nagyobb minősítések.

+ A szűrőszintaxis egy OData-konstrukció. További információk: [OData-szűrőszintaxis](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-the-query"></a><a name="facet-query"></a> A lekérdezés értékkorlátozása

Az értékkorlátozó szűrők megjelennek a keresési kérésekben. A facet paraméter adja vissza azon dokumentumok összegzett darabszámát, amelyek megfelelnek a megadott értékkorlátozási értéknek.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Példa (hatókörszűkítéssel korlátozva): `search=*&facet=Category&$top=2`

+ A **search=*** egy üres keresés. Az üres keresések mindenben keresnek. Az üres lekérdezések elküldésének egyik oka a teljes dokumentumkészlet szűrése vagy értékkorlátozása lehet. Például azt szeretné, hogy egy aspektusban lévő navigációs struktúra az index összes szállodájának álljon.
+ A **facet** paraméter olyan navigációs szerkezetet ad vissza, amelyet továbbíthat egy felhasználói felületi vezérlőnek. Kategóriákat és egy számot ad vissza. Ebben az esetben a kategóriák egy *kategóriának* megfelelő mezőn alapulnak. Nincs összesítés az Azure Cognitive Searchban, de megközelítheti az összesítést a használatával `facet` , amely az egyes kategóriákban lévő dokumentumok számát adja meg.

+ A **$top=2** paraméter két dokumentumot ad vissza, így bemutatja, hogy a `top` használatával csökkentheti és növelheti is az eredményeket.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Példa (a numerikus értékekre korlátozva): `search=spa&facet=Rating`

+ Ez a lekérdezés az értékelés dimenziója, a *Spa* szöveges keresésekor. A *minősítés* kifejezése egy dimenzióként adható meg, mert a mező lekérhető, szűrhető, és az indexben látható, valamint a benne foglalt értékek (numerikus, 1 – 5) alapján kategorizálható a listák csoportjaiba.

+ Csak a szűrhető mezők értéke korlátozható. Csak a lekérdezhető mezők adhatók vissza az eredményekben.

+ A *minősítés* mező dupla pontosságú lebegőpontos, és a csoportosítás pontos értékkel történik. További információ az intervallumok szerinti csoportosításról (például "3 csillagos minősítések", "4 csillagos minősítések" stb.): részletes [Navigálás megvalósítása az Azure Cognitive Searchban](./search-faceted-navigation.md#filter-based-on-a-range).

### <a name="highlight-search-results"></a><a name="highlight-query"></a> Keresési eredmények kiemelése

A találatok kiemelése a kulcsszóval megegyező szöveg formázását jelenti, feltéve, hogy vannak egyezések a megadott mezőben. Ha a keresett kifejezés egy leírás mélyén rejlik, a találatok kiemelése funkcióval könnyebben észrevehetővé teheti.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Példa (kiemelő): `search=beach&highlight=Description`

+ Ebben a példában a formázott Word- *strand* könnyebben látható a Leírás mezőben.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Példa (nyelvi elemzés): `search=beaches&highlight=Description`

+ A teljes szöveges keresés felismeri a Word-űrlapok alapvető változatait. Ebben az esetben a keresési eredmények a "Beach" Kiemelt szövegét tartalmazzák azon szállodák esetében, amelyeken a kifejezés a kereshető mezőkben szerepel, a "strandok" kifejezésre adott kulcsszavas keresésre válaszul. A nyelvészeti elemzés következtében ugyanazon szó különböző alakjai is megjelenhetnek az eredmények között. 

+ Az Azure Cognitive Search a Lucene és a Microsofttól származó 56-elemzőt is támogatja. Az Azure Cognitive Search alapértelmezés szerint a standard Lucene Analyzer.

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> Az intelligens keresés kipróbálása

Alapértelmezés szerint a helytelenül írt lekérdezési kifejezések (például a "Seattle" *Seatle* ) nem felelnek meg a szokásos keresésben szereplő találatoknak. A következő példa nem ad vissza eredményt.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Példa (elírt kifejezés, nem kezelve): `search=seatle`

A helytelenül leírt szavak kezelésére használhat intelligens keresést. Az intelligens keresés akkor érhető el, amikor a teljes Lucene lekérdezési szintaxis használja, ehhez pedig a következő két dolog szükséges: állítsa be a **queryType=full** paramétert a lekérdezésre, és fűzze hozzá a **~** utótagot a keresési sztringhez.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Példa (elírt kifejezés, kezelve): `search=seatle~&queryType=full`

Ez a példa most a "Seattle" egyezéseit tartalmazó dokumentumokat adja vissza.

Amikor a **queryType** paraméter nincs meghatározva, a rendszer az alapértelmezett egyszerű lekérdezéselemzőt használja. Ez az egyszerű lekérdezéselemző gyorsabb, de ha intelligens keresésre, reguláris kifejezésekre, közelségi keresésre vagy egyéb speciális lekérdezéstípusokra van szüksége, a teljes szintaxisra szüksége lesz.

Az intelligens keresés és a helyettesítő karakteres keresések befolyásolják a keresési eredményeket. A rendszer az ilyen formátumú kereséseken nem végez nyelvi elemzést. A fuzzy és a helyettesítő karakteres keresés használata előtt tekintse át, [Hogyan működik a teljes szöveges keresés az Azure Cognitive Searchban](search-lucene-query-architecture.md#stage-2-lexical-analysis) , és keresse meg a lexikális analízis alóli kivételekről szóló szakaszt.

A teljes lekérdezés-elemző által engedélyezett lekérdezési forgatókönyvekkel kapcsolatos további információkért lásd: [Lucene lekérdezési szintaxis az Azure Cognitive Searchban](/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="try-geospatial-search"></a><a name="geo-search"></a> A térinformatikai keresés kipróbálása

 A térinformatikai keresés az [edm.GeographyPoint adattípuson](/rest/api/searchservice/supported-data-types) keresztül támogatott a koordinátákat tartalmazó mezők esetében. A geosearch egy szűrőtípus, amelynek meghatározása a [OData-szűrőszintaxis](/rest/api/searchservice/odata-expression-syntax-for-azure-search) című témakörben olvasható.

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Példa (geokoordináta-szűrők): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

A példa lekérdezés minden eredményt szűr a helyzeti adatok alapján, és olyan eredményeket a vissza, amelyek kevesebb, mint 5 kilométerre találhatók a (szélességi és hosszúsági koordinátákként) megadott ponttól. A **$count** paraméter hozzáadásával láthatja, hány eredményt ad vissza a rendszer, amikor módosítja a távolságot vagy a koordinátákat.

A térinformatikai keresés hasznos lehet, ha a keresőalkalmazás rendelkezik „keresés a közelben” funkcióval vagy térképes navigációt használ. Ez azonban nem teljes szöveges keresés. Ha a város vagy ország/régió szerinti kereséshez felhasználói követelmények vannak megadva, a koordináták mellett adja hozzá a város vagy ország/régió nevét tartalmazó mezőket.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag gyors bevezetést biztosít az Azure Cognitive Search a Azure Portal használatával.

Megtudta, hogyan hozhat létre keresési indexet az **Adatok importálása** varázslóval. Megismerkedett az [indexelőkkel](search-indexer-overview.md), valamint az indextervezés alapvető munkafolyamatával, többek között [a közzétett indexek támogatott módosításaival](/rest/api/searchservice/update-index) is.

Az Azure Portal **Keresési ablakával** megismert egyes alapvető lekérdezési szintaxisokat olyan gyakorlati példákon keresztül, amelyek kulcsfontosságú funkciókat, például a szűrést, a találatok kiemelését, az intelligens és a földrajzi keresést mutatták be.

Azt is megtanulta, hogyan találhat indexeket, indexelő fájlokat és adatforrásokat a portálon. Később bármilyen új adatforrás definícióit és mezőgyűjteményeit gyorsan és egyszerűen ellenőrizheti a portálon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Egy portál varázsló segítségével előkészítheti a böngészőben futó, használatra kész webalkalmazást. Ezt a varázslót kipróbálhatja az imént létrehozott kis indexen, vagy használhatja a beépített mintaadatok egyikét egy gazdagabb keresési élményhez.

> [!div class="nextstepaction"]
> [Bemutató alkalmazás létrehozása a portálon](search-create-app-portal.md)