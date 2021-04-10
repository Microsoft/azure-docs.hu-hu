---
title: Optimalizálja a Gen2 cache-t
description: Megtudhatja, hogyan figyelheti a Gen2 cache-gyorsítótárat a Azure Portal használatával.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: fed3ed2c87342d557872e97bfc2a6c4d142b5a3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585621"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>Az adaptív gyorsítótár figyelése

Ez a cikk bemutatja, hogyan figyelheti és elháríthatja a lassú lekérdezési teljesítményt azzal, hogy meghatározza, hogy a számítási feladatok optimálisan hasznosítják-e a dedikált SQL-készletek adaptív gyorsítótárát.

A dedikált SQL-készlet tárolási architektúrája automatikusan lekéri a leggyakrabban lekérdezett oszlopcentrikus-szegmenseket a NVMe-alapú SSD-k által használt gyorsítótárban. Nagyobb teljesítményre lesz szüksége, ha a lekérdezések a gyorsítótárban lévő szegmenseket kérik le.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Hibakeresés a Azure Portal használatával

A Azure Monitor használatával megtekintheti a gyorsítótár-metrikákat a lekérdezés teljesítményének hibakereséséhez. Először lépjen a Azure Portalra, és kattintson a **figyelés**, a **metrikák** és a **+ hatókör kijelölése** elemre:

![A képernyőképen a Azure Portal Metrikái közül kiválasztott hatókör kiválasztása látható.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

A keresés és a legördülő sávok segítségével keresse meg a dedikált SQL-készletet. Ezután válassza az alkalmaz lehetőséget.

![A képernyőképen a hatókör kiválasztása panel látható, ahol kiválaszthatja az adattárházat.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

A gyorsítótárral kapcsolatos hibaelhárítás fő mérőszámai a **gyorsítótár-találatok százalékos aránya** és a **gyorsítótárban használt százalék**. Válassza a **gyorsítótár találati százaléka** lehetőséget, majd a **metrika hozzáadása** gombbal adja hozzá a **gyorsítótár használt százalékát**. 

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

## <a name="next-steps"></a>Következő lépések
Az általános lekérdezési teljesítmény finomhangolásával kapcsolatos további információkért lásd: a [lekérdezés-végrehajtás figyelése](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
