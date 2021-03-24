---
title: Az Excel és a Apache Hadoop összekötése Power Query-Azure HDInsight
description: Ismerje meg, hogyan használhatja ki az üzleti intelligencia összetevőit, és hogyan férhet hozzá az Excelhez Power Query az Hadoop-on tárolt adatokhoz a HDInsight-on.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 13862e642c6a91fe6f3c635df2efde91672ecbad
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866811"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Az Excel és a Apache Hadoop összekötése Power Query használatával

A Microsoft Big-adatok megoldásának egyik fő funkciója a Microsoft Business Intelligence-(BI-) összetevők integrálása Apache Hadoop-fürtökkel az Azure HDInsight-ben. Az elsődleges példa az Excel és az Azure Storage-fiók összekapcsolása, amely a Hadoop-fürthöz társított adatait tartalmazza a Excelhez készült Microsoft Power Query beépülő modullal. Ez a cikk bemutatja, hogyan állíthatja be és használhatja a Power Queryt a HDInsight-mel felügyelt Hadoop-fürtökhöz kapcsolódó adatlekérdezéshez.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).
* Windows 10, 7, Windows Server 2008 R2 vagy újabb operációs rendszert futtató munkaállomás.
* Microsoft 365 alkalmazások nagyvállalati, Office 2016, Office 2013 Professional Plus, Excel 2013 önálló vagy Office 2010 Professional Plus rendszerhez.

## <a name="install-microsoft-power-query"></a>A Microsoft Power Query telepítése

A Power Query képes importálni olyan adatokat, amelyek kimenete vagy egy HDInsight-fürtön futó Hadoop-feladatok által generált adatok.

Az Excel 2016-es verziójában a Power Query be lett építve az adatszalagba a beolvasás & átalakítás szakaszban. A régebbi Excel-verziók esetében töltse le Excelhez készült Microsoft Power Query a [Microsoft letöltőközpontból](https://go.microsoft.com/fwlink/?LinkID=286689) , és telepítse.

## <a name="import-hdinsight-data-into-excel"></a>HDInsight-adatimportálás az Excelbe

Az Excelhez készült Power Query beépülő modul megkönnyíti az adatok importálását az HDInsight-fürtről az Excelbe, ahol a BI-eszközök, például a PowerPivot és a Power Map használhatók az adatok vizsgálatára, elemzésére és bemutatására.

1. Indítsa el az Excelt.

1. Hozzon létre egy új üres munkafüzetet.

1. Hajtsa végre az alábbi lépéseket az Excel-verziótól függően:

   * Excel 2016

     * Válassza ki > **adatok**  >  **beolvasása**  >  **Az Azure**  >  **-ból Azure HDInsight-ből (HDFS)**.

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png" alt-text="HDI. PowerQuery. SelectHdiSource. 2016" border="true":::

   * Excel 2013/2010

     * Válassza ki a **Power Query**  >  **Az Azure**  >  **-ból Microsoft Azure HDInsightból**.

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png" alt-text="HDI. PowerQuery. SelectHdiSource" border="true":::

       **Megjegyzés:** Ha nem látja a **Power Query** menüt, lépjen a **file**  >  **Options**  >  **bővítmények** menüpontra, majd a lap alján található legördülő lista **kezelés** mezőjében válassza a **com-bővítmények** lehetőséget. Kattintson a **Go... (ugrás)** gombra, és ellenőrizze, hogy az Excel-bővítményhez tartozó Power Query jelölőnégyzet be van-e jelölve.

       **Megjegyzés:** A Power Query lehetővé teszi az adatok HDFS történő importálását **más forrásokból** való kiválasztással.

1. Az **Azure HDInsight (HDFS)** párbeszédpanel **fiók neve vagy URL** szövege mezőjébe írja be a fürthöz társított Azure Blob Storage-fiók nevét. Ez után válassza az **OK** gombot. Ez a fiók lehet az alapértelmezett Storage-fiók vagy egy társított Storage-fiók.  A formátum `https://StorageAccountName.blob.core.windows.net/`.

1. A **fiók kulcsa** mezőben adja meg a blob Storage-fiók kulcsát, majd válassza a **kapcsolat** lehetőséget. (A fiók adatait csak akkor kell megadnia, amikor először fér hozzá ehhez a tárolóhoz.)

1. A lekérdezés-szerkesztő bal oldalán található **navigátor** ablaktáblán kattintson duplán a fürthöz társított blob Storage-tároló nevére. Alapértelmezés szerint a tároló neve ugyanaz a neve, mint a fürt neve.

1. Keresse meg **HiveSampleData.txt** a **Name (név** ) oszlopban (a mappa elérési útja: **.. /Hive/Warehouse/hivesampletable/**), majd a HiveSampleData.txt bal oldalán válassza a **Binary (bináris** ) lehetőséget. HiveSampleData.txt az összes fürthöz tartozik. Igény szerint saját fájlt is használhat.

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png" alt-text="HDI Excel Power Query – adatimportálás" border="true":::

1. Ha szeretné, átnevezheti az oszlopnevek nevét. Ha elkészült, válassza a **bezárás & betöltés** lehetőséget.  A rendszer betöltötte az adatait a munkafüzetbe:

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png" alt-text="HDI Excel Power Query importált táblázat" border="true":::

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan használhatja a Power Queryt adatok lekéréséhez a HDInsight-ből az Excelbe. Hasonlóképpen lekérheti a HDInsight adatait a Azure SQL Databaseba. Az adatok a HDInsight is feltölthetők. További tudnivalókért olvassa el a következő cikket:

* Az [Azure HDInsight-ban a Microsoft Power BI Apache Hivei az adatmegjelenítést](apache-hadoop-connect-hive-power-bi.md).
* [Interaktív lekérdezési struktúra-adatmegjelenítés az Azure HDInsight Power BIával](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Apache Hive lekérdezések futtatása az Azure HDInsight az Apache Zeppelin használatával](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Az Excel és a HDInsight összekötése a Microsoft kaptár ODBC-illesztővel](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Kapcsolódjon az Azure HDInsight-hez, és Apache Hive-lekérdezéseket futtathat a Visual studióhoz készült Data Lake Tools használatával](apache-hadoop-visual-studio-tools-get-started.md).
* [Használja az Azure HDInsight eszközt a Visual Studio Code](../hdinsight-for-vscode.md)-hoz.
* [Adatok feltöltése a HDInsight](./../hdinsight-upload-data.md).
