---
title: MapReduce és SSH-kapcsolatok Apache Hadoop-Azure HDInsight
description: Ismerje meg, hogy az SSH használatával hogyan futtathat MapReduce-feladatokat a HDInsight Apache Hadoop használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 991b91d1feee185d17bbf01266e0392f347e1a66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939634"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>A MapReduce használata a HDInsight-mel az SSH-val Apache Hadoop

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ismerje meg, hogyan küldhet MapReduce-feladatokat egy Secure Shell-(SSH-) kapcsolatból a HDInsight-be.

> [!NOTE]
> Ha már ismeri a Linux-alapú Apache Hadoop-kiszolgálók használatát, de most ismerkedik a HDInsight-mel, tekintse meg a [Linux-alapú HDInsight kapcsolatos tippeket](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Előfeltételek

Egy Apache Hadoop-fürt a HDInsight-on. Lásd: [Apache Hadoop-fürtök létrehozása a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Hadoop parancsok használata

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Miután csatlakozott a HDInsight-fürthöz, a következő paranccsal indíthatja el a MapReduce-feladatot:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Ez a parancs elindítja az `wordcount` osztályt, amely a `hadoop-mapreduce-examples.jar` fájlban található. A `/example/data/gutenberg/davinci.txt` dokumentumot bemenetként használja, és a kimenetet a következő helyen tárolja: `/example/data/WordCountOutput` .

    > [!NOTE]
    > További információ erről a MapReduce-feladatról és a példában szereplő adatokról: a [MapReduce használata a HDInsight-on Apache Hadoop](hdinsight-use-mapreduce.md).

    A feladatnak a folyamat során kibocsátja a részleteket, és a következő szöveghez hasonló adatokat ad vissza, amikor a feladatok befejeződik:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. Amikor a feladatok befejeződik, a következő paranccsal listázhatja ki a kimeneti fájlokat:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Ez a parancs két fájlt jelenít meg, `_SUCCESS` és `part-r-00000` . A `part-r-00000` fájl tartalmazza a feladatokhoz tartozó kimenetet.

    > [!NOTE]  
    > Egyes MapReduce-feladatok több **rész-r-#** # # # # fájlra oszthatják meg az eredményeket. Ha igen, használja a # # # # # utótagot, hogy jelezze a fájlok sorrendjét.

1. A kimenet megtekintéséhez használja a következő parancsot:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Ez a parancs megjeleníti a **wasbs://example/data/gutenberg/davinci.txt** fájlban található szavak listáját, valamint az egyes szavak előfordulási idejét. A következő szöveg egy példa a fájlban található fájlokra:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Következő lépések

Amint láthatja, a Hadoop parancsai egyszerűen futtathatják a MapReduce-feladatokat egy HDInsight-fürtön, majd megtekinthetik a feladat kimenetét. További információ a Hadoop a HDInsight-ben való használatával kapcsolatos egyéb módszerekről:

* [A MapReduce használata a HDInsight-Hadoop](hdinsight-use-mapreduce.md)
* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)
