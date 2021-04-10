---
title: Apache Hadoop MapReduce-példák futtatása a HDInsight-Azure-ban
description: Ismerkedjen meg a MapReduce-minták használatával a HDInsight-ben található jar-fájlokban. Használja az SSH-t a fürthöz való kapcsolódáshoz, majd használja a Hadoop parancsot a mintavételi feladatok futtatásához.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 5e75a39b1f9e8503097914b0c9e735915f9ae667
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943212"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>A HDInsight-ben található MapReduce-példák futtatása

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Megtudhatja, hogyan futtathatja a Apache Hadoop HDInsight-on található MapReduce-példákat.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Példák a MapReduce

A minták a HDInsight-fürtön találhatók `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar` . A minták forráskódját a HDInsight-fürt tartalmazza `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples` .

Ebben az archívumban a következő minták szerepelnek:

|Sample |Leírás |
|---|---|
|aggregatewordcount|Megszámolja a bemeneti fájlokban szereplő szavakat.|
|aggregatewordhist|Kiszámítja a bemeneti fájlokban szereplő szavak hisztogramját.|
|BBP|A Bailey-Borwein-Plouffe használatával számítja ki a PI pontos számjegyeit.|
|dbcount|Megszámolja az adatbázisban tárolt oldalmegtekintési naplókat.|
|distbbp|Egy BBP típusú képlettel számítja ki a PI pontos biteit.|
|GREP|Megszámolja a bemenetben lévő regex egyezéseit.|
|csatlakozás|Összeillesztést végez rendezett, egyformán particionált adatkészletekkel.|
|multifilewc|Megszámolja a szavakat több fájlból.|
|pentomino|A pentomino kapcsolatos problémák megoldására szolgáló program.|
|PI|A PI becslése egy kvázi-Monte Carlo metódus használatával.|
|randomtextwriter|10 GB véletlenszerű szöveges adatot ír egy csomóponton.|
|randomwriter|10 GB véletlenszerű adatmennyiséget ír egy csomóponton.|
|secondarysort|Másodlagos rendezést határoz meg a csökkentési fázishoz.|
|Rendezés|Rendezi a véletlenszerű író által írt adatmennyiséget.|
|sudoku|Egy sudoku-Solver.|
|teragen|A terasort vonatkozó adatértékek előállítása.|
|terasort|Futtassa a terasort.|
|teravalidate|A terasort eredményeinek ellenőrzése.|
|WordCount|Megszámolja a bemeneti fájlokban szereplő szavakat.|
|wordmean|Megszámolja a bemeneti fájlokban lévő szavak átlagos hosszát.|
|wordmedian|Megszámolja a bemeneti fájlokban szereplő szavak medián hosszát.|
|wordstandarddeviation|Megszámolja a bemeneti fájlokban található szavak hosszának szórását.|

## <a name="run-the-wordcount-example"></a>A WordCount-példa futtatása

1. Kapcsolódás a HDInsight az SSH használatával. Cserélje le a `CLUSTER` nevet a fürt nevére, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Az SSH-munkamenetben használja a következő parancsot a minták listázásához:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Ez a parancs létrehozza a minta listáját a dokumentum előző szakaszából.

3. Használja az alábbi parancsot egy adott minta súgójának lekéréséhez. Ebben az esetben a **WordCount** minta:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    A következő üzenet jelenik meg:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Ez az üzenet azt jelzi, hogy több bemeneti elérési utat is megadhat a forrás dokumentumaihoz. A végső elérési út az a hely, ahol a kimenet (a forrásbizonylatok szavainak száma) tárolódik.

4. A következő paranccsal számíthatja ki a Leonardo da Vinci jegyzetfüzetek összes olyan szavát, amely mintaadatokként van megadva a fürthöz:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    A feladathoz tartozó bemenet beolvasva `/example/data/gutenberg/davinci.txt` . A példa kimenetét a következő tárolja: `/example/data/davinciwordcount` . Mindkét útvonal a fürt alapértelmezett tárolójában, nem pedig a helyi fájlrendszerben található.

   > [!NOTE]  
   > Ahogy az a WordCount-minta súgójában is látható, több bemeneti fájlt is megadhat. Például a `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` davinci.txt és a ulysses.txt egyaránt megszámolja a szavakat.

5. A művelet befejezése után a következő parancs használatával tekintheti meg a kimenetet:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Ez a parancs összefűzi a feladatokban létrehozott összes kimeneti fájlt. Megjeleníti a kimenetet a konzolon. A kimenet az alábbi szöveghez hasonló:

    ```output
    zum     1
    zur     1
    zwanzig 1
    zweite  1
    ```

    Minden sor egy szót jelöl, és a bemeneti adatokban hányszor fordult elő.

## <a name="the-sudoku-example"></a>A sudoku példája

A [sudoku](https://en.wikipedia.org/wiki/Sudoku) olyan logikai puzzle, amely kilenc 3x3-as rácsból áll. A rács egyes celláiban számok vannak, míg mások üresek, és a cél az üres cellák feloldása. Az előző hivatkozás több információt tartalmaz a puzzle-ról, de ennek a mintának a célja, hogy megoldja az üres cellákat. Így a bemenetnek a következő formátumú fájlnak kell lennie:

* Kilenc sor kilenc oszlop
* Minden oszlop tartalmazhat számot vagy `?` (amely üres cellát jelez)
* A cellákat szóközzel elválasztva

A sudoku-rejtvényeket a következő módon hozhatja létre: oszlopokban vagy sorokban nem lehet megismételni a számot. Van egy példa a megfelelően kialakított HDInsight-fürtre. A (z) helyen található `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` , és a következő szöveget tartalmazza:

```output
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

Ha ezt a példát a sudoku példán keresztül szeretné futtatni, használja a következő parancsot:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Az eredmények az alábbi szöveghez hasonlóan jelennek meg:

```output
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

## <a name="pi--example"></a>PI (π) példa

A PI-minta egy statisztikai (kvázi-Monte Carlo) metódust használ a PI értékének becsléséhez. A pontok véletlenszerűen vannak elhelyezve egy egység négyzetben. A négyzet egy kört is tartalmaz. Annak valószínűsége, hogy a pontok a kör alá esnek, egyenlő a kör területével (PI/4). A PI értéke a 4R értékének alapján becsülhető ki. Az R a körben belüli pontok számának és a négyzetben lévő pontok teljes számának aránya. Minél nagyobb a felhasznált pontok mintája, annál jobb a becslés.

A minta futtatásához használja a következő parancsot. Ez a parancs 16 térképet használ 10 000 000-es mintákkal a PI értékének becsléséhez:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

A parancs által visszaadott érték a **3.14159155000000000000** hasonló. A hivatkozások esetében a PI első 10 decimális helye 3,1415926535.

## <a name="10-gb-graysort-example"></a>10 GB Mintarendezés példa

A Mintarendezés egy teljesítményteszt-rendezés. A metrika a rendezési arány (TB/perc), amely a nagy mennyiségű adat rendezése során érhető el, általában 100 TB-nál kisebb.

Ez a minta egy szerény 10 GB-nyi adat használatát használja, hogy viszonylag gyorsan fusson. Az Owen O'Malley és Arun Marsal által fejlesztett MapReduce-alkalmazásokat használ. Ezek az alkalmazások az éves általános célú ("Daytona") terabájtos rendezési teljesítménytesztet nyerte el 2009-ben, 0,578 TB/perc sebességgel (100 TB, 173 perc). További információ erről és az egyéb rendezési teljesítménytesztekről: [rendezési teljesítményteszt](https://sortbenchmark.org/) -hely.

Ez a példa három MapReduce programot használ:

* **TeraGen**: olyan MapReduce program, amely sorba állítja az adatsorokat

* **TeraSort**: a bemeneti adatok mintavételezése és a MapReduce használatával rendezi az adatokat egy teljes sorrendbe

    A TeraSort egy szabványos MapReduce-rendezés, amely az egyéni particionáló kivételével. A particionáló az N-1 mintázott kulcsok rendezett listáját használja, amelyek meghatározzák az egyes csökkentések kulcsának tartományát. Különösen az [i-1] <= Key < minta [i] mintát kell elküldeni, hogy csökkentse a következőt:. Ez a particionáló garantálja, hogy az i/o-mennyiség csökkentése minden kisebb, mint az i + 1 csökkentése.

* **TeraValidate**: egy MapReduce program, amely ellenőrzi, hogy a kimenet globálisan rendezett-e

    Egy leképezést hoz létre fájlonként a kimeneti könyvtárban, és minden egyes Térkép biztosítja, hogy az egyes kulcsok kisebbek vagy egyenlőek legyenek az előzővel. A Map függvény az egyes fájlok első és utolsó kulcsaihoz tartozó rekordokat hozza létre. A csökkentés funkció biztosítja, hogy a fájl első kulcsa nagyobb legyen, mint az i-1. fájl utolsó kulcsa. Az esetleges problémák a csökkentési fázis kimenetében jelennek meg, a nem sorrendben lévő kulcsokkal.

A következő lépésekkel hozhatja végre az adatokat, rendezheti és ellenőrizheti a kimenetet:

1. 10 GB adat előállítása, amely a HDInsight-fürt alapértelmezett tárolójában tárolódik a következő helyen `/example/data/10GB-sort-input` :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    A `-Dmapred.map.tasks` Hadoop azt jelzi, hogy hány leképezési feladat használható ehhez a feladathoz. Az utolsó két paraméter arra utasítja a feladatot, hogy 10 GB adatmennyiséget hozzon létre, és tárolja azt a következő helyen: `/example/data/10GB-sort-input` .

2. Az alábbi paranccsal rendezheti az adatsorokat:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    A `-Dmapred.reduce.tasks` Hadoop azt jelzi, hogy hány műveletet kell használni a feladathoz. Az utolsó két paraméter csak az adatok bemeneti és kimeneti helyei.

3. A következő paranccsal ellenőrizheti a rendezés által generált adatforrásokat:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan futtathatja a Linux-alapú HDInsight-fürtökben található mintákat. A Pig, a kaptár és a MapReduce HDInsight-vel való használatával kapcsolatos oktatóanyagokat a következő témakörökben találja:

* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight Apache Hadoop használatával](hdinsight-use-mapreduce.md)
