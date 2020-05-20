---
title: Szinapszis SQL-javaslatok
description: Tudnivalók a szinapszis SQL-javaslatairól és azok létrehozásáról
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 17b8ce04cb5029d1bea11344617bf65718ca579c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653023"
---
# <a name="synapse-sql-recommendations"></a>Szinapszis SQL-javaslatok

Ez a cikk a Azure Advisor által kiszolgált szinapszis SQL-ajánlásokat ismerteti.  

A szinapszis SQL javaslatokat tesz arra vonatkozóan, hogy az adattárház számítási feladatait következetesen optimalizálták a teljesítmény érdekében. A javaslatok szorosan integrálva vannak a [Azure Advisorekkel](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) , így közvetlenül a [Azure Portalon](https://aka.ms/Azureadvisor)belül biztosíthatja az ajánlott eljárásokat. A szinapszis SQL napi ütemben gyűjti az aktív számítási feladatra vonatkozó telemetria és felületek javaslatait. A támogatott javaslatok alább láthatók a javasolt műveletek alkalmazásával együtt.

[A javaslatait](https://aka.ms/Azureadvisor) még ma is megtekintheti! 

## <a name="data-skew"></a>Az adattorzítás

Az adatok eldöntése további adatáthelyezést vagy erőforrás-szűk keresztmetszetet eredményezhet a számítási feladatok futtatásakor. Az alábbi dokumentáció ismerteti, hogyan azonosíthatja az adatok eldöntését, és megakadályozhatja, hogy az optimális terjesztési kulcs kiválasztásával megtörténjen.

- [A döntés azonosítása és eltávolítása](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Nem vagy elavult statisztika

Az optimálisnál rosszabb statisztikai adatok súlyosan befolyásolhatják a lekérdezési teljesítményt, mert az SQL-lekérdezés-optimalizáló az optimálisnál rosszabb lekérdezési terveket hoz elő. Az alábbi dokumentáció a statisztikák létrehozásával és frissítésével kapcsolatos ajánlott eljárásokat ismerteti:

- [Táblázat statisztikáinak létrehozása és frissítése](sql-data-warehouse-tables-statistics.md)

Ha meg szeretné tekinteni az érintett táblák listáját a javaslatok alapján, futtassa az alábbi [T-SQL-szkriptet](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Az Advisor folyamatosan futtatja ugyanazt a T-SQL-szkriptet a javaslatok létrehozásához.

## <a name="replicate-tables"></a>Táblák replikálása

A replikált táblákra vonatkozó javaslatok esetében az Advisor a következő fizikai jellemzők alapján észleli a táblázatos pályázókat:

- Replikált tábla mérete
- Oszlopok száma
- Tábla eloszlásának típusa
- Partíciók száma

Az Advisor folyamatosan kihasználja a munkaterhelés-alapú heurisztikus feladatokat, például a táblák elérési gyakoriságát, az átlagban visszaadott sorokat, valamint az adatraktár méretének és tevékenységének küszöbértékeit, hogy magas színvonalú javaslatokat generáljon.

A következő szakasz a munkaterhelésen alapuló heurisztikus információkat ismerteti, amelyek a Azure Portalban találhatók az egyes replikált táblákra vonatkozó javaslatok esetében:

- Scan AVG – a táblázatból visszaadott sorok átlagos százaléka az elmúlt hét napban való hozzáférés során
- Gyakori olvasás, nincs frissítés – azt jelzi, hogy a tábla nem frissült az elmúlt hét napban a hozzáférési tevékenység megjelenítése közben.
- Olvasási/frissítési arány – annak a gyakorisága, hogy a táblázat milyen gyakran érhető el, ha az elmúlt hét napban frissül
- Tevékenység – a használatot a hozzáférési tevékenység alapján méri. Ez a tevékenység a tábla hozzáférési tevékenységét hasonlítja össze az adattárházban az elmúlt hét napban az átlagos tábla-hozzáférési tevékenységhez képest.

Az Advisor jelenleg legfeljebb négy replikált tábla jelöltjét jeleníti meg egyszerre a fürtözött oszlopcentrikus indexekkel, amelyek rangsorolják a legmagasabb tevékenységet.

> [!IMPORTANT]
> A replikált tábla javaslata nem teljes körűen igazolható, és nem veszi figyelembe az adatáthelyezési műveleteket. Jelenleg is dolgozunk, de a javaslat alkalmazása után mindig ellenőriznie kell a munkaterhelést. A replikált táblákkal kapcsolatos további információkért tekintse meg az alábbi [dokumentációt](design-guidance-for-replicated-tables.md#what-is-a-replicated-table).


## <a name="adaptive-gen2-cache-utilization"></a>Adaptív (Gen2) gyorsítótár kihasználtsága
Ha nagy munkakészlettel rendelkezik, az alacsony gyorsítótárbeli találatok százalékos arányát és a nagy gyorsítótár kihasználtságát tapasztalhatja. Ennél a forgatókönyvnél a gyorsítótár kapacitásának növeléséhez és a számítási feladatok újrafuttatásához növelje a méretezést. További információért látogasson el a következő [dokumentációra](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-how-to-monitor-cache). 

## <a name="tempdb-contention"></a>Tempdb-tartalom

A lekérdezés teljesítménye csökkenhet, ha magas a tempdb-tartalom.  A tempdb-tartalom felhasználó által definiált ideiglenes táblákon vagy nagy mennyiségű adatmozgatáson keresztül fordulhat elő. Ebben a forgatókönyvben több tempdb-kiosztást és az [erőforrás-osztályok és a számítási feladatok kezelését is beállíthatja](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management) , hogy a lekérdezések több memóriát szolgáltassanak. 
