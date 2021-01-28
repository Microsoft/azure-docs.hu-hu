---
title: 'Oktatóanyag: a HDInsight Apache Storm a Storage-ban – Azure/Data Lake'
description: Oktatóanyag – Ismerje meg, hogy a Apache Storm használatával hogyan írhat a HDFS-kompatibilis tárolóba az Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 4e648c57be699620e669ce7db0845dad2b876095
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932554"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Oktatóanyag: írás Apache Hadoop HDFS az Azure HDInsight Apache Stormból

Ez az oktatóanyag azt mutatja be, hogyan használható a Apache Storm a Apache Storm által a HDInsight-ben használt HDFS-kompatibilis tárolóba való íráshoz. A HDInsight az Azure Storage-t és a Azure Data Lake Storage is használhatja HDFS-kompatibilis tárolóként. A Storm egy [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) -összetevőt biztosít, amely az HDFS-ba írja az adatot. Ez a dokumentum információt nyújt a HdfsBolt-ból származó tárolók típusának írásáról.

A dokumentumban használt példa topológiája a HDInsight-alapú Storm-hez tartozó összetevőkre támaszkodik. Előfordulhat, hogy a módosítást úgy kell megkövetelni, hogy más Apache Storm fürtökkel való használat esetén Azure Data Lake Storage működjön.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A fürt konfigurálása parancsfájl-művelettel
> * A topológia létrehozása és becsomagolása
> * A topológia üzembe helyezése és futtatása
> * Kimeneti adatokat tekinthet meg
> * A topológia leállítása

## <a name="prerequisites"></a>Előfeltételek

* [Java Developer Kit (JDK) 8-as verzió](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* Az [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [van telepítve](https://maven.apache.org/install.html) az Apache-ban.  A Maven egy projekt-összeállítási rendszer Java-projektekhez.

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

* A fürtök elsődleges tárolójának [URI-sémája](../hdinsight-hadoop-linux-information.md#URI-and-scheme) . Ez az `wasb://` Azure Storage számára lenne `abfs://` Azure Data Lake Storage Gen2 vagy Azure Data Lake Storage Gen1 esetén `adl://` . Ha a biztonságos átvitel engedélyezve van az Azure Storage-hoz, az URI a következő lesz: `wasbs://` .  Lásd még: [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Konfigurációs példa

A következő YAML a `resources/writetohdfs.yaml` példában szereplő fájl kivonata. Ez a fájl határozza meg a Storm-topológiát a Apache Storm [Flux](https://storm.apache.org/releases/current/flux.html) -keretrendszerének használatával.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Ez a YAML a következő elemeket határozza meg:

* `syncPolicy`: Meghatározza, hogy a rendszer mikor szinkronizálja vagy Ürítse ki a fájlokat a fájlrendszerben. Ebben a példában minden 1000-rekordok.
* `fileNameFormat`: Meghatározza a fájlok írásakor használandó elérési utat és fájlnevet. Ebben a példában az elérési utat egy szűrővel kell megadni futásidőben, és a fájlkiterjesztés a következő: `.txt` .
* `recordFormat`: Az írt fájlok belső formátumának meghatározása. Ebben a példában a mezőket a karakter tagolja `|` .
* `rotationPolicy`: Meghatározza, hogy mikor kell elforgatni a fájlokat. Ebben a példában a rendszer nem hajt végre rotációt.
* `hdfs-bolt`: Az előző összetevőket használja konfigurációs paraméterekként a `HdfsBolt` osztályhoz.

További információ a Flux-keretrendszerről: [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html) .

## <a name="configure-the-cluster"></a>A fürt konfigurálása

Alapértelmezés szerint a Storm on HDInsight nem tartalmazza az `HdfsBolt` Azure Storage szolgáltatással való kommunikációhoz használt összetevőket, Data Lake Storage vagy a Storm osztályútvonal. A következő parancsfájl-művelettel adhatja hozzá ezeket az összetevőket a `extlib` fürtön található Storm-címtárhoz:

| Tulajdonság | Érték |
|---|---|
|Parancsfájl típusa |– Egyéni|
|Bash-parancsfájl URI-ja |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Csomópont típusa (i) |Nimbus, felügyelő|
|Paraméterek |Nincs|

További információ a parancsfájlnak a fürthöz való használatáról: [HDInsight-fürtök testreszabása parancsfájl-műveletek dokumentum használatával](./../hdinsight-hadoop-customize-cluster-linux.md) .

## <a name="build-and-package-the-topology"></a>A topológia létrehozása és becsomagolása

1. Töltse le a példa projektet a alkalmazásból [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) a fejlesztői környezetbe.

2. A parancssorból, a terminálból vagy a rendszerhéj-munkamenetből módosítsa a könyvtárakat a letöltött projekt gyökerébe. A topológia létrehozásához és előkészítéséhez használja a következő parancsot:

    ```cmd
    mvn compile package
    ```

    A build és a csomagolás befejezése után egy nevű új könyvtár található `target` , amely egy nevű fájlt tartalmaz `StormToHdfs-1.0-SNAPSHOT.jar` . Ez a fájl tartalmazza a lefordított topológiát.

## <a name="deploy-and-run-the-topology"></a>A topológia üzembe helyezése és futtatása

1. A következő parancs használatával másolja a topológiát a HDInsight-fürtre. Cserélje le a `CLUSTERNAME` nevet a fürt nevére.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. A feltöltés befejeződése után a következő paranccsal csatlakozhat a HDInsight-fürthöz az SSH használatával. Cserélje le a `CLUSTERNAME` nevet a fürt nevére.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Csatlakozás után a következő paranccsal hozzon létre egy nevű fájlt `dev.properties` :

    ```bash
    nano dev.properties
    ```

1. Használja a következő szöveget a fájl tartalmának megfelelően `dev.properties` . Szükség szerint módosítsa az [URI-séma](../hdinsight-hadoop-linux-information.md#URI-and-scheme)alapján.

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    A fájl mentéséhez használja a __CTRL + X billentyűkombinációt__, majd az __Y__ billentyűt, és végül __írja be__ a következőt:. A fájl értékei a tárolási URL-címet és annak a könyvtárnak a nevét írják be, amelybe az adatok bekerülnek.

1. A topológia elindításához használja a következő parancsot:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Ez a parancs elindítja a topológiát a Flux keretrendszer használatával, ha elküldi azt a fürt Nimbus-csomópontjára. A topológiát a `writetohdfs.yaml` jar-ban található fájl határozza meg. A `dev.properties` rendszer szűrőként továbbítja a fájlt, és a fájlban található értékeket a topológia olvassa be.

## <a name="view-output-data"></a>Kimeneti adatokat tekinthet meg

Az adatmegjelenítéshez használja a következő parancsot:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Megjelenik a topológia által létrehozott fájlok listája. Az alábbi lista az előző parancsokban visszaadott értékeket szemlélteti:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>A topológia leállítása

A Storm-topológiák Leállításig futnak, vagy a fürt törlődik. A topológia leállításához használja a következő parancsot:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag által létrehozott erőforrásokat, akkor törölje az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó HDInsight-fürt, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az __Erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan használhatja a Apache Stormt a Apache Storm által a HDInsight-ben használt HDFS-kompatibilis tárolóba való íráshoz.

> [!div class="nextstepaction"]
> További [Apache Storm példák a HDInsight](apache-storm-example-topology.md)