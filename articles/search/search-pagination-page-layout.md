---
title: A keresési eredmények használata
titleSuffix: Azure Cognitive Search
description: A keresési eredmények strukturálása és rendezése, a dokumentumok számának beolvasása, valamint a tartalom navigációjának hozzáadása az Azure Cognitive Search keresési eredményeihez.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 08641814e2a4fdf6f174f94b1e38e4124cf531d0
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934922"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Keresési eredmények használata az Azure-ban Cognitive Search

Ez a cikk azt ismerteti, hogyan kérhet le egy lekérdezési választ, amely a megfelelő dokumentumok teljes számával, a többoldalas eredményekkel, a rendezett eredményekkel és a találatok kiemelésével kapcsolatos feltételeket tartalmaz.

A válasz struktúráját a lekérdezésben szereplő paraméterek határozzák meg: [keresési dokumentum](/rest/api/searchservice/Search-Documents) a REST API vagy a [DocumentSearchResult OSZTÁLYBAN](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) a .net SDK-ban.

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

A teljes szöveges keresési lekérdezések esetében a találatok automatikusan egy keresési pontszám szerint vannak rangsorolva, a kifejezés gyakorisága és a dokumentum közelsége alapján számítva, és a keresési kifejezéssel több vagy erősebb egyezést eredményező dokumentumokat fog keresni. 

A keresési eredmények általános jelentőséggel bírnak, amely az azonos eredményhalmaz más dokumentumaihoz képest az egyezés erősségét tükrözi. A pontszámok nem mindig konzisztensek az egyik lekérdezéstől a következőig, így a lekérdezések használatakor előfordulhat, hogy a keresési dokumentumok rendezésének módja kis eltéréseket észlel. A probléma oka több magyarázat is lehet.

| Ok | Leírás |
|-----------|-------------|
| Adatvolatilitás | Az indexelési tartalom a dokumentumok hozzáadása, módosítása vagy törlése során változik. A kifejezés gyakorisága módosul, mert az index frissítései időben lesznek feldolgozva, ami hatással van a megfelelő dokumentumok keresési pontjaira. |
| Több replika | Több replikát használó szolgáltatások esetén a lekérdezéseket párhuzamosan kell kiadni az egyes replikákkal. A keresési pontszám kiszámításához használt index statisztikáit a rendszer replika alapon számítja ki, a lekérdezési válaszban egyesítve és elrendezve az eredményeket. A replikák többnyire egymást tükrözik, de a statisztikák eltérőek lehetnek az állami különbségek miatt. Előfordulhat például, hogy az egyik replika törölte a statisztikához hozzájáruló dokumentumokat, amelyek más replikából lettek egyesítve. A replikák statisztikái közötti különbségek jellemzően kisebb indexekben figyelhetők meg. |
| Azonos pontszámok | Ha több dokumentum ugyanazzal a pontszámmal rendelkezik, akkor előfordulhat, hogy ezek közül bármelyik előbb megjelenik.  |

### <a name="consistent-ordering"></a>Konzisztens sorrend

Ha az eredmények rendezésében a Flex szerepel, érdemes lehet más lehetőségeket is megvizsgálni, ha az egységesség egy alkalmazásra vonatkozó követelmény. A legegyszerűbb módszer a mezőérték, például a minősítés vagy a dátum szerinti rendezés. Olyan esetekben, ahol egy adott mező, például egy minősítés vagy dátum alapján szeretne rendezni, explicit módon meghatározhat egy [ `$orderby` kifejezést](query-odata-filter-orderby-syntax.md), amely a **rendezhető**indexelt bármely mezőre alkalmazható.

Egy másik lehetőség [Egyéni pontozási profilt](index-add-scoring-profiles.md)használ. A pontozási profilok nagyobb mértékben szabályozzák a keresési eredményekben lévő elemek rangsorolását, és lehetővé teszi az egyes mezőkben található egyezések növelését. A további pontozási logika segíthet felülbírálni a replikák közötti kisebb különbségeket, mivel az egyes dokumentumokhoz tartozó keresési pontszámok egymástól eltérőek lehetnek. Az ehhez a megközelítéshez tartozó [rangsorolási algoritmust](index-ranking-similarity.md) ajánljuk.

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

## <a name="next-steps"></a>További lépések

Az ügyfél keresési oldalának gyors létrehozásához vegye figyelembe a következő lehetőségeket:

+ Az [Application Generator](search-create-app-portal.md)a portálon egy olyan HTML-oldalt hoz létre, amelyben a keresősáv, a csiszolt navigálás és az eredmények területen képeket tartalmaz.
+ Az [első alkalmazás létrehozása a C#-ban](tutorial-csharp-create-first-app.md) egy olyan oktatóanyag, amely egy funkcionális ügyfelet épít ki. A mintakód a többoldalas lekérdezéseket, a találatok kiemelését és a rendezést mutatja be.

Több mintakód is tartalmaz egy webes előtér-felületet, amely itt található: [New York City Jobs bemutató alkalmazás](https://aka.ms/azjobsdemo), [JavaScript-mintakód élő bemutató webhellyel](https://github.com/liamca/azure-search-javascript-samples)és [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).