---
title: Forrásforrások a Synapse SQL fejlesztéshez
description: Fejlesztési fogalmak, tervezési döntések, javaslatok és kódolási technikák Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4d842414d3046692c982ca3203957a96f8a01b37
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377330"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Tervezési döntések és kódolási technikák a Synapse SQL funkcióihoz Azure Synapse Analytics
Ebben a cikkben megtalálja a dedikált SQL-készlethez és a kiszolgáló nélküli SQL-készlet funkcióihoz használható erőforrások listáját a Synapse SQL. Az ajánlott cikkek két szakaszra oszlnak: Fő tervezési döntések, valamint fejlesztési és kódolási technikák.

A cikkek célja, hogy segítsenek az optimális technikai megközelítés kialakításában a Synapse SQL összetevőinek Azure Synapse Analytics.

## <a name="key-design-decisions"></a>Fő tervezési döntések
Az alábbi cikkek a fejlesztéssel kapcsolatos fogalmakat és tervezési Synapse SQL emelik ki:

| Cikk | dedikált SQL-készlet | kiszolgáló nélküli SQL-készlet |
| ------- | -------- | ------------- |
| [Kapcsolatok](connect-overview.md)                    | Igen | Igen |
| [Erőforrásosztályok és egyidejűség](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Igen    | Nem |
| [Tranzakciók](develop-transactions.md)              | Igen | Nem |
| [Felhasználó által definiált sémák](develop-user-defined-schemas.md) | Igen | Igen |
| [a táblaelosztással](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Igen | Nem |
| [táblaindexekkel](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Igen | Nem |
| [Táblapartíciók](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Igen | Nem |
| [Statisztika](develop-tables-statistics.md)            | Igen | Igen |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Igen | Nem |
| [Külső táblák](develop-tables-external-tables.md) | Igen | Igen |
| [CETAS](develop-tables-cetas.md)                     | Igen | Igen |


## <a name="recommendations"></a>Javaslatok

Az alábbiakban olyan alapvető cikkeket talál, amelyek konkrét kódolási technikákat, tippeket és fejlesztési javaslatokat emelnek ki:

| Cikk | dedikált SQL-készlet | kiszolgáló nélküli SQL-készlet |
| ------- | -------- | ------------- |
| [Tárolt eljárások](develop-stored-procedures.md)  | Igen                | Igen                      |
| [Címkék](develop-label.md)                           | Igen                | Nem                      |
| [Megjelenítések](develop-views.md)                             | Igen                | Igen                     |
| [ideiglenes táblákkal](develop-tables-temporary.md)       | Igen                | Igen                     |
| [Dinamikus SQL](develop-dynamic-sql.md)                 | Igen                | Igen                     |
| [Hurkolás](develop-loops.md)                         | Igen                | Igen                     |
| [Csoportosítás beállítások szerint](develop-group-by-options.md)       | Igen                | Nem                      |
| [Változó-hozzárendelés](develop-variable-assignment.md) | Igen                | Igen                     |

## <a name="next-steps"></a>Következő lépések
További információkért lásd: [SQL-készlet T-SQL-utasításai.](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

