---
title: Szinonimák – C#-példa
titleSuffix: Azure Cognitive Search
description: Ebből a C#-példaból megtudhatja, hogyan adhatja hozzá a szinonima funkciót az Azure Cognitive Search egy indexéhez. A szinonimák leképezése az egyenértékű kifejezések listája. A szinonimákat támogató mezők kibontják a lekérdezéseket, hogy tartalmazzák a felhasználó által megadott kifejezést és az összes kapcsolódó szinonimát.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 50d5d73e71b8129f061ec49b363a0ebb13d22bdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97704656"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Példa: az Azure Cognitive Search szinonimáinak hozzáadása a C-ben #

A szinonimák bővítik a lekérdezéseket azáltal, hogy találatként kezelik a bemeneti kifejezéssel szemantikailag egyenértékűnek tekintett kifejezéseket. Előfordulhat például, hogy azt szeretné, hogy a „kocsi” kifejezésre olyan dokumentumokat kapjon eredményül, amelyek az „autó” vagy a „jármű” kifejezéseket is tartalmazzák. 

Az Azure Cognitive Searchban a szinonimák a *szinonimák* szerint vannak definiálva, a *megfeleltetési szabályok* alapján, amelyek egyenértékű feltételeket társítanak. Ez a példa a szinonimák meglévő indextel való hozzáadásának és használatának alapvető lépéseit ismerteti.

Ebben a példában a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Hozzon létre egy szinonima leképezést a [SynonymMap osztály](/dotnet/api/azure.search.documents.indexes.models.synonymmap)használatával. 
> * Adja meg a [SynonymMapsName tulajdonságot](/dotnet/api/azure.search.documents.indexes.models.searchfield.synonymmapnames) olyan mezőknél, amelyeknek támogatnia kell a lekérdezés kiterjesztését szinonimák használatával.

A szinonimákat támogató mezőket a szokásos módon kérdezheti le. A szinonimák eléréséhez nincs szükség további lekérdezési szintaxisra.

Több szinonimatérképet is létrehozhat, közzéteheti őket bármely index számára elérhető szolgáltatásszintű erőforrásként, majd hivatkozhat arra, amelyiket a mezőszinten használni kívánja. A lekérdezési időpontokban az Azure Cognitive Search egy szinonimás térképen keres, ha a lekérdezésben használt mezőkben meg van adva.

> [!NOTE]
> A szinonimák programozott módon hozhatók létre, de nem a portálon.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag az alábbi követelményekkel rendelkezik:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Cognitive Search szolgáltatás](search-create-service-portal.md)
* [Azure.Search.Documents-csomag](https://www.nuget.org/packages/Azure.Search.Documents/)

Ha nem ismeri a .NET-ügyfél függvénytárát, tekintse meg az [Azure Cognitive Search használata a .net-ben](search-howto-dotnet-sdk.md)című témakört.

## <a name="sample-code"></a>Mintakód

Az ebben a példában a [githubon](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)használt minta alkalmazás teljes forráskódja megtalálható.

## <a name="overview"></a>Áttekintés

A szinonimák értékének megjelenítéséhez előtte és után lekérdezéseket kell használni. Ebben a példában egy minta alkalmazás végrehajtja a lekérdezéseket, és visszaadja az eredményeket egy minta "Hotels" indexben, amely két dokumentummal van feltöltve. Először az alkalmazás olyan kifejezéseket és kifejezéseket használó keresési lekérdezéseket hajt végre, amelyek nem szerepelnek az indexben. Másodszor, a kód lehetővé teszi a szinonimák szolgáltatást, majd újra kiad ugyanazokat a lekérdezéseket, ezúttal az eredményeket a szinonimák leképezésében szereplő találatok alapján adja vissza. 

Az alábbi kód az általános folyamatot mutatja be.

```csharp
static void Main(string[] args)
{
   SearchIndexClient indexClient = CreateSearchIndexClient();

   Console.WriteLine("Cleaning up resources...\n");
   CleanupResources(indexClient);

   Console.WriteLine("Creating index...\n");
   CreateHotelsIndex(indexClient);

   SearchClient searchClient = indexClient.GetSearchClient("hotels");

   Console.WriteLine("Uploading documents...\n");
   UploadDocuments(searchClient);

   SearchClient searchClientForQueries = CreateSearchClientForQueries();

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Adding synonyms...\n");
   UploadSynonyms(indexClient);

   Console.WriteLine("Enabling synonyms in the test index...\n");
   EnableSynonymsInHotelsIndexSafely(indexClient);
   Thread.Sleep(10000); // Wait for the changes to propagate

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Complete.  Press any key to end application...\n");

   Console.ReadKey();
}
```

## <a name="before-queries"></a>„Előtte” lekérdezések

A `RunQueriesWithNonExistentTermsInIndex` parancsban olyan keresési lekérdezéseket adunk ki, mint „five star” (ötcsillagos), „internet”, valamint „economy AND hotel” (gazdaságos ÉS szálloda).

A lekérdezéseket, például az "öt csillag" kifejezést idézőjelek közé kell foglalni, és az ügyféltől függően Escape-karakterekre is szükség lehet.

```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = searchClient.Search<Hotel>("\"five star\"", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = searchClient.Search<Hotel>("internet", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = searchClient.Search<Hotel>("economy AND hotel", searchOptions);
WriteDocuments(results);
```

A két indexelt dokumentum egyike sem tartalmazza a feltételt, ezért az elsőből a következő kimenetet vesszük fel `RunQueriesWithNonExistentTermsInIndex` :  **egyetlen dokumentum sincs egyeztetve**.

## <a name="enable-synonyms"></a>Szinonimák engedélyezése

A "korábbi" lekérdezések futtatása után a mintakód engedélyezi a szinonimákat. A szinonimák engedélyezése egy kétlépéses folyamat. Először határozza meg és töltse fel a szinonima szabályait. Másodszor konfigurálja a mezőket a használatára. A folyamatot az `UploadSynonyms` és az `EnableSynonymsInHotelsIndex` vázolja.

1. Adjon hozzá egy szinonimatérképet a keresőszolgáltatáshoz. Az `UploadSynonyms` parancsban négy szabályt határozunk meg a 'desc-synonymmap' szinonimatérképben, és feltöltjük őket a szolgáltatásba.

   ```csharp
   private static void UploadSynonyms(SearchIndexClient indexClient)
   {
      var synonymMap = new SynonymMap("desc-synonymmap", "hotel, motel\ninternet,wifi\nfive star=>luxury\neconomy,inexpensive=>budget");

      indexClient.CreateOrUpdateSynonymMap(synonymMap);
   }
   ```

1. Konfiguráljon kereshető mezőket a szinonimatérkép indexdefinícióban történő használatához. Az `AddSynonymMapsToFields` parancsban engedélyezzük a szinonimákat a `category` és a `tags` mezőkben úgy, hogy a `SynonymMapNames` tulajdonságot az újonnan feltöltött szinonimatérkép nevére állítjuk.

   ```csharp
   private static SearchIndex AddSynonymMapsToFields(SearchIndex index)
   {
      index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
      index.Fields.First(f => f.Name == "tags").SynonymMapNames.Add("desc-synonymmap");
      return index;
   }
   ```

   Szinonimatérkép hozzáadásakor nem kell újraépíteni az indexet. A szolgáltatáshoz hozzáadhat egy szinonimatérképet, majd bármely indexben módosíthatja a meglévő mezőmeghatározásokat úgy, hogy azok az új szinonimatérképet használják. Az új attribútumok hozzáadása nincs hatással az index elérhetőségére. Ugyanez vonatkozik a szinonimák egy adott mező számára történő letiltására. A `SynonymMapNames` tulajdonságot egyszerűen átállíthatja egy üres listára.

   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
   ```

## <a name="after-queries"></a>„Utána” lekérdezések

A szinonimatérkép feltöltése és az index ennek használatára való frissítése után a második `RunQueriesWithNonExistentTermsInIndex` hívás a következő kimenetet eredményezi:

```dos
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```

Az első lekérdezés a `five star=>luxury` szabály alapján megtalálja a dokumentumot. A második lekérdezés kibővíti a keresést az `internet,wifi` használatával, a harmadik pedig a `hotel, motel` és az `economy,inexpensive=>budget` kifejezést is használja a dokumentumok megtalálásához.

A szinonimák hozzáadásával teljesen megváltozik a keresési élmény. Ebben a példában az eredeti lekérdezések nem tudták visszaadni az értelmes eredményeket, még ha az index dokumentumai is relevánsak voltak. A szinonimák engedélyezésével úgy bővíthetjük ki az indexet, hogy a gyakran használt kifejezéseket is tartalmazza a benne található mögöttes adatok megváltoztatása nélkül.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Egy példa után a leggyorsabb megoldás az Azure Cognitive Search szolgáltatást tartalmazó erőforráscsoport törlése. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. A portálon az erőforráscsoport neve az Azure Cognitive Search szolgáltatás áttekintés lapján található.

## <a name="next-steps"></a>Következő lépések

Ez a példa a C#-kódban szereplő szinonimák funkciót mutatta be a leképezési szabályok létrehozásához és közzétételéhez, majd a szinonimák leképezésének meghívásához a lekérdezésen. További információt a [.NET SDK](/dotnet/api/overview/azure/search.documents-readme) és a [REST API](/rest/api/searchservice/) referencia-dokumentációjában találhat.

> [!div class="nextstepaction"]
> [Szinonimák használata az Azure-ban Cognitive Search](search-synonyms.md)