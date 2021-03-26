---
title: 'Azure Toolkit for IntelliJ: Spark-alkalmazások hibakeresése SSH-HDInsight'
description: Részletes útmutató a HDInsight-eszközök használatáról a Azure Toolkit for IntelliJban az alkalmazások távoli HDInsight-fürtökön keresztül SSH-n keresztül történő hibakereséséhez
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: de838e094c8a37d375aa6c7649ee5717705ad33c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866352"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Apache Spark-alkalmazások hibakeresése HDInsight-fürtön Azure Toolkit for IntelliJ SSH-n keresztül

Ez a cikk részletes útmutatást nyújt arról, hogyan használhatók a [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) HDInsight eszközei az alkalmazások távoli HDInsight-fürtökön való hibakereséséhez. A projekt hibakereséséhez a [HDInsight Spark-alkalmazásokat Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) videóval is megtekintheti.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. Lásd: [Apache Spark-fürt létrehozása](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Windows-felhasználók esetén: a helyi Spark Scala-alkalmazás Windows rendszerű számítógépen való futtatásakor kivételt jelenthet a [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356)című részben leírtak szerint. A kivétel oka, hogy WinUtils.exe hiányzik a Windows rendszerből.

    A hiba elhárításához töltse le [Winutils.exet](https://github.com/steveloughran/winutils) egy olyan helyre, mint például a **C:\WinUtils\bin**. Ezután adja hozzá **HADOOP_HOME** környezeti változót, és állítsa a változó értékét **C:\WinUtils** értékre.

* [INTELLIJ Idea](https://www.jetbrains.com/idea/download/#section=windows) (a Community Edition ingyenes.)

* [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* [IntelliJ-hez készült Scala beépülő modul](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Spark Scala-alkalmazás létrehozása

1. Indítsa el a IntelliJ ÖTLETET, és válassza az **új projekt létrehozása** elemet az **új projekt** ablak megnyitásához.

1. Válassza ki a **Apache Spark/HDInsight** elemet a bal oldali ablaktáblán.

1. Válassza ki a **Spark Project with Samples (Scala)** elemet a főablakból.

1. A **Build eszköz** legördülő listából válassza ki a következők egyikét:

    * **Maven** a Scala projekt-létrehozás varázsló támogatásához.
    * A **SBT** kezelése és a Scala-projekt kiépítése.

     :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png" alt-text="IntelliJ – új projekt létrehozása Spark" border="true":::

1. Kattintson a **Tovább** gombra.

1. A következő **új projekt** ablakban adja meg a következő információkat:

    |Tulajdonság |Leírás |
    |---|---|
    |Projektnév|Adjon meg egy nevet. Ez az útmutató a felhasználási módokon érhető el `myApp` .|
    |Projekt helye|Adja meg a kívánt helyet a projekt mentéséhez.|
    |Projekt SDK|Ha üres, válassza az **új...** lehetőséget, és navigáljon a JDK-hez.|
    |Spark-verzió|A létrehozás varázsló a Spark SDK és a Scala SDK megfelelő verzióját integrálja. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Ellenkező esetben válassza a **Spark 2. x.** elemet. Ez a példa a **Spark 2.3.0 (Scala 2.11.8)** használja.|

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png" alt-text="Új IntelliJ-projekt kiválasztása Spark-verzió" border="true":::

1. Válassza a **Befejezés** gombot. A projekt elérhetővé válása néhány percet igénybe vehet. Tekintse meg a jobb alsó sarokban a folyamat előrehaladását.

1. Bontsa ki a projektet, és navigáljon az **src**  >  **Main**  >  **Scala**-  >  **mintához**. Kattintson duplán a **SparkCore_WasbIOTest** elemre.

## <a name="perform-local-run"></a>Helyi futtatás végrehajtása

1. A **SparkCore_WasbIOTest** parancsfájlban kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a **"SparkCore_WasbIOTest"** parancsot a helyi Futtatás végrehajtásához.

1. A helyi Futtatás befejezése után a kimeneti fájl mentése a jelenlegi **Project Explorer-**  >  **__adatalapértelmezett__** értékre.

    :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png" alt-text="IntelliJ projekt helyi futtatásának eredménye" border="true":::

1. Az eszközök a helyi Futtatás és a helyi hibakeresés végrehajtásakor automatikusan beállították az alapértelmezett helyi futtatási konfigurációt. A jobb felső sarokban található **[Spark on HDInsight] XXX** megnyitásával megtekintheti a **[Spark on HDInsight] XXX** -t, amelyet a **HDInsight**-ben már Apache Spark létrehoztak. Váltson a **helyi Futtatás** lapra.

    :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png" alt-text="IntelliJ helyi futtatású hibakeresési konfigurációk futtatása" border="true":::

    - [Környezeti változók](#prerequisites): Ha már beállította a rendszerkörnyezeti változót a **C:\WinUtils** **HADOOP_HOME** , akkor az automatikusan felismeri, hogy nincs szükség a manuális hozzáadására.
    - [WinUtils.exe hely](#prerequisites): Ha nem állította be a rendszerkörnyezeti változót, akkor a gombra kattintva megkeresheti a helyet.
    - Csupán két lehetőség közül választhat, és a MacOS és a Linux rendszeren nem szükséges.

1. A konfigurációt manuálisan is beállíthatja a helyi Futtatás és a helyi hibakeresés végrehajtása előtt. Az előző képernyőképen válassza a pluszjelet ( **+** ). Ezután válassza ki a **Apache Spark a HDInsight** beállításnál. Adja meg a **név**, a **fő osztály nevét** , a menteni kívánt adatokat, majd kattintson a helyi Futtatás gombra.

## <a name="perform-local-debugging"></a>Helyi hibakeresés végrehajtása

1. Nyissa meg a **SparkCore_wasbloTest** szkriptet, és állítsa be a töréspontokat.

1. A helyi hibakeresés végrehajtásához kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a **"[Spark on HDInsight] xxx"** lehetőséget.

## <a name="perform-remote-run"></a>Távoli futtatás végrehajtása

1. Navigáljon a konfigurációk szerkesztési beállításainak **futtatásához**  >  **..**. Ebből a menüből létrehozhat vagy szerkesztheti a távoli hibakeresés konfigurációit.

1. A **Futtatás/hibakeresés konfiguráció** párbeszédpanelen válassza a pluszjelet ( **+** ). Ezután válassza ki a **Apache Spark a HDInsight** beállításnál.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png" alt-text="IntelliJ új konfiguráció hozzáadása" border="true":::

1. Váltson **távoli futtatásra a fürt** lapon. Adja meg a **név**, a **Spark-fürt** és a **fő osztály nevét**. Ezután kattintson a **Speciális konfiguráció (távoli hibakeresés)** elemre. Eszközeink támogatják a **végrehajtókkal** való hibakeresést. A **numExectors** az alapértelmezett érték 5. Nagyobb, mint 3.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png" alt-text="Hibakeresési konfigurációk futtatása IntelliJ" border="true":::

1. A **Speciális konfiguráció (távoli hibakeresés)** részben válassza a **Spark távoli hibakeresés engedélyezése** lehetőséget. Adja meg az SSH-felhasználónevet, majd írjon be egy jelszót, vagy használjon titkos kulcsot tartalmazó fájlt. Ha távoli hibakeresést szeretne végezni, be kell állítania azt. Ha csak a távoli futtatást szeretné használni, nem kell beállítania.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png" alt-text="IntelliJ speciális konfiguráció a Spark távoli hibakeresésének engedélyezése" border="true":::

1. A konfiguráció mostantól a megadott névvel lett mentve. A konfiguráció részleteinek megtekintéséhez válassza ki a konfiguráció nevét. A módosítások elvégzéséhez válassza a **konfigurációk szerkesztése** lehetőséget.

1. A konfigurációk beállításainak elvégzése után futtathatja a projektet a távoli fürtön, vagy távoli hibakeresést végezhet.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png" alt-text="IntelliJ hibakeresése távoli Spark-feladatok távoli Futtatás gombja" border="true":::

1. Kattintson a **Leválasztás** gombra, hogy a beküldési naplók ne jelenjenek meg a bal oldali panelen. Azonban továbbra is fut a háttérön.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png" alt-text="IntelliJ hibakeresése távoli Spark-feladatok távoli futtatásának eredménye" border="true":::

## <a name="perform-remote-debugging"></a>Távoli hibakeresés végrehajtása

1. Állítsa be a törési pontokat, majd kattintson a **távoli hibakeresés** ikonra. A távoli beküldéssel való különbség az, hogy az SSH-felhasználónevet/jelszót be kell állítani.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png" alt-text="IntelliJ hibakeresése távoli Spark-feladatok hibakeresési ikonja" border="true":::

1. Amikor a program végrehajtása eléri a feltörési pontot, megjelenik egy **illesztőprogram** lap és két **végrehajtó** lap a **hibakereső** ablaktáblán. Válassza a **program folytatása** ikont a kód futtatásának folytatásához, amely ezután eléri a következő töréspontot. A hibakereséshez a megfelelő **végrehajtó** lapra kell váltania. A végrehajtási naplókat a megfelelő **konzol** lapon tekintheti meg.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png" alt-text="IntelliJ hibakeresése távoli Spark-feladatok hibakeresése lap" border="true":::

### <a name="perform-remote-debugging-and-bug-fixing"></a>Távoli hibakeresés és hibajavítás

1. Állítson be két töréspontot, majd válassza a **hibakeresés** ikont a Távoli hibakeresési folyamat elindításához.

1. A kód az első feltörési ponton leáll, és a paraméterek és a változó információk a **változók** ablaktáblán jelennek meg.

1. A folytatáshoz válassza a **program folytatása** ikont. A kód a második ponton leáll. A kivételt a rendszer a várt módon kapja meg.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png" alt-text="IntelliJ hibakeresése távoli Spark-feladatokkal – hiba" border="true":::

1. Válassza újra a **program folytatása** ikont. A **HDInsight Spark beküldési** ablakában a "sikertelen feladatok futtatása" hibaüzenet jelenik meg.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png" alt-text="IntelliJ hibakeresése távoli Spark-feladatokkal kapcsolatos hibák elküldése" border="true":::

1. Ha a IntelliJ hibakeresési funkciójával szeretné dinamikusan frissíteni a változó értékét, válassza a **hibakeresés** újra lehetőséget. A **változók** ablaktábla ismét megjelenik.

1. Kattintson a jobb gombbal a cél elemre a **hibakeresés** lapon, majd válassza az **érték beállítása** lehetőséget. Ezután adjon meg egy új értéket a változóhoz. Ezután válassza az **ENTER billentyűt** az érték mentéséhez.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png" alt-text="IntelliJ hibakeresése távoli Spark-feladatütemezés értéke" border="true":::

1. Válassza a **program folytatása** ikont a program futtatásának folytatásához. Ezúttal egyetlen kivétel sem kerül kifogásra. Láthatja, hogy a projekt kivételek nélkül fut sikeresen.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png" alt-text="IntelliJ hibakeresése távoli Spark-feladatokhoz kivétel nélkül" border="true":::

## <a name="next-steps"></a>Következő lépések

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="demo"></a>Bemutató

* Scala-projekt létrehozása (videó): [Apache Spark Scala-alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresés (videó): a [Azure Toolkit for IntelliJ használata Apache Spark alkalmazások távoli hibakereséséhez egy HDInsight-fürtön](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learningrel: a Spark in HDInsight használatával elemezze az építési hőmérsékletet a HVAC-adataival](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](./apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Azure Toolkit for IntelliJ használata Apache Spark-alkalmazások HDInsight-fürthöz való létrehozásához](apache-spark-intellij-tool-plugin.md)
* [Apache Spark-alkalmazások távoli hibakeresése a Azure Toolkit for IntelliJ használatával VPN-en keresztül](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Spark alkalmazások létrehozásához használja a Azure Toolkit for Eclipse HDInsight-eszközeit](./apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [A HDInsight Apache Spark-fürtben Jupyter Notebook számára elérhető kernelek](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)