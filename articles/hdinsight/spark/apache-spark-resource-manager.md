---
title: Apache Spark-fürt erőforrásainak kezelése az Azure HDInsight
description: Ismerje meg, hogyan kezelheti az Azure HDInsight-beli Spark-fürtök erőforrásait a jobb teljesítmény érdekében.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/12/2021
ms.openlocfilehash: ff7cfe8ad09201df20db89e14f8c175e678e5107
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929803"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Apache Spark-fürt erőforrásainak kezelése az Azure HDInsight

Megtudhatja, hogyan érheti el az olyan felületeket, mint az [Apache Ambari](https://ambari.apache.org/) felhasználói felület, a [Apache Hadoop a fonal](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) felhasználói felülete, valamint a [Apache Spark](https://spark.apache.org/) -fürthöz társított [Spark History-kiszolgáló](./apache-azure-spark-history-server.md) , valamint az optimális teljesítmény érdekében a fürtkonfiguráció finomhangolása.

## <a name="open-the-spark-history-server"></a>A Spark History-kiszolgáló megnyitása

A Spark History Server a Spark-alkalmazások befejezéséhez és futtatásához használható webes KEZELŐFELÜLET. Ez a Spark webes felhasználói felületének kiterjesztése. A teljes információ: [Spark History Server](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>A fonal felhasználói felületének megnyitása

A fonal felhasználói felületét használhatja a Spark-fürtön jelenleg futó alkalmazások figyelésére.

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a Spark-fürtöt. További információ: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters).

2. A **fürt irányítópultok** területen válassza a **fonal** lehetőséget. Ha a rendszer kéri, adja meg a Spark-fürt rendszergazdai hitelesítő adatait.

    ![A fonal felhasználói felületének indítása](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Azt is megteheti, hogy a Ambari felhasználói felületéről is elindítja a fonal felhasználói felületét. A Ambari felhasználói felületén navigáljon az  >  Active  >    >  **Resource Manager felhasználói felületén** található fonalak gyors hivatkozásaihoz.

## <a name="optimize-clusters-for-spark-applications"></a>Fürtök optimalizálása Spark-alkalmazásokhoz

A Spark konfigurálásához használható három kulcsfontosságú paraméter az alkalmazás követelményeitől függően a, a `spark.executor.instances` és a `spark.executor.cores` `spark.executor.memory` . A végrehajtó egy Spark-alkalmazáshoz indított folyamat. A feldolgozó csomóponton fut, és feladata az alkalmazás feladatainak elvégzése. A végrehajtók és a végrehajtói méretek alapértelmezett számának kiszámítása a munkavégző csomópontok száma és a munkavégző csomópont mérete alapján történik. Ezeket az információkat a rendszer a `spark-defaults.conf` fürt fő csomópontjain tárolja.

A három konfigurációs paramétert a fürt szintjén lehet konfigurálni (a fürtön futó összes alkalmazás esetében), illetve az egyes alkalmazásokhoz is megadható.

### <a name="change-the-parameters-using-ambari-ui"></a>Paraméterek módosítása Ambari felhasználói felület használatával

1. A Ambari felhasználói felületén navigáljon a **Spark2**  >  **configs**  >  **Egyéni Spark2 – Alapértelmezések** elemre.

    ![Paraméterek beállítása a Ambari Custom használatával](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Paraméterek beállítása a Ambari Custom használatával")

1. Az alapértelmezett értékek kiválóan alkalmasak arra, hogy négy Spark-alkalmazás párhuzamosan fusson a fürtön. Ezeket az értékeket a felhasználói felületen módosíthatja, ahogy az alábbi képernyőképen is látható:

    ![Paraméterek beállítása a Ambari használatával](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Paraméterek beállítása a Ambari használatával")

1. A konfigurációs módosítások mentéséhez kattintson a **Mentés** gombra. A lap tetején a rendszer felszólítja az összes érintett szolgáltatás újraindítására. Válassza az **Újraindítás** lehetőséget.

    ![Szolgáltatások újraindítása](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter Notebook futó alkalmazás paramétereinek módosítása

A Jupyter Notebook futó alkalmazások esetén a Magic használatával módosíthatja `%%configure` a konfigurációt. Ideális esetben az első kódrészlet futtatása előtt el kell végeznie az ilyen módosításokat az alkalmazás elején. Ezzel biztosíthatja, hogy a rendszer a konfigurációt a Livy-munkamenetre alkalmazza, amikor a rendszer létrehozza. Ha az alkalmazás egy későbbi szakaszában szeretné módosítani a konfigurációt, a paramétert kell használnia `-f` . Ezzel azonban az alkalmazás minden folyamata elvész.

A következő kódrészlet azt mutatja be, hogyan lehet módosítani egy Jupyter-ben futó alkalmazás konfigurációját.

```scala
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

A konfigurációs paramétereket JSON-karakterláncként kell átadni, és a Magic után a következő sorban kell szerepelniük, ahogy az a példában látható oszlopban látható.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>A Spark-Submit használatával elküldött alkalmazások paramétereinek módosítása

A következő parancs egy példa arra, hogyan lehet módosítani a használatával elküldött batch-alkalmazás konfigurációs paramétereit `spark-submit` .

```scala
spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>
```

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>A cURL használatával elküldött alkalmazások paramétereinek módosítása

A következő parancs egy példa arra, hogyan módosíthatja a cURL használatával elküldött batch-alkalmazás konfigurációs paramétereit.

```bash
curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches
```

> [!Note]
> Másolja a JAR-fájlt a fürt Storage-fiókjába. Ne másolja a JAR-fájlt közvetlenül a fő csomópontra.

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>A paraméterek módosítása a Spark takarékossági kiszolgálón

A Spark takarékossági kiszolgáló JDBC/ODBC-hozzáférést biztosít egy Spark-fürthöz, és a Spark SQL-lekérdezések kiszolgálására szolgál. Az olyan eszközök, mint például a Power BI, a tabló és így tovább, az ODBC protokoll használatával kommunikálhat a Spark takarékosság-kiszolgálóval a Spark SQL-lekérdezések Spark-alkalmazásként való végrehajtásához. Spark-fürt létrehozásakor a Spark takarékossági kiszolgáló két példánya indul el, egyet az egyes fő csomópontokon. Az egyes Spark-takarékossági kiszolgálók Spark-alkalmazásként láthatók a fonal felhasználói felületén.

A Spark takarékossági kiszolgáló a Spark dinamikus végrehajtó lefoglalását használja, ezért a `spark.executor.instances` nincs használatban. Ehelyett a Spark-takarékos kiszolgáló `spark.dynamicAllocation.maxExecutors` és `spark.dynamicAllocation.minExecutors` a végrehajtók számának megadására szolgál. A konfigurációs paraméterek a `spark.executor.cores` `spark.executor.memory` végrehajtó méretének módosítására szolgálnak. Ezeket a paramétereket a következő lépésekben látható módon módosíthatja:

* A paraméterek frissítéséhez bontsa ki a **speciális spark2-takarékosság-sparkconf** kategóriát `spark.dynamicAllocation.maxExecutors` `spark.dynamicAllocation.minExecutors` .

    ![A Spark takarékosság-kiszolgáló konfigurálása](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "A Spark takarékosság-kiszolgáló konfigurálása")

* Bontsa ki az **Egyéni spark2-takarékosság-sparkconf** kategóriát a paraméterek frissítéséhez `spark.executor.cores` és `spark.executor.memory` .

    ![A Spark takarékosság Server paraméter konfigurálása](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "A Spark takarékosság Server paraméter konfigurálása")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>A Spark takarékosság-kiszolgáló illesztőprogram-memóriájának módosítása

A Spark-kiszolgáló illesztőprogram-memóriája a fő csomópont RAM-méretének 25%-ában van konfigurálva, ha a fő csomópont teljes RAM-mérete meghaladja a 14 GB-ot. A Ambari felhasználói felületén módosíthatja az illesztőprogram-memória konfigurációját, ahogy az alábbi képernyőképen is látható:

A Ambari felhasználói felületén navigáljon a **Spark2**  >  **konfigurációk**  >  **speciális Spark2 – env** elemre. Ezután adja meg a **spark_thrift_cmd_opts** értékét.

## <a name="reclaim-spark-cluster-resources"></a>Spark-fürt erőforrásainak visszaigénylése

A Spark dinamikus kiosztása miatt a takarékossági kiszolgáló által felhasznált erőforrások csak a két alkalmazás-főkiszolgáló erőforrásai. Az erőforrások visszaigényléséhez le kell állítania a fürtön futó takarékossági kiszolgáló szolgáltatásait.

1. A Ambari felhasználói felületén, a bal oldali panelen válassza a **Spark2** lehetőséget.

2. A következő lapon válassza a **Spark2 takarékosság-kiszolgálók** lehetőséget.

    ![A takarékos Kiszolgáló1 újraindítása](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "A takarékos Kiszolgáló1 újraindítása")

3. Ekkor meg kell jelennie a két átjárócsomópontokkal, amelyen a Spark2-takarékossági kiszolgáló fut. Válassza ki az egyik átjárócsomópontokkal.

    ![A gazdaságosság Kiszolgáló2 újraindítása](./media/apache-spark-resource-manager/restart-thrift-server-2.png "A gazdaságosság Kiszolgáló2 újraindítása")

4. A következő oldalon az adott átjárócsomóponthoz futó összes szolgáltatás látható. A listából válassza ki a Spark2 takarékossági kiszolgáló melletti legördülő gombot, majd válassza a **Leállítás** lehetőséget.

    ![A gazdaságosság Server3 újraindítása](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "A gazdaságosság Server3 újraindítása")
5. Ismételje meg ezeket a lépéseket a többi átjárócsomóponthoz is.

## <a name="restart-the-jupyter-service"></a>A Jupyter szolgáltatás újraindítása

Indítsa el a Ambari webes felhasználói felületét a cikk elején látható módon. A bal oldali navigációs panelen válassza a **Jupyter** lehetőséget, válassza a **szolgáltatási műveletek** lehetőséget, majd kattintson az **összes újraindítása** elemre. Ezzel elindítja a Jupyter szolgáltatást az összes átjárócsomópontokkal.

![Jupyter újraindítása](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Jupyter újraindítása")

## <a name="monitor-resources"></a>Erőforrások megfigyelése

Indítsa el a fonal felhasználói felületét a cikk elején látható módon. A képernyő felső részén található fürt Metrikái táblában a **felhasznált memória** és a **memória összes** oszlopának értékét kell megnéznie. Ha a két érték be van zárva, előfordulhat, hogy nem áll rendelkezésre elegendő erőforrás a következő alkalmazás elindításához. Ugyanez vonatkozik a **használt virtuális mag** és a **virtuális mag összes** oszlopára is. Emellett a fő nézetben, ha egy alkalmazás **elfogadva** állapotba került, és nem a **futó** és a **sikertelen** állapotra vált, akkor ez azt is jelezheti, hogy nem kap elég erőforrást az induláshoz.

![Erőforrás-korlát](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Erőforrás-korlát")

## <a name="kill-running-applications"></a>Futó alkalmazások leölése

1. A fonal felhasználói felületén, a bal oldali panelen válassza a **Futtatás** lehetőséget. A futó alkalmazások listájából állapítsa meg a leölési alkalmazást, és válassza ki az **azonosítót**.

    ![App1 leölése](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "App1 leölése")

2. Válassza az **alkalmazás leölése** lehetőséget a jobb felső sarokban, majd kattintson **az OK gombra**.

    ![App2 leölése](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "App2 leölése")

## <a name="see-also"></a>Lásd még

* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Adatelemzők számára

* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)
* [Az Application Insight telemetria-adatelemzési szolgáltatásának használata a HDInsight-ben Apache Spark](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Apache Spark fejlesztőknek

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata a Apache Spark alkalmazások távoli hibakereséséhez](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [A HDInsight Apache Spark-fürtben Jupyter Notebook számára elérhető kernelek](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)
