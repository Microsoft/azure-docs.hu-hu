---
title: 'Oktatóanyag: Scala Maven-alkalmazás létrehozása a Spark in Azure HDInsight számára, az IntelliJ használatával'
description: Hozzon létre egy Scalában írt Spark-alkalmazást az Apache Maven buildelési rendszer használatával, valamint egy meglévő Maven archetype-ot a Scala számára az IntelliJ IDEA használatával.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/07/2018
ms.openlocfilehash: 529dc37187f6709ecfa93bf3a6d71f053ba49103
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51008963"
---
# <a name="tutorial-create-a-scala-maven-application-for-spark-in-hdinsight-using-intellij"></a>Oktatóanyag: Scala Maven-alkalmazás létrehozása a Spark in HDInsight számára, az IntelliJ használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre Scalában írt Spark-alkalmazást a Maven és az IntelliJ IDEA használatával. Ez a cikk az Apache Maven buildelési rendszert használja, és egy, a Scala számára írt, az IntelliJ IDEA által biztosított, meglévő Maven archetype-ot használ kiindulási pontként.  A Scala-alkalmazás IntelliJ IDEA-ban történő létrehozása az alábbi lépésekből áll:

* Buildelési rendszerként a Mavent használja.
* A Spark-modul függőségeinek feloldásához frissítse a Project Object Model- (POM-) fájlt.
* Írja meg az alkalmazást a Scalában.
* Hozzon létre egy olyan JAR-fájlt, amit el lehet küldeni a HDInsight Spark-fürtöknek.
* Futtassa az alkalmazást a Sparkon a Livy használatával.

> [!NOTE]
> Az alkalmazások létrehozásának és azok Linux rendszerű HDInsight Spark-fürtöknek való elküldésének megkönnyítésére a HDInsight egy IntelliJ IDEA beépülőmodul-eszközt is biztosít. További információk: [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md).
> 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az IntelliJ használata Scala Maven-alkalmazások fejlesztéséhez

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* Oracle Java fejlesztői készlet. A készletet [innen](https://aka.ms/azure-jdks) telepítheti.
* Java IDE. Ez a cikk az IntelliJ IDEA 18.1.1-es verzióját használja. A készletet [innen](https://www.jetbrains.com/idea/download/) telepítheti.

## <a name="use-intellij-to-create-application"></a>Alkalmazás létrehozása az IntelliJ használatával

1. Indítsa el az IntelliJ IDEA-t, majd hozzon létre egy projektet. A **New Project** (Új projekt) párbeszédablakban hajtsa végre az alábbi lépéseket: 

   a. Válassza ki a **HDInsight** > **Spark on HDInsight (Scala)** lehetőséget.

   b. A **Build tool** (Létrehozási eszköz) listában válassza ki az alábbiak közül az igényeinek megfelelőt:

      * **Maven**, a Scala projektlétrehozási varázsló támogatásához
      * **SBT**, a függőségek kezeléséhez és a Scala-projekt létrehozásához

   ![A New project (Új projekt) párbeszédablak.](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

1. Kattintson a **Tovább** gombra.

1. A Scala projektlétrehozási varázsló automatikusan észleli, hogy telepítette-e a Scala beépülő modulját. Válassza az **Install** (Telepítés) lehetőséget.

   ![A Scala beépülő moduljának ellenőrzése](./media/apache-spark-create-standalone-application/Scala-Plugin-check-Reminder.PNG) 

1. Az Scala beépülő moduljának letöltéséhez válassza az **OK** elemet. Az IntelliJ újraindításához kövesse az utasításokat. 

   ![A Scala beépülő moduljának telepítési párbeszédablaka](./media/apache-spark-create-standalone-application/Choose-Scala-Plugin.PNG)

1. A **New Project** (Új projekt) ablakban hajtsa végre az alábbi lépéseket:  

    ![A Spark SDK kiválasztása](./media/apache-spark-create-standalone-application/hdi-new-project.png)

   a. Adja meg a projekt nevét és helyét.

   b. A **Project SDK** (Projekt SDK) legördülő listából Spark 2.x-fürt esetében válassza a **Java 1.8**, Spark 1.x-fürt esetében pedig a **Java 1.7** lehetőséget.

   c. A **Spark version** (Spark-verzió) legördülő listában a Scala projektlétrehozási varázsló integrálja a Spark SDK-hoz és Scala SDK-hoz megfelelő verziót. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa a következő verziót használja: **Spark 2.0.2 (Scala 2.11.8)**.

1. Válassza a **Finish** (Befejezés) elemet.

## <a name="install-scala-plugin-for-intellij-idea"></a>Scala beépülő modul az IntelliJ IDEA-hoz
A Scala beépülő modul telepítésének lépesei:

1. Nyissa meg az IntelliJ IDEA-t.
1. Az üdvözlőképernyőn válassza a **Configure2** (Konfigurálás), majd a **Plugins** (Beépülő modulok) lehetőséget.
   
    ![A Scala beépülő modul engedélyezése](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
1. Kattintson a képernyő bal alsó sarkában található **Install JetBrains plugin** (JetBrains beépülő modul telepítése) gombra. 
1. A **Browse JetBrains Plugins** (JetBrains beépülő modulok tallózása) párbeszédpanelen keressen rá a **Scala** kifejezésre, majd válassza az **Install** (Telepítés) lehetőséget.
   
    ![A Scala beépülő modul telepítése](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
1. A beépülő modul sikeres telepítését követően újra kell indítania az IDE-t.

## <a name="create-a-standalone-scala-project"></a>Önálló Scala-projekt létrehozása
1. Nyissa meg az IntelliJ IDEA-t.
1. Új projekt létrehozásához a **File** (Fájl) menüben válassza a **New > Project** (Új > Projekt) lehetőséget.
1. Az Új projekt párbeszédpanelen válassza a következő lehetőségeket:
   
    ![Maven-projekt létrehozása](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * A projekt típusaként válassza a **Maven** lehetőséget.
   * Adjon meg egy **Projekt SDK**-t. Válassza a **NEW** (Új) lehetőséget, majd lépjen a Java telepítési könyvtárába, ami általában a következő: `C:\Program Files\Java\jdk1.8.0_66`.
   * Válassza a **Create from archetype** (Létrehozás archetype-ból) lehetőséget.
   * Az archetype-ok listájából válassza ki a következőt: **org.scala-tools.archetypes:scala-archetype-simple**. Ez az archetype létrehozza a megfelelő könyvtárszerkezetet, valamint letölti a Scala-program megírásához szükséges alapértelmezett függőségeket.
1. Kattintson a **Tovább** gombra.
1. Adja meg a **GroupId** (Csoportazonosító), az **ArtifactId** (Összetevő-azonosító) és a **Version** (Verzió) mezők értékeit. Ebben az oktatóanyagban a következő értékeket használjuk:

    - Csoportazonosító: com.microsoft.spark.example
    - Összetevő-azonosító: SparkSimpleApp
1. Kattintson a **Tovább** gombra.
1. Ellenőrizze a beállításokat, majd válassza a **Next** (Tovább) lehetőséget.
1. Ellenőrizze a projekt nevét és helyét, majd válassza a **Finish** (Befejezés) lehetőséget.
1. A bal oldali panelen válassza az **src > test > scala > com > microsoft > spark > example** lehetőségeket, kattintson a jobb gombbal a **MySpec** elemre, majd válassza a **Delete** (Törlés) lehetőséget. Nincs szüksége erre a fájlra az alkalmazáshoz.
  
1. Az ezt követő lépésekben frissíteni fogja a pom.xml fájlt a Spark Scala-alkalmazás függőségeinek meghatározásához. A rendszer által automatikusan letöltött és feloldott függőségek esetében a Mavent ennek megfelelően kell konfigurálnia.
   
    ![A Maven konfigurálása automatikus letöltésekhez](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. A **File** (Fájl) menüben válassza a **Settings** (Beállítások) lehetőséget.
   1. A **Settings** (Beállítások) párbeszédpanelen válassza a **Build, Execution, Deployment** > **Build Tools** > **Maven** > **Importing** (Összeállítás, Futtatás, Üzembe helyezés; Összeállítási eszközök; Maven; Importálás) lehetőséget.
   1. Jelölje be az **Import Maven projects automatically** (Maven-projektek automatikus importálása) beállítást.
   1. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.
1. A bal oldali panelen válassza az **src > main > scala > com.microsoft.spark.example** lehetőségeket, majd kattintson duplán az **App** (Alkalmazás) elemre az App.scala megnyitásához.

1. Cserélje le a meglévő mintakódot a következő kódra, majd mentse a módosításokat. Ez a kód a (minden HDInsight Spark-fürtön megtalálható) HVAC.csv fájlból olvassa be az adatokat, lekéri azokat a sorokat, amelyek hatodik oszlopában csak egy számjegy szerepel, a kimenetet pedig a fürt alapértelmezett Storage-tárolójának **/HVACOut** mappájába írja.

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
1. A bal oldali panelen kattintson duplán a **pom.xml** fájlra.
   
   1. A `<project>\<properties>` részben adja hozzá a következő szegmenseket:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   1. A `<project>\<dependencies>` részben adja hozzá a következő szegmenseket:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Mentse a pom.xml fájl módosításait.
1. Hozza létre a .jar kiterjesztésű fájlt. Az IntelliJ IDEA lehetővé teszi a JAR-fájlok projektösszetevőként való létrehozását. Hajtsa végre a következő lépéseket.
    
    1. A **File** (Fájl) menüben válassza a **Project Structure** (Projektstruktúra) lehetőséget.
    1. A **Project Structure** (Projektstruktúra) párbeszédpanelen válassza az **Artifacts** (Összetevők) elemet, majd kattintson a pluszjelre. Az előugró párbeszédpanelen válassza a **JAR**, majd a **From modules with dependencies** (Függőségekkel rendelkező modulokból) lehetőséget.
       
        ![JAR-fájl létrehozása](./media/apache-spark-create-standalone-application/create-jar-1.png)
    1. A **Create JAR from Modules** (JAR-fájl létrehozása modulokból) párbeszédpanelen kattintson a **Main Class** (Main osztály) elem melletti három pont (![…](./media/apache-spark-create-standalone-application/ellipsis.png)) gombra.
    1. A **Select Main Class** (Main osztály kiválasztása) párbeszédpanelen válassza az alapértelmezettként megjelenő osztályt, majd kattintson az **OK** gombra.
       
        ![JAR-fájl létrehozása](./media/apache-spark-create-standalone-application/create-jar-2.png)
    1. A **Create JAR from Modules** (JAR-fájl létrehozása modulokból) párbeszédpanelen győződjön meg arról, hogy az **extract to the target JAR** (Kibontás a cél JAR-fájlba) beállítás be van jelölve, majd kattintson az **OK** gombra.  Ez a beállítás egyetlen JAR-fájlt hoz létre az összes függőséggel együtt.
       
        ![JAR-fájl létrehozása](./media/apache-spark-create-standalone-application/create-jar-3.png)
    1. A Kimeneti elrendezés lapon fel van sorolva az összes olyan JAR-fájl, amelyet a Maven-projekt tartalmaz. Itt kijelölheti és törölheti azokat, amelyek nincsenek közvetlen függőségi viszonyban a Scala-alkalmazással. Az itt létrehozott alkalmazás esetében az utolsó (**SparkSimpleApp compile output**) kivételével az összes fájlt törölheti. Jelölje ki a törölni kívánt JAR-fájlokat, majd kattintson a **Delete** (Törlés) ikonra.
       
        ![JAR-fájl létrehozása](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Győződjön meg arról, hogy az **Include in project build** (Projektbuild belefoglalása) beállítás be van jelölve, amely azt biztosítja, hogy a projekt összeállításakor vagy frissítésekor minden alkalommal létrejöjjön a JAR-fájl. Kattintson az **Apply** (Alkalmaz), majd az **OK** gombra.
    1. A JAR-fájl létrehozásához a **Build** menüben válassza a **Build Artifacts** (Buildösszetevők) lehetőséget. A kimeneti JAR-fájl az **\out\artifacts** mappában jön létre.
       
        ![JAR-fájl létrehozása](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Az alkalmazás futtatása a Spark-fürtön
Az alkalmazás fürtön történő futtatásához a következő módszereket használhatja:

* **Másolja át az alkalmazás JAR-fájlját abba az Azure Storage-blobba**, amely a fürthöz van társítva. Ehhez az [**AzCopy**](../../storage/common/storage-use-azcopy.md) parancssori segédprogramot használhatja. Az adatok feltöltésére számos egyéb kliens is rendelkezésre áll. További információ: [Upload data for Hadoop jobs in HDInsight](../hdinsight-upload-data.md) (Adatok feltöltése Hadoop-feladatokhoz a HDInsightban).
* **A Livyt használhatja egy alkalmazásfeladat távoli elküldéséhez** a Spark-fürt számára. A HDInsight-alapú Spark-fürtök tartalmazzák a Livyt, amely elvégzi a REST-végpontok közzétételét, így lehetővé teszi a Spark-feladatok távoli elküldését. További információ: [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight](apache-spark-livy-rest-interface.md) (Spark-feladatok távoli elküldése a Livy használatával, HDInsight-alapú Spark-fürtök esetében).

## <a name="next-step"></a>Következő lépés

Ebben a cikkben megismerkedhetett Spark Scala-alkalmazások létrehozásával. Folytassa a következő cikkel, amelyben megtudhatja, hogyan futtathatja ezt az alkalmazást egy HDInsight Spark-fürtön, a Livy használatával.

> [!div class="nextstepaction"]
>[Feladatok távoli futtatása Spark-fürtön a Livy használatával](./apache-spark-livy-rest-interface.md)

