---
title: Naplózás beállítása a logikai alkalmazások figyeléséhez Azure Security Center
description: A diagnosztikai naplózás beállításával figyelheti Azure Security Center Logic Apps erőforrásainak állapotát.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 42b33a5b96de7334f8310b040052c633342f5e05
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712385"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>Naplózás beállítása a logikai alkalmazások figyeléséhez Azure Security Center

Ha [Microsoft Azure Security Centerban](../security-center/security-center-introduction.md)figyeli a Logic apps erőforrásokat, [megtekintheti, hogy a logikai alkalmazások az alapértelmezett házirendeket követik-e](#view-logic-apps-health-status). Az Azure egy Logic Apps erőforrás állapotának állapotát jeleníti meg, miután engedélyezte a naplózást, és helyesen állította be a napló célhelyét. Ez a cikk bemutatja, hogyan konfigurálhatja a diagnosztikai naplózást, és győződjön meg arról, hogy az összes logikai alkalmazás kifogástalan erőforrásokkal rendelkezik.

> [!TIP]
> Az Logic Apps szolgáltatás aktuális állapotának megkereséséhez tekintse át az [Azure Status (állapot) lapot](https://status.azure.com/), amely felsorolja a különböző termékek és szolgáltatások állapotát az egyes elérhető régiókban.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nincs előfizetése, [hozzon létre egy ingyenes Azure-fiókot a](https://azure.microsoft.com/free/) Kezdés előtt.
* Meglévő logikai alkalmazások, amelyeken [engedélyezve van a diagnosztikai naplózás](#enable-diagnostic-logging).
* Egy Log Analytics munkaterület, amely a logikai alkalmazás naplózásának engedélyezéséhez szükséges. Ha nincs munkaterülete, először [hozza létre a munkaterületet](../azure-monitor/logs/quick-create-workspace.md).

## <a name="enable-diagnostic-logging"></a>Diagnosztikai naplózás engedélyezése

A logikai alkalmazások erőforrás-állapotának megtekintéséhez először [be kell állítania a diagnosztikai naplózást](monitor-logic-apps-log-analytics.md). Ha már rendelkezik Log Analytics munkaterülettel, a logikai alkalmazás létrehozásakor vagy a meglévő logikai alkalmazásokban is engedélyezheti a naplózást.

> [!TIP]
> Az alapértelmezett javaslat a diagnosztikai naplók engedélyezése Logic Apps számára. Ezt a beállítást azonban a logikai alkalmazásokhoz is szabályozhatja. Ha engedélyezi a diagnosztikai naplókat a logikai alkalmazásokhoz, az információ segítségével elemezheti a biztonsági incidenseket.

### <a name="check-diagnostic-logging-setting"></a>Diagnosztikai naplózási beállítás keresése

Ha nem biztos abban, hogy a logikai alkalmazásaiban engedélyezve van-e a diagnosztikai naplózás, akkor a Security Centerban is bejelentkezhet:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A keresősáv mezőben adja meg és válassza ki a **Security Center**.
1. Az Security Center irányítópult menü **általános** területén válassza a **javaslatok** elemet.
1. A biztonsági javaslatok táblázatában keresse meg és jelölje be a **naplózási és naplózási** &gt; **diagnosztikai naplók engedélyezése a Logic Appsban jelölőnégyzetet** a biztonsági vezérlők táblázatban.
1. Az ajánlás lapon bontsa ki a **szervizelési lépések** szakaszt, és tekintse át a beállításokat. A **gyors javítás** lehetőség kiválasztásával engedélyezheti Logic apps diagnosztikát. gombra, vagy a manuális szervizeléssel kapcsolatos utasításokat követve.

## <a name="view-logic-apps-health-status"></a>A Logic apps állapotának megtekintése

A [diagnosztikai naplózás engedélyezése](#enable-diagnostic-logging)után Security Centerban láthatja a logikai alkalmazások állapotát.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A keresősáv mezőben adja meg és válassza ki a **Security Center**.
1. Az Security Center irányítópult menüjének **általános** területén válassza a **leltár** elemet.
1. A leltár lapon szűrheti az eszközök listáját, hogy csak Logic Apps erőforrások jelenjenek meg. Az oldal menüben válassza ki az **erőforrástípusok** &gt; **logikai alkalmazások** elemet.

   A nem megfelelő **állapotú erőforrások** számláló a nem kifogástalan állapotú logikai alkalmazások számát jeleníti meg Security Center.
1.  A Logic apps-erőforrások listájában tekintse át a **javaslatok** oszlopot. Egy adott logikai alkalmazás állapotának áttekintéséhez válassza ki az erőforrás nevét, vagy válassza az ellipszisek gombot (**...**) &gt; **Erőforrás megtekintése**.
1.  A lehetséges erőforrás-állapottal kapcsolatos problémák megoldásához kövesse a Logic Apps-alkalmazásokban felsorolt lépéseket.

Ha a diagnosztikai naplózás már engedélyezve van, előfordulhat, hogy probléma van a naplók céljával. Tekintse át [a különböző diagnosztikai naplózási célokkal kapcsolatos problémák megoldását ismertető témakört](#fix-diagnostic-logging-for-logic-apps).

## <a name="fix-diagnostic-logging-for-logic-apps"></a>A Logic apps diagnosztikai naplózásának javítása

Ha a [logikai alkalmazások nem megfelelőként vannak felsorolva a Security Centerban](#view-logic-apps-health-status), nyissa meg a logikai alkalmazást a Azure Portalban vagy az Azure CLI-n keresztül. Ezután keresse meg a diagnosztikai naplók célhelyének konfigurációját: [azure log Analytics](#log-analytics-and-event-hubs-destinations), [Azure Event Hubs](#log-analytics-and-event-hubs-destinations)vagy [egy Azure Storage-fiók](#storage-account-destination).

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics és Event Hubs célhelyek

Ha log Analytics vagy Event Hubs használja a Logic apps diagnosztikai naplók célhelye, ellenőrizze a következő beállításokat. 

1. Annak ellenőrzéséhez, hogy a diagnosztikai naplók engedélyezve vannak-e, ellenőrizze, hogy a diagnosztikai beállítások `logs.enabled` mező értéke `true` . 
1. Annak megerősítéséhez, hogy a Storage-fiók nem a célhelyként van beállítva, ellenőrizze, hogy a `storageAccountId` mező értéke `false` .

Például:

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>Storage-fiók célhelye

Ha a Logic Apps diagnosztikai naplóihoz a Storage-fiókot használja, ellenőrizze a következő beállításokat.

1. Annak ellenőrzéséhez, hogy engedélyezve vannak-e a diagnosztikai naplók, ellenőrizze, hogy a diagnosztikai beállítások `logs.enabled` mező értéke `true` .
1. Annak ellenőrzéséhez, hogy engedélyezve van-e a diagnosztikai naplók adatmegőrzési szabályzata, győződjön meg arról, hogy a `retentionPolicy.enabled` mező értéke `true` .
1. A 0-365 napos megőrzési idő beállításának megerősítéséhez ellenőrizze, hogy a mező értéke a `retentionPolicy.days` 0 és a 365 közötti érték.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```