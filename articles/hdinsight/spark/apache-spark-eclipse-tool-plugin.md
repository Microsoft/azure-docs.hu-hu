---
title: 'Azure Toolkit for Eclipse: Scala-alkalmazások létrehozása a HDInsight Sparkhoz'
description: Az Azure Toolkit for Eclipse HDInsight eszközeivel fejlesztheti a Scalaban írt Spark-alkalmazásokat, és beküldheti azokat egy HDInsight Spark-fürtbe közvetlenül az Eclipse IDE-ből.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: e4f19afc0edf0f43b64548ae1c93ed5ff8dfa8a1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866573"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Azure Toolkit for Eclipse használata Apache Spark-alkalmazások HDInsight-fürthöz való létrehozásához

Az [Eclipse](https://www.eclipse.org/) -hez készült Azure Toolkit HDInsight eszközeivel fejlesztheti [Apache Spark](https://spark.apache.org/) a [scalaban](https://www.scala-lang.org/) írt alkalmazásokat, és beküldheti azokat egy Azure HDInsight Spark-fürtbe közvetlenül az Eclipse ide-ből. A HDInsight-eszközök beépülő modult többféleképpen is használhatja:

* Scala Spark-alkalmazás fejlesztése és beküldése egy HDInsight Spark-fürtön.
* A Azure HDInsight Spark-fürt erőforrásainak eléréséhez.
* Egy Scala Spark-alkalmazás helyi fejlesztése és futtatása.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark fürt a HDInsight-on. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* A [Java Developer Kit (JDK) 8-as verziója](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Eclipse ide](https://www.eclipse.org/downloads/). Ez a cikk a Java-fejlesztőknek készült Eclipse IDE-t használja.

## <a name="install-required-plug-ins"></a>A szükséges beépülő modulok telepítése

### <a name="install-azure-toolkit-for-eclipse"></a>Az Azure Toolkit for Eclipse telepítése

A telepítési utasításokért lásd: [Azure Toolkit for Eclipse telepítése](/azure/developer/java/toolkit-for-eclipse/installation).

### <a name="install-the-scala-plug-in"></a>A Scala beépülő modul telepítése

Az Eclipse megnyitásakor a HDInsight-eszközök automatikusan észlelik, hogy telepítette-e a Scala beépülő modult. A folytatáshoz kattintson az **OK** gombra, majd kövesse az utasításokat a beépülő modul az Eclipse piactérről való telepítéséhez. A telepítés befejezése után indítsa újra az IDE-t.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png" alt-text="A Scala beépülő modul automatikus telepítése" border="true":::

### <a name="confirm-plug-ins"></a>Beépülő modulok megerősítése

1. Navigáljon **az**  >  **Eclipse Marketplace**-re...

1. Válassza a **telepített** lapot.

1. Legalább a következőket kell látnia:
    * Azure Toolkit for Eclipse \<version> .
    * Scala IDE \<version> .

## <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésbe

1. Indítsa el az Eclipse IDE-t.

1. Navigáljon a másik **ablak**  >   **Megjelenítés nézetéhez**  >  **...**  >  **Bejelentkezés..**.

1. A **nézet megjelenítése** párbeszédpanelen navigáljon az **Azure**  >  **Azure Explorer** elemre, majd válassza a **Megnyitás** lehetőséget.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png" alt-text="Apache Spark Eclipse show nézet" border="true":::

1. Az **Azure Explorerben** kattintson a jobb gombbal az **Azure** -csomópontra, majd válassza a **Bejelentkezés** lehetőséget.

1. Az **Azure bejelentkezési** párbeszédpanelen válassza ki a hitelesítési módszert, válassza a **Bejelentkezés** lehetőséget, és fejezze be a bejelentkezési folyamatot.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png" alt-text="Apache Spark Eclipse Azure-bejelentkezés" border="true":::

1. Miután bejelentkezett, a **saját előfizetések** párbeszédpanel felsorolja a hitelesítő adatokhoz társított összes Azure-előfizetést. A párbeszédpanel bezárásához kattintson a **kiválasztás** gombra.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png" alt-text="Előfizetések kiválasztása párbeszédpanel" border="true":::

1. Az **Azure Explorerben** navigáljon az **Azure**  >   **HDInsight** , és tekintse meg az előfizetéshez tartozó HDInsight Spark-fürtöket.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png" alt-text="HDInsight Spark-fürtök az Azure Explorer3" border="true":::

1. A fürthöz társított erőforrások (például Storage-fiókok) megtekintéséhez tovább bővítheti a fürt neve csomópontot.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png" alt-text="A fürt nevének kibontása az erőforrások megjelenítéséhez" border="true":::

## <a name="link-a-cluster"></a>Fürt csatolása

A Ambari által felügyelt Felhasználónév használatával összekapcsolhatja a normál fürtöt. Hasonlóképpen, a tartományhoz csatlakoztatott HDInsight-fürtökhöz a tartomány és a Felhasználónév használatával is kapcsolódhat, például: `user1@contoso.com` .

1. Az **Azure Explorerben** kattintson a jobb gombbal a **HDInsight** elemre, majd válassza **a fürt csatolása** lehetőséget.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png" alt-text="Azure Explorer hivatkozás fürt menüje" border="true":::

1. Adja meg a **fürt nevét**, a **felhasználónevet** és a **jelszót**, majd kattintson **az OK gombra**. Szükség esetén megadhatja a Storage-fiókot, a Storage-kulcsot, majd kiválaszthatja a Storage Explorer tárolási tárolóját a bal oldali fanézetben való működéshez.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png" alt-text="Új HDInsight-fürt csatolása párbeszédpanel" border="true":::

   > [!NOTE]  
   > A csatolt Storage-kulcsot, a felhasználónevet és a jelszót használjuk, ha a fürt az Azure-előfizetésben is be van jelentkezve, és egy fürthöz kapcsolódik.
   > :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png" alt-text="Azure Explorer Storage-fiókok" border="true":::
   >
   > A billentyűzet csak a felhasználó számára, ha az aktuális fókusz a **tárolási kulcsban** van, a **CTRL + TAB** billentyűkombinációt kell használnia a párbeszédpanel következő mezőjére koncentrálva.

1. A csatolt fürtöt a **HDInsight** alatt tekintheti meg. Most elküldheti az alkalmazást ehhez a csatolt fürthöz.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png" alt-text="Azure Explorer HDI csatolt fürt" border="true":::

1. A fürtöket az **Azure Explorerben** is leválaszthatja.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png" alt-text="Azure Explorer – nem csatolt fürt" border="true":::

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Spark Scala-projekt beállítása HDInsight Spark-fürthöz

1. Az Eclipse ide munkaterületen válassza a **fájl**  >  **új**  >  **projekt...** lehetőséget.

1. Az **új projekt** varázslóban válassza a **HDInsight Project**  >  **Spark a HDInsight (Scala)** lehetőséget. Ezután kattintson a **Tovább** gombra.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png" alt-text="A Spark on HDInsight (Scala) projekt kiválasztása" border="true":::

1. Az **új HDInsight Scala projekt** párbeszédpanelen adja meg a következő értékeket, majd válassza a Next ( **tovább**) gombot:
   * Adjon meg a projekt nevét.
   * A **JRE** területen győződjön meg arról, hogy a **végrehajtási környezeti JRE használata** **javase-1,7** vagy újabb értékre van beállítva.
   * A **Spark-könyvtár** területen válassza a Maven használata lehetőséget a **Spark SDK konfigurálásához** .  Az eszköz a Spark SDK és a Scala SDK megfelelő verzióját integrálja. Manuálisan is választhatja a **Spark SDK hozzáadása manuálisan** lehetőséget, a letöltést és a Spark SDK hozzáadását.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png" alt-text="Új HDInsight Scala-projekt párbeszédpanel" border="true":::

1. A következő párbeszédpanelen tekintse át a részleteket, majd kattintson a **Befejezés gombra**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Scala-alkalmazás létrehozása HDInsight Spark-fürthöz

1. A **Package Explorerben** bontsa ki a korábban létrehozott projektet. Kattintson a jobb gombbal az **src** elemre, majd válassza az **új**  >  **másik.**.. lehetőséget.

1. A **varázsló kiválasztása** párbeszédpanelen válassza a **Scala varázslók**  >  **Scala-objektum** lehetőséget. Ezután kattintson a **Tovább** gombra.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png" alt-text="Varázsló kiválasztása a Scala-objektum létrehozásához" border="true":::

1. Az **új fájl létrehozása** párbeszédpanelen adja meg az objektum nevét, majd kattintson a **Befejezés gombra**. Ekkor megnyílik egy szövegszerkesztő.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png" alt-text="Új fájl varázsló új fájl létrehozása" border="true":::

1. A szövegszerkesztőben cserélje le az aktuális tartalmat az alábbi kódra:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. Futtassa az alkalmazást egy HDInsight Spark-fürtön:

   a. A Package Explorerben kattintson a jobb gombbal a projekt nevére, majd válassza a **Spark-alkalmazás elküldése a HDInsight** lehetőséget.

   b. A **Spark beküldése** párbeszédpanelen adja meg a következő értékeket, majd válassza a **Submit (Küldés**) lehetőséget:

   * A **fürt neve** mezőben válassza ki azt a HDInsight Spark-fürtöt, amelyen futtatni szeretné az alkalmazást.
   * Válasszon ki egy összetevőt az Eclipse-projektből, vagy válasszon egy merevlemezből. Az alapértelmezett érték attól függ, hogy melyik elemre kattint a jobb gombbal a Package Explorerben.
   * A **Főosztály neve** legördülő listában a beküldési varázsló megjeleníti a projekt összes objektumának nevét. Válassza ki vagy adja meg az egyiket, amelyet futtatni szeretne. Ha az összetevőt egy merevlemezről választotta, akkor manuálisan kell megadnia a fő osztály nevét. 
   * Mivel ebben a példában az alkalmazás kódjában nincs szükség parancssori argumentumokra vagy hivatkozási Tégelyekre vagy fájlokra, a fennmaradó szövegmezőket üresen hagyhatja.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png" alt-text="Apache Spark beküldési párbeszédpanel" border="true":::

1. A **Spark beküldési** lapján meg kell kezdeni a folyamat megjelenítését. Az alkalmazás leállításához kattintson a **Spark beküldési** ablakban a vörös gombra. Megtekintheti az adott alkalmazáshoz tartozó naplókat is, ha kiválasztja a földgömb ikont (a képen a kék mező jelzi).

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png" alt-text="Apache Spark beküldési ablak" border="true":::

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>HDInsight Spark-fürtök elérése és kezelése a Azure Toolkit for Eclipse HDInsight eszközeinek használatával

A HDInsight-eszközök használatával különböző műveleteket hajthat végre, beleértve a feladatok kimenetének elérését is.

### <a name="access-the-job-view"></a>A feladatok nézet elérése

1. Az **Azure Explorerben** bontsa ki a **HDInsight**, majd a Spark-fürt neve elemet, majd válassza a **feladatok** lehetőséget.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png" alt-text="Azure Explorer Eclipse-feladatok nézet csomópontja" border="true":::

1. Válassza a **feladatok** csomópontot. Ha a Java-verzió a **1,8**-nál kisebb, a HDInsight-eszközök automatikusan emlékeztetik az **E (FX) klipek** beépülő moduljának telepítésére. A folytatáshoz kattintson az **OK gombra** , majd kövesse a varázslót az Eclipse piactéren való telepítéshez és az Eclipse újraindításához.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png" alt-text="A hiányzó beépülő modul E (FX) klipek telepítése" border="true":::

1. Nyissa meg a feladat nézetet a **feladatok** csomópontból. A jobb oldali ablaktáblán a **Spark-feladatok nézet** lapon a fürtön futó összes alkalmazás látható. Válassza ki annak az alkalmazásnak a nevét, amelyhez további részleteket szeretne látni.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png" alt-text="Apache Eclipse – megtekintési feladatok naplóinak részletei" border="true":::

   Ezután a következő műveletek bármelyikét elvégezheti:

   * Vigye a kurzort a feladatok gráfra. A futó feladatra vonatkozó alapszintű információkat jeleníti meg. Válassza ki a feladathoz tartozó gráfot, és tekintse meg az egyes feladatok által generált szakaszokat és információkat.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png" alt-text="Apache Spark a feladatok gráf szakaszának adatai" border="true":::

   * A **napló** lapon megtekintheti a gyakran használt naplókat, beleértve az **illesztőprogram-stderr**, az **illesztőprogram StdOut**-ját és a **címtár-információkat**.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png" alt-text="Apache Spark Eclipse-Projektnapló adatai" border="true":::

   * Nyissa meg a Spark előzmények felhasználói felületét és a Apache Hadoop fonal felhasználói felületét (az alkalmazás szintjén) az ablak tetején található hiperhivatkozások kiválasztásával.

### <a name="access-the-storage-container-for-the-cluster"></a>A fürt tárolójának elérése

1. Az Azure Explorerben bontsa ki a **HDInsight** csomópontot, és tekintse meg az elérhető HDInsight Spark-fürtök listáját.

1. Bontsa ki a fürt nevét a Storage-fiók és a fürt alapértelmezett tárolójának megtekintéséhez.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png" alt-text="Storage-fiók és alapértelmezett tároló" border="true":::

1. Válassza ki a fürthöz társított Storage-tároló nevét. A jobb oldali ablaktáblán kattintson duplán a **HVACOut** mappára. Az alkalmazás kimenetének megjelenítéséhez nyissa meg az egyik **rész –** fájlok lehetőséget.

### <a name="access-the-spark-history-server"></a>A Spark-előzmények kiszolgálójának elérése

1. Az Azure Explorerben kattintson a jobb gombbal a Spark-fürt nevére, majd válassza a **Spark-előzmények megnyitása felhasználói felület** lehetőséget. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait. Ezeket a fürt kiépítés közben adta meg.

1. A Spark verziótörténete-kiszolgáló irányítópultján az alkalmazás neve segítségével keresse meg az éppen futó alkalmazást. Az előző kódban a paranccsal állíthatja be az alkalmazás nevét `val conf = new SparkConf().setAppName("MyClusterApp")` . Így a Spark-alkalmazás neve **MyClusterApp** volt.

### <a name="start-the-apache-ambari-portal"></a>Az Apache Ambari portál elindítása

1. Az Azure Explorerben kattintson a jobb gombbal a Spark-fürt nevére, majd válassza a **fürt felügyeleti portál megnyitása (Ambari)** lehetőséget.

1. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait. Ezeket a fürt kiépítés közben adta meg.

### <a name="manage-azure-subscriptions"></a>Azure-előfizetések kezelése

Alapértelmezés szerint a HDInsight eszköz a Azure Toolkit for Eclipse felsorolja a Spark-fürtöket az összes Azure-előfizetésből. Ha szükséges, megadhatja azokat az előfizetéseket, amelyekhez el szeretné érni a fürtöt.

1. Az Azure Explorerben kattintson a jobb gombbal az **Azure** -legfelső szintű csomópontra, majd válassza az **Előfizetések kezelése** lehetőséget.

1. A párbeszédpanelen törölje a nem kívánt előfizetéshez tartozó jelölőnégyzeteket, majd kattintson a **Bezárás** gombra. A kijelentkezés lehetőséget is választhatja, **Ha ki szeretné** jelentkezni az Azure-előfizetésből.

## <a name="run-a-spark-scala-application-locally"></a>Spark Scala-alkalmazás helyi futtatása

A Azure Toolkit for Eclipse HDInsight eszközeivel helyileg is futtathat Spark Scala-alkalmazásokat a munkaállomáson. Ezeknek az alkalmazásoknak általában nincs szükségük a fürt erőforrásaihoz, például a tárolóhoz való hozzáféréshez, és a helyileg futtathatja és tesztelheti is azokat.

### <a name="prerequisite"></a>Előfeltétel

Miközben a helyi Spark Scala alkalmazást futtatja egy Windows rendszerű számítógépen, előfordulhat, hogy kivételt tapasztal a [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Ez a kivétel azért fordul elő, mert **WinUtils.exe** hiányzik a Windows rendszerből.

A hiba megoldásához [Winutils.exe](https://github.com/steveloughran/winutils) kell egy olyan helyre, mint a **C:\WinUtils\bin**, majd hozzá kell adnia a környezeti változót **HADOOP_HOME** , és a változó értékét **C\WinUtils** értékre kell állítania.

### <a name="run-a-local-spark-scala-application"></a>Helyi Spark Scala-alkalmazás futtatása

1. Indítsa el az Eclipse-t és hozzon létre egy projektet. Az **új projekt** párbeszédpanelen hajtsa végre a következő beállításokat, majd válassza a **tovább** lehetőséget.

1. Az **új projekt** varázslóban válassza a **HDInsight Project** Spark lehetőséget a  >  **HDInsight helyi futtatási minta (Scala)** elemnél. Ezután kattintson a **Tovább** gombra.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png" alt-text="Az új projekt kiválasztja a varázsló párbeszédpanelt" border="true":::

1. A projekt részleteinek megadásához kövesse a [HDInsight Spark-fürthöz tartozó Spark Scala-projekt beállítása](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster)a korábbi szakasz 3 – 6. lépését.

1. A sablon egy mintakód (**LogQuery**) hozzáadására szolgál az **src** mappában, amelyet helyileg futtathat a számítógépen.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/local-scala-application.png" alt-text="A LogQuery helyi Scala-alkalmazás helye" border="true":::

1. Kattintson a jobb gombbal a **LogQuery. Scala** elemre, majd válassza **a Futtatás**  >  **1 Scala-alkalmazásként** lehetőséget. A következőhöz hasonló kimenet jelenik meg a **konzol** lapon:

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png" alt-text="Spark-alkalmazás helyi futtatásának eredménye" border="true":::

## <a name="reader-only-role"></a>Csak olvasó szerepkör

Ha a felhasználók a csak olvasási szerepkörrel rendelkező fürthöz küldenek feladatokat, a Ambari hitelesítő adatai szükségesek.

### <a name="link-cluster-from-context-menu"></a>Fürt csatolása a helyi menüből

1. Jelentkezzen be csak olvasó szerepkörrel rendelkező fiókkal.

2. Az **Azure Explorerben** bontsa ki a **HDInsight** elemet az előfizetésében található HDInsight-fürtök megtekintéséhez. A **"szerepkör: olvasó"** jelölésű fürtökön csak a csak olvasási jogosultsággal rendelkező szerepkör jogosult.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png" alt-text="HDInsight Spark-fürtök az Azure Explorer szerepkör-olvasóban" border="true":::

3. Kattintson a jobb gombbal a fürtre, csak a csak olvasó szerepkörre vonatkozó engedéllyel. Válassza a **fürt csatolása** a helyi menüből lehetőséget a fürt összekapcsolásához. Adja meg a Ambari felhasználónevét és jelszavát.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png" alt-text="HDInsight Spark-fürtök az Azure Explorer-ben – hivatkozás" border="true":::

4. Ha a fürt sikeresen csatolva van, a rendszer frissíti a HDInsight.
   A fürt szakasza csatolva lesz.
  
    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png" alt-text="HDInsight Spark-fürtök az Azure Explorerben kapcsolódva" border="true":::

### <a name="link-cluster-by-expanding-jobs-node"></a>Fürt csatolása a feladatok csomópont kibontásával

1. Kattintson a **feladatok** csomópontra, majd a **fürthöz való hozzáférés megtagadva** ablakra.

2. Kattintson **a fürt összekapcsolása** elemre a fürt csatolásához.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png" alt-text="HDInsight Spark-fürtök az Azure Explorer9" border="true":::

### <a name="link-cluster-from-spark-submission-window"></a>Fürt csatolása a Spark beküldési ablakból

1. Hozzon létre egy HDInsight-projektet.

2. Kattintson a jobb gombbal a csomagra. Ezután válassza **a Spark-alkalmazás küldése a HDInsight** lehetőséget.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png" alt-text="HDInsight Spark-fürtök az Azure Explorer-ben – küldés" border="true":::

3. Válasszon ki egy fürtöt, amely csak a csak olvasó szerepkörre vonatkozó jogosultsággal rendelkezik a **fürt nevéhez**. Figyelmeztető üzenet jelenik meg. A fürt csatolásához kattintson **a fürt csatolása** lehetőségre.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png" alt-text="HDInsight Spark-fürtök az Azure Explorerben" border="true":::

### <a name="view-storage-accounts"></a>Storage-fiókok megtekintése

* A csak olvasási szerepkörrel rendelkező fürtök esetében kattintson a **Storage-fiókok** csomópontra, majd a **tárterület-hozzáférés megtagadva** ablakra.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png" alt-text="HDInsight Spark-fürtök az Azure Explorer Storage-ban" border="true":::

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png" alt-text="HDInsight Spark-fürtök az Azure Explorerben megtagadva" border="true":::

* A csatolt fürtök esetében kattintson a **Storage-fiókok** csomópontra, majd a tárterület- **hozzáférés megtagadva** ablak jelenik meg.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png" alt-text="HDInsight Spark-fürtök az Azure Explorer denied2" border="true":::

## <a name="known-problems"></a>Ismert problémák

**Fürt csatolásakor** azt javasoljuk, hogy adja meg a tárterület hitelesítő adatait.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png" alt-text="fürt csatolása a tárolási hitelesítő adatok fogyatkozásával" border="true":::

Két mód van a feladatok elküldésére. Ha megadja a tárolási hitelesítő adatokat, a rendszer a kötegelt módot használja a feladatok elküldéséhez. Ellenkező esetben az interaktív mód lesz használatban. Ha a fürt foglalt, előfordulhat, hogy az alábbi hibaüzenet jelenik meg.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png " alt-text="Eclipse Get hiba, ha a fürt foglalt" border="true":::

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png " alt-text="Eclipse Get hiba, ha a fürt foglalt fonala" border="true":::

## <a name="see-also"></a>Lásd még

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [A Azure Toolkit for IntelliJ használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Apache Spark-alkalmazások távoli hibakeresése a Azure Toolkit for IntelliJ használatával VPN-en keresztül](./apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Spark-alkalmazások távoli hibakeresése az Azure Toolkit for IntelliJ használatával SSH-n keresztül](./apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [A HDInsight Apache Spark-fürtben Jupyter Notebook számára elérhető kernelek](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)