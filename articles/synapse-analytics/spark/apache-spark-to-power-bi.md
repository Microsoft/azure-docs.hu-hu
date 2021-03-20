---
title: Azure Synapse Studio-jegyzetfüzetek
description: Ez az oktatóanyag áttekintést nyújt arról, hogyan hozhat létre Power BI irányítópultot a Apache Spark és egy kiszolgáló nélküli SQL-készlet használatával.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 53fb256d3a0fba1cc10cafb239ff7f7bb21ac4eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98121073"
---
# <a name="tutorial-create-a-power-bi-report-using-apache-spark-and-azure-synapse-analytics"></a>Oktatóanyag: Power BI-jelentés létrehozása Apache Spark és az Azure szinapszis Analytics használatával

A szervezeteknek gyakran nagy mennyiségű adattal kell feldolgozniuk, mielőtt a legfontosabb üzleti szereplőkkel foglalkoznak. Ebből az oktatóanyagból megtudhatja, hogyan használhatja fel az Azure szinapszis Analytics integrált élményeit az adatfeldolgozásra Apache Spark és később a végfelhasználók számára Power BI és kiszolgáló nélküli SQL használatával.

## <a name="before-you-begin"></a>Előkészületek
- Az [Azure szinapszis Analytics-munkaterület](../quickstart-create-workspace.md) egy ADLS Gen2 Storage-fiókkal, amely alapértelmezett tárolóként van konfigurálva. 
- Power BI munkaterület és Power BI Desktop az adatmegjelenítéshez. Részletekért lásd: [Power bi munkaterület létrehozása](/power-bi/service-create-the-new-workspaces) és [Power bi-asztal telepítése](https://powerbi.microsoft.com/downloads/)
- Társított szolgáltatás az Azure szinapszis Analytics és Power BI munkaterületek összekapcsolásához. Részletekért lásd: [Power bi munkaterületre mutató hivatkozás](../quickstart-power-bi.md)
- Kiszolgáló nélküli Apache Spark készlet a szinapszis Analytics-munkaterületen. Részletekért lásd: [kiszolgáló nélküli Apache Spark készlet létrehozása](../quickstart-create-apache-spark-pool-studio.md)
  
## <a name="download-and-prepare-the-data"></a>Az adatgyűjtés letöltése és előkészítése
Ebben a példában a Apache Spark használatával végezheti el a New York-i taxi-tippek adatainak elemzését. Az adatokat az [Azure Open-adatkészletek](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)segítségével érheti el. Az adatkészlet ezen részhalmaza a Yellow taxi-utakról tartalmaz információkat, beleértve az egyes utazásokkal, a kezdési és befejezési időponttal és a hellyel, a díjszabással és egyéb érdekes attribútumokkal kapcsolatos információkat.

1. A következő sorok futtatásával hozzon létre egy Spark-dataframe a kód egy új cellába való beillesztésével. Ezzel lekéri az adatokat a megnyitott adatkészletek API-n keresztül. Az összes ilyen adatmennyiség körülbelül 1 500 000 000 sort hoz létre. A következő kódrészlet a start_date és a end_date használatával alkalmaz egy olyan szűrőt, amely egyetlen hónapot ad vissza.
   
   ```python
    from azureml.opendatasets import NycTlcYellow
    from dateutil import parser
    from datetime import datetime

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
   ```
2. A Apache Spark SQL használatával létrejön egy NycTlcTutorial nevű adatbázis. Ezt az adatbázist fogjuk használni az adatfeldolgozás eredményeinek tárolásához.
   ```python
   %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS NycTlcTutorial")
   ```
3. Ezután a Spark dataframe-műveleteket fogjuk használni az adatok feldolgozásához. A következő kódban a következő átalakításokat hajtjuk végre:
   1. Nem szükséges oszlopok eltávolítása.
   2. Kiugró/helytelen értékek eltávolítása szűréssel.
   3. Új funkciók, például ```tripTimeSecs``` ```tipped``` további elemzések létrehozása.
    ```python
    from pyspark.sql.functions import unix_timestamp, date_format, col, when

    taxi_df = filtered_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                    , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                    , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                    , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                    , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                    , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                    )\
                            .filter((filtered_df.passengerCount > 0) & (filtered_df.passengerCount < 8)\
                                    & (filtered_df.tipAmount >= 0) & (filtered_df.tipAmount <= 25)\
                                    & (filtered_df.fareAmount >= 1) & (filtered_df.fareAmount <= 250)\
                                    & (filtered_df.tipAmount < filtered_df.fareAmount)\
                                    & (filtered_df.tripDistance > 0) & (filtered_df.tripDistance <= 100)\
                                    & (filtered_df.rateCodeId <= 5)
                                    & (filtered_df.paymentType.isin({"1", "2"})))
    ```
4. Végül a Apache Spark metódus használatával mentjük a dataframe ```saveAsTable``` . Ez lehetővé teszi, hogy később lekérdezéssel és a kiszolgáló nélküli SQL-készletekkel kapcsolódjon ugyanahhoz a táblához.
  ```python
     taxi_df.write.mode("overwrite").saveAsTable("NycTlcTutorial.nyctaxi")
  ```
   
## <a name="query-data-using-serverless-sql-pools"></a>Az adatlekérdezés kiszolgáló nélküli SQL-készletekkel
Az Azure szinapszis Analytics lehetővé teszi, hogy a különböző munkaterület-számítási motorok adatbázisokat és táblákat osszanak meg kiszolgáló nélküli Apache Spark készletei és kiszolgáló nélküli SQL-készlete között. Ez a szinapszis [megosztott metaadat-kezelési](../metadata/overview.md) képességein keresztül érhető el. Ennek eredményeképpen a Spark által létrehozott adatbázisok és a parketta által támogatott táblázatok láthatóvá válnak a munkaterület kiszolgáló nélküli SQL-készletében.

Apache Spark tábla lekérdezése a kiszolgáló nélküli SQL-készlettel:
   1. Miután mentette a Apache Spark táblát, váltson át az **adat** lapra.
   
   2. A **munkaterületek** területen keresse meg az imént létrehozott Apache Spark táblát, és válassza az **új SQL-parancsfájl** lehetőséget, majd **válassza a Top 100 sort**. 
      
      :::image type="content" source="../spark/media/apache-spark-power-bi/query-spark-table-with-sql.png" alt-text="SQL-lekérdezés." border="true":::

   3. Folytathatja a lekérdezés pontosítását, vagy akár az SQL-diagramok beállításainak használatával is megjelenítheti az eredményeket.

## <a name="connect-to-power-bi"></a>Csatlakozás a Power BI-hoz
Ezután a kiszolgáló nélküli SQL-készletet a Power BI munkaterülethez fogjuk kapcsolni. Miután csatlakozott a munkaterülethez, Power BI jelentéseket hozhat létre közvetlenül az Azure szinapszis Analyticsből, valamint a Power BI Desktopból.

>[!Note]
> Mielőtt elkezdené, be kell állítania egy társított szolgáltatást a [Power bi munkaterületre](../quickstart-power-bi.md) , és le kell töltenie a [Power bi Desktopot](/power-bi/service-create-the-new-workspaces).  

Kiszolgáló nélküli SQL-készlet kapcsolódása Power BI munkaterülethez:

1.  Navigáljon a **Power bi adatkészletek** lapra, és válassza a **+ új adatkészlet** lehetőséget. A parancssorból töltse le a. pbids fájlt az adatforrásként használni kívánt SQL Analytics-adatbázisból. 
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-datasets.png" alt-text="Power BI adatkészletek." border="true":::

2.  Adatkészlet létrehozásához nyissa meg az Power BI Desktop fájlt. A fájl megnyitása után kapcsolódjon az SQL Server-adatbázishoz a **Microsoft-fiók** és az **Importálás** lehetőség használatával. 
   

## <a name="create-a-power-bi-report"></a>Power BI-jelentés létrehozása
1. A Power BI Desktopban mostantól hozzáadhat egy **Key befolyásolási** diagramot a jelentéshez.
   
   1. Húzza az átlagos *tipAmount* oszlopot a tengely **elemzéséhez** .
   
   2. Húzza a **weekdayString**, az átlagos **tripDistance** és az átlagos **tripTimeSecs** oszlopot a **magyarázat** tengelyre. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-desktop.png" alt-text="Power BI Desktop." border="true":::

2. A Power BI asztali Kezdőlap lapon válassza a **Közzététel** lehetőséget, és **mentse** a módosításokat. Adjon meg egy fájlnevet, és mentse a jelentést a *NycTaxiTutorial munkaterületre*.
   
3. Emellett Power BI vizualizációkat is létrehozhat az Azure szinapszis Analytics-munkaterületen belülről. Ehhez navigáljon a **fejlesztés** lapra az Azure szinapszis-munkaterületen, és nyissa meg a Power bi lapot. Innen kiválaszthatja a jelentést, és folytathatja a további vizualizációk kiépítése. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-synapse.png" alt-text="Az Azure szinapszis Analytics-munkaterülete." border="true":::

Ha további információt szeretne arról, hogyan hozhat létre egy adatkészletet kiszolgáló nélküli SQL-kapcsolaton keresztül, és hogyan csatlakozhat a Power BIhoz, látogasson el az oktatóanyagba [Power bi asztalhoz való csatlakozáshoz](../../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md)

## <a name="next-steps"></a>Következő lépések
Az Azure szinapszis Analytics adatvizualizációs funkcióival kapcsolatos további információkért tekintse meg az alábbi dokumentumokat és oktatóanyagokat:
   - [Az adatmegjelenítés kiszolgáló nélküli Apache Spark készletekkel](../spark/apache-spark-data-visualization-tutorial.md)
   - [Apache Spark-készletekkel rendelkező adatvizualizációk áttekintése](../spark/apache-spark-data-visualization.md)