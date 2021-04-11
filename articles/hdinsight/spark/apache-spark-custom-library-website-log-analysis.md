---
title: Webhelyek naplóinak elemzése Python-tárakkal a Sparkban – Azure
description: Ez a jegyzetfüzet azt mutatja be, hogyan elemezheti a naplózási adataikat egyéni kódtár használatával a Spark on Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: d99bf914d3ef746fe328d2447426565fc15e52b4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866318"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Webhelyek naplóinak elemzése egyéni Python-kódtár használatával Apache Spark-fürttel a HDInsight-on

Ez a jegyzetfüzet azt mutatja be, hogyan elemezheti a naplózási adataikat egy egyéni kódtár használatával, Apache Spark a HDInsight. Az általunk használt egyéni könyvtár egy **iislogparser.py** nevű Python-könyvtár.

## <a name="prerequisites"></a>Előfeltételek

Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Nyers adatbázis mentése RDD

Ebben a szakaszban a HDInsight lévő Apache Spark-fürthöz társított [Jupyter](https://jupyter.org) -jegyzetfüzetet használjuk a nyers mintaadatok feldolgozását végző feladatok futtatásához és egy struktúra-táblázatként való mentéséhez. A mintaadatok egy. csv-fájl (hvac.csv), amely alapértelmezés szerint az összes fürtön elérhető.

Miután az adatait Apache Hive táblázatként mentette, a következő szakaszban a BI-eszközök, például a Power BI és a tabló használatával fogunk csatlakozni a kaptár táblához.

1. Egy webböngészőből nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net/jupyter` , ahol a a `CLUSTERNAME` fürt neve.

1. Hozzon létre új notebookot. Válassza az **új**, majd a **PySpark** lehetőséget.

    :::image type="content" source="./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png " alt-text="Új Apache-Jupyter notebook létrehozása" border="true"::: Jegyzetfüzet "Border =" true ":::

1. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. Válassza ki a jegyzetfüzet nevét a tetején, és adjon meg egy rövid nevet.

    :::image type="content" source="./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png " alt-text="Adja meg a" border="true":::"Border =" true "notebook tebook nevét:::

1. Mivel a PySpark kernel használatával létrehozott egy jegyzetfüzetet, nem kell explicit módon létrehoznia a környezeteket. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. Először importálja a forgatókönyvhöz szükséges típusokat. Illessze be a következő kódrészletet egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt.

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. Hozzon létre egy RDD a fürtön már elérhető mintaadatok használatával. A fürthöz tartozó alapértelmezett Storage-fiókban található adatelérési helyhez férhet hozzá `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log` . Hajtsa végre a következő kódot:

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. Egy mintául szolgáló naplóbejegyzés lekérésével ellenőrizheti, hogy az előző lépés sikeresen befejeződött-e.

    ```pyspark
    logs.take(5)
    ```

    Az alábbi szöveghez hasonló kimenetnek kell megjelennie:

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>Naplófájlok elemzése egyéni Python-kódtár használatával

1. A fenti kimenetben az első pár sor tartalmazza a fejléc adatait, és minden fennmaradó sor megegyezik a fejlécben leírt sémával. Az ilyen naplók elemzése bonyolult lehet. Tehát egy egyéni Python-függvénytárat (**iislogparser.py**) használunk, amely sokkal egyszerűbbé teszi az ilyen naplók elemzését. Alapértelmezés szerint ez a könyvtár a HDInsight-on található Spark-fürt része `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py` .

    Ez a tár azonban nem a következő, `PYTHONPATH` ezért nem használható olyan importálási utasítás használatával, mint például: `import iislogparser` . Ennek a könyvtárnak a használatához terjesztenie kell az összes munkavégző csomópontra. Futtassa az alábbi kódrészletet.

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. `iislogparser` egy olyan függvényt ad `parse_log_line` vissza, amely azt adja meg, hogy a `None` naplósor fejléc-e, és az osztály egy példányát adja vissza, `LogLine` Ha egy naplófájlt tartalmaz. A `LogLine` osztály használatával csak a naplósorokat lehet kibontani a RDD:

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. Néhány kibontott naplózási sor beolvasása annak ellenőrzéséhez, hogy a lépés sikeresen befejeződött-e.

    ```pyspark
    logLines.take(2)
    ```

   A kimenetnek az alábbi szöveghez hasonlónak kell lennie:

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. A `LogLine` (z) osztálynak vannak olyan hasznos módszerei, mint például a `is_error()` , amely azt adja vissza, hogy a naplóbejegyzés tartalmaz-e hibakódot. Ebben az osztályban kiszámíthatja a hibák számát a kibontott napló soraiban, majd egy másik fájlba naplózhatja az összes hibát.

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    A kimenetnek állapotnak kell lennie `There are 30 errors and 646 log entries` .

1. Az **Matplotlib** használatával az adatvizualizációt is létrehozhatja. Ha például el szeretné különíteni a hosszú ideig futó kérelmek okát, érdemes lehet megkeresni azokat a fájlokat, amelyek a legtöbb időt igénybe vehetik. Az alábbi kódrészlet lekérdezi az első 25 erőforrást, amely a legtöbb időt a kérelem kiszolgálására vette.

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   Az alábbi szöveghez hasonló kimenetnek kell megjelennie:

    ```output
    [(u'/blogposts/mvc4/step13.png', 197.5),
    (u'/blogposts/mvc2/step10.jpg', 179.5),
    (u'/blogposts/extractusercontrol/step5.png', 170.0),
    (u'/blogposts/mvc4/step8.png', 159.0),
    (u'/blogposts/mvcrouting/step22.jpg', 155.0),
    (u'/blogposts/mvcrouting/step3.jpg', 152.0),
    (u'/blogposts/linqsproc1/step16.jpg', 138.75),
    (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
    (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
    (u'/blogposts/nested/step2.jpg', 126.0),
    (u'/blogposts/adminpack/step1.png', 124.0),
    (u'/BlogPosts/datalistpaging/step2.png', 118.0),
    (u'/blogposts/mvc4/step35.png', 117.0),
    (u'/blogposts/mvcrouting/step2.jpg', 116.5),
    (u'/blogposts/aboutme/basketball.jpg', 109.0),
    (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
    (u'/blogposts/mvc4/step12.png', 106.0),
    (u'/blogposts/linq8/step0.jpg', 105.5),
    (u'/blogposts/mvc2/step18.jpg', 104.0),
    (u'/blogposts/mvc2/step11.jpg', 104.0),
    (u'/blogposts/mvcrouting/step1.jpg', 104.0),
    (u'/blogposts/extractusercontrol/step1.png', 103.0),
    (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
    (u'/blogposts/mvcrouting/step21.jpg', 101.0),
    (u'/blogposts/mvc4/step1.png', 98.0)]
    ```

1. Ezeket az információkat a mintaterület formájában is megjelenítheti. Első lépésként hozzon létre egy ideiglenes tábla **AverageTime**. A tábla ideje szerint csoportosítja a naplókat, hogy az adott időpontban szokatlan késéssel jár-e.

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. Ezután futtathatja a következő SQL-lekérdezést a **AverageTime** tábla összes rekordjának lekéréséhez.

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   A `%%sql` Magic ezt követően `-o averagetime` biztosítja, hogy a lekérdezés kimenete helyileg megmaradjon a Jupyter-kiszolgálón (általában a fürt átjárócsomóponthoz). A kimenet a megadott nevű **Averagetime** [pandák](https://pandas.pydata.org/) dataframe marad.

   A következő képhez hasonló kimenetnek kell megjelennie:

   :::image type="content" source="./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png " alt-text="hdinsight jupyter SQL-lekérdezés kimeneti" border="true":::yter SQL-lekérdezés kimenete "Border =" true ":::

   A Magic szolgáltatással kapcsolatos további információkért `%%sql` lásd: [%% SQL Magic által támogatott paraméterek](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. Most már használhatja a Matplotlib, az adatvizualizációk létrehozásához használt könyvtárat, amely létrehoz egy rajzolt. Mivel a parcellát a helyileg megőrzött **averagetime** dataframe kell létrehozni, a kódrészletnek a mágia kell kezdődnie `%%local` . Ez biztosítja, hogy a kód helyileg fusson a Jupyter-kiszolgálón.

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   A következő képhez hasonló kimenetnek kell megjelennie:

   :::image type="content" source="./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png " alt-text="Apache Spark web log Analysis Plot" border="true":::az EB log Analysis Plot "Border =" true ":::

1. Miután befejezte az alkalmazás futtatását, állítsa le a notebookot az erőforrások felszabadításához. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ez a művelet leállítja és bezárja a jegyzetfüzetet.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a következő cikkekkel:

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)
