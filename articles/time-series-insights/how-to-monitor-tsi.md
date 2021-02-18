---
title: Figyelési Time Series Insights | Microsoft Docs
description: A rendelkezésre állás, a teljesítmény és a művelet Time Series Insights figyelése.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 940f7efc7130ef80817be3b42e3c0eff83588a90
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587992"
---
# <a name="monitoring-time-series-insights"></a>Figyelés Time Series Insights

Ha kritikus fontosságú alkalmazásokat és üzleti folyamatokat kíván használni az Azure-erőforrásokon, figyelnie kell ezeket az erőforrásokat a rendelkezésre állással, a teljesítménnyel és a művelettel kapcsolatban. Ez a cikk ismerteti a Time Series Insights által generált figyelési információkat, valamint azt, hogy miként használhatók a Azure Monitor funkciói az adatelemzéshez és a riasztáshoz.

## <a name="monitor-overview"></a>Figyelő áttekintése

Az egyes Time Series Insights-környezetek Azure Portal **áttekintő** lapja az erőforrás-használat rövid nézetét tartalmazza, például a fogadott üzenetek számát és a tárolt bájtok számát. Ezek az információk hasznosak, de csak kis mennyiségű figyelési adat érhető el ezen a panelen. Az adatok némelyikét automatikusan gyűjti a rendszer, és az erőforrás létrehozásakor az elemzéshez azonnal elérhetővé válik. További típusú adatgyűjtést is engedélyezhet néhány konfigurációval.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?

A Time Series Insights a [Azure monitor](../azure-monitor/overview.md)használatával hoz létre figyelési adatokkal, amely az Azure teljes verem-figyelési szolgáltatása, amely teljes körű funkciókat biztosít az Azure-erőforrások figyeléséhez más Felhőbeli és helyszíni erőforrásokon kívül.

A következő fogalmakat ismerteti az [Azure-erőforrások Azure Monitorával való monitorozásával foglalkozó](../azure-monitor/essentials/monitor-azure-resource.md)cikkből:

- Mi az Azure Monitor?
- A figyeléshez kapcsolódó költségek
- Az Azure-ban összegyűjtött adatok figyelése
- Adatgyűjtés konfigurálása
- Szabványos eszközök az Azure-ban a figyelési adatok elemzéséhez és riasztásához

Az alábbi részekben a cikk a Azure Time Series Insightshoz összegyűjtött adatok leírásával épít. Ezek a szakasz példákat is tartalmaznak az adatgyűjtés konfigurálására és az adatok elemzésére az Azure-eszközökkel.

> [!TIP]
> A Azure Monitorhez kapcsolódó költségek megismeréséhez tekintse meg a [használati és becsült költségeket](../azure-monitor//usage-estimated-costs.md). Ha meg szeretné tudni, hogy mire van szüksége ahhoz, hogy az adatai megjelenjenek Azure Monitorban, tekintse meg az [adatfeldolgozási idő naplózása](../azure-monitor/logs/data-ingestion-time.md)című témakört.

## <a name="monitoring-data-from-azure-time-series-insights"></a>Adatok figyelése Azure Time Series Insightsról

Azure Time Series Insights ugyanolyan típusú figyelési adatokat gyűjt, mint az [Azure-erőforrások monitorozásával](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)kapcsolatos további Azure-erőforrások. 

A begyűjthető naplók és mérőszámok részletes ismertetését lásd: [Azure Time Series Insights monitorozási adatok referenciája](how-to-monitor-tsi-reference.md) .

## <a name="collection-and-routing"></a>Gyűjtés és Útválasztás

A platform metrikáinak gyűjtése és tárolása automatikusan történik, de más helyekre is átirányítható diagnosztikai beállítások használatával.

Az erőforrás-naplók gyűjtése és tárolása addig nem történik meg, amíg létre nem hozza a diagnosztikai beállításokat, és egy vagy több helyre irányítja őket.
A diagnosztikai beállításoknak a Azure Portal, a CLI vagy a PowerShell használatával történő létrehozásával kapcsolatos részletes folyamatért lásd: [diagnosztikai beállítás létrehozása a platform-naplók és-metrikák összegyűjtéséhez az Azure-ban](../azure-monitor/essentials/diagnostic-settings.md) . Diagnosztikai beállítás létrehozásakor meg kell adnia, hogy a rendszer milyen típusú naplókat gyűjtsön.

A következő kategóriákból gyűjthet naplókat Azure Time Series Insights:

   | Kategória | Leírás |
   |---|---|
   | Bejövő forgalom  | A bejövő forgalom kategóriában nyomon követheti a beáramló folyamat során felmerülő hibákat. Ez a kategória olyan hibákat tartalmaz, amelyek akkor jelentkeznek, amikor eseményeket fogadnak (például egy esemény forrásához való kapcsolódási hibák) és az események feldolgozásának folyamatát (például az események hasznos adatainak elemzésekor felmerülő hibákat). |

## <a name="analyzing-metrics"></a>Mérőszámok elemzése

A metrikákat a Azure Monitor menüből megnyitva elemezheti Azure Time Series Insights mérőszámait, valamint más Azure-szolgáltatások metrikáit is. Az eszköz használatával kapcsolatos részletekért lásd: az [Azure Metrikaböngésző használatának első lépései](../azure-monitor/platform/metrics-getting-started.md) .

A gyűjtött platform metrikáinak listáját itt tekintheti meg: [Monitoring Azure Time Series Insights adatok referenciája](how-to-monitor-tsi-reference.md#metrics)

Ebben a példában az összes eseményforrás által az Azure Time Series Insights-környezetbe fogadott bájtok száma látható.

Bejövő **fogadott bájtok** az [ ![ Azure Time Series bejövő forgalomban fogadott bájtjai](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox)

A példa a Azure Time Series Insights-környezetben a sikeresen feldolgozott és a lekérdezésekhez elérhető bájtok számát jeleníti meg.

**Bejövő forgalomban tárolt bájtok** [ ![ Azure Time Series bejövő forgalomban tárolt bájtjai](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox)

## <a name="analyzing-logs"></a>Naplók elemzése
Az erőforrás-naplókat megadhatja blobként egy Storage-fiókban, az Event-adatként vagy a log analitikus lekérdezésekkel.

Azure Monitor naplókban található adatkészletek olyan táblákban tárolódnak, amelyekben az egyes táblák egyedi tulajdonságokkal rendelkeznek.

Azure Monitor összes erőforrás-naplója ugyanazokkal a mezőkkel rendelkezik, amelyeket a szolgáltatás-specifikus mezők követnek. Az általános séma [Azure monitor erőforrás-naplózási sémában](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)szerepel. A Azure Time Series Insights gyűjtött erőforrás-naplók típusairól a [Azure Time Series Insights monitorozási adatok referenciája](how-to-monitor-tsi-reference.md#resource-logs)című témakörben olvashat.

A Azure Time Series Insights az alábbi táblázatokban tárolja az adattárakat.

| Táblázat | Leírás |
|:---|:---|
| TSIIngress | A bejövő forgalom kategóriából származó adatok tárolására szolgáló tábla. A bejövő forgalom kategóriában nyomon követheti a beáramló folyamat során felmerülő hibákat. Ez a kategória olyan hibákat tartalmaz, amelyek akkor jelentkeznek, amikor eseményeket fogadnak (például egy esemény forrásához való kapcsolódási hibák) és az események feldolgozásának folyamatát (például az események hasznos adatainak elemzésekor felmerülő hibákat).

Az adatok Azure Monitor naplókba való továbbításához létre kell hoznia egy diagnosztikai beállítást, amely erőforrás-naplókat vagy platform-metrikákat küld egy Log Analytics munkaterületre. További információ: [gyűjtemény és útválasztás](../iot-hub/monitor-iot-hub.md#collection-and-routing).

## <a name="sample-queries"></a>Példák a lekérdezésekre

Az alábbi lekérdezések segítségével figyelheti Azure Time Series Insights környezetét:

+ Az elmúlt öt napban az eseményforrás-kapcsolati hibák részleteinek megismerése:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ Az elmúlt öt napban fogadott érvénytelen üzenetek részleteinek beolvasása:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>Riasztások

Azure Monitor riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a rendszeren észlelt problémák azonosítását és megoldását, mielőtt az ügyfelek bejelentkeznek. Riasztásokat állíthat be a [metrikák](../azure-monitor/platform/alerts-metric-overview.md), [naplók](../azure-monitor/platform/alerts-unified-log.md)és a [tevékenység naplójában](../azure-monitor/platform/activity-log-alerts.md). A különböző típusú riasztásokhoz előnyök és hátrányok tartoznak.

A platform metrikái alapján létrehozott riasztási szabályok létrehozásakor vegye figyelembe, hogy a darabszámban összegyűjtött Time Series Insights platform metrikák esetében előfordulhat, hogy egyes összesítések nem lesznek elérhetők vagy használhatók.

## <a name="next-steps"></a>Következő lépések

* A Azure Time Series Insights által létrehozott naplók és metrikák hivatkozását a [Azure Time Series Insights monitorozási adatok referenciája](how-to-monitor-tsi-reference.md) tartalmazza.
* Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../azure-monitor/essentials/monitor-azure-resource.md) .