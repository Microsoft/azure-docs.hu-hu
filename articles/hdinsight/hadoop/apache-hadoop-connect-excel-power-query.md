---
title: Excel csatlakoztatása a Power Query – Azure HDInsight az Apache Hadoop
description: Ismerje meg, hogyan üzleti intelligencia összetevők előnyeit, és használja a Power Query az Excel programhoz Hadoop on HDInsight tárolt adatok elérését.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: e35e86bee4dfee21d0a98867c3e0d1d7850ed0ec
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762007"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Az Apache hadoop Excel csatlakoztatása a Power Query használatával
A Microsoft big-data megoldás egyik fontos szolgáltatása az integrációt a Microsoft üzleti intelligenciára épülő (BI) összetevők az Azure HDInsight az Apache Hadoop-fürtök. Egy elsődleges példája az Excel összekapcsolása a az Azure Storage-fiókot, amely tartalmazza az adatokat a Hadoop-fürt társított az Excel-bővítmény a Microsoft Power Query használatával lehetővé teszi. Ez a cikk ismerteti, hogyan beállítása és használata a Power Query használatával adatokat lekérdezni a HDInsight felügyelt Hadoop-fürt társított.

### <a name="prerequisites"></a>Előfeltételek
Ez a cikk elkezdéséhez a következőkkel kell rendelkeznie:

* **Egy HDInsight-fürt**. Az egyik beállítása: [Ismerkedés az Azure HDInsight] [hdinsight – első lépések].
* **Egy munkaállomás** futtató, Windows 7, Windows Server 2008 R2 vagy újabb operációs rendszert.
* **Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, az Excel 2013 Standalone vagy Office 2010 Professional Plus**.

## <a name="install-power-query"></a>A Power Query telepítése
A Power Query importálhatja az adatokat, amelyek lett kimeneti vagy, amely egy HDInsight-fürtön futó Hadoop-feladatok által lett létrehozva.

Az Excel 2016 esetében a Power Query integrálva lett az adatok szalagon beolvasása és átalakítása szakaszban. Az Excel régebbi verziójú, töltse le a Microsoft Power Query az Excel programhoz készült a [Microsoft Download Center] [ powerquery-download] , és telepítse.

## <a name="import-hdinsight-data-into-excel"></a>HDInsight-adatok importálása Excelbe
Excelhez készült Power Query beépülő megkönnyíti az adatok importálása az Excelbe, ahol Üzletiintelligencia-eszközökkel, mint például a PowerPivot és a Power Mappel segítségével megvizsgálhatja, elemzése és az adatokat a HDInsight-fürtből származó.

**Adatok importálása egy HDInsight-fürt**

1. Nyissa meg az Excelt.
2. Hozzon létre egy új üres munkafüzet.
3. Hajtsa végre az alábbi lépéseket az Excel-verzió alapján:

   - Excel 2016

     - Kattintson a **adatok** menüben kattintson a **adatok lekérése** származó a **adatok beolvasása és átalakítása** menüszalagra, majd **az Azure**, majd **Származó Azure HDInsight(HDFS)**.

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

   - Excel 2013/2010

     - Kattintson a **Power Query** menüben kattintson a **az Azure**, és kattintson a **a Microsoft Azure HDInsight**.
   
       ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
       **Megjegyzés:** Ha nem látja a **Power Query** menüben nyissa meg **fájl** > **beállítások** > **bővítmények**, és válassza ki a  **COM-bővítmények** a legördülő listából **kezelés** be az oldal alján. Válassza ki a **Go...**  gombra, és győződjön meg arról, hogy a Power Query az Excel bővítmény be a ellenőrizte.
       
       **Megjegyzés:** Power Query is lehetővé teszi, hogy adatokat importáljon HDFS kattintva **egyéb forrásokból származó**.
4. A **fióknév**, adja meg a fürthöz társított Azure Blob storage-fiók nevét, és kattintson **OK**. Ezt a fiókot az alapértelmezett tárfiókot, vagy egy társított storage-fiók lehet.  A formátum *https://&lt;StorageAccountName >.blob.core.windows.net/*.
5. A **Fiókkulcs**, adja meg a Blob storage-fiók kulcsát, és kattintson **mentése**. (Kell adja meg a fiók csak az első információk ideje az áruház.)
6. Az a **kezelő** a a Lekérdezésszerkesztő bal oldali ablaktáblán kattintson duplán a Blob storage-tároló nevének. Alapértelmezés szerint a tároló neve a neve megegyezik a fürt neve.
7. Keresse meg **HiveSampleData.txt** a a **neve** oszlopban (a mappa elérési útja **... / hive/adatraktár-/ hivesampletable/**), és kattintson a **bináris** HiveSampleData.txt, a bal oldalon. A fürt összes HiveSampleData.txt tartalmaz. Ha szeretné a saját fájlt is használhat.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Ha azt szeretné, átnevezheti az oszlopok neveit. Amikor elkészült, kattintson a **Bezárás és betöltés**.  A munkafüzet fel lett töltve az adatokat:
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan HDInsight-adatokat lekérni az Excelbe a Power Query használatával. Hasonlóképpen kérheti le adatokat a HDInsight az Azure SQL Database-be. Akkor is az adatok feltöltése a HDInsight. További tudnivalókért tekintse meg a következő cikkeket:

* [Az Apache Hive-adatok vizualizálása a Microsoft Power bi-ban az Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Power BI segítségével az Azure HDInsight adatok interaktív lekérdezéses Hive megjelenítése](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Az Apache a Zeppelin használata Azure HDInsight az Apache Hive-lekérdezések futtatásához](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Excel csatlakoztatása a Microsoft Hive ODBC illesztőprogram segítségével a HDInsight](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Csatlakozás az Azure HDInsight és a Data Lake Tools for Visual Studio használatával, az Apache Hive-lekérdezések futtatása](apache-hadoop-visual-studio-tools-get-started.md).
* [Az Azure HDInsight-eszköz használata a Visual Studio Code](../hdinsight-for-vscode.md).
* [Adatok feltöltése a HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: https://go.microsoft.com/fwlink/?LinkID=286689
