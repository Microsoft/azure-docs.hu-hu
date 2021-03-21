---
title: Frissítés az aktuális Azure Monitor log riasztások API-ra
description: Útmutató a log-riasztások ScheduledQueryRules API-hoz való váltáshoz
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b91560f09b30cc5d3089e5beca4d670f94f33402
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031310"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>Frissítés az aktuális log riasztások API-ra örökölt Log Analytics riasztási API-ból

> [!NOTE]
> Ez a cikk csak az Azure Public szolgáltatásra vonatkozik (**nem** Azure Government vagy az Azure China Cloud-ba).

> [!NOTE]
> Ha a felhasználó úgy dönt, hogy a jelenlegi [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules) -ra vált, nem lehet visszatérni a régebbi [örökölt log Analytics riasztási API](./api-alerts.md)-ra.

A múltban a felhasználók a [korábbi log Analytics riasztási API](./api-alerts.md) -t használták a naplózási riasztási szabályok kezeléséhez. Az aktuális munkaterületek a [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)-t használják. Ez a cikk a régi API-ról az aktuális API-ra való áttérés előnyeit és folyamatát ismerteti.

## <a name="benefits"></a>Előnyök

- Egyetlen sablon a riasztási szabályok létrehozásához (korábban három különálló sablon szükséges).
- Önálló API Log Analytics munkaterületekhez vagy Application Insights erőforrásokhoz.
- A [PowerShell-parancsmagok támogatják a használatát](./alerts-log.md#managing-log-alerts-using-powershell).
- A megszakítások összehangolása az összes többi riasztási típussal.
- Több olyan külső erőforrásra (például Log Analytics munkaterületekre vagy Application Insights erőforrásokra) [kiterjedő több munkaterület-naplózási riasztás](../logs/cross-workspace-query.md) létrehozása.
- A felhasználók megadhatnak dimenziókat a riasztások felosztásához az "aggregált on" paraméter használatával.
- A naplózási riasztások kiterjesztett időtartama legfeljebb két napos adat lehet (korábban csak egy nap).

## <a name="impact"></a>Hatás

- Minden új szabályt létre kell hozni/szerkeszteni kell az aktuális API-val. Tekintse meg a [minta használata az Azure Resource template](alerts-log-create-templates.md) használatával és a [minta használata a PowerShell](./alerts-log.md#managing-log-alerts-using-powershell)segítségével című témakört.
- Mivel a szabályok Azure Resource Manager nyomon követett erőforrásokat az aktuális API-ban, és egyedinek kell lenniük, a szabályok erőforrás-azonosítója a következő struktúrára fog váltani: `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . A riasztási szabály megjelenítendő nevei változatlanok maradnak.

## <a name="process"></a>Folyamat

A váltás folyamata nem interaktív, és nem igényel manuális lépéseket a legtöbb esetben. A riasztási szabályok nem állnak le vagy nem szűnnek meg a kapcsoló alatt vagy után.
Ehhez a híváshoz az adott Log Analytics munkaterülethez társított összes riasztási szabályt át kell váltania:

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Az alábbi JSON-t tartalmazó kérelem törzse:

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Íme egy példa arra, hogyan használhatja a [ARMClient](https://github.com/projectkudu/ARMClient)-t, amely egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti a fenti API-hívások meghívását:

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Ha a kapcsoló sikeres, a válasz a következőket:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>Munkaterület váltási állapotának megtekintése

Ezt az API-hívást a kapcsoló állapotának vizsgálatára is használhatja:

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Használhatja a [ARMClient](https://github.com/projectkudu/ARMClient) eszközt is:

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Ha a Log Analytics munkaterület át lett állítva a [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)-ra, a válasz a következő:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Ha a Log Analytics munkaterület nincs bekapcsolva, a válasz a következőket:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitor-log riasztásokról](./alerts-unified-log.md).
- Ismerje meg, hogyan [kezelheti a naplózási riasztásokat az API használatával](alerts-log-create-templates.md).
- Megtudhatja, hogyan [kezelheti a naplózási riasztásokat a PowerShell használatával](./alerts-log.md#managing-log-alerts-using-powershell).
- További információ az [Azure-riasztások felhasználói élményéről](./alerts-overview.md).