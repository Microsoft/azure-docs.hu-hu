---
title: Kezelhetőség és monitorozás – lekérdezési tevékenység, erőforrás-használat
description: Megtudhatja, milyen képességek érhetők el a szolgáltatások kezeléséhez és Azure Synapse Analytics. A Azure Portal és dinamikus felügyeleti nézetek (DMV-k) segítségével megértheti az adattárház lekérdezési tevékenységét és erőforrás-használatát.
services: synapse-analytics
author: julieMSFT
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7b103991e22ffab8ed5bd2b3c10400330e1d09b3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568403"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Erőforrás-használat és lekérdezési tevékenység monitorozása a Azure Synapse Analytics

Azure Synapse Analytics gazdag monitorozási élményt biztosít a Azure Portal az adattárház számítási feladatával kapcsolatos információk kiéséhez. Az Azure Portal ajánlott eszköz az adattárház monitorozásához, mivel konfigurálható megőrzési időtartamokat, riasztásokat, javaslatokat, valamint testreszabható diagramokat és irányítópultokat biztosít a metrikákhoz és naplókhoz. A portál azt is lehetővé teszi, hogy más Azure monitorozási szolgáltatásokkal, például az Azure Monitor-nal (naplók) integrálva átfogó monitorozási élményt nyújtson nemcsak az adattárház, hanem az integrált monitorozási élmény érdekében a teljes Azure-elemzési platform számára is. Ez a dokumentáció ismerteti, hogy milyen monitorozási képességek állnak rendelkezésre az elemzési platform optimalizálásához és kezeléséhez a Synapse SQL.

## <a name="resource-utilization"></a>Erőforrás-felhasználás

A következő metrikák érhetők el a Azure Portal a Synapse SQL. Ezek a metrikák a következő [Azure Monitor.](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics)

| Metrika neve             | Description                                                  | Aggregáció típusa |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Processzorhasználat (%)          | Cpu-kihasználtság az adattárház összes csomópontja között      | Átlag, minimum, maximum    |
| Adat IO kihasználtsága (%)      | Az adattárház összes csomópontja I/O-kihasználtsága       | Átlag, minimum, maximum    |
| Memória százalékos aránya       | Memóriahasználat (SQL Server) az adattárház összes csomópontja között | Átlag, minimum, maximum   |
| Aktív lekérdezések          | A rendszeren végrehajtható aktív lekérdezések száma             | Sum              |
| Várólistán lévő lekérdezések          | A végrehajtásra váró várólistán lévő lekérdezések száma          | Sum              |
| Sikeres kapcsolatok  | Sikeres kapcsolatok (bejelentkezések) száma az adatbázison | Összeg, darabszám       |
| Sikertelen kapcsolatok      | Az adatbázison meghiúsult kapcsolatok (bejelentkezések) száma | Összeg, darabszám       |
| A tűzfal blokkolja     | Az adattárházba való, blokkolt bejelentkezések száma     | Összeg, darabszám       |
| DWU-korlát               | Az adattárház szolgáltatásiszint-célkitűzése                | Átlag, minimum, maximum    |
| DWU-százalék          | Maximális érték a processzorhasználat és az adat-I/O-arány között        | Átlag, minimum, maximum    |
| Használt DWU                | DWU-korlát * DWU-százalékos érték                                   | Átlag, minimum, maximum    |
| Gyorsítótár-találatok százalékos aránya    | (gyorsítótár-találatok /gyorsítótár-tévesztés) * 100, ahol a gyorsítótár-találatok a helyi SSD-gyorsítótárban található összes oszlopcentrikus szegmens találatának összege, a gyorsítótár-tévesztés pedig a helyi SSD-gyorsítótár oszlopcentrikus szegmensei az összes csomópontra összegezve | Átlag, minimum, maximum    |
| Gyorsítótár felhasznált százalékos aránya   | (felhasznált gyorsítótár/gyorsítótár-kapacitás) * 100, ahol a felhasznált gyorsítótár a helyi SSD-gyorsítótárban lévő összes bájt összes bájtja az összes csomóponton és a gyorsítótár-kapacitás az összes csomóponton lévő helyi SSD-gyorsítótár tárolási kapacitásának összege | Átlag, minimum, maximum    |
| Helyi tempdb százalékos aránya | Helyi tempdb-kihasználtság az összes számítási csomóponton – az értékek öt percenként vannak kiadva | Átlag, minimum, maximum    |

Metrikák megtekintésekor és riasztások beállításakor megfontolt dolgok:

- A használt DWU csak a használat magas szintű ábrázolása az **SQL-készletben,** és nem a használat átfogó jelzője. Annak megállapításához, hogy a DWU milyen tényezőkre lehet hatással, például az egyidejűség, a memória, a tempdb és az adaptív gyorsítótár-kapacitás, vegye figyelembe, hogy a DWU milyen tényezőket befolyásol. [Javasoljuk, hogy a számítási feladatot különböző DWU-beállításokon](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) futtasa annak meghatározásához, hogy mi felel meg a legjobban az üzleti célkitűzéseknek.
- A sikertelen és sikeres kapcsolatok jelentése egy adott adattárházra, nem pedig magára a kiszolgálóra történik.
- A memória százalékos aránya akkor is tükrözi a kihasználtságot, ha az adattárház tétlen állapotban van – nem tükrözi az aktív számítási feladat memóriahasználatát. A metrikával és másokkal (tempdb, gen2 cache) együtt használva átfogó döntést hoz arról, hogy a további gyorsítótár-kapacitás skálázása növeli-e a számítási feladatok teljesítményét az igényeinek megfelelően.

## <a name="query-activity"></a>Lekérdezési tevékenység

A T-SQL-Synapse SQL történő figyelés programozott élménye érdekében a szolgáltatás dinamikus felügyeleti nézetek (DMV-k) készletét biztosítja. Ezek a nézetek hasznosak a számítási feladat teljesítménybeli szűk keresztmetszetének aktív hibaelhárítása és azonosítása során.

Az alkalmazásra vonatkozó DMV-k listájának Synapse SQL tekintse meg ezt a [dokumentációt.](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs) 

## <a name="metrics-and-diagnostics-logging"></a>Metrikák és diagnosztikai naplózás 

Mind a metrikák, mind a naplók exportálhatók Azure Monitor, pontosabban a [Azure Monitor-naplók](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) összetevőbe, és programozott módon érhetők el naplólekérdezésekkel. [](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json) A naplók késése Synapse SQL körülbelül 10–15 perc. A késést befolyásoló tényezőkről az alábbi dokumentációban talál további információt.

## <a name="next-steps"></a>Következő lépések

Az alábbi útmutató az adattárház figyelése és kezelése során gyakran használt forgatókönyveket és alkalmazási helyzeteket ismerteti:

- [Az adattárház számítási feladatainak monitorozása DMV-k segítségével](sql-data-warehouse-manage-monitor.md)