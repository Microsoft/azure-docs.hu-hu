---
title: Log Analytics munkaterület állapotának figyelése Azure Monitor
description: Ismerteti, hogyan lehet figyelni a Log Analytics munkaterület állapotát a műveleti tábla adataival.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: bef7db19142f0387077af1487230e7891c255aa1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617525"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Log Analytics munkaterület állapotának figyelése Azure Monitor
A Log Analytics munkaterület teljesítményének és rendelkezésre állásának Azure Monitor-ban való fenntartásához képesnek kell lennie proaktív módon észlelni a felmerülő problémákat. Ez a cikk azt ismerteti, hogyan figyelheti a Log Analytics munkaterület állapotát a [műveleti](/azure/azure-monitor/reference/tables/operation) tábla adatai alapján. Ez a táblázat minden Log Analytics-munkaterület része, és a munkaterületen előforduló hibákat és figyelmeztetéseket tartalmazza. Rendszeresen tekintse át ezeket az adatait, és hozzon létre riasztásokat, amelyekkel proaktívan értesítheti, ha vannak olyan fontos incidensek a munkaterületen.

## <a name="_logoperation-function"></a>_LogOperation függvény

Azure Monitor naplók a probléma előfordulásakor a munkaterületen lévő [művelet](/azure/azure-monitor/reference/tables/operation) táblájában lévő hibák részleteit küldi el. A **_LogOperation** System függvény a **műveleti** táblázaton alapul, és egyszerűsített információt nyújt az elemzéshez és a riasztásokhoz.

## <a name="columns"></a>Oszlopok

A **_LogOperation** függvény az alábbi táblázatban szereplő oszlopokat adja vissza.

| Oszlop | Leírás |
|:---|:---|
| TimeGenerated | Az incidens UTC-ben történt ideje. |
| Kategória  | Műveleti kategória csoport. Felhasználható a műveletek típusainak szűrésére és a rendszernaplózás és a riasztások pontosabb létrehozására. A kategóriák listáját az alábbi szakaszban találja. |
| Művelet  | A művelet típusának leírása. Ez az egyik Log Analytics korlátot, a művelet típusát vagy egy folyamat egy részét jelezheti. |
| Level | A probléma súlyossági szintje:<br>-Info: nincs szükség külön beavatkozásra.<br>-Figyelmeztetés: a folyamat nem a várt módon fejeződött be, és figyelmet igényel.<br>-Hiba: a folyamat sikertelen volt, és sürgős figyelmet igényel. 
| Részletek | A művelet részletes leírása tartalmaz bizonyos hibaüzenetet, ha az létezik. |
| _ResourceId | A művelethez kapcsolódó Azure-erőforrás erőforrás-azonosítója.  |
| Computer | A számítógép neve, ha a művelet egy Azure Monitor ügynökhöz kapcsolódik. |
| CorrelationId | Egymást követő kapcsolódó műveletek csoportosítására szolgál. |


## <a name="categories"></a>Kategóriák

Az alábbi táblázat a _LogOperation függvény kategóriáit ismerteti. 

| Kategória | Leírás |
|:---|:---|
| Betöltés           | Az adatfeldolgozási folyamat részét képező műveletek. További részletekért lásd alább. |
| Ügynök               | Az ügynök telepítésével kapcsolatos hibát jelez. |
| Adatgyűjtés     | Az adatgyűjtési folyamatokkal kapcsolatos műveletek. |
| Megoldás célcsoportja  | A *ConfigurationScope* típusú művelet feldolgozása megtörtént. |
| Értékelési megoldás | Értékelési folyamat lett végrehajtva. |


### <a name="ingestion"></a>Betöltés
A betöltési műveletek olyan problémák, amelyek az adatok betöltése során merültek fel, beleértve az Azure Log Analytics-munkaterület korlátainak elérésére vonatkozó értesítést is. A kategóriába tartozó hibák adatvesztést okozhatnak, ezért különösen fontos a figyelés. Az alábbi táblázat részletesen ismerteti ezeket a műveleteket. Tekintse meg a Log Analytics-munkaterületek szolgáltatási korlátainak [Azure monitor szolgáltatási](../service-limits.md#log-analytics-workspaces) korlátozásait.


| Művelet | Level | Részletek | Kapcsolódó cikk |
|:---|:---|:---|:---|
| Egyéni napló | Hiba   | Az egyéni mezőkhöz tartozó oszlopok száma elérte a korlátot. | [Azure Monitor szolgáltatási korlátok](../service-limits.md#log-analytics-workspaces) |
| Egyéni napló | Hiba   | Az egyéni naplók betöltése sikertelen volt. | |
| Metaadatok. | Hiba | Konfigurációs hiba észlelhető. | |
| Adatgyűjtés | Hiba   | Az rendszer eldobta az adatmennyiséget, mert a kérést a beállított napok száma előtt hozták létre. | [A használat és a költségek felügyelete Azure Monitor-naplókkal](../platform/manage-cost-storage.md#alert-when-daily-cap-reached)
| Adatgyűjtés | Információ    | A rendszer a gyűjtemény számítógépének konfigurációját észlelte.| |
| Adatgyűjtés | Információ    | Az adatgyűjtés új nap miatt megkezdődött. | [A használat és a költségek felügyelete Azure Monitor-naplókkal](../platform/manage-cost-storage.md#alert-when-daily-cap-reached) |
| Adatgyűjtés | Figyelmeztetés | Az adatgyűjtés a napi korlát miatt leállt.| [A használat és a költségek felügyelete Azure Monitor-naplókkal](../platform/manage-cost-storage.md#alert-when-daily-cap-reached) |
| Adatfeldolgozás | Hiba   | Érvénytelen JSON-formátum. | [Naplóbejegyzések küldése a Azure Monitornak a HTTP-adatgyűjtő API-val (nyilvános előzetes verzió)](../logs/data-collector-api.md#request-body) | 
| Adatfeldolgozás | Figyelmeztetés | Az érték a megengedett maximális méretre van kimetszve. | [Azure Monitor szolgáltatási korlátok](../service-limits.md#log-analytics-workspaces) |
| Adatfeldolgozás | Figyelmeztetés | A mező értéke elérte a méretkorlátot. | [Azure Monitor szolgáltatási korlátok](../service-limits.md#log-analytics-workspaces) | 
| Betöltési arány | Információ | A betöltési arány a 70%-ra közeledik. | [Azure Monitor szolgáltatási korlátok](../service-limits.md#log-analytics-workspaces) |
| Betöltési arány | Figyelmeztetés | A betöltési arány elérte a korlátot. | [Azure Monitor szolgáltatási korlátok](../service-limits.md#log-analytics-workspaces) |
| Betöltési arány | Hiba   | Elérte a díjszabási korlátot. | [Azure Monitor szolgáltatási korlátok](../service-limits.md#log-analytics-workspaces) |
| Tárolás | Hiba   | A Storage-fiók nem érhető el, mert a használt hitelesítő adatok érvénytelenek.  |



   

## <a name="alert-rules"></a>Riasztási szabályok
A [naplózási lekérdezési riasztások](../alerts/alerts-log-query.md) használata Azure monitor, hogy proaktívan értesítsék, ha problémát észlel a rendszer a log Analytics munkaterületen. Olyan stratégiát kell használnia, amely lehetővé teszi, hogy a költségek minimalizálása mellett időben válaszoljon a problémákra. Az előfizetést minden egyes riasztási szabályért a kiértékelt gyakoriságtól függően kell fizetni.

Az ajánlott stratégia a probléma szintjén alapuló két riasztási szabály elindítása. Használjon rövid gyakorisággal (például 5 percenként) a hibákhoz, és egy hosszú gyakoriságot, például 24 órát a figyelmeztetésekhez. Mivel a hibák jelzik a lehetséges adatvesztést, gyorsan reagálni szeretne rájuk a veszteség csökkentése érdekében. A figyelmeztetések általában egy azonnali beavatkozást nem igénylő problémát jeleznek, így naponta áttekintheti őket.

A naplózási riasztási szabályok létrehozásához használja a következő eljárást a [naplók létrehozása, megtekintése és kezelése Azure monitor használatával](../alerts/alerts-log.md) . A következő szakaszok ismertetik az egyes szabályok részleteit.


| Lekérdezés | Küszöbérték | Időszak | Gyakoriság |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Ezek a riasztási szabályok ugyanúgy reagálnak a hibákra vagy figyelmeztetésekre vonatkozó összes műveletre. Ahogy egyre jobban megismerik a riasztásokat generáló műveleteket, érdemes lehet az adott műveletekben különbözőképpen válaszolni. Előfordulhat például, hogy az értesítéseket különböző személyeknek szeretné elküldeni adott műveletekhez. 

Egy adott művelethez tartozó riasztási szabály létrehozásához használjon olyan lekérdezést, amely tartalmazza a **Kategória** és a **művelet** oszlopokat. 

A következő példa figyelmeztető riasztást hoz létre, ha a betöltési mennyiség elérte a korlát 80%-át.

- Cél: válassza ki a Log Analytics munkaterületet
- Kritériumok
  - Jel neve: egyéni naplók keresése
  - Keresési lekérdezés: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - A következő alapján: az eredmények száma
  - Feltétel: nagyobb, mint
  - Küszöbérték: 0
  - Időszak: 5 (perc)
  - Gyakoriság: 5 (perc)
- Riasztási szabály neve: elérte a napi adatkorlátot
- Súlyosság: figyelmeztetés (1. pont)


Az alábbi példa figyelmeztető riasztást hoz létre, amikor az adatgyűjtés elérte a napi korlátot. 

- Cél: válassza ki a Log Analytics munkaterületet
- Kritériumok
  - Jel neve: egyéni naplók keresése
  - Keresési lekérdezés: `_LogOperation | where Category == "Ingestion" | where Operation == "Data Collection" | where Level == "Warning"`
  - A következő alapján: az eredmények száma
  - Feltétel: nagyobb, mint
  - Küszöbérték: 0
  - Időszak: 5 (perc)
  - Gyakoriság: 5 (perc)
- Riasztási szabály neve: elérte a napi adatkorlátot
- Súlyosság: figyelmeztetés (1. pont)



## <a name="next-steps"></a>Következő lépések

- További információ a [naplózási riasztásokról](../alerts/alerts-log.md).
- A munkaterület [lekérdezési naplózási adatainak összegyűjtése](../log-query/query-audit.md) .