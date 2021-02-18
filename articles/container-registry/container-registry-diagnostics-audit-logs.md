---
title: Erőforrás-naplók & gyűjtése
description: Az Azure Container Registry, például a hitelesítés, a képküldés és a képek lekéréséhez szükséges erőforrás-naplózási események rögzítése és elemzése.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 952f239e94df6b3b21317985f56d3d7a999813fe
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572004"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Azure Container Registry naplók a diagnosztika kiértékeléséhez és a naplózáshoz

Ez a cikk azt ismerteti, hogyan gyűjthetők be az Azure Container Registry naplózási adatai a [Azure monitor](../azure-monitor/overview.md)szolgáltatásainak használatával. A Azure Monitor [erőforrás-naplókat](../azure-monitor/essentials/platform-logs-overview.md) (korábbi néven *diagnosztikai naplókat*) gyűjt a beállításjegyzékben lévő felhasználó által vezérelt eseményekhez. Gyűjtse össze és használja fel ezeket az adatokat az igények kielégítéséhez, például:

* A beállításjegyzék hitelesítési eseményeinek naplózása a biztonság és a megfelelőség biztosítása érdekében 

* Adjon meg egy teljes tevékenység-nyomvonalat a beállításjegyzék összetevőiről, például a lekéréses és lekéréses eseményekről, hogy diagnosztizálja a beállításjegyzék működési problémáit 

Az erőforrás-naplózási adatok Azure Monitor használatával történő összegyűjtése további költségeket eredményezhet. Lásd: [Azure monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Adattár eseményei

A rendszerképekhez és egyéb összetevőkhöz tartozó adattár-szintű események naplózása jelenleg a következő:

* **Leküldés**
* **Lekéréses**
* **Jelölését**
* **Törlés** (beleértve az adattár-törlési eseményeket)
* **Címke kiürítése** és a **jegyzékfájl kiürítése**

> [!NOTE]
> A kiürítési események naplózása csak akkor történik meg, ha egy beállításjegyzék- [adatmegőrzési házirend](container-registry-retention-policy.md) konfigurálva van.

## <a name="registry-resource-logs"></a>Beállításjegyzék-erőforrások naplói

Az erőforrás-naplók olyan Azure-erőforrások által kibocsátott információkat tartalmaznak, amelyek leírják a belső műveleteket. Az Azure Container Registry esetében a naplók a következő táblázatokban tárolt hitelesítési és adattár-szintű eseményeket tartalmazzák. 

* **ContainerRegistryLoginEvents**  – a beállításjegyzék hitelesítési eseményei és állapota, beleértve a bejövő identitást és IP-címet is
* **ContainerRegistryRepositoryEvents** – például leküldéses és lekéréses műveletek a beállításjegyzékbeli adattárakban található képekhez és egyéb összetevőkhöz
* **AzureMetrics**  -  [Tároló beállításjegyzék-metrikák](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerregistryregistries) , például összesített leküldéses és lekéréses darabszám.

A műveletek esetében a naplózási adatok a következők:
  * Sikeres vagy sikertelen állapot
  * Kezdő és záró időbélyegző

Az Azure az erőforrás-naplók mellett egy [tevékenységi naplót](../azure-monitor/essentials/platform-logs-overview.md)is biztosít, amely az Azure felügyeleti eseményeinek egyetlen előfizetési szintű rekordja, például egy tároló-beállításjegyzék létrehozása vagy törlése.

## <a name="enable-collection-of-resource-logs"></a>Erőforrás-naplók gyűjtésének engedélyezése

A tároló-beállításjegyzék erőforrás-naplófájljainak gyűjteménye alapértelmezés szerint nincs engedélyezve. Explicit módon engedélyezze a diagnosztikai beállításokat minden figyelni kívánt beállításjegyzékhez. A diagnosztikai beállítások engedélyezésének lehetőségeiért lásd: [diagnosztikai beállítás létrehozása a platform-naplók és-metrikák gyűjtéséhez az Azure-ban](../azure-monitor/essentials/diagnostic-settings.md).

Ha például egy tároló-beállításjegyzék naplóit és metrikáit szeretné megtekinteni a Azure Monitor közel valós időben, Gyűjtse össze az erőforrás-naplókat egy Log Analytics munkaterületen. A diagnosztikai beállítás engedélyezése a Azure Portal használatával:

1. Ha még nem rendelkezik munkaterülettel, hozzon létre egy munkaterületet a [Azure Portal](../azure-monitor/logs/quick-create-workspace.md)használatával. Az adatgyűjtés késleltetésének csökkentése érdekében ügyeljen arra, hogy a munkaterület ugyanabban a **régióban** legyen, mint a tároló-beállításjegyzék.
1. A portálon válassza ki a beállításjegyzéket, és válassza a **figyelés > diagnosztikai beállítások > diagnosztikai beállítás hozzáadása** elemet.
1. Adja meg a beállítás nevét, majd kattintson a **küldés log Analytics** lehetőségre.
1. Válassza ki a beállításjegyzék diagnosztikai naplóihoz tartozó munkaterületet.
1. Válassza ki a gyűjteni kívánt naplózási adatokat, majd kattintson a **Mentés** gombra.

Az alábbi ábrán egy beállításjegyzék diagnosztikai beállításainak létrehozása látható a portál használatával.

![Diagnosztikai beállítások engedélyezése](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Gyűjtsön csak a szükséges adatokat, kiegyensúlyozza a költségeket és a figyelési igényeket. Ha például csak a hitelesítési események naplózása szükséges, csak a **ContainerRegistryLoginEvents** -naplót válassza ki. 

## <a name="view-data-in-azure-monitor"></a>Azure Monitorban lévő adatmegjelenítés

Miután engedélyezte a diagnosztikai naplók gyűjtését Log Analyticsban, eltarthat néhány percig, amíg az adat megjelenik a Azure Monitorban. Ha meg szeretné tekinteni az adatait a portálon, válassza ki a beállításjegyzéket, és válassza a **figyelés > naplók** lehetőséget. Válassza ki az egyik táblázatot, amely a beállításjegyzék adatait tartalmazza. 

Lekérdezések futtatása az adatmegjelenítéshez. Több minta lekérdezés van megadva, vagy saját maga futtathatja. A következő lekérdezés például lekéri a legutóbbi 24 órás adatmennyiséget a **ContainerRegistryRepositoryEvents** táblából:

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

Az alábbi képen a minta kimenet látható:

![Naplóadatok lekérdezése](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

A Azure Portal Log Analytics használatáról szóló oktatóanyagért lásd: [Bevezetés a Azure Monitor log Analytics használatába](../azure-monitor/logs/log-analytics-tutorial.md), vagy próbálja ki a log Analytics [bemutató környezetét](https://portal.loganalytics.io/demo). 

A naplók lekérdezésével kapcsolatos további információkért lásd: [a Azure monitorban található naplók áttekintése](../azure-monitor/logs/log-query-overview.md).

## <a name="query-examples"></a>Lekérdezéspéldák

### <a name="error-events-from-the-last-hour"></a>Hiba eseményei az elmúlt órában

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>100 a legfrissebb beállításjegyzék-események

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>A tárházat törölt felhasználó vagy objektum identitása

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>A címkét törölt felhasználó vagy objektum identitása

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>Tárház-szintű műveleti hibák

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>Beállításjegyzék-hitelesítési hibák

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="additional-log-destinations"></a>További naplófájl-célhelyek

Amellett, hogy a naplókat Log Analyticsre küldi, vagy egy másik megoldásként egy Azure Storage-fiókot kell kiválasztania a napló célhelyként. Az Azure Storage-beli naplók archiválásához hozzon létre egy Storage-fiókot, mielőtt engedélyezi az archiválást a diagnosztikai beállításokon.

A diagnosztikai napló eseményeit egy [Azure Event hub](../event-hubs/event-hubs-about.md)-ba is továbbíthatja. A Event Hubs másodpercenként több millió eseményt képes befogadni, amelyet később bármilyen valós idejű elemzési szolgáltató használatával átalakíthat és tárolhat. 

## <a name="next-steps"></a>Következő lépések

* További információ a [log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) használatáról és a [naplók](../azure-monitor/logs/get-started-queries.md)létrehozásáról.
* A különböző Azure-rétegekben elérhető platform-naplók megismeréséhez tekintse meg [Az Azure platform-naplók áttekintése](../azure-monitor/essentials/platform-logs-overview.md) című témakört.