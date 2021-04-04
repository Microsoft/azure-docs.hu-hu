---
title: Dedikált SQL-készlet (korábban SQL DW) – gyakori kérdések
description: Ez a cikk a dedikált SQL-készlettel (korábban SQL DW) kapcsolatos gyakori kérdéseket sorolja fel az ügyfelek és fejlesztők Azure szinapszis Analytics szolgáltatásában.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: c8f8ae81f6f030245899ec82dbe16b29846dab23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460511"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-frequently-asked-questions"></a>Dedikált SQL-készlet (korábban SQL DW) az Azure szinapszis Analyticsben – gyakori kérdések

## <a name="general"></a>Általános kérdések

K. Mi az az Azure Synapse?

A. Az Azure szinapszis egy elemzési szolgáltatás, amely egyesíti az adattárházat és a Big adatelemzéseket. Az Azure szinapszis ezt a két világot közösen ötvözi a BI-és gépi tanulási igényeknek megfelelő adatgyűjtéssel,-előkészítéssel,-kezeléssel és-kiszolgálással kapcsolatos egységes tapasztalattal. További információ: [Mi az az Azure szinapszis Analytics](sql-data-warehouse-overview-what-is.md).

K. Mi történt a Azure SQL Data Warehouse?

A. Az Azure szinapszis Azure SQL Data Warehouse fejlődött. Ugyanezt az piacvezető adattárházat készítettük a teljesítmény és a képességek egész új szintjére. Továbbra is futtathatja meglévő adattárház-számítási feladatait éles környezetben a dedikált SQL-készlettel (korábban SQL DW) az Azure Szinapszisban. További információ: [Mi az az Azure szinapszis Analytics](sql-data-warehouse-overview-what-is.md).

K. Mi a dedikált SQL-készlet (korábban SQL DW) az Azure szinapszis Analyticsben?

A. A dedikált SQL-készlet (korábbi nevén SQL DW) az Azure szinapszis szolgáltatással általánosan elérhető vállalati adattárház-funkciókra utal. További információ: [Mi az az Azure szinapszis Analytics](sql-data-warehouse-overview-what-is.md).

K. Hogyan az Azure szinapszis megkezdése?

A. További információért forduljon az [ingyenes Azure-fiókhoz](https://azure.microsoft.com/free/sql-data-warehouse/) , vagy [vegye fel a kapcsolatot az értékesítéssel](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html).

K. Mit kínál az Azure szinapszis az adatbiztonsághoz?

A. Az Azure szinapszis számos megoldást kínál az adatok védelmére, például a TDE és a naplózásra. További információ: [Biztonság](sql-data-warehouse-overview-manage-security.md).

K. Honnan tudhatom meg, hogy az Azure szinapszis milyen jogi vagy üzleti szabványoknak felel meg?

A. Tekintse meg a [Microsoft megfelelőségi](https://www.microsoft.com/trustcenter/compliance/complianceofferings) oldalát, ahol különböző megfelelőségi ajánlatokat talál, például a SoC és az ISO terméket. Először válassza a megfelelőségi cím lehetőséget. Ezután bontsa ki az Azure-t az oldal jobb oldalán található Microsoft-hatókörű Cloud Services szakaszban, ahol megtekintheti, hogy mely szolgáltatások az Azure szinapszis-kompatibilisek.

K. Csatlakozhatok Power BIhoz?

A. Igen! Habár Power BI támogatja az Azure szinapszis közvetlen lekérdezését, nem sok felhasználónak vagy valós idejű adatmennyiségnek a célja. A Power BI teljesítményének további optimalizálása érdekében érdemes lehet Power BIt használni a Azure Analysis Services vagy az Analysis Service IaaS.

K. Mi a dedikált SQL-készlet (korábban az SQL DW) kapacitásának korlátai?

A. Tekintse meg a jelenlegi [Kapacitási korlátok](sql-data-warehouse-service-capacity-limits.md) lapot.

K. Miért van a méretezési/szüneteltetési vagy folytatási művelet?

A. Több tényező is befolyásolhatja a számítási felügyeleti műveletek idejét. A hosszú ideig futó műveletek esetében gyakori eset a tranzakciós visszaállítás. Ha egy méretezési vagy szüneteltetési műveletet kezdeményeznek, az összes bejövő munkamenet le lesz tiltva, és a rendszer leállítja a lekérdezéseket. Ahhoz, hogy a rendszer stabil állapotban maradjon, a tranzakciókat vissza kell állítani a művelet megkezdése előtt. Minél nagyobb a szám, és nagyobb a tranzakciók naplózási mérete, annál hosszabb a művelet, mert a rendszer stabil állapotba állítja vissza a műveletet.

## <a name="user-support"></a>Felhasználói támogatás

K. Van egy szolgáltatási kérésem, hová tudom elküldeni?

A. Ha van szolgáltatási kérése, küldje el a [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) oldalon

K. Hogyan tehetek x-t?

A. Ha segítségre van szüksége az Azure Szinapszistal való fejlesztéshez, kérdéseket tehet fel [stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) oldalon.

K. Hogyan támogatási jegyet küldeni?

A. A [támogatási jegyeket](sql-data-warehouse-get-started-create-support-ticket.md) Azure Portalon keresztül lehet benyújtani.

## <a name="sql-languagefeature-support"></a>SQL nyelv/funkció támogatása

K. Milyen adattípusok támogatottak?

A. Lásd  [az adattípusokat](sql-data-warehouse-tables-data-types.md).

K. Milyen funkciókat támogat?

A. Számos funkció támogatott. A nem támogatott funkciók a nem támogatott [tábla-funkciókban](sql-data-warehouse-tables-data-types.md)találhatók.

## <a name="tooling-and-administration"></a>Eszközök és felügyelet

K. Támogatja a dedikált SQL-készlet (korábbi nevén SQL DW) a REST API-kat?

A. Igen. A SQL Database használható legtöbb REST-funkció a dedikált SQL-készlettel (korábban SQL DW) is elérhető. A REST dokumentációs oldalain vagy [adatbázisaiban](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)találhat API-információkat.

## <a name="loading"></a>Betöltés

K. Milyen ügyfél-illesztőprogramokat támogat?

A. A dedikált SQL-készlet (korábban SQL DW) illesztőprogram-támogatása a [kapcsolatok karakterláncok](sql-data-warehouse-connection-strings.md) oldalán található.

K: milyen fájlformátumokat támogat a Base?

A: ork, RC, Parquet és laposan tagolt szöveg

K: milyen adatforrásokhoz csatlakozhatok a Base használatával?

A: [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) és [Azure Storage-Blobok](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

K: az Azure Storage-Blobokhoz vagy-ADLS való csatlakozáskor lehetséges a számítási pushdown?

A: nem, a csak a Storage-összetevőkkel kommunikáló

K: csatlakozhatok a HDI szolgáltatáshoz?

A: a HDI a ADLS vagy a WASB is használhatja a HDFS rétegként. Ha a HDFS réteggel rendelkezik, ezeket az adatait egy dedikált SQL-készletbe (korábban SQL DW) is betöltheti. Azonban nem hozhatja pushdown számítást a HDI-példányra.

## <a name="next-steps"></a>Következő lépések

További információ a dedikált SQL-készletről (korábbi nevén SQL DW) az Azure Szinapszisban: [Áttekintés](sql-data-warehouse-overview-what-is.md) oldal.
