---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 5f2b77c7d8e1a2da9517183043231b717b6cceab
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734083"
---
### <a name="general-query-limits"></a>Általános lekérdezési korlátok

| Korlát | Leírás |
|:---|:---|
| Lekérdezés nyelve | Azure Monitor ugyanazt a [Kusto-lekérdezési nyelvet](/azure/kusto/query/) használja, mint az Azure adatkezelő. Lásd: [Azure monitor a naplózási lekérdezés nyelvi eltérései](/azure/data-explorer/kusto/query/) a KQL nyelvi elemeihez Azure monitor nem támogatottak. |
| Azure-régiók | A naplók lekérdezése túlzott terhelést jelenthet, ha az adatLog Analytics több Azure-régióban lévő munkaterületek is átnyúlnak. Részletekért lásd a [lekérdezési korlátokat](../articles/azure-monitor/logs/scope.md#query-scope-limits) . |
| Erőforrásközi lekérdezések | Application Insights erőforrások és Log Analytics munkaterületek maximális száma egyetlen lekérdezésben 100-ra korlátozva.<br>Az erőforrások közötti lekérdezés nem támogatott a Tervező nézetében.<br>Az új scheduledQueryRules API támogatja a naplózási riasztásokban lévő erőforrás-lekérdezések közötti lekérdezést.<br>További részletekért lásd: [erőforrások közötti lekérdezési korlátok](../articles/azure-monitor/logs/cross-workspace-query.md#cross-resource-query-limits) . |

### <a name="user-query-throttling"></a>Felhasználói lekérdezés szabályozása
Azure Monitor több szabályozási korláttal rendelkezik a túlzott számú lekérdezést küldő felhasználók elleni védelemhez. Az ilyen viselkedés potenciálisan túlterhelheti a rendszerháttér-erőforrásokat, és veszélyeztetheti a szolgáltatás érzékenységét. A következő korlátok úgy vannak kialakítva, hogy az ügyfelek számára biztosítható legyen a megszakítások és a konzisztens szolgáltatási szint. A felhasználói szabályozás és a korlátok kizárólag a szélsőséges használati forgatókönyvek hatására vannak kialakítva, és nem feltétlenül relevánsak a szokásos használathoz.


| Measure | Felhasználónként korlátozva | Leírás |
|:---|:---|:---|
| Egyidejű lekérdezések | 5 | Ha a felhasználó számára már 5 lekérdezés fut, minden új lekérdezés egy felhasználónkénti egyidejűségi sorba kerül. Ha az egyik futó lekérdezés véget ér, a rendszer a következő lekérdezést fogja lekérni a várólistából, és elindítja azt. Ez nem tartalmazza a riasztási szabályok lekérdezéseit.
| A párhuzamossági várólistán lévő idő | 3 perc | Ha egy lekérdezés több mint 3 percen belül a várólistán található, az elindítása nélkül, akkor a 429-as kóddal rendelkező HTTP-hiba miatt leáll. |
| Összes lekérdezés a egyidejűségi várólistában | 200 | Ha a várólistán lévő lekérdezések száma eléri a 200-et, a további lekérdezések elutasítása a 429-es HTTP-hibakód alapján történik. Ez a szám a egyszerre futtatható 5 lekérdezésen kívül esik. |
| Lekérdezések sebessége | 200-lekérdezések száma 30 másodpercenként | Ez az általános mérték, amellyel a lekérdezéseket egyetlen felhasználó elküldheti az összes munkaterülethez.  Ez a korlát a vizualizációs részek, például az Azure-irányítópultok és a Log Analytics munkaterület összefoglalás lapja által kezdeményezett programozott lekérdezésekre és lekérdezésekre vonatkozik. |

- Optimalizálja a lekérdezéseket a [Azure monitorban található naplók optimalizálása](../articles/azure-monitor/logs/query-optimization.md)című témakörben leírtak szerint.
- Az irányítópultok és a munkafüzetek egyetlen nézetben több lekérdezést is tartalmazhatnak, amelyek minden betöltéskor vagy frissítéskor feltört lekérdezéseket hoznak. Érdemes lehet több, igény szerinti terheléssel rendelkező nézetet feltörni. 
- Power BI a nyers naplók helyett csak összesített eredményeket kell kinyerni.