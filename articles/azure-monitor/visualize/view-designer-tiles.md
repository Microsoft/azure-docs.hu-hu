---
title: Útmutató a tervezői csempék Azure Monitorhoz való áttekintéséhez | Microsoft Docs
description: A Azure Monitorban megjelenő tervező használatával létrehozhat olyan egyéni nézeteket, amelyek megjelennek a Azure Portalban, és a Log Analytics munkaterületen lévő adatvizualizációk különböző vizualizációit is tartalmazhatják. Ez a cikk az egyéni nézetekben elérhető csempék beállításairól nyújt útmutatást.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: abcd95a0c433b59df6e7a564e5db948ea2a9eaf5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717213"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Útmutató a tervezői csempék Azure Monitor való megtekintéséhez
A Azure Monitor Tervező nézetében számos egyéni nézetet hozhat létre a Azure Portalban, amelyek segíthetnek a Log Analytics munkaterületen lévő adatmegjelenítésben. Ez a cikk az egyéni nézetekben elérhető csempék beállításairól nyújt útmutatást.

További információ a tervező nézetéről:

* [Tervező megtekintése](view-designer.md): áttekintést nyújt az egyéni nézetek létrehozásához és szerkesztéséhez szükséges tervezőről és eljárásokról.
* [Vizualizációs rész hivatkozása](view-designer-parts.md): útmutatást nyújt az egyéni nézetekben elérhető vizualizációs részek beállításaihoz.


A következő táblázat ismerteti az elérhető tervezői csempéket:  

| Csempe | Leírás |
|:--- |:--- |
| [Szám](#number-tile) |Egy lekérdezés rekordjainak száma. |
| [Két szám](#two-numbers-tile) |Két különböző lekérdezésből származó rekordok száma. |
| [Gyűrű](#donut-tile) | Egy lekérdezésen alapuló diagram, amely egy összegző értékkel rendelkezik a központban. |
| Vonalas diagram és ábrafelirat | Egy lekérdezésen alapuló vonalas diagram, valamint egy összesítő értéket tartalmazó ábrafelirat. |
| [Vonalas diagram](#line-chart-tile) |Egy lekérdezésen alapuló vonalas diagram. |
| [Két idősor](#two-timelines-tile) | Oszlopdiagram két sorozattal, amelyek mindegyike külön lekérdezésen alapul. |

A következő szakaszok részletesen ismertetik a csempék típusát és azok tulajdonságait.

> [!NOTE]
> A nézetekben lévő csempék a Log Analytics munkaterületen található [naplókon](../logs/log-query-overview.md) alapulnak. Jelenleg nem támogatják a [több erőforrás lekérdezését](../logs/cross-workspace-query.md) az adatok Application Insightsból való lekéréséhez.

## <a name="number-tile"></a>Szám csempe
A **szám** csempe a rekordok számát jeleníti meg egy napló lekérdezésből és egy címkéből.

![Szám csempe](media/view-designer-tiles/tile-number.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg |
| Leírás |A csempe neve alatt megjelenő szöveg |
| **Csempe** | |
| Jelmagyarázat |Az érték alatt megjelenő szöveg |
| Lekérdezés |A futtatott lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| **Felsőfokú** |**Adatfolyam-ellenőrzés>** |
| Engedélyezve |Válassza ezt a hivatkozást, ha az adatfolyam-ellenőrzést engedélyezni kell a csempén. Ez a megközelítés egy másik üzenetet biztosít, ha az információ nem érhető el. Általában arra használja a megközelítést, hogy a nézet telepítésekor az ideiglenes időszakban üzenetet szolgáltasson, és az adatmennyiség elérhetővé válik. |
| Lekérdezés |A futtatott lekérdezés, amely meghatározza, hogy elérhetők-e az adatnézet. Ha a lekérdezés nem ad vissza eredményt, egy üzenet jelenik meg a fő lekérdezés értékének helyén. |
| Üzenet |Az az üzenet, amely akkor jelenik meg, ha az adatáramlás-ellenőrző lekérdezés nem ad vissza adatmennyiséget. Ha nem ad meg üzenetet, a rendszer *elvégez egy értékelési* állapotjelző üzenetet. |


## <a name="two-numbers-tile"></a>Két szám csempe
Ez a csempe a rekordok számát jeleníti meg két különböző naplózási lekérdezésből és egy címkéből.

![Két szám csempe](media/view-designer-tiles/tile-two-numbers.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg |
| Leírás |A csempe neve alatt megjelenő szöveg |
| **Első csempe** | |
| Jelmagyarázat |Az érték alatt megjelenő szöveg |
| Lekérdezés |A futtatott lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| **Második csempe** | |
| Jelmagyarázat |Az érték alatt megjelenő szöveg |
| Lekérdezés |A futtatott lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| **Felsőfokú** |**Adatfolyam-ellenőrzés>** |
| Engedélyezve |Válassza ezt a hivatkozást, ha az adatfolyam-ellenőrzést engedélyezni kell a csempén. Ez a megközelítés egy másik üzenetet biztosít, ha az információ nem érhető el. Általában arra használja a megközelítést, hogy a nézet telepítésekor az ideiglenes időszakban üzenetet szolgáltasson, és az adatmennyiség elérhetővé válik. |
| Lekérdezés |A futtatott lekérdezés, amely meghatározza, hogy elérhetők-e az adatnézet. Ha a lekérdezés nem ad vissza eredményt, egy üzenet jelenik meg a fő lekérdezés értékének helyén. |
| Üzenet |Az az üzenet, amely akkor jelenik meg, ha az adatáramlás-ellenőrző lekérdezés nem ad vissza adatmennyiséget. Ha nem ad meg üzenetet, a rendszer *elvégez egy értékelési* állapotjelző üzenetet. |


## <a name="donut-tile"></a>Fánk csempe
A **fánk** csempe egyetlen számot jelenít meg, amely összefoglalja a log lekérdezés Value oszlopát. A fánk grafikusan jeleníti meg az első három rekord eredményeit.

![Fánk csempe](media/view-designer-tiles/tile-donut.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg |
| Leírás |A csempe neve alatt megjelenő szöveg |
| **Gyűrű** | |
| Lekérdezés |A fánkhoz futtatott lekérdezés. Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus érték. A lekérdezés általában a *mérték* kulcsszó használatával összegzi az eredményeket. |
| **Gyűrű** |**> központ** |
| Szöveg |A fánkon belüli érték alatt megjelenő szöveg |
| Művelet |A Value tulajdonságon végrehajtott művelet, amely egyetlen értékként összegzi azt.<ul><li>Sum: az összes rekord értékeit adja hozzá a tulajdonság értékével.</li><li>Százalék: a rekordokból származó összegezett értékek százalékos aránya a tulajdonság értékével az összes rekord összefoglalt értékeihez képest.</li></ul> |
| A középső műveletben használt eredmények értékei |Ha egy vagy több értéket szeretne hozzáadni, válassza a pluszjelet (+). A lekérdezés eredményei csak a megadott tulajdonságértékek rekordjaira korlátozódnak. Ha nincs hozzáadott érték, az összes rekord belekerül a lekérdezésbe. |
| **Gyűrű** |**> további beállítások** |
| Színek |A három legfontosabb tulajdonsághoz megjelenített szín Ha alternatív színeket szeretne megadni az adott tulajdonságértékek számára, használjon *speciális színleképezést*. |
| Speciális színleképezés |Megjeleníti az adott tulajdonság értékeit jelölő színt. Ha a megadott érték a három legfontosabb, a normál szín helyett a helyettesítő szín jelenik meg. Ha a tulajdonság értéke nem az első három, a szín nem jelenik meg. |
| **Felsőfokú** |**Adatfolyam-ellenőrzés>** |
| Engedélyezve |Válassza ezt a hivatkozást, ha az adatfolyam-ellenőrzést engedélyezni kell a csempén. Ez a megközelítés egy másik üzenetet biztosít, ha az információ nem érhető el. Általában arra használja a megközelítést, hogy a nézet telepítésekor az ideiglenes időszakban üzenetet szolgáltasson, és az adatmennyiség elérhetővé válik. |
| Lekérdezés |A futtatott lekérdezés, amely meghatározza, hogy elérhetők-e az adatnézet. Ha a lekérdezés nem ad vissza eredményt, egy üzenet jelenik meg a fő lekérdezés értékének helyén. |
| Üzenet |Az az üzenet, amely akkor jelenik meg, ha az adatáramlás-ellenőrző lekérdezés nem ad vissza adatmennyiséget. Ha nem ad meg üzenetet, a rendszer *elvégez egy értékelési* állapotjelző üzenetet. |


## <a name="line-chart-tile"></a>Vonalas diagram csempe
Ez a csempe egy olyan vonalas diagram, amely egyszerre több adatsorozatot jelenít meg egy napló lekérdezésében. 

![Képernyőfelvétel a Azure Monitor View Designerben található vonalas diagram csempéről.](media/view-designer-tiles/tile-line-chart.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg |
| Leírás |A csempe neve alatt megjelenő szöveg |
| **Vonalas diagram** | |
| Lekérdezés |A vonalas diagramhoz futtatott lekérdezés Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus érték. A lekérdezés általában a *mérték* kulcsszó használatával összegzi az eredményeket. Ha a lekérdezés az *intervallum* kulcsszót használja, az x tengely ezt az időtartamot használja. Ha a lekérdezés nem használja az *intervallum* kulcsszót, az x tengely óránkénti időközöket használ. |
| **Vonalas diagram** |**> Y tengely** |
| Logaritmikus skála használata |Válassza ezt a hivatkozást, ha logaritmikus méretezést szeretne használni az y tengelyhez. |
| Egység |Adja meg a lekérdezés által visszaadott értékek egységeit. Ezek az adatok a diagramon lévő feliratok megjelenítésére szolgálnak, amelyek az értékeket és opcionálisan az értékek átalakítását jelzik. Az **egység típusa** határozza meg az egység kategóriáját, és meghatározza az **aktuálisan elérhető egység típusú** értékeket. Ha kijelöl egy értéket a **Konvertálás** értékre, akkor a rendszer a numerikus értékeket az **aktuális egység** típusról a **Konvertálás** típusra konvertálja. |
| Egyéni címke |Az y tengelyen megjelenített szöveg az *egység* típusához tartozó címke mellett. Ha nincs megadva címke, csak az *egység* típusa jelenik meg. |
| **Felsőfokú** |**Adatfolyam-ellenőrzés>** |
| Engedélyezve |Válassza ezt a hivatkozást, ha az adatfolyam-ellenőrzést engedélyezni kell a csempén. Ez a megközelítés egy másik üzenetet biztosít, ha az információ nem érhető el. Általában arra használja a megközelítést, hogy a nézet telepítésekor az ideiglenes időszakban üzenetet szolgáltasson, és az adatmennyiség elérhetővé válik. |
| Lekérdezés |A futtatott lekérdezés, amely meghatározza, hogy elérhetők-e az adatnézet. Ha a lekérdezés nem ad vissza eredményt, egy üzenet jelenik meg a fő lekérdezés értékének helyén. |
| Üzenet |Az az üzenet, amely akkor jelenik meg, ha az adatáramlás-ellenőrző lekérdezés nem ad vissza adatmennyiséget. Ha nem ad meg üzenetet, a rendszer *elvégez egy értékelési* állapotjelző üzenetet. |


## <a name="line-chart-and-callout-tile"></a>Vonalas diagram és ábrafelirat csempe
Ez a csempe egy olyan vonalas diagrammal is rendelkezik, amely egyszerre több adatsorozatot jelenít meg egy naplózási lekérdezésből, valamint egy összesített értéket tartalmazó ábrafeliratot. 

![Képernyőfelvétel egy vonalas diagramról és egy ábrafelirat csempéről a Azure Monitor View Designerben. az ábrafelirat egy összegzett értéket ábrázolva kibővíti a diagramot.](media/view-designer-tiles/tile-line-chart-callout.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg |
| Leírás |A csempe neve alatt megjelenő szöveg |
| **Vonalas diagram** | |
| Lekérdezés |A vonalas diagramhoz futtatott lekérdezés Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus érték. A lekérdezés általában a *mérték* kulcsszó használatával összegzi az eredményeket. Ha a lekérdezés az *intervallum* kulcsszót használja, az x tengely ezt az időtartamot használja. Ha a lekérdezés nem használja az *intervallum* kulcsszót, az x tengely óránkénti időközöket használ. |
| **Vonalas diagram** |**> ábrafelirat** |
| Képfelirat címe | A képfelirat értéke felett megjelenő szöveg |
| Adatsorozat neve |A képfelirat értékeként használandó sorozat-tulajdonság értéke. Ha nincs megadva adatsorozat, a rendszer a lekérdezés összes rekordját használja. |
| Művelet |A Value tulajdonságon végrehajtott művelet, amely egyetlen értékként összegzi a képfeliratot.<ul><li>Average (átlag): az összes rekord értékének átlaga.</li><li>Darabszám: a lekérdezés által visszaadott összes rekord száma.</li><li>Utolsó minta: a diagramon szereplő utolsó intervallum értéke.</li><li>Max: a diagramban található intervallumok maximális értéke.</li><li>Min: a diagramban szereplő intervallumok minimális értéke.</li><li>Sum: az összes rekord értékének összege.</li></ul> |
| **Vonalas diagram** |**> Y tengely** |
| Logaritmikus skála használata |Válassza ezt a hivatkozást, ha logaritmikus méretezést szeretne használni az y tengelyhez. |
| Egység |Adja meg a lekérdezés által visszaadott értékek egységeit. Ezek az adatok az értékek típusát jelző, és opcionálisan az értékek átalakítására szolgáló diagram-címkék megjelenítésére szolgálnak. Az *egység* típusa határozza meg az egység kategóriáját, és meghatározza a rendelkezésre álló *aktuális egység* típusú értékeket. Ha a *Konvertálás a* értékre lehetőséget választja, a rendszer a numerikus értékeket az *aktuális egység* típusról a *Konvertálás* típusra konvertálja. |
| Egyéni címke |Az y tengelyen megjelenített szöveg az *egység* típusához tartozó címke mellett. Ha nincs megadva címke, csak az *egység* típusa jelenik meg. |
| **Felsőfokú** |**Adatfolyam-ellenőrzés>** |
| Engedélyezve |Válassza ezt a hivatkozást, ha az adatfolyam-ellenőrzést engedélyezni kell a csempén. Ez a megközelítés egy másik üzenetet biztosít, ha az információ nem érhető el. Általában arra használja a megközelítést, hogy a nézet telepítésekor az ideiglenes időszakban üzenetet szolgáltasson, és az adatmennyiség elérhetővé válik. |
| Lekérdezés |A futtatott lekérdezés, amely meghatározza, hogy elérhetők-e az adatnézet. Ha a lekérdezés nem ad vissza eredményt, egy üzenet jelenik meg a fő lekérdezés értékének helyén. |
| Üzenet |Az az üzenet, amely akkor jelenik meg, ha az adatáramlás-ellenőrző lekérdezés nem ad vissza adatmennyiséget. Ha nem ad meg üzenetet, a rendszer *elvégez egy értékelési* állapotjelző üzenetet. |


## <a name="two-timelines-tile"></a>Két idősor csempe
A **két idővonal** csempe a két naplózási lekérdezés eredményét jeleníti meg az oszlopok időbeli diagramja szerint. Megjelenik egy ábrafelirat az egyes adatsorozatokhoz. 

![Két idősor csempe](media/view-designer-tiles/tile-two-timelines.png)

| Beállítás | Leírás |
|:--- |:--- |
| Név |A csempe tetején megjelenő szöveg |
| Leírás |A csempe neve alatt megjelenő szöveg |
| Első diagram | |
| Jelmagyarázat |Az első adatsorozat ábrafelirata alatt megjelenő szöveg |
| Szín |Az első adatsorozat oszlopaihoz használt szín |
| Diagram lekérdezése |Az első adatsorozathoz futtatott lekérdezés. Az egyes időintervallumokon belül a rekordok számát a diagram oszlopai jelölik. |
| Művelet |A Value tulajdonságon végrehajtott művelet, amely egyetlen értékként összegzi a képfeliratot.<ul><li>Average (átlag): az összes rekord értékének átlaga.</li><li>Darabszám: a lekérdezés által visszaadott összes rekord száma.</li><li>Utolsó minta: a diagramon szereplő utolsó intervallum értéke.</li><li>Max: a diagramban található intervallumok maximális értéke.</li></ul> |
| **Második diagram** | |
| Jelmagyarázat |A második adatsorozat ábrafelirata alatt megjelenő szöveg |
| Szín |A második adatsorozat oszlopaihoz használt szín |
| Diagram lekérdezése |A második adatsorozathoz futtatott lekérdezés. Az egyes időintervallumokon belül a rekordok számát a diagram oszlopai jelölik. |
| Művelet |A Value tulajdonságon végrehajtott művelet, amely egyetlen értékként összegzi a képfeliratot.<ul><li>Average (átlag): az összes rekord értékének átlaga.</li><li>Darabszám: a lekérdezés által visszaadott összes rekord száma.</li><li>Utolsó minta: a diagramon szereplő utolsó intervallum értéke.</li><li>Max: a diagramban található intervallumok maximális értéke. |
| **Felsőfokú** |**Adatfolyam-ellenőrzés>** |
| Engedélyezve |Válassza ezt a hivatkozást, ha az adatfolyam-ellenőrzést engedélyezni kell a csempén. Ez a megközelítés egy másik üzenetet biztosít, ha az információ nem érhető el. Általában arra használja a megközelítést, hogy a nézet telepítésekor az ideiglenes időszakban üzenetet szolgáltasson, és az adatmennyiség elérhetővé válik. |
| Lekérdezés |A futtatott lekérdezés, amely meghatározza, hogy elérhetők-e az adatnézet. Ha a lekérdezés nem ad vissza eredményt, egy üzenet jelenik meg a fő lekérdezés értékének helyén. |
| Üzenet |Az az üzenet, amely akkor jelenik meg, ha az adatáramlás-ellenőrző lekérdezés nem ad vissza adatmennyiséget. Ha nem ad meg üzenetet, a rendszer *elvégez egy értékelési* állapotjelző üzenetet. |


## <a name="next-steps"></a>Következő lépések
* Útmutató a csempék lekérdezéseit támogató [naplók lekérdezéséhez](../logs/log-query-overview.md) .
* [Vizualizációs részek](view-designer-parts.md) hozzáadása az egyéni nézethez.