---
title: Dedikált SQL-készletre vonatkozó Azure Advisor javaslatok
description: A Synapse SQL és létrehozása
services: synapse-analytics
author: julieMSFT
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: b418b46199c524ca92d60dece6031073938e159b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568420"
---
# <a name="azure-advisor-recommendations-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Advisor javaslatok dedikált SQL-készlethez a Azure Synapse Analytics

Ez a cikk a dedikált SQL-készletre vonatkozó javaslatokat ismerteti, amelyek a Azure Advisor.  

A dedikált SQL-készlet javaslatokat tesz annak biztosítására, hogy az adattárház számítási feladatai folyamatosan teljesítményre legyenek optimalizálva. A javaslatok szorosan integrálva vannak a [Azure Advisor,](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) hogy közvetlenül a [Azure Portal.](https://aka.ms/Azureadvisor) A dedikált SQL-készlet naponta gyűjt telemetriai adatokat, és javaslatokat tesz az aktív számítási feladatokra. A támogatott javaslati forgatókönyveket az alábbiakban ismertetjük a javasolt műveletek alkalmazásával együtt.

Javaslatait [még ma ellenőrizheti!](https://aka.ms/Azureadvisor) 

## <a name="data-skew"></a>Adatdedúszó

Az adatdeduklódás további adatátmozgatást vagy az erőforrások szűk keresztmetszetét okozhatja a számítási feladatok futtatásakor. A következő dokumentáció azt ismerteti, hogyan lehet azonosítani az adateloszlást, és megelőzni az adateloszlást egy optimális elosztási kulcs kiválasztásával.

- [Az eltúszódás azonosítása és eltávolítása](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Nincs vagy elavult statisztikák

Az optimálisnál rosszabb statisztikák súlyos hatással lehetnek a lekérdezési teljesítményre, mivel ez azt okozhatja, hogy az SQL-lekérdezésoptimalátor nem optimális lekérdezésterveket hoz létre. A következő dokumentáció a statisztikák létrehozásával és frissítésével kapcsolatos ajánlott eljárásokat ismerteti:

- [Táblastatisztikák létrehozása és frissítése](sql-data-warehouse-tables-statistics.md)

A javaslatok által érintett táblák listájának az alábbi  [T-SQL-szkript](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)futtatásával megjelenik. Az Advisor folyamatosan ugyanazt a T-SQL-szkriptet futtatja a javaslatok létrehozásához.

## <a name="replicate-tables"></a>Táblák replikálása

A replikált táblaajánlott javaslatok esetén az Advisor a következő fizikai jellemzők alapján észleli a tábla jelöltjeit:

- Replikált tábla mérete
- Oszlopok száma
- Táblaelosztási típus
- Partíciók száma

Az Advisor folyamatosan kihasználja a számítási feladatokon alapuló heurisztikákat, például a táblaelérési gyakoriságot, az átlagban visszaadott sorokat és az adattárház méretére és tevékenységére vonatkozó küszöbértékeket, így jó minőségű javaslatokat hoz létre.

A következő szakasz a replikált táblaajánlott Azure Portal heurisztikáit ismerteti:

- Vizsgálat átlaga – a táblázatból visszaadott sorok átlagos százalékos aránya az egyes táblaelérések során az elmúlt hét napban
- Gyakori olvasás, nincs frissítés – azt jelzi, hogy a táblázat nem frissült az elmúlt hét napban a hozzáférési tevékenység megjelenítése közben
- Olvasási/frissítési arány – a táblázat hozzáférésének aránya ahhoz képest, hogy az elmúlt hét napban mikor frissült
- Tevékenység – a használatot méri a hozzáférési tevékenység alapján. Ez a tevékenység összehasonlítja a tábla hozzáférési tevékenységét az adattárház átlagos tábla-hozzáférési tevékenységéhez viszonyítva az elmúlt hét napban.

Jelenleg az Advisor egyszerre csak négy replikált táblát mutat be, a fürtözött oszlopcentrikus indexek a legmagasabb tevékenységet rangsorolják.

> [!IMPORTANT]
> A replikált táblára vonatkozó javaslat nem teljes körű, és nem veszi figyelembe az adatátmozgatási műveleteket. Dolgozunk ennek heurisztikusként való hozzáadásán, de addig is mindig ellenőrizze a számítási feladatot a javaslat alkalmazása után. A replikált táblákkal kapcsolatos további információkért olvassa el a következő [dokumentációt.](design-guidance-for-replicated-tables.md#what-is-a-replicated-table)


## <a name="adaptive-gen2-cache-utilization"></a>Adaptív (Gen2) gyorsítótár-használat
Ha nagy méretű munkakészlete van, alacsony gyorsítótár-találati arányt és magas gyorsítótár-kihasználtságot tapasztalhat. Ebben a forgatókönyvben érdemes horizontálisan felskálával növelni a gyorsítótár-kapacitást, és újrafuttatni a számítási feladatot. További információt a következő dokumentációban [talál:](./sql-data-warehouse-how-to-monitor-cache.md). 

## <a name="tempdb-contention"></a>Tempdb-tartalom

A lekérdezési teljesítmény csökkenhet, ha magas a tempdb-hez valóértés.  A Tempdb-hez való hozzáférés a felhasználó által megadott ideiglenes táblákon keresztül vagy nagy mennyiségű adatátmozgatás esetén fordulhat elő. Ebben a forgatókönyvben több tempdb-lefoglalást is skálázhat, és konfigurálhatja az erőforrásosztályokat és a számítási feladatok kezelését, hogy több memóriát biztosítson a lekérdezésekhez. [](./sql-data-warehouse-workload-management.md) 

## <a name="data-loading-misconfiguration"></a>Helytelen adatbetöltési konfiguráció

A késés minimalizálása érdekében mindig töltse be az adatokat egy, a dedikált SQL-készletével azonos régióban található tárfiókból. Használja a [COPY utasítást a nagy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) átviteli sebességű adatbeteléshez, és ossza fel az szakaszos fájlokat a tárfiókban az átviteli sebesség maximalizálása érdekében. Ha nem tudja használni a COPY utasítást, használhatja az SqlBulkCopy API-t vagy a bcp-t nagy kötegmérettel a jobb átviteli sebesség érdekében. További adatbetöltési útmutatásért látogasson el a következő [dokumentációba.](./guidance-for-loading-data.md)