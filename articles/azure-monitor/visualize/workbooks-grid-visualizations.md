---
title: Azure Monitor munkafüzet-rácsok vizualizációi
description: Ismerkedjen meg az összes Azure Monitor munkafüzet Grid vizualizációval.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100615615"
---
# <a name="grid-visualizations"></a>Rácsvizualizációk

A rácsok vagy táblázatok a felhasználók számára történő adatmegjelenítés közös módja. A munkafüzetek lehetővé teszik a felhasználók számára a rács oszlopainak egyéni stílusát, hogy a jelentésekben gazdag felhasználói felületet nyújtsanak.

Az alábbi példa egy olyan rácsot mutat be, amely kombinálja az ikonokat, a intenzitástérképei és a Spark-sávokat a komplex információk megjelenítéséhez. A munkafüzet a rendezést, a keresőmezőt és a "to-Analytics" gombot is tartalmazza.

[![A log-alapú rács képernyőképe](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>Log-alapú rács hozzáadása

1. Az eszköztár **szerkesztése** elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A **lekérdezés hozzáadása** hivatkozásra kattintva hozzáadhat egy log lekérdezési vezérlőt a munkafüzethez.
3. Válassza ki a lekérdezés típusát **naplóként**, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. A lekérdezéstervező segítségével megadhatja az elemzés KQL (például egy küszöbérték alatti virtuális gépeket)
5. Vizualizáció beállítása **rácsra**
6. Szükség esetén más paramétereket is beállíthat, például időtartományt, méretet, színpalettát és jelmagyarázatot.

[![A log-alapú rács lekérdezésének képernyőképe](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>A naplózási diagram paraméterei

| Paraméter | Magyarázat | Példa |
| ------------- |:-------------|:-------------|
| `Query Type` | A használandó lekérdezés típusa. | Napló, Azure Resource Graph stb. |
| `Resource Type` | A célként megadott erőforrástípus. | Application Insights, Log Analytics vagy Azure – első |
| `Resources` | Erőforrások készlete, amelyből a metrikák értékét be kell olvasni. | MyApp1 |
| `Time Range` | A naplózási diagram megtekintésére szolgáló időablak. | Az elmúlt óra, az elmúlt 24 óra stb. |
| `Visualization` | A használni kívánt vizualizáció. | Rács |
| `Size` | A vezérlő függőleges mérete | Kicsi, közepes, nagy vagy teljes |
| `Query` | Bármely olyan KQL-lekérdezés, amely a diagram vizualizációja által várt formátumban adja vissza az adatok értékét. | _kérelmek \| Összefoglaló kérések = count () név szerint_ |

## <a name="simple-grid"></a>Egyszerű rács

A munkafüzetek egyszerű táblaként tehetik KQL az eredményeket. Az alábbi táblázat a kérelmek számát és az egyedi felhasználókat mutatja egy adott alkalmazásban.

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![Képernyőfelvétel egy log-alapú rácsról szerkesztési módban](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>Rács stílusa

Míg egy egyszerű táblázat az adathalmazokat jeleníti meg, nehezen olvasható, és az elemzések nem mindig lesznek láthatók. A rács stílusa megkönnyíti az információk olvasását és értelmezését.

Az alábbi táblázat az előző szakasz intenzitástérképei.

[![Képernyőkép egy log-alapú rácsról, amely a intenzitástérképei stílusú oszlopokat ábrázolja](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

Itt ugyanaz a rács stílusú sáv: képernyőkép a [ ![ log-alapú rácsról, amely a sávok stílusú oszlopokat](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox) ábrázolja

### <a name="styling-a-grid-column"></a>Rácsos oszlop stílusa

1. Válassza az **oszlop beállítása** gombot a lekérdezés vezérlőelem eszköztárán.
2. Az *oszlop szerkesztése beállításban* válassza ki az oszlopot a stílushoz.
3. Válassza ki az oszlop megjelenítését (például hő, sáv, sáv alatta stb.) és az oszlop stílusához kapcsolódó beállításokat.

Az alábbiakban látható egy példa, amely a *kérések* oszlopát sávként stílusként mutatja be:

[![Képernyőfelvétel a log-alapú rácsról sáv stílusú, a kérelem oszlopával.](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>Oszlopok renderelése

| Oszlop megjelenítő | Magyarázat | További beállítások |
|:------------- |:-------------|:-------------|
| `Automatic` | Az alapértelmezett – az oszlop típusa alapján a legmegfelelőbb megjelenítőt használja.  |  |
| `Text` | Szövegként jeleníti meg az oszlop értékeit. | |
| `Right Aligned` | Hasonló a szöveghez, kivéve, hogy jobbra igazított. | |
| `Date/Time` | Olvasható dátum-idő karakterláncot jelenít meg. | |
| `Heatmap` | A Rácsvonalak színe a cella értéke alapján. | A méretezéshez használt színpaletta és minimális/maximális érték. |
| `Bar` | A cella melletti sáv megjelenítése a cella értéke alapján. | A méretezéshez használt színpaletta és minimális/maximális érték. |
| `Bar underneath` | A cella alján lévő sáv megjelenítése a cella értéke alapján. | A méretezéshez használt színpaletta és minimális/maximális érték. |
| `Composite bar` | Egy összetett sávot jelenít meg az adott sorban megadott oszlopok használatával. A részletekért tekintse meg az [összetett sávot](workbooks-composite-bar.md) . | A sávok megjelenítéséhez és a sáv tetején megjelenő címkéhez megfelelő színeket tartalmazó oszlopok. |
| `Spark bars` | Egy Spark-sávot jelenít meg a cellában egy dinamikus tömb értékei alapján. Például a felső képernyőképen látható trend oszlop. | A méretezéshez használt színpaletta és minimális/maximális érték. |
| `Spark lines` | Egy Spark sort jelenít meg a cellában egy dinamikus tömb értékei alapján a cellában. | A méretezéshez használt színpaletta és minimális/maximális érték. |
| `Icon` | Az ikonokat a cellában lévő szöveges értékek alapján jeleníti meg. A támogatott értékek a következők:,,,,,,,,,,,,,,,,,,,,,,,,,, `cancelled` `critical` `disabled` `error` `failed` `info` `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` és `Blank` .|  |
| `Link` | Egy olyan hivatkozást jelenít meg, amely egy konfigurálható műveletre kattint vagy végez. Akkor használja ezt a beállítást, ha *azt szeretné,* hogy az elem hivatkozásként legyen felhasználva.  A többi típus *is lehet hivatkozás* a `Make this item a link` beállítás használatával. További információ: az alábbi [hivatkozásokkal kapcsolatos műveletek](#link-actions) . |  |
| `Location` | Egy felhasználóbarát Azure-régió nevét jeleníti meg a régió azonosítói alapján. |  |
| `Resource type` | Egy egyszerű erőforrástípus karakterláncát adja meg egy erőforrástípus-azonosító alapján  |  |
| `Resource` | Felhasználóbarát erőforrás-név és hivatkozás megjelenítése erőforrás-azonosító alapján  | Az erőforrástípus ikon megjelenítésének lehetősége  |
| `Resource group` | Felhasználóbarát erőforráscsoport-nevet és hivatkozást jelenít meg egy erőforráscsoport-azonosító alapján. Ha a cella értéke nem erőforráscsoport, akkor azt a rendszer konvertálja egy értékre.  | Az erőforráscsoport ikon megjelenítésének lehetősége  |
| `Subscription` | Egy felhasználóbarát előfizetés nevét és hivatkozását jeleníti meg egy előfizetés-azonosító alapján.  Ha a cella értéke nem előfizetés, a rendszer konvertálja egy értékre.  | Lehetőség az előfizetés ikon megjelenítéséhez.  |
| `Hidden` | Elrejti az oszlopot a rácsban. Akkor hasznos, ha az alapértelmezett lekérdezés további oszlopokat ad vissza, mint amennyi szükséges, de a projekt el nem várt |  |

### <a name="link-actions"></a>Hivatkozásműveletek

Ha a `Link` megjelenítő be van jelölve, vagy ha be van jelölve az *Ez az elem legyen hivatkozás* jelölőnégyzet, akkor a szerző beállíthat egy hivatkozás műveletet, amely a cella kiválasztásakor fog történni. Ez általában egy másik nézetre viszi a felhasználót a cellából származó kontextussal, vagy megnyit egy URL-címet.

### <a name="custom-formatting"></a>Egyéni formázás

A munkafüzetek azt is lehetővé teszik a felhasználók számára, hogy megadják a cellák értékének formázását. Ezt úgy teheti meg, hogy az *egyéni formázás* jelölőnégyzetre kattint, ha elérhető.

| Formázási beállítás | Magyarázat |
|:------------- |:-------------|
| `Units` | Az oszlop egységei – a százalék, a darabszám, az idő, a bájt, a darabszám/idő, a bájt/idő stb. különböző lehetőségei. Az 1234-es értékhez tartozó egység például ezredmásodpercre állítható, és 1,234 s-ként jelenik meg. |
| `Style` | A formátum, amelyet decimálisként, pénznemként, százalékként kell megjeleníteni. |
| `Show group separator` | A csoportok elválasztóinak megjelenítéséhez jelölje be a jelölőnégyzetet. Az USA 1234-as 1 234-as verzióit jeleníti meg. |
| `Minimum integer digits` | A használandó egész számjegyek minimális száma (alapértelmezett érték: 1). |
| `Minimum fractional digits` | A használandó tört számjegyek minimális száma (alapértelmezett érték: 0).  |
| `Maximum fractional digits` | A használandó tört számjegyek maximális száma. |
| `Minimum significant digits` | A használandó jelentős számjegyek minimális száma (alapértelmezés szerint 1). |
| `Maximum significant digits` | A használni kívánt jelentős számjegyek maximális száma. |
| `Custom text for missing values` | Ha egy adatponthoz nem tartozik érték, üres helyett ezt az egyéni szöveget jeleníti meg. |

### <a name="custom-date-formatting"></a>Egyéni dátum formázása

Ha a szerző azt adta meg, hogy egy oszlop a dátum/idő megjelenítő értékre van beállítva, a szerző egyéni dátum formázási beállításokat adhat meg az *Egyéni dátum formázása* jelölőnégyzet használatával.

| Formázási beállítás | Magyarázat |
|:------------- |:-------------|
| `Style` | A dátum rövid, hosszú, teljes formátumként vagy rövid vagy hosszú időformátumként történő megjelenítésének formátuma. |
| `Show time as` | Lehetővé teszi a szerző számára, hogy eldöntse, hogy a helyi idő (alapértelmezett) vagy az UTC szerinti idő látható-e. A választott dátumformátum-stílustól függően előfordulhat, hogy az UTC/időzóna adatai nem jelennek meg. |

## <a name="custom-column-width-setting"></a>Egyéni oszlopszélesség-beállítás

A szerző a rács bármely oszlopának vastagságát testreszabhatja az *oszlop beállításaiban* az *Egyéni* oszlopszélesség mező használatával.

![Képernyőkép az oszlop beállításairól a Vörös mezőben jelzett egyéni oszlopszélesség mezővel](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

Ha a mező üresen marad, a rendszer automatikusan meghatározza a szélességet az oszlop karaktereinek száma és a látható oszlopok száma alapján. Az alapértelmezett egység a "CH" (karakter).

A címke kék **(aktuális szélesség)** gombjának kiválasztásával kitölti a szövegmezőt a kijelölt oszlop aktuális szélességével. Ha egy érték szerepel az egyéni szélesség mezőben a mértékegység nélkül, akkor a rendszer az alapértelmezett értéket fogja használni.

Az elérhető mérési egységek a következők:

| Mértékegység | Definíció           |
|:--------------------|:---------------------|
| ch                  | karakterek (alapértelmezett) |
| px                  | pixel               |
| fr                  | tört egységek     |
| %                   | percentage           |

Bemeneti ellenőrzés – ha az érvényesítés meghiúsul, a vörös útmutató üzenet a mező alá kerül, de a felhasználó továbbra is alkalmazhatja a szélességet. Ha egy érték szerepel a bemenetben, az elemzésre kerül. Ha nem található érvényes mértékegység, a rendszer az alapértelmezett értéket fogja használni.

Nincs minimális/maximális szélesség, mert ez a szerző saját belátása szerint jelenik meg. Az egyéni oszlopszélesség mező le van tiltva a rejtett oszlopok esetében.

## <a name="examples"></a>Példák

### <a name="spark-lines-and-bar-underneath"></a>Spark-vonalak és-sáv alatta

Az alábbi példa a kérelmek számát és a hozzá tartozó trendet jeleníti meg a kérelem neve alapján.

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![Képernyőfelvétel egy log-alapú rácsról, egy sáv alatt és egy Spark-vonalon](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>Hő megosztott skálákkal és egyéni formázással

Ez a példa a kérelmek különböző időtartamának mérőszámait és számát mutatja. A hő-megjelenítő a beállításokban beállított minimális értékeket használja, vagy kiszámítja az oszlophoz tartozó minimális és maximális értéket, és a cella kiválasztott palettáján lévő háttérszínt az oszlop minimális és maximális értékéhez viszonyítva rendeli hozzá a cella értéke alapján.

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![Képernyőfelvétel egy log-alapú rácsról egy olyan hő, amely megosztott léptékű az oszlopok között](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

A fenti példában egy közös paletta (zöld vagy piros) és a skála az oszlopok színének (középérték, medián, P80, p95 és P99) színét használja. A kérelem oszlopához használt külön paletta (kék).

#### <a name="shared-scale"></a>Megosztott skála

Megosztott skála beszerzése:

1. Használjon reguláris kifejezéseket több oszlop kiválasztásához, ha a beállítást alkalmazni kívánja. Állítsa be például az oszlop nevét az `Mean|Median|p80|p95|p99` összes kiválasztásához.
2. Az egyes oszlopok alapértelmezett beállításainak törlése.

Ez azt eredményezi, hogy az új többoszlopos beállítás alkalmazza a beállításait úgy, hogy az tartalmazza a megosztott méretezést.

[![Képernyőfelvétel egy log-alapú rácsos beállításról az oszlopok közötti megosztott skálázáshoz](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>Az állapotot jelölő ikonok

Az alábbi példa a kérelmek egyéni állapotát mutatja a p95 időtartama alapján.

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![Képernyőkép a log-alapú rácsról egy olyan hő, amely a fenti lekérdezés használatával megosztott léptékű oszlopokat használ.](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

A támogatott ikonok nevei a következők:,,,,,,,,,,,,,,,,,,,,,,,,, `cancelled` `critical` `disabled` `error` `failed` `info` `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` és `Blank` .

### <a name="using-thresholds-with-links"></a>Küszöbértékek használata hivatkozásokkal

Az alábbi utasítások azt mutatják be, hogyan használhatók a küszöbértékek az ikonok hozzárendelésére és a különböző munkafüzetek megnyitására szolgáló hivatkozásokkal. A rács minden hivatkozása egy másik munkafüzet-sablont nyit meg az adott Application Insights erőforráshoz.

1. Váltson át a munkafüzet szerkesztési módjára az eszköztár *szerkesztése* elem kiválasztásával.
2. Válassza a **Hozzáadás** , majd a *lekérdezés hozzáadása* lehetőséget.
3. Módosítsa az *adatforrást* a "JSON" értékre, és a *vizualizációt* a "Grid" értékre.
4. Adja meg a következő lekérdezést.

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. Lekérdezés futtatása.
6. A beállítások megnyitásához válassza az **oszlop beállításai** lehetőséget.
7. Válassza a "név" lehetőséget az *oszlopok* listából.
8. Az *oszlop megjelenítő* résznél válassza a "küszöbértékek" lehetőséget.
9.  Adja meg a következő *küszöbérték-beállításokat*, és válassza ki azt.
   
    | Operátor | Érték   | Ikonok   |
    |----------|---------|---------|
    | ==       | figyelmeztetés | Figyelmeztetés |
    | ==       | error   | Sikertelen  |

    ![Képernyőkép: az oszlop beállításainak szerkesztése lap a fenti beállításokkal.](./media/workbooks-grid-visualizations/column-settings.png)

    Tartsa meg az alapértelmezett sort. Megadhatja a kívánt szöveget. A Text (szöveg) oszlop egy karakterlánc-formátumot használ bemenetként, és megadja az oszlop értékét és az egységet, ha meg van adva. Ha például a figyelmeztetés az oszlop értéke, akkor a szöveg " {0} {1} link!" lesz, "figyelmeztetési hivatkozásként" jelenik meg.
10. Jelölje be az *elem elkészítése hivatkozásként* jelölőnégyzetet.
    1. A *Megnyitás nézet* alatt válassza a "munkafüzet (sablon)" lehetőséget.
    2. *A hivatkozás értéke* területen válassza a "hivatkozás" lehetőséget.
    3. Jelölje be a *hivatkozás megnyitása a kontextusban* panelt.
    4. Válassza ki az alábbi beállításokat a *munkafüzet hivatkozásának beállításaiban*
        1. *A sablon azonosítója* területen válassza az "oszlop" lehetőséget.
        2. Az *oszlop* alatt válassza a "hivatkozás" lehetőséget.

    ![A fenti beállításokkal rendelkező hivatkozás beállításainak képernyőképe.](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. Válassza a "hivatkozás" lehetőséget az *oszlopok* listából. Az *oszlop megjelenítése* melletti beállítások területen válassza a **(oszlop elrejtése)** lehetőséget.
1. A "név" oszlop megjelenítendő nevének módosításához válassza a **címkék** lapot. A "Name" (név) nevű sorban, az *oszlop azonosítója* alatt adja meg a megjeleníteni kívánt nevet.
2. **Alkalmazás** kiválasztása

![Képernyőfelvétel a hálózatban lévő küszöbértékekről a fenti beállításokkal](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>Tört egységek százalékos aránya

A frakciós egység (FR) a mérések gyakran használt dinamikus mértékegysége a különböző típusú rácsokon. Az ablak méretének/feloldásának módosításakor a fr szélessége is megváltozik.

Az alábbi képernyőképen egy olyan táblázat látható, amely az egyes és az összes egyenlő szélességű 1fr nyolc oszlopból áll. Az ablak méretének változása esetén az egyes oszlopok szélessége arányosan változik.

[![Képernyőfelvétel a rács oszlopairól az egyes 1fr oszlop szélessége értékével](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

Az alábbi képen ugyanaz a tábla látható, az első oszlop azonban 50%-os szélességre van állítva. Ezzel a beállítással az oszlop a teljes rácsvonal szélessége felére lesz állítva. Az ablak átméretezése továbbra is megőrzi a 50%-os szélességet, ha az ablak mérete túl kicsivé válik. Ezek a dinamikus oszlopok a tartalmuk alapján minimális vastagsággal rendelkeznek. A rács fennmaradó 50%-a a nyolc teljes tört egység alapján oszlik meg. Az alábbi "Kind" oszlop a 2FR értékre van állítva, így a fennmaradó terület egyharmadát veszi igénybe. Ahogy a többi oszlop is 1fr, ezek mindegyike a rács jobb felének egy-egy nyolcadikját veszi igénybe.

[![Képernyőfelvétel az oszlopokról a rácsban 1 oszlop vastagsági értékkel, 50%, a többit pedig 1fr](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

A fr, a%, a px és a CH szélesség együttes használata lehetséges, és az előző példákhoz hasonlóan működik. A statikus egységek (CH és px) által beállított szélességi állandók, amelyek nem változnak, még akkor is, ha az ablak/megoldás módosul. A (z)% által beállított oszlopok a teljes rácsvonal szélessége alapján elveszik a százalékos arányt (Előfordulhat, hogy a korábbi minimális szélesség miatt nem pontosak). A fr értékkel beállított oszlopok csak a kiosztott tört egységek száma alapján osztják fel a fennmaradó rácsos területet.

[![Képernyőfelvétel a rácsban lévő oszlopokról a különböző szélességű egységek választékával](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan hozhat létre [fát a munkafüzetekben](workbooks-tree-visualizations.md).
* Útmutató a [munkafüzet szöveges paramétereinek](workbooks-text.md)létrehozásához.