---
title: Adatfolyamok
description: Az adatfolyamok áttekintése a Azure Synapse Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 4769cc8abe121625f3bf77785cd681c0f649d166
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567689"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Adatfolyamok a Azure Synapse Analytics

## <a name="what-are-data-flows"></a>Mik azok az adatfolyamok?

Az adatfolyamok vizuálisan megtervezett adatátalakítások a Azure Synapse Analytics. Az adatfolyamokkal az adatmérnökök kódírás nélkül fejleszthet adatátalakítási logikát. Az eredményül kapott adatfolyamok tevékenységekként vannak végrehajtva Azure Synapse Analytics, amelyek horizontálisan felskálált Apache Spark használják. Az adatfolyam-tevékenységek a meglévő ütemezési, Azure Synapse Analytics, folyam- és monitorozási képességek használatával is működőképesek.

Az adatfolyamok teljes mértékben vizuális élményt biztosítanak kódolás nélkül. Az adatfolyamok a Synapse által felügyelt végrehajtási fürtökön futnak a horizontálisan felskálázható adatfeldolgozás érdekében. Azure Synapse Analytics az adatfolyam-feladatok összes kódfordítását, útvonaloptimalizálását és végrehajtását.

## <a name="getting-started"></a>Első lépések

Az adatfolyamokat a Synapse Studio Fejlesztés panelje alapján lehet létrehozni. Adatfolyam létrehozásához válassza a Develop (Fejlesztés) melletti pluszjelet, majd a **Adatfolyam.** 

![Új adatfolyam](media/data-flow/new-data-flow.png)

Ez a művelet az adatfolyamvászonra viszi, ahol létrehozhatja az átalakítási logikát. A **forrásátalakítás konfigurálásához** válassza a Forrás hozzáadása lehetőséget. További információ: [Forrásátalakítás.](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="authoring-data-flows"></a>Adatfolyamok írása

Az adatfolyam egyedi szerzői vásznon teszi egyszerűvé az átalakítási logika építését. Az adatfolyam-vászon három részből áll: a felső sávból, a diagramból és a konfigurációs panelből. 

![Képernyőkép az adatfolyamvászonról, a felső sáv, a gráf és a konfigurációs panel címkével.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Graph

A gráf megjeleníti az átalakítási streamet. Megjeleníti a forrásadatok leszárazását, amikor egy vagy több fogadóba áramlik. Új forrás hozzáadásához válassza a **Forrás hozzáadása lehetőséget.** Új átalakítás hozzáadásához válassza a meglévő átalakítás jobb alsó részén lévő pluszjelet. További információ az [adatfolyam-diagram kezeléséhez.](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

![A vászon gráf részének képernyőképe a Keresés szövegmezővel.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Konfigurációs panel

A konfigurációs panel az aktuálisan kiválasztott átalakításra vonatkozó beállításokat jeleníti meg. Ha nincs kiválasztva átalakítás, az adatfolyamot jeleníti meg. Az adatfolyam teljes konfigurációjában paramétereket adhat hozzá a **Paraméterek lapon.** További információ: [Adatfolyam-paraméterek.](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

Minden átalakítás legalább négy konfigurációs lapot tartalmaz.

#### <a name="transformation-settings"></a>Átalakítási beállítások

Az egyes átalakítások konfigurációs panelének első lapja tartalmazza az átalakításra vonatkozó beállításokat. További információt az átalakítás dokumentációs oldalán talál.

![Forrásbeállítások lap](media/data-flow/source-1.png)

#### <a name="optimize"></a>Optimalizálás

Az **Optimalizálás** lap a particionálási sémák konfigurálási beállításait tartalmazza. Az adatfolyamok optimalizálásával kapcsolatos további információkért tekintse meg az adatfolyam-teljesítményre [vonatkozó leképezési útmutatót.](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

![Az Optimalizálás lapot bemutató képernyőkép](media/data-flow/optimize.png)

#### <a name="inspect"></a>Ellenőrizni

Az **Inspect** (Vizsgálat) lapon megtekintheti az átalakított adatfolyam metaadatait. Láthatja az oszlopok számát, a módosított oszlopokat, a hozzáadott oszlopokat, az adattípusokat, az oszlop sorrendjét és az oszlophivatkozásokat. **Az Inspect** a metaadatok írásható nézete. Nem kell engedélyeznie a hibakeresési módot, hogy a metaadatok láthatóak legyen a Vizsgálat **panelen.**

![Vizsgálat lap](media/data-flow/inspect.png)

Amikor átalakításokkal módosítja az adatok alakját, a metaadatok változásainak folyamatát az Inspect (Vizsgálat) **panelen láthatja.** Ha a forrásátalakítás nem tartalmaz meghatározott sémát, akkor a metaadatok nem lesznek láthatók a Vizsgálat **panelen.** A metaadatok hiánya gyakori a sémasodródási forgatókönyvekben.

#### <a name="data-preview"></a>Adatelőnézet

Ha a hibakeresési mód be van kapcsolva, az **Adatelőnézet** lapon interaktív pillanatképet készíthet az egyes átalakítások adatairól. További információ: [Adatelőnézet hibakeresési módban.](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview)

### <a name="top-bar"></a>Felső sáv

A felső sáv olyan műveleteket tartalmaz, amelyek a teljes adatfolyamot érintik, például érvényesítési és hibakeresési beállításokat. Megtekintheti az átalakítási logika mögöttes JSON-kódját és adatfolyam-szkriptét is.

## <a name="available-transformations"></a>Elérhető átalakítások

Az elérhető [átalakítások listájának](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) megtekintéséhez tekintse meg az adatfolyam-leképezés átalakításának áttekintését.

## <a name="data-flow-activity"></a>Adatfolyam-tevékenység

Az adatfolyamok az adatfolyam-tevékenység Azure Synapse Analytics belül vannak [használatban.](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) A felhasználónak csak azt kell megadnia, hogy melyik integrációskörnyezetet kell használnia, és paraméterértékeket kell megadnia. További információkért olvassa el az Azure integration [runtime -t.](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime)

## <a name="debug-mode"></a>Hibakeresési mód

A hibakeresési mód lehetővé teszi, hogy interaktívan tekintse meg az egyes átalakítási lépés eredményeit az adatfolyamok buildlése és hibakeresése során. A hibakeresési munkamenet az adatfolyam-logika kiépítésekor és a folyamat hibakeresési futtatásakor is használható adatfolyam-tevékenységekkel együtt. További tudnivalókért tekintse meg a [hibakeresési mód dokumentációját.](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="monitoring-data-flows"></a>Adatfolyamok figyelése

Az adatfolyam integrálható a meglévő Azure Synapse Analytics monitorozási képességekkel. Az adatfolyam-monitorozás kimenetének megismerése a leképezési [adatfolyamok monitorozásával kapcsolatos cikkből ad információt.](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

Az Azure Synapse Analytics csapat létrehozott egy [](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) teljesítményhangolási útmutatót, amely segít optimalizálni az adatfolyamok végrehajtási idejét az üzleti logika létrehozása után.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan hozhat létre [forrásátalakítást.](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* Ismerje meg, hogyan építhet ki adatfolyamokat [hibakeresési módban.](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
