---
title: A Gen2-gyorsítótár optimalizálása
description: Ismerje meg, hogyan figyelheti a Gen2-gyorsítótárat a Azure Portal.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 9de795c54f55295fa69ed7fcb5dd894e2963385b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566630"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>Az adaptív gyorsítótár figyelése

Ez a cikk azt ismerteti, hogyan figyelheti és háríthatja el a lassú lekérdezési teljesítményt annak meghatározásával, hogy a számítási feladatok optimálisan hasznosak-e a dedikált SQL-készletek adaptív gyorsítótárának használatával.

A dedikált SQL-készlet tárolási architektúrája automatikusan rétegzi a leggyakrabban lekérdezett oszlopcentrikus szegmenseket egy NVMe-alapú SSD-n található gyorsítótárban. Nagyobb teljesítményt fog eredményezni, ha a lekérdezések a gyorsítótárban található szegmenseket lekérik.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Hibaelhárítás a Azure Portal

A lekérdezési teljesítmény Azure Monitor gyorsítótár-metrikák megtekintéséhez használhatja a lekérdezési adatokat. Először is ugrás a Azure Portal kattintson a **Figyelés,** a **Metrikák** és **a + Hatókör kiválasztása elemre:**

![Képernyőkép a kiválasztott hatókör kiválasztásáról a metrikák közül a Azure Portal.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

A keresés és a legördülő sávok használatával keresse meg a dedikált SQL-készletet. Ezután válassza az Alkalmaz lehetőséget.

![Képernyőkép a Hatókör kiválasztása panelről, ahol kiválaszthatja az adattárházat.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

A gyorsítótár hibaelhárításának fő mérőszámai a Gyorsítótár-találatok **százalékos aránya** és a Gyorsítótár által felhasznált **százalékos érték.** Válassza **a Gyorsítótár-találatok százalékos aránya lehetőséget,** majd használja a **Metrika hozzáadása** gombot a **Gyorsítótárban használt százalékos arány hozzáadásához.** 

![Gyorsítótár-metrikák](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Gyorsítótár-találatok és felhasznált százalékos arány

Az alábbi mátrix a gyorsítótár-metrikák értékei alapján ismerteti a forgatókönyveket:

|                                | **Magas gyorsítótár-találatok százalékos aránya** | **Alacsony gyorsítótár-találatok százalékos aránya** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Magas gyorsítótár-felhasznált százalékos arány** |          1\. példa           |          2\. példa          |
| **Alacsony gyorsítótár-felhasznált százalékos arány**  |          3\. példa           |          4\. példa          |

**1. forgatókönyv:** Optimálisan használja a gyorsítótárat. [Hárítsa](sql-data-warehouse-manage-monitor.md) el a lekérdezéseket lelassító egyéb területeket.

**2. forgatókönyv:** Az aktuálisan működő adatkészlet nem fér el a gyorsítótárban, ami a fizikai olvasások miatt alacsony gyorsítótár-találati arányt okoz. Fontolja meg a teljesítményszint felméretezését, és futtassa újra a számítási feladatot a gyorsítótár feltöltéséhez.

**3. forgatókönyv:** Valószínű, hogy a lekérdezés a gyorsítótárhoz nem kapcsolódó okok miatt lassan fut. [Hárítsa](sql-data-warehouse-manage-monitor.md) el a lekérdezéseket lelassító egyéb területeket. Érdemes megfontolni a példány [leméretezését](sql-data-warehouse-manage-monitor.md) is a gyorsítótár méretének csökkentése érdekében a költségek csökkentése érdekében. 

**4. forgatókönyv:** Hideg gyorsítótára volt, ami miatt a lekérdezés lassú volt. Érdemes lehet újrafuttatni a lekérdezést, mivel a működő adatkészletnek gyorsítótárban kell lennie. 

> [!IMPORTANT]
> Ha a gyorsítótár-találatok százalékos aránya vagy a felhasznált gyorsítótár százalékos aránya nem frissül a számítási feladat újrafuttatása után, a munkakészlet már a memóriában is lehet. A rendszer csak a fürtözött oszlopcentrikus táblákat gyorsítótárazza.

## <a name="next-steps"></a>Következő lépések
A lekérdezési teljesítmény általános finomhangolásával kapcsolatos további információkért lásd: [Lekérdezések végrehajtásának figyelése.](sql-data-warehouse-manage-monitor.md#monitor-query-execution)
