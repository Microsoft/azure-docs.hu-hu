---
title: Metrikák, riasztások és diagnosztikai naplók – Azure Batch | Microsoft Docs
description: Jegyezze fel, és elemezze a diagnosztikai naplót eseményeire az Azure Batch-fiók erőforrásokat, például a készletek és tevékenységek.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: aa86d6cf22562fa1fac7d45de20b28aa0eec33aa
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261673"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch-metrikák, a riasztások és a naplókat a további diagnosztikai kiértékeléséhez és figyeléséhez

 
Ez a cikk azt ismerteti, hogyan szolgáltatását használja, amely a Batch-fiók figyelése [Azure Monitor](../azure-monitor/overview.md). Az Azure Monitor gyűjt [metrikák](../azure-monitor/platform/data-platform-metrics.md) és [diagnosztikai naplók](../azure-monitor/platform/resource-logs-overview.md) az erőforrásokat a Batch-fiókban. Összegyűjtheti, és számos módon figyelheti a Batch-fiókhoz, és diagnosztizálhatja a problémákat a feldolgozásukhoz. Beállíthatja úgy is [metrikákhoz kapcsolódó riasztások](../azure-monitor/platform/alerts-overview.md) így értesítést kapjon a metrika eléri a megadott értéket. 

## <a name="batch-metrics"></a>Batch-metrikák

Metrikák az Azure-erőforrások, amelyek használják az Azure Monitor szolgáltatás által kibocsátott (más néven teljesítményszámlálók) Azure telemetriai adatokat. A Batch-fiók metrikái például a következők: A készletben események hozhatók létre, alacsony prioritású csomópontok száma és a feladat teljes eseményei. 

Tekintse meg a [Batch támogatott mérőszámok listája](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Metrikák a következők:

* Minden egyes Batch-fiókban, további konfiguráció nélkül alapértelmezés szerint engedélyezve
* Generált 1 percenként
* Nem maradnak meg automatikusan, de van egy 30 napos gördülő előzményei. A tevékenység-metrikák a diagnosztikai naplózás részeként is megmaradhatnak.

### <a name="view-metrics"></a>Metrikák megtekintése

A Batch-fiók metrikák megtekintése az Azure Portalon. A **áttekintése** alapértelmezés szerint a fiók jeleníti meg a fő csomópont, a core és a feladat metrikák oldala. 

Az összes Batch-fiók metrikákat tekinthet meg: 

1. Kattintson a portál **minden szolgáltatás** > **Batch-fiókok**, majd kattintson a Batch-fiók nevére.
2. A **figyelés**, kattintson a **metrikák**.
3. Válassza ki egy vagy több metrikákat. Ha azt szeretné, válassza a további erőforrás-mérőszámok segítségével a **előfizetések**, **erőforráscsoport**, **erőforrástípus**, és **erőforrás** a legördülő menük.
    * A Count-alapú metrikák esetében (például a "dedikált mag darabszáma" vagy az "alacsony prioritású csomópontok száma") az "átlag" összesítést használja. Eseményvezérelt metrikák esetén (például "a készlet átméretezése kész események") használja a "Count" összesítést.

    ![Batch-metrikák](media/batch-diagnostics/metrics-portal.png)

Metrikák programozott módon lekéréséhez használja az Azure Monitor API-kat. Lásd a [lekérése az Azure Monitor-metrikák .NET-tel](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Batch-metrika megbízhatóság

Metrikák trendelemzés és az adatok elemzési célokra szolgálnak. Metrika kézbesítési nem garantált, és a kimenő soron kívüli kézbesítési, adatvesztés és/vagy ismétlődést használatára. Egyetlen eseményeket a riasztás vagy az eseményindító függvények használata nem ajánlott. Tekintse meg a [Batch-metrikákhoz kapcsolódó riasztások](#batch-metric-alerts) riasztási küszöbértékek beállításával kapcsolatos további részletekért.

Az elmúlt 3 perc kibocsátott metrikák továbbra is lehetséges, hogy összesíti. Ez idő alatt a metrikaértékek is vezethet.

## <a name="batch-metric-alerts"></a>A Batch metrikákhoz kapcsolódó riasztások

Igény szerint állítsa be közel valós idejű *metrikákhoz kapcsolódó riasztások* , amely indítható el, ha egy adott mérőszám értéke átlép egy küszöbértéket, hozzárendelt. A riasztást állít elő egy [értesítési](../monitoring-and-diagnostics/insights-alerts-portal.md) úgy dönt, hogy ha a riasztás "aktív" (amikor áthaladnak a küszöbértéket, és a riasztási feltétel nem teljesül), valamint amikor azt "megoldódott" (amikor újra áthaladnak a küszöbértéket, és a feltétel nem már teljesül). Egyetlen adatpont alapuló riasztások nem ajánlott, mivel a metrikák out soron kívüli kézbesítési, az adatvesztést és/vagy duplikált vonatkozik. Riasztási kell, használja a küszöbértékek áthidalhatók az ezeket az inkonzisztenciákat.

Például érdemes metrikariasztás konfigurálása, ha az alacsony prioritású magok száma egy bizonyos szintre csökken, így módosíthatja a készletek az összeállításban. Javasoljuk, hogy állítsa be legalább 10 percig, ahol riasztásokat indítás, ha az átlagos alacsony prioritású magok száma a küszöbérték érték alá csökken, a teljes időszakra. Egy 1 – 5 percnél hosszabb ideig a riasztást, metrikák továbbra is lehetséges, hogy összesítés nem ajánlott.

Metrikariasztás konfigurálása a portálon:

1. Kattintson a **Minden szolgáltatás** > **Batch-fiókok** lehetőségre, majd a Batch-fiók nevére.
2. A **figyelés**, kattintson a **riasztási szabályok** > **metrikariasztás hozzáadása**.
3. Válassza ki a metrika, egy riasztási feltétel (például ha egy metrika egy időszakban meghalad egy bizonyos értéket) és egy vagy több értesítés.

A közel valós idejű riasztási használatával is konfigurálhatja a [REST API-val](https://docs.microsoft.com/rest/api/monitor/). További információkért lásd: [riasztások – áttekintés](../azure-monitor/platform/alerts-overview.md)

## <a name="batch-diagnostics"></a>Batch-diagnosztika

Diagnosztikai naplók az Azure-erőforrások, amelyek ismertetik az egyes erőforrások a művelet által kibocsátott információkat tartalmaznak. A Batch gyűjtse össze a következő naplók kapcsolódnak:

* **Szolgáltatási naplók** egyes Batch-erőforrások teljes élettartama alatt az Azure Batch szolgáltatás által kibocsátott események, például egy készlettel vagy feladattal. 

* **Metrikák** naplók a fiók szintjén. 

Alapértelmezés szerint nem engedélyezettek a beállítások a diagnosztikai naplók gyűjtésének engedélyezéséhez. Explicit módon a figyelni kívánt minden egyes Batch-fiókhoz tartozó diagnosztikai beállítások engedélyezése.

### <a name="log-destinations"></a>Napló célhelyek

Gyakran előfordul, hogy válassza ki az Azure Storage-fiók log céljaként. Az Azure Storage-naplók tárolásához, a fiók létrehozása-naplók gyűjtésének engedélyezése előtt. A Batch-fiók egy storage-fiókot társított, ha kiválaszthatja, hogy a fiók log céljaként. 

A diagnosztikai naplók számára más választható célok:

* Kötegelt diagnosztikai napló események a Stream- [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Az Event Hubs fogadására képes több millió esemény / másodperc, amely, átalakíthatja és tárolhatja bármilyen valós idejű elemzési szolgáltató segítségével. 

* Diagnosztikai naplók küldése [Azure monitor naplókba](../log-analytics/log-analytics-overview.md), ahol elemezheti őket, vagy exportálhatja őket elemzésre Power bi vagy Excelben.

> [!NOTE]
> Ön további költségekkel járhat tárolásához, vagy az Azure-szolgáltatások diagnosztikai naplóadatokat feldolgozni. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Diagnosztikai naplók a Batchben gyűjtésének engedélyezéséhez

1. Kattintson a portál **minden szolgáltatás** > **Batch-fiókok**, majd kattintson a Batch-fiók nevére.
2. A **figyelés**, kattintson a **diagnosztikai naplók** > **diagnosztika bekapcsolása**.
3. A **diagnosztikai beállítások**területen adja meg a beállítás nevét, és válassza ki a napló célhelyét (meglévő Storage-fiók, Event Hub vagy Azure monitor napló). Válassza ki az egyik vagy mindkét **ServiceLog** és **AllMetrics**.

    Amikor kiválaszt egy tárfiókot, igény szerint adatmegőrzési szabály beállításához. Ha nem adja meg a megőrzési napok száma, adatok megőrződnek a storage-fiók élettartama során.

4. Kattintson a **Save** (Mentés) gombra.

    ![Batch-diagnosztika](media/batch-diagnostics/diagnostics-portal.png)

Az Erőforrásnapló-gyűjtés engedélyezése más lehetőségek a következők: a portálon az Azure Monitor használatával diagnosztikai beállítások konfigurálása, használja a [Resource Manager-sablon](../azure-monitor/platform/diagnostic-settings-template.md), vagy az Azure PowerShell vagy az Azure parancssori felület. Lásd: [gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások](../azure-monitor/platform/resource-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Hozzáférés diagnosztikai naplók storage-ban

Ha archiválja kötegelt diagnosztikai naplókat egy tárfiókban, egy storage-tárolót a storage-fiókban jön létre, amint a kapcsolódó esemény következik be. Blobok jönnek létre a következő elnevezési mintának megfelelően:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Példa:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Minden `PT1H.json` blob-fájl JSON-formázott eseményeket tartalmaz, amelyek a blob URL-címében megadott órán belül történtek `h=12`(például:). A jelen órában az eseményeket a rendszer hozzáfűzi a `PT1H.json` fájlhoz, ahogy azok bekövetkeznek. A perc értéke (`m=00`) mindig `00`, mivel a diagnosztikai napló eseményeinek az egyes blobokra való felosztása óránként történik. (UTC szerint is minden esetben.)

Az alábbi példa `PoolResizeCompleteEvent` egy `PT1H.json` naplófájlban lévő bejegyzésre mutat. A dedikált és alacsony prioritású csomópontok aktuális és megcélzott számával, valamint a művelet kezdési és befejezési időpontjával kapcsolatos információkat tartalmaz:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

További információ a tárfiókot a diagnosztikai naplók a séma: [archiválása az Azure diagnosztikai naplók](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-resource-logs-in-storage-account). A Storage API-k használatával programozott módon hozzáférni az a tárfiókban lévő naplókat. 

### <a name="service-log-events"></a>Szolgáltatás bejelentkezési események
Az Azure szolgáltatási naplók a Batchben, ha a gyűjtött, a Batch-készlettel vagy feladattal például egyedi erőforrásokat élettartama során az Azure Batch szolgáltatás által kibocsátott események tartalmaznak. Minden egyes köteg által kibocsátott naplóz JSON formátumban. Például ez a minta törzse **készlet létrehozása esemény**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

A Batch szolgáltatás jelenleg a következő szolgáltatás bejelentkezési eseményeket bocsát ki. Ez a lista nem lehet teljes, mivel további események is bővült, mivel ez a cikk utolsó frissítés.

| **Szolgáltatás bejelentkezési események** |
| --- |
| [Készlet létrehozása](batch-pool-create-event.md) |
| [Készlet törlésének indítása](batch-pool-delete-start-event.md) |
| [Készlet törlése kész](batch-pool-delete-complete-event.md) |
| [Készlet átméretezésének indítása](batch-pool-resize-start-event.md) |
| [Készlet átméretezése kész](batch-pool-resize-complete-event.md) |
| [Zahájení úlohy](batch-task-start-event.md) |
| [A feladat befejezése](batch-task-complete-event.md) |
| [A feladat sikertelen](batch-task-fail-event.md) |



## <a name="next-steps"></a>További lépések

* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
* Tudjon meg többet [Batch-megoldások figyelési](monitoring-overview.md).
