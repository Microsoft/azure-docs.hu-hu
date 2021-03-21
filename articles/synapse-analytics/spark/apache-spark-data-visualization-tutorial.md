---
title: Adatvizualizáció az Apache Spark használatával
description: Gazdag adatvizualizációk létrehozása Apache Spark és Azure szinapszis Analytics-jegyzetfüzetek használatával
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 56af49b6fa862c93822293056752182b534ac442
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942274"
---
# <a name="analyze-data-with-apache-spark"></a>Adatok elemzése az Apache Sparkkal

Ebből az oktatóanyagból megtudhatja, hogyan végezheti el a felderítő adatok elemzését az Azure Open-adatkészletek és a Apache Spark használatával. Ezután megjelenítheti az eredményeket egy szinapszis Studio jegyzetfüzetben az Azure szinapszis Analyticsben.

Különösen elemezzük a [New York City (New York-i) taxi](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) -adatkészletet. Az adatokat az Azure Open-adatkészletek segítségével érheti el. Az adatkészlet ezen részhalmaza a sárga taxis utakkal kapcsolatos információkat tartalmazza: az egyes utazásokkal, a kezdő és a záró időponttal, valamint a díjszabással és egyéb érdekes attribútumokkal kapcsolatos információkat.
  
## <a name="before-you-begin"></a>Előkészületek
Hozzon létre egy Apache Spark készletet az [Apache Spark készlet létrehozása oktatóanyag](../articles/../quickstart-create-apache-spark-pool-studio.md)követésével. 

## <a name="download-and-prepare-the-data"></a>Az adatgyűjtés letöltése és előkészítése
1. Hozzon létre egy jegyzetfüzetet a PySpark kernel használatával. Útmutatásért lásd: [Jegyzetfüzet létrehozása](../quickstart-apache-spark-notebook.md#create-a-notebook). 
   
   > [!Note]
   > A PySpark kernel miatt nem kell explicit módon létrehoznia a környezeteket. A Spark-környezet automatikusan létrejön az első kód cellájának futtatásakor.

2. Ebben az oktatóanyagban számos különböző kódtárat fogunk használni az adatkészlet megjelenítéséhez. Az elemzés végrehajtásához importálja a következő kódtárakat: 

   ```python
   import matplotlib.pyplot as plt
   import seaborn as sns
   import pandas as pd
   ```

3. Mivel a nyers adatmennyiség parkettás formátumú, a Spark-környezettel közvetlenül is lehívhatja a fájlt a memóriába DataFrame. Hozzon létre egy Spark-DataFrame az adatoknak a nyílt adatkészletek API-n keresztüli beolvasásával. Itt az olvasási tulajdonságok Spark DataFrame *sémáját* használjuk az adattípusok és a séma kikövetkeztetni.

   ```python
   from azureml.opendatasets import NycTlcYellow
   from datetime import datetime
   from dateutil import parser

   end_date = parser.parse('2018-06-06')
   start_date = parser.parse('2018-05-01')
   nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
   df = nyc_tlc.to_spark_dataframe()
   ```

4. Az adat olvasása után egy kezdeti szűrést szeretnénk végrehajtani az adatkészlet tisztításához. Eltávolíthatjuk a szükségtelen oszlopokat, és hozzáadhatjuk azokat az oszlopokat, amelyek fontos információkat nyernek ki. Emellett kiszűrjük az adathalmazon belüli rendellenességeket is.

   ```python
   # Filter the dataset 
   from pyspark.sql.functions import *

   filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                               .filter((df.passengerCount > 0)\
                                   & (df.tipAmount >= 0)\
                                   & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                   & (df.tripDistance > 0) & (df.tripDistance <= 200))

   filtered_df.createOrReplaceTempView("taxi_dataset")
   ```

## <a name="analyze-data"></a>Adatok elemzése
Adatelemzőként számos eszköz érhető el, amely segít az adatokból származó elemzések kinyerésében. Az oktatóanyagnak ebben a részében az Azure szinapszis Analytics notebookokon elérhető néhány hasznos eszközt ismertetjük. Ebben az elemzésben szeretnénk megismerni azokat a tényezőket, amelyek magasabb szintű taxis tippeket adnak a kiválasztott időszakra vonatkozóan.

### <a name="apache-spark-sql-magic"></a>Apache Spark SQL Magic 
Először az Azure szinapszis notebook használatával Apache Spark SQL-és Magic-parancsokat is végrehajtjuk a feltáró adatok elemzését. A lekérdezésünk után az eredményeket a beépített képesség használatával fogjuk megjeleníteni ```chart options``` . 

1. Hozzon létre egy új cellát a notebookon belül, és másolja a következő kódot. Ennek a lekérdezésnek a használatával azt szeretnénk megérteni, hogy az átlagos tipp mennyisége milyen mértékben módosult a kiválasztott időszakban. A lekérdezés segítséget nyújt a további hasznos megállapítások azonosításában is, beleértve a napi minimális/maximális tippet, valamint az átlagos Viteldíj összegét.
   
   ```sql
   %%sql
   SELECT 
       day_of_month
       , MIN(tipAmount) AS minTipAmount
       , MAX(tipAmount) AS maxTipAmount
       , AVG(tipAmount) AS avgTipAmount
       , AVG(fareAmount) as fareAmount
   FROM taxi_dataset 
   GROUP BY day_of_month
   ORDER BY day_of_month ASC
   ```

2. A lekérdezés befejeződése után a diagram nézetre való váltással megjelenítheti az eredményeket. Ez a példa egy vonalas diagramot hoz létre a ```day_of_month``` mező kulcsként és értékként való megadásával ```avgTipAmount``` . Miután elvégezte a beállításokat, kattintson az **alkalmaz** gombra a diagram frissítéséhez. 
   
## <a name="visualize-data"></a>Adatok vizualizációja
A beépített jegyzetfüzet-diagramok lehetőségein kívül népszerű, nyílt forráskódú kódtárakat is használhat saját vizualizációk létrehozásához. A következő példákban a Seaborn és a Matplotlib használatát fogjuk használni. Ezeket gyakran használják Python-kódtárak az adatvizualizációhoz. 

> [!Note]
> Alapértelmezés szerint az Azure szinapszis Analytics minden Apache Spark készlete gyakran használt és alapértelmezett kódtárakat tartalmaz. Megtekintheti a kódtárak teljes listáját az [Azure szinapszis Runtime](../spark/apache-spark-version-support.md) dokumentációjában. Emellett ahhoz, hogy a harmadik féltől származó vagy helyileg létrehozott kódokat az alkalmazásai számára is elérhetővé tegye, [telepíthet egy könyvtárat](../spark/apache-spark-azure-portal-add-libraries.md) az egyik Spark-készletre.

1. Ahhoz, hogy a fejlesztés egyszerűbbé és olcsóbb legyen, felbontáscsökkentéséhez az adatkészletet. A beépített Apache Spark mintavételezési funkciót fogjuk használni. Emellett a Seaborn és a Matplotlib is szükség van egy Panda DataFrame vagy NumPy tömbre. A pandák DataFrame beszerzéséhez használja a ```toPandas()``` parancsot a DataFrame konvertálásához.

   ```python
   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   # The charting package needs a Pandas DataFrame or NumPy array to do the conversion
   sampled_taxi_pd_df = sampled_taxi_df.toPandas()
   ```

1. Szeretnénk megérteni a tippek eloszlását az adatkészletben. A Matplotlib használatával létrehozunk egy hisztogramot, amely megjeleníti a tip-mennyiség és a darabszám eloszlását. A terjesztés alapján láthatjuk, hogy a tippek a $10-nál kisebb vagy azzal egyenlő értékekre vannak elferdítve.

   ```python
   # Look at a histogram of tips by count by using Matplotlib

   ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
   ax1.set_title('Tip amount distribution')
   ax1.set_xlabel('Tip Amount ($)')
   ax1.set_ylabel('Counts')
   plt.suptitle('')
   plt.show()
   ```

   ![Tippek hisztogramja.](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. A következő lépés az adott utazáshoz tartozó tippek és a hét napja közötti kapcsolat megismerése. A Seaborn használatával hozzon létre egy mezőt, amely összegzi a hét egyes napjainak tendenciáit. 

   ```python
   # View the distribution of tips by day of week using Seaborn
   ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
   ax.set_title('Tip amount distribution per day')
   ax.set_xlabel('Day of Week')
   ax.set_ylabel('Tip Amount ($)')
   plt.show()

   ```
   ![A tippek napi eloszlását ábrázoló gráf.](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. A miénk egy másik hipotézise az lehet, hogy pozitív kapcsolat áll fenn az utasok száma és a taxi teljes összege között. A kapcsolat ellenőrzéséhez futtassa a következő kódot egy olyan Box-ábra létrehozásához, amely az egyes utasok számának elosztását szemlélteti. 
   
   ```python
   # How many passengers tipped by various amounts 
   ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
   ax2.set_title('Tip amount by Passenger count')
   ax2.set_xlabel('Passenger count')
   ax2.set_ylabel('Tip Amount ($)')
   ax2.set_ylim(0,30)
   plt.suptitle('')
   plt.show()
   ```
   ![A Box-habverőt ábrázoló diagram.](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Végül a viteldíj összege és a tipp összege közötti kapcsolatot szeretnénk megérteni. Az eredmények alapján láthatjuk, hogy több észrevétel is van, ahol az emberek nem tip. Ugyanakkor a teljes viteldíj és a tip-mennyiség közötti pozitív kapcsolat is látható.
   
   ```python
   # Look at the relationship between fare and tip amounts

   ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
   ax.set_title('Tip amount by Fare amount')
   ax.set_xlabel('Fare Amount ($)')
   ax.set_ylabel('Tip Amount ($)')
   plt.axis([-2, 80, -2, 20])
   plt.suptitle('')
   plt.show()
   ```
   ![Tipp összegének Scatter ábrázolása](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>A Spark-példány leállítása

Miután befejezte az alkalmazás futtatását, állítsa le a notebookot az erőforrások felszabadításához. Zárjuk be a fület, vagy a jegyzetfüzet alján található állapot panelen válassza a **Befejezés munkamenet** elemet.

## <a name="see-also"></a>Lásd még

- [Áttekintés: Apache Spark az Azure szinapszis Analytics szolgáltatásban](apache-spark-overview.md)
- [Gépi tanulási modell létrehozása Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Következő lépések

- [Azure Synapse Analytics](../index.yml)
- [Apache Spark hivatalos dokumentáció](https://spark.apache.org/docs/latest/)