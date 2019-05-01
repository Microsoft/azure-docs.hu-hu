---
title: Az Apache Mahout és a HDInsight (SSH) – Azure javaslatok létrehozása
description: Ismerje meg, hogyan használhatja az Apache Mahout machine learning-könyvtárral filmajánlók a HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: d566b57ae12520b9eee26334a67d2e10c05f8040
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64709079"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-apache-hadoop-in-hdinsight-ssh"></a>Filmajánlók Apache Mahout az Apache Hadoop Linux-alapú HDInsight (SSH) használatával

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Ismerje meg, hogyan használható a [Apache Mahout](https://mahout.apache.org) gépi tanulási kódtár, az Azure HDInsight a filmajánlók.

Mahout egy [gépi tanulás](https://en.wikipedia.org/wiki/Machine_learning) library for Apache Hadoop. Mahout adatok feldolgozásával, például szűréshez, osztályozáshoz és fürtözéshez algoritmusokat tartalmaz. Ez a cikk segítségével egy ajánlattételi modul beépítését az ismerősök látott filmek alapuló filmajánlók.

## <a name="prerequisites"></a>Előfeltételek

* Egy HDInsight az Apache Hadoop-fürtöt. Lásd: [HDInsight Linux első lépések](./apache-hadoop-linux-tutorial-get-started.md).

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Az Apache Mahout verziószámozás

A HDInsight létrehozása a Mahout verziójára vonatkozó további információkért lásd: [HDInsight verziók és az Apache Hadoop-összetevők](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Javaslatok megértése

A Mahout által biztosított függvények egyikét egy ajánlattételi modul beépítését. Ez a motor az formátumban adatokat fogad `userID`, `itemId`, és `prefValue` (a beállítás az elem). Mahout hajthat végre megállapítani közös előfordulás elemzési: *előnyben részesíti-e egy elem felhasználók is rendelkeznek ezeket előnyben más elemek*. Mahout majd hasonló-cikk beállítások, amely használható ajánlásokat rendelkező felhasználók határozza meg.

Az alábbi munkafolyamat egy egyszerű példa film adatait használó:

* **közös előfordulás**: János Alice és az összes tetszett Bob *Star Wars*, *vissza is a Empire*, és *visszaküldése az ereje*. Mahout határozza meg, hogy felhasználók, akik ezek filmek közül bármelyik is, mint például a másik kettőt.

* **közös előfordulás**: Bob és Alice is tetszett *a látszólagos támadása*, *támadások, a klónok*, és *, a Sith megtorlás*. Mahout határozza meg, hogy az előző három filmek is kedvelő felhasználók például ezek három filmek.

* **Hasonlósági javaslat**: János az első három filmek tetszett, mert Mahout megvizsgálja filmek, hogy mások hasonló beállítások tetszett, de János rendelkezik nem figyelt (tetszett/névleges). Ebben az esetben a Mahout javasolja *a látszólagos támadása*, *támadások, a klónok*, és *, a Sith megtorlás*.

### <a name="understanding-the-data"></a>Az adatok megismerése

Kényelmesen [GroupLens kutatási](https://grouplens.org/datasets/movielens/) adja meg, amely kompatibilis a mahout használatával formátumú filmek minősítés adatait. Ezek az adatok érhető el a fürt alapértelmezett tárolója, `/HdiSamples/HdiSamples/MahoutMovieData`.

Két fájl `moviedb.txt` és `user-ratings.txt`. A `user-ratings.txt` fájl elemzése során használatos. A `moviedb.txt` felhasználóbarát szöveges információkat biztosít, amikor az eredmények megtekintése.

A felhasználó-ratings.txt szereplő adatok struktúrája `userID`, `movieID`, `userRating`, és `timestamp`, ami azt jelenti, hogy hogyan magas minden felhasználó egy filmet számoljuk el. Íme egy példa az adatokat:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Az elemzés futtatása

Alkalmazás a következő parancsot a fürthöz SSH-kapcsolatot, a javaslat feladat futtatása:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> A feladat befejezése több percet is igénybe vehet, és előfordulhat, hogy több MapReduce-feladatok futtatásához.

## <a name="view-the-output"></a>A kimenet megtekintéséhez

1. A feladat befejezése után használja a következő parancsot a létrehozott kimenet megtekintéséhez:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    A kimenet a következőképpen jelenik meg:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    Az első oszlop a `userID`. Szereplő értékeket a(z) "[" és "]" vannak `movieId`:`recommendationScore`.

2. A kimenetet, és a moviedb.txt használatával további információkat a javaslatokat. Először másolja a fájlokat helyileg az alábbi parancsokkal:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Ezzel a paranccsal a kimeneti adatokat másol egy fájlt **recommendations.txt** az aktuális könyvtárban, a film adatait fájlokkal együtt.

3. A következő paranccsal hozzon létre egy Python-szkriptet, amely megkeresi az movie nevét a javaslatok kimeneti adatait:

    ```bash
    nano show_recommendations.py
    ```

    Amikor megnyílik a szerkesztő, használja a fájl tartalmát a következő szöveget:

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

    Nyomja meg **Ctrl-X**, **Y**, és végül **Enter** az adatok mentéséhez.

4. A Python-szkript futtatásához. Az alábbi parancs feltételezi, hogy a könyvtárban, ahol letöltött összes fájlt:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Ez a parancs ellenőrzi a felhasználó azonosító 4 generált ajánlásokat.

   * A **felhasználói-ratings.txt** fájl filmek értékelése lekérdezéséhez használatos.

   * A **moviedb.txt** fájl nevét a filmek lekérdezéséhez használatos.

   * A **recommendations.txt** olvashatók be a film javaslatok ehhez a felhasználóhoz.

     Ez a parancs kimenete az alábbi szöveghez hasonlít:

       Tibet (1997), a hét évig pontozása 5.0-s = Indiana Jones és az utolsó Crusade (1989), pontszám 5.0-s = Jaws (1975), pontszám 5.0-s = Sensibility (1995), pontszám és az 5.0-s = függetlenség nap (mint az id4 esetében) (1996), pontszám = 5.0 saját segítője Esküvői (1997), pontszám 5.0-s = Jerry Maguire (1996), pontszám 5.0-s = Scream 2 (1997), pontszám 5.0-s = Kill, ideje egy (1996), pontszám = 5.0

## <a name="delete-temporary-data"></a>Törli az ideiglenes adatokat

Feladatok létrehozása a mahout ne távolítsa el a feladat feldolgozása során létrehozott ideiglenes adatok. A `--tempDir` paraméter van megadva a példa feladat elkülöníteni az ideiglenes fájlokat egyszerűen törlésre megadott elérési útra. Az ideiglenes fájlok eltávolításához használja a következő parancsot:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Ha szeretne futtassa újra a parancsot, a kimeneti könyvtárat is törölni kell. A címtár törléséhez használja a következő:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>További lépések

Most, hogy, hogyan használható a Mahout hogyan, Fedezze fel az adatok használata a HDInsight egyéb módjait:

* [Az Apache Hive a HDInsight](hdinsight-use-hive.md)
* [A HDInsight Apache Pig](hdinsight-use-pig.md)
* [A MapReduce és a HDInsight](hdinsight-use-mapreduce.md)