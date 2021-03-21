---
title: Spark-feladatok hibakeresése a IntelliJ Azure Toolkit (előzetes verzió) használatával – HDInsight
description: Útmutatás az alkalmazások hibakereséséhez a Azure Toolkit for IntelliJ HDInsight eszközeinek használatával
keywords: távoli IntelliJ hibakeresése, távoli hibakeresés IntelliJ, SSH, IntelliJ, hdinsight, hibakeresési IntelliJ, hibakeresés
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 5abbb26e7582d8ddabb73f3a178cf6d87e24db21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942544"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Hiba a Spark-feladatok hibakereséséhez Azure Toolkit for IntelliJ (előzetes verzió)

Ez a cikk részletes útmutatást nyújt arról, hogyan használhatja a HDInsight-eszközöket a [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) a **Spark-hibák hibakeresési** alkalmazásainak futtatásához.

## <a name="prerequisites"></a>Előfeltételek

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Ez az oktatóanyag a Java-verzió 8.0.202 használja.
  
* IntelliJ ötlet. Ez a cikk a [INTELLIJ Idea Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows)-t használja.
  
* Azure Toolkit for IntelliJ. Lásd: [a Azure Toolkit for IntelliJ telepítése](/azure/developer/java/toolkit-for-intellij/installation).

* Kapcsolódjon a HDInsight-fürthöz. Lásd: [Kapcsolódás a HDInsight-fürthöz](apache-spark-intellij-tool-plugin.md).

* Microsoft Azure Storage Explorer. Lásd a [Microsoft Azure Storage Explorer letöltését](https://azure.microsoft.com/features/storage-explorer/)ismertető témakört.

## <a name="create-a-project-with-debugging-template"></a>Projekt létrehozása hibakeresési sablonnal

Hozzon létre egy Spark 2.3.2-projektet a hibák hibakeresésének folytatásához, végezze el a hiba feladatának hibakeresését a dokumentumban.

1. Nyissa meg az IntelliJ IDEA-t. Nyissa meg az **új projekt** ablakot.

   a. Válassza ki az **Azure Spark/HDInsight** elemet a bal oldali panelen.

   b. A főablakból válassza a **hiba feladatának hibakeresése (előzetes verzió) lehetőséget a Spark Project szolgáltatásban** .

     ![Hibakeresési projekt létrehozása a IntelliJ](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Válassza a **Tovább** gombot.

2. Az **új projekt** ablakban végezze el a következő lépéseket:

   ![Új IntelliJ-projekt kiválasztása Spark-verzió](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Adja meg a projekt nevét és a projekt helyét.

   b. A **Project SDK** legördülő listában válassza ki a következőt: **Java 1,8** for **Spark 2.3.2** -fürt.

   c. A **Spark-verzió** legördülő listában válassza a **Spark 2.3.2 (Scala 2.11.8)** elemet.

   d. Válassza a **Befejezés** gombot.

3. Válassza a **src**  >  **Main**  >  **Scala** elemet a kód a projektben való megnyitásához. Ez a példa a **AgeMean_Div ()** parancsfájlt használja.

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Spark Scala/Java-alkalmazás futtatása HDInsight-fürtön

Hozzon létre egy Spark Scala/Java-alkalmazást, majd futtassa az alkalmazást egy Spark-fürtön az alábbi lépések végrehajtásával:

1. Kattintson a **konfiguráció hozzáadása** elemre a **futtatási/hibakeresési konfigurációk** ablak megnyitásához.

   ![HDI IntelliJ-konfiguráció hozzáadása](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. A **Futtatás/hibakeresés konfiguráció** párbeszédpanelen válassza a pluszjelet ( **+** ). Ezután válassza ki a **Apache Spark a HDInsight** beállításnál.

   ![IntelliJ új konfiguráció hozzáadása](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Váltson **távoli futtatásra a fürt** lapon. Adja meg a **név**, a **Spark-fürt** és a **fő osztály nevét**. Eszközeink támogatják a **végrehajtókkal** való hibakeresést. A **numExectors**, az alapértelmezett érték 5, és jobb, ha nem nagyobb, mint 3. A futási idő csökkentése érdekében a **Spark. fonál. maxAppAttempts** felveheti a **feladatok konfigurációba** , és az értéket 1-re állíthatja. A konfiguráció mentéséhez kattintson **az OK** gombra.

   ![IntelliJ új hibakeresési konfigurációk futtatása](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. A konfiguráció mostantól a megadott névvel lett mentve. A konfiguráció részleteinek megtekintéséhez válassza ki a konfiguráció nevét. A módosítások elvégzéséhez válassza a **konfigurációk szerkesztése** lehetőséget.

5. A konfigurációk beállításainak elvégzése után a projektet a távoli fürtön futtathatja.

   ![IntelliJ hibakeresése távoli Spark-feladatok távoli Futtatás gombja](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Az alkalmazás AZONOSÍTÓját a kimenet ablakban tekintheti meg.

   ![IntelliJ hibakeresése távoli Spark-feladatok távoli futtatásának eredménye](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Sikertelen munkahelyi profil letöltése

Ha a feladatok elküldése meghiúsul, a további hibakereséshez letöltheti a sikertelen feladatot a helyi gépre.

1. Nyissa meg **Microsoft Azure Storage Explorer**, keresse meg a sikertelen feladatokhoz tartozó fürt HDInsight-fiókját, töltse le a sikertelen feladatok erőforrásait a megfelelő helyről: **\hdp\spark2-Events \\ . \\ \<application ID> Spark – hibák** egy helyi mappába. A **tevékenységek** ablakban megjelennek a letöltési folyamat.

   ![Azure Storage Explorer letöltési hiba](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Azure Storage Explorer sikeres letöltés](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Helyi hibakeresési környezet konfigurálása és sikertelen hibakeresés

1. Nyissa meg az eredeti projektet, vagy hozzon létre egy új projektet, és társítsa az eredeti forráskódhoz. Jelenleg csak a Spark 2.3.2 verziója támogatott a hibák hibakereséséhez.

1. A IntelliJ IDEA-ben hozzon létre egy **Spark-hiba hibakeresési** konfigurációs fájlját, válassza ki az FTD-fájlt a korábban letöltött sikertelen feladatok erőforrásai közül a **Spark-feladatokhoz tartozó környezeti hely** mezőnél.

   ![Crete-hibák konfigurálása](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Kattintson a helyi Futtatás gombra az eszköztáron, a hiba a Futtatás ablakban jelenik meg.

   ![Futtatás – hiba – configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![Futtatás – hiba – configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Állítsa be a töréspontot a naplóban, majd kattintson a helyi hibakeresés gombra a helyi hibakereséshez ugyanúgy, mint a IntelliJ szokásos Scala/Java-projektjei.

1. A hibakeresés után, ha a projekt sikeresen befejeződik, újra elküldheti a sikertelen feladatot a Spark on HDInsight-fürtön.

## <a name="next-steps"></a><a name="seealso"></a>Következő lépések

* [Áttekintés: Apache Spark alkalmazások hibakeresése](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Bemutató

* Scala-projekt létrehozása (videó): [Apache Spark Scala-alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresés (videó): a [Azure Toolkit for IntelliJ használata Apache Spark alkalmazások távoli hibakereséséhez egy HDInsight-fürtön](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark BI-val: interaktív adatelemzést végezhet a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learningrel: a Spark in HDInsight használatával elemezze az építési hőmérsékletet a HVAC-adataival](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](./apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Azure Toolkit for IntelliJ használata Apache Spark-alkalmazások HDInsight-fürthöz való létrehozásához](apache-spark-intellij-tool-plugin.md)
* [Apache Spark-alkalmazások távoli hibakeresése a Azure Toolkit for IntelliJ használatával VPN-en keresztül](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [A IntelliJ HDInsight-eszközeinek használata a Hortonworks-homokozóban](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Apache Spark alkalmazások létrehozásához használja a Azure Toolkit for Eclipse HDInsight-eszközeit](./apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [A HDInsight Apache Spark-fürtben Jupyter Notebook számára elérhető kernelek](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)