---
title: Az indexelő állapotának és eredményeinek figyelése
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search indexek állapotának, előrehaladásának és eredményének figyelése a Azure Portal, a REST API vagy a .NET SDK használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: ed79831559802efdf7e3813f48b74c2fd3c63e87
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581867"
---
# <a name="monitor-indexer-status-and-results-in-azure-cognitive-search"></a>Az indexelő állapotának és eredményeinek figyelése az Azure Cognitive Search

Az indexelő feldolgozását figyelheti a Azure Portal, vagy programozott módon, REST-hívásokkal vagy egy Azure SDK-val. Az indexelő állapotán kívül az indítási és befejezési időpontokat, valamint egy adott Futtatás részletes hibáit és figyelmeztetéseit is áttekintheti.

## <a name="monitor-using-azure-portal"></a>Figyelés Azure Portal használatával

Az összes indexelő aktuális állapotát megtekintheti a keresési szolgáltatás áttekintés lapján. A portál oldalai néhány percenként frissülnek, így az új indexelő azonnal nem jelenik meg.

   ![Indexelő lista](media/search-monitor-indexers/indexers-list.png "Indexelő lista")

| Állapot | Leírás |
|--------|-------------|
| **Folyamatban** | Aktív végrehajtást jelez. A portál részleges információkat fog jelenteni. Az indexelés előrehaladtával megtekintheti, hogy a **docs sikeres** értékének növekedése válaszként történt-e. A nagyméretű adatmennyiséget feldolgozó indexelő hosszú időt vehetnek igénybe. A több millió forrásoldali dokumentumot kezelő indexelő például 24 óráig futhatnak, majd a szinte azonnal újraindulnak. Előfordulhat, hogy a nagy mennyiségű indexelő állapota mindig a portálon **van folyamatban** . Még ha egy indexelő is fut, a részletek a folyamatban lévő és a korábbi futtatások esetében is elérhetők. |
| **Siker** | Azt jelzi, hogy a Futtatás sikeres volt. Az indexelő futása akkor is sikeres lehet, ha az egyes dokumentumok hibákkal rendelkeznek, ha a hibák száma kisebb, mint az indexelő **nem megfelelő elemek maximális** beállítása. |
| **Sikertelen** | A hibák száma túllépte a **hibás elemek maximális** számát, és az indexelés leállt. |
| **Reset** | Az indexelő belső módosítás-követési állapota alaphelyzetbe állt. Az indexelő teljes egészében fut, az összes dokumentum frissítése nélkül, és nem csak az újabb időbélyegekkel rendelkezők. |

A listában szereplő indexelő elemre kattintva további részleteket tudhat meg az indexelő jelenlegi és legutóbbi futtatásáról.

   ![Indexelő Összegzés és végrehajtási előzmények](media/search-monitor-indexers/indexer-summary.png "Indexelő Összegzés és végrehajtási előzmények")

Az **Indexelő összegző** diagram a legutóbbi futtatásokban feldolgozott dokumentumok számának gráfját jeleníti meg.

A **végrehajtás részletei** listán legfeljebb 50 a legutóbbi végrehajtás eredményei. Kattintson egy végrehajtási eredményre a listában, hogy megtekintse a futtatási jellemzőket. Ez magában foglalja az indítási és befejezési időpontokat, valamint az esetleges hibákat és figyelmeztetéseket.

   ![Indexelő végrehajtásának részletei](media/search-monitor-indexers/indexer-execution.png "Indexelő végrehajtásának részletei")

Ha a Futtatás során dokumentált problémák léptek fel, azok a hibák és figyelmeztetések mezőkben jelennek meg.

   ![Indexelő adatai hibákkal](media/search-monitor-indexers/indexer-execution-error.png "Indexelő adatai hibákkal")

A figyelmeztetések gyakran előfordulnak bizonyos típusú indexelő típusokban, és nem mindig jeleznek problémát. A kognitív szolgáltatásokat használó indexelő például figyelmeztetést jelenthetnek, ha a kép-vagy PDF-fájlok nem tartalmaznak feldolgozni kívánt szöveget. 

További információ az indexelő hibáiról és a figyelmeztetésekről: [Az Azure Cognitive Search gyakori indexelő problémáinak elhárítása](search-indexer-troubleshooting.md).

## <a name="monitor-using-get-indexer-status-rest-api"></a>Figyelő az indexelő állapotának lekérése (REST API) használatával

Az indexelő állapotának és végrehajtásának előzményeit az [Indexelő állapotának lekérése paranccsal](/rest/api/searchservice/get-indexer-status)kérheti le:

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

A válasz általános indexelő állapotot, az utolsó (vagy folyamatban lévő) indexelő meghívást, valamint a legutóbbi indexelő hívás előzményeit tartalmazza.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

A végrehajtási előzmények akár a 50 legutóbbi futtatását is tartalmazzák, amelyek fordított időrendi sorrendben vannak rendezve (a legutóbbi első).

Figyelje meg, hogy két különböző állapotüzenetek vannak. A legfelső szintű állapot az indexelő esetében van. A **futó** indexelő állapota azt jelenti, hogy az indexelő helyesen van beállítva, és elérhető a futtatásához, de nem, hogy éppen fut.

Az indexelő minden futtatása saját állapottal rendelkezik, amely azt jelzi, hogy az adott végrehajtás folyamatban van-e (**fut**), vagy már befejeződött **sikeres**, **transientFailure** vagy **persistentFailure** állapottal. 

Ha egy indexelő visszaáll a Change Tracking állapotának frissítésére, egy külön végrehajtási előzmény kerül a **visszaállítási** állapotba.

Az állapotkódok és az indexelő adatainak figyelésével kapcsolatos további információkért lásd: az [Indexelő állapotának beolvasása](/rest/api/searchservice/get-indexer-status).

## <a name="monitor-using-net"></a>Figyelés a .NET használatával

Az Azure Cognitive Search .NET SDK-t használva a következő C#-példa adatokat ír az indexelő állapotára, valamint a legutóbbi (vagy folyamatban lévő), a konzolon futtatott eredményekre.

```csharp
static void CheckIndexerStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        string indexerName = "hotels-sql-idxr";
        SearchIndexerStatus execInfo = indexerClient.GetIndexerStatus(indexerName);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("Run Status: {0}", result.Status.ToString());
        Console.WriteLine("Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("ErrorMessage: {0}", errorMsg);
        Console.WriteLine(" Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

A konzol kimenete a következőképpen fog kinézni:

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 11:29:31 PM, EndTime: 11:29:31 PM, Elapsed: 00:00:00.2560000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

Figyelje meg, hogy két különböző állapotüzenetek vannak. A legfelső szintű állapot az indexelő állapota. A **futó** indexelő állapota azt jelenti, hogy az indexelő helyesen van beállítva, és végrehajtásra elérhető, de nem az éppen végrehajtás alatt áll.

Az indexelő minden futtatása saját állapottal rendelkezik, hogy az adott végrehajtás folyamatban van-e (**fut**), vagy már befejeződött **sikeres** vagy **TransientError** állapottal. 

Ha egy indexelő visszaáll a változás követési állapotának frissítésére, egy külön előzmény kerül a **visszaállítási** állapotba.

## <a name="next-steps"></a>Következő lépések

Az állapotkódok és az indexelő figyelési információkkal kapcsolatos további információkért tekintse meg a következő API-referenciát:

* [GetIndexerStatus (REST API)](/rest/api/searchservice/get-indexer-status)
* [IndexerStatus](/dotnet/api/azure.search.documents.indexes.models.indexerstatus)
* [IndexerExecutionStatus](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionstatus)
* [IndexerExecutionResult](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionresult)