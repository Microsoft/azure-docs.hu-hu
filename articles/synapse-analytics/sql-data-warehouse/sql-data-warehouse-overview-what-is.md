---
title: Mi az a dedikált SQL-készlet (korábban SQL DW)?
description: Az Azure szinapszis Analytics dedikált SQL-készlete (korábbi nevén SQL DW) a vállalati adattárház-kezelő funkció az Azure szinapszis Analyticsben.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9077ce35065b1bf45646496cc4c43d6def82d958
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685225"
---
# <a name="what-is-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Mi a dedikált SQL-készlet (korábban SQL DW) az Azure szinapszis Analyticsben?

Az Azure Synapse Analytics egy elemzőszolgáltatás, amely egyesíti a vállalati adattárházakat és a Big Data-elemzéseket. A dedikált SQL-készlet (korábbi nevén SQL DW) az Azure szinapszis Analyticsben elérhető vállalati adattárház-funkciókra utal.



![Dedikált SQL-készlet (korábban SQL DW) az Azure Szinapszishoz képest](./media/sql-data-warehouse-overview-what-is/dedicated-sql-pool.png)



A dedikált SQL-készlet (korábbi nevén SQL DW) a szinapszis SQL használatakor kiépített analitikai erőforrások gyűjteményét jelöli. A dedikált SQL-készlet (korábban SQL DW) méretét az adatraktározási egységek (DWU-EK) határozzák meg.

Miután létrehozta a dedikált SQL-készletet, importálhatja a big datat egyszerű, [Base](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL-lekérdezésekkel, majd az elosztott lekérdezési motor erejével futtathatja a nagy teljesítményű elemzéseket. Az adat integrálásával és elemzésével a dedikált SQL-készlet (korábban SQL DW) lesz az igazság egyetlen verziója, amelyet az üzleti vállalkozása a gyorsabb és robusztusabb elemzésekhez is felhasználhat.

> [!NOTE]
>Ismerje meg az [Azure szinapszis Analytics dokumentációját](../overview-what-is.md).
> 

## <a name="key-component-of-a-big-data-solution"></a>Egy big data-megoldás fő összetevője

Az adattárházak a felhőalapú, végpontok közötti big data megoldások egyik kulcsfontosságú összetevője.

![Adattárház-megoldás](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

A felhőbeli adatmegoldásokban az adatok a különböző forrásokból big data-tárolóba kerülnek. Miután bekerültek a big data-tárolókba, a Hadoop-, a Spark- és a gépi tanulási algoritmusok előkészítik és betanítják az adatokat. Ha az adatok készen állnak az összetett elemzésre, a dedikált SQL-készlet a big data-tárolók lekérdezéséhez használ albase-t. A Base standard T-SQL-lekérdezéseket használ, hogy az adatok dedikált SQL-készletbe (korábban SQL DW) táblái legyenek.

A dedikált SQL-készlet (korábbi nevén SQL DW) oszlopos tárolással rendelkező, egymással összefüggő táblákban tárolja az adataikat. Ez a formátum jelentős mértékben csökkenti az adattárolási költségeket, és javítja a lekérdezési teljesítményt. Az adattárolást követően nagy méretekben futtathat elemzéseket. A hagyományos adatbázis-rendszerekkel összehasonlítva az elemzési lekérdezések percek helyett másodpercek alatt, vagy akár napok helyett órák alatt képesek lefutni.

Az elemzések eredményei globális jelentéskészítési adatbázisokba vagy alkalmazásokba küldhetőek. Az üzleti elemzők így az ezekből nyerhető betekintések révén tájékozott üzleti döntéseket hozhatnak.

## <a name="next-steps"></a>Következő lépések

- Az [Azure szinapszis architektúrájának](massively-parallel-processing-mpp-architecture.md) megismerése
- [DEDIKÁLT SQL-készlet gyors létrehozása](create-data-warehouse-portal.md)
- [Mintaadatok betöltése](./load-data-from-azure-blob-storage-using-copy.md).
- [Videók](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse) megismerése

Vagy tekintse meg a többi Azure szinapszis-erőforrást.

- Keresési [Blogok](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Szolgáltatási kérelmek](https://feedback.azure.com/forums/307516-sql-data-warehouse) elküldése
- [Támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md)
- Keresés [A Microsoft Q&A kérdéses oldalon](/answers/topics/azure-synapse-analytics.html)
- [Stack overflow fórum](https://stackoverflow.com/questions/tagged/azure-sqldw) keresése