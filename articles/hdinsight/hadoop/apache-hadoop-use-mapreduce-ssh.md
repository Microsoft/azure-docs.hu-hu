---
title: Az Apache Hadoop, a HDInsight - Azure MapReduce és SSH-kapcsolat
description: Útmutató az SSH használata a HDInsight segítségével az Apache Hadoop MapReduce-feladatok futtatásához.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 3448a5e89f6930a5bdcb7d0d77b92576e58fc90b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687300"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Az ssh-val a HDInsight az Apache Hadoop MapReduce használata

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ismerje meg, hogyan lehet elküldeni a HDInsight a Secure Shell (SSH) kapcsolat MapReduce-feladatok.

> [!NOTE]
> Ha már ismeri a Linux-alapú Apache Hadoop-kiszolgálók használata, de még nem ismeri a HDInsight: [Linux-alapú HDInsight-tippek](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Előfeltételek

* Egy Linux-alapú HDInsight (Hadoop on HDInsight)-fürt

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Egy SSH-ügyfél. További információkért lásd: [az SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Kapcsolódás az ssh-val

Csatlakozhat a fürthöz SSH használatával. Az alábbi parancs például nevű fürthöz csatlakozó **myhdinsight** , a **sshuser** fiók:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Ha az SSH-hitelesítési tanúsítvány kulcs használata**, szükség lehet adja meg a titkos kulcs helyét az ügyfél rendszeren:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Ha az SSH-hitelesítést használhat olyan jelszót**, meg kell adnia a jelszót, amikor a rendszer kéri.

Az SSH és a HDInsight további információkért lásd: [az SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Hadoop parancsok használata

1. Miután csatlakozott a HDInsight-fürthöz, használja a következő parancsot a MapReduce feladat indításához:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Ez a parancs elindítja a `wordcount` osztály, amely tartalmazza a `hadoop-mapreduce-examples.jar` fájlt. Használja a `/example/data/gutenberg/davinci.txt` kimenetként, és a dokumentum tárolt `/example/data/WordCountOutput`.

    > [!NOTE]
    > A MapReduce-feladatot, és a példaadatokat kapcsolatos további információkért lásd: [a HDInsight az Apache Hadoop MapReduce használata](hdinsight-use-mapreduce.md).

2. A feladat részletei bocsát ki, feldolgozza, és azt hasonló információt ad vissza a következő szöveget a feladat befejezése után:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Ha a feladat befejeződik, használja a következő parancsot a kimeneti fájlok listázásához:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Ez a parancs két fájlt megjelenítéséhez `_SUCCESS` és `part-r-00000`. A `part-r-00000` fájl tartalmazza a feladat kimenetét.

    > [!NOTE]  
    > Bizonyos MapReduce-feladatok az eredmények lehet, hogy elosztja a több **. rész – az r-###** fájlokat. Ha igen, használja a ### utótagot a fájlok sorrendjét.

4. A kimenet megtekintéséhez használja a következő parancsot:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Ez a parancs az abban található szavak listáját jeleníti meg a **wasb://example/data/gutenberg/davinci.txt** fájl- és, hogy hányszor történt az egyes szavak. A következő szöveg egy példa a fájlban lévő adatok:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Summary (Összefoglalás)

Ahogy láthatjuk, a Hadoop-parancsokat a MapReduce-feladatok futtatása HDInsight-fürtben, és nézze meg a feladat kimenetének egyszerű módot biztosítanak.

## <a id="nextsteps"></a>Következő lépések

HDInsight MapReduce-feladatok általános tájékoztatást:

* [HDInsight hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Egyéb módjaival kapcsolatos további információk a HDInsight Hadoop-keretrendszerrel használhatja:

* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-pig.md)
