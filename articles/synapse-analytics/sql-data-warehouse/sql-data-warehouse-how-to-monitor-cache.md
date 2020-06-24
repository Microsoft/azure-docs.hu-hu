---
title: Optimalizálja a Gen2 cache-t
description: Megtudhatja, hogyan figyelheti a Gen2 cache-gyorsítótárat a Azure Portal használatával.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 4d66a1174b1b4adc94b24c6aecd55b2b8679f2f7
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85211884"
---
# <a name="how-to-monitor-the-gen2-cache"></a>A Gen2 cache figyelése

Ez a cikk bemutatja, hogyan figyelheti és elháríthatja a lassú lekérdezési teljesítményt, ha meghatározza, hogy a munkaterhelés optimálisan kihasználja-e a Gen2 cache-gyorsítótárat.

A Gen2-tároló architektúrája automatikusan lekéri a leggyakrabban lekérdezett oszlopcentrikus-szegmenseket a NVMe-alapú SSD-meghajtókon alapuló, Gen2 adattárházak számára tervezett gyorsítótárban. Nagyobb teljesítmény érhető el, ha a lekérdezések a gyorsítótárban lévő szegmenseket kérik le.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Hibakeresés a Azure Portal használatával

Az Azure Monitor használatával megtekintheti a Gen2 gyorsítótár-metrikáit a lekérdezés teljesítményének hibakereséséhez. Először lépjen a Azure Portalra, és kattintson a **figyelés**, a **metrikák** és a **+ hatókör kijelölése**elemre:

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Az adattárház megkereséséhez használja a keresési és a legördülő sávokat. Ezután válassza az alkalmaz lehetőséget.

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

A Gen2 gyorsítótár hibaelhárításának legfontosabb mérőszámai a **gyorsítótár-találatok százalékos aránya** és a **gyorsítótárban használt százalék**. Válassza a **gyorsítótár találati százaléka** lehetőséget, majd a **metrika hozzáadása** gombbal adja hozzá a **gyorsítótár használt százalékát**. 

![Gyorsítótár Metrikái](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Gyorsítótár-találat és a felhasznált százalék

Az alábbi mátrix a gyorsítótár metrikáinak értékein alapuló forgatókönyveket ismerteti:

|                                | **Nagy gyorsítótárbeli találatok százalékos aránya** | **Alacsony gyorsítótárbeli találatok százalékos aránya** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Nagy gyorsítótár használt százaléka** |          1\. példa           |          2\. példa          |
| **Alacsony gyorsítótár-használat százalékos aránya**  |          3\. példa           |          4\. példa          |

**1. forgatókönyv:** Ön optimálisan használja a gyorsítótárat. A lekérdezéseket esetlegesen lelassító egyéb területek [hibáinak megoldása](sql-data-warehouse-manage-monitor.md) .

**2. forgatókönyv:** A jelenlegi munkakészlet nem fér bele a gyorsítótárba, ami a fizikai olvasások miatt alacsony gyorsítótárbeli találati arányt okoz. Érdemes lehet felskálázást végezni a teljesítmény szintjén, és újra kell futtatni a munkaterhelést a gyorsítótár feltöltéséhez.

**3. forgatókönyv:** Valószínű, hogy a lekérdezés lassan fut, mert nem kapcsolódik a gyorsítótárhoz. A lekérdezéseket esetlegesen lelassító egyéb területek [hibáinak megoldása](sql-data-warehouse-manage-monitor.md) . Azt is megteheti, hogy [a példány méretezésével](sql-data-warehouse-manage-monitor.md) csökkenti a gyorsítótár méretét a költségek megtakarítása érdekében. 

**4. forgatókönyv:** A lassú gyorsítótárat használta, ami lehet az oka, hogy a lekérdezés lassú volt. Érdemes lehet újból futtatni a lekérdezést, mert a munkakészlete most már gyorsítótárazva van. 

> [!IMPORTANT]
> Ha a gyorsítótár-találatok százalékos aránya vagy a gyorsítótár kihasználtsági aránya nem frissül a munkaterhelés újbóli futtatása után, a munkakészlete már a memóriában is tartózkodik. A rendszer csak a fürtözött oszlopcentrikus-táblákat gyorsítótárazza.

## <a name="next-steps"></a>További lépések
Az általános lekérdezési teljesítmény finomhangolásával kapcsolatos további információkért lásd: a [lekérdezés-végrehajtás figyelése](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
