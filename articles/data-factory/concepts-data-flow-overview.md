---
title: Adatfolyamok leképezése
description: Adatfolyamok leképezésének áttekintése a Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/11/2021
ms.openlocfilehash: 26d119fb6880f4c539e0064db424699bc5d22f96
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478675"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Adatfolyamok leképezése a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>Mik azok a leképezési adatfolyamok?

A leképezési adatfolyamok vizuálisan megtervezett adatátalakítások a Azure Data Factory. Az adatfolyamokkal az adatmérnökök kódírás nélkül fejleszthet adatátalakítási logikát. Az eredményül kapott adatfolyamok tevékenységekként vannak végrehajtva Azure Data Factory, amelyek horizontálisan felskálált Apache Spark használják. Az adatfolyam-tevékenységek a meglévő ütemezési, Azure Data Factory, folyam- és monitorozási képességek használatával is működőképesek.

A leképezési adatfolyamok teljes mértékben vizuális élményt biztosítanak kódolás nélkül. Az adatfolyamok ADF által felügyelt végrehajtási fürtökön futnak a horizontálisan felskálált adatfeldolgozás érdekében. Azure Data Factory az adatfolyam-feladatok összes kódfordítását, útvonaloptimalizálását és végrehajtását kezeli.

## <a name="getting-started"></a>Első lépések

Az adatfolyamok a gyári erőforrások paneljére, például folyamatokra és adatkészletekre vannak létrehozva. Adatfolyam létrehozásához válassza a Gyári erőforrások melletti pluszjelet, majd a **Adatfolyam.** 

![Új adatfolyam](media/data-flow/new-data-flow.png)

Ez a művelet az adatfolyamvászonra viszi, ahol létrehozhatja az átalakítási logikát. A **forrásátalakítás konfigurálásához** válassza a Forrás hozzáadása lehetőséget. További információ: [Forrásátalakítás.](data-flow-source.md)

## <a name="authoring-data-flows&quot;></a>Adatfolyamok írása

A leképezési adatfolyam egyedi szerzői vásznon teszi egyszerűvé az átalakítási logika építését. Az adatfolyam-vászon három részből áll: a felső sávból, a diagramból és a konfigurációs panelből. 

![Képernyőkép az adatfolyamvászonról, a felső sáv, a gráf és a konfigurációs panel címkével.](media/data-flow/canvas-1.png &quot;Vászon")

### <a name="graph"></a>Graph

A gráf megjeleníti az átalakítási streamet. Megjeleníti a forrásadatok leszárazását, amikor egy vagy több fogadóba áramlik. Új forrás hozzáadásához válassza a **Forrás hozzáadása lehetőséget.** Új átalakítás hozzáadásához válassza a meglévő átalakítás jobb alsó részén lévő pluszjelet. További információ az [adatfolyam-diagram kezelésről.](concepts-data-flow-manage-graph.md)

![Képernyőkép a vászon gráf részről a Keresés szövegmezővel.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Konfigurációs panel

A konfigurációs panel az aktuálisan kiválasztott átalakításra vonatkozó beállításokat jeleníti meg. Ha nincs kiválasztva átalakítás, az adatfolyamot jeleníti meg. Az adatfolyam teljes konfigurációjában paramétereket adhat hozzá a **Paraméterek lapon.** További információ: [Adatfolyam-paraméterek leképezése.](parameters-data-flow.md)

Minden átalakítás legalább négy konfigurációs lapot tartalmaz.

#### <a name="transformation-settings"></a>Átalakítási beállítások

Az egyes átalakítások konfigurációs panelének első lapja az adott átalakításra vonatkozó beállításokat tartalmazza. További információt az átalakítás dokumentációs oldalán talál.

![Forrásbeállítások lap](media/data-flow/source1.png "Forrásbeállítások lap")

#### <a name="optimize"></a>Optimalizálás

Az **Optimalizálás** lap a particionálási sémák konfigurálási beállításait tartalmazza. Az adatfolyamok optimalizálásával kapcsolatos további információkért tekintse meg az adatfolyam-leképezési [teljesítményre vonatkozó útmutatót.](concepts-data-flow-performance.md)

![Képernyőkép az Optimalizálás lapról, amely a Partíció lehetőséget, a Partíciótípust és a Partíciók számát tartalmazza.](media/data-flow/optimize.png)

#### <a name="inspect"></a>Ellenőrizni

Az **Inspect** (Vizsgálat) lapon megtekintheti az átalakított adatfolyam metaadatait. Láthatja az oszlopok számát, a módosított oszlopokat, a hozzáadott oszlopokat, az adattípusokat, az oszlop sorrendjét és az oszlophivatkozásokat. **Az Inspect** (Vizsgálat) a metaadatok csak olvasható nézete. Nem kell engedélyeznie a hibakeresési módot, hogy a metaadatok láthatóak legyen a Vizsgálat **panelen.**

![Ellenőrizni](media/data-flow/inspect1.png "Ellenőrizni")

Amikor átalakításokkal módosítja az adatok alakját, a metaadatok változásainak folyamatát a Vizsgálat **panelen láthatja.** Ha a forrásátalakítás nem tartalmaz meghatározott sémát, akkor a metaadatok nem lesznek láthatók a Vizsgálat **panelen.** A metaadatok hiánya gyakori a sémasodródási forgatókönyvekben.

#### <a name="data-preview"></a>Adatelőnézet

Ha a hibakeresési mód be van kapcsolva, az **Adatelőnézet** lapon interaktív pillanatképet készíthet az egyes átalakítások adatairól. További információ: [Adatelőnézet hibakeresési módban.](concepts-data-flow-debug-mode.md#data-preview)

### <a name="top-bar"></a>Felső sáv

A felső sáv olyan műveleteket tartalmaz, amelyek a teljes adatfolyamot érintik, például a mentést és az érvényesítést. Megtekintheti az átalakítási logika mögöttes JSON-kódját és adatfolyam-szkriptét is. További információ az [adatfolyam-szkriptről.](data-flow-script.md)

## <a name="available-transformations"></a>Elérhető átalakítások

Az elérhető [átalakítások listájának](data-flow-transformation-overview.md) megtekintéséhez tekintse meg az adatfolyam-leképezés átalakításának áttekintését.

## <a name="data-flow-data-types"></a>Adatfolyam-adattípusok

tömb bináris logikai összetett decimális dátum lebegőpontos szám hosszú térkép rövid sztring időbélyege

## <a name="data-flow-activity"></a>Adatfolyam-tevékenység

A leképezési adatfolyamok az adatfolyam-tevékenységgel vannak működőképesek az [ADF-folyamatokban.](control-flow-execute-data-flow-activity.md) A felhasználónak csak azt kell megadnia, hogy melyik integrációskörnyezetet kell használnia, és paraméterértékeket kell megadnia. További információkért olvassa el az Azure integration [runtime -t.](concepts-integration-runtime.md#azure-integration-runtime)

## <a name="debug-mode"></a>Hibakeresési mód

A hibakeresési mód lehetővé teszi, hogy interaktívan tekintse meg az egyes átalakítási lépés eredményeit az adatfolyamok buildlése és hibakeresése során. A hibakeresési munkamenet az adatfolyam-logika kiépítésekor és a folyamat hibakeresési futtatásakor is használható adatfolyam-tevékenységekkel. További tudnivalókért tekintse meg a [hibakeresési mód dokumentációját.](concepts-data-flow-debug-mode.md)

## <a name="monitoring-data-flows"></a>Adatfolyamok figyelése

A leképezési adatfolyam integrálható a meglévő Azure Data Factory monitorozási képességekkel. Az adatfolyam-monitorozás kimenetének megismerése a leképezési [adatfolyamok monitorozásával kapcsolatos cikkből ad információt.](concepts-data-flow-monitoring.md)

Az Azure Data Factory csapat létrehozott egy [](concepts-data-flow-performance.md) teljesítményhangolási útmutatót, amely segít optimalizálni az adatfolyamok végrehajtási idejét az üzleti logika létrehozása után.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan hozhat létre [forrásátalakítást.](data-flow-source.md)
* Ismerje meg, hogyan építhet ki adatfolyamokat [hibakeresési módban.](concepts-data-flow-debug-mode.md)
