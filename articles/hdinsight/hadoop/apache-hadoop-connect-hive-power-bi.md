---
title: Apache Hive-alapú adatmegjelenítés Power BI-Azure HDInsight
description: Ismerje meg, hogyan jelenítheti meg az Azure HDInsight által feldolgozott kaptár-információkat a Microsoft Power BI használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: bb448a4befb15618485b2b5951222761180a1f22
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866743"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Apache Hive-adatok vizualizálása a Microsoft Power BI-jal és ODBC-vel az Azure HDInsightban

Ismerje meg, hogyan csatlakoztathatók a Microsoft Power BI Desktop az Azure HDInsight az ODBC használatával, és Hogyan jeleníthető meg a Apache Hive adatai.

> [!IMPORTANT]
> A struktúra ODBC-illesztőjét kihasználhatja a Power BI Desktop-ben lévő általános ODBC-összekötőn keresztül történő importáláshoz. Azonban nem ajánlott a BI-munkaterhelések esetében a kaptár-lekérdezési motor nem interaktív természetét figyelembe venni. A [HDInsight interaktív lekérdezés-összekötő](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) és a [HDInsight Spark-összekötő](/power-bi/spark-on-hdinsight-with-direct-connect) jobb választás a teljesítményük szempontjából.

Ebben a cikkben egy `hivesampletable` struktúra-táblázat adatait Power BIba tölti be. A kaptár tábla néhány mobiltelefon-használati adatokat tartalmaz. Ezután kinyomtatja a használati adatokat a globális térképen:

:::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png" alt-text="HDInsight Power BI a leképezési jelentés" border="true":::

Az adatok az új [interaktív lekérdezési](../interactive-query/apache-interactive-query-get-started.md) fürt típusára is érvényesek. Ha közvetlen lekérdezéssel szeretne csatlakozni a HDInsight interaktív lekérdezéshez, tekintse meg az [interaktív lekérdezési struktúra adatai a Microsoft Power bi használatával közvetlen lekérdezéssel az Azure HDInsight-ben](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtása előtt a következő elemeket kell megadnia:

* HDInsight-fürt. A fürt lehet egy struktúra vagy egy újonnan kiadott interaktív lekérdezési fürttel rendelkező HDInsight-fürt. Fürtök létrehozásával kapcsolatban lásd: [fürt létrehozása](apache-hadoop-linux-tutorial-get-started.md).

* [Microsoft Power bi Desktop](https://powerbi.microsoft.com/desktop/). A másolást a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=45331)töltheti le.

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-adatforrás létrehozása

Lásd: [struktúra ODBC-adatforrás létrehozása](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Adatok betöltése a HDInsight

A **hivesampletable** -struktúra tábla minden HDInsight-fürtöt tartalmaz.

1. Indítsa el a Power BI Desktopot.

1. A felső menüben navigáljon a **Kezdőlap**  >  **adatok beolvasása**  >  **továbbiak...** elemre.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png" alt-text="Az HDInsight Excel Power BI megnyitása" border="true":::

1. Az **adatok lekérése** párbeszédpanelen válassza **az egyéb** lehetőséget a bal oldalon, válassza ki az **ODBC** elemet a jobb oldalon, majd válassza a lenti **kapcsolat** lehetőséget.

1. Az az **ODBC-ből** párbeszédpanelen válassza ki a legördülő lista utolsó szakaszában létrehozott adatforrás nevét. Ez után válassza az **OK** gombot.

1. Az első használathoz egy **ODBC-illesztőprogram** párbeszédablak nyílik meg. Válassza az **alapértelmezett vagy az egyéni** lehetőséget a bal oldali menüben. Ezután válassza a **Kapcsolódás** a **navigátor** megnyitásához lehetőséget.

1. A **navigátor** párbeszédpanelen bontsa ki az **ODBC > struktúra > alapértelmezett** beállítást, válassza a **hivesampletable** lehetőséget, majd válassza a **Betöltés** lehetőséget.

## <a name="visualize-data"></a>Adatok vizualizációja

Folytassa az utolsó eljárással.

1. A vizualizációk ablaktáblán válassza a **Térkép** lehetőséget, ez egy földgömb ikon.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png" alt-text="HDInsight Power BI a jelentés testreszabása" border="true":::

1. A **mezők** panelen válassza az **ország** és a **devicemake** lehetőséget. Láthatja a térképen ábrázolt adatfeliratokat.

1. Bontsa ki a térképet.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan jelenítheti meg a HDInsight adatait a Power BI használatával.  További tudnivalókért olvassa el a következő cikket:

* [Az Excel és a HDInsight összekötése a Microsoft kaptár ODBC-illesztővel](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* Az [Excel Apache Hadoop Power Query használatával csatlakoztatható](apache-hadoop-connect-excel-power-query.md).
* [Interaktív lekérdezési Apache Hive adatai megjelenítése a Microsoft Power BI használatával közvetlen lekérdezéssel](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)