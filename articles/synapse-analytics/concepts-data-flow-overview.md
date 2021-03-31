---
title: Adatfolyamok
description: Az Azure szinapszis Analytics szolgáltatásban található adatfolyamatok áttekintése
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 18322afbdca94b12ef142f6e37c4d2a22170436b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592625"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Adatforgalom az Azure szinapszis Analytics szolgáltatásban

## <a name="what-are-data-flows"></a>Mik azok az adatforgalom?

Az adatfolyamatok vizuálisan tervezett adatátalakítások az Azure szinapszis Analyticsben. Az adatforgalom lehetővé teszi, hogy az adatmérnökök programkód írása nélkül fejlesszenek Adatátalakítási logikát. Az eredményül kapott adatfolyamatok olyan Azure szinapszis Analytics-folyamatokban végrehajtott tevékenységek, amelyek kibővített Apache Spark fürtöket használnak. Az adatfolyam-tevékenységek a meglévő Azure szinapszis Analytics-ütemezési,-ellenőrzési,-folyamati és-figyelési funkciókkal is működőképesek.

Az adatfolyamatok teljes körű vizuális élményt biztosítanak, és nincs szükség kódolásra. Az adatfolyamatok a szinapszis által felügyelt végrehajtási fürtökön futnak a kibővíthető adatfeldolgozáshoz. Az Azure szinapszis Analytics az adatfolyam-feladatok összes fordítását, elérési útjának optimalizálását és végrehajtását kezeli.

## <a name="getting-started"></a>Első lépések

Az adatfolyamatok a szinapszis Studio fejlesztés paneljéről jönnek létre. Az adatfolyamatok létrehozásához válassza ki a **fejlesztés** melletti pluszjelet, majd válassza az **adatfolyam** lehetőséget. 

![Új adatfolyam](media/data-flow/new-data-flow.png)

Ez a művelet végigvezeti az adatáramlási vászonon, ahol létrehozhatja az átalakítási logikát. A forrás-átalakítás konfigurálásának megkezdéséhez válassza a **forrás hozzáadása** lehetőséget. További információ: forrás- [átalakítás](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="authoring-data-flows"></a>Adatfolyamatok készítése

Az adatfolyamok egyedi szerzői vásznon lettek kialakítva, hogy könnyen kiépíthető legyen az átalakítási logika. Az adatfolyam-vászon három részből áll: a felső sáv, a gráf és a konfigurációs panel. 

![Képernyőfelvétel: az adatáramlási vászon a felső sáv, a gráf és a konfigurációs panel feliratú.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Graph

A gráf megjeleníti az átalakítási adatfolyamot. Megjeleníti a forrásadatok vonalát, mivel az egy vagy több mosogatóba áramlik. Új forrás hozzáadásához válassza a **forrás hozzáadása** elemet. Új átalakítás hozzáadásához válassza a meglévő átalakítás jobb alsó sarkában látható plusz jelre. További információ [az adatfolyam-gráf kezeléséről](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![A képernyőfelvétel a vászon Graph részét jeleníti meg egy keresési szövegmezővel.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Konfigurációs panel

A konfigurációs panel megjeleníti az aktuálisan kiválasztott átalakításhoz tartozó beállításokat. Ha nincs kiválasztva átalakítás, az adatfolyamatot jeleníti meg. A teljes adatfolyam-konfigurációban paramétereket adhat hozzá a **Parameters (paraméterek** ) lapon. További információ: adatfolyam- [Paraméterek](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Minden átalakítás legalább négy konfigurációs lapot tartalmaz.

#### <a name="transformation-settings"></a>Átalakítási beállítások

Az egyes átalakítások konfigurációs paneljének első lapja az adott átalakításra vonatkozó beállításokat tartalmazza. További információ: az átalakítás dokumentációs lapja.

![Forrás beállításai lap](media/data-flow/source-1.png)

#### <a name="optimize"></a>Optimalizálás

Az **optimalizálás** lap a particionálási sémák konfigurálásához szükséges beállításokat tartalmazza. Az adatfolyamatok optimalizálásával kapcsolatos további tudnivalókért tekintse meg az [adatfolyamok teljesítményének feltérképezése című útmutatót](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Képernyőfelvétel az optimalizálás lapon](media/data-flow/optimize.png)

#### <a name="inspect"></a>Vizsgálata

Az **ellenőrzés** lapon megtekintheti az átalakítás alatt álló adatfolyam metaadatait. Megtekintheti az oszlopok számát, a megváltoztatott oszlopokat, a hozzáadott oszlopokat, az adattípusokat, az oszlopok sorrendjét és az oszlopok hivatkozásait. A **vizsgálat** a metaadatok csak olvasható nézete. Nem kell engedélyezni a hibakeresési módot a metaadatok megjelenítéséhez a **vizsgálat** ablaktáblán.

![Vizsgálat lap](media/data-flow/inspect.png)

Amikor átalakításokon keresztül módosítja az adatok alakját, a metaadatok változásai a **vizsgálat** panelen jelennek meg. Ha nincs definiált séma a forrás-átalakításban, akkor a metaadatok nem lesznek láthatók a **vizsgálat** ablaktáblán. A metaadatok hiánya gyakori a séma-drift forgatókönyvekben.

#### <a name="data-preview"></a>Adatelőnézet

Ha a hibakeresési mód be van kapcsolva, az **adatelőnézet** lap interaktív pillanatképet biztosít az adatokról az egyes transzformációk során. További információ: az [adatelőnézet hibakeresési módban](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview).

### <a name="top-bar"></a>Felső sáv

A felső sáv olyan műveleteket tartalmaz, amelyek befolyásolják a teljes adatforgalmat, például az érvényesítési és a hibakeresési beállításokat. Az átalakítási logikában megtekintheti a mögöttes JSON-kódot és adatfolyam-parancsfájlt is.

## <a name="available-transformations"></a>Elérhető átalakítások

Az elérhető átalakítások listájának megtekintéséhez tekintse meg a [leképezési adatfolyam-átalakítás áttekintését](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="data-flow-activity"></a>Adatfolyam-tevékenység

Az adatfolyamatok az Azure szinapszis Analytics-folyamatokon belül működnek az [adatfolyam tevékenységgel](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Az összes felhasználónak meg kell határoznia, hogy melyik integrációs modult használja, és adja át a paraméter értékeit. További információ: az [Azure Integration Runtime](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime)ismertetése.

## <a name="debug-mode"></a>Hibakeresési mód

A hibakeresési mód lehetővé teszi az egyes átalakítási lépések eredményének interaktív megjelenítését az adatfolyamatok létrehozásakor és hibakeresése során. A hibakeresési munkamenet az adatfolyam-logika összeállításakor és az adatfolyamatok hibakeresésének futtatásakor is használható az adatfolyam-tevékenységekkel. További információt a [hibakeresési mód dokumentációjában](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)talál.

## <a name="monitoring-data-flows"></a>Adatfolyamatok figyelése

Az adatfolyam integrálható a meglévő Azure szinapszis Analytics-figyelési képességekkel. Az adatfolyam-figyelés kimenetének megismeréséhez lásd: a [leképezési adatfolyamatok figyelése](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Az Azure szinapszis Analytics csapata egy teljesítmény- [hangolási útmutatót](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) hozott létre, amellyel optimalizálhatja az Adatáramlások végrehajtási idejét az üzleti logikája kiépítése után.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan hozhat létre [forrás-átalakítást](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Megtudhatja, hogyan hozhat létre adatfolyamatokat [hibakeresési módban](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
