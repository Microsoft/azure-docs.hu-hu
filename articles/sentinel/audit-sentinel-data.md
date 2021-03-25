---
title: Azure Sentinel-lekérdezések és-tevékenységek naplózása | Microsoft Docs
description: Ez a cikk az Azure Sentinelben végrehajtott lekérdezések és tevékenységek naplózását ismerteti.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: a02be0938b1ab925fb0343351ce1c414cc59c615
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044838"
---
# <a name="audit-azure-sentinel-queries-and-activities"></a>Azure Sentinel-lekérdezések és-tevékenységek naplózása

Ez a cikk azt ismerteti, hogyan tekintheti meg a lekérdezések futtatásának és az Azure Sentinel-munkaterületen végrehajtott tevékenységeknek a naplózási adatait, például a belső és külső megfelelőségi követelményeket a biztonsági műveletek (SOC) munkaterületen.

Az Azure Sentinel hozzáférést biztosít a következőhöz:

- A **AzureActivity** tábla, amely az Azure sentinelben végrehajtott összes művelet részleteit tartalmazza, például a riasztási szabályok szerkesztését. A **AzureActivity** tábla nem naplóz bizonyos lekérdezési adatfájlokat. További információ: [Az Azure-beli tevékenység naplóinak naplózása](#auditing-with-azure-activity-logs).

- A **LAQueryLogs** tábla, amely a log Analytics futó lekérdezések részleteit tartalmazza, beleértve az Azure sentinelből futtatott lekérdezéseket is. További információ: [naplózás a LAQueryLogs](#auditing-with-laquerylogs).

> [!TIP]
> A cikkben ismertetett manuális lekérdezések mellett az Azure Sentinel egy beépített munkafüzettel is rendelkezik, amely segítséget nyújt a SOC-környezet tevékenységeinek naplózásához.
>
> Az Azure Sentinel- **munkafüzetek** területen keressen rá a munkaterület- **naplózási** munkafüzetre.



## <a name="auditing-with-azure-activity-logs"></a>Naplózás az Azure Activity naplóival

Az Azure Sentinel naplóit az Azure-beli [tevékenységek naplóiban](../azure-monitor/essentials/platform-logs-overview.md)tartják karban, ahol a **AzureActivity** tábla tartalmazza az Azure Sentinel-munkaterületen végrehajtott összes műveletet.

A **AzureActivity** táblát használhatja a SoC-környezetben az Azure Sentinel használatával végzett naplózási tevékenységhez.

**A AzureActivity tábla lekérdezése**:

1. Az [Azure-tevékenység](connect-azure-activity.md) adatforrásának összekapcsolásával elindíthatja az adatfolyam-naplózási eseményeket egy új táblába a **naplók** képernyőn, a AzureActivity nevű lapon.

1. Ezt követően az KQL használatával kérdezheti le az adatlekérdezéseket, mint bármely más táblát.

    A **AzureActivity** tábla számos szolgáltatásból származó, például az Azure sentinelből származó adatokkal rendelkezik. Ha csak az Azure Sentinel adatait szeretné szűrni, indítsa el a lekérdezést a következő kóddal:

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    Ha például meg szeretné tudni, hogy ki volt az utolsó felhasználó egy adott elemzési szabály szerkesztéséhez, használja a következő lekérdezést (a helyére a `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` szabályt, amelyet meg szeretne keresni):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

További paraméterek hozzáadása a lekérdezéshez a **AzureActivities** tábla további megismeréséhez, attól függően, hogy mit kell jelentenie. A következő szakaszokban további példákat talál a **AzureActivity** -táblákkal végzett naplózáshoz. 

További információ: [Azure Sentinel-adatok az Azure-tevékenység naplóiban](#azure-sentinel-data-included-in-azure-activity-logs).

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>Egy adott felhasználó által az elmúlt 24 órában végrehajtott összes művelet megkeresése

A következő **AzureActivity** -lekérdezés felsorolja az adott Azure ad-felhasználó által az elmúlt 24 órában végrehajtott összes műveletet.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>Az összes törlési művelet megkeresése

A következő **AzureActivity** felsorolja az Azure Sentinel-munkaterületen végrehajtott összes törlési műveletet.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
``` 


### <a name="azure-sentinel-data-included-in-azure-activity-logs"></a>Azure Sentinel-az Azure-tevékenység naplóiban található
 
Az Azure Sentinel naplózási naplóit az [Azure-tevékenységek naplójában](../azure-monitor/essentials/platform-logs-overview.md)tartja karban, és a következő típusú információkat tartalmazza:

|Művelet  |Információs típusok  |
|---------|---------|
|**Létrehozva**     |Riasztási szabályok <br> Eseti megjegyzések <br>Incidensek megjegyzései <br>Mentett keresések<br>Watchlists    <br>Munkafüzetek     |
|**Törölve**     |Riasztási szabályok <br>Könyvjelzők <br>Adatösszekötők <br>Incidensek <br>Mentett keresések <br>Beállítások <br>Fenyegetési intelligencia jelentései <br>Watchlists      <br>Munkafüzetek <br>Munkafolyamat  |
|**Frissített**     |  Riasztási szabályok<br>Könyvjelzők <br> Esetekben <br> Adatösszekötők <br>Incidensek <br>Incidensek megjegyzései <br>Fenyegetési intelligencia jelentései <br> Munkafüzetek <br>Munkafolyamat       |
|     |         |

Az Azure-tevékenység naplóit is használhatja a felhasználói engedélyek és licencek kereséséhez. 

Az alábbi táblázat például felsorolja az Azure-tevékenység naplófájljaiban található kiválasztott műveleteket az adott erőforrással, amelyből a naplózási adatok bekerülnek.

|Művelet neve|    Erőforrás típusa|
|----|----|
|Munkafüzet létrehozása vagy frissítése  |Microsoft. bepillantások/munkafüzetek|
|Munkafüzet törlése    |Microsoft. bepillantások/munkafüzetek|
|Munkafolyamat beállítása   |Microsoft. Logic/munkafolyamatok|
|Munkafolyamat törlése    |Microsoft. Logic/munkafolyamatok|
|Mentett keresés létrehozása    |Microsoft. OperationalInsights/munkaterületek/savedSearches|
|Mentett keresés törlése    |Microsoft. OperationalInsights/munkaterületek/savedSearches|
|Riasztási szabályok frissítése |Microsoft. SecurityInsights/alertRules|
|Riasztási szabályok törlése |Microsoft. SecurityInsights/alertRules|
|Riasztási szabály válaszára vonatkozó műveletek frissítése |Microsoft. SecurityInsights/alertRules/műveletek|
|Riasztási szabály válaszára vonatkozó műveletek törlése |Microsoft. SecurityInsights/alertRules/műveletek|
|Könyvjelzők frissítése   |Microsoft. SecurityInsights/könyvjelzők|
|Könyvjelzők törlése   |Microsoft. SecurityInsights/könyvjelzők|
|Esetek frissítése   |Microsoft. SecurityInsights/esetek|
|Frissítési eset vizsgálata  |Microsoft. SecurityInsights/esetek/vizsgálatok|
|Eseti megjegyzések létrehozása   |Microsoft. SecurityInsights/esetek/megjegyzések|
|Adatösszekötők frissítése |Microsoft. SecurityInsights/dataConnectors|
|Adatösszekötők törlése |Microsoft. SecurityInsights/dataConnectors|
|Beállítások frissítése    |Microsoft. SecurityInsights/beállítások|
| | |

További információ: [Azure Activity log esemény sémája](../azure-monitor/essentials/activity-log-schema.md).


## <a name="auditing-with-laquerylogs"></a>Naplózás a LAQueryLogs

A **LAQueryLogs** tábla a log Analytics futtatott napló lekérdezésekkel kapcsolatos részleteket tartalmazza. Mivel Log Analytics Azure Sentinel alapul szolgáló adattárként van használatban, beállíthatja, hogy a rendszer LAQueryLogs adatokat gyűjtsön az Azure Sentinel-munkaterületen.

A LAQueryLogs-adatok olyan információkat tartalmaznak, mint például a következők:

- Lekérdezések futtatásakor
- A Log Analytics lekérdezések futtatása
- Milyen eszközt használtak a lekérdezések futtatásához Log Analytics, például az Azure Sentinel
- Maguk a lekérdezési szövegek
- Az egyes lekérdezéseken futó teljesítményadatok

> [!NOTE]
> - A **LAQueryLogs** tábla csak az Azure Sentinel naplók paneljén futtatott lekérdezéseket tartalmazza. Nem tartalmazza az ütemezett elemzési szabályok által futtatott lekérdezéseket a **vizsgálati gráf** vagy az Azure Sentinel **Hunting** oldalon.
> - Előfordulhat, hogy a lekérdezés futtatása és az adatok feltöltése a **LAQueryLogs** táblában rövid ideig tart. Javasoljuk, hogy körülbelül 5 percet várjon a **LAQueryLogs** tábla lekérdezésére a naplózási információkhoz.
>


**A LAQueryLogs tábla lekérdezése**:

1. A **LAQueryLogs** tábla alapértelmezés szerint nincs engedélyezve a log Analytics munkaterületen. Ha **LAQueryLogs** -adatait szeretné használni az Azure Sentinel szolgáltatásban való vizsgálat során, először engedélyezze a **LAQueryLogs** a log Analytics munkaterület **diagnosztikai beállítások** területén.

    További információ: [Azure monitor-naplókban található lekérdezések naplózása](../azure-monitor/logs/query-audit.md).


1. Ezt követően az KQL használatával kérdezheti le az adatlekérdezéseket, mint bármely más táblát.

    Például a következő lekérdezés azt mutatja, hogy hány lekérdezés futott a múlt héten, napi szinten:

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

A következő fejezetekben a **LAQueryLogs** -táblán futtatandó több minta lekérdezés látható, amikor az Azure Sentinel használatával végez naplózási tevékenységeket az SoC-környezetben.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>Azoknak a lekérdezéseknek a száma, amelyeken a válasz nem volt "OK".

A következő **LAQueryLogs** a lekérdezések futtatásának számát jeleníti meg, ahol az **200** -es http-válaszoktól függetlenül minden adat érkezett. Ez a szám például olyan lekérdezéseket tartalmaz, amelyeket nem sikerült futtatni.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>Felhasználók megjelenítése CPU-igényes lekérdezésekhez

A következő **LAQueryLogs** -lekérdezés felsorolja azokat a felhasználókat, akik a legtöbb CPU-igényű lekérdezést futtatták, a felhasznált CPU és a lekérdezési idő hosszán alapulva.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>Azon felhasználók megjelenítése, akik a legtöbb lekérdezést futtatták az elmúlt héten

A következő **LAQueryLogs** -lekérdezés felsorolja azokat a felhasználókat, akik a legtöbb lekérdezést futtatták az elmúlt héten.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-azure-sentinel-activities"></a>Riasztások konfigurálása az Azure Sentinel-tevékenységekhez

Proaktív riasztások létrehozásához érdemes az Azure Sentinel naplózási erőforrásait használni.

Ha például bizalmas táblákat használ az Azure Sentinel munkaterületen, a következő lekérdezéssel értesítheti a táblák lekérdezésének minden egyes időpontját:

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```


## <a name="next-steps"></a>Következő lépések

Az Azure Sentinelben használja a **munkaterület-naplózási** munkafüzetet a SoC-környezet tevékenységeinek naplózásához.

További információ: [oktatóanyag: adatok megjelenítése és figyelése](tutorial-monitor-your-data.md).