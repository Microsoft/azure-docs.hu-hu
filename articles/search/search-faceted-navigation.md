---
title: Csiszolt navigációs kategória hierarchiájának hozzáadása
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search-nal integrálható keresési alkalmazásokban az önálló irányított szűréshez hozzáadhat csiszolatlan navigálást.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 05be5295ae5f8c73c916a21bba7dbc98ab0c5e87
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89002792"
---
# <a name="how-to-implement-faceted-navigation-in-azure-cognitive-search"></a>Sokoldalú navigáció megvalósítása az Azure-ban Cognitive Search

A sokoldalú Navigálás olyan szűrési mechanizmus, amely a keresési alkalmazásokban a saját irányítású részletezési navigációt teszi lehetővé. Előfordulhat, hogy a "sokoldalú navigáció" kifejezés ismeretlen, de korábban már használta. Az alábbi példa azt mutatja, hogy a csiszolatlan Navigálás nem több, mint az eredmények szűréséhez használt kategóriák.

 ![Azure Cognitive Search Job Portal – bemutató](media/search-faceted-navigation/azure-search-faceting-example.png "Azure Cognitive Search Job Portal – bemutató")

A sokoldalú Navigálás egy alternatív belépési pont a kereséshez. Kényelmes alternatívát kínál összetett keresési kifejezések kézzel történő beírásához. Az aspektusok segítségével megtalálhatja, hogy mit keres, és ezzel biztosíthatja, hogy ne kapjon nulla eredményt. Fejlesztőként a dimenziók lehetővé teszik, hogy elérhetővé tegye a keresési indexhez való navigálás leghasznosabb keresési feltételeit. Az online kiskereskedelmi alkalmazásokban a sokoldalú Navigálás gyakran a márkák, a részlegek (gyerek cipők), a méret, az ár, a népszerűség és a minősítések alapján történik. 

A sokoldalú navigáció megvalósítása különböző keresési technológiákkal tér el. Az Azure Cognitive Search-ban a lecsiszolt navigáció a lekérdezési időpontra épül, a sémában korábban használt mezők használatával.

-   Az alkalmazás által létrehozott lekérdezésekben a lekérdezésnek meg kell küldenie a *dimenzió lekérdezési paramétereit* , hogy lekérje a dokumentumhoz tartozó eredményhalmaz elérhető dimenzióinak értékeit.

-   A dokumentum-eredményhalmaz tényleges kivágásához az alkalmazásnak egy kifejezést is alkalmaznia kell `$filter` .

Az alkalmazásfejlesztés során a lekérdezéseket alkotó kód megírása a munka nagy részét képezi. A szolgáltatás számos olyan alkalmazási viselkedést is tartalmaz, amelyeket a sokoldalú navigálástól elvár, beleértve a tartományok definiálásának és a dimenziók eredményeinek beszerzésének beépített támogatását is. A szolgáltatás olyan ésszerű alapértékeket is tartalmaz, amelyek segítenek elkerülni a nem nehézkes navigációs struktúrákat. 

## <a name="sample-code-and-demo"></a>Mintakód és bemutató
Ez a cikk a feladatok keresési portálját használja példaként. A példa ASP.NET MVC-alkalmazásként van implementálva.

- Tekintse meg és tesztelje a munkahelyi bemutatót az [Azure Cognitive Search Job Portal bemutatójában](https://aka.ms/azjobsdemo).

- Töltse le a kódot az [Azure-Samples repóból a githubon](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Bevezetés
Ha még nem ismeri a fejlesztést, a legjobb módszer a sokoldalú navigálásra, hogy a saját irányított keresés lehetőségeit jelenítse meg. Az előre meghatározott szűrők alapján részletezett keresési élményt nyújt, amely a keresési eredmények gyors, pont-és kattintásos műveletekkel való szűkítéséhez használható. 

### <a name="interaction-model"></a>Interakciós modell

A sokoldalú Navigálás keresési felülete iterációs megoldás, ezért kezdjük azzal, hogy a felhasználói műveletekre adott válaszként megjelenő lekérdezések sorozatából megértsük.

A kiindulási pont egy olyan alkalmazási oldal, amely sokoldalú navigálást tesz lehetővé, jellemzően a periférián helyezi el. A sokoldalú Navigálás gyakran faszerkezetes, az egyes értékekhez tartozó jelölőnégyzetekkel vagy a szövegre kattintva. 

1. Az Azure Cognitive Searchba eljuttatott lekérdezés megadja a csiszolt navigációs szerkezetet egy vagy több aspektus lekérdezési paraméterrel. Előfordulhat például, hogy a lekérdezés tartalmazhat `facet=Rating` , például egy `:values` vagy `:sort` lehetőséggel, hogy tovább pontosítsa a bemutatót.
2. A megjelenítési réteg olyan keresési oldalt jelenít meg, amely sokoldalú navigálást tesz lehetővé a kérésben megadott aspektusok használatával.
3. A minősítést tartalmazó, sokoldalú navigációs struktúra esetén a "4" gombra kattintva jelezheti, hogy csak a 4-es vagy magasabb minősítésű termékek jelennek meg. 
4. Válaszként az alkalmazás egy lekérdezést küld, amely tartalmazza a következőket: `$filter=Rating ge 4` 
5. A megjelenítési réteg frissíti az oldalt, amely egy csökkentett eredményhalmazt mutat, amely csak azokat az elemeket tartalmazza, amelyek megfelelnek az új feltételeknek (ebben az esetben a 4. és az összes termék).

A dimenzió egy lekérdezési paraméter, de nem tévesztendő össze a lekérdezési bevitelsel. A lekérdezésben soha nem használjuk kiválasztási feltételként. Ehelyett a dimenzió lekérdezési paramétereit a válaszban visszakapott navigációs struktúra bemenetként kell megtekintenie. Az Azure Cognitive Search kiértékeli, hogy az egyes dimenziós lekérdezési paraméterek közül hány dokumentum szerepel az egyes dimenziós értékek részleges eredményei között.

Figyelje `$filter` meg a 4. lépését. A szűrő a sokoldalú Navigálás fontos aspektusa. Bár a dimenziók és a szűrők függetlenek az API-ban, mindkét esetben a kívánt élményt kell biztosítania. 

### <a name="app-design-pattern"></a>Alkalmazás kialakítási mintája

Az alkalmazás kódjában a minta a dimenzió lekérdezési paramétereit használja a csiszolt navigációs struktúra, valamint a dimenziós eredmények, valamint egy $filter kifejezés visszaadására.  A szűrési kifejezés kezeli a Click eseményt a Face értéken. Gondolja át, hogy a `$filter` kifejezés a megjelenítési rétegbe visszaadott keresési eredmények tényleges vágása mögötti kód. A színek aspektusa, a vörös színre való kattintás olyan kifejezésen keresztül történik, `$filter` amely csak a piros színnel jelölt elemeket jelöli ki. 

### <a name="query-basics"></a>Lekérdezés alapjai

Az Azure Cognitive Searchban a kérelem egy vagy több lekérdezési paraméteren keresztül van megadva (lásd a [dokumentumok keresése](/rest/api/searchservice/Search-Documents) a leíráshoz című témakört). A lekérdezési paraméterek egyike sem szükséges, de legalább egyet meg kell adni ahhoz, hogy egy lekérdezés érvényes legyen.

A nem releváns találatok kiszűrésének lehetősége a következő kifejezések egyikén vagy mindkettőn keresztül érhető el:

-   **Keresés =**  
    A paraméter értéke képezi a keresési kifejezést. Lehet, hogy egyetlen szövegrész vagy egy összetett keresési kifejezés, amely több kifejezést és operátort is tartalmaz. A kiszolgálón a teljes szöveges kereséshez keresési kifejezés használható, kereshető mezők lekérdezése az indexben a megfelelő feltételekhez, az eredmények rangsorban való visszaadása. Ha `search` NULL értékűre van állítva, a lekérdezés végrehajtása a teljes indexnél (azaz `search=*` ) történik. Ebben az esetben a lekérdezés más elemei, például egy `$filter` vagy pontozási profil a visszaadott dokumentumokat érintő elsődleges tényezők, `($filter` és milyen sorrendben ( `scoringProfile` vagy `$orderby` ).

-   **$filter =**  
    A szűrők hatékony mechanizmust biztosítanak a keresési eredmények méretének korlátozására az adott dokumentum attribútumainak értékei alapján. A `$filter` kiértékelése először történik, majd az egyes értékekhez tartozó, a rendelkezésre álló értékeket és a hozzájuk tartozó számadatokat generáló aspektusi logika.

Az összetett keresési kifejezések csökkentik a lekérdezés teljesítményét. Ha lehetséges, használjon jól felépített szűrési kifejezéseket a pontosság növeléséhez és a lekérdezési teljesítmény javításához.

Ha szeretné jobban megismerni, hogy egy szűrő nagyobb pontosságot ad hozzá, hasonlítson össze egy összetett keresési kifejezéssel, amely egy szűrési kifejezést tartalmaz:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq 'Seattle' and Parking and Type ne 'motel'`

Mindkét lekérdezés érvényes, de a második jobb, ha nem moteleket keres a Seattle-ben.
-   Az első lekérdezés azon konkrét szavakra támaszkodik, amelyek nem szerepelnek a karakterlánc mezőiben, például a név, a leírás és bármely más olyan mező, amely kereshető adat.
-   A második lekérdezés a strukturált adatmennyiség pontos egyezését keresi, és valószínűleg sokkal pontosabb lesz.

A sokoldalú navigációt tartalmazó alkalmazásokban ügyeljen arra, hogy az egyes felhasználói műveletek egy sokoldalú navigációs struktúrában legyenek a keresési eredmények szűkítéséhez. Az eredmények szűkítéséhez használjon egy szűrési kifejezést.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Sokoldalú navigációs alkalmazás készítése
A keresési kérést felépítő alkalmazás kódjában az Azure Cognitive Search felületes navigálást hajt végre. A sokoldalú Navigálás a séma azon elemein alapul, amelyeket a korábban definiált.

A keresési indexben előre definiált `Facetable [true|false]` index attribútum a kiválasztott mezőkre van beállítva, hogy az engedélyezze vagy tiltsa le a használatukat egy sokoldalú navigációs struktúrában. `"Facetable" = true`A nélkül nem használható mező a dimenziós navigálásban.

A kódban a megjelenítési réteg biztosítja a felhasználói élményt. Tartalmaznia kell a csiszolt navigációs elemek, például a felirat, az értékek, a jelölőnégyzetek és a darabszám alkotóelemeit. Az Azure Cognitive Search REST API a platform agnosztikus, ezért a kívánt nyelvet és platformot kell használnia. A lényeg a növekményes frissítést támogató felhasználói felületi elemek belefoglalása, a felhasználói felület frissített állapota, mivel minden további aspektus ki van választva. 

A lekérdezés időpontjában az alkalmazás kódja létrehoz egy kérelmet, amely tartalmaz `facet=[string]` egy olyan kérési paramétert, amely a mezőt a dimenzióhoz adja. Egy lekérdezésnek több aspektusa is lehet, például a `&facet=color&facet=category&facet=rating` , amelyek mindegyike egy jel (&) karakterrel elválasztható.

Az alkalmazás kódjának meg kell adnia egy `$filter` kifejezést is, amely kezeli a kattintási eseményeket a sokoldalú navigálásban. A `$filter` csökkenti a keresési eredményeket, a dimenzió értékének szűrési feltételként való használatával.

Az Azure Cognitive Search a keresési eredményeket egy vagy több megadott kifejezés alapján adja vissza, a sokoldalú navigációs struktúra frissítéseivel együtt. Az Azure Cognitive Search-ban a csiszolatlan navigáció egy egyszintű kialakítás, amely a dimenzió értékeit tartalmazza, és megszámolja, hány eredményt talál.

A következő szakaszokban alaposabban megvizsgáljuk, hogyan hozhat létre egyes részeket.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Az index összeállítása
A Tagolás az index ezen index attribútumán keresztül, a mező szerint mező alapján van engedélyezve: `"Facetable": true` .  
Alapértelmezés szerint az összes olyan mezőtípus, amely felhasználható a sokoldalú navigálásban `Facetable` . Ilyen típusú mezők például `Edm.String` `Edm.DateTimeOffset` a következők:, és az összes numerikus mezőtípus (lényegében az összes mezőtípus látható, kivéve a `Edm.GeographyPoint` nem használható, sokoldalú navigálásban). 

Az indexek létrehozásakor az ajánlott eljárás a sokoldalú navigáláshoz, ha explicit módon kikapcsolja azokat a mezőket, amelyeket soha nem szabad dimenzióként használni.  Az egyedi értékek (például AZONOSÍTÓk vagy Terméknév) karakterlánc-mezőinek beállításával megakadályozható, hogy a `"Facetable": false` véletlen (és nem hatékony) használatba kerüljön a csiszolatlan navigálás. Ha nincs szüksége a méretezésre, nem kell megtartania az index méretét, és általában javítja a teljesítményt.

A következő része a feladatütemezés bemutató minta alkalmazásának sémája, néhány attribútum kivágása a méret csökkentése érdekében:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { "name": "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { "name": "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { "name": "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Ahogy a minta sémában látható, `Facetable` ki van kapcsolva a sztring mezőkhöz, amelyek nem használhatók dimenzióként, például azonosító értékként. Ha nincs szüksége a méretezésre, nem kell megtartania az index méretét, és általában javítja a teljesítményt.

> [!TIP]
> Ajánlott eljárásként adja meg az összes mező index-attribútumának teljes készletét. Bár a `Facetable` alapértelmezés szerint majdnem az összes mezőhöz be van kapcsolva, az egyes attribútumok szándékosan való beállítása segíthet az egyes sémák megdöntésének következményein. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Az adatkeresés
Az adatok minősége közvetlen hatással van arra, hogy a sokoldalú navigációs struktúra megvalósul-e a várt módon. Emellett hatással van a szűrők egyszerű létrehozására is, hogy csökkentse az eredményhalmaz mennyiségét.

Ha a dimenziót a márka vagy az ár alapján kívánja használni, akkor minden dokumentumnak tartalmaznia kell a *BrandName* és a *ProductPrice* érvényes, konzisztens és hatékony szűrési lehetőséget.

Íme néhány emlékeztető arról, hogy mi a bozót a következőhöz:

* Minden olyan mező esetében, amelynek a dimenzióját meg szeretné jeleníteni, megkérdezheti, hogy tartalmaz-e olyan értékeket, amelyek szűrőként használhatók saját irányított keresésben. Az értékeknek rövidnek, leírónak és megfelelő megkülönböztetőnek kell lenniük, hogy a versengő lehetőségek között egyértelmű választást nyújtsanak.
* Helytelenül írt vagy majdnem egyező értékeket tartalmaznak. Ha a szín dimenzióban van, és a mezőértékek közé tartozik a narancssárga és a ornage (a hibás helyesírás), akkor a színmező alapján a két dimenzió is felveszi mindkét értéket.
* A kevert eset szövege is kitarthat egy kis-és nagybetűs navigálást, a narancssárga és a narancssárga két különböző értékként jelenik meg. 
* Az azonos értékkel rendelkező önálló és a többes számú verziók egy külön aspektust is eredményezhetnek.

Az is elképzelhető, hogy az adatelőkészítési gondosság a hatékony navigációs elemek alapvető aspektusa.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>A felhasználói felület létrehozása
A bemutató rétegből való munkavégzés segítséget nyújt az esetlegesen kihagyott követelmények felfedéséhez, és megtudhatja, hogy mely képességek elengedhetetlenek a keresési élményhez.

A sokoldalú navigáció szempontjából a web vagy az alkalmazás oldal megjeleníti a sokoldalú navigációs struktúrát, észleli a felhasználói adatokat a lapon, és beszúrja a módosított elemeket. 

A webalkalmazások esetében az AJAX általában a bemutató rétegben használatos, mivel lehetővé teszi a növekményes módosítások frissítését. Használhatja a ASP.NET MVC-t vagy bármely más vizualizációs platformot is, amely képes HTTP-n keresztül kapcsolódni egy Azure Cognitive Search szolgáltatáshoz. A jelen cikkben hivatkozott minta alkalmazás – az **Azure Cognitive Search Job Portal bemutatója** – egy ASP.net MVC-alkalmazás.

A példában a csiszolt navigáció a keresési eredmények oldalon van beépítve. A `index.cshtml` minta alkalmazás fájlja alapján a következő példa a statikus HTML-struktúra megjelenítését mutatja be a keresési eredmények oldalon. A metszetek listája dinamikusan lett létrehozva vagy újraépítve, amikor beküld egy keresési kifejezést, vagy kijelöl vagy töröl egy dimenziót.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

Az oldal következő kódrészlete `index.cshtml` dinamikusan létrehozza a HTML-t az első aspektus, az üzleti cím megjelenítéséhez. A hasonló függvények dinamikusan felépítik a HTML-ket a többi aspektushoz. Az egyes dimenziók címkével és számmal rendelkeznek, amely megjeleníti az adott aspektus eredményéhez tartozó elemek számát.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> A keresési eredmények oldal tervezésekor ne felejtsen el felvenni egy mechanizmust a dimenziók törléséhez. Ha jelölőnégyzeteket ad hozzá, egyszerűen megtekintheti a szűrők törlésének módját. Más elrendezésekhez szükség lehet egy morzsa-mintára vagy egy másik kreatív megközelítésre. Például a feladatok keresési portál minta alkalmazásban kattintson a `[X]` kijelölt aspektus után lehetőségre a dimenzió törléséhez.

<a name="buildquery"></a>

## <a name="build-the-query"></a>A lekérdezés összeállítása
A lekérdezések létrehozásához írt kódnak meg kell adnia egy érvényes lekérdezés összes részét, beleértve a keresési kifejezéseket, a dimenziókat, a szűrőket, a pontozási profilokat – bármit, ami a kérés összeállításához használatos. Ebben a szakaszban azt vizsgáljuk meg, hogy az egyes dimenziók hogyan illeszkednek egy lekérdezésbe, és hogyan használhatók a szűrők a dimenziókkal a csökkentett eredményhalmaz továbbításához.

Figyelje meg, hogy az aspektusok szerves részét képezik ebben a minta alkalmazásban. A Job Portal-bemutató keresési felülete a navigációs és a szűrési feladatok köré épül. Az oldalon a sokoldalú Navigálás Kiemelt elhelyezése mutatja be a jelentőségét. 

Egy példa gyakran jó kiindulópont. A fájlból származó következő példa `JobsSearch.cs` egy olyan kérést hoz létre, amely az üzleti cím, a hely, a feladás típusa és a minimális fizetés alapján létrehoz egy dimenziós navigálást. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Egy dimenzióérték-lekérdezési paraméter egy mezőre van beállítva, és az adattípustól függően további paramétert adhat meg a vesszővel tagolt lista, amely a következőket tartalmazza:,, `count:<integer>` `sort:<>` `interval:<integer>` és `values:<list>` . A tartományok beállításakor az értékek listája a numerikus adatok esetében támogatott. A használat részleteiért tekintse meg a [dokumentumok keresése (Azure Cognitive Search API)](/rest/api/searchservice/Search-Documents) című témakört.

Az egyes aspektusokkal együtt az alkalmazás által létrehozott kérelemnek olyan szűrőket is kell kiépítenie, amelyekkel szűkítheti a jelölt dokumentumok készletét egy adott dimenzió érték kiválasztása alapján. A bike Store-ban a sokoldalú Navigálás olyan kérdéseket *tesz elérhetővé, mint a színek, a gyártók és a különböző típusú kerékpárok típusai?*. A szűrés olyan kérdésekre ad választ, mint a *pontos kerékpárok piros, Mountain Bikes, ebben az árakban?*. Ha a "vörös" gombra kattint, hogy csak a vörös termékek jelenjenek meg, az alkalmazás által küldött következő lekérdezés tartalmazza a-t `$filter=Color eq 'Red'` .

Az oldal következő kódrészlete `JobsSearch.cs` hozzáadja a kiválasztott üzleti címet a szűrőhöz, ha az üzleti cím dimenzióból kiválaszt egy értéket.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tippek és ajánlott eljárások

### <a name="indexing-tips"></a>Indexelési tippek
**Az indexelés hatékonyságának növelése, ha nem használ keresőmezőt**

Ha az alkalmazás csak a sokoldalú navigációt használja (azaz nincs keresőmező), akkor a mezőt megjelölheti úgy `searchable=false` , `facetable=true` hogy egy kompakt indexet hozzon létre. Az indexelés továbbá csak az egész dimenziós értékeken fordul elő, a többszavas értékek összetevőinek nem megfelelő szövegének vagy indexelésének megadásával.

**Annak meghatározása, hogy mely mezők használhatók dimenzióként**

Ne felejtse el, hogy az index sémája határozza meg, hogy mely mezők használhatók dimenzióként. Feltételezve, hogy egy mező nem látható, a lekérdezés meghatározza, hogy mely mezők legyenek a dimenzióra. Az a mező, amellyel az aspektusa látható, a címke alatt megjelenő értékeket adja meg. 

Az egyes címkék alatt megjelenő értékeket az indexből kéri le a rendszer. Ha például a dimenzió mező *színe*, a további szűréshez elérhető értékek az adott mezőhöz tartozó értékek: vörös, fekete és így tovább.

Csak numerikus és DateTime érték esetén explicit módon adhat meg értékeket a dimenzió mezőben (például: `facet=Rating,values:1|2|3|4|5` ). Ezeknél a típusú mezőknél az értékek listája leegyszerűsítheti a dimenziók eredményeinek folytonos tartományba való elkülönítését (a numerikus értékek vagy időszakok alapján megadható tartományok). 

**Alapértelmezés szerint csak egy szinttel rendelkező navigáció lehet** 

Ahogy azt említettük, nincs közvetlen támogatás az aspektusok hierarchiában való beágyazásához. Alapértelmezés szerint az Azure Cognitive Search-alapú Navigálás csak egy szintű szűrőt támogat. A megkerülő megoldások azonban léteznek. Hierarchiában egy belépési ponttal is kódolhat hierarchikus dimenziós struktúrát `Collection(Edm.String)` . Ennek a megkerülő megoldásnak a megvalósítása meghaladja a jelen cikk hatókörét. 

### <a name="querying-tips"></a>Tippek lekérdezése
**Mezők érvényesítése**

Ha a dimenziók listáját dinamikusan hozza létre a nem megbízható felhasználói bevitel alapján, ellenőrizze, hogy érvényesek-e a csiszolt mezők nevei. Az URL-címek kiépítésekor a `Uri.EscapeDataString()` .net-ben vagy a megfelelő platformon is megadhatja a neveket.

### <a name="filtering-tips"></a>Szűrési tippek
**A keresési pontosság fokozása szűrőkkel**

Szűrők használata. Ha csak a keresési kifejezéseket használja, a kivezetés olyan dokumentum visszaadását eredményezheti, amely nem rendelkezik a pontos dimenzió értékkel a saját mezőiben.

**Keresési teljesítmény fokozása szűrőkkel**

A szűrők leszűkítik a jelölt dokumentumok készletét a kereséshez, és kizárják őket a rangsorolásból. Ha nagy mennyiségű dokumentumot használ, a szelektív aspektusok részletezése gyakran jobb teljesítményt nyújt.
  
**Csak a csiszolatlan mezők szűrése**

A részletes részletezés során általában csak olyan dokumentumokat szeretne szerepeltetni, amelyek egy adott (csiszolt) mezőben található dimenzióérték-értékkel rendelkeznek, és nem minden kereshető mező között. A szűrő hozzáadásával erősíti meg a célként megadott mezőt úgy, hogy a szolgáltatást csak a csiszolatlan mezőben keresi a megfelelő értékre.

**Aspektusok eredményeinek további szűrőkkel való kivágása**

A dimenziós eredmények olyan keresési eredményekben található dokumentumok, amelyek megfelelnek egy aspektus kifejezésének. A következő példában a *felhőalapú számítástechnika* keresési eredményeiben a 254-es elemek *belső specifikációval* is rendelkeznek, mint a tartalom típusa. Az elemek nem feltétlenül kölcsönösen kizárják egymást. Ha egy elem megfelel mindkét szűrő feltételeinek, akkor a rendszer minden egyesnek számít. Ez az ismétlődés akkor lehetséges, ha a `Collection(Edm.String)` mezőkre van szükség, amelyek gyakran a dokumentumok címkézésének megvalósítására használatosak.

```output
Search term: "cloud computing"
Content type
   Internal specification (254)
   Video (10)
```

Általánosságban elmondható, hogy ha úgy találja, hogy a dimenziók eredményei túl nagyok, javasoljuk, hogy adjon hozzá további szűrőket, hogy a felhasználók további lehetőségeket adjanak a keresés szűkítéséhez.

### <a name="tips-about-result-count"></a>Az eredmények számával kapcsolatos tippek

**Az elemek számának korlátozása a dimenziós navigációban**

A navigációs fa minden egyes csiszolt mezőjénél van egy alapértelmezett korlát 10 értéknél. Ez az alapértelmezett beállítás a navigációs struktúrákra vonatkozik, mivel az értékek listáját kezelhető méretre tartja. Az alapértelmezett érték felülbírálásához rendeljen hozzá egy értéket.

* `&facet=city,count:5` azt határozza meg, hogy csak a legfelső rangsorolt eredményekben található első öt város lesz visszaadva. Vegyünk példaként egy "repülőtér" keresési kifejezéssel és 32-es egyezéssel rendelkező mintát. Ha a lekérdezés azt adja meg `&facet=city,count:5` , csak a keresési eredményekben a legtöbb dokumentumot tartalmazó első öt egyedi város szerepel a dimenzió eredményei között.

Figyelje meg, hogy különbséget tesz a dimenzió eredményei és a keresési eredmények között. A keresési eredmények az összes olyan dokumentum, amely megfelel a lekérdezésnek. A dimenziók eredményei az egyes dimenziók értékeinek felelnek meg. A példában a keresési eredmények közé tartoznak azok a városok neve, amelyek nem szerepelnek a Face besorolási listán (5 a példánkban). A sokoldalú Navigálás során kiszűrt eredmények láthatóvá válnak, ha törli a dimenziókat, vagy más aspektusokat választ a város mellett. 

> [!NOTE]
> Megbeszélheti `count` , ha több típus is zavaró lehet. Az alábbi táblázat röviden összefoglalja, hogyan használható a kifejezés az Azure Cognitive Search API-ban, a mintakódben és a dokumentációban. 

* `@colorFacet.count`<br/>
  A megjelenítési kódban a Faces paramétert kell látni a dimenzióban, amelyet a rendszer a Faces eredmények számának megjelenítésére használ. A dimenzió eredményei között a Count (dimenzió) érték jelzi, hogy hány dokumentum felel meg az adott aspektusnak vagy tartománynak.
* `&facet=City,count:12`<br/>
  Egy dimenzióérték-lekérdezésben megadhatja a Count értéket.  Az alapértelmezett érték 10, de magasabb vagy alacsonyabb értéket is beállíthat. `count:12`A beállítás az első 12 egyezést jeleníti meg a dimenzió eredményei között a dokumentumok száma alapján.
* "`@odata.count`"<br/>
  A lekérdezési válaszban ez az érték a keresési eredményekben szereplő egyező elemek számát jelzi. Átlagosan ez nagyobb, mint az összes aspektusi eredmény összesített összege, a keresési kifejezésnek megfelelő elemek jelenléte miatt, de a dimenzió értéke nem egyezik.

**Számok beolvasása a dimenzió eredményei között**

Ha szűrőket ad hozzá egy sokoldalú lekérdezéshez, érdemes megtartania a Face utasítást (például: `facet=Rating&$filter=Rating ge 4` ). Technikailag, dimenzió = minősítés nem szükséges, de a megtartás után a 4. vagy magasabb minősítések dimenzió értékeinek számát adja vissza. Ha például a "4" elemre kattint, és a lekérdezés a "4" értéknél nagyobb vagy egyenlő szűrőt tartalmaz, a rendszer az egyes 4 és annál nagyobb minősítések számát adja vissza.  

**Győződjön meg róla, hogy pontos dimenziók száma**

Bizonyos körülmények között előfordulhat, hogy a dimenziók száma nem egyezik meg az eredményhalmaz értékével (lásd: részletes [Navigálás az Azure Cognitive Search (Microsoft Q&a kérdéses oldalon)](/answers/topics/azure-cognitive-search.html)).

A metszeti architektúra miatt pontatlan lehet a dimenziók száma. Minden keresési indexnek több szegmense van, és az egyes szegmensek az első N aspektust jelentik a dokumentumok száma alapján, amelyet aztán egyetlen eredménybe egyesít. Ha egyes szegmensek több egyező értékkel rendelkeznek, míg mások kevesebbet tartalmaznak, előfordulhat, hogy bizonyos aspektusok hiányoznak, vagy az eredmények között szerepelnek.

Bár ez a viselkedés bármikor megváltozhat, ha ez a viselkedés még ma tapasztalható, a darabszám mesterséges feltöltésével: \<number> nagy mennyiségre kikényszerítheti az egyes szegmensek teljes jelentéskészítését. Ha a Count érték értéke: nagyobb vagy egyenlő, mint a mezőben szereplő egyedi értékek száma, akkor a pontos eredmények garantáltak. Ha azonban a dokumentumok száma magas, akkor teljesítménybeli szankció van, ezért ezt a beállítást megfontoltan kell használni.

### <a name="user-interface-tips"></a>Felhasználói felülettel kapcsolatos tippek
**Címkék hozzáadása az egyes mezőkhöz a dimenziós navigációban**

A címkék általában a HTML-ben vagy az űrlapon vannak definiálva ( `index.cshtml` a minta alkalmazásban). Nincs olyan API az Azure Cognitive Searchban, amely dimenziós navigációs címkéket vagy egyéb metaadatokat tartalmaz.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Szűrés tartomány alapján
Az értékek tartományán alapuló aspektus egy gyakori keresési alkalmazásra vonatkozó követelmény. A tartományok numerikus adatok és DateTime értékek esetén támogatottak. További információt a [keresési dokumentumok (Azure Cognitive Search API)](/rest/api/searchservice/Search-Documents)egyes módszereiről itt olvashat.

Az Azure Cognitive Search egyszerűsíti a tartomány-kialakítást azáltal, hogy két módszert biztosít a különböző számítási felépítéshez. Mindkét megközelítés esetében az Azure Cognitive Search létrehozza a megfelelő tartományokat a megadott bemenetek alapján. Ha például 10 | 20 | 30 tartomány értéket ad meg, akkor a automatikusan 0-10, 10-20, 20-30 tartományt hoz létre. Az alkalmazás opcionálisan eltávolíthatja az összes olyan intervallumot, amely üres. 

**1. módszer: az intervallum paraméter használata**  
Az $10-es számú érték megadásához a következőket kell megadnia: `&facet=price,interval:10`

**2. módszer: értéklista használata**  
Numerikus adatok esetében az értékek listáját használhatja.  Vegye fontolóra egy mező dimenzióérték-tartományát, amely a `listPrice` következőképpen jelenik meg:

  ![Sample Values lista](media/search-faceted-navigation/Facet-5-Prices.PNG "Sample Values lista")

Ha meg szeretné adni az előző képernyőképen látható egyik dimenziót, használja az értékek listát:

> `facet=listPrice,values:10|25|100|500|1000|2500`

Az egyes tartományok kiindulási pontként, a listában szereplő értékekkel és végpontként, majd az előző tartományból kivágással, különálló időközök létrehozásához lettek létrehozva. Az Azure Cognitive Search a sokoldalú Navigálás részeként teszi ezeket a dolgokat. Az egyes intervallumok strukturálásához nem kell kódot írnia.

### <a name="build-a-filter-for-a-range"></a>Szűrő létrehozása tartományhoz
A dokumentumok a kiválasztott tartomány alapján történő szűréséhez használhatja a `"ge"` és a `"lt"` Filter operátort egy kétrészes kifejezésben, amely meghatározza a tartomány végpontját. Ha például egy mezőhöz a 10-25 tartományt választja `listPrice` , a szűrő a következő lesz: `$filter=listPrice ge 10 and listPrice lt 25` . A mintakód **priceFrom** és **priceTo** paramétereket használ a végpontok beállításához. 

  ![Lekérdezés számos értékhez](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Lekérdezés számos értékhez")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Szűrés távolság alapján
Gyakori, hogy olyan szűrőket lát, amelyek segítenek az áruház, az étterem vagy a célhely kiválasztásában az aktuális tartózkodási hely közelsége alapján. Habár az ilyen típusú szűrők kitűnnek a kicsiszolt navigációhoz, csak egy szűrő lehet. Itt megemlítjük azokat a felhasználókat, akik kifejezetten az adott tervezési problémára vonatkozó megvalósítási tanácsokat keresik.

Két térinformatikai függvény van az Azure Cognitive Search, a **Geo. Distance** és a **Geo. metszetekben**.

* A **Geo. Distance** függvény két pont közötti távolságot adja vissza kilométerben. Az egyik pont egy mező, a másik pedig a szűrő részeként kapott állandó. 
* A **Geo. metszetek** függvény Igaz értéket ad vissza, ha egy adott pont egy adott sokszögen belül van. A pont egy mező, a sokszög pedig a szűrő részeként átadott koordináták állandó listájának van megadva.

A szűrési példákat a [OData kifejezés szintaxisában (Azure Cognitive Search)](query-odata-filter-orderby-syntax.md)találja.

<a name="tryitout"></a>

## <a name="try-the-demo"></a>A bemutató kipróbálása
Az Azure Cognitive Search Job Portal bemutatója a cikkben hivatkozott példákat tartalmazza.

-   Tekintse meg és tesztelje a munkahelyi bemutatót az [Azure Cognitive Search Job Portal bemutatójában](https://aka.ms/azjobsdemo).

-   Töltse le a kódot az [Azure-Samples repóból a githubon](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Amikor a keresési eredményekkel dolgozik, tekintse meg az URL-címet a lekérdezés-kialakítás változásairól. Ez az alkalmazás úgy működik, hogy az egyes elemekhez tartozó dimenziókat hozzáfűzi az URI-hoz.

1. A bemutató alkalmazás leképezési funkciójának használatához szerezze be a Bing Maps-kulcsot a [Bing Maps fejlesztői központjából](https://www.bingmapsportal.com/). Illessze be a lap meglévő kulcsára `index.cshtml` . A `BingApiKey` `Web.config` fájl beállítása nincs használatban. 

2. Futtassa az alkalmazást. Tegye meg a választható bemutatót, vagy zárja be a párbeszédpanelt.
   
3. Írjon be egy keresési kifejezést, például "elemző", majd kattintson a keresés ikonra. A lekérdezés végrehajtása gyorsan elvégezhető.
   
   A keresési eredményekkel egy sokoldalú navigációs struktúra is visszakerül. A keresési eredmény lapon a sokoldalú navigációs struktúra az egyes aspektusok eredményét tartalmazza. Nincsenek kiválasztva dimenziók, így az összes egyező eredményt adja vissza.
   
   ![Keresési eredmények az aspektusok kiválasztása előtt](media/search-faceted-navigation/faceted-search-before-facets.png "Keresési eredmények az aspektusok kiválasztása előtt")

4. Kattintson az üzleti címre, a helyszínre vagy a minimális fizetésre. A kezdeti keresési dimenziók null értékűek voltak, de az értékekre való betöltéskor a keresési eredmények a már nem egyező elemek kivágására szolgálnak.
   
   ![Keresési eredmények az aspektusok kiválasztása után](media/search-faceted-navigation/faceted-search-after-facets.png "Keresési eredmények az aspektusok kiválasztása után")

5. A sokoldalú lekérdezés törléséhez, hogy kipróbáljon különböző lekérdezési viselkedéseket, kattintson a `[X]` kijelölt aspektusok után a dimenziók törléséhez.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Tudjon meg többet
Tekintse meg az [Azure Cognitive Search a Deep Dive](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410)szolgáltatást. A 45:25-es verzióban egy bemutató mutatja be, hogyan valósíthatók meg a dimenziók.

A részletes Navigálás tervezési alapelveivel kapcsolatos további információk a következő hivatkozásokat ajánljuk:

* [Tervezési minták: csiszolt navigáció](https://alistapart.com/article/design-patterns-faceted-navigation)
* [A részletes keresés megvalósítása során felmerülő előzetesek – 1. rész](https://articles.uie.com/faceted_search2/)