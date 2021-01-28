---
title: Javaslatok készítése az Apache Mahout használatával az Azure HDInsight
description: Ismerje meg, hogy az Apache Mahout Machine learning-kódtár segítségével hogyan hozhatja elő a filmkészítési javaslatokat a HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: c31ffaf094801bdd49e5800bd338a15d8b8315f6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946489"
---
# <a name="generate-recommendations-using-apache-mahout-in-azure-hdinsight"></a>Javaslatok készítése az Apache Mahout használatával az Azure HDInsight

Ismerje meg, hogyan hozhatja elő a filmkészítési javaslatokat az [Apache Mahout](https://mahout.apache.org) Machine learning-kódtár és az Azure HDInsight használatával.

A Mahout egy [gépi tanulási](https://en.wikipedia.org/wiki/Machine_learning) könyvtár a Apache Hadoop számára. A Mahout az adatfeldolgozáshoz szükséges algoritmusokat, például a szűrést, a besorolást és a fürtözést is tartalmazza. Ebben a cikkben egy ajánlási motort használ, amely a barátok által készített filmek alapján készít javaslatokat.

A HDInsight-ben található Mahout verziójával kapcsolatos további információkért lásd: [HDInsight-verziók és Apache Hadoop-összetevők](../hdinsight-component-versioning.md).

## <a name="prerequisites"></a>Előfeltételek

Egy Apache Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="understanding-recommendations"></a>A javaslatok ismertetése

A Mahout által biztosított függvények egyike egy ajánlási motor. Ez a motor a, a és a `userID` `itemId` `prefValue` (az elemre vonatkozó beállítások) formátumban fogadja el az adatmennyiséget. A Mahout Ezután elvégezheti a közös előfordulási elemzést annak megállapításához, hogy az adott *elemhez előnyben részesített felhasználók is rendelkeznek-e ezekkel a többi elemmel*. A Mahout ezután meghatározza a hasonló elemekkel rendelkező felhasználókat, amelyekkel ajánlásokat lehet tenni.

A következő munkafolyamat egy egyszerűsített példa, amely a Movie-adatbevitelt használja:

* **Közös előfordulás**: Joe, Alice és Bob minden tetszett *Star Wars*, *a birodalom* visszatér, és *visszaküldi a jedit*. A Mahout határozza meg, hogy a fenti filmek egyike, a másik kettőhöz hasonlóan a felhasználók is hasonlóak.

* **Együttes előfordulás**: Bob és Alice is tetszett *a Phantom árnyak*, *a klónok támadása* és *a Sith bosszúja*. A Mahout határozza meg, hogy az előző három filmhez hasonló felhasználók is szeretik a három filmet.

* **Hasonlósági javaslat**: mivel Joe tetszett az első három film, a Mahout olyan filmeket keres, amelyeket mások hasonló beállításokkal kedveltek, de Joe nem figyelt (tetszett/értékelt). Ebben az esetben a Mahout *a Phantom árnyak*, a *klónok támadását* és *a Sith bosszúját* javasolja.

### <a name="understanding-the-data"></a>Az adatgyűjtés ismertetése

A [GroupLens Research](https://grouplens.org/datasets/movielens/) kényelmesen, a Mahout-mel kompatibilis formátumban biztosítja a filmek minősítési információit. Ezek az adatkészletek a fürt alapértelmezett tárolójában érhetők el `/HdiSamples/HdiSamples/MahoutMovieData` .

Két fájl `moviedb.txt` és `user-ratings.txt` . A `user-ratings.txt` fájl az elemzés során használatos. A a `moviedb.txt` használatával felhasználóbarát szöveges adatokat biztosít az eredmények megtekintésekor.

A ben található adat a,, `user-ratings.txt` és és rendszer struktúrája, `userID` `movieID` `userRating` `timestamp` amely azt jelzi, hogy az egyes felhasználók mennyire értékelték a filmet. Íme egy példa az adatmennyiségre:

```output
    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596
```

## <a name="run-the-analysis"></a>Az elemzés futtatása

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A következő parancs használatával futtassa a javaslati feladatot:

    ```bash
    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
    ```

> [!NOTE]  
> A feladat végrehajtása több percet is igénybe vehet, és több MapReduce-feladatot is futtathat.

## <a name="view-the-output"></a>A kimenet megtekintése

1. Miután a feladatok befejeződik, a következő paranccsal tekintheti meg a generált kimenetet:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    A kimenet a következőképpen jelenik meg:

    ```output
    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
    ```

    Az első oszlop a `userID` . A (z) "[" és "]" fájlban szereplő értékek a következők `movieId` : `recommendationScore` .

2. Az ajánlásokkal együtt a kimenetet is használhatja a moviedb.txthoz. Először másolja a fájlokat helyileg a következő parancsok használatával:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Ez a parancs a kimeneti adatokat egy **recommendations.txt** nevű fájlba másolja át az aktuális könyvtárban, valamint a film adatfájljaival együtt.

3. A következő parancs használatával hozzon létre egy Python-parancsfájlt, amely a javaslatok kimenetében szereplő adatokat keresi meg.

    ```bash
    nano show_recommendations.py
    ```

    A szerkesztő megnyitásakor használja a következő szöveget a fájl tartalmának megfelelően:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Nyomja le a **CTRL-X**, az **Y** billentyűt, és végül **adja meg** az adatmentést.

4. Futtassa a Python-szkriptet. Az alábbi parancs feltételezi, hogy abban a könyvtárban van, ahol az összes fájl le lett töltve:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Ez a parancs a 4. felhasználói AZONOSÍTÓhoz generált ajánlásokat vizsgálja.

   * A **user-ratings.txt** fájl a minősítés alatt álló filmek beolvasására szolgál.

   * A **moviedb.txt** fájl a filmek nevének beolvasására szolgál.

   * A **recommendations.txt** az ehhez a felhasználóhoz tartozó filmkészítési javaslatok beolvasására szolgál.

     A parancs kimenete az alábbi szöveghez hasonló:

        ```output
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        ```

## <a name="delete-temporary-data"></a>Ideiglenes adatmennyiség törlése

A Mahout-feladatok nem távolítják el a feladat feldolgozása során létrehozott ideiglenes adatok körét. A `--tempDir` példában a paramétert úgy adja meg, hogy a rendszer elkülönítse az ideiglenes fájlokat egy adott elérési útra az egyszerű törlés érdekében. Az ideiglenes fájlok eltávolításához használja a következő parancsot:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Ha újra futtatni szeretné a parancsot, törölnie kell a kimeneti könyvtárat is. A következő paranccsal törölheti a könyvtárat:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatja a Mahout-t, Fedezze fel a HDInsight-on tárolt adatkezelés egyéb módszereit:

* [Apache Hive HDInsight](hdinsight-use-hive.md)
* [MapReduce a HDInsight](hdinsight-use-mapreduce.md)
