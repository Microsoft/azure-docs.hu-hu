---
title: Adatelemzés a Spark on Azure HDInsight – csoportos adatelemzési folyamat
description: A Spark MLlib Toolkit jelentős gépi tanulási modellezési képességeket biztosít az elosztott HDInsight-környezethez.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1dd82fb00c55e3676929999f204eae8755671038
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93314747"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Az adatelemzés áttekintése az Azure HDInsight Spark használatával

Ez a témakör bemutatja, hogyan használhatja a HDInsight Sparkot olyan általános adatelemzési feladatok elvégzéséhez, mint például az adatok betöltése, a funkciók mérnöki vizsgálata, a modellezés és a modell kiértékelése. A használt adat a 2013 NYC taxi Trip és a fare adatkészlet mintája. A modellek a következők: logisztikai és lineáris regresszió, véletlenszerű erdők és átmenetes kiemeléses fák. A témakörök azt is mutatják be, hogyan tárolhatja ezeket a modelleket az Azure Blob Storage-ban (WASB), és hogyan jelenítheti meg és értékelheti a prediktív teljesítményt. A fejlettebb témakörök ismertetik, hogyan lehet a modelleket több ellenőrzéssel és a Hyper-paraméteres megtakarítással betanítani. Ez az áttekintő témakör azokat a témaköröket is ismerteti, amelyek leírják, hogyan kell beállítani a Spark-fürtöt, hogy végre kell hajtania a megadott forgatókönyvek lépéseit.

## <a name="spark-and-mllib"></a>Spark és MLlib
A [Spark](https://spark.apache.org/) egy nyílt forráskódú, párhuzamos feldolgozást végző keretrendszer, amely támogatja a memóriabeli feldolgozást a Big-adatelemző alkalmazások teljesítményének növelése érdekében. A Spark-feldolgozó motor a sebességre, a könnyű használatra és a kifinomult elemzésekre épül. A Spark memóriában elosztott számítási képességei jó választást biztosítanak a gépi tanulásban és a Graph-számításokban használt iterációs algoritmusok számára. A [MLlib](https://spark.apache.org/mllib/) a Spark skálázható gépi tanulási könyvtára, amely az algoritmikus modellezési képességeket az elosztott környezetbe hozza.

## <a name="hdinsight-spark"></a>HDInsight Spark
A [HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) a nyílt forráskódú Spark Azure által üzemeltetett ajánlata. Emellett támogatja a Spark-fürt **Jupyter PySpark notebookjának** támogatását is, amely Spark SQL interaktív lekérdezéseket futtathat az Azure-blobokban (WASB) tárolt adatátalakításhoz, szűréshez és megjelenítéshez. A PySpark a Spark Python API-ját. A megoldásokat biztosító kódrészletek, valamint a kapcsolódó mintaterületek megjelenítése az itt látható adat megjelenítéséhez a Spark-fürtökön telepített Jupyter-jegyzetfüzetekben. Az ezekben a témakörökben található modellezési lépések olyan kódot tartalmaznak, amely bemutatja, hogyan lehet betanítani, kiértékelni, menteni és használni az egyes modelleket.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Telepítés: Spark-fürtök és Jupyter-jegyzetfüzetek
Ebben az útmutatóban a telepítési lépéseket és kódokat a HDInsight Spark 1,6 használatára vonatkozó útmutatóban ismertetjük. A HDInsight Spark 1,6-es és a Spark 2,0-fürtök esetében azonban Jupyter jegyzetfüzetek is elérhetők. A jegyzetfüzetek leírását és azok hivatkozásait az azokat tartalmazó GitHub-adattár [readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) tartalmazza. Emellett a kód itt és a csatolt jegyzetfüzetekben általános, és minden Spark-fürtön működnie kell. Ha nem a HDInsight Sparkot használja, akkor a fürt beállítása és a felügyeleti lépések némileg eltérnek az itt láthatótól. A Jupyter notebookokra mutató hivatkozásokat a Spark 1,6 (a Jupyter Notebook-kiszolgáló pySpark kernelében kell futtatni) és a Spark 2,0 (a Jupyter Notebook-kiszolgáló pySpark3 kernelében kell futtatni):

### <a name="spark-16-notebooks"></a>Spark 1,6 notebookok
Ezeket a jegyzetfüzeteket a Jupyter notebook Server pySpark kernelében kell futtatni.

- [pySpark-Machine-learning-Information-Science-Spark-adatok-Exploration-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): információt nyújt az adatok feltárásának, modellezésének és pontozásának különböző algoritmusokkal történő végrehajtásáról.
- [pySpark-Machine-learning-adat-tudomány-Spark-Advanced-adat-feltárás-modellezés. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): a notebook-#1 témaköröket tartalmaz, valamint a hiperparaméter finomhangolását és a több érvényesítést használó modellek fejlesztését.
- [pySpark-Machine-learning-adattudományi-Spark-Model-fogyasztás. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): azt mutatja be, hogyan működővé tenni egy mentett modellt a Python használatával HDInsight-fürtökön.

### <a name="spark-20-notebooks"></a>Spark 2,0 notebookok
Ezeket a jegyzetfüzeteket a Jupyter notebook Server pySpark3 kernelében kell futtatni.

- [Spark 2.0-pySpark3-Machine-learning-adat-tudomány-Spark-Advanced-adat-feltárás-modellezés. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ez a fájl információt nyújt arról, hogyan végezheti el az adatfeltárást, modellezést és pontozást a Spark 2,0-fürtökön a New York-i, a NYC-alapú taxi Trip és a fare adathalmaz [használatával.](#the-nyc-2013-taxi-data) Ez a jegyzetfüzet jó kiindulási pont lehet a Spark 2,0-hoz megadott kód gyors feltárásához. A részletes jegyzetfüzetek elemzik a New York-i taxit, és a lista következő jegyzetfüzetében találhatók. Tekintse meg a következő megjegyzéseket, amelyek összehasonlítják ezeket a jegyzetfüzeteket.
- [Spark 2.0 – pySpark3_NYC_Taxi_Tip_Regression. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Ez a fájl azt mutatja be, hogyan végezhető el az adatok huzavona (Spark SQL-és dataframe-műveletek), feltárás, modellezés és pontozás a New York-i taxi Trip és a fare adatkészlet [használatával.](#the-nyc-2013-taxi-data)
- [Spark 2.0 – pySpark3_Airline_Departure_Delay_Classification. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Ez a fájl azt mutatja be, hogyan végezhető el az adatok huzavona (Spark SQL-és dataframe-műveletek), feltárás, modellezés és pontozás az 2011-es és 2012-es, jól ismert légitársaságok által használt időponthoz képest. A (például Szélsebesség, a hőmérséklet, a tengerszint feletti magasság stb.) a modellezés előtt integráltuk a légitársaság adatkészletét, így ezek az időjárási funkciók a modellben is szerepelhetnek.

<!-- -->

> [!NOTE]
> A légitársaság adatkészlete a Spark 2,0 notebookokhoz lett hozzáadva, így jobban szemlélteti a besorolási algoritmusok használatát. Tekintse meg a következő hivatkozásokat a légitársaságok időpontjának indulási adatkészletével és az időjárási adatkészlettel kapcsolatban:
> 
> - A légitársaságok időpontjának kiindulási adatvédelme: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Repülőtér időjárási adatkészletei: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> A New York-i taxik és a légitársaságok repülési késési adatkészletei a Spark 2,0 notebookok esetében akár 10 percet is igénybe vehetnek (a HDI-fürt méretétől függően). A fenti listában szereplő első jegyzetfüzetben az adatfeltárás, a vizualizáció és a ML-modell képzésének számos aspektusát láthatja egy olyan jegyzetfüzetben, amely kevesebb időt vesz igénybe a levett, mint a New York-i (a taxi-és a viteldíj- [)](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)fájlok előzetes csatlakoztatásával Ez a jegyzetfüzet sokkal rövidebb időt vesz igénybe (2-3 perc), és jó kiindulási pont lehet a Spark 2,0-hoz megadott kód gyors feltárásához.

<!-- -->

A Spark 2,0 modell és a pontozási modell felhasználásának operacionalizálási kapcsolatos útmutatásért tekintse meg a [spark 1,6-dokumentum felhasználásáról szóló](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) példát, amely a szükséges lépéseket ismerteti. Ha ezt a példát a Spark 2,0-on szeretné használni, cserélje le a Python-kódrészletet [erre a fájlra](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Előfeltételek

Az alábbi eljárások a Spark 1,6-hez kapcsolódnak. A Spark 2,0-es verziójához használja a korábban leírt és ahhoz csatolt jegyzetfüzeteket.

1. Rendelkeznie kell egy Azure-előfizetéssel. Ha még nem rendelkezik ilyennel, tekintse meg az [Azure ingyenes próbaverziójának beszerzése](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)című témakört.

2. A forgatókönyv végrehajtásához Spark 1,6-fürtre van szüksége. A létrehozáshoz tekintse meg az első [lépések: Apache Spark létrehozása az Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)című témakör utasításait. A fürt típusa és verziója a **fürt típusának kiválasztása** menüben van megadva.

![Fürt konfigurálása](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Egy olyan témakörben, amely bemutatja, hogyan használhatja a Scalat Python helyett a teljes adatelemzési folyamat feladatainak elvégzéséhez, tekintse [meg az adatelemzést a Scala és a Spark on Azure használatával](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>A NYC 2013 taxi-adatközpont
A New York-i taxi Trip-adatok körülbelül 20 GB tömörített, vesszővel tagolt (CSV) fájlokkal rendelkeznek (~ 48 GB tömörítetlen), amely több mint 173 000 000 egyedi utazást és az egyes utazásokhoz fizetett viteldíjat foglal magában. Az egyes utazási rekordok tartalmazzák a felvételi és a lemorzsolódási helyét és időpontját, a névtelen csapkod (illesztőprogram) licencének számát és a Digitális medál (a taxi egyedi azonosítóját). Az adat a 2013-as év összes utazására vonatkozik, és a következő két adatkészletben szerepel minden hónapban:

1. A (z) "trip_data" CSV-fájlok olyan utazási adatokat tartalmaznak, mint például az utasok száma, a beléptetési és a lemorzsolódási pontok, az utazás időtartama és az utazás hossza. Íme néhány példa a rekordokra:

   `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. A (z) "trip_fare" CSV-fájlok tartalmazzák az egyes utazások díjait, például a fizetési típust, a díjszabást, a pótdíjat, az adókat, a tippeket és az autópályadíjat, valamint a teljes fizetett összeget. Íme néhány példa a rekordokra:

   `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Ezeket a fájlokat a 0,1%-os mintát vettük, és az utazási \_ adatok és az utazási \_ viteldíjak CVS-fájljai egyetlen adatkészletbe lettek használva, amely az útmutató bemeneti adatkészlete. Az utazáshoz és az utazási viteldíjhoz való csatlakozás egyedi kulcsa \_ a következő \_ mezőkből áll: emlékérem, Hack- \_ licenc és a pickup \_ DateTime. Az adatkészlet minden rekordja a következő attribútumokat tartalmazza, amelyek a New York-i taxis utakat jelképezik:

| Mező | Rövid leírás |
| --- | --- |
| medál |Anonim taxi-medál (egyedi taxi-azonosító) |
| hack_license |Névtelen Hackney-fuvarozási licenc száma |
| vendor_id |Taxi szállító azonosítója |
| rate_code |A New York-i taxi díjszabása |
| store_and_fwd_flag |Tárolás és továbbítás jelzője |
| pickup_datetime |Dátum & idő kiválasztása |
| dropoff_datetime |Lemorzsolódási dátum & időpontja |
| pickup_hour |Óra kiválasztása |
| pickup_week |Az év hete |
| hét napja |Hétköznap (tartomány 1-7) |
| passenger_count |Utasok száma egy taxiban |
| trip_time_in_secs |Menetidő másodpercben |
| trip_distance |Az utazás távolsága mérföldben |
| pickup_longitude |Hosszúság kiválasztása |
| pickup_latitude |Földrajzi szélesség kiválasztása |
| dropoff_longitude |Lemorzsolódási hosszúsága |
| dropoff_latitude |Lemorzsolódási földrajzi szélesség |
| direct_distance |Közvetlen távolság a felvételi és a lemorzsolódási helyei között |
| payment_type |Fizetési típus (készpénz, hitelkártya stb.) |
| fare_amount |Viteldíj összege itt: |
| felárat |Felárat |
| mta_tax |MTA Metro szállítási adó |
| tip_amount |Tipp mennyisége |
| tolls_amount |Autópályadíjak összege |
| total_amount |Teljes összeg |
| végű |Kiállítva (0/1, nem vagy igen) |
| tip_class |Tipp osztály (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Kód végrehajtása Jupyter-jegyzetfüzetből a Spark-fürtön
A Jupyter Notebook a Azure Portal indítható el. Keresse meg a Spark-fürtöt az irányítópulton, és kattintson rá a fürt felügyeleti oldalának megadásához. A Spark-fürthöz társított jegyzetfüzet megnyitásához kattintson a **fürt irányítópultok**  ->  **Jupyter notebook** elemre.

![Fürt irányítópultok](./media/spark-overview/spark-jupyter-on-portal.png)

Tallózással is megkeresheti ***`https://CLUSTERNAME.azurehdinsight.net/jupyter`*** a Jupyter-jegyzetfüzetek elérését. Cserélje le az URL CLUSTERNAME részét a saját fürt nevére. A jegyzetfüzetek eléréséhez szüksége lesz a rendszergazdai fiók jelszavára.

![Jupyter-jegyzetfüzetek tallózása](./media/spark-overview/spark-jupyter-notebook.png)

Válassza a PySpark lehetőséget egy olyan könyvtár megtekintéséhez, amely néhány példát tartalmaz a PySpark API-t használó előre csomagolt jegyzetfüzetekre. A Spark-témakörhöz tartozó kód mintákat tartalmazó jegyzetfüzetek a [githubon](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) érhetők el

A jegyzetfüzeteket közvetlenül a [githubról](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) töltheti fel a Spark-fürtön lévő Jupyter notebook-kiszolgálóra. A Jupyter kezdőlapján kattintson a képernyő jobb oldalán található **feltöltés** gombra. Megnyílik egy fájlkezelő. Itt beillesztheti a GitHub (nyers tartalom) URL-címét, majd kattintson a **Megnyitás** gombra.

A Jupyter-fájlok listájában megjelenik a fájl neve, és ismét **feltölthető** gomb jelenik meg. Kattintson erre a **feltöltés** gombra. Most importálta a jegyzetfüzetet. Ismételje meg ezeket a lépéseket a többi jegyzetfüzet ezen útmutatóból való feltöltéséhez.

> [!TIP]
> Kattintson a jobb gombbal a böngészőben található hivatkozásokra, és válassza a **hivatkozás másolása** lehetőséget a GitHub nyers tartalom URL-címének lekéréséhez. Ezt az URL-címet beillesztheti a Jupyter upload file Explorer párbeszédpanelbe.
> 
> 

Ezután:

* A jegyzetfüzetre kattintva tekintse meg a kódot.
* Hajtsa végre az egyes cellákat a **SHIFT** billentyű lenyomásával.
* A teljes jegyzetfüzet futtatásához kattintson a **cella**  ->  **futtatása** elemre.
* Használja a lekérdezések automatikus vizualizációját.

> [!TIP]
> A PySpark kernel automatikusan megjeleníti az SQL (HiveQL) lekérdezések kimenetét. A következő lehetőségek közül választhat: több különböző típusú vizualizáció (tábla, torta, vonal, terület vagy sáv) között a jegyzetfüzet **Type (típus** ) gombjának használatával:
>
>

![Logisztikai regressziós ROC-görbe az általános megközelítéshez](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>A következő lépések
Most, hogy beállította a HDInsight Spark-fürtöt, és feltöltötte a Jupyter-jegyzetfüzeteket, készen áll a három PySpark-jegyzetfüzetnek megfelelő témakörökre. Megmutatják, hogyan böngészheti az adatait, és hogyan hozhat létre és használhat modelleket. A speciális adatfeltárási és-modellezési jegyzetfüzet azt mutatja be, hogyan lehet a több érvényesítést, a Hyper-paraméter-eltakarítást és a modell kiértékelését.

**Adatelemzés és-modellezés a sparktal:** Fedezze fel az adatkészletet, és hozza létre, szerzi és értékelje ki a gépi tanulási modelleket a Spark MLlib Toolkit című témakörben található [bináris besorolási és regressziós modellek létrehozásával](spark-data-exploration-modeling.md) .

**Modell felhasználása:** A jelen témakörben létrehozott besorolási és regressziós modellek kiértékelésével kapcsolatban lásd a [Spark által készített gépi tanulási modellek pontszámát és értékelését](spark-model-consumption.md)ismertető témakört.

**Több ellenőrzés és hiperparaméter**: a [fejlett adatfeltárás és-modellezés a Spark](spark-advanced-data-exploration-modeling.md) használatával a modellek több ellenőrzéssel és a Hyper-paraméterrel történő elvetésével is betanítható