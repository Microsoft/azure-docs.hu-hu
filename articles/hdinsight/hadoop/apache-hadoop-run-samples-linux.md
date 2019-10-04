---
title: Az Apache Hadoop MapReduce-példák futhatnak a HDInsight – Azure
description: Ismerkedés a jar-fájlt is tartalmaz HDInsight MapReduce-minták használatával. Csatlakozzon a fürthöz SSH használatával, és a Hadoop paranccsal minta feladatok futtatásához.
keywords: Példa jar hadoop, hadoop példák jar, hadoop mapreduce-példák, mapreduce-példák
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: hrasheed
ms.openlocfilehash: f0251e3926c569b45ebebcd18b98df5af4564443
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706663"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Futtassa a szerepel a HDInsight MapReduce-példák

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Ismerje meg, hogyan futtathat a mellékelt HDInsight az Apache Hadoop MapReduce példák.

## <a name="prerequisites"></a>Előfeltételek

* Egy HDInsight az Apache Hadoop-fürtöt. Lásd: [HDInsight Linux első lépések](./apache-hadoop-linux-tutorial-get-started.md).

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>A MapReduce-példák

**Hely**: A mintákat, a HDInsight-fürtön található `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Tartalom**: A következő minták archívum tartalmazza:

* `aggregatewordcount`: Összesítés mapreduce-programot, amely a szavakat a bemeneti fájlok száma alapján.
* `aggregatewordhist`: Összesítés alapján kiszámítja a szavakat a bemeneti fájlokat hisztogramja mapreduce-programot.
* `bbp`: Mapreduce-programot, Bailey-Borwein-Plouffe használó számítási pi számjegyeket.
* `dbcount`: Egy példa a feladat, amely egy adatbázis tárolt oldalmegtekintés naplók száma.
* `distbbp`: A Pi pontos bits számítási BBP-típusú képletet használó mapreduce-programot.
* `grep`: A mapreduce-programot, amely a bemeneti adatok reguláris kifejezést az egyezések száma.
* `join`: Egy feladat, amely végrehajtja a csatlakozzon rendezve, melyek egyaránt adatkészletek.
* `multifilewc`: Ez a feladat megszámolja a szavak több fájlokból.
* `pentomino`: A mapreduce csempe szóló program pentomino problémák megoldást találhat.
* `pi`: A mapreduce-programot, amely megbecsüli Pi használatával kvázi Monte Carlo metódust.
* `randomtextwriter`: A mapreduce-programot, amely 10 GB-nyi véletlenszerű értéket képviselő szöveges adatok csomópontonként ír.
* `randomwriter`: A mapreduce-programot, amely 10 GB-nyi véletlenszerű adat csomópontonként ír.
* `secondarysort`: Példa egy másodlagos rendezés meghatározása csökkentse fázisa.
* `sort`: A mapreduce-programot, amely az adatokat a véletlenszerű író által írt.
* `sudoku`: Egy sudoku solver.
* `teragen`: Hozhat létre a terasort az adatokat.
* `terasort`: Futtassa a terasort.
* `teravalidate`: Terasort eredményeinek ellenőrzése.
* `wordcount`: A mapreduce-programot, amely a bemeneti fájlok szavakat számolja.
* `wordmean`: A mapreduce-programot, amely megszámolja a szavak a bemeneti fájlok átlagos hossza.
* `wordmedian`: A mapreduce-programot, amely megszámolja a szavak a bemeneti fájlok közepes hossza.
* `wordstandarddeviation`: A mapreduce-programot, amely a szavakat a bemeneti fájlok hossza szórását száma.

**Forráskód**: Ezek a minták forráskódja szerepel-e, a HDInsight-fürt `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

## <a name="run-the-wordcount-example"></a>A wordcount-példa futtatása

1. A HDInsight SSH használatával csatlakozhat. Cserélje le `CLUSTER` a fürt nevére, és adja meg a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Az a `username@#######:~$` kérdés, a következő paranccsal listázhatja a mintákat:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Ez a parancs létrehozza az összetevőlistát a minta az előző szakaszban ebben a dokumentumban.

3. A következő paranccsal egy adott minta segítség kérése. Ebben az esetben a **wordcount** minta:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    A következő üzenet jelenik meg:

        Usage: wordcount <in> [<in>...] <out>

    Ez az üzenet azt jelzi, hogy a forrás dokumentumok megadhat több bemeneti elérési utak. A végső elérési út, a kimenet (szavakat a forrás dokumentumok száma) tárolására.

4. Használja az alábbi a notebookok, Leonardo da Vinci, mintaadatokat is tartalmaz a fürt összes szavak számát:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Ez a feladat olvasása az bemenetének `/example/data/gutenberg/davinci.txt`. Ebben a példában a rendszer tárolt kimeneti `/example/data/davinciwordcount`. A fürt nem a helyi fájlrendszer alapértelmezett tároló mindkét útvonal található.

   > [!NOTE]  
   > A wordcount-mintaszószámlálás súgójában feljegyzett több bemeneti fájllal is megadhatja. Ha például `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` davinci.txt és ulysses.txt szavakat számolja.

5. Miután a feladat befejeződik, használja a következő parancsot a kimenet megtekintéséhez:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Ez a parancs a feladat által létrehozott kimeneti fájlok fűz össze. A kimenet megjeleníti a konzolhoz. A kimenet az alábbi szöveghez hasonló:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Minden sor egy szót, és hogy hányszor történt a bemeneti adatok jelöli.

## <a name="the-sudoku-example"></a>A Sudoku példa

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) egy logikai kirakós kilenc 3 x 3 rácsok áll. Néhány a rács celláinak számok, míg mások üresek, és a cél, hogy az üres cellák megoldani. A fenti hivatkozáson olvashat a Kirakós rendelkezik, de ez a minta célja az, hogy az üres cellák megoldani. Tehát a bemeneti egy fájlt, amely a következő formátumban kell lennie:

* Kilenc oszlopok kilenc sorok
* Minden oszlop tartalmazhat eltolás számként vagy `?` (amely azt jelzi, hogy egy üres cella)
* Cellák szóközzel vannak elválasztva.

Van egy bizonyos módon hozhat létre Sudoku rejtvények; egy szám az oszlop vagy sor nem sikerült megismételni. A HDInsight-fürt megfelelően összeállított nincs egy példa. Ez a következő helyen található `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` , és tartalmazza a következő szöveget:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Ebben a példában a probléma haladjon végig a Sudoku példa, használja a következő parancsot:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Az eredmények megjelennek az alábbi szöveghez hasonló:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>A pi (π) Példa

A pi példa egy statisztikai (látszólagos Monte Carlo) módszer a pi értékét. Pont véletlenszerűen kiválaszt egy egység szögletes kerülnek. A szögletes is tartalmaz egy kör. Annak a valószínűsége, hogy a pontok esik-e a kör megegyeznek a kör területét pi/4-es. A pi értékét 4R értékből becsülhető meg. Az R a belüli belül a szögletes pontok száma a kör pontok aránya. Minél nagyobb a minta használt pontokat, annál jobb a becslés van.

Használja a következő parancsot a minta futtatásához. Ez a parancs a 10,000,000 minták 16 maps használatával megbecsülheti a pi értékét:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Ez a parancs által visszaadott érték hasonlít a **3.14159155000000000000**. Hivatkozások az első 10 tizedesjegyek pi 3.1415926535.

## <a name="10-gb-graysort-example"></a>10 GB-os GraySort-példa

GraySort a teljesítményteszt rendezés. A metrika az a rendezési gyakoriság (TB/perc), amelyek során nagy mennyiségű adatot, általában egy 100 TB-os minimális rendezés érhető el.

Ez a minta egy szerény 10 GB adatot használja, így viszonylag gyorsan futtatható. A MapReduce alkalmazások Owen O'Malley és Arun Murthy által fejlesztett használ. Ezeket az alkalmazásokat az éves általános célú ("Daytona") terabájt rendezési referenciaalap megnyert 2009-arány 0.578 TB/perc (100 TB-os 173 percek alatt). Erről és más rendezési referenciaalapokhoz képest történő további információkért lásd: a [rendezési Referenciaalap](https://sortbenchmark.org/) hely.

Ebben a példában három különböző MapReduce-programok:

* **TeraGen**: A MapReduce-programot, amely létrehozza az adatok rendezéséhez sorok

* **TeraSort**: MapReduce-minták a bemeneti adatokat és segítségével rendezze az adatokat egy teljes rendelés

    TeraSort standard MapReduce rendezni, kivéve az egyéni partitioner. A partitioner mintavételezés N-1 kulcsok, amelyek meghatározzák az egyes csökkentse a tartományok rendezett listáját használja. Ebben az esetben, minden kulcs ilyen mintavételezik a [i-1] < kulcs = < [i] minta érkeznek i csökkentése érdekében. Ez partitioner garantálja, hogy a kimeneteket, csökkentse az i az összes, kevesebb, mint a kimenetét csökkentése i + 1.

* **TeraValidate**: A MapReduce-programot, amely ellenőrzi, hogy a kimenet globálisan rendezett

    A kimeneti könyvtárat hoz létre egy térkép fájlonként, és minden egyes térkép biztosítja, hogy minden egyes kulcs kisebb vagy egyenlő, mint az előzőre. A térkép függvény hoz létre az első és utolsó kulcsok minden egyes fájl rögzíti. A kevesebb funkciót biztosítja, hogy az első kulcsot fájl i nagyobb, mint az utolsó fájl i-1 kulcsa. Problémák az csökkentse fázis kimenetként a kulcsok, amelyek nincsenek megfelelő sorrendben jelenti.

Használja a következő lépések végrehajtásával hozhat létre adatokat, rendezés, és a kimeneti ellenőrzéséhez:

1. 10 GB adatot, amely a HDInsight-fürt alapértelmezett tárhelyét, készítése `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    A `-Dmapred.map.tasks` arra utasítja a Hadoop használata ehhez a feladathoz hány térkép feladatokat. Az utolsó két paramétert kérje meg a feladat 10 GB adatot hozzon létre és tárolja a `/example/data/10GB-sort-input`.

2. Az alábbi parancs segítségével rendezze az adatokat:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    A `-Dmapred.reduce.tasks` közli a Hadoop hány csökkentése használata a feladathoz tartozó feladatok. Az utolsó két paraméter csak a bemeneti és kimeneti helyek az adatok.

3. A rendezés által létrehozott adatok érvényesítéséhez használja a következő:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>További lépések

Az ebben a cikkben megismerkedett a Linux-alapú HDInsight-fürtökkel a minta futtatásához. A Pig, Hive és a MapReduce használata a HDInsight kapcsolatos oktatóanyagok és az alábbi témakörökben található:

* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-pig.md)
* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-hive.md)
* [A HDInsight az Apache Hadoop MapReduce használata](hdinsight-use-mapreduce.md)