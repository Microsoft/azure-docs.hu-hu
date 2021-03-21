---
title: 'Azure Toolkit for IntelliJ: Spark-alkalmazás – HDInsight'
description: A Azure Toolkit for IntelliJ használatával a Scala-ben írt Spark-alkalmazásokat fejlesztheti, és beküldheti azokat egy HDInsight Spark-fürtbe.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: ced066623d23c6894745374ace812454c52f3109
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103420017"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Apache Spark alkalmazások létrehozása a HDInsight-fürthöz Azure Toolkit for IntelliJ használatával

Ez a cikk bemutatja, hogyan fejleszthet Apache Spark-alkalmazásokat az Azure HDInsight a IntelliJ IDE-hez készült **Azure Toolkit** beépülő modullal. Az [Azure HDInsight](../hdinsight-overview.md) egy felügyelt, nyílt forráskódú elemzési szolgáltatás a felhőben. A szolgáltatás lehetővé teszi olyan nyílt forráskódú keretrendszerek használatát, mint például a Hadoop, a Apache Spark, a Apache Hive és a Apache Kafka.

Az **Azure Toolkit** beépülő modult többféleképpen is használhatja:

* Scala Spark-alkalmazás fejlesztése és beküldése egy HDInsight Spark-fürtbe.
* Hozzáférés a Azure HDInsight Spark-fürt erőforrásaihoz.
* A Scala Spark alkalmazást helyileg fejlesztheti és futtathatja.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * A Azure Toolkit for IntelliJ beépülő modul használata
> * Apache Spark alkalmazások fejlesztése
> * Alkalmazás beküldése az Azure HDInsight-fürtbe

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Ez a cikk a Java-verzió 8.0.202 használja.

* IntelliJ ötlet. Ez a cikk a [INTELLIJ Idea Community ver-t használja.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Lásd: [a Azure Toolkit for IntelliJ telepítése](/azure/developer/java/toolkit-for-intellij/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Scala beépülő modul az IntelliJ IDEA-hoz

A Scala beépülő modul telepítésének lépései:

1. Nyissa meg az IntelliJ IDEA-t.

2. Az üdvözlőképernyőn kattintson a plugins **konfigurálása** elemre  >   a **plugins** ablak megnyitásához.

    ![IntelliJ IDEA – a Scala beépülő modul engedélyezése](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Válassza a **telepítés** lehetőséget az új ablakban Kiemelt Scala beépülő modulhoz.  

    ![IntelliJ IDEA – a Scala beépülő modul telepítése](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. A beépülő modul sikeres telepítését követően újra kell indítania az IDE-t.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Spark Scala-alkalmazás létrehozása HDInsight Spark-fürthöz

1. Indítsa el a IntelliJ ÖTLETET, és válassza az **új projekt létrehozása** elemet az **új projekt** ablak megnyitásához.

2. Válassza ki az **Azure Spark/HDInsight** elemet a bal oldali panelen.

3. Válassza ki a **Spark Project (Scala)** elemet a főablakból.

4. A **Build eszköz** legördülő listából válassza a következő lehetőségek egyikét:
   * **Maven** a Scala projekt-létrehozás varázsló támogatásához.
   * A **SBT** kezelése és a Scala-projekt kiépítése.

     ![IntelliJ IDEA új projekt párbeszédpanel](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Kattintson a **Tovább** gombra.

6. Az **új projekt** ablakban adja meg a következő információkat:  

    |  Tulajdonság   | Leírás   |  
    | ----- | ----- |  
    |Projektnév| Adjon meg egy nevet.  Ez a cikk a következőt használja: `myApp` .|  
    |Projekt &nbsp; helye| Adja meg a projekt mentésének helyét.|
    |Projekt SDK| Ez a mező lehet üres az első ÖTLETE alapján.  Válassza az **új...** lehetőséget, és navigáljon a JDK-hez.|
    |Spark-verzió|A létrehozás varázsló a Spark SDK és a Scala SDK megfelelő verzióját integrálja. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa a **Spark 2.3.0 (Scala 2.11.8)** használja.|

    ![Az Apache Spark SDK kiválasztása](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Válassza a **Befejezés** gombot.  A projekt elérhetővé válása néhány percet igénybe vehet.

8. A Spark-projekt automatikusan létrehoz egy összetevőt. Az összetevő megtekintéséhez hajtsa végre a következő lépéseket:

   a. A menüsávban navigáljon a **fájl**  >  **projekt szerkezete...** elemre.

   b. A **projekt szerkezete** **ablakban válassza az** összetevők elemet.  

   c. A lelet megtekintése után válassza a **Mégse**  lehetőséget.

      ![Az összetevő adatai a párbeszédpanelen](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Adja hozzá az alkalmazás forráskódját a következő lépések végrehajtásával:

    a. A projekt területen navigáljon a **SajátPr**  >  **src**  >  **Main**  >  **Scala** webhelyre.  

    b. Kattintson a jobb gombbal a **Scala** elemre, majd navigáljon az **új**  >  **Scala osztályhoz**.

   ![A Scala-osztály projektből való létrehozásának parancsai](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Az **új Scala-osztály létrehozása** párbeszédpanelen adjon meg egy nevet, válassza ki **az objektum** elemet a **típus** legördülő listából, majd kattintson **az OK gombra**.

     ![Új Scala-osztály létrehozása párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. A **SajátPr. Scala** fájl ekkor megnyílik a fő nézetben. Cserélje le az alapértelmezett kódot az alább található kódra:  

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object myApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("myApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV file
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }

    }
    ```

    A kód beolvassa az adatokat a HVAC.csvból (az összes HDInsight Spark-fürtön elérhető), lekérdezi azokat a sorokat, amelyek csak egy számjegytel rendelkeznek a CSV-fájl hetedik oszlopában, és a kimenetet a `/HVACOut` fürthöz tartozó alapértelmezett tárolóba írja.

## <a name="connect-to-your-hdinsight-cluster"></a>Kapcsolódás a HDInsight-fürthöz

A felhasználó [bejelentkezhet az Azure-előfizetésbe](#sign-in-to-your-azure-subscription), vagy [összekapcsolhat egy HDInsight-fürtöt](#link-a-cluster). A HDInsight-fürthöz való csatlakozáshoz használja a Ambari username/Password vagy a tartományhoz csatlakoztatott hitelesítő adatokat.

### <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésbe

1. A menüsávban navigáljon a **Megtekintés**  >  **eszközre a Windows**  >  **Azure Explorerben**.

   ![A IntelliJ ÖTLETE az Azure Explorert mutatja be](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. Az Azure Explorerben kattintson a jobb gombbal az **Azure** -csomópontra, majd válassza a **Bejelentkezés** lehetőséget.

   ![A IntelliJ IDEA Explorer jobb oldali gombjával kattintson az Azure elemre](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. Az **Azure bejelentkezési** párbeszédpanelen válassza az **eszköz bejelentkezés** lehetőséget, majd válassza a **Bejelentkezés** lehetőséget.

    !["IntelliJ IDEA Azure bejelentkezési eszköz bejelentkezhessen](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. Az **Azure-eszköz bejelentkezési** párbeszédpanelén kattintson a **Másolás&Megnyitás** elemre.

   ![' IntelliJ IDEA Azure-eszköz bejelentkezhessen](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. A böngésző felületén illessze be a kódot, majd kattintson a **tovább** gombra.

   !["A Microsoft írja be a kód párbeszédpanelt a HDI"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Adja meg az Azure-beli hitelesítő adatait, majd zárjuk be a böngészőt.

   !["Microsoft ENTER e-mail párbeszédpanel HDI"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. Miután bejelentkezett, az **előfizetések kiválasztása** párbeszédpanel felsorolja az összes olyan Azure-előfizetést, amely társítva van a hitelesítő adatokhoz. Válassza ki az előfizetését, majd kattintson a **kiválasztás** gombra.

    ![Az Előfizetések kiválasztása párbeszédpanel](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. Az **Azure Explorerben** bontsa ki a **HDInsight** elemet, és tekintse meg az előfizetésekben található HDInsight Spark-fürtöket.

    ![IntelliJ IDEA Azure Explorer – fő nézet](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Ha meg szeretné tekinteni a fürthöz társított erőforrásokat (például Storage-fiókokat), akkor tovább bővítheti a fürt neve csomópontot.

    ![Azure Explorer Storage-fiókok](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Fürt csatolása

Egy HDInsight-fürtöt az Apache Ambari Managed username használatával kapcsolhat össze. Hasonlóképpen, a tartományhoz csatlakoztatott HDInsight-fürtökhöz a tartomány és a Felhasználónév használatával is kapcsolódhat, például: `user1@contoso.com` . Emellett a Livy Service-fürtöt is összekapcsolhatja.

1. A menüsávban navigáljon a **Megtekintés**  >  **eszközre a Windows**  >  **Azure Explorerben**.

1. Az Azure Explorerben kattintson a jobb gombbal a **HDInsight** csomópontra, majd válassza a **fürt csatolása** lehetőséget.

   ![Azure Explorer hivatkozás fürt helyi menüje](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. A **fürt csatolása** ablakban elérhető beállítások attól függően változnak, hogy melyik értéket választotta ki az **Erőforrás típusa** legördülő listából.  Adja meg az értékeket, majd kattintson **az OK gombra**.

    * **HDInsight-fürt**  
  
        |Tulajdonság |Érték |
        |----|----|
        |Erőforrás típusának csatolása|Válassza ki a **HDInsight-fürtöt** a legördülő listából.|
        |Fürt neve/URL-címe| Adja meg a fürt nevét.|
        |Hitelesítés típusa| Szabadság **alapszintű hitelesítésként**|
        |Felhasználónév| Adja meg a fürt felhasználói nevét, az alapértelmezett érték a rendszergazda.|
        |Jelszó| Adja meg a felhasználónévhez tartozó jelszót.|

        ![IntelliJ-ötlet csatolása egy fürthöz párbeszédpanel](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy szolgáltatás**  
  
        |Tulajdonság |Érték |
        |----|----|
        |Erőforrás típusának csatolása|Válassza ki a **Livy szolgáltatást** a legördülő listából.|
        |Livy végpont| Livy-végpont megadása|
        |Fürt neve| Adja meg a fürt nevét.|
        |Fonal végpontja|Választható.|
        |Hitelesítés típusa| Szabadság **alapszintű hitelesítésként**|
        |Felhasználónév| Adja meg a fürt felhasználói nevét, az alapértelmezett érték a rendszergazda.|
        |Jelszó| Adja meg a felhasználónévhez tartozó jelszót.|

        ![IntelliJ IDEA link Livy-fürt párbeszédpanel](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. A csatolt fürtöt a **HDInsight** csomóponton tekintheti meg.

   ![Az Azure Explorer csatolt cluster1](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. A fürtöket az **Azure Explorerben** is leválaszthatja.

   ![Azure Explorer – nem csatolt fürt](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Spark Scala-alkalmazás futtatása HDInsight Spark-fürtön

A Scala-alkalmazás létrehozása után elküldheti azt a fürtnek.

1. A projekt területen navigáljon a **SajátPr**  >  **src**  >  **Main**  >  **Scala**-  >  **SajátPr**.  Kattintson a jobb gombbal a **SajátPr** elemre, majd válassza a **Spark-alkalmazás beküldése** lehetőséget (ez valószínűleg a lista alján található).

      ![A Spark-alkalmazás beküldése a HDInsight parancsba](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. A **Spark-alkalmazás elküldése** párbeszédpanelen válassza az **1 lehetőséget. Spark on HDInsight**.

3. A **konfiguráció szerkesztése** ablakban adja meg a következő értékeket, majd kattintson **az OK gombra**:

    |Tulajdonság |Érték |
    |----|----|
    |Spark-fürtök (csak Linux)|Válassza ki azt a HDInsight Spark-fürtöt, amelyen futtatni szeretné az alkalmazást.|
    |Válassza ki a elküldeni kívánt összetevőt|Hagyja meg az alapértelmezett beállítást.|
    |Fő osztály neve|Az alapértelmezett érték a kijelölt fájl fő osztálya. Az osztályt a három pont (**..**.) lehetőség kiválasztásával módosíthatja.  és válasszon másik osztályt.|
    |Feladatok konfigurációi|Módosíthatja az alapértelmezett kulcsokat és értékeket. További információ: [Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html).|
    |Parancssori argumentumok|Szükség esetén megadhat argumentumokat szóközzel elválasztva a Main osztály számára.|
    |Hivatkozott tégelyek és hivatkozott fájlok|Megadhatja a hivatkozott tégelyek és fájlok elérési útját, ha van ilyen. Tallózással is tallózhat az Azure-beli virtuális fájlrendszerben, amely jelenleg csak a ADLS Gen 2 fürtöt támogatja. További információ: [Apache Spark konfiguráció](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Lásd még: [erőforrások feltöltése a fürtbe](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md).|
    |Feladatok feltöltési tárolója|Kibontás a további beállítások megjelenítéséhez.|
    |Tárhelytípusa|Válassza az **Azure Blob használata a feltöltéshez** lehetőséget a legördülő listából.|
    |Tárfiók|Adja meg a Storage-fiókját.|
    |Storage-kulcs|Adja meg a tárolási kulcsot.|
    |Storage-tároló|A Storage- **fiók** és a **Storage-kulcs** megadása után válassza ki a tárolót a legördülő listából.|

    ![A Spark beküldése párbeszédpanel](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Válassza a **SparkJobRun** lehetőséget a projektnek a kiválasztott fürthöz való elküldéséhez. A **fürt távoli Spark-feladata** lapon a feladatok végrehajtási folyamata látható alul. Az alkalmazást a vörös gombra kattintva állíthatja le.

    ![Apache Spark beküldési ablak](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Apache Spark alkalmazások hibakeresése helyileg vagy távolról egy HDInsight-fürtön

Azt is javasoljuk, hogy a Spark-alkalmazást a fürtön küldje el. Ehhez állítsa be a paramétereket a **Run/debug konfigurációk** ide. Lásd: [Apache Spark alkalmazások hibakeresése helyileg vagy távolról egy HDInsight-fürtön az SSH-n keresztüli Azure Toolkit for IntelliJ használatával](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>HDInsight Spark-fürtök elérése és kezelése Azure Toolkit for IntelliJ használatával

A Azure Toolkit for IntelliJ használatával különböző műveleteket végezhet.  A műveletek többsége az **Azure Explorerben** indul el.  A menüsávban navigáljon a **Megtekintés**  >  **eszközre a Windows**  >  **Azure Explorerben**.

### <a name="access-the-job-view"></a>A feladatok nézet elérése

1. Az Azure Explorerben navigáljon a **HDInsight**-  >  \<Your Cluster>  >  **feladatok** elemre.

    ![IntelliJ az Azure Explorer-feladatok nézet csomópontja](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. A jobb oldali ablaktáblán a **Spark-feladatok nézet** lapon a fürtön futó összes alkalmazás látható. Válassza ki annak az alkalmazásnak a nevét, amelyhez további részleteket szeretne látni.

    ![Spark-feladatok nézet alkalmazásának részletei](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Az alapszintű futó feladat adatainak megjelenítéséhez vigye a kurzort a feladat gráf fölé. Ha meg szeretné tekinteni a szakaszok gráfját és az összes feladathoz tartozó információt, válasszon ki egy csomópontot a feladathoz tartozó diagramon.

    ![Spark Job View – feladatütemezés részletei](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. A gyakran használt naplók, például az *illesztőprogram-stderr*, az *illesztőprogram StdOut* és a *könyvtár adatainak* megtekintéséhez válassza a **napló** lapot.

    ![Spark-feladatok nézet naplójának részletei](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. Megtekintheti a Spark-előzmények felhasználói felületét és a fonal felhasználói felületét (az alkalmazás szintjén). Válasszon egy hivatkozást az ablak tetején.

### <a name="access-the-spark-history-server"></a>A Spark-előzmények kiszolgálójának elérése

1. Az Azure Explorerben bontsa ki a **HDInsight** csomópontot, kattintson a jobb gombbal a Spark-fürt nevére, majd válassza a **Spark-előzmények megnyitása felhasználói felület** lehetőséget.  
2. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait, amelyet a fürt beállításakor adott meg.

3. A Spark History Server irányítópultján használhatja az alkalmazás nevét, hogy megkeresse az éppen futó alkalmazást. Az előző kódban a paranccsal állíthatja be az alkalmazás nevét `val conf = new SparkConf().setAppName("myApp")` . A Spark-alkalmazás neve **SajátPr**.

### <a name="start-the-ambari-portal"></a>A Ambari-portál elindítása

1. Az Azure Explorerben bontsa ki a **HDInsight** csomópontot, kattintson a jobb gombbal a Spark-fürt nevére, majd válassza a **fürt felügyeleti portál megnyitása (Ambari)** elemet.  

2. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait. Ezeket a hitelesítő adatokat a fürt telepítési folyamata során adta meg.

### <a name="manage-azure-subscriptions"></a>Azure-előfizetések kezelése

Alapértelmezés szerint a Azure Toolkit for IntelliJ felsorolja a Spark-fürtöket az összes Azure-előfizetésből. Szükség esetén megadhatja az elérni kívánt előfizetéseket.  

1. Az Azure Explorerben kattintson a jobb gombbal az **Azure** -legfelső szintű csomópontra, majd válassza az **előfizetések kiválasztása** lehetőséget.  

2. Az **előfizetések kiválasztása** ablakban törölje a jelölőnégyzet jelölését azon előfizetések mellett, amelyeket nem szeretne elérni, majd válassza a **Bezárás** lehetőséget.

## <a name="spark-console"></a>Spark-konzol

Futtathatja a Spark helyi konzolt (Scala), vagy futtathatja a Spark Livy interaktív munkamenet-konzolt (Scala).

### <a name="spark-local-consolescala"></a>Spark helyi konzol (Scala)

Győződjön meg arról, hogy teljesítette a WINUTILS.EXE előfeltételt.

1. A menüsávban navigáljon **a**  >  **konfigurációk szerkesztése..**. lehetőségre.

2. A **Futtatás/hibakeresés konfigurációk** ablak bal oldali ablaktábláján navigáljon **Apache Spark a HDInsight**  >  **[Spark on HDInsight] SajátPr**.

3. A fő ablakban válassza a **`Locally Run`** fület.

4. Adja meg a következő értékeket, majd kattintson **az OK gombra**:

    |Tulajdonság |Érték |
    |----|----|
    |Job Main osztály|Az alapértelmezett érték a kijelölt fájl fő osztálya. Az osztályt a három pont (**..**.) lehetőség kiválasztásával módosíthatja.  és válasszon másik osztályt.|
    |Környezeti változók|Győződjön meg arról, hogy a HADOOP_HOME értéke helyes.|
    |WINUTILS.exe helye|Győződjön meg arról, hogy az elérési út helyes.|

    ![Helyi konzol beállítása – konfiguráció](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. A projekt területen navigáljon a **SajátPr**  >  **src**  >  **Main**  >  **Scala**-  >  **SajátPr**.  

6. A menüsávban navigáljon az **eszközök**  >  **Spark-konzol**  >  **Futtatás Spark helyi konzol (Scala)** elemre.

7. Ezután két párbeszédpanel jelenik meg, amely megkérdezi, hogy szeretné-e automatikusan kijavítani a függőségeket. Ha igen, válassza az **Automatikus javítás** lehetőséget.

    ![IntelliJ ötlet Spark automatikus javítás dialog1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![IntelliJ ötlet Spark automatikus javítás Dialog2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. A konzolnak az alábbi ábrához hasonlóan kell kinéznie. A konzol ablakában írja be a következőt: `sc.appName` , majd nyomja le a CTRL + ENTER billentyűkombinációt.  Ekkor megjelenik az eredmény. A helyi konzolt a vörös gombra kattintva állíthatja le.

    ![A IntelliJ IDEA helyi konzoljának eredménye](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy interaktív munkamenet-konzol (Scala)

1. A menüsávban navigáljon **a**  >  **konfigurációk szerkesztése..**. lehetőségre.

2. A **Futtatás/hibakeresés konfigurációk** ablak bal oldali ablaktábláján navigáljon **Apache Spark a HDInsight**  >  **[Spark on HDInsight] SajátPr**.

3. A fő ablakban válassza a **`Remotely Run in Cluster`** fület.

4. Adja meg a következő értékeket, majd kattintson **az OK gombra**:

    |Tulajdonság |Érték |
    |----|----|
    |Spark-fürtök (csak Linux)|Válassza ki azt a HDInsight Spark-fürtöt, amelyen futtatni szeretné az alkalmazást.|
    |Fő osztály neve|Az alapértelmezett érték a kijelölt fájl fő osztálya. Az osztályt a három pont (**..**.) lehetőség kiválasztásával módosíthatja.  és válasszon másik osztályt.|

    ![Interaktív konzol beállított konfigurációja](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. A projekt területen navigáljon a **SajátPr**  >  **src**  >  **Main**  >  **Scala**-  >  **SajátPr**.  

6. A menüsávban navigáljon az **eszközök**  >  **Spark-konzolon** a  >  **Spark Livy interaktív munkamenet-konzolja (Scala)** elemre.

7. A konzolnak az alábbi ábrához hasonlóan kell kinéznie. A konzol ablakában írja be a következőt: `sc.appName` , majd nyomja le a CTRL + ENTER billentyűkombinációt.  Ekkor megjelenik az eredmény. A helyi konzolt a vörös gombra kattintva állíthatja le.

    ![A IntelliJ IDEA interaktív konzoljának eredménye](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Kijelölés küldése a Spark-konzolra

A szkript eredményét úgy teheti meg, hogy elküld egy kódot a helyi konzolon vagy a Livy interaktív munkamenet-konzolján (Scala). Kiemelheti a Scala-fájlban lévő egyes kódokat, majd kattintson a jobb gombbal a **kijelölés küldése a Spark-konzolra** lehetőségre. A rendszer elküldi a kiválasztott kódot a konzolra. Az eredmény a konzolon a kód után fog megjelenni. Ha már van ilyen, a konzolon ellenőrizze a hibákat.  

   ![Kijelölés küldése a Spark-konzolra](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integráció a HDInsight Identity Broker (HIB) szolgáltatással

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Kapcsolódás a HDInsight ESP-fürthöz az ID Broker (HIB) használatával

A normál lépéseket követve bejelentkezhet az Azure-előfizetésbe, hogy csatlakozhasson a HDInsight ESP-fürthöz az ID Broker (HIB) használatával. Bejelentkezés után a rendszer a fürt listáját fogja látni az Azure Explorerben. További útmutatásért lásd: [Kapcsolódás a HDInsight-fürthöz](#connect-to-your-hdinsight-cluster).

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spark Scala-alkalmazás futtatása HDInsight ESP-fürtön az ID Broker (HIB) szolgáltatással

A normál lépések végrehajtásával elküldheti a feladatot HDInsight ESP-fürtre az ID Broker (HIB) használatával. További útmutatásért lásd: [Spark Scala-alkalmazás futtatása HDInsight Spark-fürtön](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster) .

Feltöltjük a szükséges fájlokat egy nevű mappába a bejelentkezési fiókjával, és a konfigurációs fájlban láthatja a feltöltési útvonalat.

   ![feltöltési útvonal a konfigurációban](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spark-konzol egy HDInsight ESP-fürtön az ID Broker (HIB) segítségével

A Spark helyi konzolt (Scala) futtathatja, vagy futtathatja a Spark Livy interaktív munkamenet-konzolt (Scala) egy HDInsight ESP-fürtön az ID Broker (HIB) használatával. További útmutatásért tekintse meg a [Spark-konzolt](#spark-console) .

   > [!NOTE]  
   > A HDInsight ESP-fürthöz, és az id Broker (HIB) esetében jelenleg nem támogatott a [fürt](#link-a-cluster) és a [hibakeresés Apache Spark-alkalmazások távoli](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) használata.

## <a name="reader-only-role"></a>Csak olvasó szerepkör

Ha a felhasználók a csak olvasási szerepkörrel rendelkező fürthöz küldenek feladatokat, a Ambari hitelesítő adatai szükségesek.

### <a name="link-cluster-from-context-menu"></a>Fürt csatolása a helyi menüből

1. Jelentkezzen be csak olvasó szerepkörrel rendelkező fiókkal.

2. Az **Azure Explorerben** bontsa ki a **HDInsight** elemet az előfizetésében található HDInsight-fürtök megtekintéséhez. A **"szerepkör: olvasó"** jelölésű fürtökön csak a csak olvasási jogosultsággal rendelkező szerepkör jogosult.

    !["IntelliJ Azure Explorer szerepkör: olvasó"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Kattintson a jobb gombbal a fürtre, csak a csak olvasó szerepkörre vonatkozó engedéllyel. Válassza a **fürt csatolása** a helyi menüből lehetőséget a fürt összekapcsolásához. Adja meg a Ambari felhasználónevét és jelszavát.

    ![A fürt IntelliJ az Azure Explorerben](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Ha a fürt sikeresen csatolva van, a rendszer frissíti a HDInsight.
   A fürt szakasza csatolva lesz.
  
    ![IntelliJ az Azure Explorer csatolt párbeszédpanelje](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Fürt csatolása a feladatok csomópont kibontásával

1. Kattintson a **feladatok** csomópontra, majd a **fürthöz való hozzáférés megtagadva** ablakra.

2. Kattintson **a fürt összekapcsolása** elemre a fürt csatolásához.

    ![fürthöz való hozzáférés megtagadva párbeszédpanel](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Fürt csatolása a futtatási/hibakeresési konfigurációk ablakból

1. Hozzon létre egy HDInsight-konfigurációt. Ezután válassza **a távoli Futtatás a fürtben** lehetőséget.

2. Válasszon ki egy olyan fürtöt, amely csak a csak a Linux rendszerre vonatkozó szerepkör-engedéllyel rendelkezik a **Spark-fürtökhöz**. Figyelmeztető üzenet jelenik meg. A fürt csatolásához kattintson **a fürt csatolása** lehetőségre.

   ![IntelliJ ötlet futtatása/hibakeresési konfiguráció létrehozása](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Storage-fiókok megtekintése

* A csak olvasási szerepkörrel rendelkező fürtök esetében kattintson a **Storage-fiókok** csomópontra, majd a **tárterület-hozzáférés megtagadva** ablakra. A **Azure Storage Explorer** megnyitása lehetőségre kattintva megnyithatja Storage Explorer.

   !["IntelliJ IDEA Storage-hozzáférés megtagadva"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![IntelliJ IDEA tároló hozzáférésének megtagadva gombja](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* A csatolt fürtök esetében kattintson a **Storage-fiókok** csomópontra, majd a tárterület- **hozzáférés megtagadva** ablak jelenik meg. Az **Azure Storage megnyitása** lehetőségre kattintva megnyithatja Storage Explorer.

   !["IntelliJ IDEA Storage-hozzáférés Denied2"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![IntelliJ IDEA Storage-hozzáférés Denied2 gombja](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Meglévő IntelliJ IDEA-alkalmazások konvertálása Azure Toolkit for IntelliJ használatára

A IntelliJ IDEA-ben létrehozott meglévő Spark Scala-alkalmazások a Azure Toolkit for IntelliJsal való kompatibilitás érdekében átalakíthatók. Ezután a beépülő modullal elküldheti az alkalmazásokat egy HDInsight Spark-fürtbe.

1. A IntelliJ IDEA használatával létrehozott meglévő Spark Scala-alkalmazáshoz nyissa meg a társított `.iml` fájlt.

2. A gyökérszintű szinten a **modul** olyan eleme, amely az alábbi szöveghez hasonló:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
    ```

   Szerkessze a hozzáadandó elemet `UniqueKey="HDInsightTool"` úgy, hogy a **modul** eleme a következő szövegre hasonlítson:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
    ```

3. Mentse a módosításokat. Az alkalmazásnak mostantól kompatibilisnek kell lennie Azure Toolkit for IntelliJsal. Teszteléshez kattintson a jobb gombbal a projekt nevére a projektben. Az előugró menüben már elérhető a **Spark-alkalmazás küldése a HDInsight**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a létrehozott fürtöt a következő lépésekkel:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A felső **keresőmezőbe** írja be a **HDInsight** kifejezést.

1. Válassza ki a **HDInsight-fürtök** elemet a **szolgáltatások** területen.

1. A megjelenő HDInsight-fürtök listájában válassza a **...** elemet a jelen cikkhez létrehozott fürt mellett.

1. Válassza a **Törlés** elemet. Válassza az **Igen** lehetőséget.

![Azure Portal törli a HDInsight-fürtöt](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja a Azure Toolkit for IntelliJ beépülő modult a [Scala](https://www.scala-lang.org/)-ban írt Apache Spark-alkalmazások fejlesztéséhez. Ezután Beküldte őket egy HDInsight Spark-fürtbe közvetlenül a IntelliJ integrált fejlesztői környezetből (IDE). A következő cikkből megtudhatja, hogy a Apache Sparkban regisztrált információk Hogyan tölthetők be egy BI Analytics-eszközbe, például Power BIba.

> [!div class="nextstepaction"]
> [Apache Spark-adatelemzés az Power BI használatával](apache-spark-use-bi-tools.md)
