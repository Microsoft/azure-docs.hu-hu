---
title: Indexelő végrehajtásának ütemterve
titleSuffix: Azure Cognitive Search
description: Ütemezze az Azure Cognitive Search indexelő programját, hogy rendszeres időközönként vagy adott időpontokban indexelje a tartalmat.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 8ae9a89ddba2010603ae5a5f6b812e3aa1e1e3a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097976"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Indexelő ütemezhetnek az Azure Cognitive Search

Az indexelő általában egyszer fut, közvetlenül a létrehozása után. Ezt követően igény szerint újra futtathatja a Azure Portal használatával, az [Indexelő (REST)](/rest/api/searchservice/run-indexer)vagy egy Azure SDK futtatásával. Azt is megteheti, hogy az indexelő úgy is konfigurálhatja, hogy az ütemterv szerint fusson. Előfordulhat, hogy az indexelő ütemezése hasznos:

* A forrásadatok idővel változnak, és azt szeretné, hogy a keresési indexelő automatikusan feldolgozza a különbséget.

* A forrásadatok nagyon nagy méretűek, és az indexelő feldolgozását az idő múlásával szeretné elosztani. Az indexelő feladatok a normál adatforrások esetében legfeljebb 24 óra, a szakértelmével rendelkező indexelő esetében pedig 2 óra alatt futnak. Ha az indexelés nem hajtható végre a maximális intervallumon belül, beállíthatja a 2 óránként futó ütemtervet. Az indexelő automatikusan kiválaszthatják, hogy hol hagyták el őket, ahogy azt egy belső, magas vízjelek jelzik, amelyek az indexelés utolsó végére utalnak. Az indexelő ismétlődő 2 órás ütemezésű futtatása lehetővé teszi, hogy egy nagyon nagy adatkészletet (több millió dokumentumot) futtasson egy adott feladatokhoz engedélyezett intervallumon túl. A nagyméretű adatmennyiségek indexelésével kapcsolatos további információkért lásd: [nagyméretű adatkészletek indexelése az Azure Cognitive Searchban](search-howto-large-index.md).

* A keresési index több adatforrásból lesz feltöltve, és azt szeretné, hogy az indexelő az ütközések csökkentése érdekében különböző időpontokban fussanak.

Vizuálisan az ütemterv a következőhöz hasonló lehet: január 1-től kezdődően, és 50 percenként fut.

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> Az ütemező az Azure Cognitive Search beépített funkciója. A külső ütemező nem támogatott.

## <a name="schedule-property"></a>Schedule tulajdonság

Az ütemezés az indexelő definíciójának része. Ha az **Schedule** tulajdonság nincs megadva, az indexelő csak azonnal, a létrehozás után fog futni. Ha egy **Schedule** tulajdonságot ad hozzá, két részből kell megadnia.

| Tulajdonság | Leírás |
|----------|-------------|
|**Intervallum** | szükséges A két egymást követő indexelő végrehajtásának kezdete közötti idő. A legkisebb megengedett intervallum 5 perc, a leghosszabb pedig 1440 perc (24 óra). A fájlnak XSD "dayTimeDuration" értéknek kell lennie (az [ISO 8601 időtartam](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) értékének korlátozott részhalmaza). A minta ehhez a következő: `P(nD)(T(nH)(nM))` . <br/><br/>Példák: `PT15M` 15 percenként, `PT2H` minden 2 órában.|
| **Kezdés időpontja (UTC)** | választható Azt jelzi, hogy az ütemezett végrehajtások kezdődjön-e. Ha nincs megadva, a rendszer az aktuális UTC-időt használja. Ez az idő lehet a múltban, amely esetben az első végrehajtás ütemezve van, mintha az indexelő az eredeti **kezdő időpont** óta folyamatosan fut.<br/><br/>Példák: `2021-01-01T00:00:00Z` január 1-től éjfélkor, `2021-01-05T22:28:00Z` 10:28 órakor kezdődően. Január 5-én.|

## <a name="scheduling-behavior"></a>Ütemezési viselkedés

Egyszerre csak egy indexelő végrehajtása futhat. Ha egy indexelő már fut a következő végrehajtás ütemezésekor, a végrehajtás a következő ütemezett időpontig Elhalasztva lesz.

Vegyünk egy példát erre a konkrétra. Tegyük fel, hogy az indexelő-ütemtervet **óránként, a** 2019-as és a 8:00:00-es UTC-kor számított 1. június 1-től **kezdődően** konfiguráljuk. Ez akkor fordulhat elő, ha egy indexelő futtatása egy óránál hosszabb időt vesz igénybe:

* Az első indexelő végrehajtása a 2019-as vagy az 8:00-os UTC-kor kezdődik. Feltételezzük, hogy ez a végrehajtás 20 percet vesz igénybe (vagy 1 óránál rövidebb ideig).
* A második végrehajtás 2019 9:00 AM UTC vagy újabb időpontban kezdődik. Tegyük fel, hogy ez a végrehajtás 70 percet vesz igénybe – több mint egy óra –, és a 10:10-es UTC-ig nem fog elindulni.
* A harmadik végrehajtás a következő időpontban indul: 10:00 UTC, de az előző végrehajtás még mindig fut. Ezt követően az ütemezett végrehajtás ki lesz hagyva. Az indexelő következő végrehajtása nem indul el a 11:00 UTC-ig.

> [!NOTE]
> Ha egy indexelő egy bizonyos ütemezésre van beállítva, de többször is meghibásodik ugyanazon a dokumentumon, akkor az indexelő a ritkábban (legalább 24 óránként legfeljebb egyszer) fog futni, amíg a folyamat újra nem sikerül. Ha úgy gondolja, hogy rögzítette a mögöttes problémát, akkor manuálisan futtathatja az indexelő, és ha az indexelés sikeres lesz, az indexelő visszatér a normál ütemtervhez.

## <a name="schedule-using-rest"></a>Ütemterv REST használatával

Az indexelő létrehozásakor vagy frissítésekor a **Schedule** tulajdonságot kell megadni.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>Ütemterv a .NET használatával

A következő C#-példa egy Azure SQL Database-indexet hoz létre egy előre definiált adatforrás és index használatával, és az ütemtervet naponta egyszer futtatja, most kezdődik:

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

Az ütemterv a [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) osztály használatával van definiálva, amikor létrehoz vagy frissít egy indexet a [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)használatával. A **IndexingSchedule** konstruktorhoz egy **TimeSpan** objektum használatával megadott **intervallum** -paraméter szükséges. Ahogy azt korábban említettük, a legkisebb intervallum 5 perc, a legnagyobb pedig 24 óra. A **DateTimeOffset** objektumként megadott második **kezdő időpont** paraméter nem kötelező.

## <a name="next-steps"></a>Következő lépések

Az olyan indexelő esetében, amelyek menetrend szerint futnak, a keresési szolgáltatás állapotának beolvasásával vagy részletes információk beszerzésével figyelheti a műveleteket a diagnosztikai naplózás engedélyezésével.

* [Keresési indexelő állapotának figyelése](search-howto-monitor-indexers.md)
* [Naplózási adatok összegyűjtése és elemzése](search-monitor-logs.md)