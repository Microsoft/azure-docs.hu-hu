---
title: Teljes szöveges lekérdezés és indexelési motor architektúrája (Lucene)
titleSuffix: Azure Cognitive Search
description: Megvizsgálja a Lucene-lekérdezés feldolgozási és dokumentum-beolvasási fogalmait a teljes szöveges kereséshez, az Azure Cognitive Search-hez kapcsolódóan.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 50a1656fcb92d9777d4a9476ef2a4c1fd2f2efc6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002748"
---
# <a name="full-text-search-in-azure-cognitive-search"></a>Teljes szöveges keresés az Azure Cognitive Search

Ez a cikk olyan fejlesztőknek szól, akiknek alaposabban meg kell ismerniük, hogyan működik a Lucene teljes szöveges keresése az Azure Cognitive Searchban. Szöveges lekérdezéseknél az Azure Cognitive Search a legtöbb esetben zökkenőmentesen visszaadja a várt eredményeket, de időnként előfordulhat, hogy az eredmény „helytelennek” tűnik. Ilyenkor a Lucene-lekérdezés végrehajtásának négy fázisában (lekérdezéselemzés, lexikális analízis, dokumentumok egyeztetése, pontozás) szerzett jártasság segíthet azonosítani a lekérdezési paraméterek vagy az indexelési konfiguráció adott módosításait, a kívánt eredmény elérése érdekében. 

> [!Note] 
> Az Azure Cognitive Search Lucene használ a teljes szöveges kereséshez, de a Lucene-integráció nem teljes. A Lucene funkcióit szelektíven tesszük elérhetővé és bővítjük, hogy az Azure Cognitive Search számára fontos forgatókönyveket lehessen engedélyezni. 

## <a name="architecture-overview-and-diagram"></a>Architektúra áttekintése és diagram

A teljes szöveges keresési lekérdezés feldolgozásával elindul a lekérdezés szövegének elemzése a keresési kifejezések kinyeréséhez. A keresőmotor egy indexet használ a dokumentumok megfelelő feltételekkel való lekéréséhez. Az egyes lekérdezési kifejezéseket időnként lebontják, és új űrlapokra alakítják át, amelyek szélesebb körű, a lehetséges egyezésnek számító adatátvitelt eredményeznek. Ekkor egy eredményhalmaz az egyes megfeleltetési dokumentumokhoz rendelt relevanciás pontszám szerint van rendezve. A rangsorolt lista tetején lévőket a rendszer visszaadja a hívó alkalmazásnak.

Újra, a lekérdezés végrehajtásának négy szakasza van: 

1. Lekérdezés elemzése 
2. Lexikális analízis 
3. Dokumentumok beolvasása 
4. Pontozás 

Az alábbi ábra a keresési kérések feldolgozásához használt összetevőket szemlélteti. 

 ![Lucene lekérdezési architektúra diagram az Azure Cognitive Search][1]


| A legfontosabb összetevők | Funkcionális Leírás | 
|----------------|------------------------|
|**Lekérdezés-elemzők** | A lekérdezési operátorok elkülönítik a lekérdezési feltételeket, és létrehozzák a keresőmotornak küldendő lekérdezési struktúrát (lekérdezési fát). |
|**Elemzők** | A lekérdezési feltételekhez tartozó lexikális analízis végrehajtása. Ez a folyamat a lekérdezési kifejezések átalakítását, eltávolítását vagy kiterjesztését is magában foglalja. |
|**Index** | Hatékony adatstruktúra, amely az indexelt dokumentumokból kinyert kereshető kifejezések tárolására és rendszerezésére szolgál. |
|**Keresőmotor** | A fordított index tartalmától függően lekéri és lekérdezi a megfelelő dokumentumokat. |

## <a name="anatomy-of-a-search-request"></a>A keresési kérelmek működése

A keresési kérések teljes körűen meghatározzák, hogy mit kell visszaadni egy eredményhalmaz. Legegyszerűbb formában ez egy üres lekérdezés, amely nem tartalmaz semmiféle feltételt. A reálisabb példa többek között paramétereket, több lekérdezési kifejezést is magában foglal, például bizonyos mezőkre, például egy szűrési kifejezésre és a megrendelési szabályokra.  

A következő példa egy olyan keresési kérelem, amelyet az Azure-Cognitive Search küldhet az [REST API](/rest/api/searchservice/search-documents)használatával.  

```
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
```

Ehhez a kérelemhez a keresőmotor a következő műveleteket végzi el:

1. Kiszűri a dokumentumokat, ahol az ár legalább $60, és kisebb, mint $300.
2. Végrehajtja a lekérdezést. Ebben a példában a keresési lekérdezés kifejezésekből és kifejezésből áll: `"Spacious, air-condition* +\"Ocean view\""` (a felhasználók általában nem adnak meg írásjeleket, de a példában szereplő módon lehetővé teszi, hogy elmagyarázza, hogyan kezeli a elemzőket). Ebben a lekérdezésben a keresőmotor megvizsgálja az `searchFields` "Ocean View" és a "tágas" kifejezéssel, illetve a "légkondicionáló" előtaggal kezdődő feltételekben megadott leírási és title mezőket. A `searchMode` paraméter minden olyan kifejezésre (alapértelmezett) vagy mindegyikre vonatkozik, amelynél nincs expliciten kötelező kifejezés ( `+` ).
3. Megrendeli az eredményül kapott szállodákat egy adott földrajzi hely közelében, majd visszaadja a hívó alkalmazásnak. 

Ennek a cikknek a többsége a *keresési lekérdezés* feldolgozását ismerteti: `"Spacious, air-condition* +\"Ocean view\""` . A szűrés és a megrendelés a hatókörön kívül esik. További információkért tekintse meg a [Search API-referenciák dokumentációját](/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>1. lépés: lekérdezés elemzése 

A lekérdezési karakterlánc a kérelem első sora: 

```
 "search": "Spacious, air-condition* +\"Ocean view\"", 
```

A lekérdezés-elemző a keresési kifejezésektől elkülöníti a operátorokat (például `*` a és a `+` példában), és a keresési lekérdezést egy támogatott típusú *allekérdezésbe* hozza létre: 

+ *kifejezéses lekérdezés* önálló kifejezésekhez (például tágas)
+ kifejezések *lekérdezése* idézőjelek között (például Ocean View)
+ előtag- *lekérdezés* a kifejezésekhez, majd egy előtag `*` -operátor (például a légkondicionáló).

A támogatott lekérdezési típusok teljes listáját lásd: [Lucene lekérdezési szintaxisa](/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Az allekérdezéshez társított operátorok határozzák meg, hogy a lekérdezésnek "vagy" értékűnek kell lennie ahhoz, hogy egy dokumentum egyezésnek tekintendő legyen. Például `+"Ocean view"` a "kötelező" a `+` kezelő miatt. 

A lekérdezés-elemző átstrukturálja az allekérdezéseket egy *lekérdezési fában* (a lekérdezést jelképező belső struktúra), amelyet a keresőmotornak továbbít. A lekérdezés-elemzés első szakaszában a lekérdezési fa így néz ki.  

 ![Logikai lekérdezés searchmode][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Támogatott elemzők: egyszerű és teljes Lucene 

 Az Azure Cognitive Search két különböző lekérdezési nyelvet tesz elérhetővé `simple` (alapértelmezett) és `full` . `queryType`Ha a paramétert a keresési kérelemre állítja be, a lekérdezési elemzőnek meg kell határoznia, hogy melyik lekérdezési nyelvet választotta, hogy tudja, hogyan kell értelmezni a kezelőket és a szintaxist. Az [egyszerű lekérdezési nyelv](/rest/api/searchservice/simple-query-syntax-in-azure-search) intuitív és robusztus, ami gyakran alkalmas arra, hogy az ügyféloldali feldolgozás nélkül értelmezze a felhasználói adatokat. Támogatja a webes keresőmotorokból ismerős lekérdezési operátorokat. A beállítás által beolvasott [teljes Lucene lekérdezési nyelv](/rest/api/searchservice/lucene-query-syntax-in-azure-search) `queryType=full` kibővíti az alapértelmezett egyszerű lekérdezési nyelvet azáltal, hogy további operátorokat és lekérdezési típusokat (például helyettesítő karaktert, fuzzy, regexet és mező-hatókörű lekérdezéseket) ad hozzá. Az egyszerű lekérdezési szintaxisban eljuttatott reguláris kifejezés például lekérdezési karakterláncként, nem pedig kifejezésként értelmezhető. A cikkben szereplő példa a teljes Lucene lekérdezési nyelvet használja.

### <a name="impact-of-searchmode-on-the-parser"></a>Az elemző searchMode gyakorolt hatása 

Az elemzést befolyásoló másik keresési kérelem paraméter a `searchMode` paraméter. A logikai lekérdezések alapértelmezett operátorát vezérli: bármely (alapértelmezett) vagy mind.  

Ha a `searchMode=any` (z) alapértelmezés szerint a tágas és a légkondicionáló közötti térköz (), vagy ( `||` ), a minta lekérdezési szövege megegyezik a következővel: 

```
Spacious,||air-condition*+"Ocean view" 
```

A explicit operátorok `+` (például a-ben `+"Ocean view"` ) nem egyértelműek a logikai lekérdezések szerkezetében (a kifejezésnek egyeznie *kell* ). Kevésbé nyilvánvaló, hogy hogyan értelmezheti a fennmaradó kifejezéseket: tágas és légkondicionáló. A keresőmotor az Ocean View *és* a tágas *, illetve* a légkondicionáló esetében is megfelel? Vagy az is előfordulhat, hogy az Ocean View vagy a fennmaradó feltételek *valamelyike* szerepel? 

Alapértelmezés szerint `searchMode=any` a keresőmotor a szélesebb körű értelmezést feltételezi. Mindkét *mezőnek egyeznie kell,* és tükröznie kell a "vagy a" szemantikai értékeket. A kezdeti lekérdezési fa korábban is látható, a két "If" művelettel pedig az alapértelmezett értéket mutatja.  

Tegyük fel, hogy most már be van állítva `searchMode=all` . Ebben az esetben a rendszer "és" műveletként értelmezi a helyet. A többi feltételnek mindkettőnek szerepelnie kell a dokumentumban, hogy egyezzen a megfelelő jogosultsággal. Az eredményül kapott minta lekérdezés a következőképpen lesz értelmezve: 

```
+Spacious,+air-condition*+"Ocean view"
```

A lekérdezés módosított lekérdezési fájának a következőnek kell lennie, ahol a megfelelő dokumentum a mindhárom allekérdezés metszéspontja: 

 ![Searchmode logikai lekérdezés][3]

> [!Note] 
> `searchMode=any`A többit megillető `searchMode=all` döntés a legjobb választás a reprezentatív lekérdezések futtatásával. Azok a felhasználók, akik valószínűleg belefoglalják az operátorokat (a dokumentumtárakban való kereséskor gyakran előfordulnak), az eredmények könnyebben megjelenhetnek, ha a `searchMode=all` logikai lekérdezési szerkezeteket tájékoztatja. A és a operátorok közötti interakcióról további információt az `searchMode` [egyszerű lekérdezés szintaxisa](/rest/api/searchservice/simple-query-syntax-in-azure-search)című témakörben talál.

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>2. fázis: lexikális analízis 

A lexikális elemzők *feldolgozzák* a lekérdezési és *kifejezési* lekérdezéseket a lekérdezési fa strukturálása után. Az analizátor fogadja az elemző által megadott szöveges bemeneteket, feldolgozza a szöveget, majd visszaküldi a jogkivonatos kifejezéseket a lekérdezési fába. 

A lexikális analízis leggyakoribb formája a *nyelvi elemzés* , amely az adott nyelvre vonatkozó szabályok alapján átalakítja a lekérdezési feltételeket: 

* Lekérdezési kifejezés csökkentése egy szó legfelső formájára 
* Nem lényeges szavak (indexelendő, például "a" vagy "és" angol nyelven) eltávolítása 
* Összetett szó lebontása összetevő-részekre 
* Alsó borítású szó kisbetűvel 

Az összes művelet általában törli a felhasználó által megadott szövegbevitel és az indexben tárolt feltételek közötti különbségeket. Az ilyen műveletek túlmutatnak a szöveg feldolgozásán, és a nyelv részletes ismeretét igényli. Az Azure Cognitive Search a nyelvi [elemzések](/rest/api/searchservice/language-support) egy hosszú listáját támogatja a Lucene és a Microsofttól.

> [!Note]
> Az elemzési követelmények a forgatókönyvtől függően minimálisról kidolgozhatók. A lexikális analízis bonyolultságát az előre definiált elemzők egyikének kiválasztásával vagy a saját [Egyéni analizátor](/rest/api/searchservice/Custom-analyzers-in-Azure-Search)létrehozásával szabályozhatja. Az elemzők a kereshető mezőkre vannak kiterjedően, és a mező definíciójának részeként vannak megadva. Ez lehetővé teszi, hogy a lexikális elemzést egy mező alapján változtassa meg. Meghatározatlan, a *standard* Lucene Analyzer használatos.

A példánkban az elemzést megelőzően a kezdeti lekérdezési fa a "tágas" kifejezést, egy nagybetűvel és egy vesszőt tartalmaz, amelyet a lekérdezési elemző a lekérdezési kifejezés részeként értelmez (a vesszőt nem tekinti a lekérdezési nyelv operátornak).  

Ha az alapértelmezett elemző feldolgozza a kifejezést, az az "Ocean View" és a "tágas" karaktert választja, és eltávolítja a vesszőt. A módosított lekérdezési fa a következőképpen fog megjelenni: 

 ![Logikai lekérdezés elemzett kifejezésekkel][4]

### <a name="testing-analyzer-behaviors"></a>Az analizátor viselkedésének tesztelése 

Az analizátor viselkedését az [elemzés API](/rest/api/searchservice/test-analyzer)használatával lehet tesztelni. Adja meg az elemezni kívánt szöveget, és tekintse meg, hogy az adott elemző milyen feltételekkel fog létrejönni. Ha például azt szeretné látni, hogy a standard Analyzer hogyan dolgozza fel a "légkondicionáló" szöveget, a következő kérést adhatja ki:

```json
{
    "text": "air-condition",
    "analyzer": "standard"
}
```

A standard Analyzer megszakítja a bemeneti szöveget a következő két jogkivonatba, és megjegyzésekkel láthatja el őket olyan attribútumokkal, mint a kezdő és a záró eltolás (a találatok kiemeléséhez használatos), valamint a helyük (a kifejezés megfeleltetéséhez használt):

```json
{
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
```

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>A lexikális analízis alóli kivételek 

A lexikális analízis csak olyan lekérdezési típusokra vonatkozik, amelyek teljes kifejezést igényelnek – vagy egy kifejezéses lekérdezést vagy egy kifejezéses lekérdezést. Nem vonatkozik a hiányos kifejezésekkel rendelkező lekérdezési típusokra – előtag-lekérdezés, helyettesítő karakteres lekérdezés, regex lekérdezés – vagy egy fuzzy lekérdezésre. Ezek a lekérdezési típusok, beleértve a példában szereplő előtag-lekérdezést is, `air-condition*` közvetlenül a lekérdezési fában lesznek hozzáadva, és megkerülik az elemzési szakaszt. Az egyetlen, az adott típusok lekérdezési feltételein végrehajtott átalakítás lowercasing.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>3. fázis: dokumentumok beolvasása 

A dokumentumok beolvasása arra utal, hogy az indexben a dokumentumok megkeresése egyező kifejezésekkel történjen. Ez a szakasz a legjobban egy példán keresztül értelmezhető. Kezdjük a következő egyszerű sémával rendelkező Hotels indextel: 

```json
{
    "name": "hotels",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "title", "type": "Edm.String", "searchable": true },
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
```

Azt feltételezi, hogy ez az index a következő négy dokumentumot tartalmazza: 

```json
{
    "value": [
        {
            "id": "1",
            "title": "Hotel Atman",
            "description": "Spacious rooms, ocean view, walking distance to the beach."
        },
        {
            "id": "2",
            "title": "Beach Resort",
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."
        },
        {
            "id": "3",
            "title": "Playa Hotel",
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },
        {
            "id": "4",
            "title": "Ocean Retreat",
            "description": "Quiet and secluded"
        }
    ]
}
```

**A feltételek indexelése**

A lekérések megismeréséhez segít az indexeléssel kapcsolatos alapvető tudnivalók megismerésében. A tárolási egység egy fordított index, amely minden kereshető mezőhöz egy. Egy invertált indexen belül az összes dokumentum összes kifejezésének rendezett listája szerepel. Minden kifejezés az alábbi példában látható módon leképezi a dokumentumok listáját.

Egy fordított indexben a kifejezések előállításához a keresőmotor a dokumentumok tartalmának lexikális elemzését hajtja végre, hasonlóan a lekérdezés feldolgozásához:

1. A rendszer átadja a *szöveges bemeneteket* egy elemzőnek, az alacsonyabb betokozású, a kimaradt írásjeleket és így tovább, az analizátor konfigurációjától függően. 
2. A *tokenek* a lexikális analízis eredményei.
3. A *feltételek* hozzáadódnak az indexhez.

Gyakori, de nem kötelező, ha ugyanazokat az elemzőket használja a keresési és indexelési műveletekhez, hogy a lekérdezési feltételek jobban hasonlítsák az indexen belüli kifejezéseket.

> [!Note]
> Az Azure Cognitive Search lehetővé teszi, hogy különböző elemzőket határozzon meg az indexeléshez és a kereséshez további `indexAnalyzer` és `searchAnalyzer` mező paraméterek használatával. Ha nincs megadva, a tulajdonsággal beállított Analyzer az `analyzer` indexeléshez és a kereséshez is használatos.  

**Invertált index például dokumentumok**

Ha visszatér a példánkban, a **title (cím** ) mezőben a fordított index a következőképpen néz ki:

| Időszak | Dokumentumok listája |
|------|---------------|
| Atman | 1 |
| Beach | 2 |
| Hotel | 1, 3 |
| óceáni | 4  |
| Playa | 3 |
| Resort | 3 |
| Retreat | 4 |

A title (cím) mezőben csak a ( *z* ) két dokumentum jelenik meg: 1, 3.

A **Leírás** mezőben az index a következő:

| Időszak | Dokumentumok listája |
|------|---------------|
| levegő | 3
| és | 4
| Beach | 1
| légkondicionált | 3
| tisztában | 3
| távolság | 1
| -sziget | 2
| kaua ʻ | 2
| található | 2
| north (észak) | 2
| óceáni | 1, 2, 3
| / | 2
| itt: |2
| csendes | 4
| tárgyalótermek  | 1, 3
| félreeső | 4
| Shore | 2
| tágas | 1
| műveletnek a(z) | 1, 2
| felhasználóként a(z) | 1
| megtekintése | 1, 2, 3
| séta | 1
| nevű és | 3


**Lekérdezési feltételek egyeztetése indexelt kifejezésekkel**

A fenti fordított indexek esetében térjünk vissza a minta lekérdezéshez, és nézzük meg, hogyan találhatók a példában szereplő lekérdezésekkel kapcsolatos egyező dokumentumok. Ne felejtse el, hogy a végső lekérdezési fa így néz ki: 

 ![Logikai lekérdezés elemzett kifejezésekkel][4]

A lekérdezés végrehajtása során az egyes lekérdezések egymástól függetlenül lesznek végrehajtva a kereshető mezőkön. 

+ A "tágas" TermQuery megfelel az 1. dokumentumnak (Hotel Atman). 

+ A "légkondicionáló *" PrefixQuery nem felel meg egyetlen dokumentumnak sem. 

  Ez egy olyan viselkedés, amely esetenként összekeveri a fejlesztőket. Bár a dokumentumban a légkondicionáló kifejezés található, az alapértelmezett elemző két kifejezésre oszlik. Ne felejtse el, hogy a részleges kifejezéseket tartalmazó előtag-lekérdezések nem lesznek elemezve. Ezért a "légkondicionáló" előtaggal rendelkező kifejezések a fordított indexben vannak felkeresve, és nem találhatók.

+ Az "Ocean View" PhraseQuery megkeresi az "Ocean" és a "View" kifejezést, és ellenőrzi a feltételek közelségét az eredeti dokumentumban. Az 1., 2. és 3. dokumentum megfelel a Leírás mezőben szereplő lekérdezésnek. Figyelje meg, hogy a 4. dokumentum az Ocean kifejezést használja a címben, de nem egyezik meg, mert az "Ocean View" kifejezést keresi az egyes szavak helyett. 

> [!Note]
> A keresési lekérdezéseket az Azure Cognitive Search index összes kereshető mezőjétől függetlenül hajtja végre, hacsak nem korlátozza a paraméterrel beállított mezőket `searchFields` , ahogyan az a példában szereplő keresési kérelemben látható. A rendszer a kijelölt mezők bármelyikének megfelelő dokumentumokat adja vissza. 

A kérdéses lekérdezés teljes egészében az 1, 2, 3. 

## <a name="stage-4-scoring"></a>4. fázis: pontozás  

A keresési eredményhalmaz minden dokumentuma releváns pontszámot kap. A relevancia pontszám funkciója, hogy magasabbra rangsorolja azokat a dokumentumokat, amelyek a keresési lekérdezés által kifejezett felhasználói kérdésre válaszolnak. A pontszám kiszámítása az egyeztetett kifejezések statisztikai tulajdonságai alapján történik. A pontozási képlet magja a [TF/IDF (kifejezés gyakorisága-inverz dokumentum gyakorisága)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). A ritka és gyakori kifejezéseket tartalmazó lekérdezésekben a TF/IDF elősegíti a ritka időszakot tartalmazó eredményeket. Például egy olyan feltételezett indexben, amelyben az összes wikipedia-cikk szerepel, a lekérdezésnek megfelelő dokumentumokban *az* elnöknek megfeleltetett dokumentumok nagyobb jelentőséggel *rendelkeznek,* mint *a* dokumentumoknak megfelelő dokumentumok.


### <a name="scoring-example"></a>Pontozási példa

Hívja fel a példát a lekérdezésnek megfelelő három dokumentumra:

```
search=Spacious, air-condition* +"Ocean view"  
```

```json
{
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
```

Az 1. dokumentum megfelelt a lekérdezésnek, mert mind a *tágas* , mind a szükséges, az *Ocean View* kifejezés a Leírás mezőben szerepel. A következő két dokumentum csak az *Ocean View* kifejezéssel egyezik meg. Előfordulhat, hogy meglepő, hogy a 2. és 3. dokumentum relevanciás pontszáma eltérő, bár a lekérdezésnek ugyanúgy egyeznek. Ez azért van, mert a pontozási képlet több összetevővel rendelkezik, mint a TF/IDF. Ebben az esetben a 3. dokumentumot egy valamivel magasabb pontszámot rendeltük, mert a leírása rövidebb. Ismerje meg a [Lucene gyakorlati pontozási képletét](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) , amelyből megtudhatja, hogy a mezők hosszának és más tényezőknek milyen hatása lehet a releváns pontszámra.

Néhány lekérdezési típus (helyettesítő karakter, előtag, regex) mindig a teljes dokumentum pontszámával járul hozzá egy állandó pontszámhoz. Ez lehetővé teszi, hogy a lekérdezési kiterjesztésen keresztül megtalált egyezések szerepeljenek az eredményekben, de a rangsorolás befolyásolása nélkül. 

Ebben a példában egy példa szemlélteti, hogy miért fontos a kérdés. A helyettesítő karakteres keresések, például az előtag-keresések, a definíciók nem egyértelműek, mivel a bemenet egy olyan részleges karakterlánc, amelynek a lehetséges egyezései nagyon nagy számú különálló kifejezéssel rendelkeznek (vegye figyelembe a "Tour *" bemenetet, amely megfelel a "Tours", a "Tourettes" és a "turmalin"). Ezeknek az eredményeknek a jellegéből adódóan nem lehet ésszerűen következtetni, hogy mely feltételek sokkal értékesek, mint mások. Ezért figyelmen kívül hagyja a kifejezési gyakoriságot, amikor a helyettesítő karakteres, előtag és regex típusú lekérdezéseket eredményez. A részleges és teljes feltételeket tartalmazó többrészes keresési kérelemben a részleges bevitel eredményei egy állandó pontszámmal vannak beépítve, hogy elkerülje a potenciálisan váratlan egyezések torzítását.

### <a name="score-tuning"></a>Pontszám finomhangolása

Az Azure Cognitive Searchban kétféleképpen hangolhatja be a relevancia pontszámait:

1. A **pontozási profilok** a szabályok egy halmaza alapján támogatják az eredmények rangsorolt listáján szereplő dokumentumokat. A példánkban a title (cím) mezőben szereplő dokumentumokat a Leírás mezőben szereplő dokumentumokra vonatkozó szempontok szerint érdemes megfontolni. Továbbá, ha az indexünk minden egyes szállodára érvényes, akkor alacsonyabb díjszabású dokumentumokat is támogatunk. További információ a [pontozási profilok keresési indexhez való hozzáadásáról.](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. A **kifejezés fokozása** (csak a teljes Lucene lekérdezési szintaxisban érhető el) olyan fellendítő operátort biztosít `^` , amely a lekérdezési fa bármely részén alkalmazható. A példánkban az előtagot a *levegő feltételének* megkeresése helyett \* az egyik a pontos *állapotot* vagy az előtagot is megkeresheti, de a pontos kifejezéssel megegyező dokumentumok magasabbra vannak rangsorolva, ha a lekéréses lekérdezésre a következő feltételek vonatkoznak: * Air-condition ^ 2 | | légkondicionáló * *. További információ a [kifejezés növeléséről](/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Pontozás egy elosztott indexben

Az Azure Cognitive Search összes indexe automatikusan több szegmensre oszlik, így gyorsan terjesztheti az indexet több csomópont között a szolgáltatás vertikális felskálázása vagy leskálázása során. Egy keresési kérelem kibocsátásakor az egyes szegmensek egymástól függetlenül kerülnek kiosztásra. Ezután az egyes szegmensek eredményeit összevontuk, és a pontszám alapján rendezi a rendszer (ha nincs más sorrend definiálva). Fontos tudni, hogy a pontozási függvény a szegmensben lévő összes dokumentumra kiterjedő, a teljes szegmensen belüli összes dokumentumban a lekérdezési kifejezés gyakoriságát és az inverz dokumentum gyakoriságát is felszámolja.

Ez azt jelenti, hogy a *relevancia pontszáma* eltérő lehet az azonos dokumentumok esetében, ha különböző szegmensekben találhatók. Szerencsére az ilyen eltérések általában nem tűnnek el, mivel az indexben lévő dokumentumok száma még a hosszabb távú eloszlás miatt is nő. Nem lehet feltételezni, hogy az adott dokumentum melyik szegmensbe kerül. Azonban feltételezve, hogy a dokumentum kulcsa nem változik, mindig ugyanahhoz a szegmenshez lesz hozzárendelve.

Általánosságban elmondható, hogy a dokumentum pontszáma nem a legjobb megoldás a dokumentumok rendezéséhez, ha a megrendelés stabilitása fontos. Ha például két, azonos pontszámmal rendelkező dokumentumot adott meg, akkor nincs garancia arra, hogy az adott lekérdezés későbbi futtatása során az egyik első jelenik meg. A dokumentum pontszáma csak az eredmények készletében lévő többi dokumentumra vonatkozó általános értelemben vett dokumentum-megfelelőséget adja meg.

## <a name="conclusion"></a>Összegzés

Az internetes keresőprogramok sikere miatt a teljes szöveges keresésre vonatkozó elvárások merültek fel a magánjellegű adatokon. Mostantól szinte bármilyen keresési élményhez elvárjuk, hogy a motor megértse a szándékát, még akkor is, ha a feltételek helytelenül vannak írva vagy hiányosak. Az is előfordulhat, hogy az egyezéseket a közel azonos feltételek vagy szinonimák alapján is elvárjuk, amelyeket valójában nem adtunk meg.

Technikai szempontból a teljes szöveges keresés nagyon összetett, és kifinomult nyelvi elemzést és szisztematikus módszert igényel a feldolgozáshoz, amely a lekérdezési kifejezések kiszűrését, kibővítését és átalakítását jelenti egy adott eredmény biztosításához. A bennük rejlő bonyodalmak miatt sok tényező befolyásolja a lekérdezés eredményét. Ebből kifolyólag a teljes szöveges kereséshez szükséges idő befektetve kézzelfogható előnyökkel jár, ha nem várt eredményekkel próbál dolgozni.  

Ez a cikk a teljes szöveges keresést ismerteti az Azure Cognitive Search kontextusában. Reméljük, hogy elegendő hátteret biztosít a gyakori lekérdezési problémák kezelésére vonatkozó lehetséges okok és megoldások felismeréséhez. 

## <a name="next-steps"></a>Következő lépések

+ Hozza létre a minta indexet, próbálja ki a különböző lekérdezéseket, és tekintse át az eredményeket. Útmutatásért lásd: [index létrehozása és lekérdezése a portálon](search-get-started-portal.md#query-index).

+ További lekérdezési szintaxist a [Search Documents](/rest/api/searchservice/search-documents#bkmk_examples) example szakasz vagy az [egyszerű lekérdezési szintaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search) a Search Explorerben a portálon.

+ Tekintse át a [pontozási profilokat](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) , ha a keresési alkalmazásban szeretné hangolni a rangsorolást.

+ Megtudhatja, hogyan alkalmazhat [nyelvi specifikus lexikális elemzőket](/rest/api/searchservice/language-support).

+ [Egyéni elemzők konfigurálása](/rest/api/searchservice/custom-analyzers-in-azure-search) az egyes mezők minimális feldolgozásához vagy speciális feldolgozásához.

## <a name="see-also"></a>Lásd még

[Dokumentumok keresése – REST API](/rest/api/searchservice/search-documents) 

[Egyszerű lekérdezési szintaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Teljes Lucene lekérdezési szintaxis](/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[A keresési eredmények kezelése](./search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png