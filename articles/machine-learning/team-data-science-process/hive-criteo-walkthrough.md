---
title: Azure HDInsight Hadoop-fürt használata 1 TB-adatkészletben – csoportos adatelemzési folyamat
description: A csoportos adatelemzési folyamat használata egy HDInsight Hadoop-fürtöt alkalmazó teljes körű forgatókönyvhöz, amely nagy (1 TB) nyilvánosan elérhető adatkészletet használó modell készítésére és üzembe helyezésére használható.
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
ms.openlocfilehash: e66bd0a4e56f63185d8361355d6cf8e0e29bc30b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93305937"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>A csoportos adatelemzési folyamat működés közben – egy Azure HDInsight Hadoop-fürt használata 1 TB-os adatkészleten

Ez a bemutató azt mutatja be, hogyan használható a csoportos adatelemzési folyamat egy teljes körű forgatókönyvben egy [Azure HDInsight Hadoop-fürttel](https://azure.microsoft.com/services/hdinsight/) , amely a nyilvánosan elérhető [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) -adatkészletek egyikéről tárolja, tárja fel, ismerteti a funkciók mérnökeit, és levonja a mintavételi adatokat. A Azure Machine Learning használatával bináris besorolási modellt hoz létre ezen az adathalmazon. Azt is bemutatja, hogyan teheti közzé ezen modellek egyikét webszolgáltatásként.

Az útmutatóban ismertetett feladatok elvégzéséhez egy IPython-jegyzetfüzet is használható. Azok a felhasználók, akik szeretnék kipróbálni ezt a megközelítést, a [kaptár ODBC-kapcsolódási témakör használatával](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) tekintse meg a Criteo útmutatót.

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Criteo adatkészlet leírása
A Criteo adatok Egy kattintásos előrejelzési adatkészlet, amely 370 GB gzip tömörített TSV-fájlokkal rendelkezik (~ 1,3 TB tömörítetlen), amely több mint 4 300 000 000 rekordot tartalmaz. A [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/)által elérhetővé tett adatok közül 24 nap múlva kerül sor. Az adatszakértők kényelme érdekében az általunk a kísérlethez elérhetővé tett adatmennyiség kibontva.

Az adatkészlet minden rekordja 40 oszlopot tartalmaz:

* az első oszlop egy címke oszlop, amely azt jelzi, hogy egy felhasználó rákattint egy **hozzáadásra** (1. érték), vagy nem kattint az egyikre (0. érték)
* a következő 13 oszlop numerikus, és
* az utolsó 26 kategorikus oszlop

Az oszlopok névtelenek, és a "Col1" (a Label oszlop esetében) és a "Col40" (az utolsó kategorikus oszlop esetében) sorozata szerepel a következőben:.

Íme egy részlet az adatkészletből származó két észrevétel (sorok) első 20 oszlopáról:

> `Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20`
>
> `0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb`
>
> `0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb`

Az adatkészlet numerikus és kategorikus oszlopaiban hiányoznak értékek. A hiányzó értékek kezelésére szolgáló egyszerű módszert ismertetjük. Az adatokra vonatkozó további részleteket a rendszer a kaptár-táblákba való tároláskor vizsgálja meg.

**Definíció:** *átkattintási arány (CTR):* ez a metrika az adatokban található kattintások százalékos arányát határozza meg. Ebben a Criteo-adatkészletben a CTR körülbelül 3,3% vagy 0,033.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Példák az előrejelzési feladatokra
Ebben az útmutatóban két példa előrejelzési problémát ismertetünk:

1. **Bináris besorolás**: azt jelzi, hogy a felhasználó egy hozzáadásra kattintott-e:

   * 0. osztály: nincs kattintás
   * 1. osztály: kattintson a
2. **Regresszió**: előre jelezheti, hogy az ad-ra való kattintás valószínűsége a felhasználói funkciókból származik.

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>HDInsight Hadoop-fürt beállítása adatelemzéshez
> [!NOTE]
> Ez a lépés általában egy **rendszergazdai** feladat.

Állítsa be az Azure adatelemzési környezetét a prediktív elemzési megoldások HDInsight-fürtökkel való létrehozásához három lépésben:

1. [Storage-fiók létrehozása](../../storage/common/storage-account-create.md): ezt a Storage-fiókot használjuk az Azure Blob Storageban tárolt adattároláshoz. Itt tárolódnak a HDInsight-fürtökben használt adathalmazok.
2. [Azure HDInsight Hadoop-fürtök testreszabása az adatelemzéshez](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md): Ez a lépés egy Azure HDInsight Hadoop-fürtöt hoz létre, amely az összes csomóponton telepítve van a 64 bites Anaconda Python 2,7. A HDInsight-fürt testreszabásakor két fontos lépést kell végrehajtania (lásd ebben a témakörben).

   * Az 1. lépésben létrehozott Storage-fiók összekapcsolása a HDInsight-fürttel a létrehozásakor. Ez a Storage-fiók a fürtön belül feldolgozható adatok elérésére szolgál.
   * A fürt fő csomópontjának távoli elérésének engedélyezése a létrehozása után. Jegyezze meg az itt megadott távelérési hitelesítő adatokat (a fürt létrehozásakor megadott hitelesítő adatoktól eltérő): végezze el az alábbi eljárásokat.
3. [Azure Machine learning Studio (klasszikus) munkaterület létrehozása](../classic/create-workspace.md): ez a Azure Machine learning munkaterület a gépi tanulási modellek létrehozásához használatos a HDInsight-fürtön végzett kezdeti adatfelderítés és-vizsgálat után.

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>Nyilvános forrásból származó adatok beolvasása és felhasználása
A [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) adatkészletet úgy érheti el, ha a hivatkozásra kattint, elfogadja a használati feltételeket, és megadja a nevet. Itt látható egy pillanatkép:

![Criteo feltételeinek elfogadása](./media/hive-criteo-walkthrough/hLxfI2E.png)

A **letöltéshez** kattintson a Tovább gombra, hogy többet tudjon meg az adatkészletről és annak rendelkezésre állásáról.

Az adat egy [Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-dotnet.md) -helyen található: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/ . Az "wasb" az Azure Blob Storage helyére utal.

1. Az Azure Blob Storage-ban tárolt adattárolók három almappából állnak a kibontott adatsorokból.

   1. Az almappa *nyers/darabszám/* az első 21 nap adat – a 00 napról a \_ 20 napra \_
   2. Az almappa *nyers/betanítása/* egynapos adat, 21. nap \_
   3. Az almappa *nyers/teszt/* két napos adatból áll, a \_ 22. és a 23. nap \_
2. A nyers gzip-adatok a fő mappában ( *RAW/* as day_NN. gz) is elérhetők, ahol az NN a 00 és 23 közötti értékre mutat.

A jelen útmutató későbbi részében a kaptárak létrehozásakor a további, a helyi letöltéseket nem igénylő adatelérést, megismerést és modellezést is elmagyarázhatja.

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>Bejelentkezés a fürt átjárócsomóponthoz
A fürt átjárócsomóponthoz való bejelentkezéshez használja a [Azure Portal](https://ms.portal.azure.com) a fürt megkereséséhez. Kattintson a bal oldali HDInsight Elephant ikonra, majd kattintson duplán a fürt nevére. Navigáljon a **konfiguráció** lapra, kattintson duplán az oldal alján található Csatlakozás ikonra, és amikor a rendszer kéri, adja meg a távelérési hitelesítő adatait a fürt átjárócsomóponthoz.

A fürt átjárócsomóponthoz a következőképpen néz ki:

![Bejelentkezés a fürtbe](./media/hive-criteo-walkthrough/Yys9Vvm.png)

A bal oldalon a "Hadoop Command line" (az adatfeltárási igásló). Figyelje meg a következő két hasznos URL-címet: "Hadoop fonal állapota" és "Hadoop Name Node". A szál állapotának URL-címe a feladatok előrehaladását mutatja, a név csomópont URL-címe pedig részletesen ismerteti a fürtkonfiguráció beállításait.

Most már be van állítva, és készen áll a bemutató első részének megkezdésére: az adatelemzés a kaptár használatával, és az adatAzure Machine Learningra való felkészülés.

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a> Struktúra-adatbázis és-táblák létrehozása
A Criteo adatkészlethez tartozó kaptár-táblázatok létrehozásához nyissa meg a ***Hadoop parancssort*** a fő csomópont asztalán, és írja be a kaptár könyvtárat a parancs beírásával.

```console
cd %hive_home%\bin
```

> [!NOTE]
> Futtassa az ebben a bemutatóban található összes struktúra-parancsot a kaptár bin/Directory parancssorból. Ez automatikusan gondoskodik az összes elérésiút-problémáról. A "kaptár-könyvtár promptja", a "kaptárak/könyvtár promptja" és a "Hadoop parancssor" kifejezéssel szinonimaként is használhatja.
>
> [!NOTE]
> A kaptár-lekérdezések végrehajtásához az egyik mindig a következő parancsokat használhatja:
>
> `cd %hive_home%\bin`
> `hive`

Miután a struktúra REPL megjelenik egy "kaptár >" jellel, egyszerűen kivágja és beilleszti a lekérdezést a végrehajtásához.

A következő kód létrehozza a "criteo" adatbázist, majd négy táblát hoz létre:

* egy *táblázat a* \_ 00 – 20. napon \_ , a
* a 21. napra épülő, *a vonat adatkészletként használandó táblázat* \_
* két tábla, amely a 22. és a 23. napon alapuló *tesztelési adatkészletekhez használható* \_ \_ .

Ossza szét a teszt adatkészletet két különböző táblára, mert az egyik nap a nyaralás. A cél annak megállapítása, hogy a modell képes-e a nyaralás és a nem szünnap közötti különbségek észlelésére a kattintások díjszabása alapján.

A szkript [minta&#95;struktúra&#95;hozza létre&#95;criteo&#95;adatbázis&#95;és&#95;táblákat. a HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) itt jelenik meg a kényelem érdekében:

```hiveql
CREATE DATABASE IF NOT EXISTS criteo;
DROP TABLE IF EXISTS criteo.criteo_count;
CREATE TABLE criteo.criteo_count (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

DROP TABLE IF EXISTS criteo.criteo_train;
CREATE TABLE criteo.criteo_train (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

DROP TABLE IF EXISTS criteo.criteo_test_day_22;
CREATE TABLE criteo.criteo_test_day_22 (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

DROP TABLE IF EXISTS criteo.criteo_test_day_23;
CREATE TABLE criteo.criteo_test_day_23 (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';
```

Az összes táblázat külső, így az Azure Blob Storage (wasb) helyeire mutathat.

**A kaptár-lekérdezések két módon hajthatók végre:**

* **A kaptár repl parancssor használata**: az első a "kaptár" parancs kiadása, és a lekérdezés másolása és beillesztése a kaptár repl parancssorba:

  ```console
  cd %hive_home%\bin
  hive
  ```

     Most a REPL parancssorában a lekérdezés kivágása és beillesztése végrehajtja azt.
* **Lekérdezések mentése fájlba és a parancs végrehajtása**: a második az, hogy mentse a lekérdezéseket egy ". HQL" fájlba ([minta&#95;struktúra&#95;hozzon létre&#95;criteo&#95;database&#95;és&#95;Tables. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)), majd adja ki a következő parancsot a lekérdezés végrehajtásához:

  ```console
  hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql
  ```

### <a name="confirm-database-and-table-creation"></a>Adatbázis és tábla létrehozásának megerősítése
Ezután erősítse meg az adatbázis létrehozását a következő paranccsal a kaptár bin/Directory parancssorból:

```console
hive -e "show databases;"
```

Ez a következőket biztosítja:

```output
criteo
default
Time taken: 1.25 seconds, Fetched: 2 row(s)
```

Ezzel megerősíti az új adatbázis ("criteo") létrehozását.

Ha szeretné megtekinteni, hogy milyen táblákat hozott létre, egyszerűen adja ki a parancsot a kaptár bin/Directory parancssorból:

```output
hive -e "show tables in criteo;"
```

Ekkor a következő kimenetnek kell megjelennie:

```ouput
criteo_count
criteo_test_day_22
criteo_test_day_23
criteo_train
Time taken: 1.437 seconds, Fetched: 4 row(s)
```

## <a name="data-exploration-in-hive"></a><a name="exploration"></a> Adatelemzés a struktúrában
Most már készen áll arra, hogy elvégezze a struktúra alapszintű adatfelderítését. Első lépésként megszámolja a példák számát a vonaton, és teszteli az adattáblákat.

### <a name="number-of-train-examples"></a>A betanítási példák száma
A [minta&#95;struktúrájának tartalma&#95;count&#95;a&#95;table&#95;-példákat. a HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) itt láthatók:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_train;
```

A következő hozamok:

```output
192215183
Time taken: 264.154 seconds, Fetched: 1 row(s)
```

Azt is megteheti, hogy az alábbi parancsot is kiadja a kaptár/könyvtár parancssorból:

```console
hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql
```

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>A két tesztelési adatkészletben szereplő tesztelési példák száma
Most a két tesztelési adatkészletben szereplő példák számát számítja ki. A [minta&#95;struktúrájának tartalma&#95;count&#95;criteo&#95;test&#95;Day&#95;22&#95;table&#95;példákat. a HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) a következők:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_22;
```

A következő hozamok:

```output
189747893
Time taken: 267.968 seconds, Fetched: 1 row(s)
```

A szokásos módon a következő parancs kiadásával is meghívhatja a parancsfájlt a kaptár bin/könyvtár parancssorból:

```console
hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql
```

Végül megvizsgálja a tesztelési adatkészletben a 23. napon alapuló tesztelési példák számát \_ .

A parancs ehhez hasonlóan jelenik meg (lásd: [sample&#95;kaptár&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;példák. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_23;
```

Ez a következőket biztosítja:

```output
178274637
Time taken: 253.089 seconds, Fetched: 1 row(s)
```

### <a name="label-distribution-in-the-train-dataset"></a>Címke eloszlása a vonat adatkészletében
A vonat adatkészletében a címke eloszlása érdekes. Ennek megtekintéséhez tekintse meg a [minta&#95;struktúra tartalmát&#95;criteo&#95;label&#95;distribution&#95;train&#95;table. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

```hiveql
SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;
```

Ez a címke eloszlását eredményezi:

```output
1       6292903
0       185922280
Time taken: 459.435 seconds, Fetched: 2 row(s)
```

A pozitív feliratok százalékos aránya körülbelül 3,3% (az eredeti adatkészlettel összhangban).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Néhány numerikus változó hisztogram eloszlása a vonat adatkészletében
A kaptár natív "hisztogram \_ numerikus" függvényével megtudhatja, hogy a numerikus változók eloszlása milyen módon néz ki. Itt látható a [minta&#95;struktúra&#95;criteo&#95;hisztogram&#95;numerikus. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

```hiveql
SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
    (SELECT
    histogram_numeric(col2, 20) as col2_hist
    FROM
    criteo.criteo_train
    ) a
    LATERAL VIEW explode(col2_hist) exploded_table as hist;
```

Ez a következőket eredményezi:

```output
26      155878415
2606    92753
6755    22086
11202   6922
14432   4163
17815   2488
21072   1901
24113   1283
27429   1225
30818   906
34512   723
38026   387
41007   290
43417   312
45797   571
49819   428
53505   328
56853   527
61004   160
65510   3446
Time taken: 317.851 seconds, Fetched: 20 row(s)
```

Az oldalirányú nézet – a struktúra kibontása a struktúrában úgy szolgálja ki, hogy a szokásos lista helyett egy SQL-szerű kimenetet hozzon létre. Ebben a táblázatban az első oszlop a bin központnak felel meg, a második pedig a raktárhely gyakorisága.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Néhány numerikus változó hozzávetőleges százalékos értéke a vonat adatkészletében
A numerikus változók esetében is a hozzávetőleges százalékos érték kiszámítása. A kaptár natív "percentilis \_ kb" értéke a számunkra. A [minta&#95;struktúra&#95;criteo&#95;hozzávetőleges&#95;percentilis. a HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) a következők:

```hiveql
SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;
```

A következő hozamok:

```output
1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
Time taken: 564.953 seconds, Fetched: 1 row(s)
```

A percentilis eloszlása szorosan kapcsolódik a numerikus változók hisztogram-eloszlásához általában.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>A vonat adatkészletében található egyes kategorikus oszlopok egyedi értékeinek megkeresése
Ha folytatja az adatfeltárást, megkeresi az egyes kategorikus oszlopokat, az általuk elvégezhető egyedi értékek száma. Ehhez a [minta&#95;struktúra tartalmának megjelenítése&#95;criteo&#95;egyedi&#95;értékeket&#95;kategorikus. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

```hiveql
SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;
```

A következő hozamok:

```output
19011825
Time taken: 448.116 seconds, Fetched: 1 row(s)
```

A Col15 egyedi értékeket 19M. Az ilyen nagy dimenziós kategorikus változók kódolásához a naiv módszerek (például az "egy gyors kódolás") használata nem valósítható meg. Különösen fontos, hogy a [tanulás](/archive/blogs/machinelearning/big-learning-made-easy-with-counts) hatékony és robusztus módszere az, hogy a probléma hatékonyan kezelhető legyen.

Végül tekintse meg a többi kategorikus oszlop egyedi értékeinek számát is. A [minta&#95;struktúra&#95;criteo&#95;egyedi&#95;értékek&#95;több&#95;kategorikus. a HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) a következők:

```hiveql
SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
FROM criteo.criteo_train;
```

A következő hozamok:

```output
30935   15200   7349    20067   3
Time taken: 1933.883 seconds, Fetched: 1 row(s)
```

Vegye figyelembe, hogy a Col20 kivételével az összes többi oszlopnak sok egyedi értéke van.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>A betanítási adatkészletben található kategorikus változók számainak együttes előfordulása

A kategorikus változók számának eloszlása is érdekes. Ez a [minta&#95;struktúra&#95;criteo&#95;párosított&#95;kategorikus&#95;Counts](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql)kód használatával határozható meg. HQL:

```hiveql
SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;
```

Adja meg fordított sorrendben a számlálást az előfordulásuk szerint, és tekintse meg az első 15-et ebben az esetben. Ez a következőt adja nekünk:

```output
ad98e872        cea68cd3        8964458
ad98e872        3dbb483e        8444762
ad98e872        43ced263        3082503
ad98e872        420acc05        2694489
ad98e872        ac4c5591        2559535
ad98e872        fb1e95da        2227216
ad98e872        8af1edc8        1794955
ad98e872        e56937ee        1643550
ad98e872        d1fade1c        1348719
ad98e872        977b4431        1115528
e5f3fd8d        a15d1051        959252
ad98e872        dd86c04a        872975
349b3fec        a52ef97d        821062
e5f3fd8d        a0aaffa6        792250
265366bf        6f5c7c41        782142
Time taken: 560.22 seconds, Fetched: 15 row(s)
```

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a> Az adatkészletek legördülő mintája Azure Machine Learning
Miután megvizsgálta az adatkészleteket, és bebizonyította, hogyan végezze el az ilyen típusú feltárást bármely változóhoz (beleértve a kombinációkat is), az adathalmazok mintáját, hogy az Azure Machine Learning modelljei is felépíthető legyenek. Ne felejtse el, hogy a probléma témája a következő: adott példa attribútumai (a Col2-Col40 származó szolgáltatások értékei), Tippelje meg, hogy a Col1 0 (nincs kattintás) vagy 1 (kattintson).

A kiépítési és tesztelési adatkészletek az eredeti méret 1%-ában való lebontásához használja a kaptár natív RAND () függvényét. A következő szkript, [minta&#95;struktúra&#95;criteo&#95;felbontáscsökkentéséhez&#95;train&#95;adatkészlet. a HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) ezt a betanítási adatkészletet adja meg:

```hiveql
CREATE TABLE criteo.criteo_train_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

---Now downsample and store in this table

INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;
```

A következő hozamok:

```output
Time taken: 12.22 seconds
Time taken: 298.98 seconds
```

A szkript [minta&#95;struktúra&#95;criteo&#95;felbontáscsökkentéséhez&#95;teszt&#95;nap&#95;22&#95;adatkészlet. a HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) végzi a tesztelési adatokat, \_ 22. nap:

```hiveql
--- Now for test data (day_22)

CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;
```

A következő hozamok:

```output
Time taken: 1.22 seconds
Time taken: 317.66 seconds
```

Végül a szkript [minta&#95;struktúra&#95;criteo&#95;felbontáscsökkentéséhez&#95;teszt&#95;nap&#95;23&#95;adatkészlet. a HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) végzi a tesztelési adatokat, \_ 23. nap:

```hiveql
--- Finally test data day_23
CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;
```

A következő hozamok:

```output
Time taken: 1.86 seconds
Time taken: 300.02 seconds
```

Ezzel készen áll arra, hogy felhasználja a modelleket az Azure Machine Learning-ban modellek létrehozásához használt minta-és tesztelési adatkészleteket.

Az utolsó fontos összetevő a Azure Machine Learningre való áttérés előtt, amely a Count (darabszám) táblára vonatkozik. A következő alszakaszban részletesen tárgyalja a Count táblát.

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a> A Count tábla rövid megbeszélése
Ahogy látta, számos kategorikus változó magas dimenzióját rendelkezik. Az útmutatóban egy olyan hatékony technikát kell [megtanulnia](/archive/blogs/machinelearning/big-learning-made-easy-with-counts) , amely a változók hatékony és robusztus módon történő kódolását mutatja be. A technikával kapcsolatos további információkat a megadott hivatkozás tartalmaz.

>[!NOTE]
>Ebben az útmutatóban a hangsúly a Count Tables használatával történik a nagy dimenziós kategorikus funkciók kompakt ábrázolásának létrehozásához. Ez nem az egyetlen módszer a kategorikus funkciók kódolására; a más módszerekkel kapcsolatos további információkért az érdeklődő felhasználók [egy-egy gyors kódolást](https://en.wikipedia.org/wiki/One-hot) és a [funkció-kivonatolást](https://en.wikipedia.org/wiki/Feature_hashing)is igénybe vehetnek.
>

A Count (darabszám) típusú táblák létrehozásához használja a RAW/Count mappában található adatértékeket. A modellezés szakaszban a felhasználók megmutatják, hogyan hozhatja létre ezeket a számlálási táblákat a teljesen új kategorikus funkciókhoz, vagy használhat egy előre elkészített számlálási táblázatot a felderítésekhez. A következőkben a "előre elkészített számlálási táblák" kifejezés használata esetén a megadott számú táblázatot használjuk. A táblázatok elérésének részletes ismertetését a következő szakaszban találja.

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a> Modell létrehozása Azure Machine Learning
Azure Machine Learning modell-létrehozási folyamata az alábbi lépéseket követi:

1. [Adatok beolvasása a kaptár tábláiból Azure Machine Learning](#step1)
2. [Hozza létre a kísérletet: tisztítsa meg az adattárat, és tegye elérhetővé a Count Tables szolgáltatást.](#step2)
3. [A modell kiépítése, betanítása és pontozása](#step3)
4. [A modell értékelése](#step4)
5. [A modell közzététele webes szolgáltatásként](#step5)

Most már készen áll a modellek Azure Machine Learning Studióban való létrehozására. A rendszer a fürtben található kaptár-táblákként menti a levett mintául szolgáló adatkészleteket. Az adatelemzéshez használja az Azure Machine Learning **adatimportálási** modult. A fürt Storage-fiókjához való hozzáféréshez szükséges hitelesítő adatokat a következő cikkben találhatja meg.

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a> 1. lépés: adatok beolvasása a kaptár tábláiból Azure Machine Learning az adatok importálása modul használatával, majd kiválaszthatja a Machine learning-kísérletekhez
Először válassza ki a **+ új**  ->  **kísérletből** álló  ->  **üres kísérletet**. Ezután a bal felső sarokban található **keresőmezőbe** írja be az "adatok importálása" kifejezést. Húzza az **adatimportálási** modult a kísérleti vászonra (a képernyő középső részére), és használja a modult az adateléréshez.

Így néz ki az adatok **importálása** , miközben az adatok beolvasása a kaptár táblából történik:

![Adatkérések importálása](./media/hive-criteo-walkthrough/i3zRaoj.png)

Az **adatimportálási** modul esetében a grafikában megadott paraméterek értékei csak a szükséges értékekre mutatnak. Íme néhány általános útmutató az **adatimportálási** modul paramétereinek kitöltéséhez.

1. Az **adatforrás** "kaptár-lekérdezés" választása
2. A **struktúra adatbázisának lekérdezése** mezőben egy egyszerű kijelölés * az \_ adatbázis neve <\_ . a \_ tábla \_ neve> – elég.
3. **Hcatalog-kiszolgáló URI-ja**: Ha a fürt "ABC", akkor ez egyszerűen: https: \/ /ABC.azurehdinsight.net
4. **Hadoop felhasználói fiók neve**: a fürt üzembe helyezésének időpontjában kiválasztott Felhasználónév. (Nem a távelérés felhasználóneve!)
5. **Hadoop felhasználói fiók jelszava**: a fürt üzembe helyezésének időpontjában kiválasztott Felhasználónév jelszava. (Nem a távelérés jelszava!)
6. **Kimeneti adatokat tároló hely**: válassza az "Azure" lehetőséget.
7. **Azure Storage-fiók neve**: a fürthöz társított Storage-fiók
8. **Azure Storage-fiók kulcsa**: a fürthöz társított Storage-fiók kulcsa.
9. **Azure-tároló neve**: Ha a fürt neve "ABC", akkor ez egyszerűen "ABC", általában.

Miután az **importálási adat** beolvassa az adatokat (a modul zöld pipa jelenik meg), mentse az adatokat adatkészletként (az Ön által választott névvel). Ez a következőképpen néz ki:

![Adatmentés az adatmentésbe](./media/hive-criteo-walkthrough/oxM73Np.png)

Kattintson a jobb gombbal az **adatimportálási** modul kimeneti portjára. Ez felfedi a **Mentés mint adatkészlet** **lehetőséget és egy** vizualizációs lehetőséget. Ha rákattint a **vizualizáció lehetőségre, a megjeleníti** az adatok 100 sorát, valamint egy jobb oldali panelt, amely néhány összefoglaló statisztikához hasznos. Az adatmentéshez egyszerűen válassza a **Mentés adatkészletként** lehetőséget, és kövesse az utasításokat.

A Machine learning-kísérletben használható mentett adatkészlet kiválasztásához keresse meg a következő ábrán látható **keresőmezőt** használó adatkészleteket. Ezután egyszerűen írja be azt a nevet, amelyet az adatkészlet részben adott meg, és húzza az adatkészletet a fő panelre. A fő panelre való eldobása kiválasztja azt a gépi tanulási modellezésben való használatra.

![Húzzon át egy adatkészletet a fő panelre](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Végezze el ezt a betanítási és a tesztelési adatkészletek esetében is. Ne felejtse el használni az erre a célra megadott adatbázisnév és táblanév nevét is. Az ábrán használt értékek kizárólag illusztrációs célokat szolgálnak. * *
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a> 2. lépés: kísérlet létrehozása Azure Machine Learningban a kattintások és a kattintások előrejelzéséhez
A Azure Machine Learning Studio (klasszikus) kísérlet így néz ki:

![Machine Learning kísérlet](./media/hive-criteo-walkthrough/xRpVfrY.png)

Most vizsgáljuk meg a kísérlet legfontosabb összetevőit. Húzza a mentett betanítási és tesztelési adatkészleteket a kísérleti vászonra először.

#### <a name="clean-missing-data"></a>Hiányzó adatok törlése
A **tiszta hiányzó** adatmodul a neve azt sugallja, hogy a felhasználó által megadott módon törli a hiányzó adatforrásokat. Tekintse meg ezt a modult a következő megjelenítéséhez:

![Hiányzó adatértékek törlése](./media/hive-criteo-walkthrough/0ycXod6.png)

Itt választhatja ki az összes hiányzó értéket 0-ra. Más lehetőségek is megtalálhatók, amelyek a modul legördülő menüjében láthatók.

#### <a name="feature-engineering-on-the-data"></a>Az adatkezelési funkciók tervezése
A nagyméretű adathalmazok néhány kategorikus funkciójának több millió egyedi értéke lehet. A naiv módszerekkel, például az ilyen nagy dimenziós kategorikus funkciók ábrázolására szolgáló, egy gyors kódolással teljes mértékben nem valósítható meg. Ez a bemutató azt mutatja be, hogyan használhatók a Count funkciók a beépített Azure Machine Learning modulok használatával a nagy dimenziós kategorikus változók kompakt ábrázolásának létrehozásához. A végeredmény a modell kisebb mérete, a gyorsabb betanítási idő és a teljesítménnyel kapcsolatos mérőszámok, amelyek hasonlóak más módszerek használatához.

##### <a name="building-counting-transforms"></a>Számlálási átalakítások kiépítése
A Count-funkciók létrehozásához használja a Azure Machine Learningban elérhető, **számlálási transzformációs** modult. A modul így néz ki:

![Számlálási átalakító modul tulajdonságainak összeállítása ](./media/hive-criteo-walkthrough/e0eqKtZ.png)
 ![ számlálási átalakító modul](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> Az **oszlopok számlálása** mezőben adja meg azokat az oszlopokat, amelyeknek a számát szeretné elvégezni. Ezek jellemzően a nagy dimenziós kategorikus oszlopok. Ne feledje, hogy a Criteo adatkészletének 26 kategorikus oszlopa van: a Col15 és a Col40 között. Itt számoljon mindet, és adja meg az indexeit (15 – 40 vesszővel elválasztva, az ábrán látható módon).
>

Ha a modult a MapReduce módban szeretné használni (a nagyméretű adatkészletek esetében megfelelő), akkor hozzá kell férnie egy HDInsight Hadoop-fürthöz (ezt a szolgáltatást a szolgáltatások feltárásához is használni lehet erre a célra) és annak hitelesítő adataival. Az előző ábrák bemutatják, hogy a kitöltött értékek hogyan néznek ki (az illusztrációhoz megadott értékeket cserélje le a saját használati esetéhez kapcsolódóan).

![Modul paramétereinek](./media/hive-criteo-walkthrough/05IqySf.png)

Az előző ábrán látható, hogyan adhatja meg a bemeneti blob helyét. Ennek a helynek a számára fenntartott adatai vannak a Count Tables szolgáltatásban.

Ha ez a modul fut, mentse az átalakítót később, és kattintson a jobb gombbal a modulra, és válassza a **Mentés másként alakítás** lehetőséget:

!["Mentés Átalakításként" lehetőség](./media/hive-criteo-walkthrough/IcVgvHR.png)

A fentebb bemutatott kísérleti architektúrában a "ytransform2" adatkészlet pontosan megfelel egy mentett Count átalakítónak. A kísérlet hátralevő részében feltételezhető, hogy az olvasó egy **számlálási átalakítási** modult használt egyes adatmennyiségek létrehozásához, és ezeket a számlálásokat használhatja a számlálási funkciók létrehozásához a vonaton és a tesztelési adatkészleteken.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>A betanítási és tesztelési adatkészletek részét képező szolgáltatások számának kiválasztása
Miután a Count-átalakító elkészült, a felhasználó kiválaszthatja, hogy milyen szolgáltatásokat kíván szerepeltetni a saját betanítási és tesztelési adatkészletekben a **Count Table paraméterek módosítása** modul használatával. A teljesség kedvéért ez a modul jelenik meg. Az egyszerűség kedvéért azonban valójában nem használja a kísérlet során.

![Count tábla paramétereinek módosítása](./media/hive-criteo-walkthrough/PfCHkVg.png)

Ebben az esetben, ahogy az látható, a rendszer a log-oddsot használja, és figyelmen kívül hagyja a back off oszlopot. Olyan paramétereket is beállíthat, mint például a garbage bin küszöbértéke, hány pszeudo-korábbi példát kell hozzáadni a simításhoz, illetve hogy a Laplacian-zajt vagy sem használja. Mindezek speciális funkciók, és meg kell jegyezni, hogy az alapértelmezett értékek jó kiindulási pont azoknak a felhasználóknak, akik az ilyen típusú szolgáltatások generációja számára újak.

##### <a name="data-transformation-before-generating-the-count-features"></a>Adatátalakítás a Count-funkciók létrehozása előtt
Most a hangsúly egy fontos pont, amely a betanítási és tesztelési célú adatelemzést is tartalmazza, mielőtt ténylegesen generálja a Count funkcióit. A Count átalakító alkalmazása előtt két **végrehajtási R-parancsfájl** -modult használunk.

![R-parancsfájl-modulok végrehajtása](./media/hive-criteo-walkthrough/aF59wbc.png)

Itt látható az első R-szkript:

![Első R-szkript](./media/hive-criteo-walkthrough/3hkIoMx.png)

Ez az R-szkript átnevezi az oszlopokat a "Col1" névre a "Col40" névre. Ennek az az oka, hogy a Count átalakító ennek a formátumnak a nevét várja.

A második R-szkript kiegyensúlyozza a pozitív és negatív osztályok (1. és 0. osztály) közötti eloszlást a negatív osztály mintavételezésével. Az R-szkript itt azt mutatja be, hogyan teheti meg ezt:

![Második R-parancsfájl](./media/hive-criteo-walkthrough/91wvcwN.png)

Ebben az egyszerű R-szkriptben a "POS \_ negatív \_ arány" a pozitív és a negatív osztályok közötti egyensúly beállítására szolgál. Ez azért fontos, mert az osztályok adategyensúlyhiányának javítása általában a besorolási problémákra vonatkozó teljesítménybeli előnyökkel jár, ha az osztály eloszlása elferdítve van (ebben az esetben ne felejtse el, hogy 3,3% pozitív osztály és 96,7% negatív osztály).

##### <a name="applying-the-count-transformation-on-our-data"></a>Az adatgyűjtési szám átalakításának alkalmazása
Végezetül az **átalakítási** modul alkalmazása lehetőségre kattintva alkalmazhatja a Count átalakításokat a vonaton és a tesztelési adatkészleteken. Ez a modul a mentett Count átalakítót egy bemenetként, a vonat vagy a tesztelési adatkészletek közül a másik bemenetként, az adatokat pedig a Count funkcióval adja vissza. Itt látható:

![Átalakítási modul alkalmazása](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>A Count-funkciók kinézetének részlete
Ez a tanulságos, hogy láthatja, mi a különbség a jelen esetünkben. Íme egy részlet a következőből:

![Szolgáltatások száma](./media/hive-criteo-walkthrough/FO1nNfw.png)

Ez a részlet azt mutatja, hogy az oszlopokban megjelenő oszlopok esetében az érintett backoffs mellett a Count és a log szorzó is megjelenik.

Most már készen áll arra, hogy az átalakított adatkészleteket használó Azure Machine Learning modellt hozzon létre. A következő szakaszban bemutatjuk, hogyan teheti ezt meg.

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a> 3. lépés: a modell kiépítése, betanítása és pontozása

#### <a name="choice-of-learner"></a>A tanuló kiválasztása
Először ki kell választania egy tanulót. A tanulónk kétosztályos, megnövelt döntési fát használ. A tanuló alapértelmezett beállításai a következők:

![Two-Class a döntési fa paramétereinek kiemelése](./media/hive-criteo-walkthrough/bH3ST2z.png)

A kísérlethez válassza az alapértelmezett értékeket. Az alapértelmezett értékek az értelmesek, és jó módszer a gyors alapkonfigurációk beszerzésére a teljesítményre. Ha az alaptervet választja, a teljesítmény növelése a paraméterek elindításával végezhető el.

#### <a name="train-the-model"></a>A modell betanítása
A betanításhoz egyszerűen hívja meg a **betanítási modell** modulját. A két bemenet a Two-Class növelt döntési fa tanuló és a vonat adatkészlete. Ez itt látható:

![Betanítási modell modul](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>A modell pontozása
Ha már rendelkezik egy betanított modellel, készen áll a tesztelési adatkészlet értékelésére és a teljesítmény kiértékelésére. Ehhez használja az alábbi ábrán látható **pontszám modell** modult, valamint a **modell kiértékelése** modult:

![A Score model (Modell montozása) modul](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a> 4. lépés: a modell kiértékelése
Végezetül elemezni kell a modell teljesítményét. A két osztályos (bináris) besorolási probléma esetén a megfelelő mérték a AUC. A görbe megjelenítéséhez csatlakoztassuk a **pontszám modell** modult egy **kiértékelési modell** modulhoz. Ha **a** **modell kiértékelése** modulban a vizualizáció elemre kattint, az a következőhöz hasonló ábrát eredményez:

![A modul BDT modell kiértékelése](./media/hive-criteo-walkthrough/0Tl0cdg.png)

A bináris (vagy két osztály) besorolási problémák esetében az előrejelzési pontosság jó mértéke a görbe alatti terület (AUC). A következő szakaszban a modell használatával kapcsolatos eredményeket láthatjuk a tesztelési adatkészleten. Kattintson a jobb gombbal a **modell kiértékelése** modul kimeneti portjára, majd **jelenítse** meg a vizualizációt.

![Modell kiértékelése modul megjelenítése](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a> 5. lépés: a modell közzététele webszolgáltatásként
Egy Azure Machine Learning-modell webszolgáltatásként való közzétételének lehetősége, amely a lehető legszélesebb körben elérhető. Ezt követően bárki megteheti, hogy meghívja a webszolgáltatást olyan bemeneti adatokkal, amelyekhez előrejelzések szükségesek, és a webszolgáltatás a modellt használja az előrejelzések visszaadásához.

Először mentse a betanított modellt egy betanított modell-objektumként. ehhez kattintson a jobb gombbal a **vonat modell** modulra, és használja a **Save as betanított modell** lehetőséget.

Következő lépésként hozzon létre bemeneti és kimeneti portokat a webszolgáltatás számára:

* a bemeneti portok ugyanolyan formában fogadják az adatokat, mint a szükséges adatok.
* a kimeneti port a pontszámmal ellátható címkéket és a kapcsolódó valószínűségeket adja vissza.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Néhány sornyi adat kiválasztása a bemeneti porthoz
Célszerű az **alkalmazás SQL-transzformációs** modulját használni, hogy csak 10 sort válasszon a bemeneti port adatként. Az itt látható SQL-lekérdezés használatával válassza ki a bemeneti porthoz tartozó adatsorokat:

![Bemeneti port adatok](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webszolgáltatás
Most már készen áll egy kis kísérlet futtatására, amely a webszolgáltatás közzétételére használható.

#### <a name="generate-input-data-for-webservice"></a>Bemeneti adatok előállítása a webszolgáltatáshoz
Nulladik lépésként, mivel a Count tábla nagy méretű, néhány sornyi vizsgálati adatból áll, és kimeneti adatokat állít elő belőle a Count funkcióival. Ez a kimenet a webszolgáltatások bemeneti adatformátuma lehet, ahogy az itt látható:

![BDT bemeneti adatok létrehozása](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> A bemeneti adatok formátumához használja a **Count Képtulajdonság** modul kimenetét. Ha a kísérlet futása befejeződött, mentse a kimenetet a **Count Képtulajdonság** -modulból adatkészletként. Ezt az adatkészletet a webszolgáltatásban lévő bemeneti adatokhoz használja a rendszer.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Pontozási kísérlet a webszolgáltatások közzétételéhez
Először is az alapvető struktúra egy **pontszám-modell** modul, amely elfogadja a betanított modell objektumát és az előző lépésekben generált bemeneti adatok néhány sorát a **Count Képtulajdonság** modul használatával. Használja az "Oszlopok kiválasztása az adatkészletben" lehetőséget a mutatós címkék és a pontszám valószínűségek kivetítéséhez.

![Adathalmaz oszlopainak kiválasztása](./media/hive-criteo-walkthrough/kRHrIbe.png)

Figyelje meg, hogyan használhatók az adatkészletek "szűrés" adatainak **kiválasztására szolgáló adatkészlet modul Select oszlopai** . A tartalom itt látható:

![Szűrés az adatkészlet kijelölése oszlopban](./media/hive-criteo-walkthrough/oVUJC9K.png)

A kék bemeneti és kimeneti portok beszerzéséhez egyszerűen kattintson a **webszolgáltatás előkészítése** lehetőségre a jobb alsó sarokban. A kísérlet futtatása lehetővé teszi a webszolgáltatások közzétételét is: kattintson a jobb alsó sarokban található **Közzététel webszolgáltatás** ikonra, amely itt látható:

![Webszolgáltatás közzététele](./media/hive-criteo-walkthrough/WO0nens.png)

Miután közzétette a webszolgáltatást, a rendszer átirányítja egy olyan oldalra, amely így néz ki:

![Webszolgáltatás irányítópultja](./media/hive-criteo-walkthrough/YKzxAA5.png)

Figyelje meg a webszolgáltatások két hivatkozását a bal oldalon:

* A **kérelem/válasz** szolgáltatás (vagy az RR) egyetlen előrejelzéshez készült, és az, amit ebben a workshopban használtak.
* A kötegelt **végrehajtási** szolgáltatás (BES) a Batch-előrejelzésekhez használatos, és megköveteli, hogy az előrejelzések elvégzéséhez használt bemeneti adatok az Azure Blob Storageban legyenek.

A link **kérelemre/válaszra** kattintva egy olyan oldalra kerül, amely a C#, a Python és az R használatával előre definiált kódot ad nekünk. Ez a kód kényelmesen használható a webszolgáltatásra irányuló hívások kezdeményezéséhez. A lapon az API-kulcsot kell használni a hitelesítéshez.

Érdemes átmásolni ezt a Python-kódot egy új cellába a IPython notebookon.

Itt látható a megfelelő API-kulccsal rendelkező Python-kód szegmense.

![Python-kód](./media/hive-criteo-walkthrough/f8N4L4g.png)

Az alapértelmezett API-kulcsot a webszolgáltatás API-kulcsa váltotta fel. Ha a IPython notebookon ezen a cellán a **Futtatás** gombra kattint, az a következő választ eredményezi:

![IPython válasz](./media/hive-criteo-walkthrough/KSxmia2.png)

A Python-parancsfájl JSON-keretrendszerében feltett két tesztelési példát a "pontozásos címkék", a mutatós valószínűségek című űrlapon kaphat választ. Ebben az esetben az alapértelmezett értékek úgy lettek kiválasztva, hogy az előre definiált kód (0 az összes numerikus oszlopra, a "value" karakterláncot pedig az összes kategorikus oszlop esetében) adja meg.

Az útmutató bemutatja, hogyan kezelheti a nagyméretű adatkészleteket Azure Machine Learning használatával. Elindított egy terabájtos adatmennyiséget, létrehozta az előrejelzési modellt, és üzembe helyezte azt webszolgáltatásként a felhőben.