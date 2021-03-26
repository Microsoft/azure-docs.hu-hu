---
title: 'Azure Toolkit for IntelliJ: Spark-alkalmazás – HDInsight'
description: A Azure Toolkit for IntelliJ használatával a Scala-ben írt Spark-alkalmazásokat fejlesztheti, és beküldheti azokat egy HDInsight Spark-fürtbe.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 29fb96dc83ada329910844506838dee461321343
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866335"
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

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png" alt-text="IntelliJ IDEA – a Scala beépülő modul engedélyezése" border="true":::

3. Válassza a **telepítés** lehetőséget az új ablakban Kiemelt Scala beépülő modulhoz.  

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png" alt-text="IntelliJ IDEA – a Scala beépülő modul telepítése" border="true":::

4. A beépülő modul sikeres telepítését követően újra kell indítania az IDE-t.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Spark Scala-alkalmazás létrehozása HDInsight Spark-fürthöz

1. Indítsa el a IntelliJ ÖTLETET, és válassza az **új projekt létrehozása** elemet az **új projekt** ablak megnyitásához.

2. Válassza ki az **Azure Spark/HDInsight** elemet a bal oldali panelen.

3. Válassza ki a **Spark Project (Scala)** elemet a főablakból.

4. A **Build eszköz** legördülő listából válassza a következő lehetőségek egyikét:
   * **Maven** a Scala projekt-létrehozás varázsló támogatásához.
   * A **SBT** kezelése és a Scala-projekt kiépítése.

     :::image type="content" source="./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png" alt-text="IntelliJ IDEA új projekt párbeszédpanel" border="true":::

5. Kattintson a **Tovább** gombra.

6. Az **új projekt** ablakban adja meg a következő információkat:  

    |  Tulajdonság   | Leírás   |  
    | ----- | ----- |  
    |Projektnév| Adjon meg egy nevet.  Ez a cikk a következőt használja: `myApp` .|  
    |Projekt &nbsp; helye| Adja meg a projekt mentésének helyét.|
    |Projekt SDK| Ez a mező lehet üres az első ÖTLETE alapján.  Válassza az **új...** lehetőséget, és navigáljon a JDK-hez.|
    |Spark-verzió|A létrehozás varázsló a Spark SDK és a Scala SDK megfelelő verzióját integrálja. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa a **Spark 2.3.0 (Scala 2.11.8)** használja.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-new-project.png" alt-text="Az Apache Spark SDK kiválasztása" border="true":::

7. Válassza a **Befejezés** gombot.  A projekt elérhetővé válása néhány percet igénybe vehet.

8. A Spark-projekt automatikusan létrehoz egy összetevőt. Az összetevő megtekintéséhez hajtsa végre a következő lépéseket:

   a. A menüsávban navigáljon a **fájl**  >  **projekt szerkezete...** elemre.

   b. A **projekt szerkezete** **ablakban válassza az** összetevők elemet.  

   c. A lelet megtekintése után válassza a **Mégse**  lehetőséget.

      :::image type="content" source="./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png" alt-text="Az összetevő adatai a párbeszédpanelen" border="true":::

9. Adja hozzá az alkalmazás forráskódját a következő lépések végrehajtásával:

    a. A projekt területen navigáljon a **SajátPr**  >  **src**  >  **Main**  >  **Scala** webhelyre.  

    b. Kattintson a jobb gombbal a **Scala** elemre, majd navigáljon az **új**  >  **Scala osztályhoz**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png" alt-text="A Scala-osztály projektből való létrehozásának parancsai" border="true":::

   c. Az **új Scala-osztály létrehozása** párbeszédpanelen adjon meg egy nevet, válassza ki **az objektum** elemet a **típus** legördülő listából, majd kattintson **az OK gombra**.

     :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png" alt-text="Új Scala-osztály létrehozása párbeszédpanel" border="true":::

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

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png" alt-text="A IntelliJ ÖTLETE az Azure Explorert mutatja be" border="true":::

2. Az Azure Explorerben kattintson a jobb gombbal az **Azure** -csomópontra, majd válassza a **Bejelentkezés** lehetőséget.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png" alt-text="A IntelliJ IDEA Explorer jobb oldali gombjával kattintson az Azure elemre" border="true":::

3. Az **Azure bejelentkezési** párbeszédpanelen válassza az **eszköz bejelentkezés** lehetőséget, majd válassza a **Bejelentkezés** lehetőséget.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png" alt-text="&quot;IntelliJ IDEA Azure bejelentkezési eszköz bejelentkezhessen" border="true":::

4. Az **Azure-eszköz bejelentkezési** párbeszédpanelén kattintson a **Másolás&Megnyitás** elemre.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png" alt-text="' IntelliJ IDEA Azure-eszköz bejelentkezhessen" border="true":::

5. A böngésző felületén illessze be a kódot, majd kattintson a **tovább** gombra.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png" alt-text="&quot;A Microsoft írja be a kód párbeszédpanelt a HDI&quot;" border="true":::

6. Adja meg az Azure-beli hitelesítő adatait, majd zárjuk be a böngészőt.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png" alt-text="&quot;Microsoft ENTER e-mail párbeszédpanel HDI&quot;" border="true":::

7. Miután bejelentkezett, az **előfizetések kiválasztása** párbeszédpanel felsorolja az összes olyan Azure-előfizetést, amely társítva van a hitelesítő adatokhoz. Válassza ki az előfizetését, majd kattintson a **kiválasztás** gombra.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png" alt-text="Az Előfizetések kiválasztása párbeszédpanel" border="true":::

8. Az **Azure Explorerben** bontsa ki a **HDInsight** elemet, és tekintse meg az előfizetésekben található HDInsight Spark-fürtöket.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png" alt-text="IntelliJ IDEA Azure Explorer – fő nézet" border="true":::

9. Ha meg szeretné tekinteni a fürthöz társított erőforrásokat (például Storage-fiókokat), akkor tovább bővítheti a fürt neve csomópontot.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png" alt-text="Azure Explorer Storage-fiókok" border="true":::

### <a name="link-a-cluster"></a>Fürt csatolása

Egy HDInsight-fürtöt az Apache Ambari Managed username használatával kapcsolhat össze. Hasonlóképpen, a tartományhoz csatlakoztatott HDInsight-fürtökhöz a tartomány és a Felhasználónév használatával is kapcsolódhat, például: `user1@contoso.com` . Emellett a Livy Service-fürtöt is összekapcsolhatja.

1. A menüsávban navigáljon a **Megtekintés**  >  **eszközre a Windows**  >  **Azure Explorerben**.

1. Az Azure Explorerben kattintson a jobb gombbal a **HDInsight** csomópontra, majd válassza a **fürt csatolása** lehetőséget.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png" alt-text="Azure Explorer hivatkozás fürt helyi menüje" border="true":::

1. A **fürt csatolása** ablakban elérhető beállítások attól függően változnak, hogy melyik értéket választotta ki az **Erőforrás típusa** legördülő listából.  Adja meg az értékeket, majd kattintson **az OK gombra**.

    * **HDInsight-fürt**  
  
        |Tulajdonság |Érték |
        |----|----|
        |Erőforrás típusának csatolása|Válassza ki a **HDInsight-fürtöt** a legördülő listából.|
        |Fürt neve/URL-címe| Adja meg a fürt nevét.|
        |Hitelesítés típusa| Szabadság **alapszintű hitelesítésként**|
        |Felhasználónév| Adja meg a fürt felhasználói nevét, az alapértelmezett érték a rendszergazda.|
        |Jelszó| Adja meg a felhasználónévhez tartozó jelszót.|

        :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png" alt-text="IntelliJ-ötlet csatolása egy fürthöz párbeszédpanel" border="true":::

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

        :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png" alt-text="IntelliJ IDEA link Livy-fürt párbeszédpanel" border="true":::

1. A csatolt fürtöt a **HDInsight** csomóponton tekintheti meg.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png" alt-text="Az Azure Explorer csatolt cluster1" border="true":::

1. A fürtöket az **Azure Explorerben** is leválaszthatja.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png" alt-text="Azure Explorer – nem csatolt fürt" border="true":::

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Spark Scala-alkalmazás futtatása HDInsight Spark-fürtön

A Scala-alkalmazás létrehozása után elküldheti azt a fürtnek.

1. A projekt területen navigáljon a **SajátPr**  >  **src**  >  **Main**  >  **Scala**-  >  **SajátPr**.  Kattintson a jobb gombbal a **SajátPr** elemre, majd válassza a **Spark-alkalmazás beküldése** lehetőséget (ez valószínűleg a lista alján található).

      :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png" alt-text="A Spark-alkalmazás beküldése a HDInsight parancsba" border="true":::

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

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png" alt-text="A Spark beküldése párbeszédpanel" border="true":::

4. Válassza a **SparkJobRun** lehetőséget a projektnek a kiválasztott fürthöz való elküldéséhez. A **fürt távoli Spark-feladata** lapon a feladatok végrehajtási folyamata látható alul. Az alkalmazást a vörös gombra kattintva állíthatja le.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png" alt-text="Apache Spark beküldési ablak" border="true":::

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Apache Spark alkalmazások hibakeresése helyileg vagy távolról egy HDInsight-fürtön

Azt is javasoljuk, hogy a Spark-alkalmazást a fürtön küldje el. Ehhez állítsa be a paramétereket a **Run/debug konfigurációk** ide. Lásd: [Apache Spark alkalmazások hibakeresése helyileg vagy távolról egy HDInsight-fürtön az SSH-n keresztüli Azure Toolkit for IntelliJ használatával](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>HDInsight Spark-fürtök elérése és kezelése Azure Toolkit for IntelliJ használatával

A Azure Toolkit for IntelliJ használatával különböző műveleteket végezhet.  A műveletek többsége az **Azure Explorerben** indul el.  A menüsávban navigáljon a **Megtekintés**  >  **eszközre a Windows**  >  **Azure Explorerben**.

### <a name="access-the-job-view"></a>A feladatok nézet elérése

1. Az Azure Explorerben navigáljon a **HDInsight**-  >  \<Your Cluster>  >  **feladatok** elemre.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png" alt-text="IntelliJ az Azure Explorer-feladatok nézet csomópontja" border="true":::

2. A jobb oldali ablaktáblán a **Spark-feladatok nézet** lapon a fürtön futó összes alkalmazás látható. Válassza ki annak az alkalmazásnak a nevét, amelyhez további részleteket szeretne látni.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png" alt-text="Spark-feladatok nézet alkalmazásának részletei" border="true":::

3. Az alapszintű futó feladat adatainak megjelenítéséhez vigye a kurzort a feladat gráf fölé. Ha meg szeretné tekinteni a szakaszok gráfját és az összes feladathoz tartozó információt, válasszon ki egy csomópontot a feladathoz tartozó diagramon.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png" alt-text="Spark Job View – feladatütemezés részletei" border="true":::

4. A gyakran használt naplók, például az *illesztőprogram-stderr*, az *illesztőprogram StdOut* és a *könyvtár adatainak* megtekintéséhez válassza a **napló** lapot.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png" alt-text="Spark-feladatok nézet naplójának részletei" border="true":::

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

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/console-set-configuration.png" alt-text="Helyi konzol beállítása – konfiguráció" border="true":::

5. A projekt területen navigáljon a **SajátPr**  >  **src**  >  **Main**  >  **Scala**-  >  **SajátPr**.  

6. A menüsávban navigáljon az **eszközök**  >  **Spark-konzol**  >  **Futtatás Spark helyi konzol (Scala)** elemre.

7. Ezután két párbeszédpanel jelenik meg, amely megkérdezi, hogy szeretné-e automatikusan kijavítani a függőségeket. Ha igen, válassza az **Automatikus javítás** lehetőséget.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png" alt-text="IntelliJ ötlet Spark automatikus javítás dialog1" border="true":::

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png" alt-text="IntelliJ ötlet Spark automatikus javítás Dialog2" border="true":::

8. A konzolnak az alábbi ábrához hasonlóan kell kinéznie. A konzol ablakában írja be a következőt: `sc.appName` , majd nyomja le a CTRL + ENTER billentyűkombinációt.  Ekkor megjelenik az eredmény. A helyi konzolt a vörös gombra kattintva állíthatja le.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/local-console-result.png" alt-text="A IntelliJ IDEA helyi konzoljának eredménye" border="true":::

### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy interaktív munkamenet-konzol (Scala)

1. A menüsávban navigáljon **a**  >  **konfigurációk szerkesztése..**. lehetőségre.

2. A **Futtatás/hibakeresés konfigurációk** ablak bal oldali ablaktábláján navigáljon **Apache Spark a HDInsight**  >  **[Spark on HDInsight] SajátPr**.

3. A fő ablakban válassza a **`Remotely Run in Cluster`** fület.

4. Adja meg a következő értékeket, majd kattintson **az OK gombra**:

    |Tulajdonság |Érték |
    |----|----|
    |Spark-fürtök (csak Linux)|Válassza ki azt a HDInsight Spark-fürtöt, amelyen futtatni szeretné az alkalmazást.|
    |Fő osztály neve|Az alapértelmezett érték a kijelölt fájl fő osztálya. Az osztályt a három pont (**..**.) lehetőség kiválasztásával módosíthatja.  és válasszon másik osztályt.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png" alt-text="Interaktív konzol beállított konfigurációja" border="true":::

5. A projekt területen navigáljon a **SajátPr**  >  **src**  >  **Main**  >  **Scala**-  >  **SajátPr**.  

6. A menüsávban navigáljon az **eszközök**  >  **Spark-konzolon** a  >  **Spark Livy interaktív munkamenet-konzolja (Scala)** elemre.

7. A konzolnak az alábbi ábrához hasonlóan kell kinéznie. A konzol ablakában írja be a következőt: `sc.appName` , majd nyomja le a CTRL + ENTER billentyűkombinációt.  Ekkor megjelenik az eredmény. A helyi konzolt a vörös gombra kattintva állíthatja le.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/interactive-console-result.png" alt-text="A IntelliJ IDEA interaktív konzoljának eredménye" border="true":::

### <a name="send-selection-to-spark-console"></a>Kijelölés küldése a Spark-konzolra

A szkript eredményét úgy teheti meg, hogy elküld egy kódot a helyi konzolon vagy a Livy interaktív munkamenet-konzolján (Scala). Kiemelheti a Scala-fájlban lévő egyes kódokat, majd kattintson a jobb gombbal a **kijelölés küldése a Spark-konzolra** lehetőségre. A rendszer elküldi a kiválasztott kódot a konzolra. Az eredmény a konzolon a kód után fog megjelenni. Ha már van ilyen, a konzolon ellenőrizze a hibákat.  

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png" alt-text="Kijelölés küldése a Spark-konzolra" border="true":::

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integráció a HDInsight Identity Broker (HIB) szolgáltatással

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Kapcsolódás a HDInsight ESP-fürthöz az ID Broker (HIB) használatával

A normál lépéseket követve bejelentkezhet az Azure-előfizetésbe, hogy csatlakozhasson a HDInsight ESP-fürthöz az ID Broker (HIB) használatával. Bejelentkezés után a rendszer a fürt listáját fogja látni az Azure Explorerben. További útmutatásért lásd: [Kapcsolódás a HDInsight-fürthöz](#connect-to-your-hdinsight-cluster).

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spark Scala-alkalmazás futtatása HDInsight ESP-fürtön az ID Broker (HIB) szolgáltatással

A normál lépések végrehajtásával elküldheti a feladatot HDInsight ESP-fürtre az ID Broker (HIB) használatával. További útmutatásért lásd: [Spark Scala-alkalmazás futtatása HDInsight Spark-fürtön](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster) .

Feltöltjük a szükséges fájlokat egy nevű mappába a bejelentkezési fiókjával, és a konfigurációs fájlban láthatja a feltöltési útvonalat.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png" alt-text="feltöltési útvonal a konfigurációban" border="true":::

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spark-konzol egy HDInsight ESP-fürtön az ID Broker (HIB) segítségével

A Spark helyi konzolt (Scala) futtathatja, vagy futtathatja a Spark Livy interaktív munkamenet-konzolt (Scala) egy HDInsight ESP-fürtön az ID Broker (HIB) használatával. További útmutatásért tekintse meg a [Spark-konzolt](#spark-console) .

   > [!NOTE]  
   > A HDInsight ESP-fürthöz, és az id Broker (HIB) esetében jelenleg nem támogatott a [fürt](#link-a-cluster) és a [hibakeresés Apache Spark-alkalmazások távoli](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) használata.

## <a name="reader-only-role"></a>Csak olvasó szerepkör

Ha a felhasználók a csak olvasási szerepkörrel rendelkező fürthöz küldenek feladatokat, a Ambari hitelesítő adatai szükségesek.

### <a name="link-cluster-from-context-menu"></a>Fürt csatolása a helyi menüből

1. Jelentkezzen be csak olvasó szerepkörrel rendelkező fiókkal.

2. Az **Azure Explorerben** bontsa ki a **HDInsight** elemet az előfizetésében található HDInsight-fürtök megtekintéséhez. A **"szerepkör: olvasó"** jelölésű fürtökön csak a csak olvasási jogosultsággal rendelkező szerepkör jogosult.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png" alt-text="&quot;IntelliJ Azure Explorer szerepkör: olvasó&quot;" border="true":::

3. Kattintson a jobb gombbal a fürtre, csak a csak olvasó szerepkörre vonatkozó engedéllyel. Válassza a **fürt csatolása** a helyi menüből lehetőséget a fürt összekapcsolásához. Adja meg a Ambari felhasználónevét és jelszavát.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png" alt-text="A fürt IntelliJ az Azure Explorerben" border="true":::

4. Ha a fürt sikeresen csatolva van, a rendszer frissíti a HDInsight.
   A fürt szakasza csatolva lesz.
  
    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png" alt-text="IntelliJ az Azure Explorer csatolt párbeszédpanelje" border="true":::

### <a name="link-cluster-by-expanding-jobs-node"></a>Fürt csatolása a feladatok csomópont kibontásával

1. Kattintson a **feladatok** csomópontra, majd a **fürthöz való hozzáférés megtagadva** ablakra.

2. Kattintson **a fürt összekapcsolása** elemre a fürt csatolásához.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png" alt-text="fürthöz való hozzáférés megtagadva párbeszédpanel" border="true":::

### <a name="link-cluster-from-rundebug-configurations-window"></a>Fürt csatolása a futtatási/hibakeresési konfigurációk ablakból

1. Hozzon létre egy HDInsight-konfigurációt. Ezután válassza **a távoli Futtatás a fürtben** lehetőséget.

2. Válasszon ki egy olyan fürtöt, amely csak a csak a Linux rendszerre vonatkozó szerepkör-engedéllyel rendelkezik a **Spark-fürtökhöz**. Figyelmeztető üzenet jelenik meg. A fürt csatolásához kattintson **a fürt csatolása** lehetőségre.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/create-configuration.png" alt-text="IntelliJ ötlet futtatása/hibakeresési konfiguráció létrehozása" border="true":::

### <a name="view-storage-accounts"></a>Storage-fiókok megtekintése

* A csak olvasási szerepkörrel rendelkező fürtök esetében kattintson a **Storage-fiókok** csomópontra, majd a **tárterület-hozzáférés megtagadva** ablakra. A **Azure Storage Explorer** megnyitása lehetőségre kattintva megnyithatja Storage Explorer.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png" alt-text="&quot;IntelliJ IDEA Storage-hozzáférés megtagadva&quot;" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png" alt-text="IntelliJ IDEA tároló hozzáférésének megtagadva gombja" border="true":::

* A csatolt fürtök esetében kattintson a **Storage-fiókok** csomópontra, majd a tárterület- **hozzáférés megtagadva** ablak jelenik meg. Az **Azure Storage megnyitása** lehetőségre kattintva megnyithatja Storage Explorer.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png" alt-text="&quot;IntelliJ IDEA Storage-hozzáférés Denied2&quot;" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png" alt-text="IntelliJ IDEA Storage-hozzáférés Denied2 gombja" border="true":::

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

:::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png " alt-text="Azure Portal törli a HDInsight-fürtöt" border="true":::

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja a Azure Toolkit for IntelliJ beépülő modult a [Scala](https://www.scala-lang.org/)-ban írt Apache Spark-alkalmazások fejlesztéséhez. Ezután Beküldte őket egy HDInsight Spark-fürtbe közvetlenül a IntelliJ integrált fejlesztői környezetből (IDE). A következő cikkből megtudhatja, hogy a Apache Sparkban regisztrált információk Hogyan tölthetők be egy BI Analytics-eszközbe, például Power BIba.

> [!div class="nextstepaction"]
> [Apache Spark-adatelemzés az Power BI használatával](apache-spark-use-bi-tools.md)
