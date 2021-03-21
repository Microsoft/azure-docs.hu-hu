---
title: 'Lásd: az interaktív lekérdezési struktúra adatPower BI az Azure HDInsight'
description: Az interaktív lekérdezési struktúra adatainak megjelenítése a Microsoft Power BI használatával az Azure HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 7f249bb0e81bf3a371b8743a304ef49baffaed7a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98941392"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Interaktív lekérdezési Apache Hive adatai megjelenítése a Microsoft Power BI használatával közvetlen lekérdezéssel a HDInsight-ben

Ez a cikk azt ismerteti, hogyan csatlakoztathatók a Microsoft Power BI az Azure HDInsight interaktív lekérdezési fürtökhöz, és Hogyan jeleníthető meg a Apache Hive adatai közvetlen lekérdezéssel. A példában szereplő példa egy `hivesampletable` struktúra-táblázat adatait Power BIba tölti be. A `hivesampletable` kaptár tábla néhány mobiltelefon-használati adatokat tartalmaz. Ezután kinyomtatja a használati adatokat a globális térképen:

![HDInsight Power BI a leképezési jelentés](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Az [Apache HIVE ODBC-illesztőt](../hadoop/apache-hadoop-connect-hive-power-bi.md) kihasználhatja a Power bi Desktop általános ODBC-összekötőn keresztül történő importáláshoz. Azonban nem ajánlott a BI-munkaterhelések esetében a kaptár-lekérdezési motor nem interaktív természetét figyelembe venni. A [HDInsight interaktív lekérdezés-összekötő](./apache-hadoop-connect-hive-power-bi-directquery.md) és a [HDInsight Apache Spark-összekötő](/power-bi/spark-on-hdinsight-with-direct-connect) jobb választás a teljesítményük szempontjából.

## <a name="prerequisites"></a>Előfeltételek
A cikk végrehajtása előtt a következő elemeket kell megadnia:

* **HDInsight-fürt**. A fürt lehet egy Apache Hive vagy egy újonnan kiadott interaktív lekérdezési fürttel rendelkező HDInsight-fürt. Fürtök létrehozásával kapcsolatban lásd: [fürt létrehozása](../hadoop/apache-hadoop-linux-tutorial-get-started.md).
* **[Microsoft Power bi Desktop](https://powerbi.microsoft.com/desktop/)**. A másolást a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=45331)töltheti le.

## <a name="load-data-from-hdinsight"></a>Adatok betöltése a HDInsight

A `hivesampletable` kaptár tábla minden HDInsight-fürtöt tartalmaz.

1. Indítsa el a Power BI Desktopot.

2. A menüsávban navigáljon a **Kezdőlap**  >  **adatok beolvasása**  >  **továbbiak...** elemre.

    ![HDInsight Power BI további információ](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Az **adatok lekérése** ablakban írja be a **hdinsight** kifejezést a keresőmezőbe.  

4. A keresési eredmények közül válassza a **HDInsight interaktív lekérdezés** lehetőséget, majd válassza a **kapcsolat** lehetőséget.  Ha nem látja a **HDInsight interaktív lekérdezést**, frissítenie kell a Power bi Desktopt a legújabb verzióra.

5. A **Folytatás** gombra kattintva zárhatja be a **harmadik féltől származó szolgáltatáshoz való kapcsolódás** párbeszédpanelt.

6. A **HDInsight interaktív lekérdezési** ablakban adja meg a következő információkat, majd kattintson **az OK gombra**:

    |Tulajdonság | Érték |
    |---|---|
    |Kiszolgáló |Adja meg a fürt nevét, például *myiqcluster.azurehdinsight.net*.|
    |Adatbázis |Adja meg az **alapértelmezett** értéket ehhez a cikkhez.|
    |Adatkapcsolati mód |Ehhez a cikkhez válassza a **DirectQuery** lehetőséget.|

    ![HDInsight interaktív lekérdezés Power BI DirectQuery-kapcsolat](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Adja meg a HTTP hitelesítő adatokat, majd válassza a **kapcsolat** lehetőséget. Az alapértelmezett Felhasználónév a **rendszergazda**.

8. A bal oldali ablaktábla **navigátor** ablakában válassza a **hivesampletale** lehetőséget.

9. Válassza a **Betöltés** lehetőséget a főablakban.

    ![HDInsight interaktív lekérdezés Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Térképes adatmegjelenítés

Folytassa az utolsó eljárással.

1. A vizualizációk ablaktáblán válassza a **Térkép**, majd a földgömb ikont. Ekkor megjelenik egy általános Térkép a főablakban.

    ![HDInsight Power BI a jelentés testreszabása](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. A mezők panelen válassza az **ország** és a **devicemake** lehetőséget. Az adatpontokkal rendelkező globális Térkép néhány pillanat múlva megjelenik a főablakban.

3. Bontsa ki a térképet.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan jelenítheti meg a HDInsight adatait a Microsoft Power BI használatával.  Az adatvizualizációval kapcsolatos további információkért tekintse meg a következő cikkeket:

* Az [Azure HDInsight használatával megjelenítheti a Microsoft Power BI Apache Hivei adataival](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Apache Hive lekérdezések futtatása az Azure HDInsight az Apache Zeppelin használatával](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Az Excel és a HDInsight összekötése a Microsoft kaptár ODBC-illesztővel](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* Az [Excel Apache Hadoop Power Query használatával csatlakoztatható](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Kapcsolódjon az Azure HDInsight-hez, és Apache Hive-lekérdezéseket futtathat a Visual studióhoz készült Data Lake Tools használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Használja az Azure HDInsight eszközt a Visual Studio Code](../hdinsight-for-vscode.md)-hoz.
* [Adatok feltöltése a HDInsight](./../hdinsight-upload-data.md).