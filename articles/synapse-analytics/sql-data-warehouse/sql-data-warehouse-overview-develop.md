---
title: Erőforrások egy dedikált SQL-készlet (korábban SQL DW) fejlesztéséhez az Azure szinapszis Analyticsben
description: Fejlesztési fogalmak, tervezési döntések, javaslatok és kódolási technikák egy dedikált SQL-készlethez (korábban SQL DW) az Azure szinapszis Analyticsben.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 6b34c70b453c26fe27a51e1aa802564864640cb9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96453683"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Tervezési döntések és kódolási technikák egy dedikált SQL-készlethez (korábban SQL DW) az Azure szinapszis Analyticsben 

 Ebben a cikkben további forrásokat talál, amelyek segítségével jobban megismerheti a legfontosabb tervezési döntéseket, javaslatokat és kódolási technikákat egy dedikált SQL-készlethez (korábban SQL DW) az Azure Szinapszisban.

## <a name="key-design-decisions"></a>Kulcsfontosságú tervezési döntések

Az alábbi cikkek kiemelik azokat az alapfogalmakat és kialakítási döntéseket, amelyekkel elosztott adattárházat fejleszthet az Azure szinapszis dedikált SQL Pool (korábban SQL DW) funkciójának használatával:

* [kapcsolatok](sql-data-warehouse-connect-overview.md)
* [Egyidejűség](resource-classes-for-workload-management.md)
* [tranzakciók](sql-data-warehouse-develop-transactions.md)
* [felhasználó által definiált sémák](sql-data-warehouse-develop-user-defined-schemas.md)
* [tábla eloszlása](sql-data-warehouse-tables-distribute.md)
* [tábla indexei](sql-data-warehouse-tables-index.md)
* [tábla partíciói](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [statisztikák](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Fejlesztési javaslatok és kódolási technikák

Az alábbi cikkekben speciális kódolási technikák, tippek és javaslatok szerepelnek a dedikált SQL-készlet (korábban SQL DW) fejlesztéséhez:

* [tárolt eljárások](sql-data-warehouse-develop-stored-procedures.md)
* [Címkék](sql-data-warehouse-develop-label.md)
* [kilátással](performance-tuning-materialized-views.md)
* [ideiglenes táblák](sql-data-warehouse-tables-temporary.md)
* [dinamikus SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [hurkolás](sql-data-warehouse-develop-loops.md)
* [csoportosítási beállítások](sql-data-warehouse-develop-group-by-options.md)
* [változó-hozzárendelés](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Következő lépések

További információ: [T-SQL-utasítások](sql-data-warehouse-reference-tsql-statements.md).
