---
title: A Jupyter helyi telepítése és kapcsolódás a Sparkhoz az Azure HDInsight
description: Megtudhatja, hogyan telepítheti Jupyter Notebook helyileg a számítógépre, és hogyan csatlakoztatható egy Apache Spark-fürthöz.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: d5915316ee9d393b2481eeca6a5da7923b271d9f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930404"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Telepítse a Jupyter Notebookt a számítógépre, és kapcsolódjon a HDInsight Apache Sparkhoz

Ebből a cikkből megtudhatja, hogyan telepítheti a Jupyter Notebookt az egyéni PySpark (a Python esetében) és a Apache Spark (a Scala) kernelekhez a Spark Magic használatával. Ezután csatlakoztassuk a jegyzetfüzetet egy HDInsight-fürthöz.

A Jupyter telepítése és a HDInsight szolgáltatáshoz való Apache Spark csatlakozás négy fő lépésből áll.

* A Spark-fürt konfigurálása.
* Telepítse a Jupyter Notebook.
* Telepítse a PySpark és a Spark-kerneleket a Spark magictal.
* A Spark Magic konfigurálása a HDInsight-beli Spark-fürt eléréséhez.

Az egyéni kernelekkel és a Spark Magic szolgáltatással kapcsolatos további információkért lásd: [Jupyter notebookokhoz elérhető kernelek Apache Spark Linux-fürtökkel a HDInsight-on](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md). A helyi jegyzetfüzet csatlakozik a HDInsight-fürthöz.

* A Jupyter-notebookok és a HDInsighton futó Spark használatának ismerete.

## <a name="install-jupyter-notebook-on-your-computer"></a>Jupyter Notebook telepítése a számítógépre

Telepítse a Pythont a Jupyter-jegyzetfüzetek telepítése előtt. Az [anaconda-disztribúció](https://www.anaconda.com/download/) a Pythont és a Jupyter notebook is telepíti.

Töltse le a platformhoz tartozó [anaconda-telepítőt](https://www.anaconda.com/download/) , és futtassa a telepítőt. A telepítővarázsló futtatásakor győződjön meg arról, hogy a PATH változóhoz a anaconda hozzáadása lehetőséget választotta.  Lásd még: a [Jupyter telepítése a anaconda használatával](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>A Spark Magic telepítése

1. Adja meg a `pip install sparkmagic==0.13.1` Spark Magic telepítéséhez szükséges parancsot a 3,6-es és a 4,0-es verziójú HDInsight-fürtökhöz. Lásd még: [sparkmagic dokumentáció](https://github.com/jupyter-incubator/sparkmagic#installation).

1. `ipywidgets`A következő parancs futtatásával ellenőrizze, hogy megfelelően van-e telepítve:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>A PySpark és a Spark kernelek telepítése

1. `sparkmagic`A következő parancs megadásával határozza meg, hogy hol van telepítve:

    ```cmd
    pip show sparkmagic
    ```

    Ezután módosítsa a munkakönyvtárat a fenti paranccsal azonosított **helyre** .

1. Az új munkakönyvtárból adja meg az alábbi parancsok valamelyikét a kívánt kernel (ek) telepítéséhez:

    |Kernel | Parancs |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Választható. Az alábbi parancs megadásával engedélyezheti a kiszolgáló bővítményét:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>A Spark Magic konfigurálása a HDInsight Spark-fürthöz való kapcsolódáshoz

Ebben a szakaszban azt a Spark-varázst konfigurálja, amelyet korábban telepített Apache Spark fürthöz való kapcsolódáshoz.

1. Indítsa el a Python rendszerhéjt a következő paranccsal:

    ```cmd
    python
    ```

2. A Jupyter konfigurációs adatait általában a felhasználók kezdőkönyvtár tárolja. Adja meg a következő parancsot a kezdőkönyvtár azonosításához, majd hozzon létre egy **\. sparkmagic** nevű mappát.  A teljes elérési út kimarad.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. A mappában `.sparkmagic` hozzon létre egy **config.js** nevű fájlt, és adja hozzá a következő JSON-kódrészletet.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Végezze el a következő módosításokat a fájlon:

    |Sablon értéke | Új érték |
    |---|---|
    |USERNAME|A fürt bejelentkezésének alapértelmezett értéke: `admin` .|
    |CLUSTERDNSNAME|Fürt neve|
    |{BASE64ENCODEDPASSWORD}|A tényleges jelszó Base64 kódolású jelszava.  Base64-jelszót is létrehozhat a következő helyen: [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/) .|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Tartsa meg, hogy használja `sparkmagic 0.12.7` -e (a v 3.5 és a v 3.6 fürtöket).  Ha a használatakor `sparkmagic 0.2.3` (fürtök: v 3.4), cserélje le a következőre: `"should_heartbeat": true` .|

    Megtekintheti a [minta config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)található teljes példaként szolgáló fájlt.

   > [!TIP]  
   > A szívverések elküldése annak biztosítására, hogy a munkamenetek nem szivárognak ki. Ha egy számítógép alvó állapotba kerül, vagy leáll, a szívverés nem kerül elküldésre, ami a munkamenet tisztítását eredményezi. Ha szeretné letiltani ezt a viselkedést, a v 3.4 fürtök esetében beállíthatja a Livy konfigurációját `livy.server.interactive.heartbeat.timeout` `0` a Ambari felhasználói felületéről. Ha az v 3.5 fürtök esetében nem állítja be az 3,5-es konfigurációt, a rendszer nem törli a munkamenetet.

5. Indítsa el a Jupyter. Használja a következő parancsot a parancssorból.

    ```cmd
    jupyter notebook
    ```

6. Ellenőrizze, hogy használható-e a kernelekkel elérhető Spark Magic. Hajtsa végre a következő lépéseket.

    a. Hozzon létre új notebookot. A jobb oldali sarokban válassza az **új** lehetőséget. Ekkor meg kell jelennie az alapértelmezett kernel **Python 2** vagy **Python 3** és a telepített kerneleknek. A tényleges értékek a telepítési lehetőségektől függően eltérőek lehetnek.  Válassza a **PySpark** lehetőséget.

    ![Elérhető kernelek a Jupyter Notebook](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Kernelek a Jupyter Notebookban")

    > [!IMPORTANT]  
    > Miután kiválasztotta az **új** rendszerhéj áttekintését a hibákhoz.  Ha úgy látja a hibát, hogy a `TypeError: __init__() got an unexpected keyword argument 'io_loop'` Tornado bizonyos verzióiban ismert probléma merülhet fel.  Ha igen, állítsa le a kernelt, majd a következő paranccsal minősítse le a tornádó telepítését: `pip install tornado==4.5.3` .

    b. Futtassa az alábbi kódrészletet.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Ha sikeresen beolvasta a kimenetet, a rendszer teszteli a HDInsight-fürthöz való kapcsolódást.

    Ha szeretné frissíteni a jegyzetfüzet konfigurációját egy másik fürthöz való kapcsolódáshoz, frissítse a config.jst az új értékekkel, a fenti 3. lépésben látható módon.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Miért érdemes telepíteni a Jupyter a számítógépre?

A Jupyter telepítésének okai a számítógépen, majd a HDInsight-fürthöz való kapcsolódás a Apache Spark-fürtön:

* Lehetőséget nyújt a jegyzetfüzetek helyi létrehozására, az alkalmazás tesztelésére egy futó fürtön, majd feltölti a jegyzetfüzeteket a fürtbe. A jegyzetfüzetek fürtbe való feltöltéséhez feltöltheti őket a vagy a fürtöt futtató Jupyter Notebook használatával, vagy mentheti őket a `/HdiNotebooks` fürthöz társított Storage-fiók mappájába. A jegyzetfüzetek fürtön való tárolásával kapcsolatos további információkért lásd: [Hol vannak tárolva a Jupyter notebookok](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* A helyileg elérhető jegyzetfüzetek esetében az alkalmazásra vonatkozó követelmény alapján csatlakozhat különböző Spark-fürtökhöz.
* A GitHub segítségével implementálhatja a verziókövetés rendszerét, és vezérelheti a jegyzetfüzetek verziószámát. Olyan együttműködési környezetet is használhat, amelyben több felhasználó is dolgozhat ugyanazzal a jegyzetfüzettel.
* A jegyzetfüzetekkel helyileg is dolgozhat, anélkül, hogy fürtöt kellene létesítenie. Csak egy fürtre van szüksége a jegyzetfüzetek teszteléséhez, a jegyzetfüzetek és a fejlesztési környezetek manuális kezeléséhez nem.
* Könnyebben konfigurálhatja saját helyi fejlesztési környezetét, mint a Jupyter-telepítés konfigurálása a fürtön.  Kihasználhatja az összes helyileg telepített szoftver előnyeit anélkül, hogy egy vagy több távoli fürtöt kellene konfigurálnia.

> [!WARNING]  
> Ha a Jupyter telepítve van a helyi számítógépen, a több felhasználó ugyanazon a Spark-fürtön is futtathatja ugyanazt a jegyzetfüzetet. Ilyen esetben a rendszer több Livy-munkamenetet hoz létre. Ha problémát tapasztal, és azt szeretné, hogy a rendszer hibakeresést végezzen, az egy összetett feladat, amellyel nyomon követheti, hogy melyik Livy-munkamenet melyik felhasználóhoz tartozik.  

## <a name="next-steps"></a>Következő lépések

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)
* [A Apache Spark Jupyter Notebook kernelei](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
