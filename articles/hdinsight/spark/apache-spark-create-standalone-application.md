---
title: 'Oktatóanyag: Scala Maven-alkalmazás Spark & IntelliJ – Azure HDInsight'
description: Oktatóanyag – hozzon létre egy olyan Spark-alkalmazást, amelyet a Scala és az Apache Maven használatával írt be a Build rendszerként. És egy meglévő Maven archetípus for Scala, amelyet a IntelliJ IDEA biztosít.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: contperf-fy21q1
ms.date: 08/21/2020
ms.openlocfilehash: 3cbeb1dbd207eec7f58465a24f33808bf2e7c7c0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867610"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Oktatóanyag: Scala Maven-alkalmazás létrehozása a HDInsight-hez Apache Spark a IntelliJ használatával

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy olyan Apache Spark alkalmazást, amely az Apache Maven és a IntelliJ IDEA használatával készült a Scalaben. A cikk az Apache Mavent használja a Build rendszerként. És egy meglévő Maven-archetípustal kezdődik a Scala számára, amelyet a IntelliJ IDEA biztosít.  A Scala-alkalmazás IntelliJ IDEA-ban történő létrehozása az alábbi lépésekből áll:

* Buildelési rendszerként a Mavent használja.
* A Spark-modul függőségeinek feloldásához frissítse a Project Object Model- (POM-) fájlt.
* Írja meg az alkalmazást a Scalában.
* Hozzon létre egy olyan JAR-fájlt, amit el lehet küldeni a HDInsight Spark-fürtöknek.
* Futtassa az alkalmazást a Sparkon a Livy használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Scala beépülő modul az IntelliJ IDEA-hoz
> * Az IntelliJ használata Scala Maven-alkalmazások fejlesztéséhez
> * Önálló Scala-projekt létrehozása

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* [Oracle Java Development Kit](https://www.azul.com/downloads/azure-only/zulu/).  Ez az oktatóanyag a Java-verzió 8.0.202 használja.

* Java IDE. Ez a cikk a [INTELLIJ Idea Community ver-t használja.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Lásd: [a Azure Toolkit for IntelliJ telepítése](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in).

## <a name="install-scala-plugin-for-intellij-idea"></a>Scala beépülő modul az IntelliJ IDEA-hoz

A Scala beépülő modul telepítéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg az IntelliJ IDEA-t.

2. Az üdvözlőképernyőn kattintson a plugins **konfigurálása** elemre  >   a **plugins** ablak megnyitásához.

    :::image type="content" source="./media/apache-spark-create-standalone-application/enable-scala-plugin1.png" alt-text="&quot;IntelliJ IDEA – a Scala plugin&quot; engedélyezése" border="true":::

3. Válassza a **telepítés** lehetőséget az új ablakban Kiemelt Scala beépülő modulhoz.  

    :::image type="content" source="./media/apache-spark-create-standalone-application/install-scala-plugin.png" alt-text="' IntelliJ IDEA – a Scala plugin &quot;telepítése" border="true":::

4. A beépülő modul sikeres telepítését követően újra kell indítania az IDE-t.

## <a name="use-intellij-to-create-application"></a>Alkalmazás létrehozása az IntelliJ használatával

1. Indítsa el a IntelliJ ÖTLETET, és válassza az **új projekt létrehozása** elemet az **új projekt** ablak megnyitásához.

2. Válassza ki a **Apache Spark/HDInsight** elemet a bal oldali ablaktáblán.

3. Válassza ki a **Spark Project (Scala)** elemet a főablakból.

4. A **Build eszköz** legördülő listából válassza ki az alábbi értékek egyikét:
      * **Maven** a Scala projekt-létrehozás varázsló támogatásához.
      * A **SBT** kezelése és a Scala-projekt kiépítése.

   :::image type="content" source="./media/apache-spark-create-standalone-application/intellij-project-apache-spark.png" alt-text="IntelliJ az új projekt párbeszédpanel" border="true":::

5. Kattintson a **Tovább** gombra.

6. Az **új projekt** ablakban adja meg a következő információkat:  

  	|  Tulajdonság   | Leírás   |  
  	| ----- | ----- |  
  	|Projektnév| Adjon meg egy nevet.|  
  	|Projekt &nbsp; helye| Adja meg a projekt mentésének helyét.|
  	|Projekt SDK| Ez a mező üresen jelenik meg a gondolat első használatakor.  Válassza az **új...** lehetőséget, és navigáljon a JDK-hez.|
  	|Spark-verzió|A létrehozás varázsló a Spark SDK és a Scala SDK megfelelő verzióját integrálja. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa a **Spark 2.3.0 (Scala 2.11.8)** használja.|

    :::image type="content" source="./media/apache-spark-create-standalone-application/hdi-scala-new-project.png" alt-text="IntelliJ ÖTLETE a Spark SDK kiválasztásához" border="true":::

7. Válassza a **Befejezés** gombot.

## <a name="create-a-standalone-scala-project"></a>Önálló Scala-projekt létrehozása

1. Indítsa el a IntelliJ ÖTLETET, és válassza az **új projekt létrehozása** elemet az **új projekt** ablak megnyitásához.

2. Válassza a **Maven** lehetőséget a bal oldali panelen.

3. Adjon meg egy **Projekt SDK**-t. Ha üres, válassza az **új...** lehetőséget, és navigáljon a Java telepítési könyvtárához.

4. Jelölje be a **Létrehozás az archetípuson** jelölőnégyzetet.  

5. Az archetípusok listájából válassza a elemet **`org.scala-tools.archetypes:scala-archetype-simple`** . Ez az archetípus létrehozza a megfelelő címtár-struktúrát, és letölti a szükséges alapértelmezett függőségeket a Scala programba való íráshoz.

    :::image type="content" source="./media/apache-spark-create-standalone-application/intellij-project-create-maven.png" alt-text="A képernyőképen a kiválasztott archetípus látható az új projekt ablakban." border="true":::

6. Kattintson a **Tovább** gombra.

7. Bontsa ki az összetevők **koordinátáit**. Adja meg a megfelelő értékeket a **GroupID** és a **ArtifactId**. A **név** és a **hely** automatikusan kitöltődik. Ebben az oktatóanyagban a következő értékeket használjuk:

    - **GroupID:** com. microsoft. Spark. példa
    - **ArtifactId:** SparkSimpleApp

    :::image type="content" source="./media/apache-spark-create-standalone-application/intellij-artifact-coordinates.png" alt-text="Képernyőfelvétel: az új projekt ablakban látható az összetevők koordinátái lehetőség." border="true":::

8. Kattintson a **Tovább** gombra.

9. Ellenőrizze a beállításokat, majd válassza a **Next** (Tovább) lehetőséget.

10. Ellenőrizze a projekt nevét és helyét, majd válassza a **Finish** (Befejezés) lehetőséget.  A projekt importálása néhány percet is igénybe vehet.

11. A projekt importálása után a bal oldali panelen navigáljon a **SparkSimpleApp**  >  **src**  >  **test**  >  **Scala**  >  **com**  >  **Microsoft**  >  **Spark**-  >  **példához**.  Kattintson a jobb gombbal a **MySpec** elemre, majd válassza a **Törlés** lehetőséget. Nincs szüksége erre a fájlra az alkalmazáshoz.  Kattintson az **OK gombra** a párbeszédpanelen.
  
12. A későbbi lépések során frissíti a **pom.xml** a Spark Scala alkalmazás függőségeinek definiálásához. Ahhoz, hogy ezek a függőségek automatikusan letöltődnek és feloldhatók legyenek, konfigurálnia kell a Mavent.

13. A **fájl** menüben válassza a **Beállítások** elemet a **Beállítások** ablak megnyitásához.

14. A **Beállítások** ablakban navigáljon a **Build, a végrehajtás, az üzembe helyezési**  >  **eszközök**  >  **Maven**  >  **importálása** elemre.

15. Jelölje be a **Maven-projektek automatikus importálása** jelölőnégyzetet.

16. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.  Ezután visszakerül a projekt ablakába.

    :::image type="content" source="./media/apache-spark-create-standalone-application/configure-maven-download.png" alt-text="A Maven konfigurálása automatikus letöltésekhez" border="true":::

17. A bal oldali panelen navigáljon a   >  **fő**  >  **Scala**  >  **com. microsoft. Spark. example** parancsra, majd kattintson duplán  az alkalmazás elemre az app. Scala megnyitásához.

18. Cserélje le a meglévő mintakódot a következő kódra, majd mentse a módosításokat. Ez a kód beolvassa az adatokat a HVAC.csvból (az összes HDInsight Spark-fürtön elérhető). Lekéri azokat a sorokat, amelyek csak egy számjeggyel rendelkeznek a hatodik oszlopban. És a kimenetet a fürt alapértelmezett tárolójában lévő **/HVACOut** írja.

    ```scala
    package com.microsoft.spark.example

    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    /**
      * Test IO to wasb
      */
    object WasbIOTest {
        def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACout")
        }
    }
    ```

19. A bal oldali panelen kattintson duplán a **pom.xml** fájlra.  

20. A `<project>\<properties>` részben adja hozzá a következő szegmenseket:

    ```xml
    <scala.version>2.11.8</scala.version>
    <scala.compat.version>2.11.8</scala.compat.version>
    <scala.binary.version>2.11</scala.binary.version>
    ```

21. A `<project>\<dependencies>` részben adja hozzá a következő szegmenseket:

    ```xml
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_${scala.binary.version}</artifactId>
        <version>2.3.0</version>
    </dependency>
    ```

    Mentse a pom.xml fájl módosításait.

22. Hozza létre a .jar kiterjesztésű fájlt. Az IntelliJ IDEA lehetővé teszi a JAR-fájlok projektösszetevőként való létrehozását. Tegye a következőket.

    1. A **fájl** menüben válassza a **projekt szerkezete...** lehetőséget.

    2. A **projekt szerkezete** ablakban navigáljon   >  **a plusz szimbólum +**  >  **jar**  >  **elemhez a függőségekkel rendelkező moduloknál..**.

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png" alt-text="&quot;IntelliJ IDEA Project Structure hozzáadása jar&quot;" border="true":::

    3. A **jar létrehozása a modulokból** ablakban válassza ki a mappa ikont a **Főosztály** szövegmezőben.

    4. A **Főosztály kiválasztása** ablakban válassza ki az alapértelmezés szerint megjelenő osztályt, majd kattintson az **OK gombra**.

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png" alt-text="&quot;A IntelliJ IDEA projekt struktúrájának kiválasztása&quot;" border="true":::

    5. A **jar létrehozása a modulokból** ablakban győződjön meg arról, **hogy a kibontás a cél jar** beállítás be van jelölve, majd kattintson **az OK gombra**.  Ez a beállítás egyetlen JAR-fájlt hoz létre az összes függőséggel együtt.

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png" alt-text="IntelliJ IDEA Project Structure jar a modulból" border="true":::

    6. A **kimeneti elrendezés** lap felsorolja a Maven-projekt részét képező összes edényt. Itt kijelölheti és törölheti azokat, amelyek nincsenek közvetlen függőségi viszonyban a Scala-alkalmazással. Az alkalmazáshoz itt hozza létre az összeset, de az utolsót (**SparkSimpleApp fordítási kimenet**). Válassza ki a törölni kívánt tégelyeket, majd válassza ki a negatív szimbólumot **-** .

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png" alt-text="&quot;IntelliJ IDEA Project Structure delete output&quot;" border="true":::

        Győződjön meg arról, hogy be van jelölve a **Belefoglalás a Project buildbe** jelölőnégyzet. Ezzel a beállítással biztosíthatja, hogy a rendszer minden alkalommal létrehozza a jar-t, amikor a projekt felépítése vagy frissítése megtörtént. Válassza az **alkalmaz** , majd **az OK gombot**.

    7. A jar létrehozásához navigáljon a **Build-összetevők** kiépítéséhez  >    >  . A projekt körülbelül 30 másodperc múlva lesz lefordítva.  A kimeneti JAR-fájl az **\out\artifacts** mappában jön létre.

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png" alt-text="IntelliJ IDEA Project-összetevő kimenete" border="true":::

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Az alkalmazás futtatása a Apache Spark-fürtön

Az alkalmazás fürtön történő futtatásához a következő módszereket használhatja:

* **Másolja az Application jar-t a fürthöz társított Azure Storage-blobba** . Ehhez az **AzCopy** parancssori segédprogramot használhatja. Az adatok feltöltésére számos egyéb kliens is rendelkezésre áll. További információt a [HDInsight-ben található Apache Hadoop feladatok feltöltésekor](../hdinsight-upload-data.md)talál.

* Az **Apache Livy használatával távolról is elküldhet egy alkalmazás-feladatot** a Spark-fürtön. A HDInsight-alapú Spark-fürtök tartalmazzák a Livyt, amely elvégzi a REST-végpontok közzétételét, így lehetővé teszi a Spark-feladatok távoli elküldését. További információ: [Apache Spark feladatok távoli elküldése az Apache Livy és a Spark-fürtök használatával a HDInsight-on](apache-spark-livy-rest-interface.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a létrehozott fürtöt a következő lépésekkel:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A felső **keresőmezőbe** írja be a **HDInsight** kifejezést.

1. Válassza ki a **HDInsight-fürtök** elemet a **szolgáltatások** területen.

1. A megjelenő HDInsight-fürtök listájában kattintson a **...** elemre az oktatóanyaghoz létrehozott fürt mellett.

1. Válassza a **Törlés** elemet. Válassza az **Igen** lehetőséget.

:::image type="content" source="./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png " alt-text="&quot;A HDInsight Azure Portalon törölje" border="true":::a (z)" törlése-fürt "szegélyét =" true ":::

## <a name="next-step"></a>Következő lépés

Ebben a cikkben megtanulta, hogyan hozhat létre Apache Spark Scala-alkalmazást. Folytassa a következő cikkel, amelyben megtudhatja, hogyan futtathatja ezt az alkalmazást egy HDInsight Spark-fürtön, a Livy használatával.

> [!div class="nextstepaction"]
>[Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](./apache-spark-livy-rest-interface.md)