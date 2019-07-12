---
title: Jupyter Notebook használata az adatok elemzése az Azure Data Explorer
description: Ez a témakör bemutatja, hogyan az adatok elemzése az Azure Data Explorer Jupyter Notebook és a Kqlmagic bővítmény használatával.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a894052e54bd1ca9f8316342f714074c92753448
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806340"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Adatok elemzése az Azure Data Explorer egy Jupyter Notebookot, és Kqlmagic bővítmény használatával

Jupyter Notebook egy nyílt forráskódú webes alkalmazás, amely lehetővé teszi, hogy hozzon létre és megoszthatja a dokumentumokat, amely tartalmazza az élő kód, a egyenletek, a Vizualizációk és a leíró szöveg. Használati adatok tisztítása és átalakítása numerikus szimuláció, statisztikai modellezési, adatvizualizáció és machine learning tartalmazza.
[Jupyter Notebook](https://jupyter.org/) magic függvények támogatásával, további parancsok a kernel képességeit kibővítő támogatja. KQL Magic Quadrant, amely bővíti a Python-kernel a Jupyter Notebook Kusto nyelvi lekérdezések futtatásához natív módon parancs. A Python és Kusto lekérdezési nyelv lekérdezése és gazdag és integrált Plot.ly könyvtár használatával adatok vizualizációja révén könnyedén egyesítheti `render` parancsokat. A futó lekérdezések adatforrások támogatottak. Ezeknek az adatforrásoknak az Azure az adatkezelőt, egy gyors és hatékonyan méretezhető exploration szolgáltatás napló és a telemetriai adatokat, csakúgy, mint az Azure Monitor naplóira és az Application Insights tartalmazza. KQL Magic Quadrant Azure notebookok, a Jupyter labor és a Visual Studio Code Jupyter kiterjesztésű is működik.

## <a name="prerequisites"></a>Előfeltételek

- Munkahelyi e-mail-fiók, amely tagja az Azure Active Directory (AAD).
- A Jupyter Notebook telepítve van a helyi gépén vagy Azure notebookok használata, és klónozza a minta [Azure Notebook](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>KQL magic erőforrástár telepítése

1. KQL Magic Quadrant telepítése:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Az Azure-notebookok használata esetén ez a lépés nem kötelező.

1. Betöltés KQL Magic Quadrant:

    ```python
    reload_ext Kqlmagic
    ```

## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Csatlakozás az Azure Data Explorer súgója fürthöz

A következő paranccsal csatlakozhat a *minták* adatbázisban lévő üzemeltetett a *súgó* fürt. A Microsoft AAD-felhasználók számára, cserélje le a bérlő neve `Microsoft.com` az AAD-bérlőben.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Lekérdezése és megjelenítése

Lekérdezési adatok használatával a [operátor render](/azure/kusto/query/renderoperator) és az adatok megjelenítése a ploy.ly kódtár használatával. Ehhez a lekérdezéshez és vizualizációs választékát kínálja, amely natív KQL integrált megoldást. Kqlmagic támogatja kivételével a legtöbb diagramok `timepivot`, `pivotchart`, és `ladderchart`. Renderelési támogatott, kivéve az összes attribútum `kind`, `ysplit`, és `accumulate`. 

### <a name="query-and-render-piechart"></a>Lekérdezés és a renderelési piechart

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Lekérdezés és a renderelési idődiagramját

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Ezek a diagramok használata interaktív. Jelöljön ki egy időtartományt, egy adott időpont nagyíthatja.

### <a name="customize-the-chart-colors"></a>A diagram színeinek testreszabásához

Ha nem szeretné az alapértelmezett színpalettát, testre szabhatja a diagramok paletta beállítások használatával. A rendelkezésre álló palettára itt található: [A KQL magic lekérdezési diagram eredményhez színpaletta kiválasztása](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Színpaletták listáját:

    ```python
    %kql --palettes -popup_window
    ```

1. Válassza ki a `cool` paletta szín és jelennek meg újra a lekérdezést:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>A Python egy lekérdezés paraméterezése

Lehetővé teszi a KQL Magic Quadrant egyszerű adatcsere Kusto lekérdezési nyelvet és a Python között. További tudnivalók: [A Python használatával KQL magic lekérdezés paraméterezése](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Használhat egy Python-változót a KQL lekérdezés

Egy Python-változó értékét használhatja a lekérdezésben az adatok szűrése:

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Lekérdezési eredmények átalakítása Pandas DataFrame

Pandas DataFrame KQL lekérdezések eredményeit is elérheti. Az utolsó végrehajtott lekérdezési eredmények eléréséhez a változó által `_kql_raw_result_` és egyszerűen alakíthatja az eredmények Pandas DataFrame módon:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Példa

Számos elemzési forgatókönyvek érdemes létrehozni, amely számos lekérdezéseket tartalmaz, és az eredmények hírcsatorna egy lekérdezés által az ezt követő lekérdezéseket újrafelhasználható notebookok. Az alábbi példa Python változót használja `statefilter` az adatok szűréséhez.

1. Az első 10 állapotot, legfeljebb egy lekérdezés futtatásával `DamageProperty`:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Bontsa ki a felső állapotot, és állítsa be egy Python-változóba egy lekérdezés futtatásával:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Egy lekérdezés segítségével futtassa a `let` utasítás és a Python-változó:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. A Súgó parancs futtatásához:

    ```python
    %kql --help "help"
    ```

> [!TIP]
> Az összes elérhető konfigurációk használata szeretne információt kapni `%config KQLmagic`. Kusto-hibák, például kapcsolati problémák és helytelen lekérdezéseket, rögzítése és hibáinak elhárítása használja `%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>További lépések

A help paranccsal Fedezze fel a következő mintafüzetek, amelyek tartalmazzák a támogatott szolgáltatások:
- [Ismerkedés az KQL magic Azure adatkezelő](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Ismerkedés a KQL magic az Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Az Azure Monitor-naplók KQL magic használatának első lépései](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [A KQL magic lekérdezés Parametrize Python használatával](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [A KQL magic lekérdezési diagram eredményhez színpaletta kiválasztása](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
