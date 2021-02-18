---
title: Metrikák, riasztások és diagnosztikai naplók
description: A diagnosztikai napló eseményeinek rögzítése és elemzése Azure Batch fiók erőforrásaihoz, például a készletekhez és a feladatokhoz.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18
ms.openlocfilehash: 83411d7018155955f5be71bd41803e510edbc9da
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592682"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch-metrikák, riasztások és naplók a diagnosztika kiértékeléséhez és figyeléséhez

Ez a cikk ismerteti, hogyan figyelheti a Batch-fiókokat a [Azure monitor](../azure-monitor/overview.md)szolgáltatásainak használatával. A Azure Monitor a Batch-fiók erőforrásaihoz tartozó [mérőszámokat](../azure-monitor/essentials/data-platform-metrics.md) és [diagnosztikai naplókat](../azure-monitor/essentials/platform-logs-overview.md) gyűjt. Gyűjtsön és fogyasszon el különböző módon ezeket az adatokat a Batch-fiók figyeléséhez és a problémák diagnosztizálásához. A [metrikai riasztásokat](../azure-monitor/alerts/alerts-overview.md) úgy is konfigurálhatja, hogy értesítést kapjon, ha egy metrika eléri a megadott értéket.

## <a name="batch-metrics"></a>Batch-metrikák

A metrikák Azure-telemetria adatok (más néven teljesítményszámlálók), amelyeket az Azure-erőforrások bocsátanak ki, és amelyeket a Azure Monitor szolgáltatás használ. A Batch-fiókokban lévő mérőszámok például a készlet létrehozása események, Low-Priority csomópontok száma, valamint a feladat teljes eseményei.

Tekintse meg a [támogatott batch-metrikák listáját](../azure-monitor/essentials/metrics-supported.md#microsoftbatchbatchaccounts).

A metrikák a következők:

- Alapértelmezés szerint engedélyezve van minden batch-fiókban, további konfiguráció nélkül
- Létrehozva 1 percenként
- A rendszer nem őrzi meg automatikusan, de egy 30 napos előzményt tartalmaz. A tevékenység-metrikák a diagnosztikai naplózás részeként is megmaradhatnak.

## <a name="view-batch-metrics"></a>Batch-metrikák megtekintése

A Azure Portal a fiók **Áttekintés** lapja alapértelmezés szerint a fő csomópont, a mag és a feladat metrikáit jeleníti meg.

Az összes batch-fiók metrikájának megtekintése a Azure Portalban:

1. A Azure Portal válassza a **minden szolgáltatás**  >  **Batch-fiókok** lehetőséget, majd válassza ki a Batch-fiók nevét.
2. A **Figyelés** területen kattintson a **Metrikák** elemre.
3. Válassza a **metrika hozzáadása** lehetőséget, majd válasszon ki egy mérőszámot a legördülő listából.
4. Válasszon **összesítési** lehetőséget a metrika számára. A Count-alapú metrikák (például a "dedikált mag darabszáma" vagy az "alacsony prioritású csomópontok száma") esetében az **átlagos** összesítést kell használni. Eseményvezérelt metrikák esetén (például "a készlet átméretezése kész események") használja a **Count** összesítést.

   > [!WARNING]
   > Ne használja a "Sum" összesítést, amely hozzáadja a diagram adott időszakában fogadott adatpontok értékeit.

5. További mérőszámok hozzáadásához ismételje meg a 3. és a 4. lépést.

A metrikákat programozott módon is beolvashatja a Azure Monitor API-kkal. Példa: [Azure monitor mérőszámok beolvasása a .net](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)-tel.

### <a name="batch-metric-reliability"></a>Batch-metrika megbízhatósága

A metrikák segítenek azonosítani a trendeket, és az adatelemzéshez is használhatók. Fontos megjegyezni, hogy a metrikák kézbesítése nem garantált, és a rendelésen kívüli kézbesítés, adatvesztés és/vagy másolás is tárgya lehet. Ezért nem ajánlott egyetlen eseményt riasztási vagy trigger függvények használatára használni. A riasztási küszöbértékek beállításával kapcsolatos további részletekért tekintse meg a következő szakaszt.

Az elmúlt 3 percben kibocsátott metrikák továbbra is összevonhatók, így a metrikai értékek ebben az időkeretben lehetnek.

## <a name="batch-metric-alerts"></a>Batch metrikus riasztások

A közel valós idejű *metrikai riasztásokat* úgy állíthatja be, hogy egy adott metrika értéke egy hozzárendelt küszöbértéket keresztez. A riasztás értesítést küld, ha a riasztás "aktiválva van" (a küszöbérték túllépését és a riasztási feltétel teljesülése esetén), valamint a "feloldva" állapotot (ha a küszöbértéket meghaladják, és a feltétel már nem teljesül).

Az egyetlen adatpontra kiváltott riasztások nem ajánlottak, mivel a metrikák a rendelésen kívüli kézbesítés, az adatvesztés és/vagy az ismétlődések hatálya alá esnek. A riasztások létrehozásakor küszöbértékeket használhat a következetlenségek kiszámításához.

Előfordulhat például, hogy egy metrikus riasztást szeretne beállítani, ha az alacsony prioritású alapszám egy bizonyos szintre esik, így beállíthatja a készletek összeállítását. A legjobb eredmények érdekében állítson be egy 10 vagy több percet, ahol a riasztások akkor aktiválódnak, ha az átlagos alacsony prioritású alapértékek a teljes időszak küszöbértéke alá csökkennek. Ez több időt is lehetővé tesz a mérőszámok összesítésére, így pontosabb eredményeket érhet el.

Metrikai riasztás konfigurálása a Azure Portalban:

1. Válassza a **minden szolgáltatás**  >  **Batch-fiókok** lehetőséget, majd válassza ki a Batch-fiók nevét.
2. A **figyelés** területen válassza a **riasztások**, majd az **új riasztási szabály** lehetőséget.
3. Kattintson a **feltétel kiválasztása**, majd a metrika elemre. Erősítse meg a **diagram időtartamát**, **a küszöbérték típusát**, az **operátort** és az **összesítési típus** értékét, és adjon meg egy **küszöbértéket**. Ezután válassza a **Done** (Kész) elemet.
4. Adjon hozzá egy műveleti csoportot a riasztáshoz egy meglévő műveleti csoport kiválasztásával vagy egy új műveleti csoport létrehozásával.
5. A **riasztási szabály részletei** szakaszban adja meg a **riasztási szabály nevét** és **leírását** , és válassza ki a **súlyosságot** .
6. Válassza a **Riasztási szabály létrehozása** lehetőséget.

A metrikai riasztások létrehozásával kapcsolatos további információkért lásd: a [metrikai riasztások működésének megértése Azure monitor és a](../azure-monitor/alerts/alerts-metric-overview.md) [metrikai riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../azure-monitor/alerts/alerts-metric.md).

A Azure Monitor [REST API](/rest/api/monitor/)használatával is konfigurálhat közel valós idejű riasztást. További információ: [a Microsoft Azure riasztások áttekintése](../azure-monitor/alerts/alerts-overview.md). A feladatok, a feladatok vagy a készletekre vonatkozó információk a riasztásokban való felvételéhez tekintse meg az [Azure monitor riasztásokkal rendelkező eseményekre adott válaszokban](../azure-monitor/alerts/tutorial-response.md)lévő keresési lekérdezéseket ismertető témakört.

## <a name="batch-diagnostics"></a>Batch-diagnosztika

A diagnosztikai naplók az egyes erőforrások működését leíró Azure-erőforrások által kibocsátott információkat tartalmazzák. A Batch szolgáltatásban a következő naplók gyűjthetők:

- A **szolgáltatás naplózza** a Azure batch szolgáltatás által kibocsátott eseményeket egy különálló batch-erőforrás, például egy készlet vagy feladat élettartama során.
- A **metrikák** a fiók szintjén vannak naplózva.

A diagnosztikai naplók gyűjtését engedélyező beállítások alapértelmezés szerint nincsenek engedélyezve. Explicit módon engedélyezze a diagnosztikai beállításokat minden figyelni kívánt batch-fiókhoz.

### <a name="log-destinations"></a>Naplók célhelyei

Gyakori forgatókönyv egy Azure Storage-fiók kiválasztása a napló célhelyként. Ha a naplókat az Azure Storage-ban szeretné tárolni, hozza létre a fiókot a naplók gyűjtésének engedélyezése előtt. Ha a Batch-fiókhoz társított egy Storage-fiókot, a fiókot a napló célhelyként is kiválaszthatja.

Másik lehetőségként a következőket teheti:

- A Batch diagnosztikai napló eseményeinek továbbítása egy [Azure Event hub](../event-hubs/event-hubs-about.md)-ba. A Event Hubs másodpercenként több millió eseményt képes befogadni, amelyet később bármilyen valós idejű elemzési szolgáltató használatával átalakíthat és tárolhat.
- Diagnosztikai naplók küldése [Azure monitor naplókba](../azure-monitor/logs/log-query-overview.md), ahol elemezheti őket, vagy exportálhatja őket elemzésre Power bi vagy Excelben.

> [!NOTE]
> Az Azure-szolgáltatásokkal további költségek is felmerülhetnek a diagnosztikai naplózási információk tárolásához vagy feldolgozásához.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Batch diagnosztikai naplók gyűjtésének engedélyezése

Ha új diagnosztikai beállítást szeretne létrehozni a Azure Portalban, kövesse az alábbi lépéseket.

1. A Azure Portal válassza a **minden szolgáltatás**  >  **Batch-fiókok** lehetőséget, majd válassza ki a Batch-fiók nevét.
2. A **Monitorozás** területen kattintson a **Diagnosztikai beállítások** elemre.
3. A **diagnosztikai beállítások** területen válassza a **diagnosztikai beállítás hozzáadása** elemet.
4. Adja meg a beállítás nevét.
5. Válasszon egy célhelyet: **küldés log Analytics**, **archiválás egy Storage-fiókba**, vagy **stream az Event hub**-ba. Ha a Storage-fiókot választja, megadhat egy adatmegőrzési szabályt. Ha nem ad meg napokat a megőrzéshez, a rendszer a Storage-fiók élettartama alatt megőrzi az adatok mennyiségét.
6. Válassza ki a **ServiceLog**, a **AllMetrics** vagy mindkettőt.
7. A diagnosztikai beállítás létrehozásához válassza a **Mentés** lehetőséget.

[A Azure Portal Azure monitoron keresztül is engedélyezheti a gyűjteményt a](../azure-monitor/essentials/diagnostic-settings.md) diagnosztikai beállítások konfigurálásához egy [Resource Manager-sablon](../azure-monitor/essentials/resource-manager-diagnostic-settings.md), illetve az Azure POWERSHELL vagy az Azure CLI használatával. További információ: [Az Azure platform naplófájljainak áttekintése](../azure-monitor/essentials/platform-logs-overview.md).

### <a name="access-diagnostics-logs-in-storage"></a>Diagnosztikai naplók elérése a Storage-ban

Ha egy Storage-fiókba archiválja a Batch-diagnosztikai naplókat, akkor a rendszer egy tárolót hoz létre a Storage-fiókban, amint egy kapcsolódó esemény következik be. A Blobok a következő elnevezési mintának megfelelően jönnek létre:

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Például:

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

Minden `PT1H.json` blob-fájl JSON-formázott eseményeket tartalmaz, amelyek a blob URL-címében megadott órán belül történtek (például: `h=12` ). A jelen órában az eseményeket a rendszer hozzáfűzi a `PT1H.json` fájlhoz, ahogy azok bekövetkeznek. A perc értéke ( `m=00` ) mindig `00` , mivel a diagnosztikai napló eseményeinek az egyes blobokra való felosztása óránként történik. (Az összes alkalommal UTC-ben van.)

Az alábbi példa egy `PoolResizeCompleteEvent` naplófájlban lévő bejegyzésre mutat `PT1H.json` . A dedikált és alacsony prioritású csomópontok aktuális és megcélzott számával, valamint a művelet kezdési és befejezési időpontjával kapcsolatos információkat tartalmaz:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

A Storage-fiókban található diagnosztikai naplók sémájával kapcsolatos további információkért lásd: [Azure-erőforrások naplófájljainak archiválása a Storage-fiókba](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). A Storage-fiókban lévő naplók programozott módon való eléréséhez használja a Storage API-kat.

### <a name="service-log-events"></a>Szolgáltatás-naplózási események

Azure Batch a szolgáltatás naplóit, ha összegyűjtöttük, az adott batch-erőforrás (például készlet vagy feladat) élettartama során a Azure Batch szolgáltatás által kibocsátott eseményeket tartalmazza. A Batch által kibocsátott összes eseményt JSON formátumban naplózza a rendszer. Ez például egy minta- **készlet létrehozási eseményének** törzse:

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
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

A Batch szolgáltatás által kibocsátott szolgáltatás-naplózási események közé a következők tartoznak:

- [Készlet létrehozása](batch-pool-create-event.md)
- [Készlet törlésének kezdete](batch-pool-delete-start-event.md)
- [Készlet törlése kész](batch-pool-delete-complete-event.md)
- [Készlet átméretezésének kezdete](batch-pool-resize-start-event.md)
- [A készlet átméretezése befejeződött](batch-pool-resize-complete-event.md)
- [Készlet-méretezés](batch-pool-autoscale-event.md)
- [Feladat indítása](batch-task-start-event.md)
- [A feladat befejeződött](batch-task-complete-event.md)
- [Sikertelen feladat](batch-task-fail-event.md)
- [Sikertelen feladat-ütemterv](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Következő lépések

- Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
- További információ a [Batch-megoldások figyeléséről](monitoring-overview.md).