---
title: Többnyelvű indexelés a nem angol nyelvű keresési lekérdezésekhez
titleSuffix: Azure Cognitive Search
description: Hozzon létre egy olyan indexet, amely támogatja a többnyelvű tartalmat, majd hozzon létre az adott tartalomra kiterjedő lekérdezéseket.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 627ec77af4e492b4f22404972729cecdb1c40f06
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801604"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Index létrehozása több nyelvhez az Azure-ban Cognitive Search

A többnyelvű keresőalkalmazás egyik kulcsfontosságú követelménye, hogy a felhasználó saját nyelvén kereshet át és kérhet le eredményeket. Az Azure Cognitive Searchban a többnyelvű alkalmazások nyelvi követelményeinek kielégítése érdekében dedikált mezőket kell létrehoznia a karakterláncok adott nyelven való tárolásához, majd a teljes szöveges keresést csak a lekérdezési időpontra korlátozza.

+ A mezők definíciói területen állítson be egy nyelvi elemzőt, amely meghívja a célnyelv nyelvi szabályait. A támogatott elemzők teljes listájának megtekintéséhez lásd a [nyelvi elemzők hozzáadása](index-add-language-analyzers.md)című témakört.

+ A lekérdezési kérelemben állítsa be a paramétereket a teljes szöveges keresés hatókörére meghatározott mezőkre, majd vágja le az összes olyan mező eredményét, amely nem kompatibilis a tartalmat a kézbesíteni kívánt keresési felülettel.

Ennek a technikának a sikere a mezők tartalmának integritására támaszkodik. Az Azure Cognitive Search nem fordítja le a karakterláncokat, és nem hajt végre nyelvi észlelést a lekérdezés végrehajtásának részeként. Így gondoskodhat arról, hogy a mezők tartalmazzák a várt karakterláncokat.

## <a name="define-fields-for-content-in-different-languages"></a>Mezők definiálása különböző nyelveken lévő tartalomhoz

Az Azure Cognitive Searchban a lekérdezések egyetlen indexet céloznak meg. A fejlesztők, akik egy adott keresési élményben szeretnék megadni a nyelvspecifikus karakterláncokat, jellemzően az értékek tárolására szolgáló dedikált mezőket határozzák meg: az angol karakterláncok egy mezője, egy a francia, és így tovább.

A mező definíciójában a "Analyzer" tulajdonság a [Language Analyzer](index-add-language-analyzers.md)beállítására szolgál. Az indexeléshez és a lekérdezés végrehajtásához is használni fogja.

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>Index létrehozása és betöltése

Egy közbenső (és talán nyilvánvaló) lépés az, hogy [az indexet fel kell építenie és fel kell töltenie a](search-get-started-dotnet.md) lekérdezés kialakítása előtt. Ez a lépés a teljesség kedvéért van megemlítve. Az indexek rendelkezésre állásának meghatározására az egyik lehetőség a [portál](https://portal.azure.com)indexek listájának ellenőrzése.

> [!TIP]
> A nyelvfelismerés és a szöveg fordítása a [mesterséges intelligenciával](cognitive-search-concept-intro.md) és a [szakértelmével](cognitive-search-working-with-skillsets.md)való adatfeldolgozás során támogatott. Ha vegyes nyelvi tartalmú Azure-adatforrással rendelkezik, kipróbálhatja a nyelvfelismerés és a fordítási funkciókat az [adatimportálás varázsló](cognitive-search-quickstart-blob.md)segítségével.

## <a name="constrain-the-query-and-trim-results"></a>A lekérdezés és a vágás eredményének korlátozása

A lekérdezés paraméterei a keresés adott mezőkre való korlátozására szolgálnak, majd az adott forgatókönyvnek nem megfelelő mezők eredményeinek levágása. 

| Paraméterek | Cél |
|-----------|--------------|
| **searchFields** | A teljes szöveges keresést a megnevezett mezők listájára korlátozza. |
| **$select** | Levágja a választ, hogy csak a megadott mezőket foglalja bele. Alapértelmezés szerint a rendszer az összes beolvasható mezőt visszaadja. A **$Select** paraméterrel kiválaszthatja, hogy melyeket kell visszaadnia. |

A francia sztringeket tartalmazó mezők keresésének célja, hogy a **searchFields** az adott nyelven sztringeket tartalmazó mezőkre célozza.

Nem szükséges megadni az elemzőt a lekérdezési kérelemhez. A lekérdezés feldolgozásakor a rendszer mindig a mező definíciójában lévő nyelvi elemzőt fogja használni. A különböző nyelvi elemzőket meghívó több mezőt megadó lekérdezések esetében a feltételek és kifejezések az egyes mezőkhöz rendelt elemzők egymástól függetlenül lesznek feldolgozva.

Alapértelmezés szerint a keresés visszaadja az összes olyan mezőt, amely beolvasható van megjelölve. Ezért érdemes lehet olyan mezőket kizárni, amelyek nem felelnek meg a megadni kívánt nyelvspecifikus keresési élménynek. Pontosabban, ha a keresés egy francia sztringet tartalmazó mezőre korlátozódik, valószínűleg ki szeretné zárni az eredményekből származó angol nyelvű mezőket. A **$Select** lekérdezési paraméterrel szabályozhatja, hogy a rendszer mely mezőket adja vissza a hívó alkalmazásnak.

#### <a name="example-in-rest"></a>Példa a REST-ben

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>Példa C-ben #

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>Nyelvi specifikus mezők fellendítése

Előfordulhat, hogy a lekérdezést kiállító ügynök nyelve nem ismert, ebben az esetben a lekérdezés egyszerre adható ki az összes mezővel. Az eredmények egy adott nyelven való megadásának elsőbbségi sorrendje a [pontozási profilok](index-add-scoring-profiles.md)használatával határozható meg. Az alábbi példában az angol nyelvű leírásban található egyezések a többi nyelvhez képest magasabb pontszámot kapnak:

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

Ezt követően a keresési kérelemben szerepelnie kell a pontozási profilnak:

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>Következő lépések

+ [Nyelvi elemzők](index-add-language-analyzers.md)
+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)
+ [Dokumentumok keresése – REST API](/rest/api/searchservice/search-documents)
+ [AI-dúsítás áttekintése](cognitive-search-concept-intro.md)
+ [A szakértelmével áttekintése](cognitive-search-working-with-skillsets.md)