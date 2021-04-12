---
title: Naplózási adatok gyűjtése
titleSuffix: Azure Cognitive Search
description: Összegyűjtheti és elemezheti a naplózási adatokat, ha engedélyezi a diagnosztikai beállításokat, majd a Kusto lekérdezési nyelv segítségével tárja fel az eredményeket.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: f0d85f056cfaaa58fcc72eb9c2182b3e1a78affb
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581622"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Az Azure-Cognitive Search naplózási adatainak összegyűjtése és elemzése

A diagnosztikai vagy működési naplók betekintést nyújtanak az Azure Cognitive Search részletes műveleteibe, és hasznosak lehetnek a szolgáltatás állapotának és folyamatainak figyelésére. Belsőleg a Microsoft megőrzi a rendszerinformációkat a backend-ben egy rövid ideig (körülbelül 30 nap), amely elegendő a vizsgálathoz és az elemzéshez, ha támogatási jegyet ad. Ha azonban az operatív adatok tulajdonjogát szeretné használni, konfigurálnia kell egy diagnosztikai beállítást annak megadásához, hogy a naplózási információk hol legyenek gyűjtve.

A diagnosztikai naplózás a [Azure monitor](../azure-monitor/index.yml)-vel való integráción keresztül érhető el. 

A diagnosztikai naplózás beállításakor a rendszer megkéri a tárolási mechanizmus megadására. Az alábbi táblázat az adatok gyűjtésére és megőrzésére vonatkozó beállításokat sorolja fel.

| Erőforrás | Alkalmazási cél |
|----------|----------|
| [Küldés a Log Analytics-munkaterületre](../azure-monitor/essentials/tutorial-resource-logs.md) | Az eseményeket és mérőszámokat egy Log Analytics munkaterületre küldi a rendszer, amely lekérdezhető a portálon, és részletes információkat adhat vissza. Bevezetés: Ismerkedés [a Azure monitor-naplókkal](../azure-monitor/logs/log-analytics-tutorial.md) |
| [Archiválás blob Storage-val](../storage/blobs/storage-blobs-overview.md) | Az események és a metrikák archiválása egy blob-tárolóba történik, és JSON-fájlokban tárolódik. A naplók meglehetősen részletesek (óránként/percenként), ami hasznos lehet egy adott incidens kereséséhez, de a nyílt végű vizsgálathoz nem. Egy JSON-szerkesztővel megtekintheti a naplófájlok összesítéséhez és megjelenítéséhez használható nyers naplófájlt vagy Power BI.|
| [Stream az Event hub-ba](../event-hubs/index.yml) | Az események és mérőszámok továbbítása egy Azure Event Hubs szolgáltatásba történik. Válassza ezt alternatív adatgyűjtési szolgáltatásként a nagyon nagy naplók számára. |

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre erőforrásokat előre, hogy a diagnosztikai naplózás konfigurálásakor ki lehessen választani egyet vagy többet.

+ [Log Analytics-munkaterület létrehozása](../azure-monitor/logs/quick-create-workspace.md)

+ [Tárfiók létrehozása](../storage/common/storage-account-create.md)

+ [Event hub létrehozása](../event-hubs/event-hubs-create.md)

## <a name="enable-data-collection"></a>Az adatgyűjtés engedélyezése

A diagnosztikai beállítások határozzák meg a naplózott események és metrikák gyűjtésének módját.

1. A **Monitorozás** területen kattintson a **Diagnosztikai beállítások** elemre.

   ![Diagnosztikai beállítások](./media/search-monitor-usage/diagnostic-settings.png "Diagnosztikai beállítások")

1. Válassza a **+ diagnosztikai beállítás hozzáadása** lehetőséget

1. Jelölje be **log Analytics**, válassza ki a munkaterületet, és válassza a **OperationLogs** és a **AllMetrics** lehetőséget.

   ![Adatgyűjtés konfigurálása](./media/search-monitor-usage/configure-storage.png "Adatgyűjtés konfigurálása")

1. Mentse a beállítást.

1. A naplózás engedélyezése után a keresési szolgáltatással megkezdheti a naplók és mérőszámok generálását. A naplózott események és a mérőszámok elérhetővé válása előtt időbe telik.

Log Analytics esetén több percet is igénybe vehet, hogy az adatforrások elérhetők legyenek, miután az Kusto-lekérdezések futtatásával visszatérhetnek az adatforrásokhoz. További információ: [lekérdezési kérelmek figyelése](search-monitor-logs.md).

A blob Storage esetében a tárolók a blob Storage-ban való megjelenítése előtt egy órával tartanak. A tárolók száma óránként egy blob. A tárolók csak akkor jönnek létre, ha van egy tevékenység a naplóba vagy a mértékbe. Amikor az adat egy Storage-fiókba másolódik, az adat JSON-ként van formázva, és két tárolóba kerül:

+ elemzések – naplók – operationlogs: keresési forgalmi naplók
+ áttekintések – mérőszámok – pt1m: metrikák esetén

## <a name="query-log-information"></a>Lekérdezési napló adatai

Két tábla tartalmaz naplókat és mérőszámokat az Azure Cognitive Search: **AzureDiagnostics** és a **AzureMetrics**.

1. A **figyelés** területen válassza a **naplók** lehetőséget.

1. Adja meg a **AzureMetrics** a lekérdezési ablakban. Ezt az egyszerű lekérdezést futtatva megismerheti az ebben a táblázatban gyűjtött adatokat. A metrikák és értékek megtekintéséhez görgessen végig a táblázaton. Figyelje meg, hogy a rekordok száma felül van, és ha a szolgáltatás egy ideig gyűjt metrikákat, érdemes lehet módosítani az időintervallumot, hogy kezelhető adatkészletet kapjon.

   ![AzureMetrics táblázat](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics táblázat")

1. Táblázatos eredményhalmaz visszaadásához adja meg a következő lekérdezést.

   ```kusto
   AzureMetrics
   | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Ismételje meg az előző lépéseket, kezdve a **AzureDiagnostics** az összes oszlop tájékoztatási céllal való visszaküldéséhez, majd egy szelektívebb lekérdezés, amely további érdekes információkat gyűjt.

   ```kusto
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics táblázat](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics táblázat")

## <a name="kusto-query-examples"></a>Kusto-lekérdezési példák

Ha engedélyezte a diagnosztikai naplózást, a **AzureDiagnostics** lekérdezheti a szolgáltatásban futó műveletek listáját, valamint a következőt:. A tevékenységek a teljesítmény változásainak vizsgálatára is felhasználhatók.

#### <a name="example-list-operations"></a>Példa: műveletek listázása 

Visszaküldi a műveletek listáját és az egyes elemek számát.

```kusto
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Példa: korrelációs műveletek

A lekérdezési kérelmek korrelációja az indexelési műveletekkel, valamint az adatpontok megjelenítése az idődiagramokon keresztül, hogy a műveletek egybeessenek.

```kusto
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

## <a name="logged-operations"></a>Naplózott műveletek

A Azure Monitor által rögzített naplózott események magukban foglalják az indexeléssel és a lekérdezésekkel kapcsolatos eseményeket. A Log Analytics **AzureDiagnostics** táblázata a lekérdezésekhez és az indexeléshez kapcsolódó operatív adatokat gyűjti.

| OperationName | Description |
|---------------|-------------|
| ServiceStats | Ez a művelet egy rutinos hívás a [szolgáltatás statisztikáinak beszerzésére](/rest/api/searchservice/get-service-statistics), vagy közvetlenül vagy implicit módon, a portál áttekintő oldalának feltöltésekor, amikor betöltődik vagy frissül. |
| Lekérdezés. keresés |  Lekérdezési kérelmek egy indextel kapcsolatban: a naplózott lekérdezésekkel kapcsolatos információk [figyelése](search-monitor-queries.md) .|
| Indexelés. index  | Ez a művelet a [dokumentumok hozzáadását, frissítését és törlését](/rest/api/searchservice/addupdate-or-delete-documents)hívja meg. |
| indexek. Prototípus | Ez az adatbázis importálása varázsló által létrehozott index. |
| Indexelő. létrehozás | Hozzon létre egy indexelő explicit módon vagy implicit módon az adatimportálás varázsló segítségével. |
| Indexelő. Get | Egy indexelő nevét adja vissza, amikor az indexelő fut. |
| Indexelő. status | Egy indexelő állapotát adja vissza, amikor az indexelő fut. |
| Adatforrások. Get | Az adatforrás nevét adja vissza, amikor egy indexelő fut.|
| Indexek. Get | Egy index nevét adja vissza, amikor egy indexelő fut. |

## <a name="log-schema"></a>Napló sémája

Ha egyéni jelentéseket készít, az Azure Cognitive Search-naplóba tartozó adatstruktúrák megfelelnek az alábbi sémának. A blob Storage esetében minden egyes blob egyetlen, a log objektumok tömbjét tartalmazó **rekordot** tartalmaz. Minden blob az adott órában végrehajtott összes művelethez tartalmaz rekordokat.

Az alábbi táblázat az erőforrás-naplózáshoz leggyakrabban használt mezők részleges listáját tartalmazza.

| Név | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| timeGenerated |dátum/idő |"2018-12-07T00:00:43.6872559 Z" |A művelet időbélyege |
| resourceId |sztring |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK/<br/> Microsoft. KERESÉS/SEARCHSERVICES/SEARCHSERVICE " |Az Ön ResourceId |
| operationName |sztring |"Lekérdezés. keresés" |A művelet neve |
| operationVersion |sztring |"2020-06-30" |A használt API-verzió |
| category |sztring |"OperationLogs" |állandó |
| resultType |sztring |Sikeres |Lehetséges értékek: sikeres vagy sikertelen |
| resultSignature |int |200 |HTTP-eredmény kódja |
| Átl |int |50 |A művelet időtartama ezredmásodpercben |
| properties |object |Tekintse meg a következő táblázatot |Művelet-specifikus adathalmazt tartalmazó objektum |

### <a name="properties-schema"></a>Tulajdonságok séma

Az alábbi tulajdonságok az Azure Cognitive Search-ra vonatkoznak.

| Név | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| Description_s |sztring |"/Indexes beolvasása (" tartalom ")/docs" |A művelet végpontja |
| Documents_d |int |42 |Feldolgozott dokumentumok száma |
| IndexName_s |sztring |"test-index" |A művelethez társított index neve |
| Query_s |sztring |"? Search = AzureSearch&$count = igaz&API-Version = 2020-06-30" |A lekérdezési paraméterek |

## <a name="metrics-schema"></a>Mérőszámok sémája

A rendszer a lekérdezési kérelmek esetében rögzíti a metrikákat, és egy percen belül méri őket. Minden metrika percenkénti minimális, maximális és átlagos értékeket tesz elérhetővé. További információ: [lekérdezési kérelmek figyelése](search-monitor-queries.md).

| Név | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| resourceId |sztring |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK/<br/>Microsoft. KERESÉS/SEARCHSERVICES/SEARCHSERVICE " |az erőforrás-azonosító |
| metricName |sztring |Késleltetés |a metrika neve |
| time |dátum/idő |"2018-12-07T00:00:43.6872559 Z" |a művelet időbélyege |
| átlag |int |64 |A nyers minták átlagos értéke a metrika időintervallumában, a mérőszámtól függően másodpercben vagy százalékban megadva. |
| minimum |int |37 |A nyers minták minimális értéke a metrikai időintervallumban, az egység másodpercben megadva. |
| maximum |int |78 |A nyers minták maximális értéke a metrikai időintervallumban, az egység másodpercben megadva.  |
| összesen |int |258 |A nyers minták teljes értéke a metrika időintervallumában, a mértékegységben megadva.  |
| count |int |4 |Egy csomópontról a naplóba kibocsátott metrikák száma egy percen belül.  |
| timegrain |sztring |"PT1M" |A metrika időbeli kiőrlése ISO 8601-ben. |

A lekérdezések végrehajtása általában ezredmásodpercben történik, így csak a másodpercben megadott mérték jelenik meg a mérőszámban, például a QPS.

A másodpercenkénti **keresési lekérdezések** esetében a minimális érték az adott percben regisztrált keresési lekérdezések másodpercenkénti legalacsonyabb értéke. Ugyanez vonatkozik a maximális értékre is. Az átlag a teljes percenkénti összesítés. Előfordulhat például, hogy egy percen belül egy ilyen mintázattal rendelkezik: egy másodperces magas terhelés, amely a SearchQueriesPerSecond maximális értéke, majd az átlagos terhelés 58 másodperce, végül egy másodperc csak egy lekérdezéssel, amely a minimum.

A **szabályozott keresési lekérdezések aránya**, a minimum, a maximum, az átlag és az összes érték azonos értékű: a megadott keresési lekérdezések százalékos aránya, a keresési lekérdezések teljes száma egy percen belül.

## <a name="view-raw-log-files"></a>Nyers naplófájlok megtekintése

A blob Storage a naplófájlok archiválására szolgál. A naplófájl megtekintéséhez bármely JSON-szerkesztőt használhat. Ha még nem rendelkezik ilyennel, javasoljuk, hogy a [Visual Studio Code](https://code.visualstudio.com/download)-ot ajánljuk.

1. Azure Portal nyissa meg a Storage-fiókját. 

2. A bal oldali navigációs panelen kattintson a **Blobok** elemre. Látnia kell az elemzéseket – **naplók – operationlogs** és elemzések – **mérőszámok – pt1m**. Ezeket a tárolókat az Azure Cognitive Search hozza létre, amikor a naplózási adatként a blob Storage-ba exportálja őket.

3. Kattintson a mappa-hierarchiára, amíg el nem éri a. JSON fájlt.  A fájl letöltéséhez használja a helyi menüt.

Miután letöltötte a fájlt, nyissa meg egy JSON-szerkesztőben a tartalom megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, tekintse át a keresési szolgáltatás figyelésének alapjait, és ismerkedjen meg a teljes körű felügyeleti funkciókkal.

> [!div class="nextstepaction"]
> [Műveletek és tevékenységek figyelése az Azure Cognitive Search](search-monitor-usage.md)