---
title: Rangsor hasonlósági algoritmusa
titleSuffix: Azure Cognitive Search
description: A hasonlósági algoritmus beállítása az új hasonlósági algoritmus kipróbálható a rangsoroláshoz
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: e2caa09d41abb1842100ed8259e82ec411390ccb
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520629"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Rangsorolási algoritmus az Azure Cognitive Search

> [!IMPORTANT]
> 2020. július 15-étől az újonnan létrehozott keresési szolgáltatások automatikusan a BM25 ranking függvényt fogják használni, amely a legtöbb esetben olyan keresési rangsorok biztosítására szolgál, amelyek a jelenlegi alapértelmezett rangsorolásnál jobban illeszkednek a felhasználói elvárásokhoz. A kiváló rangsoroláson felül a BM25 az eredmények hangolására szolgáló konfigurációs beállításokat is lehetővé teszi az olyan tényezők alapján, mint a dokumentumok mérete.  
>
> Ennek a változásnak a hatására a keresési eredmények sorrendjében valószínűleg kisebb változások jelennek meg. Azok számára, akik szeretnék kipróbálni a változás hatását, a BM25 algoritmus az API-Version 2019-05-06 – Preview és a 2020-06-30-es verzióban érhető el.  

Ez a cikk azt ismerteti, hogyan használhatók a meglévő keresési szolgáltatások új BM25 ranking algoritmusa az előzetes verziójú API-val létrehozott és lekérdezett új indexekhez.

Az Azure Cognitive Search a Okapi BM25 algoritmus ( *BM25Similarity*) hivatalos Lucene implementációjának bevezetésének folyamata, amely a korábban használt *ClassicSimilarity* -implementációt váltja fel. A régebbi ClassicSimilarity algoritmushoz hasonlóan a BM25Similarity egy olyan TF-IDF-szerű lekérési függvény, amely a kifejezés gyakoriságát (TF) és az inverz dokumentum gyakoriságát (IDF) használja változókként az egyes dokumentumokhoz kapcsolódó pontszámok kiszámításához, amelyet aztán a rangsoroláshoz használ a rendszer. 

A régebbi klasszikus hasonlósági algoritmushoz hasonlóan elméletileg a BM25 a legfelső szintű, valószínűségi adatok lekérését is felhasználja a fejlesztéshez. A BM25 speciális testreszabási lehetőségeket is kínál, például lehetővé teszi a felhasználó számára, hogy eldöntse, hogyan méretezi a relevancia pontszámát a megfeleltetett kifejezések gyakoriságával.

## <a name="how-to-test-bm25-today"></a>A BM25 tesztelése még ma

Új index létrehozásakor beállíthatja a **hasonlóság** tulajdonságot az algoritmus megadásához. Használhatja a t az `api-version=2019-05-06-Preview` alább látható módon, vagy `api-version=2020-06-30` .

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
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

A **hasonlósági** tulajdonság ebben az átmeneti időszakban hasznos, ha mindkét algoritmus elérhető, csak a meglévő szolgáltatásokban. 

| Tulajdonság | Leírás |
|----------|-------------|
| hasonlóság | Választható. Az érvényes értékek a következők: *"#Microsoft. Azure. Search. ClassicSimilarity"* vagy *"#Microsoft. Azure. Search. BM25Similarity"*. <br/> A (z `api-version=2019-05-06-Preview` ) 2020. július 15. előtt létrehozott keresési szolgáltatáshoz szükséges vagy újabb. |

A 2020. július 15. után létrehozott új szolgáltatások esetében az BM25 automatikusan használatos, és az egyetlen hasonlósági algoritmus. Ha egy új szolgáltatáshoz próbál meg **hasonlóságot** beállítani `ClassicSimilarity` , a rendszer 400-es hibát ad vissza, mivel ez az algoritmus nem támogatott az új szolgáltatásokban.

A 2020. július 15. előtt létrehozott meglévő szolgáltatások esetében a klasszikus hasonlóság az alapértelmezett algoritmus marad. Ha a **hasonlóság** tulajdonság nincs megadva vagy NULL értékre van állítva, az index a klasszikus algoritmust használja. Ha az új algoritmust szeretné használni, a fentiekben ismertetett **hasonlóságot** kell megadnia.

## <a name="bm25-similarity-parameters"></a>BM25-hasonlósági paraméterek

A BM25 hasonlósága két felhasználó által testreszabható paramétert hoz létre a számított relevanciás pontszám szabályozása érdekében.

### <a name="k1"></a>K1 csomag

A *K1* paraméter a méretezési függvényt a dokumentum-lekérdezési pár végső relevanciás pontszámának kifejezése alapján szabályozza.

A nulla érték a "bináris modell" értéket jelöli, ahol az egyetlen egyező kifejezés hozzájárulása megegyezik az összes egyező dokumentummal, függetlenül attól, hogy a kifejezés hány alkalommal jelenik meg a szövegben, míg egy nagyobb K1-érték lehetővé teszi, hogy a pontszám tovább növekedjen, mivel az adott időszak több példánya is megtalálható a dokumentumban. Alapértelmezés szerint az Azure Cognitive Search 1,2 értéket használ a K1 paraméterhez. A magasabb K1-értékek használata olyan esetekben lehet fontos, amikor egy keresési lekérdezésbe több kifejezés is beleszámít. Ezekben az esetekben érdemes lehet olyan dokumentumokat használni, amelyek megfelelnek a különböző lekérdezési kifejezéseknek, amelyek csak egy-egy, több alkalommal egyeznek meg a dokumentumokban. Ha például az "Apollo űrrepülés" kifejezést tartalmazó dokumentumok indexét kérdezi le, érdemes lehet csökkenteni a görög mitológiáról szóló cikk pontszámát, amely az "Apollo" kifejezést néhány tucat alkalommal tartalmazza, a "űrrepülés" kifejezés nélkül, egy másik cikkhez képest, amely kifejezetten megemlíti az "Apollo" és a "űrrepülés" fogalmát egy maroknyi alkalommal. 
 
### <a name="b"></a>b

A *b* paraméter azt szabályozza, hogy a dokumentum hossza hogyan befolyásolja a relevancia pontszámát.

A 0,0 érték azt jelenti, hogy a dokumentum hossza nem befolyásolja a pontszámot, míg a 1,0 érték azt jelenti, hogy a dokumentum hosszának a kifejezésre gyakorolt hatását a rendszer normalizálja. A b paraméterhez az Azure Cognitive Searchban használt alapértelmezett érték 0,75. A kifejezés gyakoriságának normalizálása a dokumentum hosszának megfelelően olyan esetekben hasznos, amikor azt szeretnénk, hogy szankcionáljuk a hosszabb dokumentumokat. Bizonyos esetekben a hosszabb dokumentumok (például egy teljes regény) nagyobb valószínűséggel tartalmaznak sok releváns kifejezést a sokkal rövidebb dokumentumokhoz képest.

### <a name="setting-k1-and-b-parameters"></a>A K1 és a b paraméterek beállítása

A b vagy K1 értékek testreszabásához egyszerűen adja hozzá őket tulajdonságokként a hasonlósági objektumhoz a BM25 használatakor:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

A hasonlósági algoritmus csak az index létrehozási idején állítható be. Ez azt jelenti, hogy a használt hasonlósági algoritmus nem módosítható a meglévő indexek esetében. A *"b"* és a *"K1"* paraméterek módosíthatók a BM25 használó meglévő index-definíció frissítésekor. Ha egy meglévő indexben módosítja ezeket az értékeket, az index legalább néhány másodpercig offline állapotba kerül, így az indexelés és a lekérdezési kérelmek sikertelenek lesznek. Emiatt a frissítési kérelem lekérdezési karakterláncában be kell állítania a "allowIndexDowntime = true" paramétert:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>Lásd még  

+ [REST API referenciája](/rest/api/searchservice/)
+ [Pontozási profilok hozzáadása az indexhez](index-add-scoring-profiles.md)
+ [Index API létrehozása](/rest/api/searchservice/create-index)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search)