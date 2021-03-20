---
title: Azure Functions figyelése Azure Monitor naplókkal
description: Megtudhatja, hogyan használhatja a Azure Monitor naplókat Azure Functions a függvények végrehajtásának figyelésére.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: b6ce9e77421df0563810fd7f8255720c1fd2d0c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591082"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Functions figyelése Azure Monitor naplókkal

A Azure Functions a függvények figyeléséhez [Azure monitor naplókkal](../azure-monitor/logs/data-platform-logs.md) való integrációt biztosít. Ez a cikk bemutatja, hogyan konfigurálhatja a Azure Functionst a rendszer által létrehozott és a felhasználó által létrehozott naplók Azure Monitor naplókba való küldéséhez.

Azure Monitor naplók lehetővé teszik a különböző erőforrásokból származó naplók összevonását ugyanabban a munkaterületen, ahol a [lekérdezésekkel](../azure-monitor/logs/log-query-overview.md) elemezhető az összegyűjtött adatok gyors lekérése, összevonása és elemzése céljából.  A Azure Portal [log Analytics](../azure-monitor/logs/log-query-overview.md) használatával létrehozhat és tesztelheti a lekérdezéseket, majd közvetlenül elemezheti ezeket az eszközöket, vagy a [vizualizációk](../azure-monitor/visualizations.md) vagy a [riasztási szabályok](../azure-monitor/alerts/alerts-overview.md)használatával mentheti a lekérdezéseket.

A Azure Monitor az Azure Adatkezelő által használt [Kusto-lekérdezési nyelv](/azure/kusto/query/) egy olyan verzióját használja, amely az egyszerű naplózási lekérdezésekhez megfelelő, de olyan speciális funkciókat is tartalmaz, mint az összesítések, az illesztések és az intelligens elemzések. A lekérdezés nyelvét [több leckével](../azure-monitor/logs/get-started-queries.md)is gyorsan megismerheti.

> [!NOTE]
> A Azure Monitor-naplókkal való integráció jelenleg nyilvános előzetes verzióban érhető el, és a Windows-használat, a prémium szintű és a dedikált üzemeltetési csomagok esetében a v2-es és v3-es függvények

## <a name="setting-up"></a>Beállítás

1. A [Azure Portal](https://portal.azure.com)a Function alkalmazás **figyelés** szakaszában válassza a **diagnosztikai beállítások** lehetőséget, majd kattintson a **diagnosztikai beállítás hozzáadása** elemre.

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="Diagnosztikai beállítások kiválasztása":::

1. A **diagnosztika beállításai** oldalon, a **Kategória részletei** és a **napló** területen válassza a **FunctionAppLogs** lehetőséget.

   A **FunctionAppLogs** tábla tartalmazza a kívánt naplókat.

1. A **célhely részletei** területen válassza a **Küldés log Analyticsba** lehetőséget, majd válassza ki a **log Analytics munkaterületet**. 

1. Adja meg a **diagnosztikai beállítások nevét**, majd kattintson a **Mentés** gombra.

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="Diagnosztikai beállítás hozzáadása":::

## <a name="user-generated-logs"></a>Felhasználó által generált naplók

Egyéni naplók létrehozásához használja az adott nyelvhez tartozó naplózási utasítást. Az alábbiakban a mintakód kódrészletek találhatók:


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Naplók lekérdezése

A generált naplók lekérdezése:
 
1. A Function alkalmazásban válassza a **diagnosztikai beállítások** lehetőséget. 

1. A **diagnosztikai beállítások** listából válassza ki azt a log Analytics munkaterületet, amelyet a függvény naplóinak küldéséhez konfigurált. 

1. A **log Analytics munkaterület** lapon válassza a **naplók** lehetőséget.

   Azure Functions az összes naplót a **FunctionAppLogs** táblába írja a **LogManagement** alatt. 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Lekérdezési ablak Log Analytics munkaterületen":::

Íme néhány példa a lekérdezésekre:

### <a name="all-logs"></a>Minden napló

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>Adott függvények naplói

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Kivételek

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [Azure functions áttekintését](functions-overview.md).
- További információ a [Azure monitor naplókról](../azure-monitor/logs/data-platform-logs.md).
- További információ a [lekérdezési nyelvről](../azure-monitor/logs/get-started-queries.md).
