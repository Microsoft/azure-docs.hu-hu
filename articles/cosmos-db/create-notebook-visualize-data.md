---
title: 'Oktatóanyag: jegyzetfüzet létrehozása Azure Cosmos DBban az adat elemzéséhez és megjelenítéséhez'
description: 'Oktatóanyag: Ismerje meg, hogyan importálhat adatokat Azure Cosmos DBba, elemezheti az adatokat, és jelenítheti meg a kimenetet a beépített Jupyter notebookok használatával.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: beb58922172a045242f4f9bcaf647b3cfc8b5551
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380797"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Oktatóanyag: jegyzetfüzet létrehozása Azure Cosmos DBban az adat elemzéséhez és megjelenítéséhez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk azt ismerteti, hogyan használhatók a beépített Jupyter jegyzetfüzetek a mintaként szolgáló kiskereskedelmi adatAzure Cosmos DBek importálásához. Láthatja, hogyan használható az SQL és a Azure Cosmos DB Magic parancsok a lekérdezések futtatásához, az adatelemzéshez és az eredmények megjelenítéséhez.

## <a name="prerequisites"></a>Előfeltételek

* [Jegyzetfüzetek engedélyezése Azure Cosmos-fiókban](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Erőforrások létrehozása és az információk importálása
 
Ebben a szakaszban létrehozza az Azure Cosmos-adatbázist, a tárolót, és importálja a kiskereskedelmi adattárat a tárolóba.

1. Navigáljon az Azure Cosmos-fiókjához, és nyissa meg a **adatkezelő.**

1. Lépjen a **jegyzetfüzetek** lapra, válassza a `…` **saját jegyzetfüzetek** lehetőséget, majd hozzon létre egy **új jegyzetfüzetet**. Az alapértelmezett Kernelként válassza a **Python 3** elemet.

   :::image type="content" source="./media/create-notebook-visualize-data/create-new-notebook.png" alt-text="Új jegyzetfüzet létrehozása":::

1. Egy új jegyzetfüzet létrehozása után átnevezheti a **VisualizeRetailData. ipynb** -hoz hasonló módon.

1. Ezután létre fog hozni egy "RetailDemo" nevű adatbázist és egy "WebsiteData" nevű tárolót a kiskereskedelmi adattároláshoz. A/CartID-t használhatja partíciós kulcsként. Másolja és illessze be a következő kódot egy új cellába a jegyzetfüzetben, majd futtassa azt:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Cella futtatásához válassza ki `Shift + Enter` vagy válassza ki a cellát, majd válassza az **aktív cella futtatása** lehetőséget az adatkezelő navigációs sávján.

   :::image type="content" source="./media/create-notebook-visualize-data/run-active-cell.png" alt-text="Az aktív cella futtatása":::

   Az adatbázis és a tároló a jelenlegi Azure Cosmos-fiókban jön létre. A tároló 400 RU/s-vel van kiépítve. Az adatbázis és a tároló létrehozása után a következő kimenet jelenik meg. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   Emellett frissítheti az **adatlapot,** és megtekintheti az újonnan létrehozott erőforrásokat:

   :::image type="content" source="media/create-notebook-visualize-data/refresh-data-tab.png" alt-text="Az új tároló megjelenítéséhez frissítse az adatlapot":::

1. A következő lépésként importálni fogja a minta kiskereskedelmi adattárat az Azure Cosmos-tárolóba. Itt látható a kiskereskedelmi adatokból származó elem formátuma:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   Az oktatóanyag céljaként a mintaként szolgáló kiskereskedelmi adat tárolása az Azure Blob Storage-ban történik. A következő kód egy új cellába való beillesztésével importálhatja azt az Azure Cosmos-tárolóba. Az elemek számának kiválasztásához a lekérdezés futtatásával ellenőrizheti, hogy az adatok importálása sikeres volt-e.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   Az előző lekérdezés futtatásakor a következő kimenetet adja vissza:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Az adatai beolvasása egy DataFrame

Mielőtt lekérdezéseket futtasson az adatok elemzéséhez, olvassa el az adatok a tárolóból egy [Panda DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) az elemzéshez. Az alábbi SQL Magic-paranccsal olvassa be az DataFrame:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

További információért lásd a [beépített notebook-parancsokat és-szolgáltatásokat Azure Cosmos db](use-python-notebook-features-and-commands.md) cikkben. A lekérdezést fogja futtatni – `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c` . Az eredmények egy df_cosmos nevű Panda DataFrame lesznek mentve. Illessze be a következő parancsot egy új jegyzetfüzet-cellába, és futtassa azt:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

Egy új jegyzetfüzet-cellában futtassa a következő kódot a kimenet első 10 elemének olvasásához:

```python
# See a sample of the result
df_cosmos.head(10)
```

:::image type="content" source="./media/create-notebook-visualize-data/run-query-get-top10-items.png" alt-text="Lekérdezés futtatása az első 10 elem lekéréséhez":::

## <a name="run-queries-and-analyze-your-data"></a>Lekérdezések futtatása és az adataik elemzése

Ebben a szakaszban néhány lekérdezést fog futtatni a beolvasott adatforrásról.

* **Query1:** Futtasson egy csoportosítási lekérdezést a DataFrame, hogy lekérje az egyes országok/régiók teljes értékesítési bevételének összegét, és 5 elemet jelenítsen meg az eredményekből. Az új jegyzetfüzet-cellában futtassa a következő kódot:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/total-sales-revenue-output.png" alt-text="Értékesítési bevétel teljes kimenete":::

* **Query2:** Az öt legfontosabb megvásárolt elem listájának lekéréséhez nyisson meg egy új jegyzetfüzet-cellát, és futtassa a következő kódot:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/top5-purchased-items.png" alt-text="Az öt legfontosabb megvásárolt elem":::

## <a name="visualize-your-data"></a>Adatok vizualizációja  

1. Most, hogy adataink az Azure Cosmos-tárolóból származnak, az adatait megjelenítheti egy tetszőleges vizualizációs könyvtárral. Ebben az oktatóanyagban a bokeh könyvtárat fogjuk használni. Nyisson meg egy új jegyzetfüzet-cellát, és futtassa a következő kódot a bokeh-könyvtár telepítéséhez. Miután az összes követelmény teljesült, a rendszer telepíti a könyvtárat.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Következő felkészülés az adatábrázolásra a térképen. Csatlakoztassa Azure Cosmos DB adatokat az Azure Blob Storage-ban található ország/régió adataival, és alakítsa át az eredményt GeoJSON formátumra. Másolja az alábbi kódot egy új jegyzetfüzet-cellába, és futtassa.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country/region information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries/regions dataframe with our data in Azure Cosmos DB, joining on country/region code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. A következő kód egy új jegyzetfüzet-cellában történő futtatásával jelenítheti meg a különböző országok/régiók értékesítési bevételeit egy globális térképen:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   A kimenet a világ térképét jeleníti meg különböző színekkel. A világosabb színek a legalacsonyabb bevételsel rendelkező országokat és régiókat jelölik.

   :::image type="content" source="./media/create-notebook-visualize-data/countries-revenue-map-visualization.png" alt-text="Országok/régiók bevételi Térkép vizualizációja":::

1. Lássuk egy másik esetet az adatvizualizációhoz. A WebsiteData-tároló olyan felhasználókat tartalmaz, akik megtekintett egy tételt, hozzáadták őket a kosárhoz, és megvásárolták az adott tételt. Nézzük meg a megvásárolt elemek konverziós arányát. Futtassa az alábbi kódot egy új cellában az egyes elemek konverziós arányának megjelenítéséhez:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png" alt-text="Vásárlási konverziós arány megjelenítése":::

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a Python notebook-parancsokról, olvassa el a következő témakört: [a beépített notebook-parancsok és-szolgáltatások használata Azure Cosmos db](use-python-notebook-features-and-commands.md) cikkben.
