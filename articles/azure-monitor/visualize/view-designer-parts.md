---
title: A Azure Monitor View Designer részeire mutató útmutató Microsoft Docs
description: A Azure Monitorban megjelenő tervező használatával létrehozhat olyan egyéni nézeteket, amelyek megjelennek a Azure Portalban, és a Log Analytics munkaterületen lévő adatvizualizációk különböző vizualizációit is tartalmazhatják. Ez a cikk az egyéni nézetekben elérhető vizualizációs részek beállításait ismerteti.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 6e971db01322fc031dd0fa8abe82f76a5b45d256
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045258"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Útmutató a Designer vizualizációs részeinek megtekintéséhez Azure Monitor
A Azure Monitor Tervező nézetében számos egyéni nézetet hozhat létre a Azure Portalban, amelyek segíthetnek a Log Analytics munkaterületen lévő adatmegjelenítésben. Ez a cikk az egyéni nézetekben elérhető vizualizációs részek beállításait ismerteti.

További információ a tervező nézetéről:

* [Tervező megtekintése](view-designer.md): áttekintést nyújt az egyéni nézetek létrehozásához és szerkesztéséhez szükséges tervezőről és eljárásokról.
* [Csempe leírása](view-designer-tiles.md): az egyéni nézetekben található egyes elérhető csempék beállításaira mutató hivatkozást tartalmaz.


A következő táblázat ismerteti az elérhető View Designer csempék típusait:

| Nézet típusa | Leírás |
|:--- |:--- |
| [Lekérdezések listája](#list-of-queries-part) |Megjeleníti a naplózott lekérdezések listáját. Az egyes lekérdezéseket kiválaszthatja az eredmények megjelenítéséhez. |
| [Szám és lista](#number-and-list-part) |A fejléc egyetlen számot jelenít meg, amely egy napló lekérdezésében lévő rekordok számát jeleníti meg. A lista a lekérdezés első tíz eredményét jeleníti meg egy gráfmal, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi. |
| [Két szám és lista](#two-numbers-and-list-part) |A fejléc két olyan számot jelenít meg, amely megjeleníti a rekordok számát a különböző naplókból származó lekérdezésekből. A lista a lekérdezés első tíz eredményét jeleníti meg egy gráfmal, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi. |
| [Fánk és lista](#donut-and-list-part) |A fejléc egyetlen számot jelenít meg, amely összegzi a log lekérdezés Value oszlopát. A fánk grafikusan jeleníti meg az első három rekord eredményeit. |
| [Két idősor és lista](#two-timelines-and-list-part) |A fejléc a két naplózási lekérdezés eredményét jeleníti meg az oszlopok során, és olyan ábrafeliratot tartalmaz, amely egyetlen számot jelenít meg egy log lekérdezésben szereplő Value oszlop összegzéséhez. A lista a lekérdezés első tíz eredményét jeleníti meg egy gráfmal, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi. |
| [Tájékoztatás](#information-part) |A fejlécben statikus szöveg és opcionális hivatkozás látható. A lista egy vagy több, statikus címmel és szöveggel rendelkező elemet jelenít meg. |
| [Vonalas diagram, ábrafelirat és lista](#line-chart-callout-and-list-part) |A fejléc egy diagramot jelenít meg, amely egyszerre több adatsorozatot tartalmaz, és egy összesített értéket tartalmazó ábrafeliratot tartalmaz. A lista a lekérdezés első tíz eredményét jeleníti meg egy gráfmal, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi. |
| [Diagram és lista](#line-chart-and-list-part) |A fejléc egy diagramot jelenít meg, amely egyszerre több adatsorozatot tartalmaz. A lista a lekérdezés első tíz eredményét jeleníti meg egy gráfmal, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi. |
| [Line Charts – rész](#stack-of-line-charts-part) |Három külön vonalas diagramot jelenít meg, több adatsorozatot pedig az idő múlásával. |

A következő szakaszok részletesen ismertetik a csempék típusát és azok tulajdonságait.

> [!NOTE]
> A nézetek részei a Log Analytics munkaterületen található [naplókon](../logs/log-query-overview.md) alapulnak. Jelenleg nem támogatják a [több erőforrás lekérdezését](../logs/cross-workspace-query.md) az adatok Application Insightsból való lekéréséhez.

## <a name="list-of-queries-part"></a>Lekérdezések részének listája
A lekérdezések listája a naplók listáját jeleníti meg. Az egyes lekérdezéseket kiválaszthatja az eredmények megjelenítéséhez. A nézet alapértelmezés szerint egyetlen lekérdezést tartalmaz, és további lekérdezések hozzáadásához kiválaszthatja a **+ lekérdezés** lehetőséget is.

![Képernyőkép a lekérdezési vizualizációk listájáról a Azure Monitor View Designerben.](media/view-designer-parts/view-list-queries.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Cím |A nézet tetején megjelenő szöveg |
| Új csoport |Válassza ezt a hivatkozást, ha új csoportot szeretne létrehozni a nézetben, az aktuális nézettől kezdődően. |
| Előre kiválasztott szűrők |A bal oldali szűrő ablaktáblán a lekérdezés kiválasztásakor szerepeltetni kívánt tulajdonságok vesszővel tagolt listája. |
| Renderelési mód |A lekérdezés kiválasztásakor megjelenő kezdeti nézet. A lekérdezés megnyitása után bármelyik elérhető nézetet kiválaszthatja. |
| **Lekérdezések** | |
| Keresési lekérdezés |A futtatandó lekérdezés. |
| Felhasználóbarát név | A megjelenített leíró név. |

## <a name="number-and-list-part"></a>Szám és lista része
A fejléc egyetlen számot jelenít meg, amely egy napló lekérdezésében lévő rekordok számát jeleníti meg. A lista a lekérdezés első tíz eredményét jeleníti meg egy gráfmal, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi.

![Képernyőkép a Azure Monitor View Designerben lévő szám és lista vizualizációs részeiről.](media/view-designer-parts/view-number-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A nézet tetején megjelenő szöveg |
| Új csoport |Válassza ezt a hivatkozást, ha új csoportot szeretne létrehozni a nézetben, az aktuális nézettől kezdődően. |
| Ikon |A fejlécben az eredmény mellett megjelenő képfájl. |
| Ikon használata |Válassza ezt a hivatkozást az ikon megjelenítéséhez. |
| **Cím** | |
| Jelmagyarázat |A fejléc tetején megjelenő szöveg |
| Lekérdezés |A fejléchez futtatandó lekérdezés Megjelenik a lekérdezés által visszaadott rekordok száma. |
| Kattintás – Navigálás | A fejlécre való kattintáskor végrehajtott művelet.  További információ: [Common Settings](#click-through-navigation). |
| **Lista** | |
| Lekérdezés |A listához futtatandó lekérdezés. Az eredményekben az első tíz rekord első két tulajdonsága jelenik meg. Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus érték. A sávokat a rendszer automatikusan hozza létre, amelyek a numerikus oszlop relatív értékén alapulnak.<br><br>A `Sort` lekérdezésben szereplő parancs használatával rendezze a rekordokat a listában. A lekérdezés futtatásához és az összes rekord visszaadásához kattintson **az összes** megjelenítése lehetőségre. |
| Gráf elrejtése |Válassza ezt a hivatkozást a numerikus oszlop jobb oldalán található gráf letiltásához. |
| Értékgörbék engedélyezése |Válassza ezt a hivatkozást, ha vízszintes sáv helyett értékgörbék megjelenítését szeretné megjeleníteni. További információ: [Common Settings](#sparklines). |
| Szín |A sávok vagy értékgörbék színe |
| Név és érték elválasztója |A Text tulajdonság több értékre való elemzéséhez használt egykarakteres határolójel. További információ: [Common Settings](#sparklines). |
| Kattintás – Navigálás | A lista egyik elemére kattintva végrehajtott művelet.  További információ: [Common Settings](#click-through-navigation). |
| **Lista** |**> oszlopok címei** |
| Name |Az első oszlop tetején megjelenő szöveg |
| Érték |A második oszlop tetején megjelenő szöveg |
| **Lista** |**> küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ezt a hivatkozást a küszöbértékek engedélyezéséhez. További információ: [Common Settings](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Két szám és lista része
A fejlécnek két száma van, amely a rekordok számát jeleníti meg a különböző naplók lekérdezései között. A lista a lekérdezés első tíz eredményét jeleníti meg egy gráfmal, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi.

![Két szám & listanézet](media/view-designer-parts/view-two-numbers-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A nézet tetején megjelenő szöveg |
| Új csoport |Válassza ezt a hivatkozást, ha új csoportot szeretne létrehozni a nézetben, az aktuális nézettől kezdődően. |
| Ikon |A fejlécben az eredmény mellett megjelenő képfájl. |
| Ikon használata |Válassza ezt a hivatkozást az ikon megjelenítéséhez. |
| **Cím navigációja** | |
| Kattintás – Navigálás | A fejlécre való kattintáskor végrehajtott művelet.  További információ: [Common Settings](#click-through-navigation). |
| **Cím** | |
| Jelmagyarázat |A fejléc tetején megjelenő szöveg |
| Lekérdezés |A fejléchez futtatandó lekérdezés Megjelenik a lekérdezés által visszaadott rekordok száma. |
| **Lista** | |
| Lekérdezés |A listához futtatandó lekérdezés. Az eredményekben az első tíz rekord első két tulajdonsága jelenik meg. Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus érték. A sávokat a rendszer automatikusan hozza létre a numerikus oszlop relatív értéke alapján.<br><br>A `Sort` lekérdezésben szereplő parancs használatával rendezze a rekordokat a listában. A lekérdezés futtatásához és az összes rekord visszaadásához kattintson **az összes** megjelenítése lehetőségre. |
| Gráf elrejtése |Válassza ezt a hivatkozást a numerikus oszlop jobb oldalán található gráf letiltásához. |
| Értékgörbék engedélyezése |Válassza ezt a hivatkozást, ha vízszintes sáv helyett értékgörbék megjelenítését szeretné megjeleníteni. További információ: [Common Settings](#sparklines). |
| Szín |A sávok vagy értékgörbék színe |
| Művelet |Az értékgörbe számára végrehajtandó művelet. További információ: [Common Settings](#sparklines). |
| Név és érték elválasztója |A Text tulajdonság több értékre való elemzéséhez használt egykarakteres határolójel. További információ: [Common Settings](#sparklines). |
| Kattintás – Navigálás | A lista egyik elemére kattintva végrehajtott művelet.  További információ: [Common Settings](#click-through-navigation). |
| **Lista** |**> oszlopok címei** |
| Name |Az első oszlop tetején megjelenő szöveg |
| Érték |A második oszlop tetején megjelenő szöveg |
| **Lista** |**> küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ezt a hivatkozást a küszöbértékek engedélyezéséhez. További információ: [Common Settings](#thresholds). |

## <a name="donut-and-list-part"></a>Fánk és lista része
A fejléc egyetlen számot jelenít meg, amely összegzi a log lekérdezés Value oszlopát. A fánk grafikusan jeleníti meg az első három rekord eredményeit.

![Fánk és listanézet](media/view-designer-parts/view-donut-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg |
| Új csoport |Válassza ezt a hivatkozást, ha új csoportot szeretne létrehozni a nézetben, az aktuális nézettől kezdődően. |
| Ikon |A fejlécben az eredmény mellett megjelenő képfájl. |
| Ikon használata |Válassza ezt a hivatkozást az ikon megjelenítéséhez. |
| **Fejléc** | |
| Cím |A fejléc tetején megjelenő szöveg |
| Alcím |A fejléc tetején található cím alatt megjelenő szöveg |
| **Gyűrű** | |
| Lekérdezés |A fánkhoz futtatandó lekérdezés Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus érték. |
| Kattintás – Navigálás | A fejlécre való kattintáskor végrehajtott művelet.  További információ: [Common Settings](#click-through-navigation). |
| **Gyűrű** |**> központ** |
| Szöveg |A fánkon belüli érték alatt megjelenő szöveg |
| Művelet |Az Value tulajdonságon végrehajtandó művelet, amely egyetlen értékként összegzi azt.<ul><li>Sum: az összes rekord értékét hozzáadja.</li><li>Százalék: az eredmények által visszaadott rekordok aránya a **középső műveletben** a lekérdezésben szereplő összes rekordban.</li></ul> |
| A középső műveletben használt eredmények értékei |Ha egy vagy több értéket szeretne hozzáadni, válassza a pluszjelet (+). A lekérdezés eredményei csak a megadott tulajdonságértékek rekordjaira korlátozódnak. Ha nincs hozzáadott érték, az összes rekord belekerül a lekérdezésbe. |
| **További beállítások** |**> színek** |
| 1. szín<br>2. szín<br>3. szín |Válassza ki a fánkban megjelenő értékek színét. |
| **További beállítások** |**> speciális színleképezés** |
| Mező értéke |Írja be annak a mezőnek a nevét, amelyet más színnel kell megjeleníteni, ha az szerepel a fánkban. |
| Szín |Válassza ki az egyedi mező színét. |
| **Lista** | |
| Lekérdezés |A listához futtatandó lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| Gráf elrejtése |Válassza ezt a hivatkozást a numerikus oszlop jobb oldalán található gráf letiltásához. |
| Értékgörbék engedélyezése |Válassza ezt a hivatkozást, ha vízszintes sáv helyett értékgörbék megjelenítését szeretné megjeleníteni. További információ: [Common Settings](#sparklines). |
| Szín |A sávok vagy értékgörbék színe |
| Művelet |Az értékgörbe számára végrehajtandó művelet. További információ: [Common Settings](#sparklines). |
| Név és érték elválasztója |A Text tulajdonság több értékre való elemzéséhez használt egykarakteres határolójel. További információ: [Common Settings](#sparklines). |
| Kattintás – Navigálás | A lista egyik elemére kattintva végrehajtott művelet.  További információ: [Common Settings](#click-through-navigation). |
| **Lista** |**> oszlopok címei** |
| Name |Az első oszlop tetején megjelenő szöveg |
| Érték |A második oszlop tetején megjelenő szöveg |
| **Lista** |**> küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ezt a hivatkozást a küszöbértékek engedélyezéséhez. További információ: [Common Settings](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Két idősor és lista
A fejléc a két naplózási lekérdezés eredményét jeleníti meg az oszlopok során, és olyan ábrafeliratot tartalmaz, amely egyetlen számot jelenít meg egy log lekérdezésben szereplő Value oszlop összegzéséhez. A lista a lekérdezés első tíz eredményét jeleníti meg egy gráfmal, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi.

![Két idősor és listanézet](media/view-designer-parts/view-two-timelines-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg |
| Új csoport |Válassza ezt a hivatkozást, ha új csoportot szeretne létrehozni a nézetben, az aktuális nézettől kezdődően. |
| Ikon |A fejlécben az eredmény mellett megjelenő képfájl. |
| Ikon használata |Válassza ezt a hivatkozást az ikon megjelenítéséhez. |
| **Cím navigációja** | |
| Kattintás – Navigálás | A fejlécre való kattintáskor végrehajtott művelet.  További információ: [Common Settings](#click-through-navigation). |
| **Első diagram <br> második diagramja** | |
| Jelmagyarázat |Az első adatsorozat ábrafelirata alatt megjelenő szöveg |
| Szín |Az adatsorozat oszlopaihoz használandó szín |
| Lekérdezés |Az első adatsorozathoz futtatandó lekérdezés Az egyes időintervallumokban lévő rekordok számát a diagram oszlopai jelölik. |
| Művelet |Az érték tulajdonságon végrehajtandó művelet, amely egyetlen értékként összegzi a képfeliratot.<ul><li>Sum: az összes rekord értékének összege.</li><li>Average (átlag): az összes rekord értékének átlaga.</li><li>Utolsó minta: a diagramon szereplő utolsó intervallum értéke.</li><li>Első minta: a diagramon szereplő első intervallum értéke.</li><li>Darabszám: a lekérdezés által visszaadott összes rekord száma.</li></ul> |
| **Lista** | |
| Lekérdezés |A listához futtatandó lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| Gráf elrejtése |Válassza ezt a hivatkozást a numerikus oszlop jobb oldalán található gráf letiltásához. |
| Értékgörbék engedélyezése |Válassza ezt a hivatkozást, ha vízszintes sáv helyett értékgörbék megjelenítését szeretné megjeleníteni. További információ: [Common Settings](#sparklines). |
| Szín |A sávok vagy értékgörbék színe |
| Művelet |Az értékgörbe számára végrehajtandó művelet. További információ: [Common Settings](#sparklines). |
| Kattintás – Navigálás | A lista egyik elemére kattintva végrehajtott művelet.  További információ: [Common Settings](#click-through-navigation). |
| **Lista** |**> oszlopok címei** |
| Name |Az első oszlop tetején megjelenő szöveg |
| Érték |A második oszlop tetején megjelenő szöveg |
| **Lista** |**> küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ezt a hivatkozást a küszöbértékek engedélyezéséhez. További információ: [Common Settings](#thresholds). |

## <a name="information-part"></a>Információs rész
A fejlécben statikus szöveg és opcionális hivatkozás látható. A lista egy vagy több, statikus címmel és szöveggel rendelkező elemet jelenít meg.

![Információ nézet](media/view-designer-parts/view-information.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg |
| Új csoport |Válassza ezt a hivatkozást, ha új csoportot szeretne létrehozni a nézetben, az aktuális nézettől kezdődően. |
| Szín |A fejléc háttérszíne |
| **Fejléc** | |
| Kép |A fejlécben megjelenő képfájl. |
| Címke |A fejlécben megjelenített szöveg |
| **Fejléc** |**> hivatkozás** |
| Címke |A hivatkozás szövege |
| URL-cím |A hivatkozás URL-címe. |
| **Információs elemek** | |
| Cím |Az egyes elemek címéhez megjelenített szöveg |
| Tartalom |Az egyes elemeknél megjelenített szöveg |

## <a name="line-chart-callout-and-list-part"></a>Vonal diagram, képfelirat és lista része
A fejlécben egy olyan vonalas diagram látható, amely több adatsorozatból áll, és egy összesített értéket tartalmazó ábrafeliratot tartalmaz. A lista a lekérdezés első tíz eredményét jeleníti meg egy gráfmal, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi.

![Vonalas diagram, ábrafelirat és listanézet](media/view-designer-parts/view-line-chart-callout-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg |
| Új csoport |Válassza ezt a hivatkozást, ha új csoportot szeretne létrehozni a nézetben, az aktuális nézettől kezdődően. |
| Ikon |A fejlécben az eredmény mellett megjelenő képfájl. |
| Ikon használata |Válassza ezt a hivatkozást az ikon megjelenítéséhez. |
| **Fejléc** | |
| Cím |A fejléc tetején megjelenő szöveg |
| Alcím |A fejléc tetején található cím alatt megjelenő szöveg |
| **Vonalas diagram** | |
| Lekérdezés |A vonalas diagramon futtatandó lekérdezés Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus érték. A lekérdezés általában a *mérték* kulcsszó használatával összegzi az eredményeket. Ha a lekérdezés az *intervallum* kulcsszót használja, a diagram x tengelye ezt az időtartamot használja. Ha a lekérdezés nem tartalmazza az *intervallum* kulcsszót, az x tengely óránkénti időközöket használ. |
| Kattintás – Navigálás | A fejlécre való kattintáskor végrehajtott művelet.  További információ: [Common Settings](#click-through-navigation). |
| **Vonalas diagram** |**> ábrafelirat** |
| Képfelirat címe |A képfelirat értéke felett megjelenő szöveg |
| Adatsorozat neve |A képfelirat értékéhez használandó adatsorozat tulajdonságának értéke. Ha nincs megadva adatsorozat, a rendszer a lekérdezés összes rekordját használja. |
| Művelet |Az érték tulajdonságon végrehajtandó művelet, amely egyetlen értékként összegzi a képfeliratot.<ul><li>Average (átlag): az összes rekord értékének átlaga.</li><li>Darabszám: a lekérdezés által visszaadott összes rekord száma.</li><li>Utolsó minta: a diagramon szereplő utolsó intervallum értéke.</li><li>Max: a diagramban szereplő intervallumok maximális értéke.</li><li>Min: a diagramban szereplő intervallumok minimális értéke.</li><li>Sum: az összes rekord értékének összege.</li></ul> |
| **Vonalas diagram** |**> Y tengely** |
| Logaritmikus skála használata |Válassza ezt a hivatkozást, ha logaritmikus méretezést szeretne használni az y tengelyhez. |
| Egység |Adja meg a lekérdezés által visszaadott értékek egységeit. Ezek az adatok az értékek típusát jelző, és opcionálisan az értékek átalakítására szolgáló diagram-címkék megjelenítésére szolgálnak. Az *egység* típusa határozza meg az egység kategóriáját, és meghatározza a rendelkezésre álló *aktuális egység* típusú értékeket. Ha a *Konvertálás a* értékre lehetőséget választja, a rendszer a numerikus értékeket az *aktuális egység* típusról a *Konvertálás* típusra konvertálja. |
| Egyéni címke |Az y tengelyen megjelenített szöveg az *egység* típusához tartozó címke mellett. Ha nincs megadva címke, csak az *egység* típusa jelenik meg. |
| **Lista** | |
| Lekérdezés |A listához futtatandó lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| Gráf elrejtése |Válassza ezt a hivatkozást a numerikus oszlop jobb oldalán található gráf letiltásához. |
| Értékgörbék engedélyezése |Válassza ezt a hivatkozást, ha vízszintes sáv helyett értékgörbék megjelenítését szeretné megjeleníteni. További információ: [Common Settings](#sparklines). |
| Szín |A sávok vagy értékgörbék színe |
| Művelet |Az értékgörbe számára végrehajtandó művelet. További információ: [Common Settings](#sparklines). |
| Név és érték elválasztója |A Text tulajdonság több értékre való elemzéséhez használt egykarakteres határolójel. További információ: [Common Settings](#sparklines). |
| Kattintás – Navigálás | A lista egyik elemére kattintva végrehajtott művelet.  További információ: [Common Settings](#click-through-navigation). |
| **Lista** |**> oszlopok címei** |
| Name |Az első oszlop tetején megjelenő szöveg |
| Érték |A második oszlop tetején megjelenő szöveg |
| **Lista** |**> küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ezt a hivatkozást a küszöbértékek engedélyezéséhez. További információ: [Common Settings](#thresholds). |

## <a name="line-chart-and-list-part"></a>Vonal diagram és lista része
A fejléc több adatsorozatot tartalmazó vonalas diagramot jelenít meg a napló lekérdezésével egyidejűleg. A lista a lekérdezés első tíz eredményét jeleníti meg egy gráfmal, amely egy numerikus oszlop relatív értékét vagy időbeli változását jelzi.

![Vonal diagram és listanézet](media/view-designer-parts/view-line-chart-callout-list.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg |
| Új csoport |Válassza ezt a hivatkozást, ha új csoportot szeretne létrehozni a nézetben, az aktuális nézettől kezdődően. |
| Ikon |A fejlécben az eredmény mellett megjelenő képfájl. |
| Ikon használata |Válassza ezt a hivatkozást az ikon megjelenítéséhez. |
| **Fejléc** | |
| Cím |A fejléc tetején megjelenő szöveg |
| Alcím |A fejléc tetején található cím alatt megjelenő szöveg |
| **Vonalas diagram** | |
| Lekérdezés |A vonalas diagramon futtatandó lekérdezés Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus érték. A lekérdezés általában a *mérték* kulcsszó használatával összegzi az eredményeket. Ha a lekérdezés az *intervallum* kulcsszót használja, a diagram x tengelye ezt az időtartamot használja. Ha a lekérdezés nem tartalmazza az *intervallum* kulcsszót, az x tengely óránkénti időközöket használ. |
| Kattintás – Navigálás | A fejlécre való kattintáskor végrehajtott művelet.  További információ: [Common Settings](#click-through-navigation). |
| **Vonalas diagram** |**> Y tengely** |
| Logaritmikus skála használata |Válassza ezt a hivatkozást, ha logaritmikus méretezést szeretne használni az y tengelyhez. |
| Egység |Adja meg a lekérdezés által visszaadott értékek egységeit. Ezek az adatok az értékek típusát jelző, és opcionálisan az értékek átalakítására szolgáló diagram-címkék megjelenítésére szolgálnak. Az *egység* típusa határozza meg az egység kategóriáját, és meghatározza a rendelkezésre álló *aktuális egység* típusú értékeket. Ha a *Konvertálás a* értékre lehetőséget választja, a rendszer a numerikus értékeket az *aktuális egység* típusról a *Konvertálás* típusra konvertálja. |
| Egyéni címke |Az y tengelyen megjelenített szöveg az *egység* típusához tartozó címke mellett. Ha nincs megadva címke, csak az *egység* típusa jelenik meg. |
| **Lista** | |
| Lekérdezés |A listához futtatandó lekérdezés. Megjelenik a lekérdezés által visszaadott rekordok száma. |
| Gráf elrejtése |Válassza ezt a hivatkozást a numerikus oszlop jobb oldalán található gráf letiltásához. |
| Értékgörbék engedélyezése |Válassza ezt a hivatkozást, ha vízszintes sáv helyett értékgörbék megjelenítését szeretné megjeleníteni. További információ: [Common Settings](#sparklines). |
| Szín |A sávok vagy értékgörbék színe |
| Művelet |Az értékgörbe számára végrehajtandó művelet. További információ: [Common Settings](#sparklines). |
| Név és érték elválasztója |A Text tulajdonság több értékre való elemzéséhez használt egykarakteres határolójel. További információ: [Common Settings](#sparklines). |
| Kattintás – Navigálás | A lista egyik elemére kattintva végrehajtott művelet.  További információ: [Common Settings](#click-through-navigation). |
| **Lista** |**> oszlopok címei** |
| Name |Az első oszlop tetején megjelenő szöveg |
| Érték |A második oszlop tetején megjelenő szöveg |
| **Lista** |**> küszöbértékek** |
| Küszöbértékek engedélyezése |Válassza ezt a hivatkozást a küszöbértékek engedélyezéséhez. További információ: [Common Settings](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Line Charts – rész
A sor diagramja három különálló diagramot jelenít meg, több adatsorozatot pedig egy napló lekérdezésből az idő múlásával, ahogy az itt látható:

![Vonalas diagramok verem](media/view-designer-parts/view-stack-line-charts.png)

| Beállítás | Leírás |
|:--- |:--- |
| **Általános** | |
| Csoport címe |A csempe tetején megjelenő szöveg |
| Új csoport |Válassza ezt a hivatkozást, ha új csoportot szeretne létrehozni a nézetben, az aktuális nézettől kezdődően. |
| Ikon |A fejlécben az eredmény mellett megjelenő képfájl. |
| **1. diagram, 2. ábra <br> <br> 3. diagram** |**> fejléc** |
| Cím |A diagram tetején megjelenő szöveg |
| Alcím |A diagram tetején található cím alatt megjelenő szöveg |
| **1. diagram, 2. ábra <br> <br> 3. diagram** |**Vonalas diagram** |
| Lekérdezés |A vonalas diagramon futtatandó lekérdezés Az első tulajdonság egy szöveges érték, a második tulajdonság pedig egy numerikus érték. A lekérdezés általában a *mérték* kulcsszó használatával összegzi az eredményeket. Ha a lekérdezés az *intervallum* kulcsszót használja, a diagram x tengelye ezt az időtartamot használja. Ha a lekérdezés nem tartalmazza az *intervallum* kulcsszót, az x tengely óránkénti időközöket használ. |
| Kattintás – Navigálás | A fejlécre való kattintáskor végrehajtott művelet.  További információ: [Common Settings](#click-through-navigation). |
| **Diagram** |**> Y tengely** |
| Logaritmikus skála használata |Válassza ezt a hivatkozást, ha logaritmikus méretezést szeretne használni az y tengelyhez. |
| Egység |Adja meg a lekérdezés által visszaadott értékek egységeit. Ezek az adatok az értékek típusát jelző, és opcionálisan az értékek átalakítására szolgáló diagram-címkék megjelenítésére szolgálnak. Az *egység* típusa határozza meg az egység kategóriáját, és meghatározza a rendelkezésre álló *aktuális egység* típusú értékeket. Ha a *Konvertálás a* értékre lehetőséget választja, a rendszer a numerikus értékeket az *aktuális egység* típusról a *Konvertálás* típusra konvertálja. |
| Egyéni címke |Az y tengelyen megjelenített szöveg az *egység* típusához tartozó címke mellett. Ha nincs megadva címke, csak az *egység* típusa jelenik meg. |

## <a name="common-settings"></a>Általános beállítások
A következő szakaszok a vizualizációs részek közös beállításait ismertetik.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Név és érték elválasztója
A név és az érték elválasztója az egykarakteres határolójel, amellyel a Text tulajdonságot több értékre elemezheti a lista lekérdezésében. Ha elválasztót ad meg, akkor az egyes mezők nevét megadhatja a **név** mezőben megegyező elválasztó karakterrel.

Vegyünk például egy *hely* nevű tulajdonságot, amely olyan értékeket tartalmaz, mint például a *Redmond-Building 41* és a *Bellevue-Building 12*. Megadhat egy kötőjelet (-) a név és az érték elválasztója és a *város létrehozása* számára. Ez a megközelítés az egyes értékeket a *City* és a *Building* nevű két tulajdonságra elemzi.

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Kattintás – Navigálás
Átkattintásos Navigálás meghatározza, hogy milyen műveletet hajtson végre a rendszer, amikor rákattint egy fejlécre vagy listaelemre egy nézetben.  Ekkor megnyit egy lekérdezést a [log Analytics](../logs/log-query-overview.md) , vagy egy másik nézetet indít el.

A következő táblázat a kattintásra navigáció beállításait ismerteti.

| Beállítás           | Leírás |
|:--|:--|
| Naplók keresése (automatikus) | A fejléc-elem kiválasztásakor futtatandó naplózási lekérdezés.  Ez ugyanaz a log-lekérdezés, amelyre az elem alapul.
| Naplók keresése        | A listában szereplő elemek kiválasztásakor futtatandó naplózási lekérdezés.  Írja be a lekérdezést a **navigációs lekérdezés** mezőbe.   A *{kiválasztott Item}* használatával adja meg a felhasználó által kiválasztott tétel szintaxisát.  Ha például a lekérdezés egy *számítógép* nevű oszloppal rendelkezik, és a navigációs lekérdezés *{kiválasztott elem}*, a számítógép kiválasztásakor egy lekérdezés, például a *Computer = "Sajátgép"* fut. Ha a navigációs lekérdezés *típusa = Event {kiválasztott elem}*, a lekérdezés *típusa = Event Computer = "Sajátgép"* fut. |
| Nézet              | A megnyitható nézet, amikor kijelöl egy fejléc elemet vagy egy listaelemet a listában.  A nézet **neve** mezőben válassza ki a nézet nevét a munkaterületen. |



### <a name="sparklines"></a><a name="sparklines"></a>Értékgörbék
Az értékgörbe egy kis vonalas diagram, amely egy listaelem értékének időbeli alakulását szemlélteti. Egy listával rendelkező vizualizációs részeknél kiválaszthatja, hogy megjelenjen-e egy vízszintes sáv, amely egy numerikus oszlop vagy egy értékgörbe relatív értékét jelzi, amely az érték időbeli alakulását jelzi.

Az alábbi táblázat az értékgörbék beállításait ismerteti:

| Beállítás | Leírás |
|:--- |:--- |
| Értékgörbék engedélyezése |Válassza ezt a hivatkozást, ha vízszintes sáv helyett értékgörbék megjelenítését szeretné megjeleníteni. |
| Művelet |Ha az értékgörbék engedélyezve vannak, akkor ez a művelet a lista egyes tulajdonságain végezhető el az értékgörbe értékeinek kiszámításához.<ul><li>Utolsó minta: az adatsorozat utolsó értéke az adott időszakban.</li><li>Max: az adatsorozat maximális értéke az időintervallumon belül.</li><li>Min: az adatsorozat minimális értéke az időintervallumon belül.</li><li>Sum: az adatsorozat értékeinek összege az adott időszakban.</li><li>Összefoglalás: ugyanazt a `measure` parancsot használja, mint a fejlécben található lekérdezés.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Küszöbértékek
A küszöbértékek használatával a lista egyes elemei mellett egy színes ikon is megjeleníthető. A küszöbértékek olyan elemek gyors megjelenítését jelzik, amelyek meghaladják az adott értéket, vagy egy adott tartományon belül esnek. Megjelenítheti például a zöld ikont a megfelelő értékkel rendelkező elemekhez, sárga értéket, ha az érték egy figyelmeztetést jelző tartományon belül van, és piros, ha meghaladja a hibaértéket.

Ha egy rész küszöbértékeit engedélyezi, meg kell adnia egy vagy több küszöbértéket. Ha egy elem értéke nagyobb, mint a küszöbérték, és a következő küszöbértéknél kisebb, akkor a rendszer az adott érték színét használja. Ha az elem nagyobb, mint a legmagasabb küszöbérték, a rendszer egy másik színt használ. 

Mindegyik küszöbérték egy küszöbértéket tartalmaz, és az **alapértelmezett** érték van megadva. Ez az a szín, amely akkor van beállítva, ha nincs más érték túllépve. A küszöbértékek hozzáadásához vagy eltávolításához kattintson a **Hozzáadás** (+) vagy a **Törlés** (x) gombra.

A következő táblázat a küszöbértékek beállításait ismerteti:

| Beállítás | Leírás |
|:--- |:--- |
| Küszöbértékek engedélyezése |Válassza ezt a hivatkozást, ha egy szín ikont szeretne megjeleníteni az egyes értékek bal oldalán. Az ikon jelzi az érték állapotát a megadott küszöbértékekhez viszonyítva. |
| Name |A küszöb értékének neve |
| Küszöbérték |A küszöbérték értéke. Az egyes listaelemek állapotának színe az adott értéknél nagyobb legmagasabb küszöbérték színét adja meg. Ha nem lépi túl a küszöbértéket, a rendszer egy alapértelmezett színt használ. |
| Szín |A küszöbértéket jelző szín |

## <a name="next-steps"></a>Következő lépések
* További információ a vizualizációs részekben található lekérdezések támogatásához használható [naplók lekérdezéséről](../logs/log-query-overview.md) .