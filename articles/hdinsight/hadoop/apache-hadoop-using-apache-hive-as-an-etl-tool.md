---
title: Apache Hive használata ETL-eszközként – Azure HDInsight
description: A Apache Hive használatával kinyerheti, átalakíthatja és betöltheti az ETL-adatok az Azure HDInsight-ben.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: bcf2f39423f033ccd5bfdb6bf51ebc89e254f802
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867814"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Apache Hive használata kinyerési, átalakítási és betöltési (ETL) eszközként

A beérkező adatbevitelt általában úgy kell megtisztítani és átalakítani, hogy az a megfelelő helyre kerüljön az elemzéshez. A kinyerési, átalakítási és betöltési (ETL) műveletek az adatok előkészítéséhez és egy adatcélhelybe való betöltéséhez használatosak.  A HDInsight Apache Hive a strukturálatlan adatban is olvashatók, szükség szerint feldolgozza az adatfeldolgozást, majd betölti az összes adatát egy kapcsolódó adattárházba a döntési támogatási rendszerek esetében. Ebben a megközelítésben az adatok kinyerése a forrásból történik. Ezután az alkalmazkodóképes tárolóban, például az Azure Storage-blobokban vagy a Azure Data Lake Storage tárolhatja. Ezt követően a rendszer a kaptár-lekérdezések sorrendjével alakítja át az adatmennyiséget. Ezután előkészítése a kaptáron belül, a cél adattárba való tömeges betöltéshez.

## <a name="use-case-and-model-overview"></a>Használati eset és modell áttekintése

Az alábbi ábra az ETL Automation használati eseteit és modelljét mutatja be. A bemeneti adatok át lettek alakítva a megfelelő kimenet létrehozásához.  Az átalakítás során az adatváltozások alakja, adattípusa és akár nyelve is megváltozik.  Az ETL-folyamatok átalakítják az Imperial-t metrikára, módosíthatja az időzónákat, és javíthatják a pontosságot, hogy megfelelően illeszkedjenek a cél meglévő adataihoz. Az ETL-folyamatok az új adatok meglévő adatokkal való összevonásával naprakészen tarthatják a jelentéseket, vagy további információkat biztosíthatnak a meglévő adatokról. Az olyan alkalmazások, mint a Jelentéskészítő eszközök és szolgáltatások, a kívánt formátumban használhatják ezeket az adatfájlokat.

:::image type="content" source="./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png" alt-text="Apache Hive ETL-architektúra" border="false":::

A Hadoop jellemzően olyan ETL-folyamatokban használják, amelyek nagy mennyiségű szövegfájlt (például CSV) importálnak. Vagy kisebb, de gyakran változó számú szövegfájl vagy mindkettő.  A kaptár egy nagyszerű eszköz, amellyel előkészítheti az adatfeldolgozást, mielőtt betölti azt az adatcélhelybe.  A struktúra lehetővé teszi, hogy létrehoz egy sémát a CSV-fájlon keresztül, és SQL-szerű nyelven hozzon létre MapReduce-programokat, amelyek az adatkezeléssel működnek együtt.

A kaptárok az ETL-re való használatának tipikus lépései a következők:

1. Betöltheti az adatAzure Data Lake Storage vagy az Azure Blob Storage.
2. Hozzon létre egy metaadat-tároló adatbázist (a Azure SQL Database használatával) a sémák tárolásához a kaptár használatával.
3. Hozzon létre egy HDInsight-fürtöt, és kapcsolódjon az adattárhoz.
4. Adja meg az adattárban található adatolvasáskor alkalmazandó sémát:

    ```hql
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs

    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT,
        system BIGINT,
        systemage BIGINT,
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE LOCATION 'wasbs://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Alakítsa át az adatszerkezetet, és töltse be a célhelyre.  A struktúra több módon is használható az átalakítás és a betöltés során:

    * Megkérdezheti és előkészítheti az adatstruktúrákat a kaptár használatával, és mentheti CSV-ként Azure Data Lake Storage vagy Azure Blob Storage-tárolóban.  Ezután használjon olyan eszközt, mint a SQL Server Integration Services (SSIS), hogy megszerezze ezeket a CSV, és betöltse az adattárolót egy célként szolgáló, például SQL Serverba.
    * Az adatok lekérdezése közvetlenül az Excelből vagy a C#-ból a kaptár ODBC-illesztő használatával.
    * Az [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) használatával olvassa el az előkészített CSV-fájlokat, és töltse be azokat a cél-viszonyítási adatbázisba.

## <a name="data-sources"></a>Adatforrások

Az adatforrások jellemzően olyan külső információk, amelyek az adattár meglévő adataihoz is illeszkednek, például:

* Az adatfájlokat létrehozó közösségi adathordozók, naplófájlok, érzékelők és alkalmazások.
* Adatszolgáltatókból beszerzett adatkészletek, például időjárási statisztikák vagy szállítói értékesítési számok.
* Egy megfelelő eszközön vagy keretrendszeren keresztül rögzített, szűrt és feldolgozott adatátviteli adatcsatorna.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Kimeneti célok

A kaptár használatával az adatokat különböző típusú célokba exportálhatja, többek között:

* Egy rokon adatbázis, például SQL Server vagy Azure SQL Database.
* Egy adattárház, például az Azure szinapszis Analytics.
* Excel.
* Azure Table és blob Storage.
* Azok az alkalmazások vagy szolgáltatások, amelyeknek az adatok meghatározott formátumokba való feldolgozására, vagy adott típusú adatszerkezetet tartalmazó fájlokra van szükségük.
* Egy JSON-dokumentum tárolója, például Azure Cosmos DB.

## <a name="considerations"></a>Megfontolandó szempontok

Az ETL modellt általában a következő esetekben használja a rendszer:

`*` Egy meglévő adatbázisba vagy információs rendszerbe betöltheti a külső forrásokból származó, részben strukturált vagy strukturálatlan adatok adatfolyamait vagy nagy mennyiségű részét.
`*` Megtisztíthatja, átalakíthatja és érvényesítheti az adataikat a betöltés előtt, például a fürtön keresztül egynél több transzformációs folyamat használatával.
`*` Jelentéseket és vizualizációkat készíthet, amelyek rendszeresen frissülnek. Ha például a jelentés túl hosszú időt vesz igénybe a nap folyamán, a jelentést a következő időpontban is futtathatja:. A kaptár-lekérdezések automatikus futtatásához használhatja a [Azure Logic apps](../../logic-apps/logic-apps-overview.md) és a PowerShellt is.

Ha az adatelérési cél nem adatbázis, a lekérdezésen belül, például egy CSV-fájlban is létrehozhatja a megfelelő formátumú fájlt. Ez a fájl ezután importálható az Excelbe vagy a Power BIba.

Ha több műveletet kell végrehajtania az adatokon az ETL-folyamat részeként, gondolja át, hogyan kezeli őket. Egy külső program által vezérelt műveletekkel, a megoldáson belüli munkafolyamatok helyett döntse el, hogy vannak-e párhuzamosan végrehajtható műveletek. És az egyes feladatok befejezésének észlelése. A munkafolyamati mechanizmusok, például a Hadoop belüli Oozie könnyebben megoldhatók, mint a külső parancsfájlok vagy egyéni programok használatával végzett műveletek sorrendjének kipróbálása.

## <a name="next-steps"></a>Következő lépések

* [ETL-skála](apache-hadoop-etl-at-scale.md)
* [`Operationalize a data pipeline`](../hdinsight-operationalize-data-pipeline.md)
