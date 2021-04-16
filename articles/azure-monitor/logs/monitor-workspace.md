---
title: Log Analytics-munkaterület állapotának figyelése a Azure Monitor
description: Ez a cikk azt ismerteti, hogyan figyelheti a Log Analytics-munkaterület állapotát az Operation tábla adataival.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 6f1a23170d84e39e5d531ae4e3a64b59d29bd677
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538849"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Log Analytics-munkaterület állapotának figyelése a Azure Monitor
A Log Analytics-munkaterület teljesítményének és rendelkezésre állásának fenntartásához a Azure Monitor képesnek kell lennie proaktívan észlelni a felmerülő problémákat. Ez a cikk bemutatja, hogyan figyelheti a Log Analytics-munkaterület állapotát az [Operation tábla adataival.](/azure/azure-monitor/reference/tables/operation) Ez a táblázat minden Log Analytics-munkaterületen megtalálható, és a munkaterületen előforduló hibákat és figyelmeztetéseket tartalmaz. Rendszeresen tekintse át az adatokat, és hozzon létre riasztásokat, hogy proaktívan értesítést kap, ha fontos incidensek vannak a munkaterületen.

## <a name="_logoperation-function"></a>_LogOperation függvény

Azure Monitor naplók részleteket küld az [](/azure/azure-monitor/reference/tables/operation) esetleges problémákról a munkaterület műveleti táblájának, ahol a probléma felmerült. A **_LogOperation** rendszerfunkciója az **Operation táblán** alapul, és egyszerűsített elemzési és riasztási információkat biztosít.

## <a name="columns"></a>Oszlopok

A **_LogOperation** függvény az alábbi táblázat oszlopait adja vissza.

| Oszlop | Leírás |
|:---|:---|
| TimeGenerated | Az incidens beesésének ideje UTC időzónában. |
| Kategória  | Műveletkategória-csoport. A művelettípusok szűrésére használható, és pontosabb rendszer-naplózást és riasztásokat hozhat létre. A kategóriák listáját az alábbi szakaszban láthatja. |
| Művelet  | A művelet típusának leírása. Ez a Log Analytics egyik korlátját, a művelet típusát vagy egy folyamat egy részét jelezheti. |
| Level | A probléma súlyossági szintje:<br>- Információ: Nincs szükség külön figyelmet.<br>- Figyelmeztetés: A folyamat nem a várt módon fejeződött be, és figyelmet igényel.<br>- Hiba: A folyamat meghiúsult, és sürgősen figyelmet igényel. 
| Részletek | A művelet részletes leírása konkrét hibaüzenetet tartalmaz, ha létezik. |
| _ResourceId | A művelethez kapcsolódó Azure-erőforrás erőforrás-azonosítója.  |
| Computer | Számítógépnév, ha a művelet egy Azure Monitor kapcsolódik. |
| CorrelationId | Egymást követő kapcsolódó műveletek csoportosítása. |


## <a name="categories"></a>Kategóriák

Az alábbi táblázat a függvény kategóriáit _LogOperation ismerteti. 

| Kategória | Leírás |
|:---|:---|
| Betöltés           | Az adatbevégrehajtásához szükséges műveletek. További részletekért lásd alább. |
| Ügynök               | Az ügynök telepítésével kapcsolatban problémát jelez. |
| Adatgyűjtés     | Adatgyűjtési folyamatokkal kapcsolatos műveletek. |
| Megoldáscélzás  | A *ConfigurationScope típusú* művelet fel lett feldolgozva. |
| Felmérési megoldás | A rendszer értékelési folyamatot hajtott végre. |


### <a name="ingestion"></a>Betöltés
Az adatbeküldési műveletek olyan problémák, amelyek az adatbeküldés során történtek, beleértve az Azure Log Analytics-munkaterület korlátainak eléréséről szóló értesítést is. Ebben a kategóriában a hibafeltételek adatvesztésre utalnak, ezért különösen fontos a figyelése. Az alábbi táblázat ezeknek a műveleteknek a részleteit tartalmazza. Lásd [Azure Monitor Log Analytics-munkaterületek](../service-limits.md#log-analytics-workspaces) szolgáltatási korlátaira vonatkozó szolgáltatási korlátokat.


| Művelet | Level | Részletek | Kapcsolódó cikk |
|:---|:---|:---|:---|
| Egyéni napló | Hiba   | Elérte az egyéni mezők oszlopkorlátját. | [Az Azure Monitor szolgáltatási korlátai](../service-limits.md#log-analytics-workspaces) |
| Egyéni napló | Hiba   | Az egyéni naplók nem sikerültek. | |
| Metaadat. | Hiba | Konfigurációs hiba észlelhető. | |
| Adatgyűjtés | Hiba   | Az adatok el lett dobva, mert a kérés a beállított napok számnál korábban jött létre. | [A használat és a költségek felügyelete Azure Monitor-naplókkal](./manage-cost-storage.md#alert-when-daily-cap-reached)
| Adatgyűjtés | Információ    | A rendszer a gyűjtemény gépkonfigurációját észlelte.| |
| Adatgyűjtés | Információ    | Az adatgyűjtés új nap miatt indult el. | [A használat és a költségek felügyelete Azure Monitor-naplókkal](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Adatgyűjtés | Figyelmeztetés | Az adatgyűjtés a napi korlát elérése miatt leállt.| [A használat és a költségek felügyelete Azure Monitor-naplókkal](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Adatfeldolgozás | Hiba   | Érvénytelen JSON formátum. | [Naplóadatok küldése a Azure Monitor HTTP Data Collector API-val (nyilvános előzetes verzió)](../logs/data-collector-api.md#request-body) | 
| Adatfeldolgozás | Figyelmeztetés | Az érték a maximálisan megengedett méretre lett levágva. | [Az Azure Monitor szolgáltatási korlátai](../service-limits.md#log-analytics-workspaces) |
| Adatfeldolgozás | Figyelmeztetés | A mező értéke a méretkorlát elérésekor van levágva. | [Az Azure Monitor szolgáltatási korlátai](../service-limits.md#log-analytics-workspaces) | 
| Ingestion rate | Információ | A bebelési sebességkorlát megközelíti a 70%-ot. | [Az Azure Monitor szolgáltatási korlátai](../service-limits.md#log-analytics-workspaces) |
| Ingestion rate | Figyelmeztetés | A korláthoz közeledő belési sebességkorlát. | [Az Azure Monitor szolgáltatási korlátai](../service-limits.md#log-analytics-workspaces) |
| Ingestion rate | Hiba   | Elérte a sebességkorlátot. | [Az Azure Monitor szolgáltatási korlátai](../service-limits.md#log-analytics-workspaces) |
| Tárolás | Hiba   | Nem lehet hozzáférni a tárfiókhoz, mert a használt hitelesítő adatok érvénytelenek.  |



   

## <a name="alert-rules"></a>Riasztási szabályok
A [naplólekérdezés-riasztások](../alerts/alerts-log-query.md) Azure Monitor proaktívan értesítheti, ha problémát észlel a Log Analytics-munkaterületen. Olyan stratégiát érdemes használnia, amely lehetővé teszi, hogy időben reagáljon a problémákra, és közben minimalizálja a költségeket. Az előfizetése minden riasztási szabályért díjat számít fel, és a kiértékelésének gyakoriságától függően költségeket számítunk fel.

Az ajánlott stratégia két riasztási sszabályokkal kezdődik a probléma szintje alapján. Használjon rövid gyakoriságot, például 5 percenként az Errors (Hibák) és a hosszabb (például 24 óra) gyakoriságot a Figyelmeztetések esetén. Mivel a hibák potenciális adatvesztésre utalnak, gyorsan kell reagálnia rájuk, hogy minimalizálja az adatvesztést. A figyelmeztetések általában olyan problémát jeleznek, amely nem igényel azonnali beavatkozást, így napi rendszerességgel áttekintheti őket.

A naplóriasztási szabályok létrehozásához használja a naplóriasztásokat Azure Monitor és kezelje a naplóriasztásokat. [](../alerts/alerts-log.md) A következő szakaszok az egyes szabályok részleteit ismertetik.


| Lekérdezés | Küszöbérték | Időszak | Gyakoriság |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Ezek a riasztási szabályok minden hibával vagy figyelmeztetéssel kapcsolatos műveletre ugyanúgy reagálnak. Ahogy egyre jobban megismeri a riasztásokat generáló műveleteket, előfordulhat, hogy eltérő választ szeretne kapni az egyes műveletekre. Előfordulhat például, hogy különböző személyeknek szeretne értesítéseket küldeni adott műveletekről. 

Ha riasztási szabályt hoz létre egy adott művelethez, használjon olyan lekérdezést, amely tartalmazza a **Kategória** és a **Művelet oszlopot.** 

Az alábbi példa figyelmeztető riasztást hoz létre, ha a betöltés mennyisége elérte a korlát 80%-át.

- Cél: Válassza ki a Log Analytics-munkaterületet
- Kritériumok:
  - Jel neve: Egyéni naplókeresés
  - Keresési lekérdezés: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - Alapján: Eredmények száma
  - Feltétel: Nagyobb, mint
  - Küszöbérték: 0
  - Időszak: 5 (perc)
  - Gyakoriság: 5 (perc)
- Riasztási szabály neve: Elérte a napi adatkorlátot
- Súlyosság: Figyelmeztetés (1. súlyosság)


Az alábbi példa figyelmeztető riasztást hoz létre, ha az adatgyűjtés elérte a napi korlátot. 

- Cél: Válassza ki a Log Analytics-munkaterületet
- Kritériumok:
  - Jel neve: Egyéni naplókeresés
  - Keresési lekérdezés: `_LogOperation | where Category == "Ingestion" | where Operation == "Data collection Status" | where Level == "Warning"`
  - Alapján: Eredmények száma
  - Feltétel: Nagyobb, mint
  - Küszöbérték: 0
  - Időszak: 5 (perc)
  - Gyakoriság: 5 (perc)
- Riasztási szabály neve: Elérte a napi adatkorlátot
- Súlyosság: Figyelmeztetés (1. súlyosság)



## <a name="next-steps"></a>Következő lépések

- További információ a [naplóriasztásról.](../alerts/alerts-log.md)
- [Gyűjtse össze a munkaterület lekérdezési](./query-audit.md) naplózási adatait.
