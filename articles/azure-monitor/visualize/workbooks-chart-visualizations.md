---
title: Azure Monitor munkafüzet-diagramok vizualizációi
description: Ismerkedjen meg az összes Azure Monitor munkafüzet-diagram vizualizációval.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618905"
---
# <a name="chart-visualizations"></a>Diagramvizualizációk

A munkafüzetek lehetővé teszik, hogy a figyelési adatgyűjtés diagramként legyen megjelenítve. A támogatott diagramtípusok közé tartozik a vonal, a sáv, a kategorikus, a terület, a pontdiagram, a torta és az idő. A szerzők dönthetnek úgy, hogy testre szabják a diagram magasságát, szélességét, színpalettáját, jelmagyarázatát, címét, nem adatüzenetét, stb. a diagram beállításai alapján testreszabják a tengely típusát és a sorozat színét.

A munkafüzetek a naplók és a metrikus adatforrások diagramjait támogatják.

## <a name="log-charts"></a>Naplózási diagramok

Azure Monitor naplók a források tulajdonosai részletes információkat biztosítanak az alkalmazások és az infrastruktúra működéséről. A metrikákkal ellentétben a rendszer alapértelmezés szerint nem gyűjti a naplózási adatokat, és a begyűjtéshez valamilyen fajta gyűjtemény szükséges. A jelen naplók azonban nagy mennyiségű információt biztosítanak az erőforrás állapotáról és a diagnosztika hasznos adatairól. A munkafüzetek vizuális diagramokként teszik lehetővé a naplófájlok megjelenítését a felhasználói elemzéshez.

### <a name="adding-a-log-chart"></a>Naplózási diagram hozzáadása

Az alábbi példa az alkalmazásra irányuló kérések trendjét mutatja az előző napokon.

1. Váltson át a munkafüzet szerkesztési módjára az eszköztár **szerkesztése** elem kiválasztásával.
2. A **lekérdezés hozzáadása** hivatkozásra kattintva hozzáadhat egy log lekérdezési vezérlőt a munkafüzethez.
3. Válassza ki a lekérdezés típusát **naplóként**, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. A lekérdezéstervező segítségével adja meg az elemzés [KQL](/azure/kusto/query/) (például a kérelmek trendje).
5. Állítsa a vizualizációt a következőre **: terület**, **sáv**, **sáv (kategorikus)**, **vonal**, torta **,** pont vagy **idő**. 
6. Szükség esetén más paramétereket is beállíthat, például a vizualizációt, a méretet, a színpalettát és a jelmagyarázatot.

[![Képernyőfelvétel a diagram szerkesztési módjáról](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>A naplózási diagram paraméterei

| Paraméter | Magyarázat | Példa |
| ------------- |:-------------|:-------------|
| `Query Type` | A használandó lekérdezés típusa. | Napló, Azure Resource Graph stb. |
| `Resource Type` | A célként megadott erőforrástípus. | Application Insights, Log Analytics vagy Azure – első |
| `Resources` | Erőforrások készlete, amelyből a metrikák értékét be kell olvasni. | MyApp1 |
| `Time Range` | A naplózási diagram megtekintésére szolgáló időablak. | Az elmúlt óra, az elmúlt 24 óra stb. |
| `Visualization` | A használni kívánt vizualizáció. | Körzet, sáv, vonal, torta, Scatter, idő, Bar kategorikus |
| `Size` | A vezérlő függőleges mérete | Kicsi, közepes, nagy vagy teljes |
| `Color palette` | A diagramban használandó színpaletta Több metrikus vagy szegmentált módban figyelmen kívül hagyva. | Kék, zöld, piros stb. |
| `Legend` | A jelmagyarázathoz használandó aggregációs függvény | Értékek vagy maximum, minimum, első, utolsó érték összege vagy átlaga |
| `Query` | Bármely olyan KQL-lekérdezés, amely a diagram vizualizációja által várt formátumban adja vissza az adatok értékét. | _a kérelmek \| sorozatos kérelmeket igényelnek = count () default = 0 az időbélyegzőn (1d) – most (), 1 – 5. lépés_ |

### <a name="time-series-charts"></a>Idősorozat-diagramok

Az idősorozat-diagramok, például a munkaterület, a sáv, a vonal, a scatter és az idő könnyen hozhatók létre a munkafüzetek lekérdezési vezérlőjének használatával. A kulcs idő-és metrikai információkkal rendelkezik az eredményhalmazban.

#### <a name="simple-time-series"></a>Egyszerű idősorozat

Az alábbi lekérdezés két oszloppal rendelkező táblázatot ad vissza: *időbélyeg* és *kérések*. A lekérdezés vezérlőelem az X tengely és az Y tengely *kéréseinek* *időbélyegét* használja.

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![Képernyőfelvétel egy egyszerű idősorozatú naplófájlról](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>Idősorozat több metrikával

Az alábbi lekérdezés három oszlopból álló táblázatot ad vissza: *időbélyeg*, *kérések* és *felhasználók*. A lekérdezési vezérlő az X tengely *időbélyegét* használja , és  &  külön adatsorozatként kéri a *felhasználókat* az Y tengelyen.

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![Képernyőkép egy idősorozatról, amely több mérőszámot tartalmazó naplófájlt ábrázol.](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>Szegmentált idősorozat

Az alábbi lekérdezés három oszlopból álló táblázatot ad vissza: *timestamp*, *requests* és *RequestName* , ahol a *RequestName* egy kategorikus oszlop a kérelmek neveivel. A lekérdezési vezérlő az X tengely *időbélyegét* használja, és egy adatsorozatot *RequestName*.

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![Képernyőfelvétel egy szegmentált idősorozat-diagramról](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>Összefoglalás és a make-sorozat összehasonlítása

Az előző szakaszban szereplő példák az `summarize` operátort használják, mert könnyebben érthető. Az összefoglalás azonban jelentős korlátozást tartalmaz, mivel nem hagyja el az eredmények sorát, ha nincsenek elemek a gyűjtőben. Ez hatással lehet a diagram időablakának váltására attól függően, hogy az üres gyűjtők az időtartomány elején vagy hátoldalán vannak-e.

Általában jobb, ha az operátor használatával `make-series` idősorozat-adatok hozhatók létre, amelyek az üres gyűjtők alapértelmezett értékeit biztosítják.

A következő lekérdezés az `make-series` operátort használja.

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

Az alábbi lekérdezés egy hasonló diagramot mutat be az `summarize` operátorral

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

Annak ellenére, hogy az alapul szolgáló eredményhalmaz eltérő. Az összes felhasználónak be kell állítania a vizualizációt a terület, a vonal, a sáv vagy az idő és a munkafüzetek számára.

[![Képernyőkép a make-Series lekérdezésből készített log line diagramról](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>Kategorikus oszlopdiagram vagy Hisztogram

A kategorikus diagramok lehetővé teszik a felhasználók számára, hogy egy diagram X tengelyén lévő dimenziót vagy oszlopot képviselnek, ez különösen a hisztogramokban hasznos. Az alábbi példában a kérelmeknek az eredmény kódja alapján történő eloszlása látható.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

A lekérdezés két oszlopot ad vissza: a *kérelmek* metrikáját és az *eredmény* kategóriáját. Az *eredmény* oszlop minden értéke a *kérések metrikájának* megfelelő magassággal fogja lekérni a diagramhoz tartozó saját sávot.

[![Képernyőfelvétel egy kategorikus oszlopdiagram-diagramról az eredmény kódja alapján történő kérelmekhez](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>Tortadiagramok

A tortadiagramok lehetővé teszik a numerikus arány megjelenítését. Az alábbi példa a kérések arányát mutatja az eredmény kódjával.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

A lekérdezés két oszlopot ad vissza: a *kérelmek* metrikáját és az *eredmény* kategóriáját. Az *eredmény* oszlop minden értéke a *kérések* metrikájának megfelelő mérettel fogja megkapni a saját szeletét a tortában.

[![Képernyőkép egy tortadiagramról, amely az eredmény kódját ábrázolja](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>Metrikai diagramok

A legtöbb Azure-erőforrás metrikus adatokat bocsát ki az állapotról és az állapotról (például a processzor kihasználtsága, a tárterület rendelkezésre állása, az adatbázis-tranzakciók száma, az alkalmazások meghibásodása stb.). A munkafüzetek idősorozat-diagramokként teszik lehetővé az adatmegjelenítést.)

### <a name="adding-a-metric-chart"></a>Metrikus diagram hozzáadása

Az alábbi példa egy Storage-fiókban lévő tranzakciók számát mutatja az előző órában. Ez lehetővé teszi, hogy a tároló tulajdonosa lássa a tranzakciós trendet, és keresse meg a viselkedésben észlelt rendellenességeket.

1. Váltson át a munkafüzet szerkesztési módjára az eszköztár **szerkesztése** elem kiválasztásával.
2. A **metrika hozzáadása** hivatkozásra kattintva hozzáadhat egy mérőszám vezérlőelemet a munkafüzethez.
3. Válasszon ki egy erőforrástípust (például Storage-fiók), a célként megadott erőforrásokat, a metrika névterét és nevét, valamint a használandó összesítést.
4. Szükség esetén más paramétereket is beállíthat, például időtartományt, felosztást, vizualizációt, méretet és színpalettát.

[![A metrikus diagram képernyőképe szerkesztési módban](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>Metrikai diagram paraméterei

| Paraméter | Magyarázat | Példa |
| ------------- |:-------------|:-------------|
| `Resource Type` | A célként megadott erőforrástípus. | Tároló vagy virtuális gép. |
| `Resources` | Erőforrások készlete, amelyből a metrikák értékét be kell olvasni. | MyStorage1 |
| `Namespace` | A metrikával rendelkező névtér. | Storage > blob |
| `Metric` | A megjeleníteni kívánt metrika. | Storage > blob > tranzakció |
| `Aggregation` | A metrikára alkalmazandó aggregációs függvény. | Összeg, Darabszám, átlag stb. |
| `Time Range` | Az időablak, amely a metrikát jeleníti meg. | Az elmúlt óra, az elmúlt 24 óra stb. |
| `Visualization` | A használni kívánt vizualizáció. | Terület, sáv, vonal, Scatter, rács |
| `Split By` | Kiválaszthatja a mérőszámot egy dimenzión. | Tranzakciók földrajzi típus szerint |
| `Size` | A vezérlő függőleges mérete | Kis, közepes vagy nagy |
| `Color palette` | A diagramban használandó színpaletta Figyelmen kívül hagyva `Split by` , ha a paraméter használatban van. | Kék, zöld, piros stb. |

### <a name="examples"></a>Példák

API-név szerint felosztott tranzakciók diagramként:

[![Képernyőkép egy metrikus vonalas diagramról a tárolási tranzakciók API-névvel való felosztásához](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

A válasz típusa szerint megszakított tranzakciók nagy sávdiagramként:

[![Képernyőkép egy nagyméretű metrikai oszlopdiagram a tárolási tranzakciók számára a válasz típusa szerint felosztva](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

Szórási diagram átlagos késése:

[![Képernyőkép egy metrikai pontdiagram a tárolási késéshez](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>Diagram beállításai

A szerzők a diagram beállításait használva testre szabhatják, hogy mely mezők legyenek a diagram tengelyei, a tengely mértékegységei, az egyéni formázás, a tartományok, a csoportosítási viselkedés, a jelmagyarázatok és az adatsorozatok színei.

### <a name="the-settings-tab"></a>A beállítások lap

A beállítások lap vezérlői:

- A tengely beállításai, beleértve a mezőket, az egyéni formázást, amely lehetővé teszi a felhasználók számára a számok formázásának megadását a tengely értékeire és az egyéni tartományokra.
- A csoportosítási beállítások, beleértve a mező értékét, a "mások" csoport előtt megjelenő korlátok.
- Jelmagyarázat beállításai, beleértve a metrikák (adatsorozat neve, színek és számok) megjelenítését alul és/vagy jelmagyarázatban (adatsorozat-nevek és színek).

![A diagram beállításainak képernyőképe](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>Egyéni formázás

A formázási lehetőségek a következők:

| Formázási beállítás             | Magyarázat                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | Az oszlop egységei – a százalék, a darabszám, az idő, a bájt, a darabszám/idő, a bájt/idő stb. különböző lehetőségei. Az 1234-es értékhez tartozó egység például lehet ezredmásodpercre beállítva, és 1.234 s-ként jelenik meg.                                  |
| `Style`                      | A formátum, amelyet decimálisként, pénznemként, százalékként kell megjeleníteni.                                               |
| `Show group separator`       | A csoportok elválasztóinak megjelenítéséhez jelölje be a jelölőnégyzetet. Az USA 1234-as 1 234-as verzióit jeleníti meg.                                    |
| `Minimum integer digits`     | A használandó egész számjegyek minimális száma (alapértelmezett érték: 1).                                                   |
| `Minimum fractional digits`  | A használandó tört számjegyek minimális száma (alapértelmezett érték: 0).                                                |
| `Maximum fractional digits`  | A használandó tört számjegyek maximális száma.                                                            |
| `Minimum significant digits` | A használandó jelentős számjegyek minimális száma (alapértelmezés szerint 1).                                               |
| `Maximum significant digits` | A használni kívánt jelentős számjegyek maximális száma.                                                           |

![Képernyőkép az X tengely beállításairól.](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>Az adatsorozat lap

A sorozat beállítása lapon módosíthatja a diagram adatsoraihoz megjelenített feliratokat és színeket.

- A `Series name` mező az adatsorozatok egyeztetésére szolgál, és ha megegyeznek, a megjelenített felirat és a szín jelenik meg.
- A `Comment` mező hasznos a sablon szerzője számára, mivel ezt a megjegyzést a fordítók a megjelenített címkék honosítására használhatják.

![Képernyőfelvétel a sorozat beállításairól.](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan hozhat létre [csempéket a munkafüzetekben](workbooks-tile-visualizations.md).
- Ismerje meg, hogyan hozhat létre [interaktív munkafüzeteket](workbooks-interactive.md).