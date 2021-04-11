---
title: Kezelhetőség és monitorozás – lekérdezési tevékenység, erőforrás-használat
description: Ismerje meg, hogy mely funkciók érhetők el az Azure szinapszis Analytics felügyeletéhez és figyeléséhez. Az adattárház lekérdezési tevékenységének és erőforrás-kihasználtságának megismeréséhez használja a Azure Portal és a dinamikus felügyeleti nézeteket (DMV).
services: synapse-analytics
author: gaursa
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e7f093860e9962db055a7551f5cdb608ecad9b29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585672"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Erőforrás-kihasználtság és lekérdezési tevékenységek figyelése az Azure szinapszis Analyticsben

Az Azure szinapszis Analytics a Azure Portalon belül gazdag monitorozási lehetőségeket biztosít az adattárház-számítási feladatokra vonatkozóan. Az Azure Portal az ajánlott eszköz az adattárház figyelésére, mivel konfigurálható megőrzési időt, riasztásokat, javaslatokat és testreszabható diagramokat és irányítópultokat biztosít a metrikák és naplók számára. A portál lehetővé teszi az integrációt más Azure monitoring-szolgáltatásokkal, például a Azure Monitor (naplókkal) a log Analytics segítségével, amely átfogó figyelési élményt nyújt a nem csupán az adattárházhoz, hanem a teljes Azure Analytics-platformot is, amely integrált figyelési élményt biztosít. Ez a dokumentáció ismerteti, hogy milyen figyelési funkciók érhetők el az elemzési platformnak a szinapszis SQL-vel való optimalizálásához és kezeléséhez.

## <a name="resource-utilization"></a>Erőforrás-felhasználás

A következő mérőszámok érhetők el a szinapszis SQL-Azure Portalban. Ezek a metrikák [Azure monitoron](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics)keresztül vannak felszínben.

| Metrika neve             | Leírás                                                  | Aggregáció típusa |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Processzorhasználat (%)          | CPU-kihasználtság az adatraktár összes csomópontja között      | AVG, min, Max    |
| Adat IO kihasználtsága (%)      | I/o-kihasználtság az adatraktár összes csomópontja között       | AVG, min, Max    |
| Memória százaléka       | Memória kihasználtsága (SQL Server) az adatraktár összes csomópontján | AVG, min, Max   |
| Aktív lekérdezések          | A rendszeren futtatott aktív lekérdezések száma             | Sum              |
| Várólistán lévő lekérdezések          | A végrehajtás megkezdésére váró üzenetsor-lekérdezések száma          | Sum              |
| Sikeres kapcsolatok  | A sikeres kapcsolatok (bejelentkezések) száma az adatbázison | Összeg, darabszám       |
| Sikertelen kapcsolatok      | Sikertelen kapcsolatok (bejelentkezések) száma az adatbázison | Összeg, darabszám       |
| A tűzfal blokkolja     | A letiltott adatraktárba való bejelentkezések száma     | Összeg, darabszám       |
| DWU korlátja               | Az adatraktár szolgáltatási szintjének célkitűzése                | AVG, min, Max    |
| DWU százalékos aránya          | A CPU-százalék és az adatio-százalék közötti maximális érték        | AVG, min, Max    |
| Használt DWU                | DWU korlát * DWU százalék                                   | AVG, min, Max    |
| Gyorsítótár találati százaléka    | (gyorsítótárbeli találatok/gyorsítótár-kihagyás) * 100, ahol a gyorsítótár-találatok a helyi SSD-gyorsítótár és a gyorsítótár-kihagyott oszlopcentrikus összes találatának összege az összes csomóponton összefoglalt helyi SSD-gyorsítótárban. | AVG, min, Max    |
| Gyorsítótár használt százaléka   | (gyorsítótár-használat/gyorsítótár-kapacitás) * 100, ahol a használt gyorsítótár a helyi SSD gyorsítótárban lévő összes bájt összege az összes csomóponton és a gyorsítótár kapacitása a helyi SSD-gyorsítótár tárolási kapacitása az összes csomóponton belül | AVG, min, Max    |
| Helyi tempdb százalékos aránya | Helyi tempdb kihasználtsága az összes számítási csomóponton – az értékek öt percenként vannak kibocsátva | AVG, min, Max    |

A metrikák megtekintésekor megfontolandó szempontok és riasztások beállítása:

- A használt DWU csak az SQL-készletben lévő **használat magas szintű ábrázolását** jelöli, és nem a kihasználtság átfogó mutatója. Annak megállapításához, hogy fel vagy le kell-e skálázást, gondolja át a DWU által befolyásolható összes tényezőt, például a párhuzamosságot, a memóriát, a tempdb és az adaptív gyorsítótárazási kapacitást. Azt javasoljuk, hogy [a számítási feladatokat különböző DWU-beállításokon futtassa](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) , hogy megtudja, mi a legmegfelelőbb az üzleti célok teljesítéséhez.
- A sikertelen és sikeres kapcsolatok egy adott adattárházra vonatkozóan lesznek jelezve – nem maga a kiszolgáló.
- A memória százalékos aránya a kihasználtságot is tükrözi, még akkor is, ha az adatraktár tétlen állapotban van – nem tükrözi az aktív munkaterhelés-memória használatát. A metrikák használata és nyomon követése másokkal (tempdb, Gen2 cache), hogy átfogó döntést hozzon, ha a további gyorsítótár-kapacitás skálázása növeli a munkaterhelés teljesítményét a követelmények teljesítése érdekében.

## <a name="query-activity"></a>Lekérdezési tevékenység

Programozási élmény a szinapszis SQL T-SQL-n keresztül történő figyelése során a szolgáltatás a dinamikus felügyeleti nézetek (DMV-EK) készletét nyújtja. Ezek a nézetek akkor hasznosak, ha aktívan hibaelhárítást végez, és azonosítja a teljesítménnyel kapcsolatos szűk keresztmetszeteket.

A szinapszis SQL-re vonatkozó DMV listájának megtekintéséhez tekintse meg ezt a [dokumentációt](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metrikák és diagnosztikai naplózás 

Mindkét metrika és napló exportálható Azure Monitorba, pontosabban a [Azure monitor naplózó](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) összetevőt, és a [naplózási lekérdezésekkel](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)programozott módon férhet hozzá. A szinapszis SQL-naplózási késése körülbelül 10-15 percet vesz igénybe. A késést befolyásoló tényezőkkel kapcsolatos további információkért látogasson el a következő dokumentációra.

## <a name="next-steps"></a>Következő lépések

Az alábbi útmutató a gyakori forgatókönyveket és a használati eseteket ismerteti az adatraktár monitorozása és kezelése során:

- [Az adatraktár munkaterhelésének figyelése a DMV](sql-data-warehouse-manage-monitor.md)