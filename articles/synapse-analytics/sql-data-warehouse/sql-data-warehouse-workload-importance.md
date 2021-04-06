---
title: Számítási feladat fontossága
description: Útmutató a dedikált SQL Pool-lekérdezések fontosságának beállításához az Azure szinapszis Analyticsben.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 06d1957d182f2cabc336afcfc47a790442a3cb9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678406"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Az Azure szinapszis Analytics számítási feladatának fontossága

Ez a cikk azt ismerteti, hogy a számítási feladatok fontossága milyen hatással lehet a dedikált SQL Pool-kérelmek végrehajtásának sorrendjére az Azure Szinapszisban.

## <a name="importance"></a>Fontosság

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Az üzleti igényeknek az adattárházas számítási feladatoknál nagyobb jelentőséggel kell rendelkezniük, mint mások.  Vegyünk egy olyan forgatókönyvet, amelyben a kritikus értékesítési adat betöltődik a pénzügyi időszak lezárása előtt.  Az egyéb forrásokhoz, például időjárási értékekhez tartozó adatterhelések nem rendelkeznek szigorú SLA-val. Az értékesítési adatforgalom betöltésére irányuló kérések nagy fontosságának beállítása, valamint az időjárási információk betöltésére irányuló kérések alacsony fontossága biztosítja, hogy az értékesítési adatterhelés első alkalommal hozzáférjen az erőforrásokhoz, és gyorsabban befejeződjön.

## <a name="importance-levels"></a>Fontossági szintek

A fontosságnak öt szintje van: alacsony, below_normal, normál, above_normal és magas.  Azok a kérések, amelyek nem határozzák meg a fontosságot, a normál alapértelmezett szintet kapják meg. Az azonos fontossági szintű kérelmek esetében ugyanaz az ütemezési viselkedés, amely ma már létezik.

## <a name="importance-scenarios"></a>Fontossági helyzetek

Az értékesítési és időjárási adatokkal kapcsolatos alapvető fontossági forgatókönyvön túl más forgatókönyvek is vannak, amelyekben a számítási feladatok fontossága segíti az adatfeldolgozási és-lekérdezési igények kielégítését.

### <a name="locking"></a>Zárolás

Az olvasási és írási tevékenységek zárolásának elérése a természetes tartalom egyik területe. A [partíciók váltását](sql-data-warehouse-tables-partition.md) vagy [átnevezését](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) igénylő tevékenységek esetében emelt szintű zárolásra van szükség.  A számítási feladatok fontossága nélkül az Azure szinapszis dedikált SQL-készlete optimalizálja az átviteli sebességet. Az adatátviteli teljesítmény optimalizálása azt jelenti, hogy ha a futtatott és a várólistára helyezett kérelmek azonos zárolási igényekkel és erőforrásokkal rendelkeznek, az üzenetsor-kezelési kérelmek megkerülhetik a kérések várólistáján megjelenő, magasabb zárolási igényekkel rendelkező kérelmeket. Ha a számítási feladatok fontossága nagyobb a zárolási igényeket kielégítő kérelmek esetében. Az alacsonyabb fontosságú kérelem előtt a rendszer a nagyobb jelentőségű kérést fogja futtatni.

Tekintse meg a következő példát:

- A Q1 aktívan fut, és kiválasztja az SalesFact-ből származó adatok közül.
- A Q2 várólistára várakozik, amíg a Q1 befejeződik.  A szolgáltatás 9 órakor lett elküldve, és az új adatváltást a SalesFact-be kísérli meg.
- A Q3 a következő címen érhető el: 01am, és szeretné kijelölni a SalesFact adatait.

Ha a Q2 és a Q3 is ugyanolyan fontossággal bír, és a Q1 még mindig végrehajtja a végrehajtást, akkor a Q3 megkezdődik. A Q2 továbbra is várni fogja a SalesFact kizárólagos zárolását.  Ha a Q2 nagyobb jelentőséggel bír a Q3nál, a Q3 megvárja, amíg a Q2 be nem fejeződik a végrehajtás megkezdése előtt.

### <a name="non-uniform-requests"></a>Nem egységes kérelmek

Egy másik forgatókönyv, ahol a fontosság segíthet a lekérdezési igények kielégítésében, ha különböző erőforrás-osztályokkal rendelkező kérelmeket küld el.  Ahogy azt korábban említettük, ugyanilyen fontos, hogy az Azure szinapszis dedikált SQL-készlete optimalizálja az átviteli sebességet. Ha a vegyes méretre vonatkozó kérések (például a smallrc vagy a mediumrc) várólistán vannak, a dedikált SQL-készlet kiválasztja az elérhető erőforrásokon belüli legkorábbi érkező kéréseket. Ha a számítási feladatok fontosságát alkalmazza, a rendszer a legnagyobb jelentőségű kérést ütemezi.
  
Vegye figyelembe a következő példát a DW500c:

- A Q1, Q2, Q3 és Q4 smallrc-lekérdezéseket futtatnak.
- A (z) Q5 a mediumrc erőforrás-osztállyal van elküldve 9 órakor.
- A K6 a következő címen érhető el: smallrc Resource osztály, 9.01am.

Mivel a Q5 mediumrc, két párhuzamossági tárolóhelyre van szükség. A Q5-nek várnia kell, hogy a futó lekérdezések közül kettő befejeződjön.  Ha azonban az egyik futó lekérdezés (Q1-Q4) befejeződik, a K6 azonnal ütemezve van, mert az erőforrások a lekérdezés végrehajtásához léteznek.  Ha a Q5 nagyobb jelentőséggel bír, mint K6, a K6 addig vár, amíg az Q5 nem fut a végrehajtás megkezdése előtt.

## <a name="next-steps"></a>Következő lépések

- Az osztályozó létrehozásával kapcsolatos további információkért lásd a [munkaterhelés-osztályozó létrehozása (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)című témakört.  
- A számítási feladatok besorolásával kapcsolatos további információkért lásd: [munkaterhelés besorolása](sql-data-warehouse-workload-classification.md).  
- A számítási feladatok besorolásának létrehozásához tekintse meg a gyors üzembe helyezési adatok [létrehozása](quickstart-create-a-workload-classifier-tsql.md) című témakört.
- Tekintse meg az útmutatókat a számítási [feladatok fontosságának konfigurálásához](sql-data-warehouse-how-to-configure-workload-importance.md) , valamint a számítási [feladatok felügyeletének kezeléséhez és figyeléséhez](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- A lekérdezéseket és a hozzárendelt fontosságot a [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) oldalon tekintheti meg.
