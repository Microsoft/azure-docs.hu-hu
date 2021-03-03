---
title: A Azure Monitor naplózási értesítéseinek hibakeresése | Microsoft Docs
description: Gyakori hibák, hibák és megoldások az Azure-beli naplózási riasztási szabályokhoz.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 09/22/2020
ms.openlocfilehash: 9352b27002162e08d53bc8166ceddd010be3c8d1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738650"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>A Azure Monitor naplózási értesítéseinek hibakeresése  

Ebből a cikkből megtudhatja, Hogyan oldhatók meg a naplózási riasztásokkal kapcsolatos gyakori problémák Azure Monitor. Emellett megoldásokat kínál a naplók működésével és konfigurációjával kapcsolatos gyakori problémákra.

A riasztások naplózása lehetővé teszi, hogy a felhasználók egy [log Analytics](../logs/log-analytics-tutorial.md) lekérdezést használjanak, amellyel kiértékelheti az erőforrások összes készletének gyakoriságát, és egy riasztást az eredmények alapján. A szabályok egy vagy több műveletet aktiválnak a [műveleti csoportok](./action-groups.md)használatával. [További információ a naplózási riasztások funkcióival és terminológiával kapcsolatban](alerts-unified-log.md).

> [!NOTE]
> Ez a cikk nem vizsgálja azokat az eseteket, amelyekben a Azure Portal riasztási szabályt váltott ki, és egy kapcsolódó műveleti csoport nem hajt végre értesítést. Ilyen esetekben tekintse meg a hibaelhárítás részleteit [itt](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected).

## <a name="log-alert-didnt-fire"></a>A napló riasztása nem tűz

### <a name="data-ingestion-time-for-logs"></a>Naplók adatfeldolgozási ideje

Azure Monitor az ügyfelek által a világ különböző pontjain futó naplók terabájtját dolgozza fel, ami a naplók betöltési [késését](../logs/data-ingestion-time.md)okozhatja.

A naplók félig strukturált adatok, és eredendően látensek, mint a metrikák. Ha több mint 4 perces késleltetést tapasztal a kilőtt riasztásokban, érdemes [metrikus riasztásokat](alerts-metric-overview.md)használni. Adatokat küldhet a metrikai tárolóba a naplók [metrikai riasztásai](alerts-metric-logs.md)használatával.

A rendszer többször próbálkozik a riasztások kiértékelésével a késés enyhítése érdekében. Amint az adatok megérkeznek, a riasztás tüzek, ami a legtöbb esetben nem egyenlő a napló rekordjának időpontjával.

### <a name="incorrect-query-time-range-configured"></a>Helytelen lekérdezési időtartomány konfigurálva

A lekérdezés időtartománya a szabály feltételének definíciójában van beállítva. Ez a mező a munkaterületek és a Application Insights **időszaka** , valamint a **lekérdezés időtartományának felülbírálása** minden más erőforrástípus esetében. A log analyticshez hasonlóan az időtartomány is a megadott időszakra korlátozza a lekérdezési időt. Akkor is alkalmazza az időtartományt **, ha a** lekérdezésben már szerepel a futtatási parancs. 

Egy lekérdezés például 60 percet vizsgál, ha az időtartomány 60 perc, még akkor is, ha a szöveg már tartalmazza a **következőt: ago (1d)**. Az időtartománynak és a lekérdezési idő szűrésének meg kell egyeznie. A példában az **adott időszakra** vonatkozó  /  **felülbírálás lekérdezési időtartománya** egy napra változik, a várt módon fog működni.

![Időtartam](media/alerts-troubleshoot-log/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>A riasztási szabályban a műveletek el vannak némítva

A naplózási riasztások lehetőséget biztosítanak egy adott időtartamra kilőtt riasztási műveletek elnémítására. Ez a mező a **riasztások** kihagyása a munkaterületeken és a Application Insights. Az összes többi erőforrástípus esetében az **Elnémítás műveletnek** nevezzük. 

Egy gyakori probléma, hogy úgy gondolja, hogy a riasztás a szolgáltatási probléma miatt nem indította el a műveleteket. Még a szabály konfigurációja is elnémítva.

![Riasztások mellőzése](media/alerts-troubleshoot-log/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>Metrikai mérési riasztási szabály az örökölt Log Analytics API-val történő felosztással

A [metrikai mérték](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) a naplózási riasztás típusa, amely az összegzett idősorozatok eredményein alapul. Ezek a szabályok lehetővé teszik az oszlopok csoportosítását a [riasztások felosztásához](alerts-unified-log.md#split-by-alert-dimensions). Ha az örökölt Log Analytics API-t használja, a felosztás nem a várt módon fog működni. Az örökölt API-k csoportosításának kiválasztása nem támogatott.

A jelenlegi ScheduledQueryRules API lehetővé teszi az **Összesítés** beállítását a [metrikus mérési](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) szabályokban, amelyek a várt módon fognak működni. [További információ az aktuális SCHEDULEDQUERYRULES API-ra való áttérésről](../alerts/alerts-log-api-switch.md).

## <a name="log-alert-fired-unnecessarily"></a>A naplózási riasztás szükségtelen

Előfordulhat, hogy a [Azure monitor konfigurált naplózási riasztási szabály](./alerts-log.md) váratlanul aktiválódik. Az alábbi szakaszok néhány gyakori okot ismertetnek.

### <a name="alert-triggered-by-partial-data"></a>A riasztások részlegesen aktiválva

Azure Monitor az ügyfelek által a világ különböző pontjain futó naplók terabájtját dolgozza fel, ami a naplók betöltési [késését](../logs/data-ingestion-time.md)okozhatja.

A naplók félig strukturált adatok, és eredendően látensek, mint a metrikák. Ha sok problémát tapasztal a kilőtt riasztásokban, érdemes lehet [metrikus riasztásokat](alerts-metric-overview.md)használni. Adatokat küldhet a metrikai tárolóba a naplók [metrikai riasztásai](alerts-metric-logs.md)használatával.

A naplózási riasztások akkor működnek a legjobban, ha a naplókban lévő adatészlelési kísérletet végez. Kevésbé jól működik, ha a naplókban lévő adathiány észlelését kísérli meg. Például a virtuális gép szívverésének riasztása. 

Habár a hamis riasztások megelőzésére beépített funkciók állnak rendelkezésre, akkor is előfordulhat, hogy a nagyon látens adatmennyiségen (~ 30 percen) és a késési tüskékkel rendelkező adaton is előfordulhatnak.

### <a name="query-optimization-issues"></a>Lekérdezés-optimalizálási problémák

A riasztási szolgáltatás megváltoztatja a lekérdezést, hogy optimalizálja az alacsonyabb terhelést és a Riasztás késését. A riasztási folyamat úgy lett felépítve, hogy átalakítsa a riasztási hibát jelző eredményeket. Például a következőhöz hasonló lekérdezés esetén:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Ha a felhasználó szándéka, hogy riasztást küldjön, ha az esemény típusa bekövetkezik, a riasztási logika hozzáfűzi `count` a lekérdezést. A futtatandó lekérdezés a következőket eredményezi:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Nem kell riasztási logikát felvennie a lekérdezésbe, és ennek következtében problémákhoz is vezethet. A fenti példában, ha belefoglalja a `count` lekérdezésbe, az mindig az 1 értéket fogja eredményezni, mivel a riasztási szolgáltatás végrehajtja a szolgáltatást `count` `count` .

Az optimalizált lekérdezés a napló riasztási szolgáltatásának futtatása. A módosított lekérdezést Log Analytics [portálon](../logs/log-query-overview.md) vagy [API](/rest/api/loganalytics/)-ban is futtathatja.

A munkaterületek és a Application Insights esetében a rendszer **lekérdezést hoz végre** a feltétel ablaktáblán. Az összes többi erőforrástípus területen válassza a **végleges riasztási lekérdezés** megjelenítése lehetőséget a feltétel lapon.

![Végrehajtandó lekérdezés](media/alerts-troubleshoot-log/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>A napló riasztása le lett tiltva

Az alábbi részekben felsorolunk néhány okot, amiért Azure Monitor letilthatja a napló riasztási szabályait. [A szabály letiltásakor egy példát is tartalmaz a tevékenység naplójának elküldésekor](#activity-log-example-when-rule-is-disabled).

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>A riasztás hatóköre már nem létezik vagy át lett helyezve

Ha egy riasztási szabály hatókör-erőforrásai már nem érvényesek, a szabály végrehajtása sikertelen lesz. Ebben az esetben a számlázás is leáll.

A Azure Monitor egy hét után letiltja a napló riasztását, ha a szolgáltatás folyamatosan leáll.

### <a name="query-used-in-a-log-alert-isnt-valid"></a>A naplózási riasztásban használt lekérdezés érvénytelen

A naplózási riasztási szabály létrehozásakor a rendszer ellenőrzi a lekérdezést a helyes szintaxishoz. Előfordulhat azonban, hogy a naplózási riasztási szabályban megadott lekérdezés nem tud elindulni. Néhány gyakori ok:

- A szabályok az API-n keresztül lettek létrehozva, és a felhasználó kihagyta az érvényesítést.
- A lekérdezés [több erőforráson fut](../logs/cross-workspace-query.md) , és egy vagy több erőforrást törölték vagy áthelyezték.
- A [lekérdezés meghiúsul](https://dev.loganalytics.io/documentation/Using-the-API/Errors) , mert:
    - A naplózási megoldás nem lett [központilag telepítve a munkaterületre](../insights/solutions.md#install-a-monitoring-solution), ezért a táblák nem jönnek létre.
    - Az adat több mint 30 napig a lekérdezésben lévő táblára áramlik.
    - Az [egyéni naplók táblái](../agents/data-sources-custom-logs.md) még nem lettek létrehozva, mert az adatfolyam nem indult el.
- A [lekérdezés nyelvének](/azure/kusto/query/) változásai a parancsok és függvények módosított formátumát tartalmazzák. Így a korábban megadott lekérdezés már nem érvényes.

[Azure Advisor](../../advisor/advisor-overview.md) figyelmeztet erre a viselkedésre. Javaslatot tesz az érintett naplózási riasztási szabályra. A használt kategória a "magas rendelkezésre állás" közepes hatású, és a "naplózási riasztási szabály kijavítása a figyeléshez" leírást.

## <a name="alert-rule-quota-was-reached"></a>Elérte a riasztási szabály kvótáját

A naplókeresési riasztási szabályok előfizetésenkénti és erőforrásonkénti száma az [itt](../service-limits.md) leírt kvótakorlátoktól függ.

### <a name="recommended-steps"></a>Javasolt lépések
    
Ha elérte a kvóta korlátját, a következő lépések segíthetnek a probléma megoldásában.

1. Próbálja meg törölni vagy letiltani a napló keresési riasztási szabályait, amelyeket a rendszer már nem használ.
1. A szabályok számának csökkentéséhez próbálja meg a [riasztások dimenziók szerinti felosztását](alerts-unified-log.md#split-by-alert-dimensions) használni. Ezek a szabályok számos erőforrás és észlelési eset figyelésére használhatók.
1. Ha növelni szeretné a kvóta korlátját, folytassa a támogatási kérelem megnyitásával, és adja meg a következő információkat:

    - Azon előfizetési azonosítók és erőforrás-azonosítók, amelyekhez a kvóta korlátját növelni kell.
    - A kvóta növelésének oka.
    - A kvóta növelésének erőforrás-típusa: **log Analytics**, **Application Insights** stb.
    - A kért kvóta korlátja.


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>Az új naplózási riasztási szabályok aktuális használatának ellenõrzése
    
#### <a name="from-the-azure-portal"></a>Az Azure Portalról

1. Nyissa meg a *riasztások* képernyőt, és válassza a *riasztási szabályok kezelése* lehetőséget.
2. Az *Előfizetés* legördülő vezérlővel szűrjön rá a szóban forgó előfizetésre
3. Ügyeljen arra, hogy ne szűrje egy adott erőforráscsoportot, erőforrástípust vagy erőforrást
4. A *jel típusa* legördülő vezérlőben válassza a „Naplókeresés” lehetőséget
5. Ellenőrizze, hogy az *Állapot* legördülő vezérlő Engedélyezve értére van-e állítva
6. A naplókeresési szabályok teljes száma a szabályok listája felett jelenik meg

#### <a name="from-api"></a>Küldő API

- PowerShell – [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- REST API – [List by subscription](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>Tevékenység naplójának példája, ha a szabály le van tiltva

Ha a lekérdezés hét napig meghiúsul, akkor Azure Monitor letiltja a napló riasztását, és leállítja a szabály számlázását. Ha Azure Monitor letiltotta a naplózási riasztást az [Azure-tevékenység naplójában](../../azure-resource-manager/management/view-activity-logs.md), megtalálhatja a pontos időpontot. Tekintse meg a következő példát:

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

## <a name="next-steps"></a>Következő lépések

- További információ a [log-riasztásokról az Azure-ban](./alerts-unified-log.md).
- További információ a [naplózási riasztások konfigurálásáról](../logs/log-query-overview.md).
- További információ a [naplók lekérdezéséről](../logs/log-query-overview.md).