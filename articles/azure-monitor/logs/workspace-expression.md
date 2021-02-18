---
title: munkaterület () kifejezés Azure Monitor napló lekérdezésében | Microsoft Docs
description: A munkaterület kifejezés egy Azure Monitor napló lekérdezésében szerepel, hogy az adott erőforráscsoport, egy másik erőforráscsoport vagy egy másik előfizetés adott munkaterületéről olvassa be az adatok adatait.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 9ce9957d348fc9d57ff688408d6aaade67e95465
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619455"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>munkaterület () kifejezés Azure Monitor napló lekérdezésében

A `workspace` kifejezés egy Azure monitor lekérdezésben szerepel, hogy az adott munkaterületről, egy másik erőforráscsoporthoz vagy egy másik előfizetésből származó adatokból kérdezze le azokat. Ez hasznos lehet a naplózási adatApplication Insightsi lekérdezésekben, valamint az adatlekérdezés több munkaterületen is a napló lekérdezésében.


## <a name="syntax"></a>Syntax

`workspace(`*Azonosító*`)`

## <a name="arguments"></a>Argumentumok

- *Azonosító*: a munkaterületet az alábbi táblázatban szereplő formátumok egyikének használatával azonosítja.

| Azonosító | Leírás | Példa
|:---|:---|:---|
| Erőforrás neve | A munkaterület emberi olvasási neve (más néven "összetevő neve") | munkaterület ("contosoretail") |
| Minősített név | A munkaterület teljes neve a következő formában: "subscriptionName/resourceGroup/componentName" | munkaterület ("contoso/ContosoResource/ContosoWorkspace") |
| ID (Azonosító) | A munkaterület GUID azonosítója | munkaterület ("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Azure-erőforrás azonosítója | Az Azure-erőforrás azonosítója | munkaterület ("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Jegyzetek

* Olvasási hozzáféréssel kell rendelkeznie a munkaterülethez.
* A kapcsolódó kifejezések `app` lehetővé teszik Application Insights alkalmazások lekérdezését.

## <a name="examples"></a>Példák

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az [alkalmazás kifejezését](../log-query/app-expression.md) Application Insights alkalmazásra való hivatkozáshoz.
- További információ a [Azure monitor adatainak](../log-query/log-query-overview.md) tárolásáról.
- A [Kusto lekérdezési nyelv](/azure/kusto/query/)teljes dokumentációjának elérése.
