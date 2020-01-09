---
title: Apache Storm Python-összetevőkkel – Azure HDInsight
description: Ismerje meg, hogyan hozhat létre olyan Apache Storm topológiát, amely a Python-összetevőket használja az Azure HDInsight-ben
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: ba632a98c21926ec28606def128cc068abf47f53
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646625"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Apache Storm-topológiák fejlesztése a Python használatával a HDInsight-on

Megtudhatja, hogyan hozhat létre egy Python-összetevőket használó [Apache Storm](https://storm.apache.org/) -topológiát. Apache Storm több nyelvet is támogat, ami lehetővé teszi, hogy a különböző nyelvekről származó összetevőket egyetlen topológiában egyesítse. A [flow](https://storm.apache.org/releases/current/flux.html) Framework (a Storm 0.10.0-mel bevezetett) segítségével könnyedén hozhat létre Python-összetevőket használó megoldásokat.

> [!IMPORTANT]  
> A dokumentumban található információk a Storm on HDInsight 3,6-es verziójában lettek tesztelve.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Storm-fürt a HDInsight-on. Lásd: [hozzon létre Apache Hadoop fürtöket a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) , és válassza a **Storm** a **fürt típusa**lehetőséget.

* Egy helyi Storm fejlesztői környezet (opcionális). Helyi Storm-környezetre csak akkor van szükség, ha helyileg szeretné futtatni a topológiát. További információ: [fejlesztői környezet beállítása](http://storm.apache.org/releases/current/Setting-up-development-environment.html).

* [Python 2,7 vagy újabb](https://www.python.org/downloads/).

* A [Java Developer Kit (JDK) 8-as verziója](https://aka.ms/azure-jdks).

* Az [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [van telepítve](https://maven.apache.org/install.html) az Apache-ban.  A Maven egy projekt-összeállítási rendszer Java-projektekhez.

## <a name="storm-multi-language-support"></a>Multi-Language Storm-támogatás

Apache Storm a programozási nyelv használatával írt összetevőkkel való együttműködésre lett tervezve. Az összetevőknek meg kell érteniük a Storm-definícióval való munkát. A Python esetében a Apache Storm projekt részeként egy modult biztosítunk, amely lehetővé teszi a Storm használatával történő egyszerű kapcsolódást. Ezt a modult a következő helyen találja: [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

A Storm egy Java-folyamat, amely a Java virtuális gépon (JVM) fut. A más nyelveken írt összetevők alfolyamatként lesznek végrehajtva. A Storm ezeket az alfolyamatokat a stdin/StdOut-on küldött JSON-üzenetek használatával kommunikál. Az összetevők közötti kommunikációról további részleteket a [multi-lang Protocol](https://storm.apache.org/documentation/Multilang-protocol.html) dokumentációjában talál.

## <a name="python-with-the-flux-framework"></a>Python a Flux-keretrendszerrel

A Flux-keretrendszer lehetővé teszi, hogy a Storm-topológiákat külön határozza meg az összetevőktől. A Flux-keretrendszer a YAML használatával határozza meg a Storm-topológiát. A következő szöveg szemlélteti, hogyan hivatkozhat egy Python-összetevőre az YAML dokumentumban:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

A `FluxShellSpout` osztály a kiöntőt megvalósító `sentencespout.py`-szkript elindítására szolgál.

A Flux arra vár, hogy a Python-parancsfájlok a topológiát tartalmazó jar-fájlban lévő `/resources` könyvtárban legyenek. Így ez a példa a Python-parancsfájlokat a `/multilang/resources` könyvtárban tárolja. A `pom.xml` a következő XML-fájl használatával tartalmazza ezt a fájlt:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Ahogy korábban már említettük, van egy `storm.py`-fájl, amely megvalósítja a Storm definícióját. A Flux-keretrendszer automatikusan `storm.py` a projekt felépítésekor, így nem kell aggódnia.

## <a name="build-the-project"></a>A projekt felépítése

1. Töltse le a projektet a következő helyről: [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

1. Nyisson meg egy parancssort, és navigáljon a projekt gyökerére: `hdinsight-python-storm-wordcount-master`. Írja be a következő parancsot:

    ```cmd
    mvn clean compile package
    ```

    Ez a parancs létrehoz egy `target/WordCount-1.0-SNAPSHOT.jar` fájlt, amely tartalmazza a lefordított topológiát.

## <a name="run-the-storm-topology-on-hdinsight"></a>A Storm-topológia futtatása a HDInsight

1. Az [SSH parancs](../hdinsight-hadoop-linux-use-ssh-unix.md) használatával másolja a `WordCount-1.0-SNAPSHOT.jar`-fájlt a Storm on HDInsight-fürtre. Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. A fájl feltöltése után csatlakozzon a fürthöz az SSH használatával:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetben használja a következő parancsot a fürt topológiájának elindításához:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Az indítás után a Storm-topológia a Leállításig fut.

1. A Storm felhasználói felület használatával megtekintheti a fürt topológiáját. A Storm felhasználói felülete `https://CLUSTERNAME.azurehdinsight.net/stormui`található. Cserélje le a `CLUSTERNAME`t a fürt nevére.

1. Állítsa le a Storm-topológiát. A fürt topológiájának leállításához használja a következő parancsot:

    ```bash
    storm kill wordcount
    ```

    Azt is megteheti, hogy a Storm felhasználói felületét használja. A topológiához tartozó **topológiai műveletek** területen válassza a **kill**(Törlés) lehetőséget.

## <a name="run-the-topology-locally"></a>A topológia helyi futtatása

A topológia helyi futtatásához használja a következő parancsot:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Ehhez a parancshoz helyi Storm fejlesztési környezet szükséges. További információ: [fejlesztői környezet beállítása](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

A topológia elindítása után a következő szöveghez hasonló módon bocsát ki információkat a helyi konzolra:

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

A topológia leállításához használja a __CTRL + C billentyűkombinációt__.

## <a name="next-steps"></a>Következő lépések

A Python és a HDInsight használatának egyéb módjairól a következő dokumentumokban talál további információt: a [Python felhasználói függvények (UDF) használata az Apache Pig és a Apache Hiveban](../hadoop/python-udf-hdinsight.md).
