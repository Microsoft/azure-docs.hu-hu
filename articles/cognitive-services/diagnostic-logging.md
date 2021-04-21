---
title: Diagnosztikai naplózás
titleSuffix: Azure Cognitive Services
description: Ez az útmutató részletes útmutatást nyújt az Azure Cognitive Services diagnosztikai naplózásának engedélyezéséhez. Ezek a naplók részletes, gyakori adatokat biztosítanak a problémák azonosításához és hibakereséséhez használt erőforrások működéséhez.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 4a78e233a41bf3b6682f52bac912528d6bcab76c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816333"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Diagnosztikai naplózás engedélyezése a Azure Cognitive Services

Ez az útmutató részletes útmutatást nyújt az Azure Cognitive Services diagnosztikai naplózásának engedélyezéséhez. Ezek a naplók részletes, gyakori adatokat biztosítanak a problémák azonosításához és hibakereséséhez használt erőforrások működéséhez. A folytatás előtt olyan Azure-fiókkal kell rendelkezik, amely legalább egy Cognitive Services-előfizetéssel rendelkezik, például [Bing Web Search,](./bing-web-search/overview.md) [Speech Services](./speech-service/overview.md)vagy [LUIS.](./luis/what-is-luis.md)

## <a name="prerequisites"></a>Előfeltételek

A diagnosztikai naplózás engedélyezéséhez szüksége lesz egy helyre a naplóadatok tárolására. Ez az oktatóanyag az Azure Storage és a Log Analytics szolgáltatást használja.

* [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) – Megőrzi a diagnosztikai naplókat a szabályzatok naplózásához, a statikus elemzéshez vagy a biztonsági mentéshez. A tárfióknak nem kell ugyanabban az előfizetésben lennie, mint a naplókat kibocsátó erőforrásnak, ha a beállítást konfiguráló felhasználó megfelelő Azure RBAC-hozzáféréssel rendelkezik mindkét előfizetéshez.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) – Egy rugalmas naplókeresési és -elemző eszköz, amely lehetővé teszi az Azure-erőforrások által létrehozott nyers naplók elemzését.

> [!NOTE]
> További konfigurációs beállítások érhetők el. További információ: Naplóadatok gyűjtése és [felhasználása az Azure-erőforrásokból.](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="enable-diagnostic-log-collection"></a>Diagnosztikai naplógyűjtés engedélyezése  

Kezdjük a diagnosztikai naplózás engedélyezésével a Azure Portal.

> [!NOTE]
> Ha a PowerShell vagy az Azure CLI használatával szeretné engedélyezni ezt a funkciót, kövesse a Naplóadatok gyűjtése és felhasználása az [Azure-erőforrásokból útmutatót.](../azure-monitor/essentials/platform-logs-overview.md)

1. Lépjen az Azure Portalra. Ezután keresse meg és válassza ki Cognitive Services erőforrást. Például az előfizetése a Bing Web Search.   
2. Ezután a bal oldali navigációs menüben keresse meg a **Figyelés elemet,** és válassza a **Diagnosztikai beállítások lehetőséget.** Ezen a képernyőn az erőforrás összes korábban létrehozott diagnosztikai beállítása látható.
3. Ha korábban létrehozott erőforrást szeretne használni, most kiválaszthatja. Ellenkező esetben válassza **a + Diagnosztikai beállítás hozzáadása lehetőséget.**
4. Adja meg a beállítás nevét. Ezután válassza **az Archive to a storage account (Archiválás tárfiókba) és a** Send to log Analytics **(Küldés a Log Analyticsbe) lehetőséget.**
5. Amikor a rendszer a konfigurálás során kéri, válassza ki a diagnosztikai naplók tárolására használni kívánt tárfiókot és OMS-munkaterületet. **Megjegyzés:** Ha nincs tárfiókja vagy OMS-munkaterülete, kövesse az utasításokat a fiók létrehozásához.
6. Válassza **az Audit**, **RequestResponse**, és **AllMetrics lehetőséget.** Ezután állítsa be a diagnosztikai naplóadatok megőrzési időszakát. Ha a megőrzési szabályzat nulla, az arra a naplózási kategóriára vonatkozó események határozatlan ideig tárolódnak.
7. Kattintson a **Mentés** gombra.

A naplózási adatok lekérdezése és elemzése akár két órát is igénybe vehet. Ne aggódjon, ha nem lát semmit azonnal.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Diagnosztikai adatok megtekintése és exportálása az Azure Storage-ból

Az Azure Storage egy robusztus objektumtárolási megoldás, amely nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. Ebben a szakaszban megtudhatja, hogyankérdezheti le a tárfiókban a 30 napos időszakra vonatkozó összes tranzakciót, és exportálhatja az adatokat az Excelbe.

1. A Azure Portal keresse meg az előző szakaszban létrehozott Azure Storage-erőforrást.
2. A bal oldali navigációs menüben keresse meg a **Figyelés elemet, és** válassza a **Metrikák lehetőséget.**
3. Az elérhető legördülő menük használatával konfigurálhatja a lekérdezést. Ebben a példában az időtartományt az Elmúlt **30** nap, a metrikát pedig a Transaction (Tranzakció) **beállításra állítva adhatja meg.**
4. Ha a lekérdezés befejeződött, az elmúlt 30 nap tranzakciós vizualizációja látható. Az adatok exportáláshoz használja az oldal tetején található Exportálás **Excelbe** gombot.

További információ az Azure Storage diagnosztikai [adataival kapcsolatos funkciókról.](../storage/blobs/storage-blobs-introduction.md)

## <a name="view-logs-in-log-analytics"></a>Naplók megtekintése a Log Analyticsben

Kövesse ezeket az utasításokat az erőforrás naplóelemzési adatainak feltárására.

1. A Azure Portal bal oldali navigációs menüben keresse meg és válassza ki a **Log Analytics** elemet.
2. Keresse meg és válassza ki a diagnosztika engedélyezésekor létrehozott erőforrást.
3. Az **Általános alatt** keresse meg és válassza a Naplók **lehetőséget.** Ezen az oldalon lekérdezéseket futtathat a naplókra.

### <a name="sample-queries"></a>Mintalekérdezések

Íme néhány alapszintű Kusto-lekérdezés, amelyek a naplóadatok feltárására használhatók.

Futtassa ezt a lekérdezést a Azure Cognitive Services megadott időszakra vonatkozó összes diagnosztikai naplóhoz:

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

Futtassa ezt a lekérdezést a műveletek erőforrás szerint való **csoportosításhoz:**

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Futtassa ezt a lekérdezést a művelet végrehajtásához szükséges átlagos idő megkereséhez:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Futtassa ezt a lekérdezést a műveletek műveletmennyiségének OperationName szerint felosztásban való megtekintéséhez, 10-edikre rekeszes számokkal.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Következő lépések

* A naplózás engedélyezésének, valamint a különböző Azure-szolgáltatások által támogatott metrikáknak [](../azure-monitor/data-platform.md) és naplókategóriáknak a megtekintéséhez olvassa el a metrikák áttekintését az Microsoft Azure-ban és az [Azure Diagnosztikai](../azure-monitor/essentials/platform-logs-overview.md) naplók áttekintése cikket.
* Az event hubsról az alábbi cikkekben olvashat:
  * [Mi az Azure Event Hubs?](../event-hubs/event-hubs-about.md)
  * [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* Olvassa el [a Metrikák és diagnosztikai naplók letöltése az Azure Storage-ból szakaszt.](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs)
* Olvassa [el a Naplókeresések a naplókban Azure Monitor cikkeket.](../azure-monitor/logs/log-query-overview.md)