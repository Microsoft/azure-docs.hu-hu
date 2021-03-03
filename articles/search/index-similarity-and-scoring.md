---
title: Hasonlóság és pontozás – áttekintés
titleSuffix: Azure Cognitive Search
description: Ismerteti a hasonlóság és a pontozás fogalmát, valamint azt, hogy mit tehet a fejlesztő a pontozási eredmények testreszabásához.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 72243f896b2cf7dbab61a42514bee634da28d4c6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676329"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Hasonlóság és pontozás az Azure Cognitive Search

Ez a cikk az Azure Cognitive Search két hasonlósági rangsorolási algoritmusát ismerteti. Emellett két kapcsolódó funkciót is bevezet: *pontozási profilok* (keresési pontszám módosításának feltételei) és a *featuresMode* paraméter (keresési pontszám kicsomagolása további részletek megjelenítéséhez). 

Egy harmadik szemantikai újrarangsoroló algoritmus jelenleg nyilvános előzetes verzióban érhető el. További információkért tekintse meg a [szemantikai keresés áttekintését](semantic-search-overview.md).

## <a name="similarity-ranking-algorithms"></a>Hasonlósági rangsorolási algoritmusok

Az Azure Cognitive Search két hasonló rangsorolási algoritmust támogat.

| Algoritmus | Pontszám | Rendelkezésre állás |
|-----------|-------|--------------|
| ClassicSimilarity | @search.score | Az összes keresési szolgáltatás használja, 2020. július 15-ig. |
| BM25Similarity | @search.score | Az összes, a július 15. után létrehozott keresési szolgáltatás használja. A klasszikus alapértéket használó régebbi szolgáltatások a [BM25 is választhatják](index-ranking-similarity.md). |

A klasszikus és a BM25 olyan TF-IDF-szerű lekérési függvények, amelyek a kifejezés gyakoriságát (TF) és az inverz dokumentum-gyakoriságot (IDF) használják változókként az egyes dokumentumokhoz kapcsolódó pontszámok kiszámításához, amelyet a rendszer az egyes dokumentumok lekérdezési párjaként használ, és a klasszikushoz hasonlóan a BM25 veszi át a gyökerét, hogy a lehető legtöbbet hozza A BM25 speciális testreszabási lehetőségeket is kínál, például lehetővé teszi a felhasználó számára, hogy eldöntse, hogyan méretezi a relevancia pontszámát a megfeleltetett kifejezések gyakoriságával.

A következő videó szegmense gyorsan továbbítható az Azure Cognitive Searchban használt általánosan elérhető rangsorolási algoritmusok magyarázatával. További háttérként tekintse meg a teljes videót.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

## <a name="relevance-scoring"></a>Relevanciapontozás

A pontozás a teljes szöveges keresési lekérdezések keresési eredményei között visszaadott keresési pontszámok kiszámítására vonatkozik. A pontszám az aktuális lekérdezés kontextusában az elemek relevanciáját jelzi. Minél nagyobb a pontszám, annál relevánsabb az elem. A keresési eredmények között az elemek sorrendje magasról alacsonyra van rendezve, az egyes elemek kiszámított keresési pontszáma alapján. A rendszer a "" értéket adja vissza @search.score minden dokumentumon.

Alapértelmezés szerint a rendszer az első 50-as értéket adja vissza a válaszban, de a **$Top** paraméterrel kisebb vagy nagyobb számú elemet adhat vissza (legfeljebb 1000 egyetlen válaszban), és **$skip** a következő eredmények beszerzéséhez.

A keresési pontszám kiszámítása az adatok és a lekérdezés statisztikai tulajdonságai alapján történik. Az Azure Cognitive Search megkeresi azokat a dokumentumokat, amelyek megfelelnek a keresési kifejezéseknek ( [searchMode](/rest/api/searchservice/search-documents#query-parameters)függően), a keresési kifejezés számos példányát tartalmazó dokumentumokat. A keresési pontszám még magasabbra nő, ha a kifejezés az adatindexben ritkán előfordul, de a dokumentumon belül is. A számítástechnikai szempontoknak való megfelelés alapja a *TF-IDF vagy* a kifejezés gyakorisága – fordított dokumentum gyakorisága.

A keresési pontszám értékei megismételhetők egy eredményhalmaz során. Ha több találat azonos keresési pontszámmal rendelkezik, az azonos pontszámú elemek sorrendje nincs definiálva, és nem stabil. Futtassa újra a lekérdezést, és előfordulhat, hogy az elemek eltolási pozíciója látható, különösen akkor, ha az ingyenes szolgáltatást vagy egy számlázható szolgáltatást több replikával használ. Az azonos pontszámú két elem esetében nincs garancia arra, hogy az egyik első megjelenjen.

Ha meg szeretné szüntetni a döntetlent az ismétlődő pontszámok között, hozzáadhat egy **$OrderBy** záradékot az első sorrend szerint a pontszám szerint, majd egy másik rendezhető mező szerint (például: `$orderby=search.score() desc,Rating desc` ). További információ: [$OrderBy](search-query-odata-orderby.md).

> [!NOTE]
> A egy nem `@search.score = 1.00` pontszámmal ellátható vagy nem rangsorolt eredményhalmaz. A pontszám egységes az összes eredményben. A nem pontozásos eredmények akkor fordulnak elő, ha a lekérdezési űrlap nem intelligens keresés, helyettesítő karakter vagy regex lekérdezés, vagy egy **$Filter** kifejezés.

<a name="scoring-statistics"></a>

## <a name="scoring-statistics-and-sticky-sessions"></a>Pontozási statisztika és Sticky-munkamenetek

A méretezhetőség érdekében az Azure Cognitive Search horizontálisan osztja el az egyes indexeket egy horizontális Felskálázási folyamaton keresztül, ami azt jelenti, hogy [az index részei fizikailag elkülönítve vannak](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

Alapértelmezés szerint a rendszer a dokumentum pontszámát a szegmensen *belüli* adatstatisztikai tulajdonságok alapján számítja ki. Ez a megközelítés általában nem jelent problémát a nagy mennyiségű adat esetében, és jobb teljesítményt nyújt, mint a pontszám kiszámításához az összes szegmens információi alapján. Ez azt eredményezte, hogy ez a teljesítmény-optimalizálás két nagyon hasonló dokumentumot (vagy akár azonos dokumentumokat) is okozhat, amelyek a különböző szegmensekben való befejezéskor különböző releváns pontszámokkal rendelkeznek.

Ha az összes szegmens statisztikai tulajdonságai alapján szeretné kiszámítani a pontszámot, ezt a *scoringStatistics = Global* [lekérdezési paraméterként](/rest/api/searchservice/search-documents) való hozzáadásával teheti meg (vagy a *"scoringStatistics": "Global"* értéket adja hozzá a [lekérdezési kérelem](/rest/api/searchservice/search-documents)törzsének paraméteréhez).

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

A scoringStatistics használatával biztosítható, hogy az azonos replika összes szegmense ugyanazt az eredményt adja. Ez azt jelentette, hogy a különböző replikák némileg eltérőek lehetnek egymástól, mivel mindig frissülnek az index legutóbbi változásaival. Bizonyos esetekben előfordulhat, hogy a felhasználók több konzisztens eredményt kapnak a "lekérdezési munkamenet" során. Ilyen esetekben a lekérdezések részeként is megadható `sessionId` . Az egy egyedi `sessionId` karakterlánc, amelyet a rendszer egy egyedi felhasználói munkamenetre való hivatkozással hoz létre.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?sessionId=[string]&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

Ha ugyanezt használja, a rendszer a `sessionId` legjobb erőfeszítést kísérli meg ugyanarra a replikára, és a felhasználók által megjelenő eredmények konzisztenciájának növelését fogja látni. 

> [!NOTE]
> Ha ismételten ugyanazt az értéket használja, a `sessionId` megzavarhatja a kérelmek terheléselosztását a replikák között, és negatív hatással lehet a keresési szolgáltatás teljesítményére. A munkamenet-azonosítóval használt érték nem kezdődhet "_" karakterrel.

## <a name="scoring-profiles"></a>Pontozási profilok

Egy *pontozási profil* definiálásával testre szabhatja a különböző mezők rangsorolásának módját. A pontozási profilok nagyobb mértékben szabályozzák a keresési eredményekben lévő elemek rangsorolását. Előfordulhat például, hogy a bevételi potenciál alapján szeretné növelni az elemeket, előléptetheti az újabb elemeket, vagy növelheti az olyan elemeket, amelyek túl hosszúak voltak a leltárban. 

A pontozási profil az index definíciójának részét képezi, amely a súlyozott mezőkből, függvényekből és paraméterekből áll. A definiálásával kapcsolatos további információkért lásd: [pontozási profilok](index-add-scoring-profiles.md).

<a name="featuresMode-param"></a>

## <a name="featuresmode-parameter-preview"></a>featuresMode paraméter (előzetes verzió)

A [keresési dokumentumok](/rest/api/searchservice/preview-api/search-documents) egy új [featuresMode](/rest/api/searchservice/preview-api/search-documents#featuresmode) -paraméterrel rendelkeznek, amely további részleteket biztosít a mező szintű relevancia vonatkozásában. Míg a `@searchScore` teljes dokumentum kiszámításának alapja (ez a dokumentum a lekérdezés kontextusában található), a featuresMode-on keresztül az egyes mezőkre vonatkozó információkat az adott struktúrában látható módon lehet lekérdezni `@search.features` . A struktúra tartalmazza a lekérdezésben használt összes mezőt (vagy egy lekérdezés **searchFields** keresztül adott mezőket, vagy az összes olyan mezőt, amely az indexben **kereshető** ). Az egyes mezőknél a következő értékeket kapja:

+ A mezőben található egyedi tokenek száma
+ Hasonlósági pontszám vagy a mező tartalmához hasonló mérték, a lekérdezési kifejezéshez viszonyítva
+ A kifejezés gyakorisága, illetve a lekérdezési kifejezésnek a mezőben található száma

Egy olyan lekérdezéshez, amely a "Leírás" és a "title" mezőket célozza meg, a következőkhöz `@search.features` hasonló válasz jelenhet meg:

```json
"value": [
 {
    "@search.score": 5.1958685,
    "@search.features": {
        "description": {
            "uniqueTokenMatches": 1.0,
            "similarityScore": 0.29541412,
            "termFrequency" : 2
        },
        "title": {
            "uniqueTokenMatches": 3.0,
            "similarityScore": 1.75451557,
            "termFrequency" : 6
        }
```

Ezeket az adatpontokat [Egyéni pontozási megoldásokban](https://github.com/Azure-Samples/search-ranking-tutorial) is felhasználhatja, vagy az adatokat felhasználhatja a kereséssel kapcsolatos problémák hibakereséséhez.

## <a name="see-also"></a>Lásd még

+ [Pontozási profilok](index-add-scoring-profiles.md)
+ [REST API referenciája](/rest/api/searchservice/)
+ [Dokumentumok keresése API](/rest/api/searchservice/search-documents)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search)