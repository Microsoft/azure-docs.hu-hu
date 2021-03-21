---
title: 'Azure Toolkit: Apache Spark alkalmazások távoli hibakeresése – Azure HDInsight'
description: Ismerje meg, hogyan használhatja a Azure Toolkit for IntelliJ HDInsight-eszközeit a HDInsight-fürtökön futó Spark-alkalmazások távoli hibakereséséhez VPN-en keresztül.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/28/2017
ms.openlocfilehash: 1daf59c47bf71ad019011036bf42bf6727754345
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940354"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Apache Spark-alkalmazások távoli hibakeresése a HDInsight a VPN-en keresztül a Azure Toolkit for IntelliJ használatával

Javasoljuk, hogy az SSH-n keresztül távolról hibakeresést [Apache Spark](https://spark.apache.org/) alkalmazásokat. Útmutatásért lásd: [Apache Spark-alkalmazások távoli hibakeresése HDInsight-fürtön az SSH-val való Azure Toolkit for IntelliJ használatával](./apache-spark-intellij-tool-debug-remotely-through-ssh.md).

Ez a cikk részletes útmutatást nyújt arról, hogyan használhatók a Azure Toolkit for IntelliJ HDInsight eszközei a Spark-feladatok HDInsight Spark-fürtön való elküldéséhez, majd az asztali számítógép távoli hibakereséséhez. A feladatok végrehajtásához a következő magas szintű lépéseket kell végrehajtania:

1. Hozzon létre egy helyek közötti vagy pont – hely típusú Azure-beli virtuális hálózatot. A jelen dokumentum lépései azt feltételezik, hogy helyek közötti hálózatot használ.
1. Hozzon létre egy Spark-fürtöt a HDInsight-ben, amely a helyek közötti virtuális hálózat részét képezi.
1. Ellenőrizze a fürt fő csomópontja és az asztal közötti kapcsolatot.
1. Hozzon létre egy Scala-alkalmazást a IntelliJ IDEA-ben, majd konfigurálja a távoli hibakereséshez.
1. Az alkalmazás futtatása és hibakeresése.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-előfizetés**. További információkért lásd az [Azure ingyenes próbaverziójának beszerzését](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)ismertető témakört.
* **Egy Apache Spark-fürt a HDInsight-ben**. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* **Oracle Java Development Kit**. Telepítheti az [Oracle webhelyéről](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* **INTELLIJ ötlet**. Ez a cikk a 2017,1-es verziót használja. A [JetBrains webhelyről](https://www.jetbrains.com/idea/download/)telepítheti.
* **Azure Toolkit for IntelliJ HDInsight eszközei**. A IntelliJ HDInsight eszközei a Azure Toolkit for IntelliJ részeként érhetők el. Az Azure Toolkit telepítésével kapcsolatos utasításokért lásd: [install Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Jelentkezzen be az Azure-előfizetésbe a INTELLIJ Idea-ból**. [Apache Spark alkalmazások HDInsight-fürthöz való létrehozásához kövesse az Azure Toolkit for IntelliJ használata](apache-spark-intellij-tool-plugin.md)című részben leírt utasításokat.
* **Kivételt megkerülő megoldás**. Előfordulhat, hogy a Windows rendszerű számítógépeken a Spark Scala alkalmazás a távoli hibakereséshez való futtatásakor kivételt tapasztal. Ezt a kivételt a [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356) ismerteti, és a Windows rendszer hiányzó WinUtils.exe fájlja miatt következik be. A hiba megkerüléséhez le kell töltenie [Winutils.exet](https://github.com/steveloughran/winutils) egy olyan helyre, mint például a **C:\WinUtils\bin**. Vegyen fel egy **HADOOP_HOME** környezeti változót, majd állítsa a változó értékét **C\WinUtils** értékre.

## <a name="step-1-create-an-azure-virtual-network"></a>1. lépés: Azure-beli virtuális hálózat létrehozása

Az alábbi hivatkozások utasításait követve hozzon létre egy Azure-beli virtuális hálózatot, majd ellenőrizze az asztali számítógép és a virtuális hálózat közötti kapcsolatot:

* [VNet létrehozása helyek közötti VPN-kapcsolattal a Azure Portal használatával](../../vpn-gateway/tutorial-site-to-site-portal.md)
* [VNet létrehozása helyek közötti VPN-kapcsolattal a PowerShell használatával](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Pont – hely kapcsolat konfigurálása egy virtuális hálózathoz a PowerShell használatával](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>2. lépés: HDInsight Spark-fürt létrehozása

Azt javasoljuk, hogy hozzon létre egy Apache Spark-fürtöt az Azure HDInsight, amely a létrehozott Azure-beli virtuális hálózat részét képezi. A [Linux-alapú fürtök létrehozása a HDInsight-](../hdinsight-hadoop-provision-linux-clusters.md)ben című témakörben található információk segítségével. A választható konfiguráció részeként válassza ki az előző lépésben létrehozott Azure-beli virtuális hálózatot.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>3. lépés: a fürt fő csomópontja és az asztal közötti kapcsolat ellenőrzése

1. A fő csomópont IP-címének beolvasása. Nyissa meg a Ambari felhasználói felületét a fürthöz. A fürt paneljén válassza az **irányítópult** lehetőséget.

    ![Irányítópult kiválasztása az Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png)

1. A Ambari felhasználói felületén válassza a **gazdagépek** lehetőséget.

    ![Gazdagépek kiválasztása az Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png)

1. Ekkor megjelenik a fő csomópontok, a feldolgozó csomópontok és a Zookeeper-csomópontok listája. A fő csomópontok **HN*** előtaggal rendelkeznek. Válassza ki az első fő csomópontot.

    ![A fő csomópont megkeresése az Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png)

1. A megnyíló lap alján található **Összefoglalás** ablaktáblán másolja a fő csomópont és az **állomásnév** **IP-címét** .

    ![Az IP-cím megkeresése az Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png)

1. Adja hozzá az IP-címet és a fő csomópont állomásnevét a **gazdagépek** fájljához azon a számítógépen, amelyen futtatni kívánja, majd a Spark-feladatot távolról kell elvégeznie. Ez lehetővé teszi, hogy az IP-cím, valamint az állomásnév használatával kommunikáljon a fő csomóponttal.

   a. Nyisson meg egy jegyzettömb-fájlt emelt szintű engedélyekkel. A **fájl** menüben válassza a **Megnyitás** elemet, majd keresse meg a Hosts fájl helyét. A Windows rendszerű számítógépeken a hely **C:\Windows\System32\Drivers\etc\hosts**.

   b. Adja hozzá a következő adatokat a **gazdagépek** fájljához:

    ```
    # For headnode0
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. A HDInsight-fürt által használt Azure-beli virtuális hálózathoz csatlakoztatott számítógépről ellenőrizze, hogy a fő csomópontokat az IP-cím, valamint az állomásnév használatával tudja-e pingelni.

1. Használja az SSH-t a fürt fő csomópontjára való kapcsolódáshoz a [Kapcsolódás HDInsight-fürthöz SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md)című témakör utasításait követve. A fürt fő csomópontján Pingelje az asztali számítógép IP-címét. A számítógéphez rendelt IP-címekkel való kapcsolat tesztelése:

    - A hálózati kapcsolatok egyike
    - Az egyik az Azure Virtual Network számára

1. Ismételje meg a többi fő csomópont lépéseit.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>4. lépés: Apache Spark Scala-alkalmazás létrehozása a Azure Toolkit for IntelliJ HDInsight-eszközeinek használatával és a távoli hibakereséshez való konfigurálásával

1. Nyissa meg a IntelliJ IDEA-t, és hozzon létre egy új projektet. A **New Project** (Új projekt) párbeszédablakban hajtsa végre az alábbi lépéseket:

    ![A IntelliJ IDEA új projekt sablonjának kiválasztása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Válassza   >  **a HDInsight Spark on HDInsight (Scala)** lehetőséget.

    b. Kattintson a **Tovább** gombra.
1. A következő **új projekt** párbeszédpanelen tegye a következőket, majd kattintson a **Befejezés gombra**:

    - Adja meg a projekt nevét és helyét.

    - A **Project SDK** (Projekt SDK) legördülő listából Spark 2.x-fürt esetében válassza a **Java 1.8**, Spark 1.x-fürt esetében pedig a **Java 1.7** lehetőséget.

    - A **Spark-verzió** legördülő listában a Scala projekt létrehozása varázsló integrálja a Spark SDK és a Scala SDK megfelelő verzióját. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa a következő verziót használja: **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![A Project SDK és a Spark verziójának kiválasztása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. A Spark-projekt automatikusan létrehoz egy összetevőt. Az összetevő megtekintéséhez tegye a következőket:

    a. A **File** (Fájl) menüben válassza a **Project Structure** (Projektstruktúra) lehetőséget.

    b. A **projekt szerkezete** **párbeszédpanelen válassza az összetevők elemet a létrehozott** alapértelmezett összetevő megtekintéséhez. Saját összetevőt is létrehozhat a plusz jelre ( **+** ) kattintva.

   ![IntelliJ IDEA-összetevők létrehozása jar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png)

1. Adja hozzá a kódtárakat a projekthez. Tár hozzáadásához tegye a következőket:

    a. Kattintson a jobb gombbal a projekt nevére a projekt fájában, majd válassza a **modul beállításainak megnyitása** lehetőséget.

    b. A **projekt szerkezete** párbeszédpanelen válassza a **könyvtárak** lehetőséget, válassza ki a ( **+** ) szimbólumot, majd válassza ki **a mavenből** lehetőséget.

    ![IntelliJ IDEA letöltési könyvtár](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png)

    c. A **könyvtár letöltése a Maven adattárból** párbeszédpanelen keresse meg és adja hozzá a következő könyvtárakat:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. Másolja `yarn-site.xml` a `core-site.xml` csomópontot a fürt fő csomópontjára, és adja hozzá őket a projekthez. A fájlok másolásához használja az alábbi parancsokat. A [Cygwin](https://cygwin.com/install.html) használatával a következő parancsok futtatásával `scp` másolhatja a fájlokat a fürt fő csomópontjairól:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Mivel már hozzáadta a fürt fő csomópontjának IP-címét és a gazdagépekhez tartozó gazdagépeket az asztalon, a következő módon használhatjuk a `scp` parancsokat:

    ```bash
    scp sshuser@nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Ha ezeket a fájlokat hozzá szeretné adni a projekthez, másolja őket a **/src** mappába a projekt fájában, például: `<your project directory>\src` .

1. Frissítse a `core-site.xml` fájlt a következő módosítások elvégzéséhez:

   a. Cserélje le a titkosított kulcsot. A `core-site.xml` fájl tartalmazza a titkosított kulcsot a fürthöz társított Storage-fiókhoz. A `core-site.xml` projekthez hozzáadott fájlban cserélje le a titkosított kulcsot az alapértelmezett Storage-fiókhoz társított tényleges tárolási kulcsra. További információ: a [Storage-fiók elérési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md).

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Távolítsa el a következő bejegyzéseket a alkalmazásból `core-site.xml` :

    ```xml
    <property>
            <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
            <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
    </property>

    <property>
            <name>fs.azure.shellkeyprovider.script</name>
            <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
    </property>

    <property>
            <name>net.topology.script.file.name</name>
            <value>/etc/hadoop/conf/topology_script.py</value>
    </property>
    ```

   c. Mentse a fájlt.

1. Adja hozzá az alkalmazás fő osztályát. A **Project Explorerben** kattintson a jobb gombbal az **src** elemre, mutasson az **új** elemre, majd válassza a **Scala osztály** elemet.

    ![IntelliJ IDEA – válassza ki a Main osztályt](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

1. Az **új Scala-osztály létrehozása** párbeszédpanelen adjon meg egy nevet, válassza ki **az objektum** elemet a **fajta** mezőben, majd kattintson **az OK gombra**.

    ![IntelliJ IDEA új Scala-osztály létrehozása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

1. A `MyClusterAppMain.scala` fájlban illessze be a következő kódot. Ez a kód létrehozza a Spark-környezetet, és egy `executeJob` metódust nyit meg az `SparkSample` objektumból.

    ```scala
    import org.apache.spark.{SparkConf, SparkContext}

    object SparkSampleMain {
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("SparkSample")
                                    .set("spark.hadoop.validateOutputSpecs", "false")
        val sc = new SparkContext(conf)

        SparkSample.executeJob(sc,
                            "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                            "wasb:///HVACOut")
        }
    }
    ```

1. Ismételje meg a 8. és a 9. lépést egy új, nevű Scala-objektum hozzáadásához `*SparkSample` . Adja hozzá a következő kódot ehhez az osztályhoz. Ez a kód beolvassa az adatokat a HVAC.csvból (az összes HDInsight Spark-fürtben elérhető). Lekéri azokat a sorokat, amelyek csak egy számjegytel rendelkeznek a CSV-fájl hetedik oszlopában, majd a kimenetet a fürt alapértelmezett **/HVACOut** alá írja.

    ```scala
    import org.apache.spark.SparkContext

    object SparkSample {
        def executeJob (sc: SparkContext, input: String, output: String): Unit = {
        val rdd = sc.textFile(input)

        //find the rows which have only one digit in the 7th column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
        println(s)

        rdd1.saveAsTextFile(output)
        //rdd1.collect().foreach(println)
         }
    }
    ```

1. A 8. és a 9. lépést megismételve adjon hozzá egy új, nevű osztályt `RemoteClusterDebugging` . Ez az osztály megvalósítja az alkalmazások hibakereséséhez használt Spark test keretrendszert. Adja hozzá a következő kódot a `RemoteClusterDebugging` osztályhoz:

    ```scala
        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }
    ```

     Néhány fontos Tudnivaló:

      * A esetében `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")` ellenőrizze, hogy a Spark Assembly jar elérhető-e a fürt tárterületén a megadott elérési úton.
      * A esetében `setJars` válassza ki azt a helyet, ahol az összetevő jar létrejött. Általában ez `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar` .

1. A `*RemoteClusterDebugging` osztályban kattintson a jobb gombbal a `test` kulcsszóra, majd válassza a **RemoteClusterDebugging-konfiguráció létrehozása** lehetőséget.

    ![IntelliJ IDEA távoli konfiguráció létrehozása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. A **RemoteClusterDebugging-konfiguráció létrehozása** párbeszédpanelen adja meg a konfiguráció nevét, **majd válassza a teszt** **neve** lehetőséget. Hagyja meg az összes többi értéket alapértelmezett beállításként. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.

    ![RemoteClusterDebugging-konfiguráció létrehozása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)

1. Ekkor a menüsávban megjelenik a **távoli futtatási** konfiguráció legördülő listája.

    ![A távoli Futtatás legördülő lista IntelliJ](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>5. lépés: az alkalmazás futtatása hibakeresési módban

1. A IntelliJ IDEA projektben nyissa meg `SparkSample.scala` és hozzon létre egy töréspontot a mellett `val rdd1` . A **töréspont létrehozása** az előugró menüben válassza a **sor a függvény executeJob** lehetőséget.

    ![IntelliJ-ötlet – töréspont hozzáadása](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png)

1. Az alkalmazás futtatásához kattintson a **távoli futtatási** konfiguráció legördülő lista melletti **hibakeresés Futtatás** gombra.

    ![IntelliJ-ötlet kiválasztása a hibakeresés futtatása gomb](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png)

1. Amikor a program végrehajtása eléri a töréspontot, megjelenik egy **hibakereső** lap az alsó ablaktáblán.

    ![IntelliJ IDEA – a hibakereső lap megtekintése](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png)

1. Az óra hozzáadásához válassza a ( **+** ) ikont.

    ![IntelliJ hibakeresése – Add-Watch-változó](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    Ebben a példában az alkalmazás a változó létrehozása előtt megszakadt `rdd1` . A Watch használatával láthatjuk a változó első öt sorát `rdd` . Nyomja le az **Enter** billentyűt.

    ![A program hibakeresési módban futtatja a IntelliJ](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Az előző ábrán látható, hogy futásidőben lekérdezheti a terabájt adatmennyiséget, és hibakeresést végezhet az alkalmazás előrehaladásával. Például az előző képen látható kimenetben láthatja, hogy a kimenet első sora egy fejléc. Ennek a kimenetnek a alapján módosíthatja az alkalmazás kódját, hogy szükség esetén kihagyja a fejlécet.

1. Most kiválaszthatja a **program folytatása** ikont az alkalmazás futtatásának folytatásához.

    ![IntelliJ ötlet válassza a program folytatása lehetőséget](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png)

1. Ha az alkalmazás sikeresen befejeződik, a következőhöz hasonló kimenetnek kell megjelennie:

    ![IntelliJ IDEA hibakereső konzol kimenete](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png)

## <a name="next-steps"></a><a name="seealso"></a>Következő lépések

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
* [Apache Spark-alkalmazások távoli hibakeresése az Azure Toolkit for IntelliJ használatával SSH-n keresztül](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Apache Spark alkalmazások létrehozásához használja a Azure Toolkit for Eclipse HDInsight-eszközeit](./apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-jegyzetfüzetek használata Apache Spark-fürttel a HDInsight-ben](apache-spark-zeppelin-notebook.md)
* [A HDInsight Apache Spark-fürtben Jupyter Notebook számára elérhető kernelek](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsight-ben](apache-spark-job-debugging.md)