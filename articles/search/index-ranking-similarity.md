---
title: A hasonlósági algoritmus konfigurálása
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan engedélyezheti a BM25 a régebbi keresési szolgáltatásokban, és hogyan módosítható a BM25 paraméterek, hogy jobban megfeleljen az indexek tartalmának.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 52b3523d3c092f1b9375f53038cc3b20d0ddedcc
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232834"
---
# <a name="configure-the-similarity-ranking-algorithm-in-azure-cognitive-search"></a>A hasonlósági rangsorolási algoritmus konfigurálása az Azure-ban Cognitive Search

Az Azure Cognitive Search két hasonlósági besorolási algoritmust támogat:

+ Egy *klasszikus hasonlósági* algoritmus, amelyet az összes keresési szolgáltatás a 2020-ig terjedően használ.
+ A *OKAPI BM25* algoritmus implementációja, amelyet az összes, a július 15. után létrehozott keresési szolgáltatásban használ.

A BM25 ranking az új alapértelmezett érték, mivel általában olyan keresési rangsorokat hoz létre, amelyek jobban illeszkednek a felhasználói elvárásokhoz. A [Paraméterek](#set-bm25-parameters) az eredmények hangolásához használhatók, például a dokumentum méretétől függően. 

A 2020. július 15. után létrehozott új szolgáltatások esetében az BM25 automatikusan használatos, és az egyetlen hasonlósági algoritmus. Ha a ClassicSimilarity hasonlóságot próbál beállítani egy új szolgáltatáson, a rendszer HTTP 400-es hibát ad vissza, mert a szolgáltatás nem támogatja ezt az algoritmust.

A 2020. július 15. előtt létrehozott régebbi szolgáltatások esetében a klasszikus hasonlóság az alapértelmezett algoritmus marad. A régebbi szolgáltatások az alábbi módon frissíthetik a BM25-ra index alapján. Ha klasszikusról BM25-re vált, a keresési eredmények sorrendjét is láthatja.

> [!NOTE]
> A kiválasztott régiókban a standard szolgáltatások esetében jelenleg előzetes verzióban elérhető szemantikai rangsor további előrelépést eredményez a további releváns eredmények előállításában. A többi algoritmustól eltérően ez egy kiegészítő funkció, amely egy meglévő eredményhalmaz megismétlésére szolgál. További információ: [szemantikai keresés áttekintése](semantic-search-overview.md) és [szemantikai rangsorolás](semantic-ranking.md).

## <a name="enable-bm25-scoring-on-older-services"></a>BM25-pontozás engedélyezése a régebbi szolgáltatásokban

Ha olyan keresési szolgáltatást futtat, amely 2020. július 15. előtt lett létrehozva, akkor engedélyezheti a BM25 az új indexek hasonlósági tulajdonságának beállításával. A tulajdonság csak az új indexeken érhető el, így ha egy meglévő indexre szeretné BM25, el kell dobnia és [újra kell építenie az indexet](search-howto-reindex.md) egy új hasonlóság tulajdonsággal, amely a "Microsoft. Azure. Search. BM25Similarity" értékre van állítva.

Ha egy index hasonlósági tulajdonsággal rendelkezik, válthat a BM25Similarity vagy a ClassicSimilarity között. 

Az alábbi hivatkozások az Azure SDK-k hasonlósági tulajdonságát írják le. 

| Ügyfélkódtár | Hasonlósági tulajdonság |
|----------------|---------------------|
| .NET  | [SearchIndex. hasonlóság](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex.setSimilarity](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [SearchIndex. hasonlóság](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [a SearchIndex hasonlósági tulajdonsága](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>REST-példa

Használhatja a [REST API](/rest/api/searchservice/create-index)is, ahogy az alábbi példa szemlélteti:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

## <a name="set-bm25-parameters"></a>BM25 paramétereinek beállítása

A BM25 hasonlósága két felhasználó által testreszabható paramétert hoz létre a számított relevanciás pontszám szabályozása érdekében. A BM25 paramétereket az index létrehozásakor, vagy index-frissítésként is megadhatja, ha a BM25 algoritmus meg lett adva az index létrehozásakor.

| Tulajdonság | Típus | Leírás |
|----------|------|-------------|
| K1 csomag | szám | A méretezési függvényt a dokumentum-lekérdezési pár végső relevanciás pontszáma alapján szabályozza. Az értékek általában 0,0 – 3,0, az 1,2 alapértelmezett értékkel. </br></br>A 0,0 érték a "bináris modell" értéket jelöli, ahol az egyetlen egyező kifejezés hozzájárulása megegyezik az összes egyező dokumentummal, függetlenül attól, hogy a kifejezés hány alkalommal jelenik meg a szövegben, míg egy nagyobb K1-érték lehetővé teszi, hogy a pontszám tovább növekedjen, mivel az adott időszak több példánya is megtalálható a dokumentumban. </br></br>A magasabb K1-értékek használata olyan esetekben lehet fontos, amikor egy keresési lekérdezésbe több kifejezés is beleszámít. Ezekben az esetekben érdemes lehet olyan dokumentumokat használni, amelyek megfelelnek a különböző lekérdezési kifejezéseknek, amelyek csak egy-egy, több alkalommal egyeznek meg a dokumentumokban. Ha például az "Apollo űrrepülés" kifejezést tartalmazó dokumentumok indexét kérdezi le, érdemes lehet csökkenteni a görög mitológiáról szóló cikk pontszámát, amely az "Apollo" kifejezést tartalmazza néhány tucat alkalommal, a "űrrepülés" kifejezés nélkül, egy másik cikkhez képest, amely kifejezetten megemlíti az "Apollo" és a "űrrepülés" fogalmát. |
| b | szám | Azt szabályozza, hogy a dokumentum hossza hogyan befolyásolja a relevancia pontszámát. Az értékek 0 és 1 közöttiek, az alapértelmezett érték a 0,75. </br></br>A 0,0 érték azt jelenti, hogy a dokumentum hossza nem befolyásolja a pontszámot, míg a 1,0 érték azt jelenti, hogy a dokumentum hosszának a kifejezésre gyakorolt hatását a rendszer normalizálja. </br></br>A kifejezés gyakoriságának normalizálása a dokumentum hosszának megfelelően olyan esetekben hasznos, amikor azt szeretnénk, hogy szankcionáljuk a hosszabb dokumentumokat. Bizonyos esetekben a hosszabb dokumentumok (például egy teljes regény) nagyobb valószínűséggel tartalmaznak sok releváns kifejezést a sokkal rövidebb dokumentumokhoz képest. |

### <a name="setting-k1-and-b-parameters"></a>A K1 és a b paraméterek beállítása

A b vagy K1 értékek beállításához vagy módosításához adja hozzá őket a BM25 hasonlósági objektumhoz. Ha egy meglévő indexben beállítja vagy megváltoztatja ezeket az értékeket, a rendszer legalább néhány másodpercig offline állapotba helyezi az indexet, ami miatt az aktív indexelési és lekérdezési kérelmek sikertelenek lesznek. Ezért állítsa be a frissítési kérelem "allowIndexDowntime = true" paraméterét:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30&allowIndexDowntime=true
{
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
}
```

## <a name="see-also"></a>Lásd még  

+ [REST API referenciája](/rest/api/searchservice/)
+ [Pontozási profilok hozzáadása az indexhez](index-add-scoring-profiles.md)
+ [Index API létrehozása](/rest/api/searchservice/create-index)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search)