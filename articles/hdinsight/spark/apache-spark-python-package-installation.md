---
title: Parancsfájl-művelet a Python-csomagokhoz az Azure HDInsight Jupyter
description: Részletes útmutató a HDInsight Spark-fürtökkel elérhető Jupyter notebookok külső Python-csomagok használatához való konfigurálásához a parancsfájl-művelet használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/29/2020
ms.openlocfilehash: 8fbbe137ece7aac2dd2196c5ebec435e118297ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929824"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Python-környezet biztonságos kezelése az Azure HDInsightban szkriptműveletekkel

A HDInsight két beépített Python-telepítéssel rendelkezik a Spark-fürtben, az anaconda Python 2,7 és a Python 3,5. Előfordulhat, hogy az ügyfeleknek testre kell szabnia a Python-környezetet, például a külső Python-csomagok telepítését. Itt bemutatjuk a Python-környezetek biztonságos kezelésének bevált gyakorlatát Apache Spark-fürtökön a HDInsight-on.

## <a name="prerequisites"></a>Előfeltételek

Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md). Ha még nem rendelkezik Spark-fürttel a HDInsight-on, a fürt létrehozása során parancsfájl-műveleteket is futtathat. Tekintse meg az [egyéni parancsfájl-műveletek használatát ismertető](../hdinsight-hadoop-customize-cluster-linux.md)dokumentációt.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>A HDInsight-fürtökön használt nyílt forráskódú szoftverek támogatása

A Microsoft Azure HDInsight szolgáltatás olyan nyílt forráskódú technológiák környezetét használja, amelyeket a rendszer Apache Hadoop. A Microsoft Azure a nyílt forráskódú technológiák általános támogatását biztosítja. További információ: Azure- [támogatás – gyakori kérdések webhelye](https://azure.microsoft.com/support/faq/). A HDInsight szolgáltatás további szintű támogatást biztosít a beépített összetevőkhöz.

A HDInsight szolgáltatásban kétféle nyílt forráskódú összetevő érhető el:

|Összetevő |Leírás |
|---|---|
|Beépített|Ezek az összetevők előre telepítve vannak a HDInsight-fürtökön, és biztosítják a fürt alapvető funkcióit. Például Apache Hadoop a fonal Resource Manager, a Apache Hive lekérdezési nyelv (HiveQL) és a Mahout könyvtár ehhez a kategóriához tartozik. A fürt összetevőinek teljes listája a [HDInsight által biztosított Apache Hadoop-fürt verziójának újdonságai](../hdinsight-component-versioning.md)című részében érhető el.|
|Egyéni|A fürt felhasználója a munkaterhelésben telepítheti vagy használhatja a Közösségben elérhető vagy Ön által létrehozott összetevőket.|

> [!IMPORTANT]
> A HDInsight-fürthöz biztosított összetevők teljes mértékben támogatottak. Microsoft ügyfélszolgálata segít elkülöníteni és elhárítani ezeket az összetevőket érintő problémákat.
>
> Az egyéni összetevők kereskedelmileg ésszerű támogatást kapnak a probléma további megoldásához. A Microsoft támogatási szolgálata megoldhatja a problémát, vagy megkérheti, hogy a nyílt forráskódú technológiák számára elérhető csatornákat adjon meg, ahol az adott technológia mélyreható szaktudása található. Többek között számos közösségi webhely használható, például: [Microsoft Q&a HDInsight-hez tartozó kérdés oldalát](/answers/topics/azure-hdinsight.html) `https://stackoverflow.com` . Emellett az Apache-projektek is rendelkeznek projekt-webhelyekkel `https://apache.org` .

## <a name="understand-default-python-installation"></a>A Python alapértelmezett telepítésének ismertetése

A HDInsight Spark-fürt az anaconda telepítésével jön létre. Két Python-telepítés van a fürtben, az anaconda Python 2,7 és a Python 3,5. Az alábbi táblázat a Spark, a Livy és a Jupyter alapértelmezett Python-beállításait mutatja be.

|Beállítás |Python 2,7|Python 3,5|
|----|----|----|
|Elérési út|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark-verzió|Alapértelmezett érték 2,7|A konfigurációt 3,5-ra módosíthatja|
|Livy verziója|Alapértelmezett érték 2,7|A konfigurációt 3,5-ra módosíthatja|
|Jupyter|PySpark kernel|PySpark3 kernel|

## <a name="safely-install-external-python-packages"></a>Külső Python-csomagok biztonságos telepítése

A HDInsight-fürt a Python 2,7 és a Python 3,5 beépített Python-környezettől függ. Ha közvetlenül telepíti az egyéni csomagokat az alapértelmezett beépített környezetekben, előfordulhat, hogy a függvénytár-verzió váratlanul módosul. És bontsa ki a fürtöt. Ha az egyéni külső Python-csomagokat biztonságosan szeretné telepíteni a Spark-alkalmazásokhoz, kövesse az alábbi lépéseket.

1. Python virtuális környezet létrehozása a Conda használatával. A virtuális környezetek elkülönített lemezterületet biztosítanak a projektekhez, és nem kell másokat feltörni. A Python virtuális környezet létrehozásakor megadhatja a használni kívánt Python-verziót. Még akkor is létre kell hoznia virtuális környezetet, ha a Python 2,7 és a 3,5-et szeretné használni. Ez a követelmény annak biztosítása, hogy a fürt alapértelmezett környezete ne legyen kitört. Hozzon létre parancsfájl-műveleteket a fürtön az alábbi szkripttel rendelkező összes csomóponton egy Python virtuális környezet létrehozásához.

    -   `--prefix` Megadja azt az elérési utat, ahol a Conda virtuális környezete él. Az itt megadott elérési út alapján több konfigurációt is meg kell változtatni. Ebben a példában a py35new használjuk, mivel a fürtön már létezik egy py35 nevű meglévő virtuális környezet.
    -   `python=` Megadja a virtuális környezet Python-verzióját. Ebben a példában az 3,5-es verziót használjuk, amely megegyezik a fürt beépített verziójával. A virtuális környezet létrehozásához más Python-verziókat is használhat.
    -   `anaconda` Megadja az anaconda-csomagok virtuális környezetben való telepítéséhez szükséges package_spec.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Szükség esetén telepítsen külső Python-csomagokat a létrehozott virtuális környezetbe. A külső Python-csomagok telepítéséhez futtassa parancsfájl-műveleteket a fürtön az alábbi szkripttel rendelkező összes csomóponthoz. Ha a virtuális környezet mappájába szeretne fájlokat írni, sudo jogosultsággal kell rendelkeznie.

    Keresse meg a [csomag indexét](https://pypi.python.org/pypi) az elérhető csomagok teljes listájához. Lekérheti az egyéb forrásokból származó elérhető csomagok listáját is. Telepítheti például a [Conda-Forge](https://conda-forge.org/feedstocks/)használatával elérhetővé tett csomagokat.

    Használja az alábbi parancsot, ha a legújabb verziójával szeretné telepíteni a könyvtárat:

    - Conda-csatorna használata:

        -   `seaborn` a csomag neve, amelyet telepíteni szeretne.
        -   `-n py35new` adja meg az imént létrehozott virtuális környezet nevét. Ügyeljen arra, hogy a virtuális környezet létrehozása alapján megfelelően módosítsa a nevet.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Vagy használja a PyPi-tárházat, módosítsa `seaborn` és `py35new` ennek megfelelő:
        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install seaborn
        ```

    Ha egy adott verziójú könyvtárat szeretne telepíteni, használja az alábbi parancsot:

    - Conda-csatorna használata:

        -   `numpy=1.16.1` a telepítendő csomag neve és verziószáma.
        -   `-n py35new` adja meg az imént létrehozott virtuális környezet nevét. Ügyeljen arra, hogy a virtuális környezet létrehozása alapján megfelelően módosítsa a nevet.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Vagy használja a PyPi-tárházat, módosítsa `numpy==1.16.1` és `py35new` ennek megfelelő:

        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install numpy==1.16.1
        ```

    Ha nem ismeri a virtuális környezet nevét, az SSH-t használhatja a fürt fő csomópontjára, és az `/usr/bin/anaconda/bin/conda info -e` összes virtuális környezet megjelenítéséhez futtathatja azt.

3. Módosítsa a Spark és a Livy konfigurációját, és mutasson a létrehozott virtuális környezetre.

    1. Nyissa meg a Ambari felhasználói felületét, lépjen a Spark2 lapra, és válassza a konfigurációk lapot.

        ![A Spark és a Livy konfiguráció módosítása a Ambari használatával](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Bontsa ki a speciális livy2-env elemet, majd a lenti utasításokat. Ha egy másik előtaggal telepítette a virtuális környezetet, módosítsa az elérési utat megfelelően.

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Livy-konfiguráció módosítása a Ambari használatával](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Bontsa ki a speciális spark2 – env elemet, cserélje le a meglévő export PYSPARK_PYTHON utasítást alulra. Ha egy másik előtaggal telepítette a virtuális környezetet, módosítsa az elérési utat megfelelően.

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Spark-konfiguráció módosítása a Ambari használatával](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Mentse a módosításokat, és indítsa újra az érintett szolgáltatásokat. Ezeknek a változásoknak a Spark2 szolgáltatás újraindítására van szükségük. A Ambari felhasználói felülete kérni fogja a szükséges újraindítási emlékeztetőt, majd kattintson az Újraindítás gombra az összes érintett szolgáltatás újraindításához.

        ![Szolgáltatások újraindítása](./media/apache-spark-python-package-installation/ambari-restart-services.png)

    5. Állítsa be a Spark-munkamenet két tulajdonságát, és győződjön meg arról, hogy a feladattípus a frissített Spark-konfigurációra mutat: `spark.yarn.appMasterEnv.PYSPARK_PYTHON` és `spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON` . 

        A terminál vagy egy jegyzetfüzet használatával használja a `spark.conf.set` függvényt.

        ```spark
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        ```

        Ha a Livy-t használja, adja hozzá a következő tulajdonságokat a kérelem törzséhez:

        ```
        “conf” : {
        “spark.yarn.appMasterEnv.PYSPARK_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”,
        “spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”
        }
        ```

4. Ha az újonnan létrehozott virtuális környezetet szeretné használni a Jupyter-on. Módosítsa a Jupyter konfigurációit, és indítsa újra a Jupyter. Futtasson parancsfájl-műveleteket minden olyan fejléc-csomóponton az alábbi utasítással, hogy az új, létrehozott virtuális környezethez Jupyter. Ügyeljen arra, hogy módosítsa a virtuális környezethez megadott előtag elérési útját. A parancsfájl futtatása után indítsa újra a Jupyter szolgáltatást a Ambari felhasználói felületén, hogy elérhetővé tegye ezt a módosítást.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Az alábbi kód futtatásával megerősítheti Jupyter Notebook Python-környezetét:

    ![Python-verzió keresése Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Ismert probléma

Létezik egy ismert hiba a anaconda-verzió `4.7.11` , a és a esetében `4.7.12` `4.8.0` . Ha úgy látja, hogy a parancsfájl műveletei nem válaszolnak a következővel: `"Collecting package metadata (repodata.json): ...working..."` `"Python script has been killed due to timeout after waiting 3600 secs"` . [Ezt a parancsfájlt](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) letöltheti, és parancsfájl-műveletekként futtathatja az összes csomóponton a probléma megoldásához.

A anaconda verziójának megadásához SSH-t használhat a fürt fejlécére, és futtathatja a parancsot `/usr/bin/anaconda/bin/conda --v` .

## <a name="next-steps"></a>Következő lépések

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)
* [Külső csomagok Jupyter notebookokkal Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
