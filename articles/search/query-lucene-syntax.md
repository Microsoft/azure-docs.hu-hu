---
title: Lucene lekérdezési szintaxis
titleSuffix: Azure Cognitive Search
description: A teljes Lucene lekérdezési szintaxisra vonatkozó hivatkozás, amelyet az Azure Cognitive Search használhat helyettesítő karakterek, fuzzy keresés, RegEx és egyéb speciális lekérdezési szerkezetek esetében.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: fc3662d8198e6ab6ab215ac1e9e8eac585f4250b
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801587"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Lucene lekérdezési szintaxis az Azure Cognitive Search

Lekérdezések létrehozásakor választhatja a [Lucene lekérdezés-elemző](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) szintaxisát speciális lekérdezési űrlapokhoz: helyettesítő karakterekkel, fuzzy kereséssel, közelségi kereséssel, reguláris kifejezésekkel. A Lucene lekérdezés-elemző szintaxisának nagy része [érintetlenül van megvalósítva az Azure Cognitive Searchban](search-lucene-query-architecture.md), a kifejezéseken alapuló *keresési tartományok* kivételével **`$filter`** . 

A teljes Lucene szintaxis használatához a queryType "Full" értékre kell állítani, és a helyettesítő karakterrel, a zavaros kereséssel vagy a teljes szintaxis által támogatott egyéb lekérdezési űrlapokkal kell átadni. A REST-ben a lekérdezési kifejezések a **`search`** [keresési dokumentumok (REST API)](/rest/api/searchservice/search-documents) kérelem paraméterében találhatók.

## <a name="example-full-syntax"></a>Példa (teljes szintaxis)

A következő példa egy, a teljes szintaxissal létrehozott keresési kérelem. Ez az adott példa a helyszíni keresés és a kifejezés növelését mutatja be. Olyan szállodákat keres, ahol a Kategória mező tartalmazza a "költségvetés" kifejezést. A "legutóbb felújított" kifejezést tartalmazó dokumentumok magasabbra vannak rangsorolva a "Boost Value" (3) kifejezés eredményeképpen.  

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
  "queryType": "full",
  "search": "category:budget AND \"recently renovated\"^3",
  "searchMode": "all"
}
```

Habár a lekérdezési típusokra nem vonatkozik, a **`searchMode`** paraméter releváns ebben a példában. Ha a kezelők a lekérdezésben vannak, általában úgy kell beállítani, hogy az `searchMode=all` *összes* feltételt egyeztetni lehessen.  

További Példákért lásd: [Lucene-lekérdezés szintaxisának példái](search-query-lucene-examples.md). A lekérdezési kérelemről és paraméterekről, beleértve a searchMode is, lásd: [dokumentumok keresése (REST API)](/rest/api/searchservice/Search-Documents).

## <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> Szintaxis alapjai  

A következő szintaxis a Lucene szintaxist használó összes lekérdezésre vonatkozik.  

### <a name="operator-evaluation-in-context"></a>Operátor kiértékelése kontextusban

Az elhelyezés meghatározza, hogy egy szimbólum operátorként vagy csak egy sztringben legyen értelmezve.

A Lucene teljes szintaxisa például a tilde (~) a fuzzy kereséshez és a közelségi kereséshez használatos. Idézett kifejezés után a ~ meghívja a közelségi keresést. A kifejezés végén a ~ meghívja a zavaros keresést.

Egy kifejezésen belül, például "Business ~ Analysis", a karakter nem lesz kiértékelve operátorként. Ebben az esetben, ha a lekérdezés egy kifejezés vagy kifejezés lekérdezése, a [teljes szöveges keresés](search-lucene-query-architecture.md) a [lexikális analízissel](search-lucene-query-architecture.md#stage-2-lexical-analysis) a ~ és a "Business ~ Analysis" kifejezést a következő két: üzleti vagy elemző.

A fenti példa a tilde (~), de ugyanez az elv minden operátorra érvényes.

### <a name="escaping-special-characters"></a>Speciális karakterek megmenekülése

Ha a keresési operátorok valamelyikét a keresett szöveg részeként szeretné használni, akkor a karaktert egyetlen fordított perjel () előtaggal kell elmenekülnie `\` . Például a helyettesítő karakteres kereséshez `https://` , ahol a a `://` lekérdezési karakterlánc részét képezi, a következőt kell megadnia: `search=https\:\/\/*` . Hasonlóképpen, egy Escape-telefonszám mintázata is így néz ki `\+1 \(800\) 642\-7676` .

A Escape-t igénylő speciális karakterek közé a következők tartoznak:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> Bár a Escapes megtartja a tokeneket, az indexelés során felmerülő [lexikális elemzések](search-lucene-query-architecture.md#stage-2-lexical-analysis) is felmerülhetnek. A standard Lucene Analyzer például megszakítja a szavakat a kötőjelek, a szóközök és más karakterek esetén. Ha speciális karaktereket szeretne megadni a lekérdezési karakterláncban, előfordulhat, hogy olyan elemzőre van szüksége, amely megőrzi őket az indexben. Bizonyos lehetőségek közé tartoznak a Microsoft természetes [nyelvi elemzők](index-add-language-analyzers.md), amelyek megőrzik az elválasztott szavakat, vagy egy egyéni elemzőt összetettebb mintákhoz. További információ: [részleges feltételek, mintázatok és speciális karakterek](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Nem biztonságos és fenntartott karakterek kódolása az URL-címekben

Győződjön meg arról, hogy az összes nem biztonságos és fenntartott karakter egy URL-címben van kódolva. Például a "#" egy nem biztonságos karakter, mert egy URL-címben szereplő töredék/horgony azonosító. A karaktert kódolni kell, `%23` Ha az URL-címben van használatban. A (z) "&" és a "=" például fenntartott karakterek, amelyek a paramétereket korlátozzák, és értékeket határoznak meg az Azure Cognitive Searchban. További részletekért tekintse meg a [RFC1738: egységes erőforrás-lokátorok (URL)](https://www.ietf.org/rfc/rfc1738.txt) című témakört.

A nem biztonságos karakterek a következők: ``" ` < > # % { } | \ ^ ~ [ ]`` . A fenntartott karakterek a következők: `; / ? : @ = + &` .

## <a name="boolean-operators"></a><a name="bkmk_boolean"></a> Logikai operátorok

Egy lekérdezési karakterláncban logikai operátorokat ágyazhat be, így javíthatja a egyezés pontosságát. A teljes szintaxis támogatja a szöveges operátorokat a karakterek mellett. Mindig a logikai operátorokat (és, vagy nem) az összes nagybetű határozza meg.

|Logikai operátor | Karakter | Példa | Használat |
|--------------|----------- |--------|-------|
| ÉS | `&`, `+` | `wifi + luxury` | Meghatározza, hogy a egyezésnek tartalmaznia kell-e a kifejezéseket. A példában a lekérdezési motor megkeresi a és a-t tartalmazó dokumentumokat `wifi` `luxury` . A plusz karaktert ( `+` ) a kötelező feltételekhez használja a rendszer. Például `+wifi +luxury` azt határozza meg, hogy mindkét kifejezésnek valahol az egyetlen dokumentum mezőjében kell megjelennie.|
| OR | `|` | `wifi | luxury` | Megkeres egy egyezést, ha valamelyik kifejezés megtalálható. A példában a lekérdezési motor a vagy a vagy a mindkettőt tartalmazó dokumentumokra vonatkozó egyezést ad vissza `wifi` `luxury` . Mivel a vagy az az alapértelmezett, és ez az operátor, azt is megteheti, hogy `wifi luxury` a megfelelő  `wifi | luxury` .|
| NOT | `!`, `-` | `wifi –luxury` | A kifejezést kizáró dokumentumok egyezéseit adja vissza. Például `wifi –luxury` megkeresi a kifejezéssel rendelkező dokumentumokat, `wifi` de nem `luxury` . <br/><br/>A `searchMode` lekérdezési kérelem paramétere azt szabályozza, hogy a nem operátorral rendelkező kifejezés ANDed vagy ORed-e a lekérdezés más feltételeivel (feltéve, hogy `+` `|` a többi feltétel nem vagy operátor.) Az érvényes értékek a következők: `any` vagy `all` .  <br/><br/>`searchMode=any` a további eredményekkel együtt növeli a lekérdezések visszahívását, és alapértelmezés szerint `-` "vagy nem" lesz értelmezve. Például `wifi -luxury` megegyeznek azok a dokumentumok, amelyek tartalmazzák a kifejezést, `wifi` vagy amelyek nem tartalmazzák a kifejezést `luxury` .  <br/><br/>`searchMode=all` növeli a lekérdezések pontosságát azáltal, hogy kevesebb eredményt ad meg, és alapértelmezés szerint "és nem" lesz értelmezve. Például `wifi -luxury` a a kifejezést tartalmazó dokumentumokat fogja egyeztetni, `wifi` és nem tartalmazza a "luxus" kifejezést. Ez vitathatatlanul intuitív viselkedést biztosít a kezelő számára `-` . Ezért érdemes megfontolni, hogy `searchMode=all` ahelyett `searchMode=any` , hogy visszahívást szeretne használni a pontossághoz, *és* a felhasználók gyakran használják a `-` keresésekben a kezelőt.<br/><br/>Egy beállítás meghatározásakor `searchMode` vegye figyelembe a felhasználói interakciós mintákat a különböző alkalmazásokban található lekérdezésekhez. Azok a felhasználók, akik információt keresnek, nagyobb valószínűséggel tartalmaznak egy operátort egy lekérdezésben, szemben a több beépített navigációs szerkezettel rendelkező e-kereskedelmi webhelyekkel. |

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> Mező szerinti keresés

Megadhat egy mezőhöz tartozó keresési műveletet a `fieldName:searchExpression` szintaxissal, ahol a keresési kifejezés lehet egyetlen szó vagy kifejezés, vagy egy összetettebb kifejezés zárójelben, opcionálisan logikai operátorokkal. Néhány példa a következőkre:  

- Műfaj: nem a jazz története  

- művészek:("Miles Davis" "John Coltrane")

Ügyeljen arra, hogy az idézőjelek között több karakterláncot is helyezzen el, ha azt szeretné, hogy mindkét sztring egyetlen entitásként legyen kiértékelve, ebben az esetben a mezőben két különböző előadó keres `artists` .  

A mezőben megadott mezőnek `fieldName:searchExpression` mezőnek kell lennie `searchable` .  További részletekért tekintse meg az index [létrehozása](/rest/api/searchservice/create-index) című témakört, amely azt ismerteti, hogyan használhatók az index attribútumai.  

> [!NOTE]
> A mezőn alapuló keresési kifejezések használatakor nem kell használnia a `searchFields` paramétert, mert az egyes mezőkhöz tartozó keresési kifejezésekhez explicit módon van megadva a mezőnév. Azonban továbbra is használhatja a `searchFields` paramétert, ha olyan lekérdezést szeretne futtatni, amelyben bizonyos részek hatóköre egy adott mezőre vonatkozik, a REST pedig több mezőre is vonatkozhat. A lekérdezés például `search=genre:jazz NOT history&searchFields=description` `jazz` csak a `genre` mezőnek felel meg, míg `NOT history` a mezőnek egyeznie kell `description` . A mezőben megadott mezőnév `fieldName:searchExpression` elsőbbséget élvez a `searchFields` paraméterrel szemben, ezért ebben a példában nem kell belefoglalni `genre` a `searchFields` paraméterbe.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> Fuzzy keresés

A nehezen megtalált találatok olyan kifejezések, amelyek hasonló szerkezettel rendelkeznek, és legfeljebb legfeljebb 50 olyan kifejezést mutatnak be, amelyek megfelelnek a két vagy annál kisebb távolsági feltételeknek. További információ: [fuzzy keresés](search-query-fuzzy.md).

A fuzzy kereséshez használja a "~" szimbólumot egyetlen szó végén egy opcionális paraméterrel, egy 0 és 2 közötti számot (alapértelmezett), amely megadja a szerkesztési távolságot. Például a "Blue ~" vagy a "Blue ~ 1" a "Blue", a "blues" és a "ragasztó" értéket fogja visszaadni.

A zavaros keresés csak a kifejezésekre alkalmazható, a kifejezésekre nem, de a tilde az egyes kifejezésekhez a több részből álló névvel vagy kifejezéssel is kiegészíthető. Például: "Unviersty ~ of ~" Wshington ~ "megegyeznek a" Washington Egyetem "kifejezéssel.
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> Közelség keresése

A közelségi keresések megkeresik a dokumentumokban egymáshoz közeli kifejezéseket. Szúrjon be egy "~" szimbólumot egy kifejezés végén, majd a közelségi határt létrehozó szavak számát. Például `"hotel airport"~5` megkeresi a "Hotel" és a "repülőtér" kifejezést a dokumentumban lévő 5 szóból.  

##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> A kifejezés fokozása

A kifejezés növelése arra utal, hogy a dokumentum rangsorolása magasabb, ha a megnövelt kifejezést tartalmazza, a kifejezést nem tartalmazó dokumentumokhoz képest. Ez különbözik az adott pontozási profilok pontozási profiljaitól, és nem adott feltételekkel növeli bizonyos mezőket.  

A következő példa segít illusztrálni a különbségeket. Tegyük fel, hogy van egy pontozási profil, amely egy adott mezőben növeli a egyezéseket, például a *műfajt* a  [musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). A kifejezés fokozása felhasználható a másoknál nagyobb keresési kifejezések további növelésére. Például a megjelenő `rock^2 electronic` dokumentumokban a keresési kifejezéseket tartalmazó dokumentumok magasabbak lesznek, mint az index más kereshető mezőinél. A *rock* keresési kifejezést tartalmazó dokumentumok továbbra is magasabbak lesznek *, mint a más keresési kifejezés,* amely a kifejezés értékének (2) kiemelését eredményezi.  

 Egy kifejezés növeléséhez használja a "^" karaktert, a szimbólumot a keresett kifejezés végén (a szám). A kifejezéseket növelheti is. Minél nagyobb a növelési tényező, annál nagyobb a kifejezés, mint a többi keresési kifejezéshez képest. Alapértelmezés szerint a Boost faktor 1. Bár a Boost faktornak pozitívnak kell lennie, kevesebb, mint 1 (például 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> Reguláris kifejezés keresése  
 A reguláris kifejezéses keresés az Apache Lucene alatt érvényes minták alapján keres egyezést a [regexp osztályban](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)dokumentálva. Az Azure Cognitive Search egy reguláris kifejezést továbbít a perjelek közé `/` .

 Ha például a "Motel" vagy a "Hotel" tartalmú dokumentumokat szeretné megkeresni, akkor a következőt kell megadnia: `/[mh]otel/` . A reguláris kifejezésekkel végzett keresések egyetlen szóval vannak összehasonlítva.

Egyes eszközök és nyelvek további Escape-jellegű követelményeket támasztanak. A JSON esetében a perjeleket tartalmazó karakterláncok egy visszafelé perjelet tartalmaznak: "microsoft.com/azure/" `search=/.*microsoft.com\/azure\/.*/` `search=/.* <string-placeholder>.*/` , ahol beállítja a reguláris kifejezést, és `microsoft.com\/azure\/` a sztring Escape-perjelet tartalmaz.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> Helyettesítő karakteres keresés

Az általánosan felismert szintaxis több ( `*` ) vagy szimpla ( `?` ) karakteres helyettesítő karakteres kereséshez használható. A lekérdezés kifejezése például `search=alpha*` "alfanumerikus" vagy "ábécé" értéket ad vissza. Vegye figyelembe, hogy a Lucene-lekérdezés elemzője egyetlen kifejezéssel támogatja a szimbólumok használatát, nem pedig egy kifejezést.

A teljes Lucene szintaxis támogatja az előtagot, a Infix és az utótag megfeleltetését. Ha azonban mindössze egy előtagra van szüksége, használhatja az egyszerű szintaxist (az előtag egyeztetése mindkettőben támogatott).

Az utótag egyeztetése, ahol `*` vagy `?` megelőzi a karakterláncot (például: `search=/.*numeric./` ), vagy a Infix egyeztetéshez teljes Lucene szintaxis szükséges, valamint a reguláris kifejezés továbbít perjel `/` elhatárolók. Nem használhat * vagy? egy kifejezés első karakterének szimbóluma, vagy egy kifejezésen belül, a nélkül `/` . 

> [!NOTE]  
> Szabályként a minta egyeztetése lassú, ezért érdemes lehet olyan alternatív módszereket is felderíteni, mint például a Edge n-Gram jogkivonatok létrehozása, amely jogkivonatokat hoz létre egy kifejezésben szereplő karakterek sorozatához. Az index nagyobb lesz, de előfordulhat, hogy a lekérdezések végrehajtása a minta szerkezettől és az indexelt karakterláncok hosszától függően gyorsabb.
>

### <a name="impact-of-an-analyzer-on-wildcard-queries"></a>Az analizátor helyettesítő lekérdezésekre gyakorolt hatása

A lekérdezések elemzése során előtagként, utótagként, helyettesítő karakterként vagy reguláris kifejezésként megadott lekérdezések a lekérdezési fában lesznek átadva – a [lexikális analízis](search-lucene-query-architecture.md#stage-2-lexical-analysis)megkerülésével. A egyezések csak akkor találhatók meg, ha az index a lekérdezés formátumában megadott karakterláncokat tartalmazza. A legtöbb esetben szüksége lesz egy elemzőre az indexelés során, amely megőrzi a karakterlánc-integritást, hogy a részleges és a mintázat egyeztetése sikeres legyen. További információ: [részleges kifejezéses keresés az Azure Cognitive Search lekérdezésekben](search-query-partial-matching.md).

Vegye fontolóra azt a helyzetet, amikor a "leállítási" keresési lekérdezés a "Leállítás", a "Leállítás" és a "megszakítások" kifejezést tartalmazó találatokat ad vissza.

Ha az en. Lucene (angol Lucene) elemzőt használja, akkor az egyes kifejezések agresszív kivonását kellene alkalmaznia. Például a "Leállítás", a "Leállítás", a "leállítások" az indexben az "Termi" jogkivonat lesz. A másik oldalon a helyettesítő karakterekkel vagy a fuzzy kereséssel végzett lekérdezésekben szereplő feltételek egyáltalán nem lesznek elemezve. így nem lesz olyan eredmény, amely megfelel a "leállítási" lekérdezésnek.

A másik oldalon a Microsoft-elemzők (ebben az esetben az en. Microsoft Analyzer) egy kicsit fejlettebbek, és nem a morfológiai elemzéshez használják. Ez azt jelenti, hogy az összes generált tokennek érvényes angol szavakat kell tartalmaznia. Például a "Leállítás", a "Leállítás" és a "megszüntetés" kifejezés általában teljes egészében marad az indexben, és előnyösebb választás lehet olyan forgatókönyvek esetében, amelyek sok esetben a helyettesítő karakterektől és a zavaros kereséstől függenek.

## <a name="scoring-wildcard-and-regex-queries"></a>Helyettesítő karakterek és regex-lekérdezések

Az Azure Cognitive Search a frekvencia-alapú pontozást ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) használja szöveges lekérdezésekhez. A helyettesítő karakterek és a regex-lekérdezések esetében azonban a feltételek hatóköre esetleg széles lehet, a rendszer figyelmen kívül hagyja a gyakorisági tényezőt, hogy megakadályozza a rangsorolást a ritkább kifejezésektől való eltérések irányába. A rendszer minden egyezést egyenlően kezel a helyettesítő karakterek és a regex-keresések esetében.

## <a name="special-characters"></a>Speciális karakterek

Bizonyos esetekben érdemes lehet speciális karaktert keresni, például egy "❤" Emoji vagy a "€" jelet. Ilyen esetekben ügyeljen arra, hogy a használt analizátor ne szűrje ki ezeket a karaktereket. A standard elemző számos speciális karaktert mellőz, kizárva őket az indexből.

A speciális karaktereket tartalmazó elemzők közé tartozik a "szóköz" elemző, amely figyelembe veszi a szóközökkel elválasztott tokenize (tehát a "❤" karakterláncot tokennek tekinti a rendszer). Emellett a Language Analyzer, például a Microsoft angol analizátor ("en. Microsoft"), a "€" karakterláncot veszi át tokenként. Az [analizátor tesztelésével](/rest/api/searchservice/test-analyzer) megtekintheti, hogy milyen jogkivonatokat hoz létre egy adott lekérdezéshez.

Unicode-karakterek használatakor győződjön meg arról, hogy a lekérdezési URL-címben a szimbólumok megfelelően vannak kimaradva (például "❤" esetén a escape-sorozatot kellene használni `%E2%9D%A4+` ). A Poster automatikusan elvégzi ezt a fordítást.  

## <a name="precedence-grouping"></a>Prioritás (csoportosítás)

A zárójelek használatával allekérdezéseket hozhat létre, beleértve az zárójeles utasításban található operátorokat is. Például `motel+(wifi|luxury)` megkeresi a "Motel" kifejezést és a "WiFi" vagy a "luxus" (vagy mindkettő) tartalmú dokumentumokat.

A mező csoportosítása hasonló, de a csoportosítás egyetlen mezőre vonatkozik. Például `hotelAmenities:(gym+(wifi|pool))` megkeresi a "hotelAmenities" mezőt a "tornaterem" és a "WiFi", a "tornaterem" és a "pool" mezőnél.  

## <a name="query-size-limits"></a>Lekérdezési méret korlátai

Korlátozva van az Azure Cognitive Searchba küldendő lekérdezések mérete. Pontosabban megadhatja a legfeljebb 1024 záradékot (a kifejezéseket és a, vagy a, és így tovább). A lekérdezésben szereplő egyes kifejezések méretének körülbelül 32 KB-os korlátja is van. Ha az alkalmazás programozott módon hozza létre a keresési lekérdezéseket, javasoljuk, hogy úgy tervezze meg úgy, hogy ne generáljon nem kötött méretű lekérdezéseket.  

## <a name="see-also"></a>Lásd még

+ [Példák az egyszerű keresésre](search-query-simple-examples.md)
+ [Példák a teljes Lucene keresésre](search-query-lucene-examples.md)
+ [Dokumentumok keresése](/rest/api/searchservice/Search-Documents)
+ [A szűrők és a rendezés OData kifejezésének szintaxisa](query-odata-filter-orderby-syntax.md)   
+ [Egyszerű lekérdezési szintaxis az Azure-ban Cognitive Search](query-simple-syntax.md)
