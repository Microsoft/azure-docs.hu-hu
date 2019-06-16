---
title: Váltás az örökölt Log Analytics API riasztások az új Azure-riasztások API
description: Örökölt savedSearch áttekintése a Log Analytics-riasztás API és a folyamat riasztási szabályok váltani új ScheduledQueryRules API-t, az ügyfél gyakori problémákat címzés részletei alapján.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 0e8cb18b3ea4b01db6b373ebbcb55c1e17614319
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399148"
---
# <a name="switch-api-preference-for-log-alerts"></a>API-preferencia váltása naplóriasztásokhoz

> [!NOTE]
> Tartalom említettük a felhasználókra vonatkozó csak az Azure nyilvános felhő és a **nem** az Azure Government vagy Azure China felhőhöz.  

A riasztási szabályokat korábban a Microsoft Operations Management Suite portál használatával lehetett kezelni. A riasztások új kezelőfelülete lett integrálva a különböző szolgáltatások, így a Log Analytics a Microsoft Azure-ban, és azt kéri, hogy [a riasztási szabályok kiterjesztése az OMS-portálon az Azure-bA](alerts-extend.md). Azonban ahhoz, hogy az ügyfelek minimális megszakadása, a folyamat nem változtatott meg a programozható felületet a felhasználásra - [Log Analytics Alert API](api-alerts.md) SavedSearch alapján.

Azonban most jelenti be a Log Analytics-riasztások a felhasználók egy igaz Azure programozott alternatív [Azure Monitor - ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), amely egyben a reflektív a [Azure-számlázás – a riasztások](alerts-unified-log.md#pricing-and-billing-of-log-alerts). A naplóriasztások az API-val kezeléséről további információkért lásd: [kezelése riasztások Azure Resource-sablonnal](alerts-log.md#managing-log-alerts-using-azure-resource-template) és [kezelése PowerShell használatával riasztások](alerts-log.md#managing-log-alerts-using-powershell).

## <a name="benefits-of-switching-to-new-azure-api"></a>Új Azure API-ra váltás előnyei

Számos előnnyel jár, létrehozásának és kezelésének, használatával riasztások [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) keresztül [örökölt Log Analytics Alert API](api-alerts.md); azt listázott néhány az alábbiak fő egyikével:

- Lehetővé teszi [közötti munkaterület naplóbeli keresés](../log-query/cross-workspace-query.md) a riasztási szabályok és a külső erőforrások, például a Log Analytics-munkaterületek, vagy akár az Application Insights-alkalmazások az időtartományt
- Használata esetén több mező lekérdezésben használt csoporthoz, [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) felhasználó megadhatja, melyik mezőt összesítés az Azure Portalon
- Naplóriasztások használatával létrehozott [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) is időszak meghatározott legfeljebb 48 óra és az adatok újbóli lekérésére korábbinál hosszabb ideig
- Riasztási szabályok létrehozása egy képernyőkép-készítés nélkül hozhat létre erőforrásokat, a három szintje egyetlen erőforrásként [örökölt Log Analytics-riasztás API](api-alerts.md)
- A lekérdezés alapú naplóriasztások az Azure-ban – új összes változatának egyetlen programozható felületet [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) szabályok kezelése a Log Analytics, valamint az Application Insights segítségével
- A naplóriasztások használata kezelheti [Powershell-parancsmagok](alerts-log.md#managing-log-alerts-using-powershell)
- Minden új naplófájlt riasztási funkciók és a jövőbeli fejlesztés csak az új keresztül elérhető lesz [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Örökölt Log riasztások API való váltás folyamata

Felhasználók szabadon választhat [örökölt Log Analytics Alert API](api-alerts.md) vagy az új [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Riasztási szabályok vagy API-t, rendszer által létrehozott *kezelhetők, csak az azonos API által* – valamint az Azure Portalról. Alapértelmezés szerint az Azure Monitor továbbra is az [örökölt Log Analytics-riasztás API](api-alerts.md) bármely új riasztási szabály létrehozásához a meglévő munkaterületeknél a Log Analytics az Azure Portalról. Mint [bejelentett új vagy 2019. június 1. után létrehozott Log-munkaterület](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) -automatikusan használja, új [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) alapértelmezett együtt az Azure Portalon.

A kapcsoló scheduledQueryRules API fontossági következményeinek ügyfélbeállításai alatt:

- Minden interakciók esetén parancsvezérelt felületek segítségével riasztások kezelése most meg kell ismételnie a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) helyette. További információ megtekintéséhez [minta használata az Azure Resource-sablon használatával](alerts-log.md#managing-log-alerts-using-azure-resource-template) és [minta használata a PowerShell-lel](alerts-log.md#managing-log-alerts-using-powershell)
- Minden olyan új riasztási szabály létrehozása az Azure portal használatával jön [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) csak és a felhasználó használhat a [további funkciókat az új API](#benefits-of-switching-to-new-azure-api) , valamint az Azure Portalon
- Riasztási szabály súlyossága a árkonstrukcióra: *A kritikus, figyelmeztetés & tájékoztató*, az *fontossági értékek 0, 1 és 2*. Együtt létrehozni vagy frissíteni a beállítást riasztási szabályok az súlyossági 4 is.

Áttérés a figyelmeztetési szabályok folyamata [örökölt Log Analytics-riasztás API](api-alerts.md) módosítása a riasztások definícióit, a lekérdezés vagy a konfiguráció semmilyen módon nem jár. A riasztási szabályok és azok nem érinti, és a riasztások figyelése nem állítja le, vagy leállt, közben vagy után a kapcsolót. Az egyetlen változás az API-t és a egy új API-n keresztül a szabályok hozzáférést változás történik.

> [!NOTE]
> Miután egy felhasználó jóváhagyja váltson az új beállításokat szabályozó [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), a meg nem optimalizált vissza szülővé és visszaalakítás szülőből használatával, a korábbi [örökölt Log Analytics Alert API](api-alerts.md).

Bárki, aki önkéntesen váltson át az új [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) és blokkolja a használat a [örökölt Log Analytics-riasztás API](api-alerts.md);. Ehhez egy PUT-hívást hajt végre az alábbi API-t az összes riasztást válthat a megadott Log Analytics-munkaterülethez társított szabályok.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

A kérelem törzse, amely tartalmazza az alábbi JSON-ban.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Az API-t is elérhető lesz egy PowerShell parancssori használatával [ARMClient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti az Azure Resource Manager API meghívása. Alábbi képen szemléltetett módon a minta Put művelet meghívásával ARMclient eszközzel a kapcsoló összes riasztási szabályt társított adott Log Analytics-munkaterületen.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Ha minden riasztási szabályok az a Log Analytics-munkaterület használata az új kapcsoló [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) van sikeres, a következő választ fognak adni.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Felhasználók is ellenőrizheti az aktuális állapotát a Log Analytics-munkaterületre és tekintse meg, ha van, vagy nem lett átadva használata [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) csak. Ellenőrizze, hogy felhasználók tudják végrehajtani egy GET hívást az alábbi API-t.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Hajtsa végre a fenti PowerShell-parancssor használatával történő [ARMClient](https://github.com/projectkudu/ARMClient) eszközről, tekintse meg az alábbi mintát.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Ha a megadott Log Analytics-munkaterület használatára lett átadva [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) csak akkor a válasz JSON lesz az alább felsorolt.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Más, ha a megadott Log Analytics-munkaterület nem még lett átadva használandó [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) csak akkor a válasz JSON lesz az alább felsorolt.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>További lépések

- További információ a [az Azure Monitor - riasztások](alerts-unified-log.md).
- Ismerje meg, hogyan hozhat létre [naplóriasztások az Azure Alerts](alerts-log.md).
- Tudjon meg többet a [Azure Alerts élmény](../../azure-monitor/platform/alerts-overview.md).
