---
title: Diagnosztikai naplózás
titleSuffix: Azure Cognitive Services
description: Ez az útmutató lépésről lépésre bemutatja, hogyan engedélyezheti az Azure kognitív szolgáltatás diagnosztikai naplózását. Ezek a naplók részletes és gyakori információkat biztosítanak egy olyan erőforrás működéséről, amely az azonosításhoz és a hibakereséshez használatos.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "71827904"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Diagnosztikai naplózás engedélyezése az Azure Cognitive Services

Ez az útmutató lépésről lépésre bemutatja, hogyan engedélyezheti az Azure kognitív szolgáltatás diagnosztikai naplózását. Ezek a naplók részletes és gyakori információkat biztosítanak egy olyan erőforrás működéséről, amely az azonosításhoz és a hibakereséshez használatos. A folytatás előtt rendelkeznie kell egy olyan Azure-fiókkal, amely rendelkezik előfizetéssel legalább egy olyan kognitív szolgáltatáshoz, mint például a [Bing Web Search](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), a [Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)vagy a [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Előfeltételek

A diagnosztikai naplózás engedélyezéséhez a naplófájlok tárolásához valahol szüksége lesz. Ez az oktatóanyag az Azure Storage és a Log Analytics használatát ismerteti.

* [Azure Storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) – a házirend-naplózás, a statikus elemzés vagy a biztonsági mentés diagnosztikai naplóinak megőrzése. A Storage-fióknak nem kell ugyanabban az előfizetésben lennie, mint az erőforrás-kibocsátó naplókat, ha a beállítást konfiguráló felhasználó mindkét előfizetéshez megfelelő RBAC-hozzáféréssel rendelkezik.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) – egy rugalmas napló keresési és elemzési eszköze, amely lehetővé teszi az Azure-erőforrások által generált nyers naplók elemzését.

> [!NOTE]
> További konfigurációs beállítások érhetők el. További információ: [adatok gyűjtése és felhasználása az Azure-erőforrásokból](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="enable-diagnostic-log-collection"></a>Diagnosztikai naplók gyűjtésének engedélyezése  

Kezdjük a diagnosztikai naplózás engedélyezésével a Azure Portal használatával.

> [!NOTE]
> Ha engedélyezni szeretné a szolgáltatást a PowerShell vagy az Azure CLI használatával, kövesse az [Azure-erőforrások adatainak összegyűjtése és felhasználása](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)című részben található utasításokat.

1. Lépjen az Azure Portalra. Ezután keresse meg és válassza ki a Cognitive Services erőforrást. Például a Bing Web Search előfizetése.   
2. Ezután a bal oldali navigációs menüben keresse meg a **figyelés** elemet, és válassza a **diagnosztikai beállítások**lehetőséget. Ez a képernyő az erőforráshoz korábban létrehozott összes diagnosztikai beállítást tartalmazza.
3. Ha egy korábban létrehozott erőforrást szeretne használni, kiválaszthatja most. Ellenkező esetben válassza a **+ diagnosztikai beállítás hozzáadása**elemet.
4. Adja meg a beállítás nevét. Ezután válassza az **archiválás egy Storage-fiókba** lehetőséget, majd **küldje el a log Analyticsnek**.
5. Ha a rendszer kéri a konfigurálását, válassza ki azt a Storage-fiókot és OMS-munkaterületet, amelyet a diagnosztikai naplók tárolására kíván használni. **Megjegyzés**: Ha nem rendelkezik Storage-fiókkal vagy OMS-munkaterülettel, kövesse az utasításokat, és hozzon létre egyet.
6. Válassza a **naplózás**, a **RequestResponse**és a **AllMetrics**lehetőséget. Ezután állítsa be a megőrzési időszakot a diagnosztikai napló adataihoz. Ha egy adatmegőrzési szabály értéke nulla, a rendszer határozatlan ideig tárolja az adott naplóhoz tartozó eseményeket.
7. Kattintson a **Save** (Mentés) gombra.

Akár két órát is igénybe vehet, mielőtt a naplózási szolgáltatás elérhetővé válik a lekérdezéshez és az elemzéshez. Ezért ne aggódjon, ha azonnal nem lát semmit.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Diagnosztikai adatok megtekintése és exportálása az Azure Storage-ból

Az Azure Storage egy robusztus objektum-tárolási megoldás, amely nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. Ebben a szakaszban megtudhatja, hogyan kérdezheti le a Storage-fiókját az összes tranzakcióhoz egy 30 napos időkereten belül, és exportálhatja az adatait az Excelbe.

1. A Azure Portal keresse meg a legutóbbi szakaszban létrehozott Azure Storage-erőforrást.
2. A bal oldali navigációs menüben keresse meg a **figyelés** elemet, és válassza a **metrikák**lehetőséget.
3. A lekérdezés konfigurálásához használja a rendelkezésre álló legördülő listát. Ebben a példában beállítjuk az időtartományot az **utolsó 30 napra** , a metrikát pedig a **tranzakcióra**.
4. A lekérdezés befejezésekor a tranzakció vizualizációja jelenik meg az elmúlt 30 napban. Az adatexportáláshoz használja az oldal tetején található **Exportálás az Excel programba** gombot.

További információ az [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)-ban található diagnosztikai információkról.

## <a name="view-logs-in-log-analytics"></a>Naplók megtekintése a Log Analyticsben

Kövesse ezeket az utasításokat az erőforrás log Analytics-adatainak megismeréséhez.

1. A Azure Portalban keresse meg és válassza ki a **log Analytics** elemet a bal oldali navigációs menüből.
2. Keresse meg és válassza ki a diagnosztika engedélyezésekor létrehozott erőforrást.
3. Az **általános**területen keresse meg és válassza ki a **naplókat**. Ezen az oldalon futtathat lekérdezéseket a naplókon.

### <a name="sample-queries"></a>Mintalekérdezések

Íme néhány alapszintű Kusto-lekérdezés, amellyel megismerheti a naplózási adatait.

A lekérdezés futtatása az Azure Cognitive Services összes diagnosztikai naplójában egy adott időszakra vonatkozóan:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Futtassa ezt a lekérdezést a 10 legutóbbi napló megtekintéséhez:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

A lekérdezés futtatásával csoportosíthatja a műveleteket **erőforrás**szerint:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
A lekérdezés futtatásával megkeresheti a művelet végrehajtásához szükséges átlagos időt:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Ennek a lekérdezésnek a futtatásával megtekintheti a műveletek mennyiségét a OperationName és a Counts dobozolni megosztva az összes 10-es értéknél.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan engedélyezheti a naplózást, valamint a különböző Azure-szolgáltatások által támogatott mérőszámokat és naplózási kategóriákat, olvassa el a Microsoft Azure [metrikáinak áttekintését](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) , valamint az [Azure diagnosztikai naplók áttekintését ismertető](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) cikket.
* Az Event hubok megismeréséhez olvassa el az alábbi cikkeket:
  * [Mi az az Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Bevezetés az Event Hubs használatába](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* [Az Azure Storage-ból letölthető mérőszámok és diagnosztikai naplók](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs)olvashatók.
* Olvassa el [a Azure monitor naplókban végzett keresések ismertetése](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new)című témakört.
