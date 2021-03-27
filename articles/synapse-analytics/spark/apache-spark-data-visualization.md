---
title: Vizualizációk
description: Az Azure szinapszis notebookok használata az adatai megjelenítéséhez
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: 7e57cdca1d212e6077d685d95a8f869c12e546a8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627948"
---
# <a name="visualize-data"></a>Adatok vizualizációja
Az Azure szinapszis egy integrált elemzési szolgáltatás, amely felgyorsítja az adattárházak és a big data elemzési rendszerek közötti időt az elemzésre. Az adatvizualizáció egy kulcsfontosságú összetevő, amellyel betekintést nyerhet az adataiba. Megkönnyíti az emberek számára a nagy és kis mennyiségű adat megértését. Megkönnyíti az adatcsoportokban lévő minták, trendek és kiugró értékek észlelését is. 

Az Azure szinapszis Analytics Apache Spark használata során számos beépített lehetőség áll rendelkezésre, amelyek segítségével megjelenítheti az adatait, beleértve a szinapszis jegyzetfüzet-diagramok beállításait, a népszerű nyílt forráskódú kódtárak elérését, valamint a szinapszis SQL és a Power BI integrálását.

## <a name="notebook-chart-options"></a>Jegyzetfüzet-diagram beállításai
Az Azure szinapszis notebook használatakor a táblázatos eredmények nézetét egy testreszabott diagramra is kikapcsolhatja a diagram beállításainak használatával. Itt megjelenítheti az adatait anélkül, hogy kódot kellene írnia. 

### <a name="displaydf-function"></a>Display (DF) függvény
A ```display``` függvény lehetővé teszi az SQL-lekérdezések bekapcsolását Apache Spark, valamint a dataframes és a RDD a gazdag adatvizualizációk között. A ```display``` függvény a PySpark, a Scala, a Java és a .NET szolgáltatásban létrehozott dataframes vagy RDD használható.

A diagram beállításainak elérése:
1. A ```%%sql``` Magic parancsok kimenete alapértelmezés szerint a megjelenített tábla nézetben jelenik meg. Meghívhatja ```display(df)``` a Spark DataFrames vagy a rugalmasan elosztott adatkészletek (RDD) függvényt is a megjelenített tábla nézet létrehozásához. 
   
2. Ha egy megjelenített táblázat nézettel rendelkezik, váltson a diagram nézetre.
   ![beépített diagramok](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. Mostantól testreszabhatja a vizualizációt a következő értékek megadásával:

   | Konfiguráció | Leírás |
   |--|--| 
   | Diagram típusa | A ```display``` függvény számos diagramtípust támogat, többek között a sávdiagramok, a pontdiagram, a vonalas diagramok és egyéb funkciók használatát. |
   | Kulcs | Az x tengely értékei tartományának megadása|
   | Érték | Az y tengely értékeihez tartozó értékek tartományának megadása |
   | Adatsorozat-csoport | Az összesítéshez tartozó csoportok meghatározására szolgál. | 
   | Összesítés | A vizualizációban lévő adatösszesítés módszere| 
   
   
    > [!NOTE]
    > Alapértelmezés szerint a ```display(df)``` függvény csak az adat első 1000 sorát fogja felvenni a diagramok megjelenítéséhez. Tekintse meg az **összesítést az összes találaton** , majd kattintson az **alkalmaz** gombra, majd alkalmazza a diagram létrehozását a teljes adatkészletből. A diagram beállításainak módosításakor a rendszer elindítja a Spark-feladatot. Vegye figyelembe, hogy a számítás befejezéséhez és a diagram megjelenítéséhez több percet is igénybe vehet.
   
4. Ha elkészült, megtekintheti és használhatja a végső vizualizációt.

### <a name="displaydf-statistic-details"></a>megjelenítési (DF) statisztika részletei
A segítségével <code>display(df, summary = true)</code> megtekintheti egy adott Apache Spark DataFrame statisztikai összegzését, amely tartalmazza az oszlop nevét, az oszlop típusát, az egyedi értékeket és az egyes oszlopok hiányzó értékeit is. Kiválaszthat egy adott oszlopot is a minimális érték, a maximális érték, a középérték és a szórás megjelenítéséhez.
    ![beépített diagramok – összefoglalás](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>displayHTML (DF) beállítás
Az Azure szinapszis Analytics notebookok a függvény használatával támogatják a HTML-grafikákat ```displayHTML``` .

Az alábbi ábrán egy példa látható a vizualizációk létrehozására [D3.js](https://d3js.org/)használatával.

   ![D3-js – példa](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

Futtassa a következő kódot a fenti vizualizáció létrehozásához.

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>Népszerű kódtárak
Az adatvizualizációk használatakor a Python több gráfot kínál, amelyek számos különböző funkcióval rendelkeznek. Alapértelmezés szerint az Azure szinapszis Analytics minden Apache Spark készlete válogatott és népszerű nyílt forráskódú kódtárakat tartalmaz. Az Azure szinapszis Analytics függvénytár-felügyeleti képességeivel további kódtárakat is hozzáadhat vagy kezelhet & verziókhoz. 

### <a name="matplotlib"></a>Matplotlib
Az egyes tárak beépített renderelési funkcióinak használatával szabványos ábrázolási kódtárakat, például Matplotlib-ket lehet megjeleníteni.

Az alábbi ábrán egy példa látható egy sávdiagram létrehozására a **Matplotlib** használatával.
   ![Példa a diagramra.](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

Futtassa a következő mintakód a fenti rendszerkép rajzolásához.

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```


### <a name="bokeh"></a>Bokeh
A használatával HTML-vagy interaktív kódtárakat, például a **bokeht** is megjelenítheti ```displayHTML(df)``` . 

Az alábbi ábrán egy példa látható a karakterjelek ábrázolására a **bokeh** használatával.

   ![bokeh – példa](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

Futtassa a következő mintakód a fenti rendszerkép rajzolásához.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```


### <a name="plotly"></a>Plotly
A **displayHTML ()** használatával megjelenítheti a HTML-vagy interaktív kódtárakat, például a **nyomtatást**.

A következő mintakód futtatásával rajzolja meg az alábbi képet.

   ![rajzolt példa](./media/apache-spark-development-using-notebooks/synapse-plotly-image.png#lightbox)


```python
from urllib.request import urlopen
import json
with urlopen('https://raw.githubusercontent.com/plotly/datasets/master/geojson-counties-fips.json') as response:
    counties = json.load(response)

import pandas as pd
df = pd.read_csv("https://raw.githubusercontent.com/plotly/datasets/master/fips-unemp-16.csv",
                   dtype={"fips": str})

import plotly.express as px

fig = px.choropleth(df, geojson=counties, locations='fips', color='unemp',
                           color_continuous_scale="Viridis",
                           range_color=(0, 12),
                           scope="usa",
                           labels={'unemp':'unemployment rate'}
                          )
fig.update_layout(margin={"r":0,"t":0,"l":0,"b":0})

# create an html document that embeds the Plotly plot
h = plotly.offline.plot(fig, output_type='div')

# display this html
displayHTML(h)
```


### <a name="additional-libraries"></a>További kódtárak 
Ezen könyvtárakon túl az Azure szinapszis Analytics Runtime a következő kódtárakat is tartalmazza, amelyeket gyakran használnak adatvizualizációhoz:
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Seaborn](https://seaborn.pydata.org/) 
- [Plotly](https://plotly.com/)

Az Azure szinapszis Analytics futtatókörnyezet [dokumentációjában](./spark/../apache-spark-version-support.md) tájékozódhat a rendelkezésre álló könyvtárakról és verziókról a legnaprakészebb információkról.

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>Kapcsolódás Power BI Apache Spark & SQL igény szerinti használatával
Az Azure szinapszis Analytics mélyen integrálva van Power BI, amely lehetővé teszi, hogy az adatmérnökök elemzési megoldásokat hozzanak létre.

Az Azure szinapszis Analytics lehetővé teszi, hogy a különböző munkaterület-számítási motorok adatbázisokat és táblákat osszanak meg a Spark-készletek és a kiszolgáló nélküli SQL-készlet között. A [megosztott metaadat-modell](../metadata/overview.md)használatával lekérdezheti az Apache Spark táblákat az SQL igény szerinti használatával. Ha elkészült, összekapcsolhatja az SQL igény szerinti végpontját, hogy Power BI a szinkronizált Spark-táblázatok egyszerű lekérdezéséhez.


## <a name="next-steps"></a>Következő lépések
- További információ a Spark SQL DW-összekötő beállításáról: [SZINAPSZIS SQL Connector](./spark/../synapse-spark-sql-pool-import-export.md)
- Az alapértelmezett könyvtárak megtekintése: az [Azure szinapszis Analytics futtatókörnyezete](../spark/apache-spark-version-support.md)