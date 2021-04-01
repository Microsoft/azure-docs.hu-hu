---
title: Egyszerű lekérdezési szintaxis
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search teljes szöveges keresési lekérdezéséhez használt egyszerű lekérdezési szintaxis referenciája.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f679d6fbab57bcbcccc09b722f6b2f670df49eb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516583"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Egyszerű lekérdezési szintaxis az Azure-ban Cognitive Search

Az Azure Cognitive Search két Lucene-alapú lekérdezési nyelvet implementál: az [egyszerű lekérdezés-elemzőt](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) és a [Lucene lekérdezés-elemzőt](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Az egyszerű elemző rugalmasabb, és akkor is megkísérli értelmezni a kérést, ha nem tökéletesen áll. Ennek a rugalmasságnak köszönhetően ez az alapértelmezett megoldás az Azure Cognitive Search-beli lekérdezésekhez.

Az egyszerű szintaxis a **`search`** [keresési dokumentumok (REST API)](/rest/api/searchservice/search-documents) paraméterében átadott lekérdezési kifejezésekhez használatos, és nem tévesztendő össze az adott kérelemben és kifejezésekben használt [OData szintaxissal](query-odata-filter-orderby-syntax.md) [**`$filter`**](search-filters.md) [**`$orderby`**](search-query-odata-orderby.md) . A OData paraméterek eltérő szintaxissal és szabályokkal rendelkeznek a lekérdezések összeállításához, a karakterláncok kiszökéséhez stb.

Bár az egyszerű elemző az [Apache Lucene egyszerű lekérdezés-elemző](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) osztályán alapul, a Cognitive Search megvalósítása kizárja a zavaros keresést. Ha [intelligens keresésre](search-query-fuzzy.md)van szüksége, vegye figyelembe az alternatív [teljes Lucene lekérdezési szintaxist](query-lucene-syntax.md) .

## <a name="example-simple-syntax"></a>Példa (egyszerű szintaxis)

Bár **`queryType`** az alábbi érték van beállítva, ez az alapértelmezett érték, és csak akkor hagyható el, ha egy alternatív típusról szeretne visszatérni. A következő példa egy független kifejezésen alapuló keresés, amely megköveteli, hogy minden egyező dokumentum tartalmazza a "pool" kifejezést.

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

A **`searchMode`** paraméter releváns ebben a példában. Ha a lekérdezésben logikai operátorok vannak, általában úgy kell beállítani, hogy az `searchMode=all` *összes* feltételt egyeztetni lehessen. Ellenkező esetben használhatja az alapértelmezett értéket, `searchMode=any` amely a pontosságot használja.

További példákért tekintse meg az [egyszerű lekérdezés szintaxisának példáit](search-query-simple-examples.md). A lekérdezési kérelemmel és paraméterekkel kapcsolatos részletekért lásd: [dokumentumok keresése (REST API)](/rest/api/searchservice/Search-Documents).

## <a name="keyword-search-on-terms-and-phrases"></a>Kulcsszavas keresés a feltételek és kifejezések alapján

A paraméternek átadott karakterláncok **`search`** tartalmazhatnak kifejezéseket vagy kifejezéseket bármilyen támogatott nyelven, logikai operátort, prioritási operátort, helyettesítő karaktert vagy előtagot a "kezdete" lekérdezésekkel, Escape-karakterekkel és URL-kódolási karakterekkel. A **`search`** paraméter megadása nem kötelező. Meghatározatlan, a Search ( `search=*` vagy `search=" "` ) tetszőleges (nem rangsorolt) sorrendben adja vissza a leggyakoribb 50-dokumentumokat.

+ A *kifejezéses keresés* egy vagy több kifejezés lekérdezése, ahol a feltételek bármelyike egyezésnek minősül.

+ A *kifejezéses keresés* az idézőjelek közé foglalt pontos kifejezés `" "` . Előfordulhat például, hogy ```Roach Motel``` (idézőjelek nélkül) olyan dokumentumokat keres ```Roach``` ```Motel``` , amelyek bármilyen sorrendben tartalmaznak és/vagy bárhol vannak ( ```"Roach Motel"``` az idézőjelekkel együtt) csak az egész kifejezést tartalmazó dokumentumokat egyeznek meg, és ebben a sorrendben (a lexikális analízis továbbra is érvényes). 

  A keresési ügyféltől függően előfordulhat, hogy az idézőjeleket egy kifejezés keresésében kell elmenekülnie. Egy POST-kérelemben például a Poster-ben a `"Roach Motel"` kérelem törzsében keresendő kifejezés `"\"Roach Motel\""` .

Alapértelmezés szerint a paraméterben átadott összes kifejezés vagy kifejezés **`search`** lexikális analízis alá kerül. Győződjön meg arról, hogy megértette a használt analizátor jogkivonatok létrehozása viselkedését. Gyakran előfordul, hogy ha a lekérdezési eredmények váratlanok, az OK nyomon követhető, hogy a feltételek hogyan legyenek jogkivonatban a lekérdezés időpontjában.

Egy vagy több kifejezéssel rendelkező szöveg érvényes kiindulási pontnak tekintendő a lekérdezés végrehajtásához. Az Azure Cognitive Search a feltételek bármelyikét vagy mindegyikét tartalmazó dokumentumokat egyezteti, beleértve a szöveg elemzése során talált módosításokat is.

Ahogy ez hangzik, az Azure Cognitive Search lekérdezés-végrehajtásának egyik aspektusa *, amely váratlan eredményeket eredményezhet,* és a keresési eredmények csökkentése helyett egyre nagyobb mértékben bővül, és a rendszer hozzáadja a bemeneti karakterlánchoz az operátorokat. Ha ez a bővítés ténylegesen bekövetkezik, attól függ, hogy egy nem operátor bekerül-e, és egy olyan **`searchMode`** paraméter-beállítással együtt, amely meghatározza, hogy a rendszer hogyan értelmezi a használati feltételeket és a viselkedést. További információ: NOT operátor a [logikai operátorok](#boolean-operators)alatt.

## <a name="boolean-operators"></a>Logikai operátorok

Egy lekérdezési karakterláncban logikai operátorokat ágyazhat be, így javíthatja a egyezés pontosságát. Az egyszerű szintaxisban a logikai operátorok karakter-alapúak. A szöveges operátorok, például a Word és a nem támogatottak.

| Karakter | Példa | Használat |
|----------- |--------|-------|
| `+` | `pool + ocean` | Egy és művelet. Például `pool + ocean` azt határozza meg, hogy egy dokumentumnak mindkét kifejezést tartalmaznia kell.|
| `|` | `pool | ocean` | Egy vagy művelet megtalál egy egyezést, ha az egyik kifejezés található. A példában a lekérdezési motor a vagy a vagy a mindkettőt tartalmazó dokumentumokra vonatkozó egyezést ad vissza `pool` `ocean` . Mivel a vagy az az alapértelmezett, és ez az operátor, azt is megteheti, hogy `pool ocean` a megfelelő  `pool | ocean` .|
| `-` | `pool – ocean` | Egy nem művelet a kifejezést kizáró dokumentumok egyezéseit adja vissza. <br/><br/>Ha nem a várt viselkedést szeretné lekérni egy nem kifejezésre, érdemes lehet beállítani a **`searchMode=all`** kérést. Ellenkező esetben a (z) alapértelmezés szerint a (z) és a (z) minden olyan dokumentumra vonatkozik, amely nem tartalmaz egyezéseket, **`searchMode=any`** `pool` `ocean` ami sok dokumentum lehet. A **`searchMode`** lekérdezési kérelem paramétere azt szabályozza, hogy a nem operátorral rendelkező kifejezés ANDed vagy ORed-e a lekérdezés más feltételeivel (feltéve, hogy `+` `|` a többi feltétel nem vagy operátor.) **`searchMode=all`** A használata növeli a lekérdezések pontosságát azáltal, hogy kevesebb eredményt ad meg, és alapértelmezés szerint a "és nem" értéket fogja értelmezni. <br/><br/>Egy beállítás meghatározásakor **`searchMode`** vegye figyelembe a felhasználói interakciós mintákat a különböző alkalmazásokban található lekérdezésekhez. Azok a felhasználók, akik információt keresnek, nagyobb valószínűséggel tartalmaznak egy operátort egy lekérdezésben, szemben a több beépített navigációs szerkezettel rendelkező e-kereskedelmi webhelyekkel. |

<a name="prefix-search"></a>

## <a name="prefix-queries"></a>Előtag-lekérdezések

A "Start with" lekérdezések esetében adjon hozzá egy utótag-operátort ( `*` ) a kifejezés hátralévő részének helyőrzőként. Egy előtag-lekérdezésnek legalább egy alfanumerikus karakterrel kell kezdődnie ahhoz, hogy hozzá lehessen adni az utótag-kezelőt.

| Karakter | Példa | Használat |
|----------- |--------|-------|
| `*` | `lingui*` megfelel a "nyelvi" vagy a "linguini" | A csillag ( `*` ) egy vagy több tetszőleges hosszúságú karaktert jelöl, figyelmen kívül hagyva a kis-és nagybetűket.  |

A szűrőkhöz hasonlóan az előtag-lekérdezések pontos egyezést keresnek. Ezért nincs releváns pontozás (az összes találat 1,0-as keresési pontszámot kap). Ügyeljen arra, hogy az előtag-lekérdezések lassúak legyenek, különösen akkor, ha az index nagy, és az előtag kis számú karakterből áll. Egy alternatív módszer, például az Edge n-Gram jogkivonatok létrehozása is gyorsabban elvégezhető.

Az egyszerű szintaxis csak az előtag-egyeztetést támogatja. A kifejezés végéhez vagy közepéhez illő utótag vagy Infix esetén használja a [helyettesítő karakteres keresés teljes Lucene szintaxisát](query-lucene-syntax.md#bkmk_wildcard).

## <a name="escaping-search-operators"></a>Keresési operátorok elől  

Az egyszerű szintaxisban a keresési operátorok a következő karaktereket tartalmazzák: `+ | " ( ) ' \`  

Ha a karakterek bármelyike az indexben található token részét képezi, akkor a lekérdezésben lévő egy fordított perjel () előtaggal kell elmenekülnie `\` . Tegyük fel például, hogy egy egyéni elemzőt használt a teljes kifejezés jogkivonatok létrehozása, és az index tartalmazza a "Luxury + Hotel" karakterláncot. Ha pontos egyezést szeretne kapni ehhez a jogkivonathoz, szúrjon be egy Escape-karaktert: `search=luxury\+hotel` .

Ahhoz, hogy a dolgok egyszerűbbek legyenek a tipikus esetekben, két kivétel vonatkozik erre a szabályra, ha nincs szükség a szökésre:  

+ A nem operátort csak akkor kell `-` megszökni, ha az első karakter a szóköz után. Ha a `-` középen (például a) jelenik meg `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` , kihagyhatja a szökést.

+ Az utótag operátort `*` csak akkor kell megszökni, ha az utolsó karakter a szóköz előtt. Ha a `*` középen (például a-ben) megjelenik `4*4=16` , nincs szükség a szökésre.

> [!NOTE]  
> Alapértelmezés szerint a standard Analyzer törli és megszakítja a szavakat a kötőjelek, a szóközök, a jelek és más karakterek számára a [lexikális analízis](search-lucene-query-architecture.md#stage-2-lexical-analysis)során. Ha speciális karakterek szükségesek a lekérdezési karakterláncban, előfordulhat, hogy olyan elemzőre van szüksége, amely megőrzi őket az indexben. Bizonyos lehetőségek közé tartoznak a Microsoft természetes [nyelvi elemzők](index-add-language-analyzers.md), amelyek megőrzik az elválasztott szavakat, vagy egy egyéni elemzőt összetettebb mintákhoz. További információ: [részleges feltételek, mintázatok és speciális karakterek](search-query-partial-matching.md).

## <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Nem biztonságos és fenntartott karakterek kódolása az URL-címekben

Győződjön meg arról, hogy az összes nem biztonságos és fenntartott karakter egy URL-címben van kódolva. Például a "#" egy nem biztonságos karakter, mert egy URL-címben szereplő töredék/horgony azonosító. A karaktert kódolni kell, `%23` Ha az URL-címben van használatban. A (z) "&" és a "=" például fenntartott karakterek, amelyek a paramétereket korlátozzák, és értékeket határoznak meg az Azure Cognitive Searchban. További információkért lásd [: RFC1738: egységes erőforrás-lokátorok (URL-cím)](https://www.ietf.org/rfc/rfc1738.txt).

A nem biztonságos karakterek a következők: ``" ` < > # % { } | \ ^ ~ [ ]`` . A fenntartott karakterek a következők: `; / ? : @ = + &` .

## <a name="special-characters"></a>Speciális karakterek

Bizonyos esetekben érdemes lehet speciális karaktert keresni, például egy "❤" Emoji vagy a "€" jelet. Ilyen esetekben ügyeljen arra, hogy a használt analizátor ne szűrje ki ezeket a karaktereket. A standard elemző számos speciális karaktert mellőz, kizárva őket az indexből.

A speciális karaktereket tartalmazó elemzők közé tartozik a "szóköz" elemző, amely figyelembe veszi a szóközökkel elválasztott tokenize (tehát a "❤" karakterláncot tokennek tekinti a rendszer). Emellett a Language Analyzer, például a Microsoft angol analizátor ("en. Microsoft"), a "€" karakterláncot veszi át tokenként. Az [analizátor tesztelésével](/rest/api/searchservice/test-analyzer) megtekintheti, hogy milyen jogkivonatokat hoz létre egy adott lekérdezéshez.

Unicode-karakterek használatakor győződjön meg arról, hogy a lekérdezési URL-címben a szimbólumok megfelelően vannak kimaradva (például "❤" esetén a escape-sorozatot kellene használni `%E2%9D%A4+` ). A Poster automatikusan elvégzi ezt a fordítást.  

## <a name="precedence-grouping"></a>Prioritás (csoportosítás)

A zárójelek használatával allekérdezéseket hozhat létre, beleértve az zárójeles utasításban található operátorokat is. Például `motel+(wifi|luxury)` megkeresi a "Motel" kifejezést és a "WiFi" vagy a "luxus" (vagy mindkettő) tartalmú dokumentumokat.

## <a name="query-size-limits"></a>Lekérdezési méret korlátai

Ha az alkalmazás programozott módon hozza létre a keresési lekérdezéseket, javasoljuk, hogy úgy tervezze meg úgy, hogy ne generáljon nem kötött méretű lekérdezéseket. 

+ A GET értéknél az URL-cím hossza nem haladhatja meg a 8 KB-ot.

+ A POST (és bármely más kérelem) esetében, ahol a kérelem törzse és egyéb paraméterek (például és) is szerepelnek, `search` `filter` `orderby` a maximális méret 16 MB, ahol a záradékok maximális száma `search` (a és a, vagy a, illetve a és más karakterekkel elválasztva) 1024. A lekérdezésben szereplő egyes kifejezések méretének körülbelül 32 KB-os korlátja is van. További információ: API- [kérelmek korlátai](search-limits-quotas-capacity.md#api-request-limits).

## <a name="next-steps"></a>Következő lépések

Ha programozott módon készíti elő a lekérdezéseket, tekintse át a [teljes szöveges keresést az Azure Cognitive Searchban](search-lucene-query-architecture.md) , és Ismerje meg a lekérdezések feldolgozásának szakaszait, valamint a szöveges elemzés következményeit.

A lekérdezési konstrukcióval kapcsolatos további tudnivalókért tekintse át a következő cikkeket:

+ [Példák az egyszerű keresésre](search-query-simple-examples.md)
+ [Példák a teljes Lucene keresésre](search-query-lucene-examples.md)
+ [Dokumentumok keresése – REST API](/rest/api/searchservice/Search-Documents)
+ [Lucene lekérdezési szintaxis](query-lucene-syntax.md)
+ [Szűrés és kiválasztás (OData) kifejezés szintaxisa](query-odata-filter-orderby-syntax.md)