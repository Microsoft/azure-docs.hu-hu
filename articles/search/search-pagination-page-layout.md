---
title: A keresési eredmények használata
titleSuffix: Azure Cognitive Search
description: A keresési eredmények strukturálása és rendezése, a dokumentumok számának beolvasása, valamint a tartalom navigációjának hozzáadása az Azure Cognitive Search keresési eredményeihez.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: a7171d656ec9f839aea4ae73763ec6ebd20c2bb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98209831"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Keresési eredmények használata az Azure-ban Cognitive Search

Ez a cikk azt ismerteti, hogyan lehet lekérdezési választ kialakítani az Azure Cognitive Searchban. A válasz struktúráját a lekérdezésben szereplő paraméterek határozzák meg: [keresési dokumentum](/rest/api/searchservice/Search-Documents) a REST API vagy a [SearchResults OSZTÁLYBAN](/dotnet/api/azure.search.documents.models.searchresults-1) a .net SDK-ban. A lekérdezés paramétereinek használatával az alábbi módokon strukturálható az eredményhalmaz:

+ Az eredményekben szereplő dokumentumok számának korlátozása vagy kötegbe állítása (alapértelmezés szerint 50)
+ Válassza ki az eredményekbe felvenni kívánt mezőket
+ Sorrend eredményei
+ A keresési eredmények törzsében lévő teljes vagy részleges kifejezés kiemelése

## <a name="result-composition"></a>Eredmény összetétele

Míg a keresési dokumentumok nagy mennyiségű mezőt tartalmazhatnak, jellemzően csak néhányra van szükség az eredményhalmaz összes dokumentumának ábrázolásához. Egy lekérdezési kérelemnél hozzáfűzéssel `$select=<field list>` határozza meg, hogy mely mezők jelenjenek meg a válaszban. Egy mezőnek az indexben **beolvasható** értékként kell szerepelnie az eredményben. 

A legjobban megfelelő mezők közé tartoznak a dokumentumok közötti kontrasztos és megkülönböztethető elemek, így elegendő információ áll rendelkezésre a felhasználó részéről egy kattintásra adott válasz meghívásához. Az e-kereskedelmi webhelyeken a terméknév, a leírás, a márka, a szín, a méret, az ár és a minősítés lehet. A Hotel-Sample-index beépített mintája a következő példában szereplő mezőkből állhat:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Ha a képfájlokat egy adott eredményben szeretné megadni, például egy termék fényképét vagy emblémáját, az Azure Cognitive Searchon kívül tárolja őket, de tartalmaz egy mezőt az indexben a keresési dokumentumban található kép URL-címére való hivatkozáshoz. Az eredményekben szereplő képeket támogató minta indexek közé tartozik a **Realestate-Sample-US** bemutató, amely a rövid útmutatóban [szerepel, a](search-create-app-portal.md) [New York City Jobs bemutató alkalmazásban](https://aka.ms/azjobsdemo).

### <a name="tips-for-unexpected-results"></a>Váratlan eredményekhez kapcsolódó tippek

Alkalmanként az anyag és az eredmények szerkezete nem várt. Ha a lekérdezés eredményei váratlanok, a lekérdezés módosításainak kipróbálásával megtekintheti, hogy az eredmények javulnak-e:

+ Módosítsa az (alapértelmezett) beállítást, hogy az összes feltétel esetében kötelező legyen az **`searchMode=any`** **`searchMode=all`** egyezések megadása a feltételek helyett. Ez különösen akkor igaz, ha a lekérdezésben logikai operátorok szerepelnek.

+ Kísérletezzen különböző lexikális elemzők vagy egyéni elemzők használatával, és ellenőrizze, hogy a lekérdezés eredménye megváltozik-e. Az alapértelmezett elemző elválasztja a Kötőjeles szavakat, és csökkenti a szavakat a legfelső szintű űrlapokra, ami általában növeli a lekérdezési válaszok megbízhatóságát. Ha azonban meg kell őriznie a kötőjeleket, vagy ha a karakterláncok speciális karaktereket tartalmaznak, előfordulhat, hogy egyéni elemzőket kell beállítania annak biztosításához, hogy az index a megfelelő formátumban tartalmazza a jogkivonatokat. További információ: [részleges kifejezéses keresés és minták speciális karakterekkel (kötőjelek, helyettesítő karakterek, regex, mintázatok)](search-query-partial-matching.md).

## <a name="paging-results"></a>Lapozás eredményei

Alapértelmezés szerint a keresőmotor az első 50 egyezést adja vissza, amelyet a keresési pontszám határoz meg, ha a lekérdezés teljes szöveges keresést használ, vagy tetszőleges sorrendben a pontos egyezésű lekérdezésekhez.

Ha más számú egyező dokumentumot szeretne visszaadni, adja hozzá `$top` `$skip` a lekérdezési kérelemhez a és a paramétereket. A következő lista ismerteti a logikát.

+ A Hozzáadás gombra kattintva megadhatja `$count=true` Az indexen belüli egyező dokumentumok teljes számát.

+ A 15 egyező dokumentum első készletének visszaadása, valamint a teljes egyezések száma: `GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Adja vissza a második készletet, kihagyva az első 15 értéket a következő 15: érték beszerzéséhez `$top=15&$skip=15` . Tegye meg ugyanezt a 15. harmadik készletnél: `$top=15&$skip=30`

A többoldalas lekérdezések eredményei nem garantálják, hogy stabilak legyenek, ha az alapul szolgáló index változik. A lapozás megváltoztatja az `$skip` egyes lapok értékeit, de az egyes lekérdezések függetlenek, és az adatok aktuális nézetén működnek, mivel a lekérdezési időpontban már szerepel az indexben (vagyis nem áll rendelkezésre az eredmények gyorsítótárazása vagy pillanatképe, például egy általános célú adatbázisban található).
 
Az alábbi példa bemutatja, hogyan lehet ismétlődéseket kapni. Négy dokumentummal rendelkező index feltételezése:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
Most tegyük fel, hogy az eredményeket a rendszer egy időben, a minősítés alapján rendezi. Ezt a lekérdezést kell végrehajtania az eredmények első oldalának beolvasásához: `$top=2&$skip=0&$orderby=rating desc` a következő eredmények előállításával:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
A szolgáltatásban tegyük fel, hogy egy ötödik dokumentum kerül a lekérdezési hívások közötti indexbe: `{ "id": "5", "rating": 4 }` .  Röviddel azután, hogy végrehajt egy lekérdezést a második lap beolvasásához: `$top=2&$skip=2&$orderby=rating desc` , és a következő eredményeket kapja:

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
Figyelje meg, hogy a 2. dokumentum kétszer van beolvasva. Ennek az az oka, hogy az 5. dokumentum új minősítési értéke nagyobb, ezért rendezi a 2. dokumentum és az első oldalon landolás előtt. Habár ez a viselkedés váratlan lehet, jellemző, hogy a keresőmotor hogyan viselkedik.

## <a name="ordering-results"></a>Az eredmények rendezése

A teljes szöveges keresési lekérdezések esetében a találatok automatikusan a keresési pontszám alapján vannak rangsorolva, a kifejezés gyakorisága és a ( [TF-IDF-](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)ből származó) dokumentum közelsége alapján számítva, és a keresési kifejezéssel több vagy erősebb egyezést eredményező dokumentumokat fog keresni. 

A keresési eredmények általános jelentőséggel bírnak, ami az egyező eredményhalmaz más dokumentumaihoz viszonyított erősségét tükrözi. A pontszámok azonban nem mindig konzisztensek az egyik lekérdezéstől a következőig, így a lekérdezések használatakor kis eltérések jelenhetnek meg a keresési dokumentumok megrendelése során. A probléma oka több magyarázat is lehet.

| Ok | Leírás |
|-----------|-------------|
| Adatvolatilitás | Az indexelési tartalom a dokumentumok hozzáadása, módosítása vagy törlése során változik. A kifejezés gyakorisága módosul, mert az index frissítései időben lesznek feldolgozva, ami hatással van a megfelelő dokumentumok keresési pontjaira. |
| Több replika | Több replikát használó szolgáltatások esetén a lekérdezéseket párhuzamosan kell kiadni az egyes replikákkal. A keresési pontszám kiszámításához használt index statisztikáit a rendszer replika alapon számítja ki, a lekérdezési válaszban egyesítve és elrendezve az eredményeket. A replikák többnyire egymást tükrözik, de a statisztikák eltérőek lehetnek az állami különbségek miatt. Előfordulhat például, hogy az egyik replika törölte a statisztikához hozzájáruló dokumentumokat, amelyek más replikából lettek egyesítve. A replikák statisztikái közötti különbségek jellemzően kisebb indexekben figyelhetők meg. |
| Azonos pontszámok | Ha több dokumentum ugyanazzal a pontszámmal rendelkezik, akkor előfordulhat, hogy ezek közül bármelyik előbb megjelenik.  |

### <a name="how-to-get-consistent-ordering"></a>Konzisztens sorrend beszerzése

Ha a konzisztens sorrend egy alkalmazásra vonatkozó követelmény, explicit módon megadhat egy [ **`$orderby`** kifejezést](query-odata-filter-orderby-syntax.md) egy mezőhöz. Csak az indexelt mezők **`sortable`** használhatók az eredmények megrendeléséhez. Az **`$orderby`** include minősítés, a dátum és a hely mezőkben gyakran használt mezők, ha megadja a paraméter értékét, **`orderby`** hogy tartalmazza a mezőneveket és a [**`geo.distance()` függvénynek**](query-odata-filter-orderby-syntax.md) a térinformatikai értékek meghívását.

Egy másik megközelítés, amely elősegíti a konzisztenciát, [Egyéni pontozási profilt](index-add-scoring-profiles.md)használ. A pontozási profilok nagyobb mértékben szabályozzák a keresési eredményekben lévő elemek rangsorolását, és lehetővé teszi az egyes mezőkben található egyezések növelését. A további pontozási logika segíthet felülbírálni a replikák közötti kisebb különbségeket, mivel az egyes dokumentumokhoz tartozó keresési pontszámok egymástól eltérőek lehetnek. Az ehhez a megközelítéshez tartozó [rangsorolási algoritmust](index-ranking-similarity.md) ajánljuk.

## <a name="hit-highlighting"></a>Találatok kiemelése

A találatok kiemelése olyan szövegformázást jelent (például félkövér vagy sárga kiemelés), amely a megfelelő kifejezésekre vonatkozik, így könnyen megoldható a egyezés. A [lekérdezési kérelem](/rest/api/searchservice/search-documents)a találatok kiemelésére vonatkozó utasításokat tartalmaz. 

A találatok kiemelésének engedélyezéséhez adja hozzá `highlight=[comma-delimited list of string fields]` a elemet annak megadásához, hogy mely mezők legyenek kiemelve. A kiemelés hasznos lehet a több tartalom mezőhöz, például a Leírás mezőhöz, ahol az egyeztetés nem azonnal nyilvánvaló. Csak a találatok kiemeléséhez **kereshető** minősítéssel rendelkező mezők definíciói láthatók.

Alapértelmezés szerint az Azure Cognitive Search egy mezőn legfeljebb öt kiemelést ad vissza. Ezt a számot úgy állíthatja be, hogy hozzáfűzi a kötőjelet egy egész számmal kiegészítve. A (z `highlight=Description-10` ) például a Description (Leírás) mezőben szereplő tartalomra vonatkozó, legfeljebb 10 kiemelést ad vissza.

A formázás a teljes távú lekérdezésekre vonatkozik. A formázás típusát a címkék határozzák meg, `highlightPreTag` és `highlightPostTag` a kód kezeli a választ (például félkövér betűtípust vagy sárga hátteret alkalmaz).

A következő példában a Leírás mezőben a "Sandy", a "Sand", a "strandok", a "Beach" kifejezés a kiemeléshez van címkézve. Azok a lekérdezések, amelyek kiváltják a lekérdezés kiterjesztését a motorban, például a fuzzy és a helyettesítő karakteres keresés, korlátozott támogatást biztosítanak a találatok kiemeléséhez.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Új viselkedés (július 1-től)

A 2020. július 15. után létrehozott szolgáltatások eltérő kiemelési élményt nyújtanak. Az ezen dátum előtt létrehozott szolgáltatások nem változnak a kiemelési viselkedésben. 

Az új viselkedéssel:

* A rendszer csak a teljes kifejezéses lekérdezésnek megfelelő kifejezéseket adja vissza. A "Super Bowl" lekérdezés a következőhöz hasonló csúcsfényeket ad vissza:

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  Vegye figyelembe, hogy a *zsetonok* nem rendelkeznek kiemeléssel, mert nem egyezik a teljes kifejezéssel.

Ha olyan állapotkódot ír, amely megvalósítja a találatok kiemelését, vegye figyelembe a változást. Vegye figyelembe, hogy ez csak akkor lesz hatással, ha teljesen új keresési szolgáltatást hoz létre.

## <a name="next-steps"></a>Következő lépések

Az ügyfél keresési oldalának gyors létrehozásához vegye figyelembe a következő lehetőségeket:

+ Az [Application Generator](search-create-app-portal.md)a portálon egy olyan HTML-oldalt hoz létre, amelyben a keresősáv, a csiszolt navigálás és az eredmények területen képeket tartalmaz.
+ Az [első alkalmazás létrehozása a C#-ban](tutorial-csharp-create-first-app.md) egy olyan oktatóanyag, amely egy funkcionális ügyfelet épít ki. A mintakód a többoldalas lekérdezéseket, a találatok kiemelését és a rendezést mutatja be.

Több mintakód is tartalmaz egy webes előtér-felületet, amely itt található: [New York City Jobs bemutató alkalmazás](https://aka.ms/azjobsdemo), [JavaScript-mintakód élő bemutató webhellyel](https://github.com/liamca/azure-search-javascript-samples)és [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).
