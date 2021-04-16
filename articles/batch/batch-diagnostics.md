---
title: Metrikák, riasztások és diagnosztikai naplók
description: A fiókerőforrások( például készletek és feladatok Azure Batch diagnosztikai naplóesemények rögzítésére és elemzésére.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: seodec18
ms.openlocfilehash: 61aaca84b609aaf7513c6de6f0f7e73aef5a5efe
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389315"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch-metrikák, riasztások és naplók diagnosztikai értékeléshez és monitorozáshoz

Azure Monitor gyűjti a saját [](../azure-monitor/essentials/platform-logs-overview.md) [fiókjában](../azure-monitor/essentials/data-platform-metrics.md) Azure Batch erőforrások metrikákat és diagnosztikai naplókat.

Az adatokat többféleképpen is összegyűjtheti és felhasználhatja a Batch-fiók monitorásához és a problémák diagnosztizálásához. A [metrikariasztásokat](../azure-monitor/alerts/alerts-overview.md) úgy is konfigurálhatja, hogy értesítéseket kapjanak, amikor egy metrika elér egy adott értéket.

## <a name="batch-metrics"></a>Batch-metrikák

[A metrikák](../azure-monitor/essentials/data-platform-metrics.md)  olyan Azure-beli telemetriai adatok (más néven teljesítményszámlálók), amelyet az Azure-erőforrások bocsátanak ki, és a Azure Monitor használnak fel. Példák a Batch-fiókban található metrikákra: Készlet létrehozása események, csomópontok Low-Priority és Tevékenység befejezése események. Ezek a metrikák segítenek a trendek azonosításában, és adatelemzéshez használhatók.

Tekintse meg [a támogatott Batch-metrikák listáját.](../azure-monitor/essentials/metrics-supported.md#microsoftbatchbatchaccounts)

A metrikák a következőek:

- Alapértelmezés szerint engedélyezve van minden Batch-fiókban további konfiguráció nélkül
- Generálva 1 percenként
- Nem marad meg automatikusan, de 30 napos gördülő előzményeket vezet be. A tevékenységmetrikák a diagnosztikai naplózás részeként is megmaradhatnak.

## <a name="view-batch-metrics"></a>Batch-metrikák megtekintése

A Azure Portal Batch-fiók **Áttekintés** lapján alapértelmezés szerint a fő csomópont-, mag- és tevékenységmetrikák fognak jelenni.

Batch-fiók további metrikainak megtekintése:

1. A Azure Portal válassza a **Minden** szolgáltatás Batch-fiók lehetőséget, majd válassza ki a  >  Batch-fiók nevét.
1. A **Figyelés** területen kattintson a **Metrikák** elemre.
1. Válassza **a Metrika hozzáadása** lehetőséget, majd válasszon ki egy metrikát a legördülő listából.
1. Válassza ki a **metrika** Összesítés beállítását. Számalapú metrikákhoz (például "Dedikált magok száma" vagy "Alacsony prioritású csomópontok száma") használja az **átlagos összesítést.** Eseményalapú metrikákhoz (például "Készlet átméretezésének befejezése események") használja a **Darabszám**" aggregációt. Kerülje a **Sum (Összeg)** összesítést, amely összeadja a diagram időszakában fogadott összes adatpont értékeit.
1. További metrikák hozzáadásához ismételje meg a 3. és a 4. lépést.

A metrikákat programozott módon is lekérheti a Azure Monitor API-okkal. Példaként tekintse meg [a lekérdezési Azure Monitor .NET-et.](/samples/azure-samples/monitor-dotnet-metrics-api/monitor-dotnet-metrics-api/)

> [!NOTE]
> Előfordulhat, hogy az elmúlt 3 percben kibocsátott metrikák továbbra is összesítőek, ezért előfordulhat, hogy az értékekről ez az időkeret alatt nem lesz jelentés. A metrikak kézbesítése nem garantált, és a kézbesítést, az adatvesztést vagy a duplikálást befolyásolhatja.

## <a name="batch-metric-alerts"></a>Batch-metrikák riasztásai

Közel valós idejű metrikákra vonatkozó riasztásokat konfigurálhat, amelyek akkor aktiválódnak, ha egy adott metrika értéke átlép egy hozzárendelt küszöbértéket. A riasztás akkor hoz létre értesítést, ha a riasztás "Aktiválva" (a küszöbérték átlépése és a riasztási feltétel teljesülése esetén), valamint a "Feloldva" állapot esetén (ha a küszöbértéket újra átlépik, és a feltétel már nem teljesül).

Mivel a metrikák kézbesítése inkonzisztenciákhoz, például rendelésen túli kézbesítéshez, adatvesztéshez vagy duplikáláshoz vezethet, javasoljuk, hogy kerülje az egyetlen adatponton aktiválódó riasztásokat. Ehelyett használjon küszöbértékeket az inkonzisztenciák, például a rendelésen túli kézbesítés, az adatvesztés és a duplikálás egy adott időszakra vonatkozó kiesésének figyelembe vennie.

Előfordulhat például, hogy olyan metrikás riasztást szeretne konfigurálni, amikor az alacsony prioritású magok száma egy bizonyos szintre esik, így módosíthatja a készletek összetételét. A legjobb eredmény érdekében állítson be egy 10 vagy több perces időszakot, amelyben a riasztás akkor aktiválódik, ha az alacsony prioritású magok átlagos száma a teljes időszak küszöbértéke alá esik. Ez időt biztosít a metrikák összesítésének, hogy pontosabb eredményeket kap.

Metrikariasztás konfigurálása a Azure Portal:

1. Válassza **a Minden szolgáltatás**  >  **Batch-fiók** lehetőséget, majd válassza ki a Batch-fiók nevét.
1. A **Figyelés alatt** válassza a **Riasztások,** majd az **Új riasztási szabály lehetőséget.**
1. Válassza **a Feltétel hozzáadása** lehetőséget, majd válasszon metrikát.
1. Válassza ki a kívánt értékeket a **Diagram** időszaka, **a Küszöbérték,** **az Operátor** és az **Összesítés típushoz.**
1. Adjon meg **egy Küszöbértéket,** és válassza ki a **küszöbérték egységét.**  Ezután válassza a **Done** (Kész) elemet.
1. Adjon hozzá [egy műveletcsoportot](../azure-monitor/alerts/action-groups.md) a riasztáshoz egy meglévő műveletcsoport kiválasztásával vagy egy új műveletcsoport létrehozásával.
1. A **Riasztási szabály részletei szakaszban** adjon meg egy **riasztási szabály nevét és** **leírását.** Ha azonnal engedélyezni szeretné a riasztást, győződjön meg arról, hogy a Riasztási szabály engedélyezése **létrehozáskor** jelölőnégyzet be van jelölve.
1. Válassza a **Riasztási szabály létrehozása** lehetőséget.

A metrikariasztásokkal [](../azure-monitor/alerts/alerts-metric-overview.md) kapcsolatos további információkért lásd: A metrikákra vonatkozó riasztások Azure Monitor és A metrikák riasztásai létrehozása, megtekintése és kezelése a [Azure Monitor.](../azure-monitor/alerts/alerts-metric.md)

Közel valós idejű riasztást is konfigurálhat a következő [Azure Monitor REST API.](/rest/api/monitor/) További információ: [A riasztások áttekintése a Microsoft Azure.](../azure-monitor/alerts/alerts-overview.md) Ha feladat-, tevékenység- vagy készletspecifikus információkat is fel akar foglalni a riasztások közé, tekintse meg az Eseményekre való válaszadás [a riasztásokkal Azure Monitor részt.](../azure-monitor/alerts/tutorial-response.md)

## <a name="batch-diagnostics"></a>Batch-diagnosztika

[A diagnosztikai naplók](../azure-monitor/essentials/platform-logs-overview.md) az egyes erőforrások működését leíró Azure-erőforrások által kibocsátott adatokat tartalmaznak. A Batchhez a következő naplókat gyűjtheti be:

- **ServiceLog:** [a Batch szolgáltatás](#service-log-events) által az egyes erőforrások, például készlet vagy tevékenység élettartama során kibocsátott események.
- **AllMetrics:** Metrikák a Batch-fiók szintjén.

Explicit módon engedélyeznie kell a diagnosztikai beállításokat minden figyelni kívánt Batch-fiókhoz.

### <a name="log-destination-options"></a>Naplócél beállításai

Gyakori forgatókönyv egy Azure Storage-fiók kiválasztása naplózási célhelyként. A naplók Azure Storage-ban való tárolásához a naplók gyűjtésének engedélyezése előtt hozza létre a fiókot. Ha társított egy tárfiókot a Batch-fiókkal, kiválaszthatja azt a fiókot naplócélként.

A következőt is használhatja:

- A Batch diagnosztikai naplóeseményeket streamel egy [Azure-eseményközpontba.](../event-hubs/event-hubs-about.md) Event Hubs másodpercenként több millió eseményt képes behozni, amelyeket aztán bármilyen valós idejű elemzési szolgáltató használatával átalakíthat és tárolhat.
- Diagnosztikai naplókat küldhet [Azure Monitor naplókba,](../azure-monitor/logs/log-query-overview.md)ahol elemezheti őket, vagy exportálhatja őket elemzés céljából Power BI Excelben.

> [!NOTE]
> A diagnosztikai naplóadatok Azure-szolgáltatásokkal való tárolása vagy feldolgozása további költségekkel is jár.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Batch diagnosztikai naplók gyűjtésének engedélyezése

Új diagnosztikai beállítás létrehozásához a Azure Portal kövesse az alábbi lépéseket.

1. A Azure Portal válassza a **Minden** szolgáltatás Batch-fiók lehetőséget, majd válassza ki a  >  Batch-fiók nevét.
2. A **Monitorozás** területen kattintson a **Diagnosztikai beállítások** elemre.
3. A **Diagnosztikai beállítások lapon** válassza a Diagnosztikai beállítás hozzáadása **lehetőséget.**
4. Adja meg a beállítás nevét.
5. Cél kiválasztása: Küldés a **Log Analyticsbe,** **Archiválás tárfiókba,** vagy **Stream küldése egy eseményközpontba.** Ha tárfiókot választ, kiválaszthatja, hogy hány napig őrizze meg az egyes naplók adatait. Ha nem adja meg a megőrzési napok számát, az adatok a tárfiók élettartamában maradnak meg.
6. Válassza **a ServiceLog,** **az AllMetrics**, vagy mindkettő lehetőséget.
7. A **diagnosztikai beállítás** létrehozásához válassza a Mentés lehetőséget.

A naplógyűjtést úgy is engedélyezheti, hogy diagnosztikai beállításokat hoz létre [a](../azure-monitor/essentials/resource-manager-diagnostic-settings.md) [Azure Portal-ban](../azure-monitor/essentials/diagnostic-settings.md)egy Resource Manager-sablonnal, vagy a Azure PowerShell vagy az Azure CLI használatával. További információ: [Overview of Azure platform logs (Az Azure platform naplóinak áttekintése).](../azure-monitor/essentials/platform-logs-overview.md)

### <a name="access-diagnostics-logs-in-storage"></a>Diagnosztikai naplók elérése a tárolóban

Ha a [Batch diagnosztikai naplóit egy](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)tárfiókban archiválja, a kapcsolódó esemény bekövetkezésekor a rendszer létrehoz egy tárolót a tárfiókban. A blobok a következő elnevezési minta szerint vannak létrehozva:

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

Minden `PT1H.json` blobfájl JSON-formátumú eseményeket tartalmaz, amelyek a blob URL-címében megadott órán belül történtek (például `h=12` ). A jelenlegi órában a rendszer hozzáfűzi az eseményeket a `PT1H.json` fájlhoz azok előfordulásakor. A perc érték ( ) mindig , mivel a diagnosztikai naplóesemények óránként külön `m=00` `00` blobra vannak bontva. (Minden időpont UTC-ben van megadva.)

Az alábbiakban egy példa látható `PoolResizeCompleteEvent` egy bejegyzésre egy `PT1H.json` naplófájlban. Információkat tartalmaz a dedikált és alacsony prioritású csomópontok aktuális és célszámára, valamint a művelet kezdési és záró időpontára:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

A tárfiók naplói programozott módon való eléréséhez használja a Storage API-kat.

### <a name="service-log-events"></a>Szolgáltatásnapló eseményei

Azure Batch szolgáltatásnaplók tartalmazzák a Batch szolgáltatás által az egyes Batch-erőforrások élettartama során kibocsátott eseményeket, például készleteket vagy tevékenységeket. A Batch által kibocsátott események naplózása JSON formátumban történik. Ez például egy mintakészlet-létrehozási **esemény törzse:**

```json
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

A Batch szolgáltatás által kibocsátott szolgáltatásnapló-események a következők:

- [Készlet létrehozása](batch-pool-create-event.md)
- [Készlet törlésének kezdete](batch-pool-delete-start-event.md)
- [A készlet törlése befejeződött](batch-pool-delete-complete-event.md)
- [Készlet átméretezésének kezdete](batch-pool-resize-start-event.md)
- [Készlet átméretezése befejeződött](batch-pool-resize-complete-event.md)
- [Készlet automatikus skálázása](batch-pool-autoscale-event.md)
- [Feladat kezdete](batch-task-start-event.md)
- [Feladat befejezve](batch-task-complete-event.md)
- [A feladat meghiúsul](batch-task-fail-event.md)
- [A feladat ütemezése sikertelen](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Következő lépések

- Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
- További információ a [Batch-megoldások monitorozásról.](monitoring-overview.md)
